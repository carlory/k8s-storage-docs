---
title: PV/PVC Binder Controller
weight: 1
description: >
  It is responsible for binding a Persistent Volume Claim (PVC) to a Persistent Volume (PV).
---

Familiarity with [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/), [Dynamic Volume Provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) and [Informer Implementation](https://github.com/kubernetes/sample-controller/blob/master/docs/controller-client-go.md) is suggested.

![](pv-pvc-binder-controller.png)

## Overview

The PV/PVC Binder controller is responsible for binding a Persistent Volume Claim (PVC) to a Persistent Volume (PV). During the binding process, the controller will try to provision a new PV if no suitable PV is found, and then bind the PVC to the newly provisioned PV. The controller will also update the status of the PV and PVC to reflect the binding status.

## Implementation

### Local Cache

The PV/PVC Binder controller maintains two local caches, one for claims (PVCs) and one for volumes (PVs). Local caches hold the last known version of claims and volumes. These caches are thread safe as long as the volumes/claims there are not modified. They must be cloned before any modification. 

#### Why do we need these caches?

Binding a volume to a claim generates 4 events, roughly in this order (depends on goroutine ordering):

* Step 1: `volume.Spec` update
* Step 2: `volume.Status` update
* Step 3: `claim.Spec` update
* Step 4: `claim.Status` update

With these caches, the controller can check that it has already saved `volume.Status` and `claim.Spec+Status` and does not need to do anything when e.g. `volume.Spec` update event arrives before all the other events.

Without these caches, the controller would see the old version of `volume.Status` and `claim.Spec+Status` in the informers (it has not been updated from API server events yet) and it would try to fix these objects to be bound together. Any write to API server would fail with version conflict - these objects have been already written.

#### How do we keep these caches up to date?

There are two kinds of caches in the controller: informer stores and local caches. 

At the startup phase, the controller populates the local caches from the informer stores after the informers are synced. The controller registers resource event handlers for `PersistentVolume` and `PersistentVolumeClaim` resources. After the controller is started, the informers will start to receive events from the API server and will update their internal caches (informer stores). Meanwhile, the event handlers will be called and the local caches will be updated as well before putting the volume/claim into work queue. The controller will then process the work queue and reconcile the volume/claim. The controller will immediately update the local caches when it saves the volume/claim to the API server (e.g. when it binds a volume to a claim). If updating cache fails, don't worry, it will be populated again via the event handlers.

In summary, the local caches and informer stores are always in sync. But the local caches are always one step ahead of the informer stores only when a volume/claim is changed by the controller.

### Reconcile

// TODO: add more details about the reconcile process


## Known Issues

TBD

## Contribution Guidelines

### Code Style

{{% alert title="PLEASE DO NOT ATTEMPT TO SIMPLIFY THIS CODE. KEEP THE SPACE SHUTTLE FLYING." %}}

This controller is intentionally written in a very verbose style. You will notice:

1. Every `if` statement has a matching `else` (exception: simple error checks for a client API call)
2. Things that may seem obvious are commented explicitly

We call this style *space shuttle style*. Space shuttle style is meant to ensure that every branch and condition is considered and accounted for - the same way code is written at NASA for applications like the space shuttle.

Originally, the work of this controller was split amongst three controllers. This controller is the result a large effort to simplify the PV subsystem. During that effort, it became clear that we needed to ensure that every single condition was handled and accounted for in the code, even if it resulted in no-op code branches.

As a result, the controller code may seem overly verbose, commented, and branchy. However, a large amount of business knowledge and context is recorded here in order to ensure that future maintainers can correctly reason through the complexities of the binding behavior. For that reason, changes to this file should preserve and add to the space shuttle style.

{{% /alert %}}

### KEPs

TBD