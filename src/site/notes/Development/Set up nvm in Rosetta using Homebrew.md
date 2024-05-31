---
{"dg-publish":true,"dg-path":"Set up nvm in Rosetta using Homebrew.md","permalink":"/set-up-nvm-in-rosetta-using-homebrew/"}
---


1. Add this to VSCode settings:

``` json
"x86 zsh 💻": {
    "path": "/usr/bin/arch",
    "args": ["-arch", "x86_64", "/bin/zsh"]
}
```

1. Use the x86 zsh to install Homebrew
2. Use `/usr/local/bin/brew` to install nvm
3. Set up *brew-x86* alias and nvm in `~/.zshrc`:

```shell
alias brew-x86="/usr/local/bin/brew"

export NVM_DIR="$HOME/.nvm"
[ -s "/usr/local/opt/nvm/nvm.sh" ] && \. "/usr/local/opt/nvm/nvm.sh"                                       # This loads nvm
[ -s "/usr/local/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/usr/local/opt/nvm/etc/bash_completion.d/nvm" # This loads nvm bash_completion

```

1. Reinstall *node_modules* in projects using the x86 terminal (using the ARM install of Yarn seems to be okay)

# Fish setup

1. Use `brew-x86` in ZSH to install fish
2. Add this to VSCode settings:

``` json
"x86 fish 🐟": {
    "path": "/usr/bin/arch",
    "args": ["-arch", "x86_64", "/usr/local/bin/fish"]
}
```

1. Run this in x86 fish to add the *brew-x86* alias:

```shell
alias -s brew-x86="/usr/local/bin/brew"
```

1. Follow steps in [[Development/Clipped/nvm on mac for fish users\|nvm on mac for fish users]] (but use `brew-x86`)
