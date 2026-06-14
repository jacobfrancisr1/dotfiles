# 🧭 My Programming System — Start Here

> The "I haven't coded in a few weeks and forgot everything" manual.
> Read the **Quick Start** when you set up a new machine. Skim the **Cheat Sheets** when you forget a key. Everything here is wired to **your** dotfiles at `github.com/jacobfrancisr1/dotfiles`.

---

## 0. The 30-second mental model

Your whole setup is one git repo that lives at `~/.dotfiles`. Nothing is configured "in your Mac" — it's all files in that repo, and your home directory just has **symlinks** pointing into it.

```
~/.dotfiles/                 ← the repo (edit things HERE)
├── zsh/zshrc                ← your shell config
├── config/ghostty/config    ← terminal look & keys
├── config/nvim/             ← Neovim (LazyVim)
├── config/tmux/tmux.conf     ← tmux
├── config/starship/          ← the prompt
└── config/brew/Brewfile      ← every app/tool you install

~/.zshrc            → symlink → ~/.dotfiles/zsh/zshrc
~/.config/ghostty   → symlink → ~/.dotfiles/config/ghostty
~/.config/nvim      → symlink → ~/.dotfiles/config/nvim
... etc.
```

**Why this is great:** edit a file → it's instantly live → `git commit && git push` → it's backed up and ready to drop onto any new Mac in one command.

**The one rule:** never edit `~/.config/ghostty/config` directly thinking it's separate. It *is* the repo file. Edit, then commit.

---

## 1. Quick Start (new machine, or first-time setup)

You have an `install.sh` in this kit. On a fresh Mac:

```bash
# 1. Put the installer somewhere and run it
cd ~/Downloads          # or wherever you saved the kit
./install.sh
```

That script will: install Homebrew, clone your dotfiles to `~/.dotfiles`, back up anything it would overwrite, create all the symlinks, install every tool from your Brewfile, set zsh as your shell, and set up signed commits. Then:

```bash
# 2. Restart your terminal (or:)
exec zsh

# 3. First time only — let the plugin managers self-install:
#    - zinit (zsh plugins) installs automatically on first shell launch
#    - tmux plugins: open tmux, press  ` + I   (backtick, then capital I)
#    - neovim plugins: just run  nvim  and LazyVim installs everything
```

That's it. The rest of this doc is reference for when you forget how a piece works.

---

## 2. What every tool in your stack is (and why it's there)

You installed a lot. Here's the map so a name never confuses you again.

| Tool | What it is | You notice it when… |
|------|-----------|---------------------|
| **Ghostty** | Your terminal app | It's the window everything runs in |
| **zsh + zinit** | Your shell + its plugin manager | Autosuggestions (grey text), syntax highlighting |
| **Starship** | The prompt (that line you type next to) | Shows git branch, language versions, dir |
| **tmux** | Terminal multiplexer — splits & persistent sessions | Prefix is the **backtick** `` ` `` key |
| **Neovim + LazyVim** | Your code editor in the terminal | `nvim file.py` or `vim` (aliased) |
| **sesh** | Session manager for tmux | `` ` `` then `S` opens a project picker |
| **zoxide** | Smart `cd` that learns your dirs | `cd` is aliased to it — `cd proj` jumps anywhere |
| **fzf** | Fuzzy finder (the `**<tab>` magic) | Ctrl-T (files), Ctrl-R (history), Alt-C (dirs) |
| **eza** | Modern `ls` with icons/git | `l`, `ll`, `LL` aliases |
| **bat** | `cat` with syntax highlighting | `cat` is aliased to it |
| **fd** | Modern `find` | Powers fzf; also use directly: `fd pattern` |
| **ripgrep (rg)** | Blazing-fast code search | `rg "TODO"` searches all files |
| **lazygit** | Visual git UI | `gg`, or in tmux `` ` `` then `g` |
| **lazydocker** | Visual docker UI | `lzd`, or in tmux `` ` `` then `d` |
| **uv** | Fast Python package/venv manager | `uv run`, `uv pip`, replaces pip/venv |
| **ruff** | Python linter/formatter | Runs in nvim on save |
| **direnv** | Auto-loads env vars per folder | Reads `.envrc` when you `cd` in |
| **aerospace** | Tiling window manager (macOS) | Auto-arranges your app windows |
| **sketchybar** | Custom macOS menu bar | The status bar at the top |
| **yazi** | Terminal file manager | Type `y` to browse, it cd's you on exit |
| **tldr** | Simplified man pages | `help <command>` (aliased) |

---

## 3. Daily Git & GitHub workflow

This is the part that fades fastest between coding sessions. Here's the loop, plus your custom aliases.

### The everyday loop
```bash
git clone <url>            # or: gh repo clone owner/name
cd repo
# ...make changes...
gs                         # git status  (your alias)
ga                         # git add -u .  (stages tracked changes)
git add <newfile>          # stage a brand-new file explicitly
git commit -m "message"    # commit (auto-signed — see §3.3)
gP                         # git push   (your alias; auto-sets upstream)
```

### Your git aliases (defined in `aliases.zsh`)
| Alias | Runs | Meaning |
|-------|------|---------|
| `gs` | `git status` | What's changed |
| `gd` | `git diff` | See unstaged changes |
| `ga` | `git add -u .` | Stage all **tracked** edits (not new files) |
| `gp` | `git pull --rebase` | Update, replaying your work on top |
| `gpa` | `git pull --rebase --autostash` | Same, but stashes uncommitted work first |
| `gP` | `git push` | Send commits up |
| `gg` | `lazygit` | Full visual git UI — **use this when confused** |

### Your git *subcommand* aliases (defined in `.gitconfig`)
These run as `git <name>`:
| Command | What it does |
|---------|-------------|
| `git lg` | Pretty graph log of all branches |
| `git undo` | Undo last commit, keep the changes staged |
| `git sync [branch]` | Fetch + rebase onto origin/main (autostash) |
| `git squash [branch]` | Squash all commits since main into one |
| `git pushf` | Force-push **safely** (`--force-with-lease`) |
| `git up` | `pull --rebase` |
| `git co` / `git br` / `git ci` | checkout / branch / commit |

### 3.3 — "Why does it ask about signing / say commit failed?"
Your `.gitconfig` has `commit.gpgsign = true` using **SSH signing**. That means every commit is cryptographically signed and shows a green **Verified** badge on GitHub. The `install.sh` sets this up for you. If you ever switch machines manually and commits fail with *"unable to sign"*, it's because the signing key isn't configured. Two fixes:

```bash
# Option A — point git at your SSH key (preferred):
git config --global user.signingkey ~/.ssh/id_ed25519.pub
# then add that same .pub to GitHub → Settings → SSH and GPG keys → "Signing Key"

