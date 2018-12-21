---
layout: post
title:  "Inspecting transient dom elements in Chrome"
date:   2018-12-21 11:24:00 +1100
categories: javascript chrome
---
Inspecting transient elements in chrome dev tools can be difficult. To be fair Chrome dev tools provide options that work for some cases. Chrome dev tools allow force focused, hover, active or visited states and that should be the first thing you try but sometimes it is not enough.
Thankfully very is another simple solution:

* in console type `setTimeout(() => {debugger}, 4000)`. This will pause the page in 4 seconds
* get your system in the state that shows element that you want to inspect
* wait for breakpoint
* go to the elements tab and inspect this elusive element