---
layout: post
title:  "Fix karma error: Disconnected because no message in 10000 ms"
date:   2017-05-31 11:24:00 +1100
categories: javascript karma
---
We are using Karma Test Runner to run our Jasmine test suite. We used to run into intermittent failures on our CI server. This would happen when our shared VM server would be starved for the resources. The error message in the log would look something like this: 
`Disconnected (1 times), because no message in 10000 ms`.

After the search on the internet we found out that setting `browserNoActivityTimeout` in `karma.config` should be increased to `60000` (`60 000`). We applied this change but were still receiving the error. The change that stopped our build errors for good was to change `browserNoActivityTimeout` to `400000` (`400 000`).

Since fix was applied we didn't have any problems with karma on our CI server.
