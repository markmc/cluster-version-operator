# How is CVO's role likely evolve over time?

Covering the relationship of the release payload, CVO, OLM, and ClusterOperators.

## Principles

### The release payload should only contain operators that are critical to "boot the platform"

The only operators that should be in the release payload are those strictly required to install and configure the cluster up to the point that OLM is running and ready to install further operators.
  
### The release payload approach has inherent scalability limits, and we need to avoid growing its contents further

What are the limits?

### Some things considered "core to the platform" will not be in the release payload

They will write status to a ClusterOperator, but not be contained in the release payload.

Examples include Istio and Knative.

(Examples of current payload components that will move?)

### Iff an operator is considered "core to the platform", it should write status to a ClusterOperator

The aggregate status of all cluster operators indicates cluster health.

### CVO only manages what is the release payload - it does not entirely capture cluster status

It follows that CVO only aggregates the status of ClusterOperators in the release payload. Therefore CVO status alone is not sufficient to indicate cluster health.

To the extent that cluster health is the "upgrade backpressure" mechanism, OLM-owned ClusterOperators will be able to exert this backpressure too.
  
### The release payload will drive the installation of OLM-owned operators

Operators that are "core to the cluster" but OLM-owned, will be installed by default, and the release payload will drive this.

This installation will continue to be launched via openshift-install, and it will continue to be possible to do an offline installation by mirroring the core platform content in advance of the installation.

### Non-core operators need to be able to take and express a dependency on the core platform

Operators obviously depend on the core platform. This will include dependencies on CVO-owned and OLM-owned operators. These non-core operators need to be able to express these individual platform dependencies.

### Upgrades are not atomic and different combinations of components must be functional during the upgrade

CVO may take a long time to move the cluster from the current version to the desired next version. During that time, unusual and somewhat non-deterministic combinations of component versions will occur, and the cluster is expected to continue to be available. The key to ensuring this is extensive CI testing.

### Not all components should be required to be updated in every cycle

e.g. nodes, or operators like Istio and Knative, should not be updated
every time we push out an update to the platform?

- if we push out an update that contains no changes to a given component,
  that component should be unaffected by the upgrade, or
- an update may contain content changes (e.g. for nodes) that are not
  automatically applied by the update, but instead applied more gradually
  by the admin after the update

## Futures

### What are the known gaps based on the above?

We will need mechanisms that allow:

1. ClusterOperators to be installed by OLM-owned operators
1. Capturing status from these OLM-owned ClusterOperators as indicative of "cluster health"
1. The release payload to trigger the installation of OLM-owned operators
1. Mirroring for these OLM-owned operators
1. ...
