---
layout: post
title:  "Testing RxJs 5.x time-based operators"
date:   2017-01-27 12:15:00 +1100
categories: javascript rxjs
---
In my [previous post](/javascript/rxjs/2016/12/29/testing-rxjs-throttling.html) I've looked into testing RxJs 4.x throttling.
Recently I've discovered that RxJs 5.x is out of beta now and decided to take a look at testing time-based operators in the released version.
As I already had an example to test throttling in RxJs 4.x I have used it as a base for my new test.

# Testing the throttling #
As it turns out testing time-based operators in RxJs 5.x is quite easy. You can simply use `jasmine.clock` like you would for any other JavaScript functionality involving timeouts.
I've created a [plunk](https://plnkr.co/edit/IwXUjMIuBxGdzfgzPk6R?p=preview) to describe the solution.
{% highlight javascript %}
var throttleTimeWindowDuration = 2 * 1000; /* 2 seconds */

function throttleTest() {
  var unthrottleTimedStream = new Rx.Subject();
  var source = unthrottleTimedStream.throttleTime(throttleTimeWindowDuration);
  var result = {
    emitCounter: 0,
    unthrottleTimedStream
  };

  var subscription = source.subscribe(
    function() {
      result.emitCounter++;
    });

  return result;
}

describe('using jasmine.clock', function() {
  beforeEach(() => {
    jasmine.clock().install();
  });

  afterEach(() => {
    jasmine.clock().uninstall();
  });

  it('allows to test throttling synchronously', function() {
    var throttleTestResult = throttleTest();

    throttleTestResult.unthrottleTimedStream.next();
    throttleTestResult.unthrottleTimedStream.next();

    jasmine.clock().tick(throttleTimeWindowDuration);
    throttleTestResult.unthrottleTimedStream.next();
    throttleTestResult.unthrottleTimedStream.next();

    jasmine.clock().tick(throttleTimeWindowDuration);
    throttleTestResult.unthrottleTimedStream.next();

    jasmine.clock().tick(throttleTimeWindowDuration);
    throttleTestResult.unthrottleTimedStream.next();

    expect(throttleTestResult.emitCounter).toBe(4);
  });
});
{% endhighlight %}

The example above demonstrates how easy it is to test time-based operators such as `throttleTime` in RxJs 5.
