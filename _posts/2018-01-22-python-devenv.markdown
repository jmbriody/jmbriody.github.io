---
layout: single
title: "Python Dev Environment: Virtualenv and Wrapper"
date:   2018-01-22
categories: python setup
tags: python setup
---
## So you want to do some Python
Have you ever searched the web for a recipe, like for Kung Pao Chicken, and the top few links all have like 4.8 stars with 500+ reviews? Then you go to a link and it ends up being 184 pictures with an 18,900 word narrative about cooking Kung Pao chicken and some random stuff about how the family cat helps with the cooking and at the end is a note-card size recipe. I'm not going to do that here--but I think virtual environments require at least a little introduction matter. I'll keep the intro short but the basic plan for this post is . . .

1. Intro--skip or scan if you already know why they are a good idea
2. Software install--parts will be essentially linux/Ubuntu oriented for OS level items. If you're on a "yum" linux vs. an "apt" or on a different distro there will be minor adjustments needed. Macs will need slightly more adjustments. Windows--I just don't know; more on that in the **Intro**.
3. Configuration--This is the true **meat and gristle** of the set up. After grokking that using virtual environments is a really good idea getting them setup so you can work with them with minimal effort can be a pain. I hope the config section will show you how to set your stuff up so you can work the way you want to.
4. Closing crud.

## INTRO -- Wherefor art thou virtualenv ##
If you're reading this I assume you've already been told virtual environments are a good idea. I'd sort of guess that you've also found, after diving in, that some shit just doesn't work right when you do a virtual environment. I am not going to go into any sort of detailed comparison of "venv" vs. "virtualenv" or any of that other stuff. **This is what works for me** and it may be a good starting point to find what works for you.

In a nutshell, as the name may imply, a virtualenv is an enclosed environment--this means you have **better control** of what you are using and also means you are **limited** in what you are using. The **better control** part helps greatly in not only **not** messing up other stuff you work on but also knowing exactly what you are working on. The **limited** part is where we run into issues. Without configuration most software will have no idea that you are running in a virtual environment. 

#### Details Details -- Why? ####
There are typically two places things can be installed--this is true for most things but we're focusing on Python . . .

1. OS level -- this should be where the stuff needed by your system is stored.
2. Local level -- this is the stuff you as a user want 

For python throwing everything into the OS level install will probably cause issues. On Linux managers like "apt" or "yum" tend to try to figure out what will work (not always perfectly--but that is why you should be nice to them). If you throw in "pip" stuff at the OS level you are going outside your OS'es management system and can potentially break things.

So we have pip "--user". This installs python packages for your specific user. That is a good thing. But we run into a problem here--one day you're working with Foo v. 1.2 the next Foo v. 2.0. Then you end up working with something later that uses Foo v 1.4 and you have no idea why stuff is going wonky.

We finally end up with virtual environments. When you set up your virtual environment system well you can seemlessly and easily work on Foo 1.2, Foo 1.4, and Foo 2.0 all at the same time (in different shells/windows). On top of that you'll know exactly what you're using--if you install 20 packages at the OS or User level you may not know for sure what you're pulling in. In a virtual environment that sort of thing becomes much simpler.

#### The Downside ####
Typically when you login or even launch your OS'es GUI 2 environments are loaded--the OS (e.g. /usr/bin) and the user stuff (~/bin/). It gets slightly more complicated than that, but, in short, your applications essentially know where to find the stuff they need. Virtual environments throw a twist into the mix. Unless an application figures out it should be working with the virtual environment it ends up using just the OS and user environment. So, we need to do some work to make the applications we want to use in many virtual environments work correctly.

>  Ultimately, once you get a sense of how to make applications work with virtual environments you should start to really understand how they are working and, possibly more importantly, have an idea of how to make other tools work with them.

**Finally**--this is how I set mine up. There are a number of different ways to do environments, but this set up probably isn't that uncommon. There are probably many people who work with "venv" or "virtualenv" directly and there are also even applications/packages like __Anaconda__ that aim to do specific things. I typically work with just plain old python--if you are doing full stack/web dev or more into analytics/science stuff you may find other solutions/options more suitable. Again though, I hope to illustrate at least some of the underlying mechanics with some concrete examples which should translate to almost all virtual environment style setups.

## Software Install (Back to the main story)
Well we're going to start off by installing all the stuff you'll need to get started. All of this assumes you're running inside a terminal.

The first items are the **OS** level stuff. `sudo` is used to run stuff as "root"--you're essentially installing items the will go into system wide directories/usage. It is highly likely that some, if not all, of these are already installed--apt will simply tell you if they are and not do anything. 

I won't be covering any "git" stuff, but you'll likely want it at some point (and gitk is just a nice tool for looking at info for a local git repo). Git doesn't actually require any configuration to work inside a virtual environment--essentially it is like a honey-badger and just doesn't care.

Idle is both an editor and a shell for python. Very handy for doing small stuff and running scripts or playing with code. I will be using it below and it does take a "tweak" to work right with virtual envs.


```
sudo apt-get install python3
sudo apt-get install python-pip python3-pip 
sudo apt-get install git gitk idle3 idle
```

Now for the "user" stuff. pip and pip3 install python packages; the "--user" flag installs it to your local user python "stuff". There isn't really any major compelling reason to not install this stuff system wide--but it really doesn't hurt to get into the habit of not installing everything globally.

As a quick side note `pip list` will show all the packages installed both at the OS level and in your user environment. `pip list --user` will list only the items installed in your user environment (they're found at "$HOME/.local"). 

So the commands: First two are just so "pip" won't complain about being up-to-date. Third command is probably not really necessary, but doesn't hurt. Final one we install virtualenv, virtualenvwrapper, and ipython + jupyter for jupyter notebooks. 

We are here mostly to talk about virtualenv/virtualenvwrapper, but, like idle, I'll cover the jupyter notebook set up as well. Mostly because notebooks are widely used, pretty handy, and over all aren't a bad thing to be familiar with.


```
pip install --upgrade pip --user
pip3 install --upgrade pip --user
pip install virtualenv virtualenvwrapper --user
pip3 install virtualenv ipython3 virtualenvwrapper jupyter --user
```

## Configuration: A bunch of nitty-gritty in one place
Some of this will simply make things a lot easier when using virtualenvs but some is also a bit of my own personal preference. There are actually any number of ways to work with virtual environments for python--I find virtualenvwrapper to be a huge time saver and makes things pretty simple. I'll explain more of what the configurations are for when we actually hit on them in use.

### ONE: Edit Idle
The executables for the two idles (/usr/bin/idle and /usr/bin/idle3) are actually just python scripts. However, they need a minor change to work well in virtual environments. So, we need to change the first line in both from 

```bash
  "#! /usr/bin/python"   to    "#!/usr/bin/env python"
  and
  "#! /usr/bin/python3"   to    "#!/usr/bin/env python3"
```

If you don't have a favorite editor you can do this with `sudo idle /usr/bin/idle` and `sudo idle /usr/bin/idle3`. (If you are new to linux you're simply using an editor, which happens to be idle, to edit a file, which just happens to be how the editor is launched. You could do something like `idle guac_recipe.txt` to use idle to create and edit a file named guac_recipe.txt. Ctrl-s Ctrl-q will save and quit idle; or you can use the menu.)

Anyway, these changes are important for running idle in a virtual environment. Rather than using the OS level python they will use the python from your virtual environment. And most importantly idle will be aware of the packages in your environment. 

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
So, that is it, virtualenv and virtualenvwrapper are now ready to go.

## But wait, there is more . . .

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
