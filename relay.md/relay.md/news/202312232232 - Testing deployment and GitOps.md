---
creation date: 2023-12-23 22:32
modification date: Saturday 23rd December 2023 22:32:42
relay-document: 4647e52a-9a77-4973-b96f-24be08c5bac5
relay-to:
  - news@relay.md
summary: Separate and independent deployment for testing of relay.md codebase
tags:
  - relaymd/journal
type: uniquenote
---

# Testing deployment and GitOps
## Testing environment
For relay.md we finally have a distinct [test environment](https://test.relay.md) which will come with most recent code base. We expect things to break there, instead of production on [relay.md](https://relay.md).

Let us know if you want to know more about how we operate things on the backend.
## GitOps
Thanks to git hooks and continuous integration and delivery, we finished automating deployments. With this, we get `develop` branch deployed to `test.relay.md`, and once a release is tagged as `v*`, we get a deployment on production. We only need to touch any of this in case we have to modify configuration, e.g. environment variables or secrets.
 
We make use of [gitea](https://gitea.com) and [drone](https://drone.io) for our pipelines.