# 48470

Reproduction for [Renovate pull request 48470](https://github.com/renovatebot/renovate/pull/48470),
which adds native support for the `boms` attribute of `maven.install()` in `MODULE.bazel`.

## Current behavior

Renovate's `bazel-module` manager parses the `artifacts` attribute of `maven.install()`, but it
ignores the `boms` attribute.

The BOM in [`MODULE.bazel`](MODULE.bazel) is therefore never extracted, so Renovate never looks up
new releases for it and never opens a pull request to update it:

```starlark
maven.install(
    boms = [
        "com.google.guava:guava-bom:33.4.7-jre",
    ],
    ...
)
```

The `artifacts` entry in the same call is updated correctly, which makes the gap easy to miss.

## Expected behavior

Renovate extracts the `boms` attribute of `maven.install()` just like `artifacts`:

- the BOM is reported as the dependency `com.google.guava:guava-bom`
- it uses the `maven` datasource and the `gradle` versioning, like other Maven dependencies
- it inherits the `repositories` of the enclosing `maven.install()` call as its registry URLs
- Renovate opens a pull request that bumps `33.4.7-jre` to `33.4.8-jre`
