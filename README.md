### Random Tips

#### Fix the Arch Linux keys not valid

```sh
# Try this command
sudo pacman -S archlinux-keyring

# Then
sudo pacman -Syyu

# Or as usual
yaourt -Syyu
```
#### Install Vim with Lua support on Mac OSX

```sh
brew instal vim  \
--with-client-server \
--with-gettext \
--with-lua \
--with-luajit \
--with-override-system-vi \
--HEAD
```

This will give you the `+lua` support which is more awesome!

I am provision this through my Ansible playbook with this role (using Homebrew):

```yml
---
## file: roles/common/tasks/editors.yml
## ...
##- name: Install Vim from source
- name: Install latest version of Vim from source with Lua support
  command: brew instal vim --with-client-server --with-gettext --with-lua --with-luajit --with-override-system-vi --HEAD
  args:
    creates: /usr/local/bin/vim
  tags: editors
## ...
```

If you things go well for you then you should see something like this when type `:version` from Vim

```
:version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled May 18 2017 22:57:51)
MacOS X (unix) version
Included patches: 1-600
Compiled by Homebrew
Huge version without GUI.  Features included (+) or not (-):
+acl             +comments        +extra_search    +keymap          +mouse_dec       +path_extra      +smartindent     +title           +xfontset
+arabic          +conceal         +farsi           +lambda          -mouse_gpm       +perl            +startuptime     -toolbar         -xim
+autocmd         +cryptv          +file_in_path    +langmap         -mouse_jsbterm   +persistent_undo +statusline      +user_commands   -xpm
-balloon_eval    +cscope          +find_in_path    +libcall         +mouse_netterm   +postscript      -sun_workshop    +vertsplit       +xsmp_interact
-browse          +cursorbind      +float           +linebreak       +mouse_sgr       +printer         +syntax          +virtualedit     +xterm_clipboard
++builtin_terms  +cursorshape     +folding         +lispindent      -mouse_sysmouse  +profile         +tag_binary      +visual          -xterm_save
+byte_offset     +dialog_con      -footer          +listcmds        +mouse_urxvt     +python          +tag_old_static  +visualextra
+channel         +diff            +fork()          +localmap        +mouse_xterm     -python3         -tag_any_white   +viminfo
+cindent         +digraphs        +gettext         +lua             +multi_byte      +quickfix        -tcl             +vreplace
+clientserver    -dnd             -hangul_input    +menu            +multi_lang      +reltime         +termguicolors   +wildignore
+clipboard       -ebcdic          +iconv           +mksession       -mzscheme        +rightleft       +terminfo        +wildmenu
+cmdline_compl   +emacs_tags      +insert_expand   +modify_fname    +netbeans_intg   +ruby            +termresponse    +windows
+cmdline_hist    +eval            +job             +mouse           +num64           +scrollbind      +textobjects     +writebackup
+cmdline_info    +ex_extra        +jumplist        -mouseshape      +packages        +signs           +timers          +X11
   system vimrc file: "$VIM/vimrc"
     user vimrc file: "$HOME/.vimrc"
 2nd user vimrc file: "~/.vim/vimrc"
      user exrc file: "$HOME/.exrc"
       defaults file: "$VIMRUNTIME/defaults.vim"
  fall-back for $VIM: "/usr/local/share/vim"
Compilation: clang -c -I. -Iproto -DHAVE_CONFIG_H   -DMACOS_X_UNIX  -g -O2 -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=1
Linking: clang   -L. -fstack-protector -L/usr/local/lib -L/usr/local/opt/libyaml/lib -L/usr/local/opt/openssl/lib -L/usr/local/opt/readline/lib  -L/usr/loca
l/lib -o vim    -lXt -lX11 -lSM -lICE  -lncurses -liconv -lintl -framework Cocoa  -pagezero_size 10000 -image_base 100000000 -L/usr/local/lib -lluajit-5.1 -
mmacosx-version-min=10.12 -fstack-protector-strong -L/usr/local/lib  -L/usr/local/Cellar/perl/5.24.1/lib/perl5/5.24.1/darwin-thread-multi-2level/CORE -lperl
 -lm -lutil -lc -F/usr/local/opt/python/Frameworks -framework Python   -lruby.2.4.1 -lobjc
```

