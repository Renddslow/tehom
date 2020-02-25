# @tehom/release

> Fine-grained monorepo release automation.

## Install

```
$ yarn add @tehom/release
```

## Usage

```json
{
  "tehom": {
    "dependencies": {},
    "versions": {
      "<pkg-A>": "1.2.3",
      "<pkg-B>": "2.0.1"
    }
  },
  "scripts": {
    "release": "@tehom/release version && @tehom/release prepare && @tehom/release publish"
  }
}
```

## API

### Config

You config should live in your package.json at a top-level key called `tehom`.

#### `tehom.dependencies`

By default, on `@tehom/release version`, all dependent packages will have their dependencies updated to reflect the manifest in package.json. What it will not do by default is update that dependent's version. The `dependencies` option lets you configure this on a package-by-package basis.

The dependencies object is an object where the key is the npm package name of package. Each package has a configuration object.

| Option     | Type            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| mustPatch  | `Array<string>` | An Array of npm package names (managed by this monorepo) that should get patched if the package updates. If the package already has version change queued that differs from its current version, the `mustPatch` will be ignored in favor of the specified version.                                                                                                                                                                                                      |
| mustFollow | `Array<string>` | An Array of npm package names (managed by this monorepo) that should follow the semver increment if the package updates. In other words, if this package goes from 2.0.1 -> 2.1.0, each package in the array would bump a minor version. If the package already has version change queued that differs from its current version, tehom will check to see if that version matches/exceed `mustFollow`. If it does not match or exceed, `mustFollow` will take precedence. |
| ignore     | `Array<string>` | An Array of npm package names (managed by this monorepo) that should be ignored when updating dependents. This is useful if you need a package to stay on an older version of this package.                                                                                                                                                                                                                                                                              |

#### `tehom.versions`

`versions` is essentially the master manifest for all of your monorepo packages. If you don't want to include a package in Tehom automation, simply exclude it from the object.

The `tehom.versions` object is an object where the key is the npm package name and the value is the package version. This should be a valid semver version.

Tehom relies on the a version mismatch between the main package.json Tehom manifest and an individual package's version. As such, you should update only the `tehom.versions` object when a package version has changed to allow the automation to run correctly.

### `version`

Compares the `version` of each package to the corresponding `tehom.versions.<pkg-name>` in the main package.json to see if there's a mismatch. If a mismatch exists, the `version` is updated in the individual package's package.json, as well as any other package that depends on it.

Version follows the following workflow:

1. Update the versions of each package based on `tehom.versions`
2. Update any dependencies across the monorepo on the updated package
3. Apply `mustPatch` or `mustFollow` rules to dependencies
4. Run `yarn` to ensure yarn.lock is updated correctly

### `prepare`

Prepare picks up where `version` left off and prepares the changelogs for each modified package, commits, and tags everything for release. Prepare and version are split apart in the event that your release process has some intermediary steps, this allows the git processes to still be automated at the end, creating a single release commit.

#### Options

##### `--skip-changelog`

Tehom will prepare a changelog for you assuming there is a `CHANGELOG.md` at the root of the package (it will simply skip for a package if it cannot find one). It also assumes that you are using the Keep a Changelog format for your changelog and have previously staged your changes in the `[Unreleased]` section.

If any of the above scenarios apply to you, we recommend skipping the changelog process altogether and perhaps adding your own ahead of `@tehom/release prepare`.

### `publish`

Publish is the final step in the Tehom workflow. It will scoop up each modified package and publish it.

#### Options

##### `-b, --build-first`

By default, Tehom puts the build process in your hands. Often this is done with a `prepare` script in your package.json. If that doesn't jive with your workflow, you can pass in the build-first flag to have Tehom run `yarn build` before each package is published.
