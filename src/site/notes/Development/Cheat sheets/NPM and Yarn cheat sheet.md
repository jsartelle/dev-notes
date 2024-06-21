---
{"dg-publish":true,"dg-path":"Cheat sheets/NPM and Yarn cheat sheet.md","permalink":"/cheat-sheets/npm-and-yarn-cheat-sheet/","tags":["tech/web"]}
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
    - should only be [[Development/Cheat sheets/NPM and Yarn cheat sheet#package.json version pinning syntax\|pinned]] to major versions, because the package manager will error if the peerDependency can't be resolved correctly

# package.json version pinning syntax

- No pinning (always install the latest version): `*` or `x`
- Only same major version: `^1.0.4` or  `1` or `1.x`
- Only same minor version: `~1.0.4` or `1.0` or `1.0.x`
- Only exact version: `1.0.4`
- `>`, `>=`, `<`, `<=`, etc. ignore the major/minor/patch division

# Commands

## npm

- `npm list`: list installed packages (useful for [[Development/Cheat sheets/Terminal cheat sheet#grep\|grepping]])
    - `-g`: global
    - `-a`: include nested dependencies
    - `-l`: include descriptions
- `npm ls foo`: see which installed packages have a dependency on `foo`

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

# Disable Yarn PnP

- Yarn PnP causes issues with some frameworks like SvelteKit, and can interfere with VSCode TypeScript support

```shell
yarn config set nodeLinker node-modules
```

# Yarn .gitignore

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
