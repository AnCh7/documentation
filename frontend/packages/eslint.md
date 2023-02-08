### ESLint

> References:
> https://eslint.org/docs/latest/user-guide/getting-started

ESLint is a tool for identifying and reporting on patterns found in  ECMAScript/JavaScript code, with the goal of making code more consistent and avoiding bugs.

### Configuration Files

Use configuration files is via `.eslintrc.*` and `package.json` files. ESLint will automatically look for them in the directory of the  file to be linted, and in successive parent directories all the way up  to the root directory of the filesystem (`/`), the home directory of the current user (`~/`), or when `root: true` is specified.

### Plugins

To indicate the npm module to use as your parser, specify it using the `parser` option in your `.eslintrc` file.

Plugins may provide processors. Processors can extract JavaScript code  from other kinds of files, then let ESLint lint the JavaScript code or  processors can convert JavaScript code in preprocessing for some  purpose.

ESLint supports the use of third-party plugins. Before using the plugin, you have to install it using npm.