# Option B — turn signing off temporarily (edit the repo file):
#   in ~/.dotfiles/config/gitconfigs/.gitconfig comment out:  gpgsign = true
```

Your personal name/email/signing-key live in `~/.gitconfig-local` (NOT in the repo — that's intentional, so secrets never get committed).

### 3.4 — `gh` (GitHub CLI)
```bash
gh auth login              # one-time: authenticate
gh repo create             # make a new repo from current folder
gh repo clone owner/name   # clone
gh pr create               # open a pull request
gh pr checkout 123         # check out someone's PR locally
```

### 3.5 — The repo audit toolkit (your `git_audit.zsh`)
Drop into any repo and run these to understand it fast — great for a new job or open-source project:
| Command | Tells you |
|---------|-----------|
| `git-churn` | The 20 files that change the most (the risky hotspots) |
| `git-authors` | Who wrote what, ranked |
| `git-bugs` | Files most tied to bug-fix commits |
| `git-velocity` | Commits per month — is the project alive? |
| `git-audit` | Runs all of them at once |

---

## 4. Terminal cheat sheets

### 4.1 Ghostty (the window) — `cmd` keys
| Key | Action |
|-----|--------|
| `cmd + ` `` ` `` | Toggle Ghostty visibility (global hotkey — works from any app) |
| `ctrl + d` | New split to the right (native) |
| `ctrl + shift + d` | New split down |
| `ctrl + shift + z` | Zoom/unzoom the focused split |
| `cmd + d` | Split in **tmux** left/right |
| `cmd + shift + d` | Split in **tmux** top/bottom |

