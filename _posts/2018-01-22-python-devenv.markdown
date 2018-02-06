---
layout: single
title: "Python Dev Environment: Virtualenv and Wrapper"
date:   2018-01-22
categories: python setup
tags: python setup
---
## So you want to do some Python
This post is pretty much 100% about how I set up my development environments. There are any number of ways to do it but I've personally found using virtualenv and virtualenvwrapper work well for me. Some major **Notes:**

1. I mostly use Ubuntu so the examples all use apt-get for the install part. If you're on a different linux with different package manager there will probably be slight changes needed. 
2. If you're on windows and want to also dive into Linux I have a bit of info on setting up Virtual Box. If you're new to both Linux and Python that is a lot to bite off (see #3)
3. **Conda/Anaconda/Miniconda** Conda is potentially a really great option to get started with particularly if your python interests are in the numpy/science area. It also may be a really good option if you are doing more "full stack" stuff as Conda bills it self as a more generalized manager. (As opposed to virtual environments that are really only concerned with python.)

**Defense of the Notes above**

1. On the OS front--python seems to be a great equalizer as it can run on windows, linux and mac. I've used linux for quite a while so I can't help much with Windows focused environments. For linux and macs aside from installation process the config stuff should work.
2. If you're new to python and no linux experience--jumping into both at the same time will probably lead to a lot of frustration. But will also probably provide a lot of rewards when you get that "thing" to finally work.
3. Conda: I've never used it but it is, from all appearances, a really good option to get up and running. May be the best option to jump into python if you're new to programming and not familiar with Linux. 

Anyway, this is the set up I use and, for me at least, it makes it easy to work on multiple things and not mess up my OS. 

### So why?
There are a number of reasons why a virtual environment is a really good idea--3 major ones are . . . 

1. You want to mess around with some python packages/clone some code/toy around with things. 
2. You want to create code that others can use and know exactly what is required to run the code.
3. Profit! (Sorry.) Serious reason #3: virtual environments and virtualenvwrapper will make life easier if you bounce from thing to thing or work on a number of different projects. Hell, it's a really good way to work on the production branch and the development branch of the same project.

For beginners the first case is very important. Virtual Environments let you use any Python version you want and let you pile on any array of extra packages and package versions without polluting your OS'es environment. Randomly adding packages to your OS **may** not hurt much--but eventually you will break things. Beyond that, lets say you want to mess around with numpy or flask; you find some interesting projects to test out. Then you discover project A wants X version of numpy and project B wants y version. In short, down the road, if your projects are in virtual environments you won't end up trying to figure you broke something that worked 2 months ago.

Second case is sort of the evolution of the first. When you start writing stuff that you want to share you will be able to easily say what exactly is required to run your code. With the right set up, testing suites, and validation you'll even be able to provide a range of environments that your code will work with.

3rd case is the real reason--it will save your sanity. Whether you bounce from topic to topic or have a laser beam like focus on a specific area, managing your code with virtual environments should help you keep your stuff organized. 

#### Detour to VirtualBox
For the Windows users--VirtualBox is a Cisco Virtual machine that can run a full-blown OS on Windows. It can run just about any flavor of Linux you want, Windows, or even OS X. I'm not going to go into details on the install and setup but two good resources are . . .

* [PsychoCats](http://www.psychocats.net/ubuntu/virtualbox)
* [https://linus.nci.nih.gov/bdge/installUbuntu.html](https://linus.nci.nih.gov/bdge/installUbuntu.html)

The second seems to be more complete. My comments:

* Install the guest additions. It will let you map windows directories into your linux directory space **and** let you copy and paste to and from windows. (You are using 2 monitors aren't you?)
* Don't skimp on disk size. Obviously don't commit your last remaining bit of hard drive to the virtual instance but if you have the disk space do not stick with the default 10 GB--20 or 40 will work well for a while. Resizing can be a pain.
* Fortunately networking (at least recently) has worked flawlessly for me out of the box. Be thankful for that and also that you don't have to rely on a dual-boot system.

**Some quick Linux tips**
* Learn shell [short cuts](https://www.skorks.com/2009/09/bash-shortcuts-for-maximum-productivity/)
  * Tab completion is a god-send. (Type the start of a command and hit tab twice)
  * Up-arrow for going through recent history
  * Ctrl-r to search backward through history
  * There are a ton more but those I use almost constantly
* Commands:
  * `history` and `history | grep word` 
  * [grep](https://linux.die.net/man/1/grep) or `man grep`
  * `locate` and `tree`: (Install with `apt-get install mlocate tree`). Once locate is installed you'll need to run `sudo updatedb` after that you can search your hard drive for any file with just `locate part-of-file-name`. The `tree` command will display the contents of current sub-directories as a tree--great for getting an idea of how something you just pulled from git is layed out.
  * Learn `ps` (`ps -awx` is pretty useful); `ls` (`ls -Flh`); `netstat` (`netstat -lpten`); `ifconfig`
* Find an editor you like
* Learn how to use `apt-get` and `apt-cache` from the command line for installing and searching for available software
* **Total Opinion (unlike everything else I say)**: Try cairo-dock: `apt-get install cairo-dock`; reboot; before logging in hit the little gear above the password box and choose Cairo.

## Installing the dev-env and related items (Back to the main story)
Well we're going to start off by installing all the stuff you'll need to get started. All of this assumes you're running inside a terminal. Python (probably 2.7) should already be installed. To check just run `python --version`; also check if python3 is installed `python3 --version`. If python3 says something like "command not found" you'll need to run the first apt-get command--if it is installed just run the last two. So run the following commands as root (e.g. first run `sudo su -` to become root) . . . 

`apt-get install python3`

`apt-get install python-virtualenv python-pip python3-virtualenv python3-pip virtualenv`

`apt-get install git gitk ipython3 ipython3-notebook idle3 idle`

After that run the following (still as root):

```
pip install --upgrade pip
pip3 install --upgrade pip
pip install virtualenvwrapper
pip3 install virtualenvwrapper
pip3 install jupyter
```

**What is all that?**
* `pip` and `pip3` are the tools for installing python(2) and python3 packages
* "virtualenv" items are the core virtual environment tools
* `git` and `gitk` are for working with git--probably won't touch on git much but you'll likely start to need it
* ipython3 and ipython3-notebook are used for running "notebooks" (more on that later)
* `idle` and `idle3` are python editors. There are plenty of options for editors, and any will do, but idle is the "standard" python development environment. Provides an easy way to both edit files and run a python shell/session. 
* **Pip Commands**
  * First we just upgrade both pips (it complains if it isn't the latest version)
  * Then we install virtualenvwrapper
  * And finally jupyter (for the ipython notebooks)

## Configuration: A bunch of nitty-gritty in a one place
Some of this will simply make things a lot easier when using virtualenvs but some is also a bit of my own personal preference. There are actually any number of ways to work with virtual environments for python--I find virtualenvwrapper to be a huge time saver and makes things pretty simple. I'll explain more of what the configurations are for when we actually hit on them in use.

### ONE: Edit Idle
The executables for the two idles (/usr/bin/idle and /usr/bin/idle3) are actually just python scripts. However, they need a minor change to work well in virtual environments. So, we need to change the first line in both from 

```bash
  "#! /usr/bin/python"   to    "#!/usr/bin/env python"
  and
  "#! /usr/bin/python3"   to    "#!/usr/bin/env python3"
```

If you don't have a favorite editor you can do this with `sudo idle /usr/bin/idle` and `sudo idle /usr/bin/idle3`. (If you are new to linux you're simply using an editor, which happens to be idle, to edit a file, which just happens to be how the editor is launched. You could do something like `idle guac_recipe.txt` to use idle to create and edit a file named guac_recipe.txt. Ctrl-s Ctrl-q will save and quit idle; or you can use the menu.)

### TWO: Create some directories
As your usual user (i.e. not as root); in your home directory (just type `cd` to get there if you aren't there already) we need to create two directories. So run . . .

```
mkdir py_envs
mkdir .virtualenv
```

### THREE: Edit .bashrc
The .bashrc file is a special file/script that gets run anytime you start a new terminal session. It is found in your home directory but is usually hidden because it starts with a "." (you can see that sort of file with `ls -l`). Anyway edit it with `idle .bashrc` and add the following lines at the end . . .

```bash
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/py_envs
source /usr/local/bin/virtualenvwrapper.sh
alias vw='virtualenvwrapper'
```

Again use Ctrl-s Ctrl-q to save and quit. Then just run `source .bashrc`. (The "source" command is a funky bit of magic that will run a file in your current session/environment--usually if you run a script or program it gets its own environment which goes away once the script/program exits. In this case we are actually changing our current shell environment. You will not need to do that if you start a new terminal as the new session will run the new modified .bashrc.)

When you run source .bashrc you should see it do a few things to your .virtualenvs directory. That is from the "source /usr/local/bin/virtualenvwrapper.sh" line. The source command is again used here to essentially keep the results of virtualenvwrapper.sh as part of your environment.

### FOUR: Configure some virtualenv items
Final config stuff. Go into your .virtualenvs directory (`cd .virtualenvs`); and just to see what is there type `ls`. You should see a bunch of things like "postactivate" and "premkproject". These are all files that are run/launched because of various virtual environment actions (their names pretty much explain when). We'll be editing 2 of them:

**`idle postactivate`**

add:

```bash
PROJECT_SOURCE=`cat "$VIRTUAL_ENV/.project"`

cd () {
    if (( $# == 0 ))
    then
        builtin cd $PROJECT_SOURCE
    else
        builtin cd "$@"
    fi
}

cd

#if [ ! -d "notebooks" ]; then
#  mkdir notebooks
#fi

#jupyter-notebook notebooks/ > /dev/null 2>&1 &
#echo $! > notebooks/pid.file 
```

The last five lines are actually commented out. If you remove the pound signs they'd create a notebooks directory in your virtual environment and also launch jupyter notebook anytime you activeate your environment. Only useful if you really use notebooks a lot. 

The uncommented lines at the start make it so the "cd" command will put you back in your virtual environments root directory (rather than your home directory).

**`idle postdeactivate`**

add:

```bash
#NPID=`cat "$PROJECT_SOURCE/notebooks/pid.file"`

#kill -9 $NPID 

unset -f cd
```

This essentially reverses the items from postactivate. The commented lines would kill the notebook server if you are running it. The uncommented line sets the "cd" command back to normal.

## . . . and we're done
So, that is it, virtualenv and virtualenvwrapper are now ready to go. Now, how to use it.

First just type `vw` (we created vw as an alias for the rather long `virtualenvwrapper` command in our .bashrc). It will show you the commands available--most of these are also rather long and you don't need to remember them. Just type `vw` at any time to get the list. You also probably won't be using them a whole lot but they are really useful when you do need them. And when you do, the "TAB" key is your best friend.

So now type `mkpr` "TAB"--should expand to `mkproject `; we need to add a project name so ultimately you'll want `mkproject test_project`.

It will do a bit of work and when it is done you should find yourself in the "~/py_envs/test_project" directory and you should see "(test_project)" at the start of your command line. 

Now for some more "virtualenvwrapper" commands. Type `vw` again just to get the list again. Why not try `cdsitepackages`. You should now find yourself in "~/.virtualenvs/test_project/lib/python3.5/site-packages/". Running `ls` will show you what was installed. Also note you are now in the ".virtualenvs/" directory and not "py_envs/". The ".virtualenvs/" directory is where the actual environment lives (python, tools, and packages); "py_envs/" will be where you'd keep your own code.

Now just type `cd`. You should be brought back to "~/py_envs/test_project".

Try `lssitepackages`--you'll see the contents you saw earlier after "cd". Run `pip install numpy`. Then run `lssitepackages` again and numpy is there.

Now run `python --version`. It should say Python 3.X.X. But currently in ubuntu the python version is 2.X?? Ok, lets get out of here for a minute and check. Run `deactivate`--you'll still be in "~/py_envs/test_project" but "(test_project)" has disappeared. 

Run `python --version` again--should say 2.X.X. Ok, enough of that. One command you will likely use a lot is "workon"--so just type `workon` the TAB (maybe TAB again). It should auto-complete to `workon test_project` (since you only have one project). Hit enter and `python --version` again.

Now, how about idle. Lets try `idle np_array.py` to create a new python file. IN the file just paste (or type)

```python
import numpy as np

x = np.array([1,4,3,2])
print(x)
```

Then hit "F5". Your file should run. Also notice "idle" and not "idle3" is running using the 3.x version of python. This is why we needed to edit the /usr/bin/idle[3] files. Without the /usr/bin/env portion idle would actually still be using your OSes set of packages and python rather than your current environments.

Ok, exit out of both idle windows. And at the command prompt run `python np_array.py`. It should work. If you run `deactivate` to exit and if you haven't installed numpy in your OSes python `python np_array.py` will not work.

Now, lets try `wipeenv`--then `lssitepackages`. Numpy is gone and `python np_array.py` doesn't work. If you do happen to use Numpy or some other package for a lot of projects you can install it at the OS level. The `toggleglobalsitepackages` command will let you use it and anything else you've installed outside of your virtualenv. 
