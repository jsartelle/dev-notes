---
{"dg-publish":true,"dg-path":"Notes/NPM and Yarn.md","permalink":"/notes/npm-and-yarn/","tags":["tech/web"]}
---


# Types of dependencies

- `dependencies`
    - packages ==required by your package at runtime==
    - are always installed on `npm install` (or `yarn install`, etc)
- `devDependencies`
    - packages that are ==only required during development== (ex. testing, transpilation, etc)
    - are not installed:
        - if your package is being installed as a dependency of another package
        - if you pass the `--production` flag
- `peerDependencies`
    - used if your package doesn't require the dependency, but instead ==is used by the dependency== (your package is a *plugin* for the dependency)
    - should only be [[Development/Notes/NPM and Yarn#package.json version pinning syntax\|pinned]] to major versions, because the package manager will error if the peerDependency can't be resolved correctly

# package.json version pinning syntax

- No pinning (always install the latest version): `*` or `x`
- Same major version: `^1.0.4` or  `1` or `1.x`
- Same minor version: `~1.0.4` or `1.0` or `1.0.x`
- Exact version: `1.0.4`
- `>`, `>=`, `<`, `<=`, etc. ignore the major/minor/patch division

# Commands

## npm

- `npm list`: list installed packages (useful for [[Development/Notes/Terminal#grep\|grepping]])
    - `-g`: global
    - `-a`: include nested dependencies
    - `-l`: include descriptions
- `npm ls foo`: see which installed packages have a dependency on `foo`
- `npm pack`: create an archive of all the files listed in the `package.json` `files` array
    - simulates how your package will look when installed as a dependency

# List versions of dependencies

- Shows every package that lists `vue` as a dependency, and which version is installed

```shell
npm why vue
```

```shell
yarn why vue
```

# npm-check

- Scans for out of date and unused packages
- Pass `-u` to interactively update packages by patch/minor/major version

```shell
npx npm-check
```

# Link local dependencies

- to use a local package folder as a dependency of another repo (ex. for testing package changes):
- in the package repo, run one of these:

```shell
npm link
```

```shell
yarn link
```

- in the repo that uses the package:

```shell
npm link packageName
```

```shell
yarn link packageName
```

- to undo, follow the same steps in reverse, with `unlink` instead of `link`

# Yarn resolutions

- lets you override a dependency of a dependency, for example to ensure a bug fix is applied
- in the example below:
    - `foo` 1.2.0 (or higher minor version) will be used for all dependencies
    - `baz` 1.2.3 will be used for the dependency `bar`

```
"resolutions": {
    "foo": "^1.2.0",
    "bar/baz": "1.2.3",
}
```

# Yarn 3 .gitignore

If using Zero-Installs:

```gitignore
.yarn/*
!.yarn/cache
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

If not:

```gitignore
.pnp.*
.yarn/*
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

# Disable Yarn 3 PnP

- Yarn PnP causes issues with some frameworks like SvelteKit, and can interfere with VSCode TypeScript support

```shell
yarn config set nodeLinker node-modules
```
