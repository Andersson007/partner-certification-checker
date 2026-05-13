# Releasing re-usable workflows

## Changelog

To track changes in this repository made between versions and to generate a changelog, we use [changelog fragments](https://docs.ansible.com/ansible/latest/community/development_process.html#creating-a-changelog-fragment).

### When updating tools versions in the reusable workflow

When updating versions of tools in the reusable workflow, ensure that the changelog, and any notifications to partners, include porting guides for related breaking changes.

For example, when bumping the `ansible-core` version from `2.16` to `2.17`, create a corresponding changelog fragment. It should note that, because workflow version N runs the `ansible-test sanity` command from ansible-core 2.17, if a partner has a `tests/sanity/ignore-2.16.txt` file, they need to copy it to `tests/sanity/ignore-2.17.txt` to prevent errors.

## Immutable releases

This repository uses [GitHub Immutable Releases](https://docs.github.com/en/code-security/concepts/supply-chain-security/immutable-releases) to protect supply chain integrity.
When a release is published, that release tag cannot be moved or deleted.

Enable **Immutable releases** in the repository settings under **Settings > Code security > Immutable releases**. You need to do this one time only.

## Release process

1. Based on the [Semantic Versioning](https://semver.org/) conventions and [changelog/fragments](changelog/fragments), determine a proper release version number.

   - When we change any versions of tools, their arguments or anything else that might result in failures on partners' side, we release a major version.
   - If this is the case, make sure there's a corresponding changelog entry containing a porting guide as explained in the [Changelog](#changelog) section.
2. Follow the [Releasing guidelines](https://docs.ansible.com/ansible/devel/community/collection_contributors/collection_release_without_branches.html) where applicable (for example, we don't publish this collection on Galaxy).
3. Create an annotated tag and push it:
   ```bash
   git tag -a vX.Y.Z -m "Release vX.Y.Z"
   git push origin vX.Y.Z
   ```
4. Create a **GitHub Release** from that tag. The release is immutable when published.
5. For v1.x releases only, update the floating `v1` tag to point to the new release.
   ```bash
   git tag -f v1 vX.Y.Z
   git push upstream -f v1
   ```
6. Update the version reference in the [calling workflow](.github/workflows/certification.yml) to `@vX.Y.Z` and open a PR.

### Floating tag transition plan

The `v1` floating tag is kept for backwards compatibility with users who referenced `@v1` in their workflows.

When `v2.0.0` is released:

- Freeze the `v1` tag at its last v1.x release (stop updating it).
- **Do NOT create a floating `v2` tag.** All v2+ users must use exact version pins only.
- Exact version pins combined with immutable releases ensure that a published release cannot be tampered with. Users can use [Dependabot](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/keeping-your-actions-up-to-date-with-dependabot) to receive automatic pull requests when new versions are released.

## Post-release actions

TBD when defined

If there are any breaking changes in a particular release, make sure that notifications to partners to update the workflow version in their repos mention the changes and contain a link to a porting guide (related changelog entry) as explained in the [Changelog](#changelog) section.
