# Linux CLI Tutorial
this tutorial assumes you basically came from windows and you're trying out linux either for fun or to get work done. it won't provide everything you need, but will get you off the ground with syntax and some examples.

If you need keywords, search for them. ctrl-f, /, or \<F3\> in browser.

## Shell
### Prompt
when you open a terminal, you will be greeted with a prompt. the most minimal prompt will be a single character, either `$` for a normal, unprivileged user, or `#` for the root, or "admin" user. It's good to know in case you're following a tutorial and suddenly the prompt changes, indicating you need root access.

more useful prompts will also show the current directory you're in, the default one being called "home," denoted by the `~` character. its actual path looks like `/home/username`, replaced with your actual username.

### Syntax
basic command syntax goes like this:
```
       $ cp -v foto.png path/to/foto2.jpg Pictures/
prompt ^ |  |  |        |                 |
command  ^  |  |        |                 |
arguments   ^  ^        ^                 ^
```
this is running the `cp` command and giving it 4 arguments, telling it to copy 2 photos to the `Pictures` directory. the `-v` flag is covered under the commands section.

sometimes, you need to download an executable script or binary file and run it directly. to do this, simply provide the path to the file.
```
$ scripts/foto-odserver -flags argument1 arg2
```
the shell assumes you want a command by default, so if the `foto-odserver` file is in your current directory, you can't just run `foto-odserver`, instead you need `./foto-odserver` where the `.` represents the current directory. similarly, `..` means the previous dir. the `/` tells the shell that you're specifying the path to an actual file.

if it says "bash: permission denied" or something, the file probably isn't marked as executable. see the Root and Permissions section (or just run `chmod +x foto-odserver`).

### Special Syntax
the shell is a programming language. this means it has a lot of syntax with special meanings.

variables: `$` next to a word like `$appleSauce` tells the shell to look for and print the "appleSauce" variable content.
```
$ appleSauce=gooberosity
$ echo $appleSauce
gooberosity
```
variables in ALL CAPS represent environment variables, which have their own section further down. however, the capitalization is just standard notation and you can do whatever you want.

comments: any text that follows `#` is a comment, and will never be executed as code.

word splitting: the shell has a useful but very annoying concept called "word splitting." you've noticed that all shell arguments are separated by whitespace (spaces or tabs), but what happens when you evaluate a variable with whitespace, like "foto folder"? it's not read as a single argument with whitespace, but 2 separate arguments. to specify 1 arg, use quotes:
```
$ file="foto folder"
$ touch $file # created 2 files
$ ls
folder  foto
$ touch "$file" # created 1 file
$ ls
folder  foto  'foto folder'
```

glob matching: the `*` symbol is used to "glob match" for files, where * means any possible string of characters. if you run `ls *.png` then it will list all files ending in .png.
what actually happens syntactically is the shell takes the list of files it found and inserts it directly into the command line. so `cp *.jpg Pictures/` turns into `cp cat.jpg dawg.jpg 'Andrew Jackson.jpg' Pictures/`.
globs do not work inside quotes.

### Keybinds
- UP/DOWN - go through command history
- TAB - completes command names, directory and file names, and if supported, command options. press tab once to complete if there's only 1 possible option, twice to see a list of options otherwise. very useful binding.
- ctrl-c - cancels the currently running command
- ctrl-z - pauses the currently running command, can be resumed later with `fg` or `bg`.

## Commands
commands don't *have* to follow any rules, but usually follow some sort of loose standard syntax:
```
$ command -h -v -flags --long-option --long-with-argument argument --alternative-long-with-argument=argument arg1 arg2 arg3
```
where every argument starting with a hyphen is a "flag" or an "option" to pass to the command to change its behavior. typically, `-h` prints help information, and `-v` makes the command "verbose" to print more information.
specific command usage can usually be found with either:
```
command -h
command --help
man command
command -?
command
```
where `man` is the "manual" command that offers more-or-less complete guides on each command. otherwise, you're using an obscure tool that hopefully has online documentation.
note that sometimes the command name of a program (the executable found in $PATH) is different from its manual name.

### Common Commands
- cp - copy
- mv - move or rename
- ln - link or symlink
- rm - remove (be careful)
- ls - list files
- pwd - print current dir
- cd - change dir
- chmod - change permissions
- sudo - run command as root
- su - switch user, root by default
- touch - create new files, update timestamp
- grep - search for text in a file
- man - command manual

## Root and Permissions
### Basics
"root" is the admin user of linux, and lets you do anything.
if you don't know what the command you're about to run as root is going to do, be careful, as it could really do anything, including nuke your entire system.

most directories/files that aren't under your home dir (`/home/username`) will need root access to change. this means either entering `sudo` before your command to temporarily gain root access, or using `su` to switch to the root user for that shell (can be exited with `exit` or ctrl-d). `sudo` uses your personal user's password, while `su` uses the actual root user's password. sudo will not always be set up for you, depending on the linux distro.

installing packages requires root because it has to modify the root file tree in /usr/bin, /usr/share, /usr/lib, etc. oh and /etc.

### Executables
in linux, there are permissions for reading, writing, and executing files for different "users" and "groups." sometimes when you download an executable, it's not actually marked as executable, and you can't execute it. this can be fixed with `chmod`:
```
$ chmod +x executable-file
```

