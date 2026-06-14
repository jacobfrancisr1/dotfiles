# dotfiles

My Mac programming setup: terminal (Ghostty), shell (zsh + starship), editor (Neovim), tmux, and CLI tools.

## How it works

Everything lives in this one repo at `~/.dotfiles`. Your home folder doesn't hold real config files — it holds **symlinks** that point into this repo. So:

- Edit a file in `~/.dotfiles` → your tool changes immediately.
- `commit` + `push` → it's backed up and ready to drop onto any other Mac.

**One rule:** always edit the file inside `~/.dotfiles`, never a copy somewhere else.

## Set up on a new Mac

Run these in order, top to bottom.

```bash
# 1. Apple's command-line build tools (git, compilers)
xcode-select --install

# 2. Homebrew (the package manager that installs everything else)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 3. Clone this repo
git clone https://github.com/jacobfrancisr1/dotfiles.git ~/.dotfiles

# 4. Link the configs into place (backs up anything already there)
mkdir -p ~/.config
ln -sf ~/.dotfiles/zsh/zshrc ~/.zshrc
ln -sf ~/.dotfiles/config/gitconfigs/.gitconfig ~/.gitconfig
ln -sf ~/.dotfiles/config/tmux/tmux.conf ~/.tmux.conf
for a in ghostty nvim tmux yazi aerospace sketchybar starship brew; do
  ln -sfn ~/.dotfiles/config/$a ~/.config/$a
done

# 5. Tell git who you are (kept out of the repo)
printf '[user]\n\tname = jacob\n\temail = 52079117+jacobfrancisr1@users.noreply.github.com\n' > ~/.gitconfig-local
git config --file ~/.gitconfig-local user.signingkey ~/.ssh/id_ed25519.pub

# 6. Install all the tools
brew bundle --file ~/.dotfiles/config/brew/Brewfile

# 7. Make zsh your shell, then restart the terminal
chsh -s $(which zsh)
```

**First launch:** open `nvim` once (LazyVim installs its plugins automatically), and start tmux with `tmx`, then press `` ` `` + `I` to install its plugins.

## Daily git workflow

```bash
gs                      # status — what changed
ga                      # stage your edits (tracked files)
git commit -m "message" # save a snapshot (auto-signed → "Verified" on GitHub)
gP                      # push to GitHub
gg                      # open lazygit — a visual git UI, best when you're unsure
```

Your name/email and signing key live in `~/.gitconfig-local`, so they never get committed.

## Terminal cheat sheet

**tmux** — the prefix key is the backtick `` ` ``. Press it, then the next key.

| Keys | Does |
|------|------|
| `Alt + arrows` | Move between split panes |
| `` ` `` then `S` | Jump to any project (this is your launchpad) |
| `` ` `` then `%` / `"` | Split pane right / down |
| `` ` `` then `g` | lazygit popup |
| `tmx` | Start/attach your main session |

**zsh** — handy aliases

| Type | Does |
|------|------|
| `l` / `ll` | Clean file listings |
| `cd <name>` | Jump to any folder by a piece of its name (zoxide) |
| `cat <file>` | View a file with syntax colors (bat) |
| `vim` | Opens Neovim |
| `dots` | Jump straight into this repo |
| `brewup` | Update all your installed tools |

**Neovim** — the leader key is `Space`. Press it and wait — a menu shows every command.

| Keys | Does |
|------|------|
| `Space Space` | Find a file |
| `Space /` | Search text across the project |
| `Space e` | Toggle the file explorer |
| `:w` / `:q` | Save / quit |

## Changing or adding something

```bash
dots                              # 1. go into the repo
nvim zsh/autocfg/aliases.zsh      # 2. edit a file (e.g. add an alias)
# 3. reload the shell to see it:
exec zsh
# 4. save it so it's backed up:
ga && git commit -m "add alias" && gP
```

To change the terminal's look, edit `config/ghostty/config` (theme, font, opacity). To install a new tool permanently, `brew install <tool>` then add a `brew "<tool>"` line to `config/brew/Brewfile`.
