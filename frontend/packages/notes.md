### Commit dependency lock files

Dependency lock files, which are supported by many package management systems should be committed to the codebase in end-of-chain projects - so that  each individual trying to run that project is doing so with *exactly* the tested set of dependencies.

Always commit at least one of `yarn.lock` or `package-lock.json` depending on which package manager you're using.

If you commit `package-lock.json` then you're building in support for people installing your dependencies with NPM 5. If you commit `yarn.lock`, you're building in support for people installing dependencies with Yarn.

---

Note:  `package-lock.json` should only be committed to the source code version control when the project cannot be a dependency of other projects, i.e. `package-lock.json` should only by committed to source code version control for top-level  projects (programs consumed by the end user, not other programs).