# Development Environment Setup

Opinionated, step-by-step instructions for setting up a full-stack development environment on Ubuntu, Linux Mint, or Windows. The shell customizations also cover macOS (zsh).

> Tip: Perform a system update before installing new software to avoid most errors.
>
> Last verified: October 2026. Versions below (Node.js, PHP, Python) move quickly; check the linked official sources if a command fails.

## Table of Contents
- [Quick Start](#quick-start)
- [Operating System Installation](#operating-system-installation)
  - [Mint Related Notes](#mint-related-notes)
- [Git](#git)
- [Zsh \& Shell Tools](#zsh--shell-tools)
- [Node.js](#nodejs)
- [Python](#python)
- [Apache](#apache)
- [MySQL](#mysql)
- [PHP](#php)
- [phpMyAdmin](#phpmyadmin)
- [Composer](#composer)
- [MongoDB](#mongodb)
- [Foundry](#foundry)
- [Custom Aliases (PowerShell \& zsh)](#custom-aliases-powershell--zsh)
  - [Windows — PowerShell](#windows--powershell)
  - [zsh (Linux/macOS)](#zsh-linuxmacos)
  - [macOS iTerm2](#macos-iterm2)
- [Useful Commands](#useful-commands)
- [Windows Notes](#windows-notes)

## Quick Start
```bash
sudo apt update && sudo apt upgrade
```

## Operating System Installation
- Create a bootable USB with [Rufus](https://rufus.ie/downloads/).
- Linux-only install: follow [this guide](https://itsfoss.com/install-ubuntu/).
- Dual-boot Windows + Linux: follow [this guide](https://itsfoss.com/install-ubuntu-1404-dual-boot-mode-windows-8-81-uefi/).


### Mint Related Notes
To duplicate the taskbar apps to all monitors:
- Right-click the taskbar, select "Add a new panel".
- Click "Applets"
- Open setting for "Grouped Window List"
- Set "Show windows from other monitors" to "From all monitors"
- You need to do this for each panel.

## Git
```bash
sudo apt install -y git
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

- Generate SSH keys for GitHub: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

Windows:
- Install Git for Windows: `winget install --id Git.Git -e` (or Chocolatey: `choco install git`). This also provides Git Bash.
- Configure the same `user.name` and `user.email` as above.
- Git Credential Manager is included; use `git credential-manager configure` if needed.

## Zsh & Shell Tools
Install Zsh and Oh My Zsh:
```bash
sudo apt install -y zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# If the shell didn't switch, run and then restart:
chsh -s "$(which zsh)"

# After that, log out and log back in, or restart your system to apply the change
```

Popular Zsh plugins/themes:
- zsh-autosuggestions: https://github.com/zsh-users/zsh-autosuggestions#installation
- zsh-syntax-highlighting: https://github.com/zsh-users/zsh-syntax-highlighting?tab=readme-ov-file#how-to-install
- powerlevel10k: https://github.com/romkatv/powerlevel10k#installation

Other handy CLI tools:
- bat: https://github.com/sharkdp/bat#installation
- fzf: https://github.com/junegunn/fzf#installation

Windows:
- Best experience: use WSL2 and follow the Linux steps inside your distro.
- Native PowerShell alternatives:
  - Prompt/theme: use [Oh My Posh](https://ohmyposh.dev/docs/installation/windows) for a modern, cross-shell prompt theme engine: `winget install JanDeDobbeleer.OhMyPosh --source winget`
  - fzf: `winget install junegunn.fzf` (or `choco install fzf`)
- bat: `winget install sharkdp.bat` (binary is `bat`)


## Node.js
Recommended: install via Node Version Manager ([nvm](https://github.com/nvm-sh/nvm#install--update-script))
```bash
nvm install --lts
nvm use --lts
```

Alternative (Linux): install standalone Node.js LTS via NodeSource
```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
```

Windows:
- Recommended: nvm-windows (Corey Butler): `winget install --id CoreyButler.NVMforWindows -e`
  - Then in a new shell: `nvm install lts` and `nvm use lts`
- Alternative: install Node LTS directly: `winget install --id OpenJS.NodeJS.LTS -e`

## Python
Recommended: install via [pyenv](https://github.com/pyenv/pyenv#automatic-installer), then [set up your shell](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv) and [build deps](https://github.com/pyenv/pyenv/wiki#suggested-build-environment).
```bash
pyenv install --list   # discover versions
pyenv install <PYTHON_VERSION>
pyenv global <PYTHON_VERSION>
```

Alternative (Linux): use OS packages or a trusted PPA (e.g., deadsnakes for versions not in your release's repo). Ubuntu 24.04 ships Python 3.12.

Windows:
- Recommended: `winget install --id Python.Python.3.14 -e` (or another supported 3.x, e.g. `Python.Python.3.13`)
- Alternative: pyenv-win: https://github.com/pyenv-win/pyenv-win

## Apache
```bash
sudo apt install -y apache2
sudo systemctl stop apache2.service
sudo systemctl start apache2.service
sudo systemctl enable apache2.service
```

Windows:
- Use a bundle such as [XAMPP](https://www.apachefriends.org/) or WAMP, or run Apache inside WSL2 with the steps above.

## MySQL
```bash
sudo apt install -y mysql-server mysql-client
sudo systemctl stop mysql.service
sudo systemctl start mysql.service
sudo systemctl enable mysql.service
```

Secure installation and root access:
```bash
sudo mysql_secure_installation
```
- When prompted, answer the questions to set a strong root password and remove insecure defaults.
- On Ubuntu, the `root` MySQL user may authenticate via `auth_socket`. To set a password:
```bash
sudo mysql
-- inside MySQL shell:
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '<your-strong-password>';
FLUSH PRIVILEGES;
```

Restart MySQL when required:
```bash
sudo systemctl restart mysql.service
```

Windows:
- Install MySQL Community Server: `winget install --id Oracle.MySQL -e` (or Chocolatey: `choco install mysql`)
- Alternatively use MariaDB: `winget install --id MariaDB.Server -e`

## PHP
Recommended (Ubuntu LTS): use a currently supported PHP version from the [ondrej/php PPA](https://launchpad.net/~ondrej/+archive/ubuntu/php) (supports Ubuntu LTS releases only)
```bash
PHPV=8.4   # or 8.5 (latest); see https://www.php.net/supported-versions.php
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y \
  php$PHPV php$PHPV-cli php$PHPV-fpm php$PHPV-mysql php$PHPV-xml php$PHPV-curl php$PHPV-zip php$PHPV-mbstring \
  libapache2-mod-php$PHPV
# Enable PHP with Apache (mod_php):
sudo a2enmod php$PHPV && sudo systemctl restart apache2
```
- Ubuntu 26.04 already ships PHP 8.5 in its default repositories, so the PPA is optional there.
- Switching between versions: https://tecadmin.net/switch-between-multiple-php-version-on-ubuntu/

Windows:
- Simplest: use a bundle (XAMPP/WAMP) that includes Apache, PHP, and MySQL.
- Native packages: `choco install php` or `winget install --id PHP.PHP -e` (ensure PHP is on PATH)

## phpMyAdmin
```bash
sudo apt install -y phpmyadmin
```
- Alternative: [Adminer](https://www.adminer.org/#download)

Note: Availability via `apt` can vary by Ubuntu release. If not found, see https://www.phpmyadmin.net/downloads/ for manual install steps.

Windows:
- Included in XAMPP/WAMP, or download it from https://www.phpmyadmin.net/downloads/ (or use Adminer).

## Composer
Install with the official, hash-verified installer (see [getcomposer.org/download](https://getcomposer.org/download/) for the current snippet):
```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
HASH="$(curl -sS https://composer.github.io/installer.sig)"
php -r "if (hash_file('sha384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); exit(1); }"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
rm composer-setup.php
```

Windows:
- Download and run `Composer-Setup.exe` from https://getcomposer.org/download/ (PHP must already be installed).

## MongoDB
Follow the official guide: https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/

Windows:
- Install MongoDB Community Server: `winget install --id MongoDB.MongoDBServer -e`

## Foundry

```bash
curl -L https://getfoundry.sh/install | bash

# The installer only installs `foundryup`; it does not edit your shell config.
# Add it to PATH for this session and persist it (use ~/.zshrc if using zsh):
export PATH="$PATH:$HOME/.foundry/bin"
echo 'export PATH="$PATH:$HOME/.foundry/bin"' >> ~/.bashrc

# Install the toolchain
foundryup
```

This installs `forge`, `cast`, `anvil`, and `chisel` commands. See the [official installation guide](https://www.getfoundry.sh/introduction/installation).

Windows:
- `foundryup` requires **Git Bash** or **WSL2**. PowerShell and Command Prompt are not supported, so run the commands above inside one of those shells.

## Custom Aliases (PowerShell & zsh)
Create a short alias as a shell function (example uses `slugcopy` with `s`).

Install `slugcopy` globally first:
```bash
npm i -g slugcopy
# usage:
slugcopy "A nice house"
# a-nice-house (also copied to the clipboard)
```

### Windows — PowerShell
Find your profile file path:
```powershell
$PROFILE
```

Create it if it doesn’t exist:
```powershell
New-Item -ItemType File -Path $PROFILE -Force
```

Open the profile for editing:
```powershell
code $PROFILE
```

My custom shortcuts:
```powershell
# Set RightArrow key as the keybinding for accepting the next word in the suggestion (ForwardWord)
Set-PSReadLineKeyHandler -Chord "RightArrow" -Function ForwardWord

# Slugcopy shortcut -> s
function s {
    slugcopy @args
}

# Delete folders (forced, recursive, long-path safe)
function rmm {
    param([Parameter(ValueFromRemainingArguments = $true)][string[]]$Paths)

    foreach ($t in $Paths) {
        try {
            $resolved = Resolve-Path -LiteralPath $t -ErrorAction Stop
            $lp = "\\?\$($resolved.Path)"

            # Clear hidden/system/read-only attributes first
            attrib -r -s -h $lp /S /D 2>$null

            # Force remove, recurse, long-path safe
            Remove-Item -LiteralPath $lp -Recurse -Force -ErrorAction Stop
        } catch {
            Write-Error $_
        }
    }
}

# -----------------------
# Git Shortcuts (Functions)
# -----------------------

function ggl {
    git pull @Args
}

function ggp {
    git push @Args
}

function gst {
    git status @Args
}

# NOTE: `gc` (Get-Content) and `gcm` (Get-Command) are built-in PowerShell aliases,
# and aliases take precedence over functions, so remove them before redefining.
Remove-Item Alias:gc -Force -ErrorAction SilentlyContinue
Remove-Item Alias:gcm -Force -ErrorAction SilentlyContinue

function gcm {
    git checkout master
}

# Git clone and cd into the cloned directory
function gc {
    param([Parameter(Mandatory = $true, Position = 0)][string]$Url)

    git clone $Url
    if ($LASTEXITCODE -ne 0) { return }
    Set-Location ([System.IO.Path]::GetFileNameWithoutExtension($Url.TrimEnd('/')))
}


# -----------------------
# Add, commit, push
# Usage: gcam "commit message"
# -----------------------
function gcam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true, Position=0)]
        [string]$Message
    )

    # Ensure we're in a git repo by checking for the top-level directory
    $null = git rev-parse --show-toplevel 2>$null
    if ($LASTEXITCODE -ne 0) {
        Write-Error "Not inside a git repository."
        return
    }

    # Get the current branch name
    $branch = (git rev-parse --abbrev-ref HEAD).Trim()

    # Stage all changes
    git add .

    # Check if there are any staged changes; if not, exit gracefully
    $status = (git status --porcelain).Trim()
    if ([string]::IsNullOrWhiteSpace($status)) {
        Write-Host "No changes to commit."
        return
    }

    # Commit with the provided message
    git commit -m "$Message"
    if ($LASTEXITCODE -ne 0) { return } # Exit if commit fails

    # Push to the origin remote on the current branch
    git push origin $branch
}
```

Reload the profile (or restart PowerShell):
```powershell
. $PROFILE
```

**Notes**

If your profile doesn’t load due to policy, allow local scripts:
```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force
```

### zsh (Linux/macOS)
macOS uses zsh by default (Catalina and later). On Linux, install and switch to zsh first (see section above).


Open the profile for editing:
```bash
nano ~/.zshrc
```

To disable existing git aliases change the plugin git to gitfast.
```bash
plugins=(gitfast)
```

My custom shortcuts:
```bash
# shortcuts
alias ggl='git pull'
alias ggp='git push'
alias gst='git status'

# git clone and cd into the cloned directory
gc() {
 git clone "$1" && cd "$(basename "$1" .git)"
}

# git add, commit and push
gcam() {
  if [ -z "$1" ]; then
    echo "❌ Please provide a commit message."
    echo "Usage: gcam \"your commit message\""
    return 1
  fi

  branch=$(git rev-parse --abbrev-ref HEAD)
  git add .
  git commit -m "$1"
  git push origin "$branch"
}

# Short alias for slugcopy
s() { slugcopy "$@"; }
```

Reload shell config to apply changes:
```bash
source ~/.zshrc
```

### macOS iTerm2
To accept only the next suggested word with the Right Arrow key (as in the PowerShell profile), follow [this guide](https://stackoverflow.com/a/22312856/2369656).

## Useful Commands
```bash
# Reboot immediately
sudo reboot

# Reload Bash config
source ~/.bashrc

# Reload Zsh config
source ~/.zshrc

# Switch to Bash / Zsh
exec bash
exec zsh
```

## Windows Notes
- Prefer WSL2 for a Linux-like development environment on Windows; install with `wsl --install`, then choose Ubuntu.
- Package managers: this guide shows `winget` first; Chocolatey (`choco`) and Scoop are good alternatives.
- After installing CLI tools with winget/Chocolatey, restart the terminal to refresh PATH.
