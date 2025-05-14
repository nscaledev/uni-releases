# Unikorn Releases

![Unikorn Logo](https://raw.githubusercontent.com/unikorn-cloud/assets/main/images/logos/light-on-dark/logo.svg#gh-dark-mode-only)
![Unikorn Logo](https://raw.githubusercontent.com/unikorn-cloud/assets/main/images/logos/dark-on-light/logo.svg#gh-light-mode-only)

Unikorn is a relatively large collection of independent micro-services that combine to provide IaaS/PaaS functionality.
Maintaining a matrix of compatible versions is complex due to a lot of inter-dependencies.
This repository aims to simplify the task by bundling together versions that are know to work together and notify of any breaking changes before the project becomes GA.

## Releases

> [!CAUTION]
> For those who don't read the documentation, here's a summary for you, please consult the linked release notes:
> * Upgrading to [`v0.1.13`](#v0113) or greater requires user migration.
> * Upgrading to [`v0.1.10`](#v0110) or greater requires platform administrator role migration.
> * Upgrading to [`v0.1.8`](#v018) or greater requires resource allocation and quotas to be populated.
> * Upgrading to [`v0.1.6`](#v016) or greater requires user migration.
> * Upgrading to [`v0.1.3`](#v013) or greater requires image metadata updates.
> * Upgrading to [`v0.1.2`](#v012) or greater requires all clusters to be deleted.

### v1.0.0

_14 May 2025_

> [!NOTE]
> From this point on all components will be tagged with the same major/minor.
> Patch releases may be advertised for individual components, and any patch version dependencies described here.

#### Release Notes

* Upgraded common helm chart upgraded to make image pull secrets common and global.
* Kubernetes and Compute services cache region data to improve interactivity of the UX, clients likewise are lazily instantiated to improve performance.
* Upgrades to the NVIDIA GPU Operator and Cilium.

#### Operational Notes

* The NVIDIA and Cilium upgrades are accompanied by a new cluster application bundle, and subject to normal auto-upgrade semantics.

#### Breaking Changes

* All charts have been modified to derive resource names from the chart release, this may cause conflicts during deployments to do with ingresses defining the same host and path.
  It's safe to delete the existing ingress and redeploy in this case.

### v0.1.19

_02 May 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.95 :new: |
| Identity | v0.2.63 :new: |
| Region | v0.1.54 :new: |
| Kubernetes | v0.2.64 :new: |
| Compute | v0.1.7 :new: |
| UI | v0.3.16 :new: |
| client-go | v0.1.4 |

#### Release Notes

* Incremental updates to the K8S Lite service.
* Support for Kubernetes 1.33.
* Upgraded tool chain and libraries.
* Some small quality of life improvements for developers across all services.
* Adding back in toasts to the UI now that's stable again.
* Fix token refresh races with concurrent API calls.

#### Operational Notes

* A new Kubernetes cluster application bundle is available that will trigger a rolling upgrade of the control plane, this is to facilitate the removal of a retired CLI flag.

### v0.1.18

_29 April 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.94 :new: |
| Identity | v0.2.62 :new: |
| Region | v0.1.53 :new: |
| Kubernetes | v0.2.63 :new: |
| Compute | v0.1.6 :new: |
| UI | v0.3.15 :new: |
| client-go | v0.1.4 :new: |

#### Release Notes

* Laying the foundation for "K8S Lite", or virtual Kubernetes clusters.
* Allows the export of Kubernetes region data from the region controller.
* This is then offered up to clients via context specific APIs in the Kubernetes and compute controllers.
* Kubernetes and UI are updated to allow virtual cluster provisioning.
* Kubernetes API now exposes auto-upgrade so this can be deactivated or manually configured.
* vCluster upgrades across the board to mitigate some bugs.
* Fixes to AMD GPU operator that weren't caught at deployment time.

#### Breaking Changes

* Virtual clusters now require monitoring, so all environments will need `kube-prometheus-stack` installing before attempting the upgrade.
* Virtual clusters now have expanded volume sizes.
  * These cannot be applied via automation, so auto upgrades are inhibited by a new major version.
  * Upgrading existing cluster manager virtual clusters requires:
    * Manually updating the PVC resource request to 10Gi to mirror the new default, if your CSI provider does not support dynamic volume resize you will need to delete the cluster manager and all managed clusters to pick up the changes (typically local development KinD clusters).
    * Manually update the cluster manager application bundle to `2.0.0`.
    * Delete the failing stateful set in ArgoCD (with non-cascading/orphan semantics).

### v0.1.17

_26 March 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.93 :new: |
| Identity | v0.2.61 |
| Region | v0.1.52 |
| Kubernetes | v0.2.62 :new: |
| Compute | v0.1.5 |
| UI | v0.3.14 :new: |
| client-go | v0.1.3 |

#### Release Notes

* Updates `HelmApplication` CRDs to remove the "interface" and instead pass through the application version to a provisioner's parameters, values and customization hooks.
* Updates the Kubernetes service to limit API resource utilization, increases machine health check limits to allow a baremetal node to be rebooted without being kicked out, adds in parallel image pulls to optimize AI/ML workloads and startup times.
* Updates Kubernetes cluster managers to enable monitoring.
* Some stylistic and functional tweaks to the UI.

#### Operational Notes

* A new Kubernetes application bundle version is released as the above changes will need a cluster rolling upgrade in order to deploy.
  * As such these are subject to the default auto-upgrade rules.
  * If you have manually disabled this behavior, then clusters will need to be manually upgraded during a convenient maintenance window.

### v0.1.16

_19 March 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.92  |
| Identity | v0.2.61 :new: |
| Region | v0.1.52 :new: |
| Kubernetes | v0.2.61 :new: |
| Compute | v0.1.5 :new: |
| UI | v0.3.13 :new: |
| client-go | v0.1.3 |

#### Release Notes

* Fixes a security hole with mTLS where NGINX was required to be able to access any secret anywhere.
* Adds validating admission policies for identity, compute and Kubernetes resources to enforce labels and annotations.
* Styling tweaks to the UI discovered after the Skeleton upgrade.

### v0.1.15

_13 March 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.92 :new: |
| Identity | v0.2.60 :new: |
| Region | v0.1.51 :new: |
| Kubernetes | v0.2.60 :new: |
| Compute | v0.1.4 :new: |
| UI | v0.3.12 :new: |
| client-go | v0.1.3 |

#### Release Notes

* Fixes CVE-2025-22870.
* Improvement to the identity service so a platform-admin can scope organizations to only those they are a member of.
* Updated software components for Kubernetes clusters.
* UI updated to Skeleton UI 3 and Tailwind CSS 4.
* Fix to the region controller to allow more backups and snapshots.

### v0.1.14

_6 March 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.91 :new: |
| Identity | v0.2.59 :new: |
| Region | v0.1.50 :new: |
| Kubernetes | v0.2.59 :new: |
| Compute | v0.1.3 :new: |
| UI | v0.3.11 :new: |
| client-go | v0.1.3 :new: |

#### Release Notes

* Updates across the board to fix CVE-2025-27144.
* Kubernetes cluster controllers improved to increase memory allocation.
* Compute and Region services updated to allow servers to act as routers.
* Removal of insecure configuration options and general maintenance.
* Identity account creation option.

#### Breaking Changes

* The internal format of access and refresh tokens has changed.
  * Users will need to logout and back in again, service accounts will need to regenerate their tokens.

### v0.1.13

_27 February 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.90 :new: |
| Identity | v0.2.58 :new: |
| Region | v0.1.49 |
| Kubernetes | v0.2.58 |
| Compute | v0.1.2 |
| UI | v0.3.10 :new: |

#### Release Notes

* Fixes last failures in the OpenID Connect Basic conformance suite
  * Notably users are now "global" objects scoped to the Identity service namespace, and are referenced by organization users that are organization scoped.
  * This allows per-client session management and more reliable access/refresh token validation/revocation.

#### Upgrade Instructions

* Upgrading to this or a newer release from an older one **MUST** be accompanied by an action that migrates users from being organization scoped to global scoped, and replaces users with organization users while maintaining referential integrity:
  * `go run github.com/unikorn-cloud/migration/v0_1_13/user_migration@latest`

#### Breaking Changes

* Identity Helm chart.
  * The `platformAdministrators.role` option been replaced with `platformAdministrators.roles` and is now an array.
* UI Helm chart.
  * The `oauth2.clientName` option is no longer supported, and you must use `oauth2.clientID` instead.
  * Alternatively, you can use `oauth2.clientSecretSecretName` to source the client ID and secret from a Kubernetes secret.

### v0.1.12

_20 February 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.89 |
| Identity | v0.2.57 :new: |
| Region | v0.1.49 :new: |
| Kubernetes | v0.2.58 :new: |
| Compute | v0.1.2 :new: |
| UI | v0.3.9 :new: |

#### Release Notes

* Primarily fixing prominent failures in the OpenID Connect Conformance suite.
* Fixes critical bugs with code and refresh token reuse.
* Makes all clients require the secure handling of a client secret.
* Small tweak to Cilium so it actually pays attention to the pod CIDR, it ignores what's passed into CAPI by default.

#### Upgrade Instructions

* The everything but the UI service should be upgraded first, this will update the `oauth2client` CRDs and populate client secrets.
* The UI itself can then be upgraded.
  * You will need to find the client secret corresponding to your client ID (`kubectl get oauth2clients -A`), then add a new `oauth2.clientSecret` key to your Helm `values.yaml`.
  * Local development environments will need to set the `tls.private=true` option to trust the TLS endpoints from Node.

### v0.1.11

_14 February 2025_ :heartbeat: :rose:

| Component | Version |
| --- | --- |
| Core | v0.1.89 |
| Identity | v0.2.56 :new: |
| Region | v0.1.48 |
| Kubernetes | v0.2.57 :new: |
| Compute | v0.1.1 |
| UI | v0.3.8 :new: |

#### Release Notes

* Add network prefix selection and some API configuration to Kubernetes, 99% of users shouldn't need this as it's for advanced routing, and they should be using ingress gateways and the like to do it properly.
* Update to CAPI/CAPO.
* Small fix to users.
* Small fixes to cluster manager selection during cluster creation.

### v0.1.10

_07 February 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.89 |
| Identity | v0.2.55 :new: |
| Region | v0.1.48 |
| Kubernetes | v0.2.56 |
| Compute | v0.1.1 |
| UI | v0.3.7 :new: |

#### Release Notes

* A high severity privilege escalation was discovered in the identity service where a regular administrator could delegate platform administration privileges to any user or service account.
  * The platform administrator role cannot be used directly by the API any more.
  * Platform administrators are now added to the system via Helm, and thus go through peer review and change control.
  * Likewise to minimize risk, system accounts using X.509 authentication accounts have also moved to using Helm as a configuration mechanism.
  * The upgrade instructions below will detect any groups with the old `platform-administrator` role, remove it and add in an `administrator` role, allowing continued operation by those users.

#### Upgrade Instructions

* Upgrading to this or a newer release from an older one **MUST** be accompanied by an action that migrates users from a privileged group to one with fewer privileges:
  * `go run github.com/unikorn-cloud/migration/v0_1_9/remove_platform_admin@latest`

### v0.1.9

> [!CAUTION]
> This release has been withdrawn, please consult v0.1.10.

_06 February 2025_

### v0.1.8

_05 February 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.89 :new: |
| Identity | v0.2.53 :new: |
| Region | v0.1.48 :new: |
| Kubernetes | v0.2.56 :new: |
| Compute | v0.1.1 :new: |
| UI | v0.3.5 :new: |

#### Release Notes

* Toolchain upgrades across the board to remove CVEs.
* Quotas can be managed at the organization level with integration with the Compute and Kubernetes services.
* UI has fixed age formatting, added quotas to the dashboard.
* UI RBAC handling completely rewritten to be less error prone.
* UI adds toasts back so you can see quota errors on cluster create/update.
* UI fixes for mobile.
* UI Multi-selects reimplmented to provide better UX, and grouped resources can now be filtered.

#### Breaking Changes

* Identity group, project, service account and user objects now always return an empty array so you can bind directly to them without having to populate and do null checks.
  While this is backward compatible for reads, writes (especially creation) will need to ensure these arrays are instantiated.
  Typescript will do this for you.

#### Upgrade Instructions

* Upgrading to this or a newer release from an older one **MUST** be accompanied by an action that creates resource allocations for existing clusters, and quotas that satisfy these requirements.
* You will need to create a platform admin level service account and do something similar to below:
  * `go run github.com/unikorn-cloud/migration/v0_1_8/quota_migration@latest --region-host=${REGION_ENDPOINT} --access-token=${PLATFORM_ADMIN_SERVICE_ACCOUNT_ACCESS_TOKEN}`
  * Local development environments using private PKI may need the `--region-ca-secret-namespace=cert-manager --region-ca-secret-name=unikorn-ca` flags too.
  * **IMPORTANT** v0.1.9 removed the ability for a service account to have platform admin, so it's recommended to upgrade to this release before v0.1.9 or later.
    * If you don't &ndash; however &ndash; then you _can_ extract an access token for a platform admin enabled account from your browser.
    * ... or you can rewrite the migration to poll the Kubernetes and Compute services for flavor information.

### v0.1.7

_28 January 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.88 |
| Identity | v0.2.52 :new: |
| Region | v0.1.47: |
| Kubernetes | v0.2.55 :new: |
| Compute | v0.1.0 |
| UI | v0.3.4 :new: |

#### Release Notes

* Fix some potential security flaws in the Identity service.
* Adds a very early (alpha) integration with SMTP for email verification onboarding flows.
* Improves the distinction between oauth2 and OIDC provider backends and enables GitHub authentication.
* A epic 3000 line refactoring of UI to separate data from the view improving code cleanliness and legibility.
* Inhibits Kubernetes cluster auto upgrade if a bundle crosses a major semver boundary, this is a breaking change.
* Enables Kubernetes traffic when the source IP isn't from within the cluster e.g. a VPN gateway.

### v0.1.6

_20 January 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.88 |
| Identity | v0.2.51 :new: |
| Region | v0.1.47: |
| Kubernetes | v0.2.54 |
| Compute | v0.1.0 |
| UI | v0.3.3 :new: |

#### Release Notes

* Rearchitects how users are managed internally, they are now objects that can exist in various states and may have metadata associated with them.

#### Upgrade Instructions

* Upgrading to this or a newer release from an older one **MUST** be accompanied by a user migration.  To perform this upgrade _after_ upgrading Unikorn components, run:
  * `go run github.com/unikorn-cloud/migration/v0_1_6/user_migration@latest`

### v0.1.5

_16 January 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.88 |
| Identity | v0.2.50 |
| Region | v0.1.47: |
| Kubernetes | v0.2.54 |
| Compute | v0.1.0 :new: |
| UI | v0.3.2 :new: |

#### Release Notes

* Provides rudimentary support for the compute service, allowing provisioning of raw virtual machines and baremetal servers.

### v0.1.4

_13 January 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.88 |
| Identity | v0.2.50 :new: |
| Region | v0.1.47: |
| Kubernetes | v0.2.54 |
| UI | v0.3.1 :new: |

#### Release Notes

* Fixes a small bug in the service account read code.
* Lots of UI stylistic changes to maintain consistency.

### v0.1.3

_10 January 2025_

| Component | Version |
| --- | --- |
| Core | v0.1.88 :new: |
| Identity | v0.2.49 :new: |
| Region | v0.1.47 :new: |
| Kubernetes | v0.2.54 :new: |
| UI | v0.3.0 :new: |

#### Release Notes

* Fixes CVE-2024-45337 and CVE-2024-45338.
* Adds service accounts to the identity service.
* Adds a meta-user view to the identity service.
* Changes image metadata to be more descriptive in preparation for the compute service.
* Fixes Kubernetes cluster resource leaks during deprovision.

#### Upgrade Instructions

* Image metadata needs to be updated to conform to the new [v2.0.0 specification](https://github.com/unikorn-cloud/specifications/blob/main/specifications/providers/openstack/flavors_and_images.md#image-properties).

### v0.1.2

_28 November 2024_

| Component | Version |
| --- | --- |
| Core | v0.1.86 :new: |
| Identity | v0.2.46 :new: |
| Region | v0.1.46 :new: |
| Kubernetes | v0.2.53 :new: |
| UI | v0.2.41 :new: |

#### Release Notes

* Makes networks generic to lay a foundation for the compute service.
* Adds in server provisioning to the region controller.
* Streamlines region resource deletion.
* Adds Kubernetes control plane node selection resource limits.
* RBAC has been tightened significantly.

#### Upgrade Instructions

* `PhysicalNetwork` resources are now `Network` resources, so all infrastructure in regions with physical network support will need to be torn down and rebuilt.  Further to that, the APIs have been modified in the same way.
* `HelmApplications` now make use of the new generic tags type, so you must delete all existing applications first in order to perform a clean upgrade: `kubectl delete helmapplications -A --all`.

#### Breaking Changes

* Any clients that relied on metadata tags on resources will need to be modified to read this from the generic metadata header, and not the spec.
* Images and flavors should be read from the relevant service from now on as those can provide context specific filtering.

### v0.1.1

_5 November 2024_

| Component | Version |
| --- | --- |
| Core | v0.1.79 |
| Identity | v0.2.44 :new: |
| Region | v0.1.45 :new: |
| Kubernetes | v0.2.46 :new: |
| UI | v0.2.39 :new: |

#### Release Notes

* Improves RBAC so users who use one service no longer need direct access to requisite services.
* Remove flavor/image filtering from the region service, these need to be proxied through the top level service e.g. Kubernetes, as those contain the domain specific knowledge to filter correctly.

#### Breaking Changes

* 3rd party UX components will be affected by a number of API changes:
  * Any ACL reliant code needs to be aware endpoints have changed e.g. `projects` becomes `identity:projects`.
    * In general the form is `<service>:<resource>` meaning we can do things like `region:*` in the future as a terse way of granting access to everything.
    * A full list of officially supported endpoints is available [here](https://github.com/unikorn-cloud/identity/blob/v0.2.44/charts/identity/values.yaml#L80).
  * Users can no longer access flavors and images directly through the region service.
    * This is a fairly trivial thing to fix as the APIs remain the same, they now belong to the service that consumes those resources.
    * This allows the Kubernetes service for example to filter out flavors that `kubeadm` cannot use, independently of the compute service.

### v0.1.0

_1 November 2024_

| Component | Version |
| --- | --- |
| Core | v0.1.79 |
| Identity | v0.2.42 |
| Region | v0.1.44 |
| Kubernetes | v0.2.45 |
| UI | v0.2.38 |

#### Release Notes

* Initial baseline.
