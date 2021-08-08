---
tags: mac pyenv brew
toc: True
---


# brew
> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

> echo 'export PATH=/opt/homebrew/bin:$PATH' >> ~/.zshrc

> source .zshrc

# pyenv
> brew install pyenv

> pyenv install 3.8.0

> echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc

# jupyter notebook
> pip3 install --upgrade pip3

> pip3 install jupyter

> jupyter notebook
