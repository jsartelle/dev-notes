---
{"dg-publish":true,"dg-path":"Cheat sheets/NPM cheat sheet.md","permalink":"/cheat-sheets/npm-cheat-sheet/","created":"","updated":""}
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
    - should only be [[Development/Cheat sheets/NPM cheat sheet#package.json version pinning syntax\|pinned]] to major versions, because the package manager will error if the peerDependency can't be resolved correctly

# package.json version pinning syntax

- No pinning (always install the latest version): `*` or `x`
- Only same major version: `1` or `1.x` or `^1.0.4`
- Only same minor version: `1.0` or `1.0.x` or `~1.0.4`
- Only exact version: `1.0.4`
