---
layout: single
title: "Python Dev Environment: Virtualenv and Wrapper"
date:   2018-01-22
categories: python setup
tags: python setup
---
## So you want to do some Python
Have you ever searched the web for a recipe, like for Kung Pao Chicken, and the top few links all have like 4.8 stars with 500+ reviews? Then you go to a link and it ends up being 184 pictures with an 18,900 word narrative about cooking Kung Pao chicken and some random stuff about how the family cat helps with the cooking and at the end is a note-card size recipe. I'm not going to do that here--but I think virtual environments require at least a little introduction matter. I'll keep the intro short and try to get to the important stuff quickly.

## Python Virtual Environments

There are a number of ways to do virtual environments in Python--"virtualenv", "venv", and even Anaconda/Conda. **This is the set up that currently works for me.** Even if you don't end up using a setup exactly like this I hope you'll at least come away with a better understanding of how these environment work and how to make them work better for you.

For starters a virtual environment is really a two way street. It lets you limit and know exactly what and which versions of python components you are using and also lets you work with many different components and versions of the same components without creating a mess of inconisitent packages and versions. Assuming you already have pip installed you can start to get a general idea of where v-envs take you by running `pip list` then `pip list --user`. The first will show all packages installed, the second will show only the "user" level packages installed. V-envs will add another layer and a big reason for it is to keep from just throwing more and more stuff into the global or user scope.

**The Problem**--This comes with a bit of a hidden cost that can be frustrating at first. Tools and applications you may be used to using don't understand that they are in a virtual environment. So you fire up idle or a jupyter notebook and can't get it to load the code you just wrote or the package you just installed in your virtual environment. So, we'll fix that.

## Software Install (Back to the main story)
Well we're going to start off by installing all the stuff you'll need to get started. All of this assumes you're running inside a terminal.

The first items are the **OS** level stuff. `sudo` is used to run stuff as "root"--you're essentially installing items that will go into system wide directories/usage. It is highly likely that some, if not all, of these are already installed--apt will simply tell you if they are and not do anything. 

I won't be covering any "git" stuff, but you'll likely want it at some point (and gitk is just a nice tool for looking at info for a local git repo). Git doesn't actually require any configuration to work inside a virtual environment--essentially it is like a honey-badger and just doesn't care.

I will cover config for both Idle and Jupyter--if you use other tools they may need similar config changes to work and I hope these two as examples will at least be a good starting point.

```
sudo apt-get install python3
sudo apt-get install python-pip python3-pip 
sudo apt-get install git gitk idle3 idle
```

Now for the "user" stuff. pip and pip3 install python packages; the "--user" flag installs it to your local user python "stuff". There isn't really any major compelling reason to not install this stuff system wide--but it really doesn't hurt to get into the habit of not installing everything globally.

```
pip install --upgrade pip --user
pip3 install --upgrade pip --user
pip install virtualenv virtualenvwrapper --user
pip3 install virtualenv ipython3 virtualenvwrapper jupyter --user
```

## Configuration: A bunch of nitty-gritty in one place
I'm going to start with the virtual environment set up then we'll quickly set up idle for v-envs. After that I'll do a quick whirl-wind tour of what we've done and basics of using this type of setup. Finally, I'll do the jupyter notebook set up--which is a bit more complex.

