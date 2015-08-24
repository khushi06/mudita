#### simianarmy.sdb.domain
This property sets the SimpleDB domain where all the monkey events will be stored.  The default is SIMIAN_ARMY.
```
    simianarmy.recorder.sdb.domain = SIMIAN_ARMY
```
### Scheduler Properties
With these properties you can configure how many monkeys are run in parallel and how frequently they are run.

#### simianarmy.scheduler.frequency
How often to run the monkeys.  This works in conjunction with frequencyUnit to determine the cycle.
If frequency is 1 and frequencyUnit is HOURS then it will run once every hour.  Default value is 1.
```
    simianarmy.scheduler.frequency = 1
```

#### simianarmy.scheduler.frequencyUnit
The value needs to be one of the [java.util.concurrent.TimeUnit](http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/TimeUnit.html) enum values.  See comment for frequency.  Default value is "HOURS".
```
    simianarmy.scheduler.frequencyUnit = HOURS
```

#### simianarmy.scheduler.threads
This sets how many monkeys to run in parallel.  The default is 1.
```
    simianarmy.scheduler.threads = 1
```

### Calendar Properties
With the calendar properties you can configure when the best time to run is for your organization.

#### simianarmy.calendar.openHour
This sets when the monkeys are open for business.  They will not run if the current time is earlier than the openHour. The default is 9am.
```
    simianarmy.calendar.openHour = 9
```

#### simianarmy.calendar.closeHour
This sets when the monkeys are closed for business. They will not run if the current time is later than the closeHour.  The default is 3pm.
```
    simianarmy.calendar.closeHour = 15
```

#### simianarmy.calendar.timezone
This sets the timezone for where the clocks should be tracked.  If the monkeys are running in eu-west-1 you might still only want them to run during the business hours of your office on the US West coast.  The default is America/Los_Angeles.
You can find other possible values [here](https://docs.oracle.com/cd/E26401_01/doc.122/e20929/T319774T319789.htm).
```
    simianarmy.calendar.timezone = America/Los_Angeles
```

#### simianarmy.calendar.isMonkeyTime
This is a calendar override used for testing.  It should not be set in your properties file.  If it is set and is "false" then the monkeys will never run, no matter what time of day it is.  If is set to "true" then it will always be business hours.
```
    simianarmy.calendar.isMonkeyTime = false
```

### Misc Properties
Properties to address specific issues.

#### simianarmy.tags.owner
Allows you to Set the (case sensitive) AWS Tag Key to use for owner tags; e.g. "Owner" or "owner:. It is Monkey Wide - used by all Monkeys. If not set defaults to "owner"
```
    simianarmy.tags.owner = owner
```

#### simianarmy.aws.email.region
Region override for Amazon Simple Email Service Client, allows a specific region to be specified for the the SES client.  If not set, default to "us-east-1".
```
    simianarmy.aws.email.region = us-east-1
```