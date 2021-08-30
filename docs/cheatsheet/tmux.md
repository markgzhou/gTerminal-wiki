![TMUX](../_images/attachment/20210830/tmux_logo.png ) 

# Tmux cheat sheet
tmux is an open-source terminal multiplexer for Unix-like operating systems. It allows multiple terminal sessions to be accessed simultaneously in a single window. It is useful for running more than one command-line program at the same time.

## Installation
### Ubuntu and Debian
```shell
sudo apt install tmux
```
### CentOS and Fedora
```shell
sudo yum install tmux
```
### MacOS
```shell
brew install tmux
```

# Actions
## Shell scripts
```shell
tmux new                          # Create a new session
tmux new -s mySessionName         # Create a new session with name

tmux ls                           # List all active sessions

tmux a                            # Attach/connect to last active session
tmux a -t <mysession>             # Attach to the session "mysession"
```

## Keyboard shortcuts
- `Ctrl+b` + `d` to detach from the session. 
- `Ctrl+b` + `c` create a new window (with shell)
- `Ctrl+b` + `w` choose window from a list
- `Ctrl+b` + `0` switch to window 0 (by number )
- `Ctrl+b` + `,` rename the current window
- `Ctrl+b` + `%` split current pane horizontally into two panes
- `Ctrl+b` + `"` split current pane vertically into two panes
- `Ctrl+b` + `o` go to the next pane
- `Ctrl+b` + `;` toggle between the current and previous pane
- `Ctrl+b` + `x` close the current pane


## Customizing

```shell
# Save this file at ~/.tmux.conf
# Improve colors
set -g default-terminal 'screen-256color'

# Set scrollback buffer to 10000
set -g history-limit 10000

# Customize the status line
set -g status-fg  green
set -g status-bg  black
```