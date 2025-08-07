# Kubernetes Releases

Kubernetes releases are bundles of applications that form a managed Kubernetes cluster, and do not reflect the Kubernetes version itself.
This covers things like:

* The Kubernetes cluster provisioner chart
* CNI
* CSI operators
* GPU operators
* Metric server
* etc.

## v1.3.0 (Release v1.4.0)

_6 June 2025_

### Release Notes

* Updates Cilium CNI to enable Gateway API.

## v1.2.2 (Release v1.0.0)

_14 May 2025_

### Release Notes

* Updates the NVIDIA GPU operator.
* Updates Cilium CNI.

## v1.2.1 (Release v0.1.i9)

_02 May 2025_

### Release Notes

* Update the OpenStack cluster chart.
  * Supports containerd registry mirrors for container image caching.
  * Removes cloud provider flags to allow deployment of Kubernetes v1.33.

### Operational Notes

* A new Kubernetes cluster application bundle is available that will trigger a rolling upgrade of the control plane, this is to facilitate the removal of a retired CLI flag.

## v1.2.0 (Release v0.1.17)

_26 March 2025_

### Release Notes

* Update the OpenStack cluster chart.
  * Allows resource limits to be modified on the Kubernetes API server for enhanced stability.

### Operational Notes

* A new Kubernetes application bundle version is released as the above changes will need a cluster rolling upgrade in order to deploy.

## v1.1.0 (Release v0.1.15)

_13 March 2025_

### Release Notes

* Updates Cilium CNI.
* Updates the OpenStack cloud provider.
* Updates the OpenStack Cinder CSI plugin.
* Updates the metrics server.
* Updates the AMD GPU operator.

## v1.0.0
