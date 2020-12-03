## Useful URLs

https://github.com/purcell/emacs.d
https://github.com/hlissner/doom-emacs
https://github.com/d12frosted/homebrew-emacs-plus

## macOS installation

1. Install emacs: https://emacsformacosx.com/builds
2. Configure PATH:
```bash
sudo ln -s /Applications/Emacs.app/Contents/MacOS/Emacs /usr/local/bin/emacs

vi ~/.zshrc
export PATH="$PATH:/usr/local/bin/emacs"
```
3. Install doom:
```bash
git clone --depth 1 https://github.com/hlissner/doom-emacs ~/.emacs.d
~/.emacs.d/bin/doom install
```

