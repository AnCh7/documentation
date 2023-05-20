### Notes

> https://thakkarparth007.github.io/copilot-explorer/posts/copilot-internals

- Prompt length=2048

- Prompt includes:

  - Language ID
  - Up to 20 files of same language. Sorted by access time
  - Relative paths of files included
  - Github repo name

- **Client**: The VSCode extension collects whatever you type (called `prompt`), and sends it to a [Codex](https://beta.openai.com/docs/guides/completion/introduction)-like model. Whatever the model returns, it then displays in your editor.

- **Model**: The Codex-like model takes the prompt and returns suggestions that complete the prompt.

- The most complete part of prompt generation, to me, appears to be snippet extraction from other files. 

- By [default](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m3055312&pos=148:1), the fixed window Jaccard Matcher is used.

- extension asks for very few suggestions (1-3) from the model in order to be fast

- it takes care of adapting the suggestions if the user continues typing

- It also takes care of debouncing the model requests if the user is  typing fast

- after generating the prompt, this module checks if the prompt is [“good enough”](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m9334&pos=733:1) to bother with invoking the model.

- score seems to be based on a simple logistic regression model over 11  features such as the language, whether previous suggestion was  accepted/rejected, duration between previous accept/reject, length of  last line in the prompt, last character before cursor, etc. The model  weights are [included in the extension code itself](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m7744&pos=1:1).

- If the score is below a threshold (default [15%](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m7744&pos=10:1)), then the request isn’t made.

- some languages had higher weight than others (e.g. `php > js > python > rust > dart`…`php`, really?)

```
  exports.contextualFilterLanguageMap = {
    javascript: 1,
    typescript: 2,
    typescriptreact: 3,
    python: 4,
    vue: 5,
    php: 6,
    dart: 7,
    javascriptreact: 8,
    go: 9,
    css: 10,
    cpp: 11,
    html: 12,
    scss: 13,
    markdown: 14,
    csharp: 15,
    java: 16,
    json: 17,
    rust: 18,
    ruby: 19,
    c: 20,
  };
```

- if the prompt ended with `)` or `]`, then the score was lower (`-0.999`, `-0.970`) than if it ended with `(` or `[` (`0.932`, `0.049`). That makes sense, because the former is more likely to be already “completed”
- Depending upon the mode in which this is invoked (`OPEN_COPILOT`/`TODO_QUICK_FIX`/`UNKNOWN_FUNCTION_QUICK_FIX`), it [modifies the prompt slightly](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m2388&pos=113:1).
- If the output is [repeatitive](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m9657&pos=52:9) which is a common failure mode of language models, then the suggestion is [discarded](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m1124&pos=12:5).
- If the user has [already typed](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m1124&pos=37:5) the suggestion, then it is discarded.
- after [15s, 30s, 2min, 5min and 10min timeouts](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m7017&pos=21:3), the extension measures if the accepted suggestion is “still in code”.
- [After 30s](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m7017&pos=29:7) of either acceptance/rejection of a suggestion, copilot [“captures” a snapshot](https://thakkarparth007.github.io/copilot-explorer/codeviz/templates/code-viz.html#m7017&pos=49:3) around the insertion point.
- model is called “**cushman-ml**”, which strongly suggests that Copilot is using a **12B parameter model instead of a 175B parameter model**.
- opensource models:
  - https://github.com/salesforce/CodeGen