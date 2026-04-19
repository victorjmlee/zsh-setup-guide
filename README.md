# Replicating Your Zsh Setup on a New MacBook

## Step 1: Verify zsh is your default shell

macOS ships with zsh as the default shell since Catalina. Confirm by running:

```sh
echo $SHELL
```

You should see `/bin/zsh`. If not, set it:

```sh
chsh -s /bin/zsh
```

---

## Step 2: Install Homebrew

Homebrew is needed to install your shell plugins.

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

After installation, follow the on-screen instructions to add Homebrew to your PATH. Typically:

```sh
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Verify:

```sh
brew --version
```

---

## Step 3: Install Oh My Zsh

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

This creates `~/.oh-my-zsh/` and generates a default `~/.zshrc`. You'll replace its contents in a later step.

---

## Step 4: Install zsh plugins via Homebrew

```sh
brew install zsh-syntax-highlighting zsh-autosuggestions
```

---


## Step 5: Replace your `.zshrc`

Open the file:

```sh
nano ~/.zshrc
```

Replace its entire contents with the following:

```sh
# Path to your Oh My Zsh installation.
export ZSH="$HOME/.oh-my-zsh"

# Theme
ZSH_THEME="agnoster"

# Plugins
plugins=(git)

source $ZSH/oh-my-zsh.sh

# Custom prompt: show username with emoji, add newline before cursor
prompt_context() {
  # Custom (emoji)
  prompt_segment black default "victor.lee 🐉"
}
build_prompt() {
  RETVAL=$?
  prompt_status
  prompt_virtualenv
  prompt_aws
  prompt_context
  prompt_dir
  prompt_git
  prompt_bzr
  prompt_hg
  prompt_newline
  prompt_end
}
prompt_newline() {
  if [[ -n $CURRENT_BG ]]; then
    echo -n "%{%k%F{$CURRENT_BG}%}$SEGMENT_SEPARATOR
%{%k%F{blue}%}$SEGMENT_SEPARATOR"
  else
    echo -n "%{%k%}"
  fi

  echo -n "%{%f%}"
  CURRENT_BG=''
}

# Shell enhancements
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh

```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X` in nano).

---

## Step 6: Apply the configuration

```sh
source ~/.zshrc
```

---

## Verification Checklist

| Check | Command |
|---|---|
| Oh My Zsh loaded | `echo $ZSH` should print `~/.oh-my-zsh` |
| Agnoster theme active | Prompt should show colored segments with your name and dragon emoji |
| Newline in prompt | Cursor should appear on a line below the prompt segments |
| Syntax highlighting | Type a valid command — it should turn green; invalid turns red |
| Autosuggestions | Start typing a previously run command — a gray suggestion should appear |
| Git plugin | `cd` into a git repo — branch name should appear in the prompt |
| pipx PATH | `echo $PATH` should include `.local/bin` |

---

## Notes

- If the SCFW block gets re-managed by your org's tooling on the new machine, it may overwrite those lines automatically — that's fine.
- The `pipx` PATH uses a hardcoded username (`victor.lee`). If your username differs on the new machine, change it to match, or use `$HOME/.local/bin` instead.