#### How to bring up your Github branch when your pull request (PR) is accepted.

To bring your local/remote branch to the same level as the upstream branch you can do the
following.

I recentely contributed to [webica](https://github.com/tmarble/webica/commit/42895b7118fe403a7fdc538f3c0f7f73fc18a82c) and like my local branch to be the same level as
the upstream version. All I have to do is the following:

```sh
# First you will need to tracking the branch if not already done
git remote add upstream git@github.com:tmarble/webica.git
```

From the base directory of your current project you should be able to see something like

```sh
git remote -v
origin	git@github.com:agilecreativity/webica.git (push)
upstream	git@github.com:tmarble/webica.git (fetch)
```

Now you want to fetch the upstream change first

```sh
git fetch upstream master
```

Then to bring your local branch to the level of your upstream branch

```sh
git pull --rebase upstream master
```

At this point you should be able to push the change to your remote branch (origin)

```sh
# This is your own branch
git push origin master
```

Your Github repository should now be at the same level as your upstream branch.

#### Default parameter in Linux shell script

Sometime it is useful to be able to use sensible default for the user.
e.g. In the following shell script we are trying to set the default `RAILS_ENV`
to `develop` if the user omit to specify one.

```bash
#!/bin/bash
## Script name: drop-create-migrate-seed-spec
## Typical script in Ruby/Rails project to setup and quickly test the code
RAILS_ENV=${RAILS_ENV:=development}
echo "Using RAILS_ENV=$RAILS_ENV"
RAILS_ENV=$RAILS_ENV bundle install
RAILS_ENV=$RAILS_ENV bundle exec rake db:drop
RAILS_ENV=$RAILS_ENV bundle exec rake db:create
RAILS_ENV=$RAILS_ENV bundle exec rake db:migrate
RAILS_ENV=$RAILS_ENV bundle exec rake db:seed
RAILS_ENV=$RAILS_ENV bundle exec rake spec
```

Note if we like to run this script with different environment then we can do so
using:

```sh
export RAILS_ENV=test && ./drop-create-migrate-seed-spec
```

#### Format your USB drive on Linux base system

WARNING: PLEASE TAKE EXTRA CARE AS THIS COULD BE DESTRUCTIVE!

```sh
# 1) determine the driver for your usb
sudo dmesg | tail

# Or use lsblk which should give you the drive information
sudo lsblk

## 2) Umount your USB driver (from the first step)
sudo umount /dev/sdb1

## 3) Then reformat your USB to Fat32 (BE EXTRA CAREFUL HERE)
sudo mkdosfs -n 'USB_LABEL' -F 32 -I /dev/sdb1

## or if you just want FAT instead of Fat32 then omit the `-F 32`
sudo mkdosfs -n 'USB_LABEL' -I /dev/sdb

## 3) For EXT3
sudo mkfs.ext3 -n 'LABEL' -I /dev/sdb
```

And that should be it

#### Checkout the pull request commit from Github

```sh
git config --add remote.origin.fetch +refs/pull/*/head:refs/remotes/origin/pull/*
git fetch origin
#git describe --all  --contains <COMMIT>
```

#### To compare the content of two `.tar.gzip` file

```sh
#!/usr/bin/env bash
file1=$1
file2=$2
# List the content of the file and find the differeces between them
diff <(tar -tvf $file1.tar.gz | sort) <(tar -tvf $file2.tar.gz | sort)
```

#### Add sample test commit for testing with CI/CD build trigger jobs

```sh
#!/usr/bin/env bash
## file: add-test-commit
## Add test commit to any projects
echo `date` >> test-commit.txt && git add --all && git commit -am "Test commit at `date`" && git push
```

#### Sample Jenkins script

I am having fun with Jenkins script in Groovy recently.

Here is the example of script that make a call to shell command to get the
branch name from a given commit hash

```groovy
stage "preparation"

node() {
    stage('demo-stage') {
        test_env("demo")
        echo "FYI: may be you see: ${test_env_number}"
        dir("filename_cleaner") {
            // Note:t this cleanup the directory so that we can clone new repository successfully
            deleteDir()

            sh "git clone https://github.com/agilecreativity/filename_cleaner.git ."
            sh "pwd"
            sh "ls -alt"

            // sample commit hash
            def commit_hash = "fc975c412178fb1363df1644f70b158ddcd77b8a"

            // Note: we always want to trim of the new line at the end
            // find the branch name from a given commit hash
            def result = exec_sh("git branch --contains $commit_hash | awk '{print \$2}'").trim()

            echo "Your result: ${result}" // this print 'master'

            // This is how you could use the result in your logic
            if (result == "master") {
                echo "Yes you are right master!!"
            } else {
                echo "I don't know you!"
            }
        }
    }
}

def test_env(rals_env="build_admin") {
    withEnv(["TEST_ENV_NUMBER=${test_env_number}"]) {
        echo "Your path: ${env.PATH}"
        echo "Your TEST_ENV_NUMBER= ${env.TEST_ENV_NUMBER}"
    }
}

def exec_sh(script_name) {
  def result = sh(returnStdout: true,
                  script: script_name)
  return result
}
```
#### Install PostgreSQL on Arch Linux

- https://wiki.archlinux.org/index.php/PostgreSQL

```
sudo -u postgres -i
```

Then

```
[postgres]$ initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'
```

Which should gives something like

```
$initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'

The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgres/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /var/lib/postgres/data -l logfile start
```

- You can then check if the database is start properly using

```
## restart the Postgres service
sudo systemctl restart postgresql.service

## Start the service at login
sudo systemctl enable postgresql.service

## Check if everything is running properly
sudo systemctl status postgresql.service
```

#### Install postgreSQL on Fedora 24

```
sudo dnf install postgresql-server
sudo postgresql-setup --initdb

# start one time only
sudo systemctrl start postgresql

# start everytime we reboot
sudo systemctrl enable postgresql
```

Change the password for user postgres

```
sudo su - postgres
#$psql
#postgres=#\password postgres
# enter the password for user postgres
```

Create a user and a database

```sh
$createuser john -P
$createdb --owner=john sample_db
```

If you already have the existing user you like to use just substitute accordingly

```
$createdb --owner=bchoomnuan sample_db
```

Edit the `/var/lib/pgsql/data/pg_hba.conf`

```
#TYPE DATABASE USER ADDRESS METHOD
host  all  all 127.0.0.1/32 md5
host  all  all ::1/128      md5
local all  all postgres     peer
```

Or just keep it simple as we are running locally

```
#TYPE DATABASE USER ADDRESS METHOD
host  all  all 127.0.0.1/32 trust
host  all  all ::1/128      trust
local all  all postgres     trust
```

Restart the service

```
$sudo systemctl enable postgresql.service
# systemctl\Created symlink from /etc/systemd/system/multi-user.target.wants/postgresql.service to /usr/lib/systemd/system/postgresql.service.
```

#### Running Arch Linux and VirtualBox

See [this link from the Arch Wiki web site](https://wiki.archlinux.org/index.php/VirtualBox)

e.g. You may just need to run `sudo modprobe vboxdrv` to start it up

#### Replace multiple blank lines with one in Emacs

```elisp
(defun single-blank-lines()
  "replace multiple blank lines with a single one"
  (interactive)
  (goto-char (point-min))
  (while (re-search-forward "\\(^\\s-*$\\)\n" nil t)
    (replace-match "\n")
    (forward-char 1)))
```

Then from inside Emacs just `M-x single-blank-lines`

#### Copy public keys to the remote server (manual way)

```
cat ~/.ssh/id_rsa.pub | ssh user@123.45.67.89 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 0600 ~/.ssh/authorized_keys"
```

Optional: disable the password for root login

```
sudo vi /etc/ssh/sshd_config
```

Ensure that the following line are only allow the connection with SSH key:

```
PermitRootLogin without-password
```

Then reload the ssh daemon:

```
sudo reload ssh
```

#### Create new Github repository from command line

Try using [gh-utils](https://github.com/agilecreativity/gh-utils)

Once installed you can simply create a new empty Github repository with

```sh
gh-utils --config config.edn --r awesome-repo-name
```

#### To properly deploy to [clojars.org](https://clojars.org)

```
# To avoid the error try with GPG keys
lein deploy clojars

# instead of just `lein deploy`
```

See [this link](https://github.com/technomancy/leiningen/issues/1890) for details

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

#### Install ruby 2.2.3 on Arch when getting openssl error

See [//wiki.archlinux.org/index.php/Rbenv](https://wiki.archlinux.org/index.php/Rbenv)

```sh
# First install required library if not already done
pacman -S base-devel libffi libyaml openssl zlib

# Install rbenv with patch
curl -fsSL https://gist.github.com/mislav/055441129184a1512bb5.txt | rbenv install --patch 2.2.3
```

For other platform or system see [this link from rbenv](https://github.com/rbenv/ruby-build/issues/826) issues in Github

#### Install and setup Postgres (and redis) on ArchLinux

The usual package installation

```sh
sudo pacman -Syu postgresql
sudo pacman -Syu redis
```

Now follow [PostgreSQL wiki](https://wiki.archlinux.org/index.php/PostgreSQL/)

In summary:

```
# login as postgres
sudo -i -u postgres

# Command now run as `postgres` user
[postgres] initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'

# Exit from running as postgres
exit

# Now it is a good time to create a password for `postgres`
# First change to root
sudo su -

# Change the password for user 'postgres'
passwd postgres

# Type and remember the password for postgres
# Now exit from the sesssion
exit

# As sudo user, we will need to start the postgresql.service
# Check the current status of the postgresql.service (it should be inactive)
sudo systemctrl status postgresql.service

# To start it with the system use
sudo systemctrl enable postgresql.service

# Similarly, for redis
sudo systemctrl enable redis.service
```

#### Checkout the Github PR locally easily

```
[remote "origin"]
	url = git@github-work:<YOUR-ORG-OR-USERID>/<YOUR-PROJECT>.git
	fetch = +refs/heads/*:refs/remotes/origin/*
  # This is the magic line, this will allow us to run `git checkout pr/<NUMBER>`
  fetch = +refs/pull/*/head:refs/remotes/origin/pr/*
```

#### How to quickly clone multiple Github repos quickly

Use my own [github-cloner](https://github.com/agilecreativity/github-cloner) gem

```sh
# Install the ruby gem
gem install github-cloner

# And just use it to clone the repos from your favourite user/organization
# e.g. To clone all of the 'Emacs Lisp' and 'HTML' repository for user 'sachac' the Emacs curator try
github cloner -u sachac -l "Emacs Lisp,HTML" -c
```

#### Install ruby version 2.2.3 on Linux e.g. ArchLinux with rbenv

In this case, I am using `rbenv` as ruby manager. This wiil not be applicable
to `rvm` or any other tools like `chruby`, etc.

- [Original link](https://github.com/rbenv/ruby-build/wiki#openssl-sslv3_method-undeclared-error)

```sh
#!/bin/bash
## See: the rbenv installation site
## For Arch Linux we need to first install the following packages

## 1)
sudo pacman -S base-devel libffi libyaml openssl zlib

## If you get the openssl error then try
## https://github.com/rbenv/ruby-build/wiki#openssl-sslv3_method-undeclared-error
## 2)
curl -fsSL https://gist.github.com/mislav/055441129184a1512bb5.txt | \
    rbenv install --patch 2.2.3
```

#### Selenium web driver for Firefox work best with version 35.0.1

- Download the older version of Firefox and install to some directory
- [https://www.mozilla.org/en-US/firefox/35.0.1/releasenotes/]()
- [download pages for version 35.0.1](https://ftp.mozilla.org/pub/firefox/releases/35.0.1/)
- [The en-US version](https://ftp.mozilla.org/pub/firefox/releases/35.0.1/linux-x86_64/en-US/)
