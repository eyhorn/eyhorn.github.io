---
layout: post
title:  "ReSharper: use only tabs for multiline alignment"
date:   2019-05-27 11:28:00 +1100
categories: resharper tools
---
[ReSharper](https://www.jetbrains.com/resharper/) is a great tool for refactoring and it gets a lot of things right but sometimes default behaviour need fine-grain tuning. If your team uses tabs you might get surprising behaviour around multiline alignment.

To be fair most of the formatting with ReSharper works right with default settings, it even uses Visual Studio's settings for tab and indent size. But if you wrapping long lines you might occasionally see spaces in addition to tabs.

After spending a little bit of time digging through ReSharper's options I have found the culprit. Option: `Code Editing -> C# -> Formatting Style -> Tabs, Indents, Alignment -> Align Multiline Constructs -> How to align when tabs are used for indents` was set to `Mix tabs and spaces for optimal fill`. To fix the problem the value should be set to: `Only use tabs`.

The proposed solution works great but the indentation might be slightly inaccurate and depending on your team's formatting standard might not be acceptable.