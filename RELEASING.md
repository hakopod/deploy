# Maintain and release the deployment action

Develop, test and release the action directly in
[hakopod/deploy](https://github.com/hakopod/deploy). The root `action.yml`,
`index.mjs` and `deploy.mjs` are the files GitHub runs. Tests, documentation and
CI are maintained alongside them.

## Review changes

Create a feature branch from the current `main`, preserving any existing local
work. Keep the README's inputs, outputs and examples aligned with `action.yml`
and the runtime. The action has no package dependencies or generated bundle.
With Node.js 24, run:

```sh
npm test
```

Open a pull request in this repository. When updating its branch, fetch and
rebase onto `origin/main`; preserve individual commits. Merge reviewed pull
requests with:

```sh
gh pr merge --rebase
```

Do not use merge commits or squash merges. Require passing CI for the reviewed
change, then confirm the merged commit's CI before releasing it.

## Verify the release commit

Use a clean checkout, fetch `origin/main`, and record its exact commit. Preserve
any local changes before checking out another commit. Publish from the merged
main commit whose checks passed.

The CI workflow runs `npm test` and invokes the root action with a synthetic
deployment API on a GitHub runner. This verifies packaging, inputs, requests and
outputs. Keep that fixture clearly identified as synthetic data. A separate
consumer workflow can exercise `uses: hakopod/deploy@<full-commit-sha>` to verify
the proposed release's public action reference.

Use the named Hakopod development cluster/context for real deployment
acceptance; never mutate an existing operator cluster. Record test results,
GitHub runner results and real-cluster results separately. A fixture response
does not establish that a deployment succeeded in a real cluster.

Before tagging, check that `action.yml` specifies `node24`, resolves
`main: index.mjs` from the root and matches the documented action interface.
Review the README examples, API compatibility and any changed permissions.

## Publish a version

Choose the next semantic version for the reviewed commit. Use a patch version
for compatible fixes, a minor version for compatible additions and a new major
version for breaking changes. Create the version tag on that exact commit and
verify the remote tag after pushing it.

Semantic-version tags such as `v1.0.0` are immutable. Never move or recreate a
published version tag to include later changes. Publish a new version instead;
earlier tags and their files remain part of release history.

The major alias `v1` follows tested, backward-compatible releases in major
version 1. Advance it only as part of publishing a new compatible release, and
verify that it resolves to the same commit as that release's semantic-version
tag. Breaking changes use a new major alias, such as `v2`. Do not advance `v1`
for an unreleased documentation or maintenance commit. Users who require
immutable action code can pin the full release commit SHA.

Create the GitHub release from the semantic-version tag. Include the change,
compatibility notes, exact commit and validation results in its release notes.
In the GitHub release editor, select **Publish this Action to the GitHub
Marketplace**, complete its required categories and terms, and publish the
release. A GitHub release alone does not confirm Marketplace publication.

## Check the public release

Verify these public results after publishing:

- The Marketplace listing opens and links to `hakopod/deploy`.
- The release is published under the intended semantic-version tag.
- That version tag and its major alias resolve to the reviewed release commit.
- The root action metadata and entrypoint are present at that commit.
- The consumer example works with the major alias and can be pinned to the full
  release commit SHA.

Report the Marketplace URL, release URL, release commit and verification
results. State any pending Marketplace publication or unverified deployment
behavior separately.