### 4.2 tmux — prefix is the **backtick** `` ` ``
Press `` ` `` then the key. (To type a literal backtick, hit it twice.)
| Keys | Action |
|------|--------|
| `Alt + ←↓↑→` | Move between panes (no prefix needed; vim-aware) |
| `Shift + ← / →` | Move current window left / right |
| `` ` `` then `%` | Split left/right |
| `` ` `` then `"` | Split top/bottom |
| `` ` `` then `S` | **sesh** session/project picker (your launchpad) |
| `` ` `` then `g` | lazygit popup |
| `` ` `` then `d` | lazydocker popup |
| `` ` `` then `t` | Floating shell popup |
| `` ` `` then `R` | Reload tmux config |
| `` ` `` then `x` | Kill current pane |
| `` ` `` then `I` | Install tmux plugins (first-time setup) |
| Type `:sync` | Toggle typing into all panes at once |

**Start/attach your main session:** `tmx` (alias). Reattach later: `tmxa`.

### 4.3 zsh aliases you'll actually use
| Alias | Does |
|-------|------|
| `l` / `ll` / `LL` | Nice listings (short / all+detail / full+sizes) |
| `lt` | Tree view |
| `cat` | → `bat` (pretty cat) |
| `cd` | → `zoxide` (jump to any dir by name fragment) |
| `vim` | → `nvim` |
| `py` | → `ipython` |
| `gg` | lazygit |
| `k` | kubectl |
| `zz` | Edit `~/.zshrc` |
| `dots` | Jump to `~/.dotfiles` and open the zshrc |
| `aliases` | Open the aliases file to add a new one |
| `brewup` | Update + upgrade everything via Homebrew, then clean up |
| `y` | Open yazi file manager |
| `mkcd <dir>` | Make a directory and cd into it |

### 4.4 fzf fuzzy-finding (muscle memory worth building)
| Keys | Finds |
|------|-------|
| `Ctrl + T` | Files (with preview) — inserts path at cursor |
| `Ctrl + R` | Your command history |
| `Alt + C` | cd into a subdirectory |
| `<cmd> **<Tab>` | Fuzzy-complete args for any command (e.g. `nvim **<Tab>`) |

### 4.5 Neovim / LazyVim survival kit
Leader key is **Space**. You don't need to memorize much — press Space and a menu appears.
| Keys | Action |
|------|--------|
| `Space` (wait) | which-key menu pops up — your map for everything |
| `Space Space` | Find files |
| `Space /` | Search text across the project (grep) |
| `Space e` | Toggle file explorer |
| `Space g g` | lazygit inside nvim |
| `:q` / `:w` / `:wq` | quit / save / save+quit |
| `Space q q` | Quit all |
LazyVim docs: https://lazyvim.org — when in doubt, press Space and read.

---

## 5. How to change your setup (the part people forget)

**Golden rule:** edit the file in `~/.dotfiles`, never a copy. The symlinks mean your edit is live immediately (restart the shell / reload the app to see it).

### Add a shell alias
```bash
aliases                 # opens ~/.dotfiles/zsh/autocfg/aliases.zsh in nvim
# add:  alias gco="git checkout"
exec zsh                # reload shell
```

### Install a new tool and make it permanent
```bash
brew install <tool>                    # installs it now
# then add a line to your Brewfile so new machines get it too:
nvim ~/.dotfiles/config/brew/Brewfile  # add:  brew "<tool>"
```

### Change the terminal's look (theme, font, opacity)
```bash
nvim ~/.dotfiles/config/ghostty/config
# e.g. theme = "Argonaut",  font-size = 16,  background-opacity = 0.85
# Ghostty live-reloads most changes; or cmd+shift+, to reload.
```
Browse bundled themes in `~/.dotfiles/config/ghostty/themes/`.

### Save your changes (do this often!)
```bash
dots                    # jump to the repo
gs                      # see what changed
ga && git commit -m "tweak prompt"   # (add new files with git add <file>)
gP                      # push — now it's backed up forever
```

---

## 6. Moving to a new Mac (the 2-minute version)

```bash
# install.sh handles all of this, but manually it's:
xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
git clone https://github.com/jacobfrancisr1/dotfiles.git ~/.dotfiles
cd ~/.dotfiles && ./install.sh
```
Then open tmux (`` ` `` + `I`) and nvim once to pull plugins. Done — identical setup everywhere.

---

## 7. Troubleshooting

| Symptom | Fix |
|---------|-----|
| Shell looks plain, no colors/prompt | zinit/starship didn't load → `exec zsh`; check `~/.zshrc` symlink exists |
| `command not found` for a tool | It's not installed → `brew install <tool>` (and add to Brewfile) |
| Commit fails: "unable to sign" | See §3.3 — set `user.signingkey` or disable `gpgsign` |
| tmux keys do nothing | Wrong prefix — it's the **backtick** `` ` ``, not Ctrl-b |
| Ghostty config change not showing | Reload Ghostty, or fully quit & reopen |
| nvim throwing plugin errors | Run `:Lazy sync` inside nvim |
| `~/.config/x` is a real folder, not a symlink | Back it up and re-run install, or `ln -s ~/.dotfiles/config/x ~/.config/x` |
| Everything broke after an edit | `cd ~/.dotfiles && git diff` to see what you changed; `git checkout <file>` to revert |

**Check your symlinks are healthy:**
```bash
ls -la ~/.zshrc ~/.config/ghostty ~/.config/nvim ~/.config/tmux
# each should show  ->  ~/.dotfiles/...
```

---

## 8. Building the habit (so it actually sticks)

You said you go on-and-off with coding and forget the system. A few low-effort anchors:

1. **One launchpad command.** Open Ghostty → type `tmx` → `` ` `` + `S` to jump to any project. Make that your only entry point and the rest comes back fast.
2. **Commit your dotfiles weekly.** Any time you tweak something, `dots && ga && git commit -m "..." && gP`. Your config history becomes its own memory.
3. **When you forget a key, don't guess** — press tmux `` ` `` then `?` (lists binds), or Space in nvim (which-key menu), or open this file.
4. **Keep this README in the repo.** Drop it at `~/.dotfiles/README.md` (it's currently empty) so it travels with your setup. Then `cat ~/.dotfiles/README.md` is always your reference.

---

*Jacob's `jacobfrancisr1/dotfiles` (adapted from jfncs/dotfiles). Keep it in the repo so future-you finds it.*
