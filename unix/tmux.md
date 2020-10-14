#### iTerm 2 and tmux 

https://www.iterm2.com/documentation-tmux-integration.html

- Install iTerm
- Install tmux 
```bash
brew install tmux
```
- Start a new tmux session using the -CC option
```bash
tmux -CC
```

Commands
```bash
cmd + t: open a new tab 
cmd + n: open a new window 
cmd + d: split vertically 
cmd + shift + d: split horizontally
cmd + shift + i: synchronize-panes
```

Attach to existing session
```bash
tmux -CC attach
```

#### iTerm 2 hotkeys:
- all panes - `Command + Shift + I`
- current tab `Command + Option + I`.


#### Tmux shortcuts and cheatsheet

Documentation: http://man.openbsd.org/OpenBSD-current/man1/tmux.1

> Prefix is: `ctrl+b`

#### Command line: `prefix+:`

```bash
# start new
tmux

# start new with session name
tmux new -s xxxxxxxxx

# attach
tmux a  #  (or at, or attach)

# attach to named
tmux a -t xxxxxxxxx

# list sessions
tmux ls

# kill session
tmux kill-session -t xxxxxxxxx
```

#### Sessions

```bash
:new<CR>  `new session
s  `list sessions
$  `name session
```

#### Windows (tabs)

```bash
c  `create window
w  `list windows
n  `next window
p  `previous window
f  `find window
,  `name window
&  `kill window`
```

#### Panes (splits)

```bash
%       `vertical split
"       `horizontal split
arrows  `go to panes

o  `swap panes
q  `show pane numbers
x  `kill pane
+  `break pane into window (e.g. to select text by mouse to copy)
-  `restore pane from window
space `toggle between layouts
q   `show pane numbers, when the numbers show up type the key to goto that pane
{   `move the current pane left
}   `move the current pane right
z   `toggle pane zoom
```

#### Sync panes

```bash
setw synchronize-panes on\off
```

#### Resizing

```bash
resize-pane -D (Resizes the current pane down)
resize-pane -U (Resizes the current pane upward)
resize-pane -L (Resizes the current pane left)
resize-pane -R (Resizes the current pane right)
resize-pane -D 20 (Resizes the current pane down by 20 cells)
```

#### Configurations

```bash
# mouse support
setw -g mouse on/off

# set the default terminal mode to 256color mode
set -g default-terminal "screen-256color"

# enable activity alerts
setw -g monitor-activity on
set -g visual-activity on

# center the window list
set -g status-justify centre
```

#### tmux.conf

You can configure tmux via the `~/.tmux.conf` file. Apply changes after modifications with:
```bash
tmux source ~/.tmux.conf
```

Example:
```yaml
set -g mouse on

# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-copycat'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run -b '~/.tmux/plugins/tpm/tpm'
```

#### Plugins

https://github.com/tmux-plugins/tpm
```bash
# Installs new plugins and refreshes environment
prefix + I

# Updates plugins
prefix + U

# Remove plugins not on the plugin list
prefix + alt + u
```

https://github.com/tmux-plugins/tmux-resurrect
```bash
# Save
prefix + Ctrl-s
  
# Restore
prefix + Ctrl-r
```

https://github.com/tmux-plugins/tmux-copycat
```bash
# Regex search
prefix + /
```
