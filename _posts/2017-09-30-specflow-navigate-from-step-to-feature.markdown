---
layout: post
title:  "Navigate from step file to SpecFlow feature with NCrunch"
date:   2017-09-30 11:24:00 +1100
categories: specflow tests ncrunch
---
If you team using [SpecFlow](http://specflow.org/) with Visual Studio you might have found difficult to navigate from the step definition file to the feature file.
The good news is: there is a solution [NCrunch](http://www.ncrunch.net/). The bad news is: it is pricy. In defence of NCrunch - it is an excellent tool and I can't imagine working without it. It does have a lot of nice features which deserve separate blog post.


Because SpecFlow steps are meant to be reused and spread between separate files it is very important to be able to jump to the feature file to trace the logic of the test. To achieve this using NCrunch you need to do following:

* Run your tests at least once. In our setup NCrunch runs tests continuously as the code changes.
* Right click on the green (or red if your test is failing)
![Select dot]({{ site.url }}/assets/images/ncrunch/select_dot.png)
* In the displayed menu select "Show tests covering this line"
![Select covering tests]({{ site.url }}/assets/images/ncrunch/show_covering_tests.png)
* Among listed tests double click on the test that you need
![Select test]({{ site.url }}/assets/images/ncrunch/select_test.png)
* You will be navigated to the correct scenario in the feature file 
![Feature]({{ site.url }}/assets/images/ncrunch/feature.png)



 
