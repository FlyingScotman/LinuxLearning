# Tmux Reference Manual
> Terminal multiplexing that survives disconnects — from first session to scripted workflows.

---

## Alphabetic Index

| Command / Keybinding | Section |
|----------------------|---------|
| [`prefix !`](#prefix--break-pane-to-window) | [Panes](#5-panes) |
| [`prefix "`](#prefix--horizontal-split) | [Panes](#5-panes) |
| [`prefix $`](#prefix--rename-session) | [Sessions](#3-sessions) |
| [`prefix %`](#prefix--vertical-split) | [Panes](#5-panes) |
| [`prefix &`](#prefix--close-window) | [Windows](#4-windows) |
| [`prefix (`](#prefix--switch-to-previousnext-session) | [Sessions](#3-sessions) |
| [`prefix )`](#prefix--switch-to-previousnext-session) | [Sessions](#3-sessions) |
| [`prefix ,`](#prefix--rename-window) | [Windows](#4-windows) |
| [`prefix .`](#prefix--move-window) | [Command Mode & Scripting](#8-command-mode--scripting) |
| [`prefix 0-9`](#prefix-0-9-jump-to-window-by-number) | [Windows](#4-windows) |
| [`prefix :`](#prefix--command-prompt) | [Command Mode & Scripting](#8-command-mode--scripting) |
| [`prefix ;`](#prefix--last-pane) | [Panes](#5-panes) |
| [`prefix =`](#prefix--choose-paste-buffer) | [Copy Mode](#6-copy-mode) |
| [`prefix ?`](#prefix--list-keybindings) | [Getting Started](#2-getting-started) |
| [`prefix [`](#prefix--enter-copy-mode) | [Copy Mode](#6-copy-mode) |
| [`prefix ]`](#prefix--paste) | [Copy Mode](#6-copy-mode) |
| [`prefix c`](#prefix-c-create-window) | [Windows](#4-windows) |
| [`prefix d`](#prefix-d-detach) | [Sessions](#3-sessions) |
| [`prefix l`](#prefix-l-last-window) | [Windows](#4-windows) |
| [`prefix n`](#prefix-np-nextprevious-window) | [Windows](#4-windows) |
| [`prefix p`](#prefix-np-nextprevious-window) | [Windows](#4-windows) |
| [`prefix q`](#prefix-q-show-pane-numbers) | [Panes](#5-panes) |
| [`prefix r` (custom)](#recommended-tmuxconf-for-python-development) | [Recommended Config](#11-recommended-tmuxconf-for-python-development) |
| [`prefix s`](#prefix-s-session-picker) | [Sessions](#3-sessions) |
| [`prefix space`](#prefix-space-cycle-layouts) | [Resizing & Layouts](#7-resizing--layouts) |
| [`prefix w`](#prefix-w-window-picker) | [Windows](#4-windows) |
| [`prefix x`](#prefix-x-close-pane) | [Panes](#5-panes) |
| [`prefix z`](#prefix-z-zoomunzoom-pane) | [Panes](#5-panes) |
| [`prefix arrow`](#prefix-arrow-navigate-panes) | [Panes](#5-panes) |
| [`prefix Ctrl-arrow`](#prefix-ctrl-arrow-resize-pane) | [Resizing & Layouts](#7-resizing--layouts) |
| [`prefix Alt-1..5`](#prefix-alt-15-preset-layouts) | [Resizing & Layouts](#7-resizing--layouts) |
| [`prefix {/}`](#prefix--swap-panes) | [Panes](#5-panes) |
| [`prefix + I`](#prefix--i-install-plugins) | [Plugin Manager (TPM)](#10-plugin-manager-tpm) |
| [`prefix + U`](#prefix--u-update-plugins) | [Plugin Manager (TPM)](#10-plugin-manager-tpm) |
| [`swap-pane`](#swap-pane) | [Resizing & Layouts](#7-resizing--layouts) |
| [`tmux attach`](#tmux-attach--t-name) | [Sessions](#3-sessions) |
| [`tmux capture-pane`](#tmux-capture-pane) | [Advanced Features](#9-advanced-features) |
| [`tmux kill-session`](#tmux-kill-session--t-name) | [Sessions](#3-sessions) |
| [`tmux kill-server`](#tmux-kill-server) | [Sessions](#3-sessions) |
| [`tmux ls`](#tmux-ls) | [Sessions](#3-sessions) |
| [`tmux new`](#tmux-new--s-name) | [Sessions](#3-sessions) |
| [`tmux pipe-pane`](#tmux-pipe-pane) | [Advanced Features](#9-advanced-features) |
| [`tmux rename-session`](#tmux-rename-session) | [Sessions](#3-sessions) |
| [`tmux send-keys`](#tmux-send-keys) | [Command Mode & Scripting](#8-command-mode--scripting) |

---

## How to Read This Manual

Each entry follows this structure:
```
COMMAND / KEYBINDING — what it does in one line

Syntax:   tmux command [flags] [arguments]
          or prefix + key
Flags:    -x    what this flag does
Examples: concrete usage
Notes:    traps, warnings, tips
```

**Tier system:**
- 🔴 **Tier 1** — Use daily. Learn these first.
- 🟡 **Tier 2** — Use regularly. Learn after Tier 1.
- 🔵 **Tier 3** — Use occasionally. Reference when needed.

---

---

# Tmux Concepts

*Before using tmux, understand the architecture. Once the hierarchy and detach/attach model click, everything else is just keybindings.*

### The Hierarchy

```
tmux server (runs in background, started automatically)
 │
 ├── Session: "webdev"
 │    ├── Window 0: "editor"
 │    │    ├── Pane 0: vim (top)
 │    │    └── Pane 1: shell (bottom)
 │    ├── Window 1: "server"
 │    │    └── Pane 0: npm run dev
 │    └── Window 2: "git"
 │         └── Pane 0: shell
 │
 └── Session: "deploy"
      ├── Window 0: "prod"
      │    └── Pane 0: ssh prod-server
      └── Window 1: "logs"
           ├── Pane 0: tail -f app.log
           └── Pane 1: tail -f error.log
```

- **Server** — One tmux server runs in the background per user. It holds all sessions. You rarely interact with it directly. It starts automatically when you run `tmux` and stops when the last session is killed.
- **Session** — A named collection of windows. Think of it as a "project workspace." You can have a session for each project you're working on. Sessions survive terminal disconnects.
- **Window** — A single full-screen view within a session. Like tabs in a browser. Each window has a name and a number. The status bar shows all windows.
- **Pane** — A subdivision of a window. Splitting a window creates panes. Each pane runs its own shell (or any program). Panes share the window's screen real estate.

### The Prefix Key Model

Tmux uses a **prefix key** to avoid collisions with programs running inside it. By default, the prefix is `Ctrl-b`.

```
Step 1: Press Ctrl-b (prefix) — nothing visible happens, tmux is now listening
Step 2: Press the command key — tmux executes the command
```

So "`prefix c`" means: press `Ctrl-b`, release, then press `c`. This creates a new window.

You are NOT holding Ctrl-b and c at the same time. You press Ctrl-b, let go, then press c.

> **Tip:** Many users rebind the prefix to `Ctrl-a` because it's easier to reach. See the [Recommended Config](#11-recommended-tmuxconf-for-python-development) section.

### Detach / Attach Model

This is **the killer feature** of tmux. When you detach from a session, it keeps running in the background. You can:

```
1. SSH into a server
2. Start a tmux session
3. Run a long process (training an ML model, running a migration, compiling)
4. Detach from tmux (or your SSH connection drops / laptop closes)
5. SSH back in later
6. Reattach to the same tmux session
7. Everything is exactly where you left it — process still running
```

Without tmux, closing your SSH connection kills all processes started in that shell. With tmux, processes survive because they run under the tmux server, not your SSH connection.

### Terminal Multiplexer vs Multiple SSH Sessions

| | Multiple SSH sessions | tmux |
|---|---|---|
| **Survives disconnect** | No — each connection is independent | Yes — everything persists |
| **Resource usage** | Each SSH = separate connection + shell | One SSH + one tmux server |
| **State persistence** | Lost on disconnect | Preserved until you kill it |
| **Shared view** | Cannot share screens | Multiple clients can attach to same session |
| **Split screens** | Need separate terminal windows | Built-in pane splitting |
| **Scripting** | Manual setup each time | Scriptable layouts |

---

---

# 2. Getting Started

*Install tmux, launch it, understand the prefix, and know how to get out.*

---

### Installing tmux
🔴 **Tier 1** — Install the tmux package.

```
Syntax:   sudo apt install tmux          # Debian/Ubuntu
          sudo yum install tmux          # CentOS/RHEL
          sudo dnf install tmux          # Fedora
          brew install tmux              # macOS
          sudo pacman -S tmux            # Arch
```

**Examples:**
```bash
# Install on Ubuntu/Debian
sudo apt update && sudo apt install tmux

# Verify installation
tmux -V
# Output: tmux 3.3a
```

**Notes:**
- Most Linux distributions include tmux in their default repositories.
- On Ubuntu 22.04+ you get tmux 3.2+. Older distros may ship tmux 2.x which lacks some features (like popup menus).
- No root access? You can compile tmux from source into `~/local/bin`.

---

### Launching tmux
🔴 **Tier 1** — Start a new tmux session.

```
Syntax:   tmux                           # unnamed session (auto-numbered)
          tmux new -s <name>             # named session (preferred)
```

**Examples:**
```bash
# Start an unnamed session (gets auto-numbered: 0, 1, 2, ...)
tmux

# Start a named session (always do this — makes reattaching easy)
tmux new -s webdev

# Start a named session with a specific first window name
tmux new -s webdev -n editor

# Start a detached session (runs in background immediately)
tmux new -s background-job -d
```

**Notes:**
- Always name your sessions. `tmux attach -t webdev` is much easier than `tmux attach -t 3`.
- If you're already inside tmux, running `tmux` will give an error: "sessions should be nested with care, unset $TMUX to force." Don't nest sessions.

---

### The Prefix Key
🔴 **Tier 1** — How to issue commands to tmux.

The default prefix is `Ctrl-b`. All tmux keybindings start with the prefix.

```
Step 1:  Press Ctrl and b together → release both
Step 2:  Press the command key (e.g., c for new window)
```

**Examples:**
```bash
# Create a new window:
#   1. Press Ctrl-b (release)
#   2. Press c

# Split pane vertically:
#   1. Press Ctrl-b (release)
#   2. Press %

# Detach from session:
#   1. Press Ctrl-b (release)
#   2. Press d
```

**Notes:**
- If nothing happens when you press `Ctrl-b`, you may not be inside a tmux session. Check: is there a green status bar at the bottom? If not, you're not in tmux.
- `Ctrl-b` is awkward for heavy use. Rebinding to `Ctrl-a` is the most popular customization. See [Recommended Config](#11-recommended-tmuxconf-for-python-development).
- If you press `Ctrl-b` by accident, just press `Escape` or wait — tmux will time out and ignore it.

---

### `prefix ?` List Keybindings
🔴 **Tier 1** — Show all current keybindings. Your built-in cheat sheet.

```
Syntax:   prefix ?
```

**Examples:**
```bash
# Press Ctrl-b, then ?
# A scrollable list of all keybindings appears
# Navigate with arrow keys or Page Up/Down
# Press q to exit
```

**Notes:**
- This shows keybindings for YOUR configuration, including any custom bindings from `.tmux.conf`. It's always accurate.
- Use `/` to search within the list (vi-style if vi mode is on).

---

### Exiting and Killing
🔴 **Tier 1** — Close panes, windows, sessions, and the server.

```
Syntax:   exit                           # close current pane/window (shell builtin)
          Ctrl-d                         # same as exit (sends EOF)
          prefix x                       # close current pane (asks confirmation)
          prefix &                       # close current window (asks confirmation)
          tmux kill-session -t <name>    # kill entire session from outside
          tmux kill-server               # nuclear option — kills everything
```

**Examples:**
```bash
# Close the current pane (just exit the shell)
exit

# Or press Ctrl-d (sends EOF to shell, shell exits, pane closes)

# If a pane is running something that won't quit, force-close it:
# Press prefix x, then confirm with y

# Kill a specific session from another terminal
tmux kill-session -t old-project

# Kill ALL tmux sessions and the server
tmux kill-server
```

**Notes:**
- When you close the last pane in a window, the window closes. When you close the last window in a session, the session ends.
- `exit` / `Ctrl-d` is the cleanest way to close a pane — it lets the shell clean up.
- `prefix x` is useful when a process is stuck and won't respond to `Ctrl-c`.
- `tmux kill-server` destroys ALL sessions for your user. Use with caution.

---

---

# 3. Sessions

*Sessions are the top-level organizational unit. One session per project is a good mental model.*

---

### `tmux new -s name`
🔴 **Tier 1** — Create a new named session.

```
Syntax:   tmux new-session -s <name> [-n <window-name>] [-d]
          tmux new -s <name>              # shorthand
```

| Flag | Meaning |
|------|---------|
| `-s <name>` | Name the session |
| `-n <name>` | Name the first window |
| `-d` | Create detached (don't attach immediately) |
| `-x <width> -y <height>` | Set initial size (useful for scripting) |

**Examples:**
```bash
# Create a session named "webdev"
tmux new -s webdev

# Create with a named first window
tmux new -s webdev -n editor

# Create a detached session (useful in scripts)
tmux new -s background -d

# Create and immediately run a command in it
tmux new -s logs -d 'tail -f /var/log/syslog'
```

**Notes:**
- Session names must be unique. Trying to create a duplicate gives: "duplicate session: webdev".
- Session names cannot contain periods or colons — tmux uses these as delimiters.
- If you omit `-s`, the session gets a number (0, 1, 2, ...). Always name your sessions.

---

### `tmux ls`
🔴 **Tier 1** — List all existing sessions.

```
Syntax:   tmux list-sessions
          tmux ls                         # shorthand
```

**Examples:**
```bash
tmux ls
# Output:
# webdev: 3 windows (created Sun Mar  8 10:15:00 2026) (attached)
# deploy: 1 windows (created Sun Mar  8 09:30:00 2026)

# The "(attached)" means someone is currently viewing that session
```

**Notes:**
- If no tmux server is running, you get: "no server running on /tmp/tmux-1000/default". This just means there are no sessions.
- Useful to run before `tmux attach` to see what's available.
- Inside tmux, `prefix s` gives you an interactive session picker instead.

---

### `tmux attach -t name`
🔴 **Tier 1** — Reattach to an existing session.

```
Syntax:   tmux attach-session -t <name>
          tmux attach -t <name>           # shorthand
          tmux a -t <name>                # even shorter
          tmux a                          # attach to most recent session
```

| Flag | Meaning |
|------|---------|
| `-t <name>` | Target session to attach to |
| `-d` | Detach other clients first (exclusive attach) |

**Examples:**
```bash
# Attach to the "webdev" session
tmux attach -t webdev

# Shorthand
tmux a -t webdev

# Attach to most recent session (when you only have one, or don't care which)
tmux a

# Attach exclusively (kick other clients off this session)
tmux a -t webdev -d
```

**Notes:**
- If you're already inside tmux, you can't attach to another session this way. Use `prefix s` (session picker) or `tmux switch-client -t <name>` instead.
- If multiple people are attached to the same session, they see the exact same thing. The smallest terminal size wins (the session shrinks to fit the smallest client). Use `-d` to detach others.
- Tab completion works for session names in most shells.

---

### `prefix d` Detach
🔴 **Tier 1** — Detach from the current session (session keeps running in background).

```
Syntax:   prefix d
```

**Examples:**
```bash
# You're inside tmux, working on something
# Press Ctrl-b, then d
# You're back at your regular terminal
# The tmux session is still running in the background

# Verify it's still there:
tmux ls
# Output: webdev: 3 windows (created Sun Mar  8 10:15:00 2026)
```

**Notes:**
- Detaching does NOT stop anything. All processes keep running. This is the whole point of tmux.
- Your SSH connection can drop, your laptop can close — the tmux session survives. Just SSH back and `tmux a`.
- You can also detach by closing your terminal window. Same effect, but `prefix d` is cleaner.

---

### `tmux rename-session`
🟡 **Tier 2** — Rename an existing session.

```
Syntax:   tmux rename-session -t <old-name> <new-name>
          prefix $                        # rename current session interactively
```

**Examples:**
```bash
# Rename from outside tmux
tmux rename-session -t 0 webdev

# Rename current session from inside tmux
# Press prefix $, type the new name, press Enter
```

**Notes:**
- Useful when you started with `tmux` (unnamed, got session "0") and want to give it a proper name.
- `prefix $` is the interactive version — it opens a prompt at the bottom of the screen.

---

### `tmux kill-session -t name`
🟡 **Tier 2** — Destroy a session and all its windows/panes.

```
Syntax:   tmux kill-session -t <name>
```

| Flag | Meaning |
|------|---------|
| `-t <name>` | Target session to kill |
| `-a` | Kill all sessions except the one specified by `-t` |

**Examples:**
```bash
# Kill a specific session
tmux kill-session -t old-project

# Kill all sessions EXCEPT "webdev"
tmux kill-session -a -t webdev

# Kill all sessions (nuclear option, same as kill-server)
tmux kill-server
```

**Notes:**
- All processes in the session are terminated. Make sure nothing important is running.
- You can kill a session you're attached to — you'll just be dropped back to the terminal (or into another session if one exists).

---

### `tmux kill-server`
🟡 **Tier 2** — Kill the tmux server and all sessions.

```
Syntax:   tmux kill-server
```

**Examples:**
```bash
# Destroy everything
tmux kill-server
```

**Notes:**
- This kills ALL sessions, ALL windows, ALL panes. Every process running under tmux dies.
- Use only when you want a completely clean slate.
- The server restarts automatically next time you run `tmux`.

---

### `prefix s` Session Picker
🟡 **Tier 2** — Interactive session picker (used from inside tmux).

```
Syntax:   prefix s
```

**Examples:**
```bash
# Press Ctrl-b, then s
# A tree view appears showing all sessions and their windows:
#   (0) + webdev: 3 windows (attached)
#   (1) + deploy: 1 windows
# Use arrow keys to navigate, Enter to switch, x to kill
# Press q to cancel
```

**Notes:**
- This is the best way to switch between sessions from inside tmux.
- You can expand sessions with the right arrow to see individual windows, and even preview pane contents.
- Press `x` on a session to kill it (with confirmation).

---

### `prefix $` Rename Session
🟡 **Tier 2** — Rename the current session interactively.

```
Syntax:   prefix $
```

**Examples:**
```bash
# Press Ctrl-b, then $
# The status bar shows: "(rename-session) current-name"
# Type the new name and press Enter
```

**Notes:**
- Same as `tmux rename-session` but interactive.
- You can clear the current name with `Ctrl-u` before typing the new one.

---

### `prefix (/)` Switch to Previous/Next Session
🟡 **Tier 2** — Cycle through sessions without the picker.

```
Syntax:   prefix (           # previous session
          prefix )           # next session
```

**Examples:**
```bash
# Switch to the next session
# Press Ctrl-b, then )

# Switch to the previous session
# Press Ctrl-b, then (
```

**Notes:**
- Quick way to toggle between two sessions. For more than two, use `prefix s` (session picker).

---

---

# 4. Windows

*Windows are like tabs within a session. Each window fills the full terminal. The status bar shows all windows in the current session.*

---

### `prefix c` Create Window
🔴 **Tier 1** — Create a new window in the current session.

```
Syntax:   prefix c
```

**Examples:**
```bash
# Press Ctrl-b, then c
# A new window appears with a fresh shell
# The status bar updates: 0:editor  1:bash*  (* = active window)
```

**Notes:**
- New windows are appended to the end of the window list.
- The new window gets the next available number.
- The window name defaults to the currently running program (usually "bash"). Rename it with `prefix ,`.

---

### `prefix n/p` Next/Previous Window
🔴 **Tier 1** — Switch to the next or previous window.

```
Syntax:   prefix n           # next window
          prefix p           # previous window
```

**Examples:**
```bash
# Go to next window
# Press Ctrl-b, then n

# Go to previous window
# Press Ctrl-b, then p

# Cycle through: 0 → 1 → 2 → 0 → 1 → ...
```

**Notes:**
- Wraps around: going "next" from the last window takes you to the first.

---

### `prefix 0-9` Jump to Window by Number
🔴 **Tier 1** — Jump directly to a specific window by its number.

```
Syntax:   prefix <number>    # 0 through 9
```

**Examples:**
```bash
# Jump to window 0
# Press Ctrl-b, then 0

# Jump to window 3
# Press Ctrl-b, then 3
```

**Notes:**
- Only works for windows 0–9. For higher-numbered windows, use `prefix '` then type the number, or use the window picker (`prefix w`).
- Many users set `set -g base-index 1` so windows start at 1 instead of 0 (easier to reach on keyboard). See [Recommended Config](#11-recommended-tmuxconf-for-python-development).

---

### `prefix ,` Rename Window
🔴 **Tier 1** — Rename the current window.

```
Syntax:   prefix ,
```

**Examples:**
```bash
# Press Ctrl-b, then ,
# The status bar shows: "(rename-window) bash"
# Clear with Ctrl-u, type new name, press Enter

# Naming convention suggestions:
#   "editor"   — where you run vim/nvim
#   "server"   — where you run dev server
#   "git"      — where you do git operations
#   "logs"     — where you tail log files
#   "ssh-prod" — SSH connection to production
```

**Notes:**
- By default, tmux auto-renames windows to the running program. After renaming, you may want to disable auto-rename: `set -g allow-rename off` in your config.
- Clear the current name with `Ctrl-u` before typing, or use `Backspace`.

---

### `prefix &` Close Window
🟡 **Tier 2** — Close the current window (and all its panes). Asks for confirmation.

```
Syntax:   prefix &
```

**Examples:**
```bash
# Press Ctrl-b, then &
# Status bar shows: "kill-window window-name? (y/n)"
# Press y to confirm, n to cancel
```

**Notes:**
- All panes in the window are killed. All processes in those panes are terminated.
- If this is the last window in the session, the session ends and you're detached.
- For closing individual panes, use `prefix x` or just `exit` / `Ctrl-d`.

---

### `prefix w` Window Picker
🟡 **Tier 2** — Interactive window picker with preview.

```
Syntax:   prefix w
```

**Examples:**
```bash
# Press Ctrl-b, then w
# A tree view appears showing all sessions and windows:
#   (0) + webdev: 3 windows (attached)
#     (0)   0: editor (1 panes) "vim app.py"
#     (1)   1: server (1 panes) "npm run dev"
#     (2)   2: git (1 panes) "bash"
#   (3) + deploy: 1 windows
# Navigate with arrows, Enter to select
# Press q to cancel
```

**Notes:**
- This is like `prefix s` (session picker) but expanded to show windows too.
- Shows a preview of each window's content on the right side.
- You can switch to windows in OTHER sessions from here. Very powerful.

---

### `prefix l` Last Window
🟡 **Tier 2** — Toggle back to the previously active window.

```
Syntax:   prefix l           # lowercase L
```

**Examples:**
```bash
# You're in window 0 (editor), you switch to window 2 (git)
# Press Ctrl-b, then l → you're back in window 0
# Press Ctrl-b, then l again → you're back in window 2
# Quick toggle between two windows
```

**Notes:**
- Extremely useful when you're constantly switching between two windows (e.g., editor and terminal).
- Don't confuse with `prefix L` (switch to last session — capital L).

---

---

# 5. Panes

*Panes split a window into multiple views. Each pane runs its own process. This is where tmux shines for development — editor on top, terminal on bottom.*

---

### `prefix %` Vertical Split
🔴 **Tier 1** — Split the current pane vertically (left | right).

```
Syntax:   prefix %
```

```
Before:                    After:
┌──────────────────┐      ┌────────┬─────────┐
│                  │      │        │         │
│     pane 0       │  →   │ pane 0 │ pane 1  │
│                  │      │        │         │
└──────────────────┘      └────────┴─────────┘
```

**Examples:**
```bash
# Press Ctrl-b, then %
# The current pane splits into two side-by-side panes
# Cursor moves to the new (right) pane
```

**Notes:**
- "Vertical split" means the dividing line is vertical (the split goes left|right). This confuses people — think about the orientation of the dividing line, not the direction of the split.
- Many users rebind this to `prefix |` because it's more intuitive. See [Recommended Config](#11-recommended-tmuxconf-for-python-development).
- The new pane starts in the same directory as the original pane (tmux 1.9+).

---

### `prefix "` Horizontal Split
🔴 **Tier 1** — Split the current pane horizontally (top / bottom).

```
Syntax:   prefix "
```

```
Before:                    After:
┌──────────────────┐      ┌──────────────────┐
│                  │      │     pane 0       │
│     pane 0       │  →   ├──────────────────┤
│                  │      │     pane 1       │
└──────────────────┘      └──────────────────┘
```

**Examples:**
```bash
# Press Ctrl-b, then " (Shift+')
# The current pane splits into top and bottom panes
# Cursor moves to the new (bottom) pane
```

**Notes:**
- "Horizontal split" means the dividing line is horizontal (the split goes top/bottom).
- Many users rebind this to `prefix -` because it's more intuitive. See [Recommended Config](#11-recommended-tmuxconf-for-python-development).
- Combined with `prefix %`, you can create complex layouts. Split first horizontally, then split one of the resulting panes vertically, etc.

---

### `prefix arrow` Navigate Panes
🔴 **Tier 1** — Move focus between panes using arrow keys.

```
Syntax:   prefix Up/Down/Left/Right
```

**Examples:**
```bash
# Move to the pane above: press Ctrl-b, then Up arrow
# Move to the pane below: press Ctrl-b, then Down arrow
# Move to the pane on the left: press Ctrl-b, then Left arrow
# Move to the pane on the right: press Ctrl-b, then Right arrow
```

**Notes:**
- This is the default way to navigate panes. Many users rebind to vim-style keys (`h/j/k/l`) for speed. See [Recommended Config](#11-recommended-tmuxconf-for-python-development).
- The arrow keys can also be used without prefix if you bind them that way, but be careful not to conflict with programs running in panes.

---

### `prefix z` Zoom/Unzoom Pane
🔴 **Tier 1** — Toggle the current pane between zoomed (fullscreen) and normal size.

```
Syntax:   prefix z
```

**Examples:**
```bash
# You have a 4-pane layout and need more space in one pane temporarily:
# Press Ctrl-b, then z
# The current pane expands to fill the entire window
# The status bar shows a "Z" next to the window name to indicate zoom

# Press Ctrl-b, then z again
# The pane shrinks back to its original position in the layout
```

**Notes:**
- The zoomed pane is the exact same pane — it just temporarily hides the others. All other panes keep running.
- When zoomed, the window name in the status bar gets a `Z` marker (e.g., `1:editor*Z`).
- You can switch to another window while zoomed. When you come back, it's still zoomed.
- One of the most useful pane commands. Use it constantly.

---

### `prefix x` Close Pane
🔴 **Tier 1** — Close the current pane. Asks for confirmation.

```
Syntax:   prefix x
```

**Examples:**
```bash
# Press Ctrl-b, then x
# Status bar shows: "kill-pane 1? (y/n)"
# Press y to confirm

# Alternative: just type 'exit' or press Ctrl-d in the pane
# No confirmation needed with exit/Ctrl-d
```

**Notes:**
- Kills the process running in the pane and removes the pane.
- If this is the last pane in the window, the window closes too.
- `prefix x` is useful when a process is stuck. `exit` / `Ctrl-d` is better for normal usage because it lets the shell clean up.

---

### `prefix {/}` Swap Panes
🟡 **Tier 2** — Swap the current pane with the previous/next pane.

```
Syntax:   prefix {           # swap with previous pane
          prefix }           # swap with next pane
```

**Examples:**
```bash
# You have two panes side by side. You want the left one on the right:
# Focus the left pane
# Press Ctrl-b, then }
# The panes swap positions

# Layout: [editor | terminal]
# After prefix }: [terminal | editor]
```

**Notes:**
- "Previous" and "next" are based on pane numbering, not visual position. Use `prefix q` to see pane numbers.
- For more control, use the `swap-pane` command in command mode.

---

### `prefix q` Show Pane Numbers
🟡 **Tier 2** — Briefly display pane numbers on each pane. Press a number to jump to that pane.

```
Syntax:   prefix q
```

**Examples:**
```bash
# Press Ctrl-b, then q
# Each pane briefly shows a large number overlay (0, 1, 2, ...)
# Quickly press a number to jump to that pane
# If you don't press a number, the numbers disappear after ~1 second
```

**Notes:**
- The display timeout is configurable: `set -g display-panes-time 3000` (3 seconds).
- Pane numbers match what tmux uses internally for targeting panes in commands.
- Useful when you have many panes and can't easily reach one with arrow keys.

---

### `prefix ;` Last Pane
🟡 **Tier 2** — Toggle back to the previously active pane.

```
Syntax:   prefix ;
```

**Examples:**
```bash
# You're in the editor pane, you switch to the terminal pane to run a command
# Press Ctrl-b, then ; → you're back in the editor pane
# Like prefix l for windows, but for panes
```

**Notes:**
- Quick toggle between two panes. Perfect for editor + terminal workflow.

---

### `prefix space` Cycle Layouts
🟡 **Tier 2** — Cycle through preset pane layouts.

```
Syntax:   prefix Space
```

**Examples:**
```bash
# Press Ctrl-b, then Space (repeat to cycle)
# Cycles through these layouts:
#   even-horizontal  → all panes side by side, equal width
#   even-vertical    → all panes stacked, equal height
#   main-horizontal  → one large pane on top, rest on bottom
#   main-vertical    → one large pane on left, rest on right
#   tiled            → all panes in a grid
```

**Notes:**
- Very useful when you've manually split panes and they're uneven — just cycle to a preset layout.
- The layouts adapt to the number of panes you have.
- For specific layouts, use `prefix Alt-1` through `prefix Alt-5`. See [Resizing & Layouts](#7-resizing--layouts).

---

### `prefix !` Break Pane to Window
🔵 **Tier 3** — Move the current pane into its own new window.

```
Syntax:   prefix !
```

**Examples:**
```bash
# You have a split with editor and terminal
# You want to give the terminal its own full window:
# Focus the terminal pane
# Press Ctrl-b, then !
# The pane becomes a new window at the end of the window list
```

**Notes:**
- The pane's process is not interrupted. It just moves to its own window.
- There's no built-in key to do the reverse (move a window back into a pane), but you can do it with the `join-pane` command: `prefix :` then `join-pane -s <window-number>`.

---

---

# 6. Copy Mode

*Copy mode lets you scroll back, search, and copy text — all without a mouse. Essential for reviewing output on remote servers.*

---

### `prefix [` Enter Copy Mode
🟡 **Tier 2** — Enter copy mode to scroll, search, and select text.

```
Syntax:   prefix [
```

**Examples:**
```bash
# Press Ctrl-b, then [
# The top-right corner shows "[0/500]" (line position / total lines)
# You are now in copy mode

# Navigation (with default emacs keys):
#   Arrow keys     — move cursor
#   Page Up/Down   — scroll by page
#   Ctrl-s         — search forward
#   Ctrl-r         — search backward
#   q              — exit copy mode

# Navigation (with vi keys — recommended, set in config):
#   h/j/k/l        — move cursor
#   Ctrl-u/d       — half-page up/down
#   g/G            — go to top/bottom
#   /              — search forward
#   ?              — search backward
#   n/N            — next/previous search match
#   q              — exit copy mode
```

**Notes:**
- To enable vi-style keys in copy mode (highly recommended): `set -g mode-keys vi` in your `.tmux.conf`.
- Copy mode is the only way to scroll back in tmux. Your terminal's scroll doesn't work inside tmux by default (unless you enable mouse mode).
- The scrollback buffer is 2000 lines by default. Increase it: `set -g history-limit 50000`.

---

### Selecting and Copying Text (vi mode)
🟡 **Tier 2** — Select and copy text from the scrollback buffer.

```
Syntax:   (in copy mode)
          Space           — start selection
          Enter           — copy selection and exit copy mode
          v               — start selection (if configured)
          y               — yank/copy selection (if configured)
```

**Examples:**
```bash
# With vi mode enabled:

# 1. Enter copy mode: prefix [
# 2. Navigate to the start of what you want to copy
# 3. Press Space to begin selection
# 4. Move cursor to highlight the text you want
# 5. Press Enter to copy and exit copy mode

# To paste: prefix ]

# For more vi-like copy bindings, add to .tmux.conf:
#   bind-key -T copy-mode-vi v send-keys -X begin-selection
#   bind-key -T copy-mode-vi y send-keys -X copy-selection-and-cancel
```

**Notes:**
- Copied text goes into tmux's internal paste buffer, NOT the system clipboard.
- To integrate with the system clipboard, use the `tmux-yank` plugin or configure `copy-pipe`. See [Plugin Manager (TPM)](#10-plugin-manager-tpm).
- You can have multiple paste buffers. `prefix =` lets you choose which one to paste.

---

### Setting Vi Mode
🟡 **Tier 2** — Configure tmux to use vi-style keys in copy mode.

```
Syntax:   # Add to ~/.tmux.conf
          set -g mode-keys vi
```

**Examples:**
```bash
# Add to your config file
echo 'set -g mode-keys vi' >> ~/.tmux.conf

# Apply immediately without restarting tmux
tmux source-file ~/.tmux.conf

# Or from inside tmux, press prefix : and type:
#   set -g mode-keys vi

# Now copy mode uses vi navigation:
#   h/j/k/l   — move
#   w/b       — word forward/back
#   0/$       — beginning/end of line
#   /         — search forward
#   ?         — search backward
#   n/N       — next/previous match
#   Space     — begin selection
#   Enter     — copy selection
```

**Notes:**
- This only affects copy mode and the command prompt. It doesn't change anything about the programs running in your panes.
- The default is emacs mode. Vi mode is recommended if you're comfortable with vim navigation.

---

### `prefix ]` Paste
🟡 **Tier 2** — Paste the most recent copy buffer.

```
Syntax:   prefix ]
```

**Examples:**
```bash
# After copying text in copy mode (Enter to copy):
# Press Ctrl-b, then ]
# The copied text is pasted at the cursor position in the active pane
```

**Notes:**
- This pastes from tmux's internal buffer, not the system clipboard.
- If you have bracketed paste mode enabled in your shell, tmux will handle it correctly.

---

### `prefix =` Choose Paste Buffer
🔵 **Tier 3** — List all paste buffers and choose one to paste.

```
Syntax:   prefix =
```

**Examples:**
```bash
# Press Ctrl-b, then =
# A list of all paste buffers appears:
#   (0): some text you copied last
#   (1): some text you copied before that
#   ...
# Select one and press Enter to paste it
```

**Notes:**
- Tmux keeps multiple paste buffers (like a clipboard history).
- Useful when you need to paste something you copied earlier, not just the most recent copy.

---

### Scrollback Buffer
🟡 **Tier 2** — Configure how much history each pane retains.

```
Syntax:   # Add to ~/.tmux.conf
          set -g history-limit 50000
```

**Examples:**
```bash
# Default scrollback is 2000 lines — not enough for long builds or logs

# Set to 50,000 lines (recommended)
# Add to ~/.tmux.conf:
set -g history-limit 50000

# Check current setting from command mode:
# prefix : then type: show-option -g history-limit

# Clear a pane's scrollback buffer:
# prefix : then type: clear-history
```

**Notes:**
- Higher values use more memory, but on modern systems 50,000 lines per pane is fine.
- The scrollback buffer is per-pane. 10 panes × 50,000 lines = 500,000 lines in memory.
- If you need to keep very long logs, use `tmux pipe-pane` to log to a file instead of increasing the buffer endlessly.

---

---

# 7. Resizing & Layouts

*Resize panes manually with arrows, or jump to preset layouts. Tmux adapts layouts to the number of panes.*

---

### `prefix Ctrl-arrow` Resize Pane
🟡 **Tier 2** — Resize the current pane in the arrow direction.

```
Syntax:   prefix Ctrl-Up       # resize pane upward (make taller)
          prefix Ctrl-Down     # resize pane downward (make shorter)
          prefix Ctrl-Left     # resize pane left (make narrower)
          prefix Ctrl-Right    # resize pane right (make wider)
```

**Examples:**
```bash
# Make the current pane taller:
# Press Ctrl-b, then Ctrl-Up (hold Ctrl, press Up)
# Repeat to keep resizing

# You can also resize in command mode with exact amounts:
# prefix : then type:
#   resize-pane -D 10    # shrink down by 10 rows
#   resize-pane -U 5     # grow up by 5 rows
#   resize-pane -L 20    # shrink left by 20 columns
#   resize-pane -R 20    # grow right by 20 columns
```

**Notes:**
- Each press resizes by 1 cell. For larger adjustments, use the `resize-pane` command with a count.
- Some terminal emulators intercept `Ctrl-arrow` for their own purposes. If it doesn't work, use `resize-pane` in command mode.
- With mouse mode enabled (`set -g mouse on`), you can drag pane borders with the mouse.

---

### `prefix Alt-1..5` Preset Layouts
🟡 **Tier 2** — Jump directly to a specific preset layout.

```
Syntax:   prefix Alt-1         # even-horizontal
          prefix Alt-2         # even-vertical
          prefix Alt-3         # main-horizontal
          prefix Alt-4         # main-vertical
          prefix Alt-5         # tiled
```

**Examples:**
```bash
# You have 3 panes. Apply different layouts:

# prefix Alt-1: even-horizontal
# ┌──────┬──────┬──────┐
# │  0   │  1   │  2   │
# └──────┴──────┴──────┘

# prefix Alt-2: even-vertical
# ┌──────────────────┐
# │        0         │
# ├──────────────────┤
# │        1         │
# ├──────────────────┤
# │        2         │
# └──────────────────┘

# prefix Alt-3: main-horizontal
# ┌──────────────────┐
# │        0         │   ← main pane (large)
# ├────────┬─────────┤
# │   1    │    2    │   ← smaller panes below
# └────────┴─────────┘

# prefix Alt-4: main-vertical
# ┌───────────┬──────┐
# │           │  1   │
# │     0     ├──────┤   ← main pane on left (large)
# │           │  2   │
# └───────────┴──────┘

# prefix Alt-5: tiled (grid)
# ┌─────────┬────────┐
# │    0    │    1   │
# ├─────────┼────────┤
# │    2    │    3   │
# └─────────┴────────┘
```

**Notes:**
- `prefix Space` cycles through these same layouts one at a time.
- `main-horizontal` and `main-vertical` are great for development: put your editor in the main pane and terminals in the smaller ones.
- The "main" pane size can be configured: `set -g main-pane-width 60%` or `set -g main-pane-height 70%`.

---

### Layout Descriptions
🔵 **Tier 3** — Understanding each preset layout.

| Layout | Description | Best for |
|--------|-------------|----------|
| `even-horizontal` | All panes side by side, equal width | Comparing files/outputs |
| `even-vertical` | All panes stacked, equal height | Monitoring multiple logs |
| `main-horizontal` | One large top pane, rest below | Editor on top, terminals below |
| `main-vertical` | One large left pane, rest right | Editor on left, terminals right |
| `tiled` | Grid arrangement, as equal as possible | Dashboard / monitoring |

**Notes:**
- You can also set a specific layout string for exact control: `tmux select-layout <layout-string>`. Get the current layout string with `tmux list-windows` (it shows the layout checksum).

---

### `swap-pane`
🔵 **Tier 3** — Swap panes by number for precise control.

```
Syntax:   swap-pane -s <src> -t <dst>
          swap-pane -U               # swap with previous pane
          swap-pane -D               # swap with next pane
```

**Examples:**
```bash
# From command mode (prefix :):

# Swap pane 0 and pane 2
swap-pane -s 0 -t 2

# Move current pane up in the order
swap-pane -U

# Move current pane down in the order
swap-pane -D

# Swap panes between windows (advanced):
# Move pane 1 from window 2 to swap with pane 0 in current window
swap-pane -s 2.1 -t 0
```

**Notes:**
- Use `prefix q` to see pane numbers before swapping.
- `prefix {` and `prefix }` are shortcuts for `swap-pane -U` and `swap-pane -D`.

---

---

# 8. Command Mode & Scripting

*Command mode gives you access to every tmux command. Scripting lets you automate session setup.*

---

### `prefix :` Command Prompt
🟡 **Tier 2** — Open tmux's command prompt to run any tmux command.

```
Syntax:   prefix :
```

**Examples:**
```bash
# Press Ctrl-b, then :
# A prompt appears at the bottom of the screen
# Type any tmux command (without the "tmux" prefix)

# Examples of commands to type at the prompt:
#   new-window -n server          # create a named window
#   split-window -h               # horizontal split (same as prefix %)
#   resize-pane -D 10             # resize current pane down by 10 rows
#   set -g mouse on               # enable mouse support
#   list-keys                     # show all keybindings
#   display-message "hello"       # show a message in status bar
#   source-file ~/.tmux.conf      # reload config
```

**Notes:**
- This is the same as running `tmux <command>` from outside tmux, but without the `tmux` prefix.
- Tab completion works in command mode.
- Command history is available with arrow keys (up/down).
- Use `prefix :` when you need a command that doesn't have a keybinding.

---

### Common Commands
🔵 **Tier 3** — Frequently used commands for command mode.

#### `select-window`
Move to a specific window by number or name.

```bash
# From command mode (prefix :):
select-window -t 3                  # go to window 3
select-window -t editor             # go to window named "editor"
```

#### `swap-window`
Swap two windows' positions.

```bash
# From command mode (prefix :):
swap-window -s 0 -t 2              # swap window 0 and window 2
swap-window -t -1                  # move current window one position left
swap-window -t +1                  # move current window one position right
```

#### `move-window`
Renumber or move a window.

```bash
# From command mode (prefix :):
move-window -t 5                   # renumber current window to 5
move-window -r                     # renumber all windows sequentially (fill gaps)
```

#### `join-pane`
Move a pane from one window into another (reverse of `prefix !`).

```bash
# From command mode (prefix :):
join-pane -s 2                     # bring window 2 into current window as a pane
join-pane -s 2.1                   # bring pane 1 of window 2
join-pane -s 2 -h                  # bring as horizontal split
```

#### `list-keys` / `list-commands`
Show available keybindings or commands.

```bash
# From command mode (prefix :):
list-keys                          # all keybindings (same as prefix ?)
list-keys -T copy-mode-vi          # copy mode bindings
list-commands                      # all available commands
```

**Notes:**
- `join-pane` is incredibly useful — it's the reverse of `prefix !` (break pane to window).
- `move-window -r` is handy after closing windows that leave gaps (e.g., 0, 2, 5 → 0, 1, 2).

---

### Scripting tmux Sessions
🔵 **Tier 3** — Automate session creation with shell scripts.

```
Syntax:   tmux new-session -d -s <name> [-n <window>]
          tmux send-keys -t <target> '<command>' Enter
          tmux split-window -t <target> [-h|-v]
          tmux select-window -t <target>
```

**Examples:**
```bash
#!/bin/bash
# dev-session.sh — Sets up a Python development workspace

SESSION="myproject"

# Kill existing session if it exists
tmux kill-session -t $SESSION 2>/dev/null

# Create a new detached session with first window named "editor"
tmux new-session -d -s $SESSION -n editor

# Window 0: Editor — open the project in vim
tmux send-keys -t $SESSION:editor 'cd ~/projects/myapp && vim .' Enter

# Window 1: Server — run the dev server
tmux new-window -t $SESSION -n server
tmux send-keys -t $SESSION:server 'cd ~/projects/myapp && python manage.py runserver' Enter

# Window 2: Shell — general purpose, split into two panes
tmux new-window -t $SESSION -n shell
tmux send-keys -t $SESSION:shell 'cd ~/projects/myapp' Enter
tmux split-window -t $SESSION:shell -v
tmux send-keys -t $SESSION:shell 'cd ~/projects/myapp && python' Enter

# Window 3: Logs — tail application logs
tmux new-window -t $SESSION -n logs
tmux send-keys -t $SESSION:logs 'tail -f ~/projects/myapp/logs/app.log' Enter

# Go back to the editor window
tmux select-window -t $SESSION:editor

# Attach to the session
tmux attach -t $SESSION
```

```bash
#!/bin/bash
# monitor.sh — Set up a monitoring dashboard

SESSION="monitor"
tmux kill-session -t $SESSION 2>/dev/null

tmux new-session -d -s $SESSION -n dashboard

# Create 4 panes in a grid
tmux split-window -t $SESSION -h
tmux split-window -t $SESSION -v
tmux select-pane -t 0
tmux split-window -t $SESSION -v

# Send commands to each pane
tmux send-keys -t $SESSION:0.0 'htop' Enter
tmux send-keys -t $SESSION:0.1 'watch df -h' Enter
tmux send-keys -t $SESSION:0.2 'tail -f /var/log/syslog' Enter
tmux send-keys -t $SESSION:0.3 'watch -n 5 ss -tlnp' Enter

tmux attach -t $SESSION
```

**Notes:**
- Always create sessions with `-d` (detached) in scripts, then attach at the end.
- Target syntax: `session:window.pane` — e.g., `myproject:editor.0`.
- `2>/dev/null` on kill-session suppresses the error if the session doesn't exist.
- Make the script executable: `chmod +x dev-session.sh`.
- For more complex setups, consider `tmuxinator` or `tmuxp` — they use YAML config files.

---

### `tmux send-keys`
🔵 **Tier 3** — Send keystrokes to a pane programmatically.

```
Syntax:   tmux send-keys -t <target> '<keys>' [Enter]
```

| Flag | Meaning |
|------|---------|
| `-t <target>` | Target pane (session:window.pane) |
| `-l` | Send keys literally (don't interpret special keys) |
| `Enter` | The Enter key (sent as a separate argument, not quoted) |
| `C-c` | Ctrl-c |
| `C-l` | Ctrl-l (clear screen) |

**Examples:**
```bash
# Send a command to a specific pane
tmux send-keys -t webdev:server 'npm run dev' Enter

# Send Ctrl-c to stop a process in a pane
tmux send-keys -t webdev:server C-c

# Send text without pressing Enter
tmux send-keys -t webdev:editor 'echo hello'

# Clear a pane's screen
tmux send-keys -t webdev:shell C-l

# Send literal text (don't interpret special characters)
tmux send-keys -t webdev:shell -l 'Enter is just text here'
```

**Notes:**
- `Enter`, `Escape`, `Space`, `C-c`, `C-l`, etc. are special key names. Quote your text but leave these unquoted.
- The `-t` target follows the pattern `session:window.pane`. Omit parts to use defaults.
- `send-keys` is the backbone of tmux scripting.

---

---

# 9. Advanced Features

*Power-user features: sync panes, log output, monitor activity. You won't use these daily, but they're invaluable when you need them.*

---

### Synchronize Panes
🔵 **Tier 3** — Type the same input into all panes simultaneously.

```
Syntax:   # From command mode (prefix :):
          setw synchronize-panes on
          setw synchronize-panes off
```

**Examples:**
```bash
# Scenario: You have 4 panes, each SSH'd into a different server
# You want to run the same command on all of them simultaneously

# Enable sync:
# prefix : then type: setw synchronize-panes on
# All panes now have a highlighted border to indicate sync is active

# Type a command — it appears in ALL panes:
sudo apt update && sudo apt upgrade -y
# All 4 servers run the update simultaneously

# Disable sync when done:
# prefix : then type: setw synchronize-panes off

# Pro tip: bind a key for quick toggle
# Add to .tmux.conf:
# bind S setw synchronize-panes
```

**Notes:**
- The toggle key binding (`bind S setw synchronize-panes`) toggles on/off — much faster than typing the command each time.
- Synchronized panes show a distinct border color (depends on your config).
- Be very careful with sync enabled — a typo goes to ALL panes. Accidentally running `rm -rf /` on 4 servers simultaneously would be catastrophic.
- Works great for: updating multiple servers, checking status across hosts, running identical setup commands.

---

### `tmux pipe-pane`
🔵 **Tier 3** — Log all output from a pane to a file.

```
Syntax:   tmux pipe-pane -t <target> [-o] '<shell-command>'
          tmux pipe-pane -t <target>      # stop piping (no command = disable)
```

| Flag | Meaning |
|------|---------|
| `-t <target>` | Target pane |
| `-o` | Only pipe output (not input) |
| `-O` | Only pipe output, include existing screen content |

**Examples:**
```bash
# Log everything from the current pane to a file:
# prefix : then type:
pipe-pane -o 'cat >> ~/tmux-pane.log'

# Start logging with timestamp:
# prefix : then type:
pipe-pane -o 'while IFS= read -r line; do echo "$(date +"%Y-%m-%d %H:%M:%S") $line"; done >> ~/tmux-session.log'

# Stop logging:
# prefix : then type:
pipe-pane

# From outside tmux:
tmux pipe-pane -t webdev:server -o 'cat >> ~/server.log'

# Pro tip: bind a key for quick toggle
# Add to .tmux.conf:
# bind P pipe-pane -o 'cat >> ~/tmux-#{session_name}-#{window_name}.log' \; display "Logging toggled"
```

**Notes:**
- `pipe-pane` captures the raw terminal output, including ANSI color codes. To strip them, pipe through `sed 's/\x1b\[[0-9;]*m//g'`.
- Running `pipe-pane` without a command argument stops the logging.
- The log file grows indefinitely. Monitor its size or rotate it.

---

### `tmux capture-pane`
🔵 **Tier 3** — Capture the contents of a pane into a paste buffer or file.

```
Syntax:   tmux capture-pane [-t <target>] [-p] [-S <start-line>] [-E <end-line>]
```

| Flag | Meaning |
|------|---------|
| `-t <target>` | Target pane |
| `-p` | Print to stdout instead of paste buffer |
| `-S <line>` | Start line (`-` for start of scrollback, negative numbers count from top of visible area) |
| `-E <line>` | End line (`-` for end of scrollback) |
| `-J` | Join wrapped lines |

**Examples:**
```bash
# Capture the visible pane contents to a paste buffer
tmux capture-pane
# Then paste with prefix ]

# Capture and print to stdout (pipe to a file)
tmux capture-pane -p > ~/pane-contents.txt

# Capture the entire scrollback history
tmux capture-pane -p -S - > ~/full-scrollback.txt

# Capture last 100 lines of scrollback
tmux capture-pane -p -S -100 > ~/recent-output.txt

# Capture a specific pane in another window
tmux capture-pane -t webdev:server.0 -p > ~/server-output.txt
```

**Notes:**
- Without `-p`, the captured text goes into the paste buffer (retrieve with `prefix ]`).
- With `-p`, it prints to stdout, which you can redirect to a file. Very useful for saving command output.
- `-S -` means "start of scrollback" and `-E -` means "end of scrollback" — together they capture everything.
- `capture-pane` gets the current state. For continuous logging, use `pipe-pane`.

---

### Monitor Activity / Silence
🔵 **Tier 3** — Get notified when something happens (or stops happening) in a pane.

```
Syntax:   # From command mode (prefix :):
          setw monitor-activity on       # notify when output appears
          setw monitor-silence 30        # notify after 30 seconds of no output
```

**Examples:**
```bash
# Monitor a build window — get notified when it produces output:
# Switch to the build window, then:
# prefix : then type: setw monitor-activity on
# Switch to another window to work
# When the build window gets output, its name highlights in the status bar

# Monitor a long-running process — get notified when it stops:
# prefix : then type: setw monitor-silence 60
# If the pane produces no output for 60 seconds, the status bar highlights

# Enable activity monitoring globally (for all windows):
# Add to .tmux.conf:
# set -g monitor-activity on
# set -g visual-activity on       # show a message, not just highlight
```

**Notes:**
- `monitor-activity on` highlights the window name in the status bar when new output appears.
- `visual-activity on` also shows a "Activity in window X" message.
- `monitor-silence` takes a value in seconds. Set to `0` to disable.
- These are per-window settings (`setw`), not global (though you can set global defaults).

---

### Hooks
🔵 **Tier 3** — Run commands automatically when tmux events occur.

```
Syntax:   set-hook -g <hook-name> '<command>'
```

**Examples:**
```bash
# Run a command when a new session is created
tmux set-hook -g session-created 'display-message "Welcome to tmux!"'

# Run a command when a window is renamed
tmux set-hook -g window-renamed 'run-shell "echo renamed >> ~/tmux-events.log"'

# Automatically rename windows based on running command
tmux set-hook -g pane-focus-in 'run-shell "tmux rename-window \"$(basename #{pane_current_path})\""'

# Available hooks include:
#   session-created, session-closed, session-renamed
#   window-linked, window-unlinked, window-renamed
#   pane-focus-in, pane-focus-out, pane-exited
#   client-attached, client-detached, client-resized
#   after-split-window, after-new-window, after-select-pane
```

**Notes:**
- Hooks use the `-g` flag for global hooks.
- Use `run-shell` within hooks to execute shell commands.
- View current hooks: `tmux show-hooks -g`.
- Remove a hook: `tmux set-hook -gu <hook-name>`.
- Hooks are powerful for automation but can be tricky to debug. Start simple.

---

---

# 10. Plugin Manager (TPM)

*TPM (Tmux Plugin Manager) lets you install and manage tmux plugins. Plugins extend tmux with features like persistent sessions, clipboard integration, and better defaults.*

---

### Installing TPM
🟡 **Tier 2** — One-time setup for the tmux plugin manager.

**Step 1: Clone TPM**

```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

**Step 2: Add to ~/.tmux.conf**

```bash
# Add these lines at the BOTTOM of your ~/.tmux.conf:

# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Initialize TPM (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

**Step 3: Reload and Install**

```bash
# Reload tmux config
tmux source-file ~/.tmux.conf

# Inside tmux, press:
#   prefix + I    (capital I = Shift+i)
# TPM will clone and install all listed plugins
# You'll see a message when it's done
```

**Notes:**
- TPM itself is a plugin — it manages other plugins.
- The `run '~/.tmux/plugins/tpm/tpm'` line MUST be the last line in your config.
- Plugins are cloned to `~/.tmux/plugins/`.

---

### `prefix + I` Install Plugins
🟡 **Tier 2** — Install all plugins listed in tmux.conf.

```
Syntax:   prefix + I          # capital I (Shift+i)
```

**Examples:**
```bash
# After adding a new plugin to .tmux.conf:
set -g @plugin 'tmux-plugins/tmux-resurrect'

# 1. Reload config: prefix : then source-file ~/.tmux.conf
# 2. Install: prefix + I
# TPM clones the plugin and shows: "TMUX environment reloaded."
```

---

### `prefix + U` Update Plugins
🟡 **Tier 2** — Update all installed plugins.

```
Syntax:   prefix + U          # capital U (Shift+u)
```

**Examples:**
```bash
# Press prefix + U (Shift+u)
# A list of plugins appears
# Press Enter to update all, or select specific ones
```

---

### `prefix + alt + u` Uninstall Plugins
🟡 **Tier 2** — Remove plugins not listed in tmux.conf.

```
Syntax:   prefix + alt + u    # lowercase u
```

**Examples:**
```bash
# 1. Remove the set -g @plugin line from .tmux.conf
# 2. Press prefix + alt + u
# TPM removes the plugin directory
```

---

### Recommended Plugins

| Plugin | What it does |
|--------|--------------|
| `tmux-plugins/tmux-sensible` | Sensible default options everyone can agree on (larger scrollback, faster key repeat, etc.) |
| `tmux-plugins/tmux-resurrect` | Save and restore tmux sessions across system restarts. `prefix + Ctrl-s` to save, `prefix + Ctrl-r` to restore |
| `tmux-plugins/tmux-continuum` | Automatic saving (every 15 min) and automatic restore on tmux start. Works with tmux-resurrect |
| `tmux-plugins/tmux-yank` | Copy to system clipboard. Makes `y` in copy mode copy to clipboard |
| `tmux-plugins/tmux-open` | Open highlighted file/URL from copy mode. `o` opens, `Ctrl-o` opens in editor |
| `tmux-plugins/tmux-pain-control` | Better pane navigation and resizing bindings |
| `tmux-plugins/tmux-prefix-highlight` | Highlight status bar when prefix is pressed — visual feedback |
| `tmux-plugins/tmux-cpu` | Show CPU and GPU usage in status bar |

**Examples:**
```bash
# A solid plugin setup in .tmux.conf:
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'tmux-plugins/tmux-yank'

# tmux-resurrect settings
set -g @resurrect-capture-pane-contents 'on'
set -g @resurrect-strategy-vim 'session'     # restore vim sessions

# tmux-continuum settings
set -g @continuum-restore 'on'               # auto-restore on tmux start
set -g @continuum-save-interval '15'         # save every 15 minutes

# Initialize TPM (must be last line)
run '~/.tmux/plugins/tpm/tpm'
```

**Notes:**
- `tmux-resurrect` + `tmux-continuum` together give you persistent sessions that survive reboots. This is a game-changer for development workflows.
- `tmux-yank` makes clipboard integration seamless. Without it, copying from tmux to other applications requires workarounds.
- Start with `tmux-sensible`, `tmux-resurrect`, `tmux-continuum`, and `tmux-yank`. Add others as needed.

---

---

# 11. Recommended tmux.conf for Python Development

*A complete, copy-paste-ready configuration. Every line is commented. Save as `~/.tmux.conf`.*

---

```bash
# ============================================================================
# ~/.tmux.conf — Python development configuration
# ============================================================================
# Reload this config:  prefix r  (bound below)
# Or manually:         tmux source-file ~/.tmux.conf
# ============================================================================


# ----------------------------------------------------------------------------
# Prefix Key
# ----------------------------------------------------------------------------
# Rebind prefix from Ctrl-b to Ctrl-a (easier to reach, screen/byobu users
# already have the muscle memory)
unbind C-b
set -g prefix C-a
bind C-a send-prefix
# "send-prefix" lets you send Ctrl-a to programs inside tmux by pressing
# Ctrl-a twice. Some programs (like readline) use Ctrl-a for beginning-of-line.


# ----------------------------------------------------------------------------
# General Settings
# ----------------------------------------------------------------------------
# Enable mouse support (scroll, click panes, resize by dragging borders)
set -g mouse on

# Start window numbering at 1 (0 is too far from the prefix key)
set -g base-index 1

# Start pane numbering at 1
setw -g pane-base-index 1

# Renumber windows when one is closed (no gaps: 1,2,3 instead of 1,3,4)
set -g renumber-windows on

# Increase scrollback buffer (default is 2000 — not enough)
set -g history-limit 50000

# Faster command sequences (reduce delay after pressing Escape)
set -sg escape-time 0

# Increase display time for pane numbers (prefix q)
set -g display-panes-time 3000

# Increase display time for status messages
set -g display-time 2000

# Enable focus events (needed by some vim plugins)
set -g focus-events on

# Don't auto-rename windows after you've manually named them
set -g allow-rename off

# Use vi key bindings in copy mode
set -g mode-keys vi

# Use vi key bindings in command prompt
set -g status-keys vi


# ----------------------------------------------------------------------------
# Colors & True Color Support
# ----------------------------------------------------------------------------
# Enable 256 colors
set -g default-terminal "tmux-256color"

# Enable true color (24-bit) support — needed for modern color schemes in vim
set -ga terminal-overrides ",xterm-256color:Tc"
set -ga terminal-overrides ",alacritty:Tc"


# ----------------------------------------------------------------------------
# Key Bindings — Splits
# ----------------------------------------------------------------------------
# Intuitive split bindings: | for vertical, - for horizontal
# Open new panes in the current directory
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"

# Keep the defaults too (% and ") but also use current directory
unbind %
unbind '"'
bind % split-window -h -c "#{pane_current_path}"
bind '"' split-window -v -c "#{pane_current_path}"


# ----------------------------------------------------------------------------
# Key Bindings — Navigation (vim-style)
# ----------------------------------------------------------------------------
# Switch panes with h/j/k/l (vim-style, no prefix needed with Alt)
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Alt + h/j/k/l to switch panes without prefix (faster)
bind -n M-h select-pane -L
bind -n M-j select-pane -D
bind -n M-k select-pane -U
bind -n M-l select-pane -R

# Alt + n/p for next/previous window without prefix
bind -n M-n next-window
bind -n M-p previous-window


# ----------------------------------------------------------------------------
# Key Bindings — Resizing
# ----------------------------------------------------------------------------
# Resize panes with H/J/K/L (capital = resize, lowercase = navigate)
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# -r means "repeatable" — press prefix once, then H/J/K/L multiple times


# ----------------------------------------------------------------------------
# Key Bindings — Copy Mode (vi-style)
# ----------------------------------------------------------------------------
# Enter copy mode with prefix [
# Use vi keys to navigate
# v to start selection, y to yank, q to cancel

bind -T copy-mode-vi v send-keys -X begin-selection
bind -T copy-mode-vi y send-keys -X copy-selection-and-cancel
bind -T copy-mode-vi r send-keys -X rectangle-toggle

# Also bind mouse drag to copy
bind -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel


# ----------------------------------------------------------------------------
# Key Bindings — Convenience
# ----------------------------------------------------------------------------
# Easy config reload
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Quick pane sync toggle (for running commands on multiple servers)
bind S setw synchronize-panes \; display "Sync #{?synchronize-panes,ON,OFF}"

# Clear pane history
bind C-l clear-history \; display "History cleared"

# Create a new window in the current directory
bind c new-window -c "#{pane_current_path}"


# ----------------------------------------------------------------------------
# Status Bar
# ----------------------------------------------------------------------------
# Status bar position
set -g status-position bottom

# Update status bar every 5 seconds
set -g status-interval 5

# Status bar length
set -g status-left-length 40
set -g status-right-length 80

# Status bar colors
set -g status-style 'bg=colour235 fg=colour248'

# Left side: session name
set -g status-left '#[fg=colour232,bg=colour39,bold] #S #[fg=colour39,bg=colour235] '

# Right side: hostname, date, time
set -g status-right '#[fg=colour248,bg=colour235] #(whoami)@#H #[fg=colour232,bg=colour248,bold] %Y-%m-%d %H:%M '

# Window list styling
# Current window
setw -g window-status-current-style 'fg=colour232 bg=colour39 bold'
setw -g window-status-current-format ' #I:#W#F '

# Other windows
setw -g window-status-style 'fg=colour248 bg=colour238'
setw -g window-status-format ' #I:#W#F '

# Window with activity
setw -g window-status-activity-style 'fg=colour39 bg=colour235 bold'

# Pane borders
set -g pane-border-style 'fg=colour238'
set -g pane-active-border-style 'fg=colour39'

# Message styling
set -g message-style 'fg=colour232 bg=colour39 bold'


# ----------------------------------------------------------------------------
# Plugins (via TPM)
# ----------------------------------------------------------------------------
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'tmux-plugins/tmux-yank'

# tmux-resurrect: save/restore sessions across restarts
set -g @resurrect-capture-pane-contents 'on'

# tmux-continuum: automatic save and restore
set -g @continuum-restore 'on'
set -g @continuum-save-interval '15'

# Initialize TPM (MUST be the last line in tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

---

### Python Development Workflow Tips

Once your config is loaded, here's how to set up an ideal Python development layout.

#### Quick Manual Setup

```bash
# Start a named session for your project
tmux new -s myproject

# Rename the first window to "editor"
# prefix , → type "editor" → Enter

# Open your editor (vim, nvim, or code)
vim .

# Create a second window for the terminal
# prefix c → prefix , → type "terminal" → Enter

# Split the terminal window: top for commands, bottom for Python REPL
# prefix " (horizontal split)

# In the bottom pane, start the Python REPL
python3

# Result:
# Window "editor": full-screen vim
# Window "terminal":
#   ┌──────────────────────────┐
#   │  shell (run tests, git)  │
#   ├──────────────────────────┤
#   │  python3 REPL            │
#   └──────────────────────────┘

# Toggle between windows: prefix n / prefix p / prefix l
# Zoom a pane temporarily: prefix z
```

#### Scripted Setup

```bash
#!/bin/bash
# python-dev.sh — Launch a Python development workspace
# Usage: ./python-dev.sh [project-directory]

PROJECT_DIR="${1:-$(pwd)}"
SESSION="$(basename $PROJECT_DIR)"

# Kill existing session
tmux kill-session -t "$SESSION" 2>/dev/null

# Window 0: Editor
tmux new-session -d -s "$SESSION" -n editor -c "$PROJECT_DIR"
tmux send-keys -t "$SESSION:editor" 'vim .' Enter

# Window 1: Terminal (split: shell on top, REPL on bottom)
tmux new-window -t "$SESSION" -n terminal -c "$PROJECT_DIR"
tmux split-window -t "$SESSION:terminal" -v -c "$PROJECT_DIR"
tmux send-keys -t "$SESSION:terminal.1" 'python3' Enter
tmux select-pane -t "$SESSION:terminal.0"

# Window 2: Tests (for running pytest)
tmux new-window -t "$SESSION" -n tests -c "$PROJECT_DIR"
tmux send-keys -t "$SESSION:tests" 'echo "Run tests: pytest -v"' Enter

# Window 3: Server (for Django/Flask/FastAPI dev server)
tmux new-window -t "$SESSION" -n server -c "$PROJECT_DIR"

# Window 4: Git
tmux new-window -t "$SESSION" -n git -c "$PROJECT_DIR"
tmux send-keys -t "$SESSION:git" 'git status' Enter

# Start in the editor window
tmux select-window -t "$SESSION:editor"

# Attach
tmux attach -t "$SESSION"
```

```bash
# Save as ~/bin/python-dev.sh (or anywhere in PATH)
chmod +x ~/bin/python-dev.sh

# Usage:
python-dev.sh ~/projects/my-django-app
python-dev.sh ~/projects/ml-pipeline
python-dev.sh   # uses current directory
```

#### Common Workflow Patterns

```bash
# Run tests in one pane, edit in another:
#   Pane 0: vim tests/test_app.py
#   Pane 1: pytest -v --tb=short tests/test_app.py
#   Use prefix z to zoom in on test output, prefix z again to unzoom

# Django development:
#   Window "editor": vim
#   Window "server": python manage.py runserver
#   Window "shell":  python manage.py shell_plus
#   Window "db":     python manage.py dbshell

# FastAPI development:
#   Window "editor": vim
#   Window "server": uvicorn app.main:app --reload
#   Window "test":   pytest -v --tb=short
#   Window "docs":   open http://localhost:8000/docs (just a reminder pane)

# Data science / ML:
#   Window "code":   vim or jupyter (in terminal: jupyter console)
#   Window "train":  python train.py (long-running, survives disconnect!)
#   Window "logs":   tail -f training.log
#   Window "gpu":    watch -n 1 nvidia-smi
```

---

---

# Quick Reference Card

*Tear this out and pin it to your monitor until the keybindings are in muscle memory.*

### Session Commands (from terminal)

| Command | Action |
|---------|--------|
| `tmux` | Start unnamed session |
| `tmux new -s name` | Start named session |
| `tmux ls` | List sessions |
| `tmux a -t name` | Attach to session |
| `tmux kill-session -t name` | Kill session |
| `tmux kill-server` | Kill everything |

### Inside tmux (prefix = Ctrl-b, or Ctrl-a if rebound)

| Keys | Action | Tier |
|------|--------|------|
| **Sessions** | | |
| `prefix d` | Detach | 🔴 |
| `prefix s` | Session picker | 🟡 |
| `prefix $` | Rename session | 🟡 |
| `prefix (` / `)` | Previous/next session | 🟡 |
| **Windows** | | |
| `prefix c` | New window | 🔴 |
| `prefix n` / `p` | Next/prev window | 🔴 |
| `prefix 0-9` | Go to window # | 🔴 |
| `prefix ,` | Rename window | 🔴 |
| `prefix &` | Kill window | 🟡 |
| `prefix w` | Window picker | 🟡 |
| `prefix l` | Last window | 🟡 |
| **Panes** | | |
| `prefix %` | Vertical split | 🔴 |
| `prefix "` | Horizontal split | 🔴 |
| `prefix arrow` | Navigate panes | 🔴 |
| `prefix z` | Zoom/unzoom | 🔴 |
| `prefix x` | Kill pane | 🔴 |
| `prefix {` / `}` | Swap panes | 🟡 |
| `prefix q` | Show pane numbers | 🟡 |
| `prefix space` | Cycle layouts | 🟡 |
| `prefix !` | Pane → window | 🔵 |
| **Copy Mode** | | |
| `prefix [` | Enter copy mode | 🟡 |
| `prefix ]` | Paste | 🟡 |
| `Space` / `Enter` | Select / copy (in copy mode) | 🟡 |
| **Other** | | |
| `prefix :` | Command prompt | 🟡 |
| `prefix ?` | List keybindings | 🔴 |
| `prefix Ctrl-arrow` | Resize pane | 🟡 |
| `prefix Alt-1..5` | Preset layouts | 🟡 |

---

*End of tmux reference. Go build something — in a session that survives the inevitable SSH dropout.*
