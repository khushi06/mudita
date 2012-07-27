### simianarmy.properties
```
    java -Dsimianarmy.properties=/path/to/my/simianarmy.properties
```

### simianarmy.aws.accountKey
```
    simianarmy.aws.accountKey = accountKey
```

### simianarmy.aws.secretKey
```
    simianarmy.aws.secretKey = secretKey
```

### simianarmy.aws.region
```
    simianarmy.aws.region = us-east-1
```
### simianarmy.sdb.domain
```
    simianarmy.sdb.domain = SIMIAN_ARMY
```
### simianarmy.scheduler.frequency
```
    simianarmy.scheduler.frequency = 1
```

### simianarmy.scheduler.frequencyUnit
The value needs to be one of the [java.util.concurrent.TimeUnit](http://docs.oracle.com/javase/6/docs/api/java/util/concurrent/TimeUnit.html) enum.
```
    simianarmy.scheduler.frequencyUnit = HOURS
```

### simianarmy.scheduler.threads
```
    simianarmy.scheduler.threads = 2
```

### simianarmy.calendar.openHour
```
    simianarmy.calendar.openHour = 9
```

### simianarmy.calendar.closeHour
```
    simianarmy.calendar.closeHour = 15
```

### simianarmy.calendar.timezone
```
    simianarmy.calendar.timezone = America/Los_Angeles
```

### simianarmy.calendar.isMonkeyTime
```
    simianarmy.calendar.isMonkeyTime = false
```

### simianarmy.chaos.enabled
```
    simianarmy.chaos.enabled = true
```

### simianarmy.chaos.leashed
```
    simianarmy.chaos.leashed = false
```

### simianarmy.chaos.ASG.enabled
```
    simianarmy.chaos.ASG.enabled = false
```

### simianarmy.chaos.ASG.probability
```
    simianarmy.chaos.ASG.probability = 1.0
```

### simianarmy.chaos.ASG.&lt;asgname&gt;.enabled
```
    simianarmy.chaos.ASG.<asgname>.enabled = true
```
### simianarmy.choas.ASG.&lt;asgName&gt;.probability
```
    simianarmy.chaos.ASG.<asgName>.probability = 1.0
```
