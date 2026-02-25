# Releasing re-usable workflows

## Changelog

To track changes in this repository made between versions and to generate a changelog, we use [changelog fragments](https://docs.ansible.com/ansible/latest/community/development_process.html#creating-a-changelog-fragment).

### When updating tools versions in the reusable workflow

When updating versions of tools in the reusable workflow, ensure that the changelog, and any notifications to partners, include porting guides for related breaking changes.

For example, when bumping the `ansible-core` version from `2.16` to `2.17`, create a corresponding changelog fragment. It should note that, because workflow version N runs the `ansible-test sanity` command from ansible-core 2.17, if a partner has a `tests/sanity/ignore-2.16.txt` file, they need to copy it to `tests/sanity/ignore-2.17.txt` to prevent errors.

## Release process

1. Based on the [Semantic Versioning](https://semver.org/) conventions and [changelog/fragments](changelog/fragments), determine a proper release version number.

   - When we change any versions of tools, their arguments or anything else that might result in failures on partners' side, we release a major version.
   - If this is the case, make sure there's a corresponding changelog entry containing a porting guide as explained in the [Changelog](#changelog) section.
2. Follow the [Releasing guidelines](https://docs.ansible.com/ansible/devel/community/collection_contributors/collection_release_without_branches.html) where applicable (for example, we don't publish this collection on Galaxy).
3. When releasing a major release, besides a tag for the release `X.y.z`, also create a corresponding tag `vX` that will always point to the latest release of this major release. For example, if you released version `1.0.0`, create tag `v1` that will point to the same commit as tag `v1.0.0`.
4. When releasing a minor or patch release `x.Y.Z`, move a corresponding `vX` tag to point to it. For example, when releasing version `1.1.0`, move tag `v1` to point to the same commit as `v1.1.0`.

   1. Get the commit SHA for the tag: `git rev-parse v1.1.0`
   2. Move the existing local `v1` tag to the new commit SHA: `git tag -f v1 <commit-sha-of-v1.1.0>`
   3. Force push the tag to GitHub: `git push upstream -f v1`
5. Update the release part in the [calling workflow](.github/workflows/certification.yml) if needed, for example, `@v1` -> `v2` so that new users copy its latest version.

## Post-release actions

TBD when defined

If there are any breaking changes in a particular release, make sure that notifications to partners to update the workflow version in their repos mention the changes and contain a link to a porting guide (related changelog entry) as explained in the [Changelog](#changelog) section.
