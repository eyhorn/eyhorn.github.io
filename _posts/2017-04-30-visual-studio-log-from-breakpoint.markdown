---
layout: post
title:  "Write to console from a breakpoint in Visual Studio"
date:   2017-04-30 11:24:00 +1100
categories: vs debugging
---
While debugging it is sometimes useful to write some information to the output from various places.
The only downside is that you have to come back and clean-up the code introduced to print information to the console.
Not so long ago I've discovered that there is an alternative and it can be accessed via breakpoint setup menu.
To use it do the following:

*  set up breakpoint
* right-click on it and access `Actions`

![Search]({{ site.url }}/assets/images/vsbreakpoint/actions.png)

In the input field enter information you want to log. The formatting options for this field are:

* Include the value of a variable or other expression by placing it in curly braces (e.g. The value of x is {x}’).
* To insert a curly brace use “\\{“. To insert a backslash, use "\\\\".

The following special keywords can be accessed using $

* $ADDRESS - Current Instruction
* $CALLER - Previous Function Name
* $CALLSTACK - Call Stack
* $FUNCTION - Current Function Name
* $PID - Process Id
* $PNAME - Process Name
* $TID- Thread Id
* $TNAME - Thread Name

![Search]({{ site.url }}/assets/images/vsbreakpoint/log-formatting.png)

The breakpoint with an output action looks like a diamond instead of the circle.
Once you finished debugging you can simply remove the breakpoint.
