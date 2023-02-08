### Commit dependency lock files

Dependency lock files, which are supported by many package management systems should be committed to the codebase in end-of-chain projects - so that  each individual trying to run that project is doing so with *exactly* the tested set of dependencies.

Always commit at least one of `yarn.lock` or `package-lock.json` depending on which package manager you're using.

If you commit `package-lock.json` then you're building in support for people installing your dependencies with NPM 5. If you commit `yarn.lock`, you're building in support for people installing dependencies with Yarn.

---

Note:  `package-lock.json` should only be committed to the source code version control when the project cannot be a dependency of other projects, i.e. `package-lock.json` should only by committed to source code version control for top-level  projects (programs consumed by the end user, not other programs).

---

#### remix-run/history

> References: https://github.com/remix-run/history/blob/dev/docs/getting-started.md

The history library provides history tracking and navigation primitives  for JavaScript applications that run in browsers and other stateful  environments.

We provide 3 different methods for working with history, depending on your environment:

- A "browser history" is for use in modern web browsers that support the [HTML5 history API](http://diveintohtml5.info/history.html)
- A "hash history" is for use in web browsers where you want to store the location in the [hash](https://developer.mozilla.org/en-US/docs/Web/API/HTMLHyperlinkElementUtils/hash) portion of the current URL to avoid sending it to the server when the page reloads
- A "memory history" is used as a reference implementation that may be used in non-browser environments, like [React Native](https://facebook.github.io/react-native/)