# Unikorn Releases

![Unikorn Logo](https://raw.githubusercontent.com/unikorn-cloud/assets/main/images/logos/light-on-dark/logo.svg#gh-dark-mode-only)
![Unikorn Logo](https://raw.githubusercontent.com/unikorn-cloud/assets/main/images/logos/dark-on-light/logo.svg#gh-light-mode-only)

Unikorn is a relatively large collection of independent microservices that combine to provide IaaS/PaaS functionality.
Maintaining a matrix of compatible versions is complex due to a lot of inter-dependencies.
This repository aims to simplify the task by bundling together versions that are know to work together and notify of any breaking changes before the project becomes GA.

## Releases

### v0.1.2

_28 November 2024_

| Component | Version |
| --- | --- |
| Core | v0.1.86 :new: |
| Identity | v0.2.46 :new: |
| Region | v0.1.46 :new: |
| Kubernetes | v0.2.53 :new: |
| UI | v0.2.41 :new: |

#### Relase Notes

* Makes networks generic to lay a foundation for the compute service.
* Adds in server provisioning to the region controller.
* Streamlines region resource deletion.
* Adds Kubernetes control plane node selection resource limits.
* RBAC has been tigtened significantly.

#### Upgrade Instructions

* `PhysicalNetwork` resources are now `Network` resources, so all infrastructure in regions with physical network suport will need to be torn down and rebuilt.  Further to that, the APIs have been modified in the same way.
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
* Remove flavor/image filtering from the region service, these need to be proxied through the top level service e.g. kubernetes, as those contain the domain specific knowledge to filter correctly.

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
