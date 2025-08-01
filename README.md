# Releases

All components will be tagged with the same major/minor.
Patch releases may be advertised for individual components, and any patch version dependencies described here.

## Core Components

| Component |
| --- |
| [core](https://github.com/unikorn-cloud/core) |
| [identity](https://github.com/unikorn-cloud/identity) |
| [region](https://github.com/unikorn-cloud/region) |
| [kubernetes](https://github.com/unikorn-cloud/kubernetes) |
| [compute](https://github.com/unikorn-cloud/compute) |
| [ui](https://github.com/unikorn-cloud/ui) |

## DX Components

| Component |
| --- |
| [client-go](https://github.com/unikorn-cloud/client-go) |
| [api-docs](https://github.com/nscaledev/uni-api-docs) |

## Change Log

### v1.4.0

_31 July 2025_

#### Release Notes

* Proliferation of tag based query selectors for API endpoints.
* Service accounts can now self rotate their tokens.
* Addition of an organization deletion API.
* Service to service calls now propagate identity principals so we know who an action is ultimately on behalf of when infrastructure is provisioned in a private organization.
* New Kubernetes clusters will now have Gateway API enabled, and those after they are are upgraded.
* Fix UI white-boxing.
* Fix compute service's flavor and image selection in the UI.

#### Breaking Changes

* The format of access tokens has changed due to a library update, so existing service accounts will need token rotation.

#### Operational Notes

* A new Kubernetes application bundle is available that will upgrade Cilium CNI and may result in temporary network blips.

### v1.3.0

_6 June 2025_

#### Patch releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| kubernetes | v1.3.1 | Fixes problem with virtual cluster syncing | |
| ui | v1.3.1 | Fix dashboard view | |

#### Release Notes

* All controllers can now set their resource limits via Helm.
* Core API health status expanded with more states.
* Region APIs update to allow filtering of resources by tag.
* Region API handler fully refactored and RBAC bypass holes patched.
* Region now does asynchronous health monitoring on server resources.
* Compute updated to take advantage of service side tag filtering.
* Compute propagates server health status to clients and does roll up to the cluster resource.
* Compute now does asynchronous health monitoring on cluster resources.
* Kubernetes propagates workload pool information to the virtual cluster chart.
* Kubernetes sensibly names virtual cluster namespaces so they can be selected by a regular expression.

#### Breaking Changes

* Existing virtual clusters will have their namespace changed.
  **These will be deleted and recreated**
* Compute clients using the machine status will break, specifically the `status` field is renamed to `provisioningStatus` to accommodate health status.

### v1.2.0

_2 June 2025_

#### Patch Releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| kubernetes | v1.2.1 | Presents cluster topology information to the underlying chart | |
| identity | v1.2.1 | Increases CPU/memory limits and allows them to be modified | |

#### Release Notes

* Region controller unifies API types where possible, which is fine for Typescript due to duck typing, but allows better usability via Go.
* Region controller is more tolerant of eventual consistency, and allows some modifications and reconciliation of some things, like floating IPs and security groups.
* Compute service fixes a plethora of issues, namely:
  * Pools can be deleted.
  * Pools can be renamed.
  * Pools can have their entire specification changed and the most appropriate action is taken.
* Kubernetes service allows a node selector label to be generated for virtual clusters to allow implicit scheduling of workloads via an external controller.
* Kubernetes service unifies application bundle handling and fixes a bug where some accesses were globally scoped, as opposed to namespaced.
* Kubernetes service creates a contract for virtual cluster deployment that must be implemented bu either the built in, or an externally provided, Helm application.
* UI implements compute service API changes (that are additive in nature - so backward compatible).
* UI implements compute service editing and improved observability.
* UI implements 3rd party branding via a set of new environment variables.

#### Breaking Changes

* There is no upgrade path for existing virtual Kubernetes clusters.
  **They will all need to be deleted prior to upgrade and then recreated.**

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

### Developer Zone

[Release Process Guide](./README.developer.md)
