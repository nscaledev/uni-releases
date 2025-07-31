# Release Process Guide

This documents the process for delivering a release.

## Release Versioning

Releases follow the normal semantic version specification of `major.minor.patch`.

### Major Releases

These are reserved for changes that require major breaking API changes.
Where possible, these should be avoided, using strategies such as incremental update, or versioned APIs with an upgrade path to get from A to B.
Under no circumstances are a product team allowed to bump the major version to "sound better".

### Minor Releases

These form the majority of releases in the ecosystem.
Minor releases contain new features, new APIs, or APIs with additive, backward-compatible updates.

### Patch Releases

Given the nature of distributed systems, and this one is spread across a number of different repositories, patch releases are only for small bug fixes in individual components.
For example, a security fit in the Identity service can have a `v1.0.1` release, without having to also do a full release for every other component.

## Release Types

There are a number of different release types that are defined for use in the software development life-cycle as defined below.

### Official Release

This will be either a major or minor version bump.
This will contain features as defined by the semantic version.

An official release requires:

* All components to be built and tagged with the same version.
* Where a component depends on another (library dependency), those dependencies will be the exact version of the release.
* All OpenAPI specification must be correctly versioned.
* OpenAPI documentation generated.

### Release Candidates

These are typically created before an official release for integration testing.
These use the semantic version `v1.0.0-rc1`.
Tags with this form are exclusively reserved for integration testing.

A release candidate follows the same basic shape as an official release, but may omit some steps, for example documentation generation is not required.

### Patch Release

These are used for bug fixes, and are the only time components are allowed to drift from one another version-wise.

## Creating an Official Release or a Release Candidate

You will need to release all of the [components](https://github.com/nscaledev/uni-releases?tab=readme-ov-file#core-components) defined in the main documentation.

For official releases only you will also need to release all of the [DX components](https://github.com/nscaledev/uni-releases?tab=readme-ov-file#dx-components) defined in the main documentation.
Additionally you will need to create a change log entry in this repository.

### Version Updates

Edit `charts/${chart}/Chart.yaml`, updating the version, which defines the canonical version for the package.

### Go Packages

A simplified version of the Go language dependency chain looks like:

* Core
* Identity
* Region
* Compute & Kubernetes
* client-go (official release only)

Start with core and work down.

For major or minor releases only, and for services with an API, update the schema located in `pkg/openapi/server.spec.yaml`.

#### Update the Library Dependencies

This is a simple propagation step.

If Core has been tagged as `v1.2.3`, and you are updating Identity, the next in the chain run:

```shell
go get github.com/unikorn-cloud/core@v1.2.3
go mod tidy
```

When Identity is tagged, you can update Region:

```shell
go get github.com/unikorn-cloud/identity@v1.2.3
go mod tidy
```

This will magically update Core at the same time.

And so on.

#### Code Generation and API Updates

Typically, if the command exists you will run:

```
make validate`
```

This will pick up any OpenAPI dependencies and generate any server stubs, clients etc. and ensure the documentation is still valid.

You can then go ahead and ensure library dependencies still work.

```
make lint
```

If they don't, then whoever is responsible has pushed a breaking change to a library and not made the necessary changes in all consumers of that library.
Take a moment to educate them!

#### Commit, Merge and Tag

Do the usual GitHub pull request dance, pull down the new main branch with the changes in, ensure you are at the correct release checkout.
You can then use the [release script](https://github.com/nscaledev/uni-scripts/blob/main/release) to tag, push to GitHub and trigger the release action.

The exception to the rule is `client-go` as that doesn't have a helm chart, just manually tag and push.

### UI

The UI is pretty similar to handling Go components, and uses the same Helm chart layout as described already to define its version.
The merge can tag flow is the same, only the following differences need to be followed.

#### Updating OpenAPI Clients

Once all of the Go services are merged, we can update the client code:

```shell
npm run openapi:identity
npm run openapi:region
npm run openapi:compute
npm run openapi:kubernetes
```

Double check everything still compiles:

```shell
npm run check
npm run lint
```

Like before, if it doesn't educate your developer on their responsibilities when making breaking changes.

### API Documentation (Official Release Only)

Run once all the services with an API are tagged, you simply tag the repository with the release version, push, and that will trigger an action to build the documentation.

## Testing

The entire point of the release process is to ensure that everything works together, with all components using the same library dependencies.

Testing is performed on release candidates, as these can be fixed.
Revoking a full release requires manually updating the Helm repository indexes, deleting the tag and release, and finally revoking the images.
You really want to avoid this!

Once you're happy with testing, re-release your release candidate as an official one by following the steps above.

### Scope

In lieu of any automated test suites, beyond unit tests, it's up to you to define the criteria.
It's usually a good idea during creation of a release candidate to start drafting the release notes in this repository, collating all the changes that have happened.

Then it's up to your judgment what needs testing based on the release contents.
Remember, developers cannot be trusted, even if they say it has been tested, it has rarely been _fully_ tested.
This is your job, if something makes it though the cracks, you're at fault, so be as thorough as makes you comfortable.

It's usually a good idea to have a long running deployment somewhere with the newest official release installed.
It's also a good idea to have an instance of all the various resource types, thus you can watch for errors when performing the upgrade.
If there is an error, it's probably customer impacting and needs fixing in another release candidate.

#### Cluster Testing

Here's a typical set of tests to run through if a specific infrastructure service has some changes:

* Does the upgrade instantly break my cluster?
* If a new application bundle has been released, does manually updating the version break it? (Better to run this now than await auto upgrade to happen randomly in the future!)
* Can I provision a new cluster?
  * It's also important to have _multiple instances_ of a cluster type running in the same project, most people miss these bugs when resources from one bleed into another.
* Can I edit things about the cluster and does it do what's expected?
* Can I delete the cluster afterwards.

## Release Notes (Official release only)

We've mentioned this previously, you should have this drafted already and used it to guide your test strategy.

Be sure to mention any operational notes, anything that is going to impact customers e.g. upgrades, even if they just work.

Be very critical of any breaking changes you need to document.
Try and come up with a solution that makes them go away.
But there are examples where they are safe as far as the end user is concerned that needs a manual tweak here and there that are acceptable.

In general, follow the example set.

## Patch Releases

Patch releases are made from hotfix branches. A hotfix branch is named `v<Major>.<Minor>.x`; e.g., `"v1.3.x"`, and is branched from the release tag of the last minor version.
For example, you would create the hotfix branch `v1.3.x`, if it didn't exist already, with:

    git switch -c v1.3.x v1.3.0

### Preparing a patch release

If a bug needs fixing, have it reviewed and merged to `main`.

Next either checkout the hot fix branch or create one.
This will always be in the form `v1.2.x` (where `x` is literal, not a variable!), as explained above.

Cherry pick the fix on to the hotfix branch:

```shell
git cherry-pick <Commit SHA>
```

This may result in a merge conflixt -- make any necessary changes to make the backport apply cleanly.

Create a release candidate by tagging the HEAD of the hotfix branch, and perform any testing.

Do not be pressured into skipping testing, you'll only waste more time - and upset end-users more - if you make a mistake or miss some edge case the developer also did.

Once satisfied with testing, create the full patch release by committing any version bumps (e.g., in Chart.yaml) to the hotfix branch, then tagging with the patch version and pushing that.
The GitHub Actions should take over and publish images, push charts, and so on.

Add it to the release notes.
