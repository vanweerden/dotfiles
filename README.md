# dotfiles

Source: https://www.atlassian.com/git/tutorials/dotfiles

## Initial Setup
1) Prior to the installation make sure you have committed the alias to your .bashrc or .zsh
```Bash
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```
2) Ensure that your source repository ignores the folder where you'll clone it, so that you don't create weird recursion problems:
```Bash
echo ".cfg" >> .gitignore
```
3) Now clone your dotfiles into a bare repository in a "dot" folder of your $HOME:
```Bash
git clone --bare <git-repo-url> $HOME/.cfg
```
4) Define the alias in the current shell scope:
```Bash
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```
5) Checkout the actual content from the bare repository to your $HOME:
```Bash
config checkout
```
  * NB: You may need to remove any existing dotfiles
6) Set the flag showUntrackedFiles to no on this specific (local) repository
```Bash
config config --local status.showUntrackedFiles no
```

## Committing Changes
- Type `config` commands to add and update your dotfiles:
```Bash
config status
config add .vimrc
config commit -m "Add vimrc"
config add .bashrc
config commit -m "Add bashrc"
config push
```

## Setup script:
```Bash
git clone --bare https://bitbucket.org/durdn/cfg.git $HOME/.cfg
function config {
   /usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME $@
}
mkdir -p .config-backup
config checkout
if [ $? = 0 ]; then
  echo "Checked out config.";
  else
    echo "Backing up pre-existing dot files.";
    config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | xargs -I{} mv {} .config-backup/{}
fi;
config checkout
config config status.showUntrackedFiles no
```