### One: Create some directories
As your usual user (i.e. not as root); in your home directory (just type `cd` to get there if you aren't there already) we need to create two directories. So run . . .

```
mkdir py_envs
mkdir .virtualenv
```

### TWO: Edit .bashrc
The .bashrc file is a special file/script that gets run anytime you start a new terminal session. It is found in your home directory but is usually hidden because it starts with a "." (you can see that sort of file with `ls -l`). Anyway edit it with `idle .bashrc` and add the following lines at the end . . .

```bash
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/py_envs
source /usr/local/bin/virtualenvwrapper.sh
alias vw='virtualenvwrapper'
```

You can use Ctrl-s Ctrl-q to save and quit. Then just run `source .bashrc`. (The "source" command is a funky bit of magic that will run a file in your current session/environment--usually if you run a script or program it gets its own environment which goes away once the script/program exits. In this case we are actually changing our current shell environment. You will not need to do that if you start a new terminal as the new session will run the new modified .bashrc.)

When you run source .bashrc you should see it do a few things to your .virtualenvs directory. That is from the "source /usr/local/bin/virtualenvwrapper.sh" line. The source command is again used here to essentially keep the results of virtualenvwrapper.sh as part of your environment.

The last line added to the .bashrc file is simply an alias--lets you just type "vw" rather than "virtualenvwrapper". At this point you can actually just run `vw`--it doesn't actually do anything except show you all the commands available in the "virtualenvwrapper" system. We have more configuration to do but it is a good sneak peak at what we're getting to.

### THREE: Configure some virtualenv items
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

### FOUR: Edit Idle
So, we're actually done with the v-env setup. But we still need to get idle to work in a virtual environment. 

Again, if you aren't in your user's home directory type `cd` to get there. Then run `mkdir bin`. Now run `cp /usr/bin/idle bin/` -- so we've just copied the OS/global version of idle to your personal user's bin directory. Now run `idle bin/idle` and change the first line from "#! /usr/bin/python" to "#!/usr/bin/env python". Ctrl-S Ctrl-Q to save and quit. (Yes that was some inception level shit right there.)

What this does: Your user's "bin" directory should be the first in your path--so running "idle" will now use ~/bin/idle. (You can run `which idle` to verify that.) Changing the first line makes it so idle will use your current environments python--this will work from inside a virtual environment or when you are not in a virtual environment. 

The one big reason to do this in your user's bin directory is so that when idle gets updated your changes won't get squashed.  

## . . . and we're done
So, that is it, virtualenv and virtualenvwrapper are now ready to go.

## But wait, there is more . . .

So now you can type `vw` again just to see what the general commands are and their descripts. We're going to start with `mkproject first_test`. . . . And it will create your virtualenv and put you in its directory. Your command line should now start with "(first_test)". Now run `pip list`--not much there. And a few more things to try . . .

1. `lssitepackages`
2. `toggleglobalsitepackages` followed by `pip list` then `togglegloabalsitepackages` again--"toggle..." will essentially add your global site packages to your environment. Can be useful at times. (And if you aren't familiar with "tab-completion" try just `toggl[tab]` so you don't have to type all that.)
3. `cdsitepackages`; `ls`; `cd`--"cdsite..." is occasionally useful, but the main point here is that `cd`, at least for now, returns you to your venv directory and not your user's home directory.

And finally run `deactivate`. This takes you out of your virtual environment (although you'll still be in its directory). Now run `mkproject second_test`. You can run some of the previous commands again but they'll pretty much show the same stuff. So run `deactivate` again.

Now--the command you'll really need--run `workon [TAB]`. You should get a list of your v-envs. Continuing by type `f[TAB]` should auto-complete to "workon first_test". Hit [ENTER] and we're back in the first_test v-env. 

## And now for something completely different
While still in "first_test" run `idle np_test.py &`. Then type or paste . . .

```python
import numpy as np

np_array = np.array([0, 2, 4, 5])
print(np_array)
```

Then hit [f5] to run the code. Even if you have numpy already installed outside your v-env you will get an error. So go back to the terminal (where it starts with (first_test)) and run `pip install numpy` (you can then run `pip list` again). Now go back to the file in idle and hit [f5] again.

You've now installed numpy in your virtual environment. 

At this point you can try some commands like `pip freeze`, then `wipeenv` followed by `pip list` (numpy should be gone).

As far as "idle"

http://zafar.cc/2016/12/13/python-virtual-environment-and-jupyter-notebooks/

['/home/jack/.virtualenvs/numbers/lib/python3.5/site-packages',
 '',
 '/usr/lib/python35.zip',
 '/usr/lib/python3.5',
 '/usr/lib/python3.5/plat-x86_64-linux-gnu',
 '/usr/lib/python3.5/lib-dynload',
 '/home/jack/.local/lib/python3.5/site-packages',
 '/usr/local/lib/python3.5/dist-packages',
 '/usr/lib/python3/dist-packages',
 '/home/jack/.local/lib/python3.5/site-packages/IPython/extensions',
 '/home/jack/.ipython']


 sys.path = [
    '/home/jack/venvs/numbers/notebooks',
    '/home/jack/.virtualenvs/numbers/lib/python35.zip',
    '/home/jack/.virtualenvs/numbers/lib/python3.5',
    '/home/jack/.virtualenvs/numbers/lib/python3.5/plat-x86_64-linux-gnu',
    '/home/jack/.virtualenvs/numbers/lib/python3.5/lib-dynload',
    '/usr/lib/python3.5',
    '/usr/lib/python3.5/plat-x86_64-linux-gnu',
    '/home/jack/.virtualenvs/numbers/lib/python3.5/site-packages',
    '/home/jack/.virtualenvs/numbers/lib/python3.5/site-packages/bookofnumbers-0.1.0-py3.5.egg',
    '/home/jack/.local/lib/python3.5/site-packages',
    '/usr/local/lib/python3.5/dist-packages',
    '/usr/lib/python3/dist-packages',
]
USER_BASE: '/home/jack/.local' (exists)
USER_SITE: '/home/jack/.local/lib/python3.5/site-packages' (exists)
ENABLE_USER_SITE: True
