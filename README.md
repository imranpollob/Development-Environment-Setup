# Development Environment Setup

Opinionated, step-by-step instructions for setting up a full-stack development environment on Ubuntu, Linux Mint, or Windows.

> Tip: Perform a system update before installing new software to avoid most errors.

## Table of Contents
- [Development Environment Setup](#development-environment-setup)
  - [Table of Contents](#table-of-contents)
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
  - [Custom Aliases (PowerShell \& zsh)](#custom-aliases-powershell--zsh)
    - [Windows — PowerShell](#windows--powershell)
    - [zsh (Linux/macOS)](#zsh-linuxmacos)
  - [Accept only next word with Right Arrow](#accept-only-next-word-with-right-arrow)
    - [Mac iterm](#mac-iterm)
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
- Install Git for Windows: `winget install --id Git.Git -e` (or Chocolatey: `choco install git`)
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
  - Prompt/theme: use [Oh My Posh](https://ohmyposh.dev) for a modern, cross-shell prompt theme engine.
  - Cross-shell prompt: https://starship.rs/
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
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
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

Alternative (Linux): use OS packages or a trusted PPA (e.g., deadsnakes for older releases). For Ubuntu 24.04+, Python 3.12+ is in the official repo.

Windows:
- Recommended: `winget install --id Python.Python.3.12 -e` (or latest 3.x)
- Alternative: pyenv-win: https://github.com/pyenv-win/pyenv-win

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

Secure installation and root access:
```bash
sudo mysql_secure_installation
```
- When prompted, answer the questions to set a strong root password and remove insecure defaults.
- On Ubuntu, the `root` MySQL user may authenticate via `auth_socket`. To set a password:
```bash
sudo mysql
-- inside MySQL shell:
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'MyNewPass4!';
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
Recommended (Ubuntu): use supported PHP 8.x packages
```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y \
  php8.2 php8.2-cli php8.2-fpm php8.2-mysql php8.2-xml php8.2-curl php8.2-zip php8.2-mbstring \
  libapache2-mod-php8.2
# Enable PHP with Apache (mod_php):
sudo a2enmod php8.2 && sudo systemctl restart apache2
```
- To use the latest stable, replace `8.2` with `8.3` if available for your distro.
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

## Composer
```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

## MongoDB
Follow the official guide: https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/

Windows:
- Install MongoDB Community Server: `winget install --id MongoDB.MongoDBServer -e`


## Custom Aliases (PowerShell & zsh)
Create a short alias as a shell function (example uses `slugcopy` with `s`).

Install `slugcopy` globally first:
```bash
npm i -g slugcopy
# usage: 
slugcopy "A nice house"  
# a-nice-house (copied is copied to clipboard)
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

My customs shortcuts:
```powershell
Invoke-Expression (&starship init powershell)

# Set RightArrow key as the keybinding for accepting the next word in the suggestion (ForwardWord)
Set-PSReadLineKeyHandler -Chord "RightArrow" -Function ForwardWord

# Slugcopy shortcut -> s
function s {
    param(
        [Parameter(ValueFromRemainingArguments = $true)]
        $args
    )
    slugcopy @args
}


# Delete a folder
function rmm {
    param([Parameter(ValueFromRemainingArguments = $true)][string[]]$Args)

    foreach ($t in $Args) {
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

My customs shortcuts:
```bash
# shortcuts
alias ggl='git pull'
alias ggp='git push'
alias gst='git status'

# git clone and go cloned to the clone directory
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

## Accept only next word with Right Arrow
Speed up inline suggestions (PSReadLine) by mapping RightArrow to accept only the next suggested word (instead of the whole line):


Open (or create) your profile file:
```bash
code $PROFILE
```
Add this line (add only once):
```bash
Set-PSReadLineKeyHandler -Chord "RightArrow" -Function ForwardWord
```
Save, then reload:
```bash
. $PROFILE
```

### Mac iterm

Follow this guide here: https://stackoverflow.com/a/22312856/2369656


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
