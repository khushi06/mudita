### Conformity Properties
With these properties you can control how conformity monkey operates.

#### simianarmy.conformity.enabled
This property allows the Conformity Monkey to run.  The default is "true".  Note that an enabled Conformity Monkey does not mean it saves the conformity check results to DB or send notifications
for unconforming clusters. It must also have the leashed property set to "false".
```
simianarmy.conformity.enabled = true
```


#### Customizing Conformity Monkey's schedule
By default Conformity Monkey wakes up and performs conformity checks, and sends notifications every hour. The properties below override the values set in **simianarmy.properties**.
```
simianarmy.scheduler.frequency = 1
simianarmy.scheduler.frequencyUnit = HOURS
simianarmy.scheduler.threads = 1
simianarmy.calendar.openHour = 0
simianarmy.calendar.closeHour = 24
simianarmy.calendar.timezone = America/Los_Angeles
```

####simianarmy.calendar.isMonkeyTime
To ease debug/dryrun, you can set this property to true so the monkey will always be able to run. This property also overrides the value in **simianarmy.properties**. The default value is false.
```
simianarmy.calendar.isMonkeyTime = false
```


The properties below allow you to customize whether conformity monkey sends notifications for failing clusters every hour or only within a specific time window.
Conformity monkey sends notifications to the owner of unconforming clusters between the open hour and close
hour only. In other hours, only summary email is sent. The default setting is to always send email notifications
after each run.
```
simianarmy.conformity.notification.openHour = 0
simianarmy.conformity.notification.closeHour = 24
```


#### simianarmy.conformity.leashed
This is effectively a "dryrun" option.  If leashed is "true" then it will performs conformity checks for all clusters, but it will not be able to save data to DB or send notifications.
The default value is "false".
```
simianarmy.conformity.leashed = false
```

#### simianarmy.conformity.resources.sdb.domain
This property specifies the SimpleDB domain for storing the data managed by the Conformity Monkey. The default is SIMIAN_ARMY.
```
simianarmy.conformity.sdb.domain = SIMIAN_ARMY
```

#### simianarmy.conformity.notification.sourceEmail
This property specifies the source email address that is used to send notifications about unconforming clusters or the summery email.
Its value needs to be a valid email address.
```
simianarmy.conformity.notification.sourceEmail = foo@bar.com
```

#### simianarmy.conformity.summaryEmail.to
This property specifies the email address to receive the summary email of Conformity Monkey after each run. The property value needs to be a valid email address.
```
simianarmy.conformity.summaryEmail.to = foo@bar.com
```

#### simianarmy.conformity.notification.defaultEmail
This property specifies the email address to receive notification about unconforming clusters when Conformity Monkey is not able to find the owner email.
You can set this property to your team's email list so that your team members are able to receive Conformity Monkey notifications.
```
simianarmy.conformity.notification.defaultEmail = foo@bar.com
```

#### simianarmy.conformity.Eureka.enabled
This property is for enabling the conformity rules that need to access Eureka for Conformity Monkey. By default Eureka is not enabled. You can only enable it if you have set up
Eureka running otherwise you will see exceptions when Conformity Monkey tries to initialize Eureka client.
```
simianarmy.conformity.Eureka.enabled = false
```


#### simianarmy.conformity.rule.SameZonesInElbAndAsg.enabled
This property is used to enable the conformity rule to check whether there is mismatch of availability
zones between any auto scaling group and its elastic load balancers in a cluster.
```
simianarmy.conformity.rule.SameZonesInElbAndAsg.enabled = true
```

#### simianarmy.conformity.rule.InstanceInSecurityGroup.enabled
# This property is used to enable the conformity rule to check whether all instances in a cluster
# are in required security groups.
```
simianarmy.conformity.rule.InstanceInSecurityGroup.enabled = true
```

#### simianarmy.conformity.rule.InstanceInSecurityGroup.requiredSecurityGroups
This property specifies the required security groups in the InstanceInSecurityGroup conformity rule.
```
simianarmy.conformity.rule.InstanceInSecurityGroup.requiredSecurityGroups = foo, bar
```

#### simianarmy.conformity.rule.InstanceTooOld.enabled
This property is used to enable the conformity rule to check whether there is any instance that is
older than certain days.
```
simianarmy.conformity.rule.InstanceTooOld.enabled = true
```

#### simianarmy.conformity.rule.InstanceTooOld.instanceAgeThreshold
This property specifies the number of days used in the InstanceInSecurityGroup, any instance that is
old than this number of days is consider nonconforming.
```
simianarmy.conformity.rule.InstanceTooOld.instanceAgeThreshold = 180
```

#### simianarmy.conformity.rule.InstanceHasStatusUrl.enabled
This property is used to enable the conformity rule to check whether all instances in the cluster
have a status url defined in Discovery/Eureka. The rule is added to Conformity Monkey only when
Eureka is enabled also.
```
simianarmy.conformity.rule.InstanceHasStatusUrl.enabled = true
```

#### simianarmy.conformity.rule.InstanceHasHealthCheckUrl.enabled
This property is used to enable the conformity rule to check whether all instances in the cluster
have a health check url defined in Discovery/Eureka. The rule is added to Conformity Monkey only when
Eureka is enabled also.
```
simianarmy.conformity.rule.InstanceHasHealthCheckUrl.enabled = true
```

#### simianarmy.conformity.rule.InstanceIsHealthyInEureka.enabled
This property is used to enable the conformity rule to check whether there are unhealthy instances
in the cluster in Discovery/Eureka. The rule is added to Conformity Monkey only when
Eureka is enabled also.
```
simianarmy.conformity.rule.InstanceIsHealthyInEureka.enabled = true
```

#### simianarmy.conformity.cluster.<name>.ownerEmail
You can override a cluster's owner email by providing a property here. For example, the line below overrides
the owner email of cluster foo to foo@bar.com
```
simianarmy.conformity.cluster.foo.ownerEmail = foo@bar.com
```

#### simianarmy.conformity.cluster.<name>.excludedRules
You can exclude specific conformity rules for a cluster using this property. For example, the line below excludes
the conformity rule rule1 and rule2 on cluster foo.
```
simianarmy.conformity.cluster.foo.excludedRules = rule1,rule2
```

#### simianarmy.conformity.cluster.<name>.optedOut
You can opt out a cluster completely from Conformity Monkey by using this property. After a cluster is opted out,
no notification is sent for it no matter it is conforming or not. For example, the line below opts out the cluster
foo.
```
simianarmy.conformity.cluster.foo.optedOut = true
```

