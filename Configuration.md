### simianarmy.properties
The system property **simianarmy.properties** specifies the location of the properties file used to configure the monkeys.  The default file is "/simianarmy.properties" located at the root of one of the classpath directories.  See the [defaults in the github repo](https://github.com/Netflix/SimianArmy/blob/master/src/main/resources/simianarmy.properties).
```
    $ java -Dsimianarmy.properties=/path/to/my/simianarmy.properties ...
```
### AWS Properties
These are the properties needed to access Amazon EC2, Amazon Auto Scaling and Amazon SimpleDB.
 
#### simianarmy.aws.accountKey
The Access Key Id from [AWS Security Credentials](https://portal.aws.amazon.com/gp/aws/securityCredentials).
```
    simianarmy.aws.accountKey = accountKey
```
#### simianarmy.aws.secretKey
The Secret Access Key Id from [AWS Security Credentials](https://portal.aws.amazon.com/gp/aws/securityCredentials).
```
    simianarmy.aws.secretKey = secretKey
```

#### simianarmy.aws.region
This specifies the Amazon region where the Monkeys will run.  It is expected that if you want to run the monkeys in multiple regions then you will a separate installation of SimianArmy in each region.  The default region is "us-east-1".
```
    simianarmy.aws.region = us-east-1
```
#### simianarmy.sdb.domain
This property sets the SimpleDB domain where all the monkey events will be stored.  The default is SIMIAN_ARMY.
```
    simianarmy.sdb.domain = SIMIAN_ARMY
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
This sets how many monkeys to run in parallel.  Since there is only one monkey currently in the Simian Army anything more than 1 is not useful at the moment.  The default is 1.
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
You can find other possible values [here](http://docs.oracle.com/cd/E19653-01/819-4438/acfib/index.html).
```
    simianarmy.calendar.timezone = America/Los_Angeles
```

#### simianarmy.calendar.isMonkeyTime
This is a calendar override used for testing.  It should not be set in your properties file.  If it is set and is "false" then the monkeys will never run, no matter what time of day it is.  If is set to "true" then it will always be business hours.
```
    simianarmy.calendar.isMonkeyTime = false
```

### Chaos Properties
With these properties you can control how chaos monkey operates.

#### simianarmy.chaos.enabled
This property allows the Chaos Monkey to run.  The default is "true".  Note that an enabled Chaos monkey does not mean it is also allowed to terminate instances.  It must also have the leashed property set to "false".
```
    simianarmy.chaos.enabled = true
```

#### simianarmy.chaos.leashed
This is effectively a "dryrun" option.  If leashed is "false" then it will discover instance groups and select instances for termination based on the configured properties, but it will not be able to terminate any instances. The default value is "false".
```
    simianarmy.chaos.leashed = false
```

#### simianarmy.chaos.terminateOndemand.enabled
This option is used to enable the on-demand termination of chaos monkey. When it is set to true, the chaos monkey will take on-demand termination requests from the REST API. The default value is "false".
```
    simianarmy.chaos.terminateOndemand.enabled = false
```

#### simianarmy.chaos.mandatoryTermination.enabled
This option is used to enable the mandatory termination of chaos monkey. When it is set to true, the chaos monkey will use a default non-zero probability for instance groups when the groups have been opted in for the last mandatory termination window and there was no terminations in the last mandatory termination window. The default value is "false".
```
    simianarmy.chaos.mandatoryTermination.enabled = false
```

#### simianarmy.chaos.mandatoryTermination.windowInDays
This option specifies the number of days of the mandatory termination window. This option takes effect only when **simianarmy.chaos.mandatoryTermination.enabled** is true. If the value is not set or has a non-positive value, no mandatory termination will happen. The **simianarmy.chaos.ASG.asgname.lastOptInTimeInMilliseconds** value is also used to decide if a mandatory termination is going to be enforced. If there is no **lastOptInTimeInMilliseconds** for the instance group, no mandatory termination is going to happen.
```
    simianarmy.chaos.mandatoryTermination.windowInDays = 32
```

#### simianarmy.chaos.mandatoryTermination.defaultProbability
This option specifies the default probability of mandatory termination. This option takes effect only when **simianarmy.chaos.mandatoryTermination.enabled** is true. The default value of this option is 0.5.
```
    simianarmy.chaos.mandatoryTermination.defaultProbability = 0.5
```

#### simianarmy.chaos.ASG.enabled
The default instance groups shipped in SimianArmy is ASG (Auto Scaling Groups).  This value can be used to change the Opt-In vs Opt-Out behavior.  If this is set to **true** then Chaos Monkey will be configured as Opt-Out, this means that **ALL ASG's will be considered for terminations unless explicitly disabled.**  If this value is set to **false** then Chaos Monkey will be configured as "Opt-In", so all ASGs will not be considered for terminations unless explicitly enabled.  The default is "false".
```
    simianarmy.chaos.ASG.enabled = false
```

#### simianarmy.chaos.ASG.probability
This sets the default probability of termination for all ASGs considered.  This is the probability of termination per day.  With the default schedule settings above, Chaos Monkey will run 6 times a day (9am to 3pm, hourly).  So if an ASG is assigned the default probability of 1.0 then each run the run-probability will be 1.0/6 or .166666.  What this means is that Chaos Monkey will draw a random number between 0.0 and 1.0, and f that random number is less then .166666 it will proceed to select an instance from the ASG and terminate it.  If you want to guarantee a termination on the first run of the day, you can set the probability to 6.0 (ie 600%), which when divided over all the runs would give you a 1.0 run probability.  In practice a 1.0 probability does not guarantee that an instance will be terminated every day, there will be days where there are no terminations.  If you want to have less frequent terminations for your ASGs you can lower the probability.  Since the monkeys only run 5 days a week (they get the weekends off) you can get a weekly termination with 0.2.  If you want a termination every other week, set it to 0.1.  Note these are still just probabilities, the actual termination events are still random.  With a 0.2 probability the terminations **on average** will be weekly, there will be times where terminations happen 2 days in a row, then perhaps followed by 2 weeks without any terminations.  Note that Chaos Monkey will not attack the same ASG in one day.  If the probability is set to 6.0, one termination will happen in the ASG on the first run, then no more in that ASG for the rest of the day.
```
    simianarmy.chaos.ASG.probability = 1.0
```

#### simianarmy.chaos.ASG.maxTerminationsPerDay
This setting allows you to set a value of maximum terminations per day (24 hours) for a specific ASG by name. The number can be a double from 0.001-100.0. 0.001 means that there can be at most 1 termination in 1000 days, 100 means there can be at most 100 terminations in one day. For example, if you want to have at most one termination every week (7 days), you can set this value to 0.143 (approx 1/7). The default value is 1.0 which means at most there is one termination per day.
```
    simianarmy.chaos.ASG.maxTerminationsPerDay = 1.0
```

#### simianarmy.chaos.ASG.&lt;asgname&gt;.enabled
This setting allows you to enable or disable a specific ASG by name.  For example if the ASG name is "monkey-target" to enable it for consideration set **simianarmy.chaos.ASG.monkey-target.enabled = true**.  
```
    simianarmy.chaos.ASG.<asgname>.enabled = true
```

#### simianarmy.chaos.ASG.&lt;asgName&gt;.probability
This setting allows you to set a probability for a specific ASG by name.  For example if the ASG name is "monkey-target" to lower the default probability to weekly set **simianarmy.chaos.ASG.monkey-target.probability = 0.2**.  See the comment above on simianarmy.chaos.ASG.probability for further details.
```
    simianarmy.chaos.ASG.<asgName>.probability = 1.0
```

#### simianarmy.chaos.ASG.&lt;asgName&gt;.maxTerminationsPerDay
Same as **simianarmy.chaos.ASG.maxTerminationsPerDay**, but setting can be applied to a specific ASG by name.
```
    simianarmy.chaos.ASG.<asgName>.maxTerminationsPerDay = 1.0
```

#### simianarmy.chaos.ASG.&lt;asgName&gt;.lastOptInTimeInMilliseconds
This value records the last timestamp when the ASG opted in. This value is used in the mandatory termination scenarios.
```
    simianarmy.chaos.ASG.<asgName>.lastOptInTimeInMilliseconds = 1234567
```