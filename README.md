# tehom

> Inter-dependency management for mono-repos.

## Install

```
$ yarn add tehom
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
    "version:tehom": "tehom version",
    "publish:tehom": "tehom publish"
  }
}
```

## API

### Config

You config should live in your package.json at a top-level key called `tehom`.

#### `tehom.dependencies`

By default, on `tehom version`, all dependent packages will have their dependencies updated to reflect the manifest in package.json. What it will not do by default is update that dependent's version. The `dependencies` option lets you configure this on a package-by-package basis.

The dependencies object is an object where the key is the npm package name of package. Each package has a configuration object.

| Option     | Type            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| mustPatch  | `Array<string>` | An Array of npm package names (managed by this monorepo) that should get patched if the package updates. If the package already has version change queued that differs from its current version, the `mustPatch` will be ignored in favor of the specified version.                                                                                                                                                                                                      |
| mustFollow | `Array<string>` | An Array of npm package names (managed by this monorepo) that should follow the semver increment if the package updates. In other words, if this package goes from 2.0.1 -> 2.1.0, each package in the array would bump a minor version. If the package already has version change queued that differs from its current version, tehom will check to see if that version matches/exceed `mustFollow`. If it does not match or exceed, `mustFollow` will take precedence. |

#### `tehom.versions`

### `version`

### `publish`
