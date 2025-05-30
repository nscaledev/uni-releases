# Unikorn Releases

![Unikorn Logo](https://raw.githubusercontent.com/unikorn-cloud/assets/main/images/logos/light-on-dark/logo.svg#gh-dark-mode-only)
![Unikorn Logo](https://raw.githubusercontent.com/unikorn-cloud/assets/main/images/logos/dark-on-light/logo.svg#gh-light-mode-only)

## Releases

All components will be tagged with the same major/minor.
Patch releases may be advertised for individual components, and any patch version dependencies described here.

### Core Components

| Component |
| --- |
| [core](https://github.com/unikorn-cloud/core) |
| [identity](https://github.com/unikorn-cloud/identity) |
| [region](https://github.com/unikorn-cloud/region) |
| [kubernetes](https://github.com/unikorn-cloud/kubernetes) |
| [compute](https://github.com/unikorn-cloud/compute) |
| [ui](https://github.com/unikorn-cloud/ui) |

### DX Components

| Component |
| --- |
| [client-go](https://github.com/unikorn-cloud/client-go) |
| [api-docs](https://github.com/nscaledev/uni-api-docs) |

### v1.1.0

_27 May 2025_

#### Release Notes

* Adds in generic health status monitoring to all OpenAPI types.
* Health monitoring enabled for all Identity and Kubernetes resources, Compute and Region services will be done in v1.2.0.
* HA etcd based control planes for virtual Kubernetes clusters.
* Fixes SQLite database compaction in vClusters.
* Fixes default image selection algorithm.
* Fixes allocation accounting in the Compute service.
* Fixes typos in the UI along with Kubernetes version sorting and rounding errors with quota views.

### v1.0.0

_14 May 2025_

#### Patch Releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| Kubernetes | v1.0.1 | Fixes SQLite database compaction in vClusters | |
| Kubernetes | v1.0.2 | Fixes default image selection algorithm | |

#### Release Notes

* Upgraded common helm chart upgraded to make image pull secrets common and global.
* Kubernetes and Compute services cache region data to improve interactivity of the UX, clients likewise are lazily instantiated to improve performance.
* Upgrades to the NVIDIA GPU Operator and Cilium.

#### Operational Notes

* The NVIDIA and Cilium upgrades are accompanied by a new cluster application bundle, and subject to normal auto-upgrade semantics.

#### Breaking Changes

* All charts have been modified to derive resource names from the chart release, this may cause conflicts during deployments to do with ingresses defining the same host and path.
  It's safe to delete the existing ingress and redeploy in this case.

### Earlier Releases

[Version v0.x.x Archive](./README-v0.md)
