## What is Chaos Monkey?

Chaos Monkey is a service which identifies groups of systems and randomly terminates one of the systems in a group. The service operates at a controlled time (does not run on weekends and holidays) and interval (only operates during business hours). In most cases we have designed our applications to continue working when a peer goes offline, but in those special cases we want to make sure there are people around to resolve and learn from any problems. With this in mind Chaos Monkey only runs in business hours with the intent that engineers will be alert and able to respond.

## Why Run Chaos Monkey?

Failures happen, and they inevitably happen when least desired. If your application can't tolerate a system failure would you rather find out by being paged at 3am or after you are in the office having already had your morning coffee? Even if you are confident that your architecture can tolerate a system failure, are you sure it will still be able to next week, how about next month? Software is complex and dynamic, that "simple fix" you put in place last week could have undesired consequences. Do your traffic load balancers correctly detect and route requests around system failures? Can you reliably rebuild your systems? Perhaps an engineer "quick patched" a live system last week and forget to commit the changes to your source repository?

Refer to the [Quick start guide](wiki/Quick-Start-Guide) to get started setting up and using Chaos Monkey