# is-new-version
a github action to check if the current new version is already exists as a git tag. It checks the version in a `pyproject.toml` file, and if a tag of the version does not exist, it states so in its output. It also fetches the release notes from a changelog.

## Example
An example workflow that uses this action to check for new tags and release a new version if it is new:
```yaml
name: Release new tags
on:
    push:
        branches:
            - main

jobs:
    check-tag:
        name: check for new tags
        runs-on: ubuntu-latest

        steps:
            - name: check for new version
              id: check-version
              uses: biocatchltd/gh-actions-is-new-version@latest

            - name: release a new version
              if: steps.check-tag.outputs.is-new == 'true'
              uses: softprops/action-gh-release@v2
              with:
                body: ${{ steps.check-version.outputs.release-notes }}
                tag_name: ${{ steps.check-version.outputs.found-version }}
                target_commitish: ${{ github.sha }}
                prerelease: ${{ steps.check-tag.outputs.is-prerelease }}
```

## inputs
* `toml-path`: the path to the TOML file to use to determine the version, default to `pyproject.toml`
* `changelog-path`: the path to the markdown file to use to determine the version, default to `CHANGELOG.md`

## outputs
* `is-new`: whether the step detected a new version
* `found-version`: the version detected by the action
* `release-notes`: the markdown snippet of the release notes of the version, will be blank if no version is not new
* `is-prerelease`: whether the version is detected as a pre-release (currently, all versions with a `-` in them are considered pre-releases). Will be blank if no version is not new.
