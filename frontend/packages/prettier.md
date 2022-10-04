### What is Prettier?

> References:
> https://prettier.io/docs/en/index.html
> https://www.jetbrains.com/help/webstorm/prettier.html

Prettier is an opinionated code formatter with support for: 

- JavaScript (including experimental features) 
- [JSX](https://facebook.github.io/jsx/) 
- [Angular](https://angular.io/) 
- [Vue](https://vuejs.org/) 
- [Flow](https://flow.org/) 
- [TypeScript](https://www.typescriptlang.org/) 
- CSS, [Less](http://lesscss.org/) and [SCSS](https://sass-lang.com)
- [HTML](https://en.wikipedia.org/wiki/HTML)
- [Ember/Handlebars](https://handlebarsjs.com/)
- [JSON](https://json.org/)
- [GraphQL](https://graphql.org/)
- [Markdown](https://commonmark.org/), including [GFM](https://github.github.com/gfm/) and [MDX](https://mdxjs.com/) [YAML](https://yaml.org/)

It removes all original styling and ensures that all outputted code conforms to a consistent style. Prettier takes your code and reprints it from scratch by taking the line length into account.

##### How does it compare to ESLint/TSLint/stylelint, etc.?

Linters have two categories of rules: 

- **Formatting rules**: eg: [max-len](https://eslint.org/docs/rules/max-len), [no-mixed-spaces-and-tabs](https://eslint.org/docs/rules/no-mixed-spaces-and-tabs), [keyword-spacing](https://eslint.org/docs/rules/keyword-spacing), [comma-style](https://eslint.org/docs/rules/comma-style)… Prettier alleviates the need for this whole category of rules!  Prettier is going to reprint the entire program from scratch in a  consistent way, so it’s not possible for the programmer to make a  mistake there anymore :) 
- **Code-quality rules**: eg [no-unused-vars](https://eslint.org/docs/rules/no-unused-vars), [no-extra-bind](https://eslint.org/docs/rules/no-extra-bind), [no-implicit-globals](https://eslint.org/docs/rules/no-implicit-globals), [prefer-promise-reject-errors](https://eslint.org/docs/rules/prefer-promise-reject-errors)… Prettier does nothing to help with those kind of rules. They are also the most important ones provided by linters as they are likely to catch real bugs with your code! 

In other words, use **Prettier for formatting** and **linters for catching bugs!**

##### Integrating with Linters

First, we have plugins that let you run Prettier as if it was a linter rule:

* [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)

These plugins were especially useful when Prettier was new. By running Prettier inside your linters, you didn’t have to set up any new  infrastructure and you could re-use your editor integrations for the  linters. But these days you can run `prettier --check .` and most editors have Prettier support.

The downsides of those plugins are: 

- You end up with a lot of red squiggly lines in your editor, which  gets annoying. Prettier is supposed to make you forget about formatting – and not be in your face about it! 
- They are slower than running Prettier directly. 
- They are yet one layer of indirection where things may break.

Finally, we have tools that run `prettier` and then immediately for example `eslint --fix` on files:

* [prettier-eslint](https://github.com/prettier/prettier-eslint)

Those are useful if some aspect of Prettier’s output makes Prettier completely unusable to you. Then you can have for example `eslint --fix` fix that up for you. The downside is that these tools are much slower than just running Prettier.

---

From stackoverflow

**tl;dr: Use `eslint-config-prettier`, you can ignore the rest.**

ESLint contains many rules and those that are formatting-related might conflict with Prettier, such as `arrow-parens`, `space-before-function-paren`, etc. Hence using them together will cause some issues. The following  tools have been created to use ESLint and Prettier together.

|                                                   | [`prettier-eslint`](https://github.com/prettier/prettier-eslint) | [`eslint-plugin-prettier`](https://github.com/prettier/eslint-plugin-prettier) | [`eslint-config-prettier`](https://github.com/prettier/eslint-config-prettier) |
| ------------------------------------------------- | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| What it is                                        |       A JavaScript module exporting a single function.       |                      An ESLint plugin.                       |                   An ESLint configuration.                   |
| What it does                                      | Runs the code (string) through `prettier` then `eslint --fix`. The output is also a string. | Plugins usually contain implementations  for additional rules that ESLint will check for. This plugin uses  Prettier under the hood and will raise ESLint errors when your code  differs from Prettier's expected output. | This config turns off formatting-related rules that might conflict with Prettier, allowing you to use Prettier  with other ESLint configs like [`eslint-config-airbnb`](https://www.npmjs.com/package/eslint-config-airbnb). |
| How to use it                                     | Either calling the function in your code or via [`prettier-eslint-cli`](https://github.com/prettier/prettier-eslint-cli) if you prefer the command line. |                 Add it to your `.eslintrc`.                  |                 Add it to your `.eslintrc`.                  |
| Is the final output Prettier compliant?           |                Depends on your ESLint config                 |                             Yes                              |                             Yes                              |
| Do you need to run `prettier` command separately? |                              No                              |                              No                              |                             Yes                              |
| Do you need to use anything else?                 |                              No                              | You may want to turn off conflicting rules using `eslint-config-prettier`. |                              No                              |

For more information, refer to the [official Prettier docs](https://prettier.io/docs/en/integrating-with-linters.html).

It's 2019, and this is still the best explanation I find, much better than the official one. You might add  that prettier-eslint is not recommended any more. And the latter 2 can  work together now.



