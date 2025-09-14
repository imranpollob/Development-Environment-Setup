# Development Environment Setup

Opinionated, step-by-step instructions for setting up a full-stack development environment on Ubuntu or Linux Mint or Windows. 

> Tip: Perform a system update before installing new software to avoid most errors.

## Table of Contents
- [Development Environment Setup](#development-environment-setup)
  - [Table of Contents](#table-of-contents)
  - [Quick Start](#quick-start)
  - [Operating System Installation](#operating-system-installation)
  - [Git](#git)
  - [Node.js](#nodejs)
  - [Python](#python)
  - [Apache](#apache)
  - [MySQL](#mysql)
  - [PHP](#php)
  - [phpMyAdmin](#phpmyadmin)
  - [Composer](#composer)
  - [MongoDB](#mongodb)
  - [Zsh \& Shell Tools](#zsh--shell-tools)
  - [Useful Commands](#useful-commands)

## Quick Start
```bash
sudo apt update && sudo apt upgrade
```

## Operating System Installation
- Create a bootable USB with [Rufus](https://rufus.ie/downloads/).
- Linux-only install: follow [this guide](https://itsfoss.com/install-ubuntu/).
- Dual-boot Windows + Linux: follow [this guide](https://itsfoss.com/install-ubuntu-1404-dual-boot-mode-windows-8-81-uefi/).

## Git
```bash
sudo apt install -y git
git config --global user.name "Your Name"
git config --global user.username "your-username"
git config --global user.email "you@example.com"
```

- Generate SSH keys for GitHub: follow the official guide: https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/

## Node.js
Recommended: install via Node Version Manager ([nvm](https://github.com/nvm-sh/nvm#install--update-script))
```bash
nvm install --lts
nvm use --lts
```

Alternative: install standalone Node.js 20 via NodeSource
```bash
curl -sL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

## Python
Recommended: install via [pyenv](https://github.com/pyenv/pyenv#automatic-installer), then [set up your shell](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv) and [build deps](https://github.com/pyenv/pyenv/wiki#suggested-build-environment).
```bash
pyenv install --list   # discover versions
pyenv install <PYTHON_VERSION>
pyenv global <PYTHON_VERSION>
```

Alternative: standalone install (version-specific instructions): https://www.debugpoint.com/install-python-3-12-ubuntu/

## Apache
```bash
sudo apt install -y apache2
sudo systemctl stop apache2.service
sudo systemctl start apache2.service
sudo systemctl enable apache2.service
```

## MySQL
```bash
sudo apt install -y mysql-server mysql-client
sudo systemctl stop mysql.service
sudo systemctl start mysql.service
sudo systemctl enable mysql.service
```

Password hardening and root access:
```bash
sudo mysql_secure_installation
```
- When prompted, answer the questions to set a strong root password and remove insecure defaults.
- If your install uses a temporary root password, you may find it here:
```bash
sudo grep 'temporary password' /var/log/mysqld.log || true
```
- Login and change the root password if needed:
```bash
mysql -u root -p
# then inside MySQL shell:
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```
- Alternate reset method: https://linuxconfig.org/how-to-reset-root-mysql-password-on-ubuntu-18-04-bionic-beaver-linux

Restart MySQL when required:
```bash
sudo systemctl restart mysql.service
```

## PHP
Example install (specific version packages shown):
```bash
sudo apt install -y \
  php7.2 libapache2-mod-php php7.2-common php7.2-mbstring php7.2-xmlrpc \
  php7.2-soap php7.2-gd php7.2-xml php7.2-mysql php7.2-cli php7.2-zip
```

- If you see "Unable to locate package", add the community PPA and try again:
```bash
sudo apt-add-repository ppa:ondrej/php
sudo apt update
```
- Switching between PHP versions: https://tecadmin.net/switch-between-multiple-php-version-on-ubuntu/

## phpMyAdmin
```bash
sudo apt install -y phpmyadmin
```
- Alternative: [Adminer](https://www.adminer.org/#download)

## Composer
```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

## MongoDB
Follow the official guide: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition-using-deb-packages

## Zsh & Shell Tools
Install Zsh and Oh My Zsh:
```bash
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# If the shell didn't switch, run and then restart:
chsh -s "$(which zsh)"
```

Popular Zsh plugins/themes:
- zsh-autosuggestions: https://github.com/zsh-users/zsh-autosuggestions#installation
- zsh-syntax-highlighting: https://github.com/zsh-users/zsh-syntax-highlighting#installation
- powerlevel10k: https://github.com/romkatv/powerlevel10k#installation

Other handy CLI tools:
- bat: https://github.com/sharkdp/bat#installation
- fzf: https://github.com/junegunn/fzf#installation

## Useful Commands
```bash
# Reboot immediately
sudo shutdown -r 0

# Reload Bash config
source ~/.bashrc

# Reload Zsh config
source ~/.zshrc

# Switch to Bash / Zsh
exec bash
exec zsh
```
