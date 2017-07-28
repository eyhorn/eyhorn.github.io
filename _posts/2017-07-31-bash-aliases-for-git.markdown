---
layout: post
title:  "Bash aliases for git"
date:   2017-07-31 11:24:00 +1100
categories: bash git
---
Git is wonderful and easy to use. Most of the commands are pretty short and do not require a lot of typing.
However some programmers, including me, are pretty lazy and if it saves me few keystrokes I prefer to type less to achieve the same results.
Enter bash aliases. It is possible to specify any command and assign it to something short. 

Here is a little guide on how to do it.

* In your bash shell type `vim ~/.bashrc`
* In the opened file you can configure your aliases like shown below
{% highlight bash %}    
alias gs='git status'
alias gph='git push'
alias gpl='git pull'
alias gco='git checkout'
alias gc='git commit -v'
alias gaa='git add -A'
alias gk='gitk &'
alias gg ='git gui &'
alias ggb='git gui blame'
alias gl='git log -10 --pretty=format:"%Cred%h%Creset %Cblue%an%Creset %ar: %Cgreen%s"'
{% endhighlight %}

Most of aliases in the list above are self explanatory. The `&` symbol at the end of `gk` and `gg` means that command will run in background.
If this command is not specified the shell will wait until window is closed before new command can be typed in.

The last alias `gl` shows example why bash alias can be really useful. If you execute `git log` command in bash you will be presented with scrolling log.
That might be what you want but I prefer condensed and easy to read output that command behind `gl` alias produces.
