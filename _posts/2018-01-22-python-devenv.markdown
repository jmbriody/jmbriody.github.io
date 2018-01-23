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
Maybe in a different post I'll talk about how to run Ubuntu on a windows machine and do a whirl-wind intro to Linux--but here I'm just going to focus on setting up a Python dev environment. The system related commands (e.g. apt-get) are all Ubuntu-ish based; if you run another flavor of linux you may need to translate a bit and use some google-fu. 

Anyway, this is the set up I use and, for me at least, it makes it easy to work on multiple things and not mess up my OS. 

### So why?

