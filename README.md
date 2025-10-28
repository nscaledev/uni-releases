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

## Kubernetes Releases

Additional documentation of Kubernetes application bundle releases is provided [here](README.kubernetes.md) to document upgrade paths if auto-update is not enabled, given they may require a full cluster upgrade.

## Change Log

### v1.10.0

_28 October 2025_

#### Release Notes

* Includes a early (not stable) release of the region V2 API, allowing user provisioning of networks.
* Implements the Saga pattern for complex region V2 APIs.
* Makes most region resources stateless to avoid drift.
* Refactors quota allocation to use a shared library, avoiding code duplication.
* Adds a network section to the UI.

#### Operational Notes

* To facilitate stateless region operation we need to rename provider resources to predictable, but unique, names.
  * This will iterate over `openstacknetwork`, `openstacksecuritygroup` and `openstackserver` resources.
  * Every resource ID encountered will be renamed to a stable and predictable name.
  * On success the `openstack*` resource will be deleted.
  * This is a fully managed, online upgrade.
* VLAN allocation tracking used to use `openstacknetwork` resources, but now uses OpenStack as the source of truth.
  * OpenStack clusters must be upgraded to use the newest network [policies](https://github.com/nscaledev/uni-python-unikorn-openstack-policy).

### v1.9.0

_13 October 2025_

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| compute | v1.9.1 | <ul><li>Fix port firewalling of L3 addresses so servers can act as routers.</li></ul> | |

#### Release Notes

* Cleanups and refactoring of the core library.
* Projects gain the ability to have references attached to them to inhibit deletion, and potentially report what would be deleted if the project were to be.
* Cloud identities in the region controller now react to project deletion events and also manage references on their project to control deletion semantics.
* Compute resources now reside in the compute service's namespace to maintain data boundaries, this is performed transparently by an online upgrade.
* Compute resources now react to network deletion in order to preserve cascading deletion semantics.

### v1.8.0

_2 October 2025_

#### Patch Releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| compute | v1.8.2 | <ul><li>Removes the necessity to have a single workload pool.</li></ul> | |
| ui | v1.8.2 | <ul><li>Removes the necessity to have a single workload pool.</li></ul> | |

#### Release Notes

* Numerous improvements to core reconciler logic.
* Logging made less verbose in the good path, it can still be enabled with `--zap-log-level=debug`.
* Region and Kubernetes deletion logic refactored and rationalized.
* Security group controller is now deleted to simplify region handler logic.
* All bug fixes included in v1.7.x patch releases.

#### Operational Notes

* You may have to manually delete `*securitygrouprules` custom resource definitions, and remove any finalizers from resources to fully clean up.

### v1.7.0

_23 September 2025_

#### Patch Releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| Region | v1.7.1 | <ul><li>Fixes a faulty custom resource that blocks server provisioning.</li></ul> | |
| Compute | v1.7.1 | <ul><li>Fixes a race condition with server eviction.</li><li>Allows correct workload pool names.</li><li>Preserves image IDs when updating a cluster to prevent accidental reprovisioning.</li><li>Adds debug logging for API updates.</li><li>Doesn't rebuild servers on cloud-init data update.</li></ul> | |
| Kubernetes | v1.7.1 | <ul><li>Introduces a new cluster manager bundle with vCluster and Cert Manager upgrade options.</li></ul> | |

#### Release Notes

* Change to the core controller upgrade logic, uses interfaces rather than forcing the use of empty functions in each controller.
* Adds power management APIs to the compute service to allow servers to stopped, started and rebooted.
* Extends the OpenStack image schema and region controller to support organization scoped private images.
* Fixes bad slug lookups in the UI and redirects to the next highest level in the hierarchy.
* Adds power management controls to the UI's compute cluster view.

#### Breaking Changes

* External services using the core controller logic and with upgrade functions defined will need to update the upgrade function signature to match that defined in the new interface.

### v1.6.0

_02 September 2025_

#### Release Notes

* Small fix to API handling to ensure deletion timestamps are set.
* Compute clusters can now have a set of servers evicted.
  This will remove the servers, free quota allocations and scale down the cluster.
* The compute service will only advertise images with no software installed.
* Fix to Kubernetes cluster upgrade.
* The UI will now accept a more inclusive character set for resource names in line with the API.

#### Breaking Changes

* Compute clusters will no longer use predictable ordinal based naming, instead random.
  While potentially inconvenient for humans, it allows better operational characteristics.
* Existing compute clusters may no longer have a suitable backing image, and will need one
  to be installed that can be used before clusters can be created or modified.
  Modified clusters whose images have vanished will be automatically rebuilt when a
  modification API request is submitted.

### v1.5.0

_20 August 2025_

#### Patch Releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| kubernetes | v1.5.1 | Fixes Kubernetes cluster upgrade. | |

#### Release Notes

* Fixes a bug affecting identity, compute and kubernetes where the namespace kind was not honored on project namespace lookup, and thus could alias with other 3rd party namespaces who also wanted to set organization and project labels.
* Fixes all the issues addressed by v1.4.x patch releases.

### v1.4.0

_31 July 2025_

#### Patch Releases

| Component | Version | Description | Dependencies |
| --- | --- | --- | --- |
| identity | v1.4.1 | Removes the need to rotate access tokens | |
| identity | v1.4.2 | Fix updates to allocations preventing cluster resize | |
| region | v1.4.1 | Fix security group rule aliasing | |
| kubernetes | v1.4.1 | Fix broken application manifest | |

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
| kubernetes | v1.3.2 | Fix kubeconfig download problem; label virtual cluster namespaces | |
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
