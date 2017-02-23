---
layout: post
title:  "Ditto Clipboard Manager"
date:   2017-02-17 12:15:00 +1100
categories: tools
---
[Ditto Clipboard Manager](http://ditto-cp.sourceforge.net/) is one of those tool that after using it for a little while
you can't imagine how could you get by without it before.
It has a lot of features that boost my productivity and I'm sure there are some that I don't even know about.

So what are the feature that make Ditto a must-have tool.

* Size of the clipboard. By default it save last 500 items you copied to your clipboard. Being programmer I always use clipboard and to copy
block of code from one file to another. Having just one item in your clipboard makes this process frustrating and
time consuming. Some IDEs like Visual Studio have their own clipboard managers. But in case of VS it only works inside
this IDE and will be cleared then Visual Studio is closed.

* Persistence. Clipboard items are persisted. This means that it will survive planned or unplanned reboot. This is incredible feature.
How many times you have rebooted your computer and had to spend time finding necessary information. Ditto takes care of
this issue by storing items copied to the clipboard into database. You can copy something into clipboard for future
reference and stop worrying about it. Previously I would usually have few files open to keep this kind of information.

* Search. Open Ditto and start typing. You will find the information you need in no time at all.

  ![Before search]({{ site.url }}/assets/images/ditto/main_window.png) ![Search]({{ site.url }}/assets/images/ditto/search.png)

* Compare two clipboard items. Simply select two items and press Ctrl-F2 and the clips will be displayed in your
comparison tool in my case it's [Beyond Compare](http://www.scootersoftware.com/).

  ![Compare]({{ site.url }}/assets/images/ditto/invoke_compare.png) ![Comparison result]({{ site.url }}/assets/images/ditto/comparison_in_external_tool.png)

* Keyboard shortcuts. Ditto wouldn't have been so convenient and useful without keyboard shortcut. By default Ditto
is activated by pressing Ctrl-\` but you can change almost any shortcut in Ditto's options. You can assign shortcut to
paste one of your last ten clipboard clips, insert text as a plain text or save current clipboard.

* Sticky items. Just mark clip as sticky and it will stay at the top of the list. You can do this to the multiple items.
I found this feature useful then I was doing refactoring in our JavaScript project. I needed to have few blocks of code
at hand. Making these blocks as sticky clipboard clips saved me a lot of time.

* Exclude some programs. Having you clipboard content safe and persisted is all nice and well but you probably wouldn't
want to keep your sensitive information in long term storage. But developers of Ditto thought about it and allow you to
exclude some program. I personally exclude [KeePass](http://keepass.info/).

* Text transformations. Often enough you need to insert you clipboard clip as plain text or you want to change it's
casing. Ditto has you covered.

  ![Special paste]({{ site.url }}/assets/images/ditto/special_paste.png)

* View, edit and save clips. Ditto can store not just text but images as well. Writing this blog entry I would make a number of
screenshots and then use Ditto to save them directly into png files. Choose the clip and select view from the
context menu or press F3 and you can quickly identify what image you are looking at. With text entries you can
use Ditto as simple editor for your notes.

In conclusion I would like to say only one thing: Install Ditto and give it a go you will not regret it.
