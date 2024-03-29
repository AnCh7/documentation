> References:
> https://yarnpkg.com/getting-started/migration
> https://yarnpkg.com/features/pnp

### Introduction

Yarn is a package manager for your code.

Code is shared through something called a **package**. A package contains all the code being shared as well as a `package.json` file (called a **manifest**) which describes the package.

### Installation

The preferred way to manage Yarn is through [Corepack](https://nodejs.org/dist/latest/docs/api/corepack.html), a new binary shipped with all Node.js releases starting from 16.10. It  acts as an intermediary between you and Yarn, and lets you use different package manager versions across multiple projects without having to  check-in the Yarn binary anymore.

```bash
corepack enable
```

Initializing your project

```bash
yarn init -2
```

### Usage

```
yarn install
yarn add [package]@[version]
yarn up [package]@[version]
yarn remove [package]
```

### Migration

Note that those commands only need to be run once for  the whole project and will automatically take effect for all your  contributors as soon as they pull the migration commit, thanks to the  power of [`yarnPath`](https://yarnpkg.com/configuration/yarnrc#yarnPath):

1. Run `npm install -g yarn` to update the global yarn version to latest v1
2. Go into your project directory
3. Run `yarn set version berry` to enable v2 (cf [Install](https://yarnpkg.com/getting-started/install) for more details)
4. If you used `.npmrc` or `.yarnrc`, you'll need to turn them into the [new format](https://yarnpkg.com/configuration/yarnrc) (see also [1](https://yarnpkg.com/getting-started/migration#update-your-configuration-to-the-new-settings), [2](https://yarnpkg.com/getting-started/migration#dont-use-npmrc-files))
5. Add [`nodeLinker: node-modules`](https://yarnpkg.com/configuration/yarnrc#nodeLinker) in your `.yarnrc.yml` file
6. Commit the changes so far (`yarn-X.Y.Z.js`, `.yarnrc.yml`, ...)
7. Run `yarn install` to migrate the lockfile
8. Take a look at [this article](https://yarnpkg.com/getting-started/qa#which-files-should-be-gitignored) to see what should be gitignored
9. Commit everything remaining

### Questions & Answers

Which files should be gitignored?

If you're using Zero-Installs:
```gitignore
.yarn/*
!.yarn/cache
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

If you're not using Zero-Installs:
```gitignore
.pnp.*
.yarn/*
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

- `.yarn/cache` and `.pnp.*` may be safely ignored, but you'll need to run `yarn install` to regenerate them between each branch switch - which would be optional otherwise, cf [Zero-Installs](https://yarnpkg.com/features/zero-installs).
- `.yarn/install-state.gz` is an optimization file that you shouldn't ever have to commit. It  simply stores the exact state of your project so that the next commands  can boot without having to resolve your workspaces all over again.
- `.yarn/patches` contain the patchfiles you've been generating with the [`yarn patch-commit`](https://yarnpkg.com/cli/patch-commit) command. You always want them in your repository, since they are necessary to install your dependencies.
- `.yarn/plugins` and `.yarn/releases` contain the Yarn releases used in the current repository (as defined by [`yarn set version`](https://yarnpkg.com/cli/set/version)). You will want to keep them versioned (this prevents potential issues  if, say, two engineers use different Yarn versions with different  features).
- `.yarn/sdks` contains the editor SDKs generated by `@yarnpkg/sdks`. Whether to keep it in your repository or not is up to you; if you  don't, you'll need to follow the editor procedure again on new clones.  See [Editor SDKs](https://yarnpkg.com/getting-started/editor-sdks) for more details.
- `.yarn/unplugged` should likely always be ignored since they typically hold machine-specific build artifacts. Ignoring it might however prevent [Zero-Installs](https://yarnpkg.com/features/zero-installs) from working (to prevent this, set [`enableScripts`](https://yarnpkg.com/configuration/yarnrc#enableScripts) to `false`).
- `.yarn/versions` is used by the [version plugin](https://yarnpkg.com/features/release-workflow) to store the package release definitions. You will want to keep it within your repository.
- `yarn.lock` should always be stored within your repository ([even if you develop a library](https://yarnpkg.com/getting-started/qa#should-lockfiles-be-committed-to-the-repository)).
- `.yarnrc.yml` (and its older counterpart, `.yarnrc`) are configuration files. They should always be stored in your project.

### Offline Cache

The offline cache is a feature that allows Yarn to work just fine even should the network go down for any reason. It's also a critical part of [Zero-Installs](https://yarnpkg.com/features/zero-installs) and doesn't store more than a single file for each package.

The way it works is simple: each time a package is downloaded from a  remote location a copy will be stored within the cache.

### Plug'n'Play

Plug'n'Play is an innovative installation strategy for [Node](https://nodejs.org/).

In this install mode (the default starting from Yarn 2.0), Yarn generates a single `.pnp.cjs` file instead of the usual `node_modules` folder containing copies of various packages. The `.pnp.cjs` file contains various maps: one linking package names and versions to  their location on the disk and another one linking package names and  versions to their list of dependencies. With these lookup tables, Yarn  can instantly tell Node where to find any package it needs to access, as long as they are part of the dependency tree, and as long as this file  is loaded within your environment (more on that in the next section).

### Plugins

What can plugins do?
- Plugins can add new resolvers.
- Plugins can add new fetchers.
- Plugins can add new linkers.
- Plugins can add new commands.
- Plugins can register to some events.
- Plugins can be integrated with each other.

### Protocols

| Name                                                | Example                                 | Description                                                  |
| --------------------------------------------------- | --------------------------------------- | ------------------------------------------------------------ |
| Semver                                              | `^1.2.3`                                | Resolves from the default registry                           |
| Tag                                                 | `latest`                                | Resolves from the default registry                           |
| Npm alias                                           | `npm:name@...`                          | Resolves from the npm registry                               |
| Git                                                 | `git@github.com:foo/bar.git`            | Downloads a public package from a Git repository             |
| GitHub                                              | `github:foo/bar`                        | Downloads a **public** package from GitHub                   |
| GitHub                                              | `foo/bar`                               | Alias for the `github:` protocol                             |
| File                                                | `file:./my-package`                     | Copies the target location into the cache                    |
| Link                                                | `link:./my-folder`                      | Creates a link to the `./my-folder` folder (ignore dependencies) |
| Patch                                               | `patch:left-pad@1.0.0#./my-patch.patch` | Creates a patched copy of the original package               |
| Portal                                              | `portal:./my-folder`                    | Creates a link to the `./my-folder` folder (follow dependencies) |
| Workspace                                           | `workspace:*`                           | Creates a link to a package in another workspace             |
| [Exec](https://yarnpkg.com/features/protocols#exec) | `exec:./my-generator-package`           | *Experimental & Plugin* Instructs Yarn to execute the specified Node script and use its output as package content |

### Workspaces

Yarn workspaces aim to make working with [monorepos](https://yarnpkg.com/advanced/lexicon#monorepository) easy, solving one of the main use cases for `yarn link` in a more declarative way. In short, they allow multiple projects to  live together in the same repository AND to cross-reference each other - any modification to one's source code being instantly applied to the  others.

Worktrees are defined through the traditional `package.json` files. What makes them special is that they have the following properties:

- They must declare a `workspaces` field which is expected to be an array of glob patterns that should be  used to locate the workspaces that make up the work tree.
- They must be connected in some way to the project-level `package.json` file.

### Zero-Installs

In order to make a project zero-install:

- check that you are using Yarn 2 or later using `yarn --version`. You can change the version with [`yarn set version `](https://yarnpkg.com/cli/set/version/)
- ensure that your project is using [Plug'n'Play](https://yarnpkg.com/features/pnp) to resolve dependencies via the cache folder and **not** from `node_modules`.
- The cache folder is by default stored within your project folder (in `.yarn/cache`). Just make sure you add it to your repository (see also, [Offline Cache](https://yarnpkg.com/features/offline-cache)).
- When running `yarn install`, Yarn will generate a `.pnp.cjs` file. Add it to your repository as well - it contains the dependency tree that Node will use to load your packages.
- Depending on whether your dependencies have install scripts or not (we advise you to avoid it if you can, and prefer wasm-powered alternatives) you may  also want to add the `.yarn/unplugged` entries.