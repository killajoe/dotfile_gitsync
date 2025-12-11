# dotfile_gitsync

**sync your dotfile with github**

using *systemd --user* service/timer and a simple script to sync your dotfiles with github (or other git).

to avoid cleartext *token* in the script adding it to the user *systemd-service* file instead as environment variable.

This will update the code and create a tag for each run on the remote repo.


**scriptfile:**

`github-dotfiles` --> main script for handling sync to github

`github-tagclean` --> removes older tags

`copy_script` ---> to add your copy scriptlets for the dotfiles you want to sync

**systemd files:**

github-dotfile.service

github-dotfile.timer

1. create a new repo at your github git `reponame` and indeed, set it to private.

2. set it up locally:

```
git clone https://github.org/joekamprad/dotfile_gitsync.git
cd dotfile_gitsync
mkdir -p $HOME/.config/systemd/user/
cp -a github-dotfile.service github-dotfile.timer $HOME/.config/systemd/user/
mkdir $HOME/.dotfiles
cp -a github-dotfiles copy_script github-tagclean $HOME/.dotfiles/
cd $HOME/.dotfiles
```

**Configure SSH to use this key for GitHub:**

Generate an SSH key (if you don’t have one)

`ssh-keygen -t ed25519 -C "your_email-used-on-github" -f ~/.ssh/id_ed25519_github`

`cat ~/.ssh/id_ed25519_github.pub` and copy your key.

Edit ~/.ssh/config

```
Host github.com
    User git
    HostName github.com
    IdentityFile ~/.ssh/id_ed25519_github
    IdentitiesOnly yes
```

Make sure your local repo uses the SSH URL:

`git remote set-url origin git@github.com:username.reponame.git`


3. edit configs:

edit github-dotfiles to your needs:


`github-dotfile.timer`  --> set [Timer] to fit your needs default is:

```
OnStartupSec=30sec
OnCalendar=00/3:00
Persistent=true
```

`copy_script` --> edit to your needs for files you want to sync.

`github-tagclean` --> edit to add username and may change tags to keep.

4. enable timer to automate running the sync:

```
systemctl --user daemon-reload
systemctl --user enable --now github-dotfile.timer
```
