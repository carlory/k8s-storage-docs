---
title: Architecture
weight: 1
---

Familiarity with [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/) and [Kubernetes Architecture](https://kubernetes.io/docs/concepts/architecture/) is suggested.

This picture shows the Kubernetes storage architecture.

![kubernetes storage architecture](kubernetes-storage-architecture.png)

## Control plane

It consists of the Kubernetes control plane components, optional CSI cluster components (snapshot controller) and CSI driver components (controller plugin).

### kube-apiserver

kube-apiserver exposes the Kubernetes API and other commponents communicates with it to reconcile the state of the cluster. 

It contains the following storage-related admission plugins:

* [PersistentVolumeClaimResize](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#persistentvolumeclaimresize) prevents resizing of all claims by default unless a claim's StorageClass explicitly enables resizing by setting allowVolumeExpansion to true.

* [DefaultStorageClass](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#defaultstorageclass) observes creation of PersistentVolumeClaim objects that do not request any specific storage class and automatically adds a default storage class to them. With [Retroactive Default StorageClass moved to GA in 1.28](https://kubernetes.io/blog/2023/08/18/retroactive-default-storage-class-ga/), this admission plugin [may be](https://github.com/kubernetes/kubernetes/pull/118863#discussion_r1349365287) deprecated in the future.

* [StorageObjectInUseProtection](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#storageobjectinuseprotection) ensures that PersistentVolumeClaims (PVCs) in active use by a Pod and PersistentVolume (PVs) that are bound to PVCs are not removed from the system, as this may result in data loss.

### kube-scheduler

* the NodeVolumeLimits plugin checks that CSI volume limits can be satisfied for the node.
* the VolumeBinding plugin checks if the node has or if it can bind the requested volumes.
* the VolumeRestrictions plugin checks that volumes mounted in the node satisfy restrictions that are specific to the volume provider.
* the VolumeZone plugin checks that volumes requested satisfy any zone requirements they might have.

### kube-controller-manager

* the PV/PVC binder controller is responsible for binding PVCs to PVs and creating PVs if needed.
* the attach/detach controlller is responsible for attaching and detaching volumes to/from nodes.
* the expand controller is responsible for expanding PVs when PVCs are resized.
* PV/PVC protection controller is responsible for protecting PVs and PVCs from being deleted when they are in use.
* the ephemeral volume controller is responsible for creating and deleting ephemeral volumes.

### CSI Cluster Components (optional)

The cluster components are optional and are only needed if the CSI driver supports volume snapshots.

### CSI driver components

TBD

## Worker nodes

It consist of the kubelet, CSI driver components (node plugin), and container runtime.

### kubelet

TBD

### CSI driver components

TBD