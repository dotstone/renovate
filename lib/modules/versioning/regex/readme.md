Regular Expression Versioning is designed to be a flexible fallback versioning approach if Renovate's other versioning schemes don't do the job.

The `regex` scheme makes use of Regular Express capture groups.
The valid capture groups for `regex` versioning are:

- `major`, `minor`, and `patch`: at least one of these must be provided. When determining whether a package has updates, these values will be compared in the standard semantic versioning fashion. If any of these fields are omitted, they will be treated as if they were `0` -- in this way, you can describe versioning schemes with up to three incrementing values.
- `build`: this capture group can be used after you've already used the `major`, `minor` and `patch` capture groups and need a fourth version part. `build` updates are handled like `patch` updates.
- `revision`: this capture group can be used after you've already used the `build` capture groups and need a fifth version part. `revision` updates are handled like `patch` updates.
- `prerelease`: this value, if captured, will mark a given release as a prerelease (e.g. unstable). If this value is captured and you have configured `"ignoreUnstable": true`, the given release will be skipped.
- `compatibility`: this value defines the "build compatibility" of a given dependency. A proposed Renovate update will never change the specified compatibility value. For example, if you are pinning to `1.2.3-linux` (and `linux` is captured as the compatibility value), Renovate will not update you to `1.2.4-osx`.

The compatibility concept was originally introduced for Docker versioning but sometimes package authors may use/misuse suffixes to mean compatibility in other versioning schemes.

Here is an example of using `regex` versioning to correct behavior of the `guava` Maven package, which misuses suffixes as compatibility indicators:

```json
{
  "packageRules": [
    {
      "matchPackageNames": ["com.google.guava:guava"],
      "versioning": "regex:^(?<major>\\d+)(\\.(?<minor>\\d+))?(\\.(?<patch>\\d+))?(-(?<compatibility>.*))?$"
    }
  ]
}
```

Here is another example, this time for handling `python` Docker images, which use both pre-release indicators as well as version suffixes for compatibility:

```json
{
  "packageRules": [
    {
      "matchDatasources": ["docker"],
      "matchPackageNames": ["python"],
      "versioning": "regex:^(?<major>\\d+)\\.(?<minor>\\d+)\\.(?<patch>\\d+)(?<prerelease>[^.-]+)?(-(?<compatibility>.*))?$"
    }
  ]
}
```

Here is another example, this time for handling Bitnami Docker images, which use `build` and `revision` indicators as well as version suffixes for compatibility:

```json
{
  "packageRules": [
    {
      "matchDatasources": ["docker"],
      "matchPackagePrefixes": ["bitnami/"],
      "versioning": "regex:^(?<major>\\d+)\\.(?<minor>\\d+)\\.(?<patch>\\d+)(:?-(?<compatibility>.+)(?<build>\\d+)-r(?<revision>\\d+))?$"
    }
  ]
}
```
