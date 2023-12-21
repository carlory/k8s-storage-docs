---
title: "Verify a feature PR with e2e tests on macOS"
weight: 1
description: >
  This document describes how to run e2e tests on macOS. It's not suitable for all e2e tests, but it's enough for some e2e tests that don't require real worker nodes.
---

## Overview

MacOS is popular among developers, but it's not easy to run e2e tests on macOS for verifying a feature PR. The CI workflow on GitHub is slow and hard to debug when a test fails. In order to get a quick feedback, we need a way to run the specific e2e tests on local environment. In this document, it will show how to run a specific e2e test on macOS with a local cluster created via both [local-up-cluster.sh](https://github.com/kubernetes/kubernetes/blob/master/hack/local-up-cluster.sh) and [kwok](https://kwok.sigs.k8s.io/docs/user/kwok-out-cluster/). Please note that it's not suitable for all e2e tests, but it's enough for some e2e tests that don't require real worker nodes.

By the way, if you want to do e2e test with real worker nodes, you can use [kind](https://kind.sigs.k8s.io/) to create a local cluster. It's not covered in this document. Please refer to [Node Image](https://kind.sigs.k8s.io/docs/design/node-image) and [Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start) for more details. 

## Prerequisites

Before starting, please make sure you have the following tools installed:

* kubectl
* etcd
* kwok
* ginkgo

For example, you can install them like this:

```sh
# If you want to compile kubectl from source code, please use `make WHAT=cmd/kubectl` 
# and then copy the binary to your PATH. Or you can install it via brew.
➜  kubernetes git:(kep-3751-quota) brew install kubernetes-cli

# This script will download and install etcd in third_party.
➜  kubernetes git:(kep-3751-quota) hack/install-etcd.sh
# A convenient way to use etcd is to add it to your PATH. the following command only works for
# the current terminal session.
➜  kubernetes git:(kep-3751-quota) export PATH="$GOPATH/src/k8s.io/kubernetes/third_party/etcd:${PATH}"

➜  kubernetes git:(kep-3751-quota) brew install kwok
➜  kubernetes git:(kep-3751-quota) go install github.com/onsi/ginkgo/v2/ginkgo
```

## Setup local cluster

*Terminal 1*: setup local cluster with VolumeAttributesClass enabled. It will take a few minutes to start the cluster. If you want to setup a cluster with different configurations, please refer to [local-up-cluster.sh](https://github.com/kubernetes/kubernetes/blob/master/hack/local-up-cluster.sh) for more details. Generally speaking, you can use similar commands to setup a cluster like this:

```sh
➜  kubernetes git:(kep-3751-quota) sudo FEATURE_GATES=VolumeAttributesClass=true ./hack/local-up-cluster.sh
Password:
...
Local Kubernetes cluster is running. Press Ctrl-C to shut it down.

Configurations:
  /private/var/folders/zz/zyxvpxvq6csfxvn_n0000000000000/T/local-up-cluster.sh.XXXXXX.Emp1teaU/kube-audit-policy-file
  /private/var/folders/zz/zyxvpxvq6csfxvn_n0000000000000/T/local-up-cluster.sh.XXXXXX.Emp1teaU/kube-scheduler.yaml
  /private/var/folders/zz/zyxvpxvq6csfxvn_n0000000000000/T/local-up-cluster.sh.XXXXXX.Emp1teaU/kube-serviceaccount.key
  /private/var/folders/zz/zyxvpxvq6csfxvn_n0000000000000/T/local-up-cluster.sh.XXXXXX.Emp1teaU/kube_egress_selector_configuration.yaml

Logs:
  /tmp/etcd.log
  /tmp/kube-apiserver.log
  /tmp/kube-controller-manager.log
  /tmp/kube-scheduler.log


To start using your cluster, you can open up another terminal/tab and run:

  export KUBECONFIG=/var/run/kubernetes/admin.kubeconfig
  cluster/kubectl.sh
...
```

*Terminal 2*: Create a fake node (it's required for e2e tests setup) and run [kwok](https://kwok.sigs.k8s.io/docs/user/kwok-out-cluster/) to maintain the fake node.

```sh
➜  kubernetes git:(kep-3751-quota) kubectl apply --kubeconfig /var/run/kubernetes/admin.kubeconfig -f - <<EOF
apiVersion: v1
kind: Node
metadata:
  annotations:
    node.alpha.kubernetes.io/ttl: "0"
    kwok.x-k8s.io/node: fake
  labels:
    beta.kubernetes.io/arch: amd64
    beta.kubernetes.io/os: linux
    kubernetes.io/arch: amd64
    kubernetes.io/hostname: kwok-node-0
    kubernetes.io/os: linux
    kubernetes.io/role: agent
    node-role.kubernetes.io/agent: ""
    type: kwok
  name: kwok-node-0
spec:
status:
  allocatable:
    cpu: 32
    memory: 256Gi
    pods: 110
  capacity:
    cpu: 32
    memory: 256Gi
    pods: 110
  nodeInfo:
    architecture: amd64
    bootID: ""
    containerRuntimeVersion: ""
    kernelVersion: ""
    kubeProxyVersion: fake
    kubeletVersion: fake
    machineID: ""
    operatingSystem: linux
    osImage: ""
    systemUUID: ""
  phase: Running
EOF

➜  kubernetes git:(kep-3751-quota) kwok \
  --kubeconfig=/var/run/kubernetes/admin.kubeconfig \
  --manage-all-nodes=false \
  --manage-nodes-with-annotation-selector=kwok.x-k8s.io/node=fake \
  --manage-nodes-with-label-selector= \
  --manage-single-node= \
  --disregard-status-with-annotation-selector=kwok.x-k8s.io/status=custom \
  --disregard-status-with-label-selector= \
  --cidr=10.0.0.1/24 \
  --node-ip=10.0.0.1 \
  --node-lease-duration-seconds=40
```

## Run e2e tests

*Terminal 3*: Run specific e2e tests. The `--` separates the arguments for ginkgo from the arguments for the test. `--kubeconfig` is required for e2e tests setup. For more options, please refer to [test_context.go](https://github.com/kubernetes/kubernetes/blob/master/test/e2e/framework/test_context.go) in kubernetes repo. 

Ginkgo will run all tests by default, so we need to use `--focus` to run the specific test. For more options, please execute `ginkgo --help` for more details. The following command is an example with a few options for running the specific e2e test. It will run the test `should create a ResourceQuota and capture the life of a persistent volume claim with a volume attributes class` in the file `test/e2e/apimachinery/resource_quota.go`. It will also print the logs of the test.

```sh
➜  ginkgo --focus "should create a ResourceQuota and capture the life of a persistent volume claim with a volume attributes class" -v test/e2e -- --kubeconfig=/var/run/kubernetes/admin.kubeconfig
  Dec 21 17:40:30.327: INFO: The --provider flag is not set. Continuing as if --provider=skeleton had been used.
  I1221 17:40:30.327668   58333 e2e.go:117] Starting e2e run "ffaf0a46-ce6a-4bf0-b2b7-e8a9c0962114" on Ginkgo node 1
Running Suite: Kubernetes e2e suite - /Users/kiki/workspace/golang/src/k8s.io/kubernetes/test/e2e
=================================================================================================
Random Seed: 1703151598 - will randomize all specs

Will run 1 of 7408 specs
------------------------------
[ReportBeforeSuite]
/Users/kiki/workspace/golang/src/k8s.io/kubernetes/test/e2e/e2e_test.go:157
[ReportBeforeSuite] PASSED [0.000 seconds]
------------------------------
[SynchronizedBeforeSuite]
/Users/kiki/workspace/golang/src/k8s.io/kubernetes/test/e2e/e2e.go:77
  Dec 21 17:40:30.417: INFO: >>> kubeConfig: /var/run/kubernetes/admin.kubeconfig
  Dec 21 17:40:30.419: INFO: Waiting up to 30m0s for all (but 0) nodes to be schedulable
  Dec 21 17:40:30.436: INFO: Waiting up to 5m0s for all daemonsets in namespace 'kube-system' to start
  Dec 21 17:40:30.436: INFO: e2e test version: v0.0.0-master+$Format:%H$
  Dec 21 17:40:30.437: INFO: kube-apiserver version: v1.29.0-alpha.3.117+af8174e3caabf3
  Dec 21 17:40:30.437: INFO: >>> kubeConfig: /var/run/kubernetes/admin.kubeconfig
  Dec 21 17:40:30.438: INFO: Cluster IP family: ipv4
[SynchronizedBeforeSuite] PASSED [0.021 seconds]
...
[sig-api-machinery] ResourceQuota [Feature:VolumeAttributesClass] should create a ResourceQuota and capture the life of a persistent volume claim with a volume attributes class [sig-api-machinery, Feature:VolumeAttributesClass]
/Users/kiki/workspace/golang/src/k8s.io/kubernetes/test/e2e/apimachinery/resource_quota.go:1220
  STEP: Creating a kubernetes client @ 12/21/23 17:40:30.532
  Dec 21 17:40:30.532: INFO: >>> kubeConfig: /var/run/kubernetes/admin.kubeconfig
  STEP: Building a namespace api object, basename volume-attributes-class @ 12/21/23 17:40:30.532
  STEP: Waiting for a default service account to be provisioned in namespace @ 12/21/23 17:40:30.56
  STEP: Waiting for kube-root-ca.crt to be provisioned in namespace @ 12/21/23 17:40:30.563
  STEP: Counting existing ResourceQuota @ 12/21/23 17:40:30.565
  STEP: Creating a ResourceQuota @ 12/21/23 17:40:35.573
  STEP: Ensuring resource quota status is calculated @ 12/21/23 17:40:35.584
  STEP: Creating a PersistentVolumeClaim with volume attributes class @ 12/21/23 17:40:37.591
  STEP: Ensuring resource quota status captures persistent volume claim creation @ 12/21/23 17:40:37.619
  STEP: Not allowing a PersistentVolumeClaim to be created that exceeds remaining quota @ 12/21/23 17:40:39.625
  STEP: Deleting a PersistentVolumeClaim @ 12/21/23 17:40:39.63
  STEP: Ensuring resource quota status released usage @ 12/21/23 17:40:39.638
  Dec 21 17:40:41.644: INFO: Waiting up to 7m0s for all (but 0) nodes to be ready
  STEP: Destroying namespace "volume-attributes-class-8151" for this suite. @ 12/21/23 17:40:41.648
• [11.125 seconds]
...
[SynchronizedAfterSuite]
/Users/kiki/workspace/golang/src/k8s.io/kubernetes/test/e2e/e2e.go:88
  Dec 21 17:40:41.673: INFO: Running AfterSuite actions on node 1
[SynchronizedAfterSuite] PASSED [0.000 seconds]
------------------------------
[ReportAfterSuite] Kubernetes e2e suite report
/Users/kiki/workspace/golang/src/k8s.io/kubernetes/test/e2e/e2e_test.go:161
[ReportAfterSuite] PASSED [0.000 seconds]
------------------------------

Ran 1 of 7408 Specs in 11.256 seconds
SUCCESS! -- 1 Passed | 0 Failed | 0 Pending | 7407 Skipped
You're using deprecated Ginkgo functionality:
=============================================
  --ginkgo.slow-spec-threshold is deprecated --slow-spec-threshold has been deprecated and will be removed in a future version of Ginkgo.  This feature has proved to be more noisy than useful.  You can use --poll-progress-after, instead, to get more actionable feedback about potentially slow specs and understand where they might be getting stuck.

To silence deprecations that can be silenced set the following environment variable:
  ACK_GINKGO_DEPRECATIONS=2.13.0

PASS

Ginkgo ran 1 suite in 43.437464292s
Test Suite Passed
```

## Debug e2e tests

Kubernetes components' logs are also available in `/tmp` directory. i.e. you can use `tail -f /tmp/kube-apiserver.log` to view the logs of kube-apiserver. During the e2e tests, you can use `kubectl --kubeconfig=/var/run/kubernetes/admin.kubeconfig` to view or change the status of the cluster. 

If it's still not enough for debugging, you can add more logs in the test code or change the cluster configurations before running the e2e tests.

## Clean up

1. *Terminal 3*: Press `Ctrl-C` to shut down ginkgo if it's still running.
2. *Terminal 2*: Press `Ctrl-C` to shut down kwok.
3. *Terminal 1*: Press `Ctrl-C` to shut down the cluster.
