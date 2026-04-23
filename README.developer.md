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

### Preparing the Release Branch

All release work is done on a dedicated release branch named `v<Major>.<Minor>.x` — e.g. `v1.16.x`.
Every change to the release branch must go through CI and receive human approval via a pull request; no direct pushes.

You may optionally create the branch manually ahead of time, for example to cherry-pick fixes before running the release script:

```shell
git switch -c v1.16.x <commit-sha>
git push origin v1.16.x
```

If the branch doesn't exist yet when the script runs, it will create and push it automatically.

Before starting the release script you may cherry-pick any fixes from `main` that you want included.
Always get the fix merged to `main` first — picking from development branches bypasses review, tends to cause reverts later, and the fix won't be present in future releases.
Raise each cherry-pick as a pull request targeting the release branch and let CI run before proceeding.

### Running the Release Script

The [release script](https://github.com/unikorn-cloud/scripts/blob/main/release.py) automates the bulk of the release work.
It processes components in dependency order: `core` → `identity` → `region` → `compute` → `kubernetes` → `ui`.

For each component it:

* Updates `Chart.yaml` — sets both `version` and `appVersion` to the release version without a `v` prefix (Helm 4 compatible), e.g. `1.16.0`
* Propagates Go library dependencies down the chain (`go get`, `go mod tidy`)
* For official releases (not RCs): updates the OpenAPI spec version and runs `make validate`
* For the UI: regenerates OpenAPI clients (`npm run openapi:*`)
* Creates a `bump` branch, commits all changes, opens a pull request targeting the release branch
* Waits for CI checks to pass, then pauses for human approval — approve and merge the PR, then press Enter to continue
* After merge: tags the release branch HEAD and pushes the tag

Invoke it from the directory containing all component repos:

```shell
# Official release
release.py --version v1.16.0

# Release candidate
release.py --version v1.16.0-rc1

# Resume from a specific component after a failure
release.py --version v1.16.0 --from-step region
```

`client-go` is not handled by the script — for official releases, tag and push it manually.

### Recovering from Failures

If a GitHub Action fails mid-run, the typical recovery is:

1. Delete the local and remote `bump` branches in the affected repo:

   ```shell
   git branch -D bump
   git push origin --delete bump
   ```

2. Get the fix merged to `main`, then cherry-pick it onto the release branch as a pull request targeting `v<Major>.<Minor>.x`.
   Getting the fix into `main` first ensures it is present in all future releases automatically.
   Picking directly from development branches bypasses review and tends to cause reverts and complications later.

3. Restart the script from the failed component:

   ```shell
   release.py --version v1.16.0 --from-step <component>
   ```

#### Broken Dependency Updates

A common failure is CI rejecting a downstream component (e.g. `region`) because an engineer introduced a breaking API change in an upstream library (`core` or `identity`) without updating all consumers.

In this case:

* The fix must first be made in the upstream repo and merged to `main`
* Cherry-pick it onto the upstream repo's release branch and re-release that component
* Only once the upstream component is cleanly tagged can you continue downstream

The dependency chain must be clean at each link before proceeding.
Take the opportunity to remind the engineer responsible that breaking changes in shared libraries require fixes in all consumers before merging.

## Testing

The entire point of the release process is to ensure that everything works together, with all components using the same library dependencies.

Testing is performed on release candidates, as these can be fixed.
Revoking a full release requires manually updating the Helm repository indexes, deleting the tag and release, and finally revoking the images.
You really want to avoid this!

Once you're happy with testing, re-release your release candidate as an official one by following the steps above.

### Deployment

Deploy the release candidate to the development or staging cluster.

It's a good idea to have an instance of all the various resource types running before upgrading, so you can watch for errors.
If there is an error it's probably customer impacting and needs fixing in another release candidate.

### API Tests

Run the API test jobs in GitHub Actions for `identity` and `region`.
When triggering the jobs, set the test source to the release branch (e.g. `v1.16.x`) rather than `main` — this prevents tests that verify unreleased upstream changes from causing false positives.

### Scope

It's usually a good idea during creation of a release candidate to start drafting the release notes in this repository, collating all the changes that have happened.

Then it's up to your judgment what needs testing based on the release contents.
Remember, developers cannot be trusted, even if they say it has been tested, it has rarely been _fully_ tested.
This is your job, if something makes it though the cracks, you're at fault, so be as thorough as makes you comfortable.

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

Patch releases are made from the release branch `v<Major>.<Minor>.x` — the same branch used for the official minor release.
After an official release that branch already exists, so there is no need to create it.
If for some reason it doesn't exist (e.g. patching a very old release), create it from the release tag:

```shell
git switch -c v1.3.x v1.3.0
git push origin v1.3.x
```

### Preparing a patch release

Get the bug fix reviewed and merged to `main` first.
This ensures the fix is present in all future releases and not just the patch.

Check out the release branch and cherry-pick the fix:

```shell
git switch v1.3.x
git cherry-pick <Commit SHA>
```

This may result in a merge conflict — make any necessary changes to make the backport apply cleanly.

Update `Chart.yaml` with the RC version (no `v` prefix), e.g. `1.3.1-rc1`.
Raise the cherry-pick and version bump as a pull request targeting the release branch and let CI run.

Once the pull request is merged, pull the branch and tag the release candidate:

```shell
git pull origin v1.3.x
git tag v1.3.1-rc1
git push origin v1.3.1-rc1
```

Deploy the release candidate and run the same tests as for a minor release.
Only once testing is passing should you update `Chart.yaml` to the final version (e.g. `1.3.1`), raise a pull request, merge, then tag the full patch release:

```shell
git pull origin v1.3.x
git tag v1.3.1
git push origin v1.3.1
```

Add it to the release notes.
