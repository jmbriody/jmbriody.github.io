---
layout: single
title: "Python Dev Environment: Virtualenv and Wrapper"
date:   2018-01-22
categories: python setup
tags: python setup
---
## So you want to do some Python
This post is pretty much 100% about how I set up my development environments. There are any number of ways to do it but I've personally found using virtualenv and virtualenvwrapper work well for me. **Major Caveat** I pretty much exclusively use linux--mostly Ubuntu for personal/development and Redhat. My work machine runs Windows--but I run Ubuntu on it in VBox.

So almost all of this is going to be Ubuntu-ish focused. 
* If you run a different linux and are comfortable with linux already the major differences will likely be how you install "system" software (probably yum vs. apt)
* If you run windows:
  * Totally new to programming and just want to do Python there are probably better places to find info
  * Want to learn python **and** learn linux. You'll probably need to really use some google-fu. Both are life-long endeavors, learning both at the same time can be a pain but probably rewarding. 

If you are running windows I'll briefly touch on how to set up an Ubuntu virtual environment using Oracle's VirtualBox. **But** that simply gets you a linux install running on windows

--------
Maybe in a different post I'll talk about how to run Ubuntu on a windows machine and do a whirl-wind intro to Linux--but here I'm just going to focus on setting up a Python dev environment. The system related commands (apt-get) are all Ubuntu based; if you run another flavor of linux you may need to translate a bit and use some google-fu. 

Anyway, this is the set up I use and, for me at least, it makes it easy to work on multiple things and not mess up my OS. 

### So why?
There tend to be 2 major reasons why a virtual environment is a really good idea. 1) You want to mess around with some python packages/clone some code/toy around with things. 2) You want create code that others can use and know exactly what is required to run the code.

For beginners the first case is very important. Virtual Environments let you use any Python version you want and let you pile on any array of extra packages and package versions without polluting your OS'es environment. Randomly adding packages to your OS may not hurt much--but you can break things, and you will almost certainly break things if you just change your OS python from 2.7 to 3.5. Beyond that, lets say you want to mess around with numpy or flask; you find some interesting projects to test out. Then you discover project A wants X version of numpy and project B wants y version. In short, down the road, if your projects are in virtual environments you won't end up trying to figure out what change you made to your system broke something that worked 2 months ago.

Second case is sort of the evolution of the first. When you start writing stuff that you want to share you will be able to easily say what exactly is required to run your code. With the right set up, testing suites, and validation you'll even be able to provide a range of environments that your code will work with.

The unmentioned case, which may be the most compelling of all, is that a good virtual environment set up will help you organize your projects and code. 

