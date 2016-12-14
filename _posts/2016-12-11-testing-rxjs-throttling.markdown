---
layout: post
title:  "Testing RxJs throttling"
date:   2016-11-02 21:52:33 +1100
categories: javascript rxjs
---
[RxJs](https://github.com/Reactive-Extensions/RxJS) is a great set of libraries. But testing it can be a challenge.

Recently I needed to unit test throttle operator and it took me quite a bit to discover solution that works well in our project.
At first I've tried to apply the solution described [Hyphe Blog](https://blog.hyphe.me/rxjs-testing-in-real-world-applications/).
This article helped me to get going so many thanks to the blog's author.

# Testing the throttling #
{% highlight javascript %}
var throttleWindowDuration = 2 * 1000; /* 2 seconds */

function throttleTest() {
  var unthrottledStream = new Rx.Subject();
  var source = unthrottledStream.throttle(throttleWindowDuration);
  var result = {
    emitCounter: 0,
    unthrottledStream
  };

  var subscription = source.subscribe(
    function() {
      result.emitCounter++;
    });

  return result;
}

describe('run with test scheduler', function() {
  var testScheduler;
  var originalThrottle;
  var throttleSpy;

  beforeAll(function() {
    testScheduler = new Rx.TestScheduler();
    originalThrottle = Rx.Observable.prototype.throttle;
    throttleSpy = spyOn(Rx.Observable.prototype, 'throttle')
      .and.callFake(function(dueTime) {
        return originalThrottle.call(this, dueTime, testScheduler);
      });
  });

  afterAll(function() {
    throttleSpy.and.callThrough();
  });

  it('advancing testScheduler allows to test throttling synchronously', function() {
    var throttleTestResult = throttleTest();

    emitCounter = 0;
    //throttled stream will not emit if scheduler clock is at zero
    testScheduler.advanceBy(1);
    throttleTestResult.unthrottledStream.onNext();
    throttleTestResult.unthrottledStream.onNext();
    throttleTestResult.unthrottledStream.onNext();
    testScheduler.advanceBy(throttleWindowDuration);
    throttleTestResult.unthrottledStream.onNext();
    throttleTestResult.unthrottledStream.onNext();
    testScheduler.advanceBy(throttleWindowDuration);
    throttleTestResult.unthrottledStream.onNext();
    testScheduler.advanceBy(throttleWindowDuration);
    throttleTestResult.unthrottledStream.onNext();

    expect(throttleTestResult.emitCounter).toBe(4);
  });
});

describe('run without test scheduler', function() {
  it('without test scheduler the emit counter will stay at 1 '
    + 'as throttle duration is not elapsed', function() {
    var throttleTestResult = throttleTest();

    throttleTestResult.unthrottledStream.onNext();
    throttleTestResult.unthrottledStream.onNext();
    throttleTestResult.unthrottledStream.onNext();
    throttleTestResult.unthrottledStream.onNext();
    expect(throttleTestResult.emitCounter).toBe(1);
  });
});
{% endhighlight %}
