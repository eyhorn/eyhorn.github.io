---
layout: post
title:  "Parsing dates in Javascript"
date:   2018-07-12 11:24:00 +1100
categories: javascript
---
Working with dates is always difficult even if you trying to do something simple.
Consider for example `new Date('1954-1-5')` and `new Date('1954-01-05')`.
Those dates look the same and you would expect them to be the same but if you evaluate these two expression in Google Chrome here is the result that you will get:

```javascript
new Date('1954-1-5')
Tue Jan 05 1954 00:00:00 GMT+1000 (Australian Eastern Daylight Time)
new Date('1954-01-05')
Tue Jan 05 1954 10:00:00 GMT+1000 (Australian Eastern Daylight Time)
```

Notice that the time-zone offset is applied for the `new Date('1954-01-05')`.
If you run same expressions in Firefox you will get:

```javascript
new Date('1954-1-5')
Date 1954-01-05T00:00:00.000Z
new Date('1954-01-05')
Date 1954-01-05T00:00:00.000Z
```

Firefox considers both dates to be UTC midnight.
But if you try running `new Date('1954-1-5')` in Safari you will get `Invalid date`

As a result I would recommend to use strict ISO 8601 formatting  `YYYY-MM-DD` then parsing dates in Javascript code.