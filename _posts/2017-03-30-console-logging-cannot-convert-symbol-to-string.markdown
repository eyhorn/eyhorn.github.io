---
layout: post
title:  "Console logging overcoming TypeError: Cannot convert a Symbol value to a string"
date:   2017-03-30 9:15:00 +1100
categories: javascript
---
While developing react application I would occasionally run into `TypeError: Cannot convert a Symbol value to a string`.
This happened when I tried to log react component to console inside jasmine test.
Fortunately where is a simple workaround.
{% highlight javascript %}
console.log(JSON.stringify([data to log], null, 2));
{% endhighlight %}

It looks like a lot of typing but the simple solution to this is to create snippet.
Almost every popular editor has a concept of snippets and if yours doesn't it might be time to change editor.
Here is an example on how to setup snippet in Atom.
In the menu select `File -> Snippets`. snippets.cson file will be opened. Add following block of code:
{% highlight conf %}
'.source.js':
  'console.log with JSON.stringify':
    'prefix': 'lj'
    'body': 'console.log(JSON.stringify($1, null, 2));'
{% endhighlight %}

Now if you want to log something with `JSON.stringify` it will be only few keystrokes away.
