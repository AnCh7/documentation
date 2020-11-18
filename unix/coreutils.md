## macOS GNU tools

Instead of shipping with the GNU flavor of command line tools, macOS ships with the FreeBSD flavor. These utils are quite outdated. You can install latest versions via brew:

```bash
brew install coreutils \
  findutils \
  binutils \
  diffutils \
  gnu-tar \
  gnu-sed \
  gnu-indent \
  gnu-getopt \
  gnu-which \
  gawk \
  gnutls \
  grep \
  autoconf \
  bash \
  ed \
  flex \
  gpatch \
  gzip \
  less \
  m4 \
  make \
  nano \
  screen \
  watch \
  wdiff \
  wget
```

Now these tools are available with "g" prefix. 

If you need to use these commands with their normal names, you can add a "gnubin" directory to your PATH from your bashrc  (or zshrc) like:

```bash
# Should be added to the beginning of the file, before other PATH statements
export PATH="$(brew --prefix)/opt/coreutils/libexec/gnubin:$PATH"

# Reload shell
exec "$SHELL"

# Verify
man mkdir
```
