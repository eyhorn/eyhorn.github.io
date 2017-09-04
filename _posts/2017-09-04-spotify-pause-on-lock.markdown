---
layout: post
title:  "Automatically pause music when locking Windows workstation"
date:   2017-09-04 11:24:00 +1100
categories: spotify autohotkey
---
Music helps me concentrate while working and Spotify is my favorite music streaming service. 
Often I forget to pause Spotify before locking my computer. As a result I have to unlock my PC, pause Spotify, lock my workstation and only then I walk away from my workstation.

At first I've tried existing software that was supposed to solve this little problem but nothing worked.
Then I decided to use [AutoHotkey](https://autohotkey.com) as I'm already using it for different purposes. 
The resulting script below is just one-liner that looks like this:
{% highlight tex %}
#l::Send {Media_Stop}
{% endhighlight %}
The script will detect `Win+L` and send `Media Stop` command.
As a result I can lock my computer without the need to pause music first.