### Folders vs Files
the way permissions work on files vs directories is a little confusing. if a directory is read-only for your user, you can't create or delete any files inside it. however, it's still possible to edit the files inside it, if they give you access. if the files are read-only, you can still delete them if the directory permits it.

## Files
### Editing
as a noob, you want to use the `nano` command to make simple text edits on files. run `nano file.txt` and it will open the editor with a keybind cheatsheet.
`vi` and `vim` are much better editors, if you know how to use them. there are many tutorials if you're interested.

if you know the executable name of your favorite graphical editor, you can use that as well, usually with the same syntax `editor file`. common gui editors include:
- kate - KDE Plasma editor
- gedit - GNOME editor
- pluma - MATE editor
- mousepad - XFCE editor
- leafpad - LXDE editor
- xed - Cinnamon (Linux Mint) editor
- geany
- notepadqq

of course these can also be accessed through the graphical file manager, but this is a cli tutorial.

### Links and Symlinks
links are useful when you need the same file in 2 or more locations at once.

when you link a file, you're taking the location of the file and using it to create another file, where both files refer to the same data. changing one link will change the other, because they're the same thing, but in different locations or have different names.

symlinks or symbolic links tend to be more useful, as they "link" on the filesystem-scale, meaning you always know the location of the source file or directory, and can also link entire directories.

links or hard links work by copying an "inode"'s location and making a new file out of it. all hard links are identical to each other; there is no "original" link that can be pointed to, and you cannot hard link directories. the major benefit of hard links is you can delete any copy you want, and the others will remain fine, while deleting the source file of a symlink will just delete all the data.

### OS-level Differences
unlike windows, linux files are extremely flexible. their filenames can use any character at all except for `/` and null bytes and can reach 255 chars in length. there are no reserved filenames.
no file extensions are needed; all file types are detected by actually *reading* the file, which can be demoed with `file --mime-type image.png`

there are also many "special" files in linux, which may not represent real files, but rather things like USB devices, hard drives, audio ports, memory, or even a random number generator. most of these are found in `/dev`. the significance of these is that you can still operate on them like real files, whether it works or not.

every directory will contain 2 "fake" directories, `.` and `..`, which refer to the current and previous directories respectively. `path/to/dir/.` resolves to `path/to/dir`, `path/to/dir/../..` resolves to `path`.

## Environment Variables
each process (running program) in a linux system will contain a list of variables that it can use to obtain information about the environment it's runnning in. there are many typical environment variables, all of which use ALL_CAPS_SNAKECASE notation, although it's not required.

for example, `echo $HOME` shows your user's home directory at /home/username, EDITOR contains your default CLI text editor, and SHELL contains your login shell, usually `/bin/bash`. you can dump your entire environment to the terminal by running `env`.

child processes will inherit their parents' environment, unless it is explicitly changed by something like the `env` program. your user has its own set of env variables that are set in a shell startup file, which for most distros will be `~/.bash_profile`. all programs the user starts after login will be spawned from that initial shell, so if you want to change your global environment, change this file.

changing it looks like:
```sh
export TERMINAL=kitty
export BROWSER=firefox
export EDITOR=vim
export XDG_CONFIG_HOME="$HOME/.config"
```
remember to log out and log back in for this file to take effect, as it's not possible to send a signal to all running processes to change their environment.

for testing purposes, you can use `export` in your current terminal so that all commands run on that terminal will use the updated environment.

### PATH variable
a common env var to change is PATH, which outlines a colon-separated list of directories that the shell and other programs use to look for commands to execute. a sample output is:
```
$ echo $PATH
/home/username/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/bin
```
outlining 4 directories to look for executables. you can `ls` these places to see yourself.

to add another directory to PATH, simply combine the existing PATH variable with the path to your directory and separate them with a colon:
```
$ echo $PATH # before modification
/usr/local/sbin:/usr/local/bin:/usr/bin
$ export PATH="$HOME/.local/bin:$PATH"
$ echo $PATH # after modification
/home/username/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/bin
```

of course, you can also place the files you need into a PATH directory. for locally-built packages, you may want to add `$HOME/.local/bin` to PATH and then either move, copy, or symlink the executable into that directory. for instance:
```
ln -s package/scripts/* ~/.local/bin
```
symlinks everything in a certain package's `scripts` folder to `~/.local/bin`.

see the Files section for an explanation on symlinks.

## Extra Utilities
there are some CLI tools that are so useful out of the box that i'd recommend them even to loser noobs like you, if you're allowed to install programs.

### Ranger
a terminal file manager is extremely useful. i use [lf](https://github.com/gokcehan/lf), but this isn't in debian repos and is more minimal, so you can install `ranger` instead which is the same thing but fancier (and slower).
- \<F1\> to view help
- h,j,k,l or arrow keys to navigate
- space to select
- yy to yank(copy), dd to cut, pp to paste
- E to edit with EDITOR
- zh to toggle hidden files
- o to sort files
- \<Return\> to open file in default application

etc.

### Network File Transfer
[croc](https://github.com/schollz/croc) lets you transfer files extremely quickly over the internet or over LAN between any 2 computers, no cloud service needed. it does NOT have a debian package because debian sucks, so debianers can install Magic Wormhole through the `wormhole-william` package, which does the same thing but worse.

if you only need LAN transfer, you can use `rsync`, for which there's a ton of guides.
