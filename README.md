### Random Tips

#### Reset the very first commit in Git

Useful when you like to rewrite the very first command as `git reset HEAD~1` will not work.

```sh
# Revert to your very first commit
git update-ref -d HEAD

# Edit and fix the thinks to your liking
# ..
# If you have already created a repository in Github, you may like to push force
git push -f origin master
```

#### Copy the ssh key to Github (Linux)


- [Adding new ssh key to Github account](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/) for more detail
- [How to generate ssh key for Github](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

```sh
$sudo apt-get install xclip # For Debian base system
$sudo pacman -Sy xclip # For ArchLinux

# Downloads and installs xclip. If you don't have `apt-get`, you might need to use another installer (like `yum`)

$xclip -sel clip < ~/.ssh/id_rsa.pub
# Copies the contents of the id_rsa.pub file to your clipboard
```

If you install the key manually you may like to

```sh
# Might need to install the sshpass/openssh
sudo pacman -Sy openssh sshpass   # To get the ssh-agent
chmod 0644 $HOME/.ssh/id_rsa      # your personal private key
chmod 0644 $HOME/.ssh/id_rsa_work # your work private key (if any)

eval "$(ssh-agent -s)"

# Then perhap you should clear the `known_hosts` file if any
>known_hosts # will clear out the file content

# Then you can try to see if your ssh Github work
ssh -T git@github.com
```

#### Work with two Github's profiles from the same machine (think Work/Personal)

You will need to create the two SSH's keypairs one for work and one for personal use

```
## file: ~/.ssh/config
## Default config for personal work
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

## Default config for client's work
Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_work
```

Now when you need want to work on your personal projecg you can use normal command like:

```
# Git clone as usual
git clone git@github.com:agilecreativity/github-cloner.git

# Git clone public repository as usual
```

If you want to do the same for work, you will need to type something like

```
git clone git@github-work:work-org/some-cool-project.git

# Change to this project
cd some-cool-project

# And make sure you have the right configuration to your work's related email
git config user.email "me@my-work.com"
```

Now you can push/pull using your usual git workflow.

#### Arch Linux - working with `pacman` and `yaourt`

List packages you have installed

```sh
sudo pacman -Qm
yaourt -Qm
```
Very usefule when you like to know what you have already installed on two different computers.

And to install package via `yaourt` and skip the need to confirm the `do you wish to continue?`

```sh
# Q: Do you wish to continue with installation?
# A: Of course I do that why I am running this command!
yaourt google-chrome --noconfirm
```

#### The best way to write changes log for the project

Highly recommended take a look at [http://keepachangelog.com/](http://keepachangelog.com/).
