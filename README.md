# dotfiles

My Mac programming setup. Everything lives in `~/.dotfiles` and is symlinked into place — so I edit files **here**, and my tools update live.

## New machine

```bash
xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
git clone https://github.com/jacobfrancisr1/dotfiles.git ~/.dotfiles
# symlink configs, then:
brew bundle --file ~/.dotfiles/config/brew/Brewfile
```
Then open `nvim` (plugins auto-install) and start `tmux` (press `` ` `` + `I` to install its plugins).

## The tools

Ghostty (terminal) · zsh + starship (shell + prompt) · tmux (splits/sessions) · Neovim/LazyVim (editor) · fzf, zoxide, eza, bat, ripgrep, fd (modern CLI) · lazygit (git UI) · uv (Python).

## Daily git

```bash
gs        # status
ga        # add tracked changes
gP        # push
gg        # lazygit (visual git — use when stuck)
```
Commits are SSH-signed (shows "Verified" on GitHub). Identity lives in `~/.gitconfig-local`.

## Terminal cheat sheet

**tmux** — prefix is the backtick `` ` ``
- `Alt + arrows` — move between panes
- `` ` `` `S` — jump to any project (sesh)
- `` ` `` `g` — lazygit · `` ` `` `d` — lazydocker
- `` ` `` `%` / `"` — split panes

**zsh aliases**
- `l` / `ll` — listings · `cat` → bat · `cd` → zoxide (jump by name)
- `vim` → nvim · `gg` → lazygit · `y` → yazi file manager
- `dots` — jump to this repo · `brewup` — update everything

**Neovim** — leader is `Space`
- `Space Space` — find files · `Space /` — search text · `Space e` — file tree
- Press `Space` and wait — a menu shows everything

## Changing things

```bash
dots                       # cd into this repo
nvim config/ghostty/config # edit a config (or aliases.zsh, etc.)
ga && git commit -m "..." && gP   # save it — now it's backed up
```

Golden rule: edit the file in `~/.dotfiles`, never a copy. The symlinks make it live.
