---
layout: post
title:  "Testing RxJs 4.0 Throttling"
date:   2016-12-29 14:52:33 +1100
categories: javascript rxjs
---
[RxJs](https://github.com/Reactive-Extensions/RxJS) is a great set of libraries. But testing it can be a challenge.

Recently I needed to unit test throttle operator and it took me quite a bit to discover solution that works well in our project.
At first I've tried to apply the solution described [Hyphe Blog](https://blog.hyphe.me/rxjs-testing-in-real-world-applications/) unfortunately it didn't work for me
but the method for injecting TestScheduler was really helpful.

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
  var throttleSpy;

  beforeAll(function() {
    testScheduler = new Rx.TestScheduler();
    var originalThrottle = Rx.Observable.prototype.throttle;
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

As you can see from the example above you can simply use `advanceBy` method to advance internal clock and test throttling synchronously.
RxJs 5 brings many changes I would have to revisit this code after it gets out of beta.
