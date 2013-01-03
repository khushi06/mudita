### Janitor Properties
With these properties you can control how janitor monkey operates.

#### Customizing Janitor Monkey's schedule
By default Janitor Monkey wakes up every hour and runs only between 11am and 12pm. The properties below override the values set in **simianarmy.properties**.
```
simianarmy.scheduler.frequency = 1
simianarmy.scheduler.frequencyUnit = HOURS
simianarmy.scheduler.threads = 1
simianarmy.calendar.openHour = 11
simianarmy.calendar.closeHour = 11
simianarmy.calendar.timezone = America/Los_Angeles
```
####simianarmy.calendar.isMonkeyTime
To ease debug/dryrun, you can set this property to true so the monkey will always be able to run. This property also overrides the value in **simianarmy.properties**. The default value is false.
```
simianarmy.calendar.isMonkeyTime = false
```

#### simianarmy.janitor.enabled
This property allows the Janitor Monkey to run.  The default is "true".  Note that an enabled Janitor Monkey does not mean it is also allowed to clean up resources.  It must also have the leashed property set to "false".
```
simianarmy.janitor.enabled = true
```

#### simianarmy.janitor.leashed
This is effectively a "dryrun" option.  If leashed is "true" then it will look for unused resources, but it will not be able to terminate clean up any resources. The default value is "true".
```
simianarmy.janitor.leashed = true
```

#### simianarmy.janitor.resources.sdb.domain
This property specifies the SimpleDB domain for storing the resources managed by the Janitor Monkey. The default is SIMIAN_ARMY.
```
simianarmy.janitor.resources.sdb.domain = SIMIAN_ARMY
```

#### simianarmy.janitor.notification.sourceEmail
This property specifies the source email address that is used to send notifications about resource cleanup. Its value needs to be a valid email address. If it is not set or is not a valid email address, no notification will be sent and no cleanup will be done.
```
simianarmy.janitor.notification.sourceEmail = foo@bar.com
```

#### simianarmy.janitor.summaryEmail.to
This property specifies the email address to receive the summary email of Janitor Monkey after each run. If it is not set or is invalid, no summary email about the Janitor Monkey run will be sent.
```
simianarmy.janitor.summaryEmail.to = foo@bar.com
```

#### simianarmy.janitor.notification.defaultEmail
This property specifies the email address to receive notification about cleanup when Janitor Monkey is not able to find the owner email of the resource it wants to clean up. When the value is not set or is not a valid email address, no notification will be sent and no cleanup will be done for the resource. When you first run Janitor Monkey, it is likely that you don't have owner emails set for your cloud resources. If you still want Janitor Monkey to be able to clean up resources, you can set this property to your team's email list so that your team members are able to receive Janitor Monkey notifications.
```
simianarmy.janitor.notification.defaultEmail = foo@bar.com
```

#### simianarmy.janitor.notification.daysBeforeTermination
This property specifies the number of business days that a notification is sent before the expected termination time. For example, if a resource is scheduled to be cleaned up by Janitor Monkey on 12/13/2012, Thursday and the property is set to 2, the owner will receive notification about the cleanup on 12/11/2012, Tuesday, which is 2 business days before the termination date. If you want Janitor Monkey to clean up any unused resources without wait, you can set the value to 0. The reason to wait for a few days before a resource is cleaned up is because the resource owner may want to keep the resource longer even it is not needed for now. The owner can use our REST API to flag the resource as 'not to clean' by Janitor Monkey during this period. The default value of this property is 2.

Details of flagging a resource as not being managed by Janitor Monkey (opt out) or bring a resource back to be managed by Janitor Monkey (opt in) can be found at [Janitor REST API](REST)
```
simianarmy.janitor.notification.daysBeforeTermination = 2
```

#### simianarmy.janitor.enabledResources
This property specifies the resource types that Janitor Monkey manages. Currently Janitor Monkey can clean up instances, auto scaling groups, EBS volumes, and EBS snapshots. By default all these resource types are enabled for Janitor Monkey to manage.
```
simianarmy.janitor.enabledResources =  Instance, ASG, EBS_Volume, EBS_Snapshot
```
### Setting rule of cleaning up instances not in auto scaling group
The properties below are used to configure the rule used to clean up orphaned instances that are not in any auto scaling group.

#### simianarmy.janitor.rule.orphanedInstanceRule.enabled
This property specifies whether Janitor monkey will clean up orphaned instances. If you do not want to clean up orphaned instances, you can set the property to false to disable the rule. The default value is true.
```
simianarmy.janitor.rule.orphanedInstanceRule.enabled = true
```
#### simianarmy.janitor.rule.orphanedInstanceRule.instanceAgeThreshold
An orphaned instance is marked as cleanup candidate if it has launched for more than the number of days specified in this property. The default value is 2, which means orphaned instance is marked as cleanup candidate and scheduled to be terminated if it has launched for more than 2 days. 
```
simianarmy.janitor.rule.orphanedInstanceRule.instanceAgeThreshold = 2
```

#### simianarmy.janitor.rule.orphanedInstanceRule.retentionDaysWithOwner
This property specifies the number of business days the instance is retained after a notification is sent about the termination when the instance has an owner. The default value is 3.
```
simianarmy.janitor.rule.orphanedInstanceRule.retentionDaysWithOwner = 3
```

#### simianarmy.janitor.rule.orphanedInstanceRule.retentionDaysWithoutOwner
This property specifies the number of business days the instance is retained after a notification is sent about the termination when the instance does not have an owner. The default value is 8.
```
simianarmy.janitor.rule.orphanedInstanceRule.retentionDaysWithoutOwner = 8
```

### Setting rule of cleaning up detached EBS volumes
The properties below are used to configure the rule used for cleaning up volumes that have been detached from instances for certain days. Since AWS does not provide a way to track when an EBS volume is detached, we provide a companion monkey called **Volume Tagging Monkey** for tracking this information. You can modify **volumeTagging.properties** file to enable it. Details can be found at [Configure Volume Tagging Monkey](VolumeTagging-Settings).

#### simianarmy.janitor.rule.oldDetachedVolumeRule.enabled
This property specifies whether Janitor monkey will clean up detached volumes. If you do not want to clean up detached volumes, you can set the property to false to disable the rule. The default value is true.
```
simianarmy.janitor.rule.oldDetachedVolumeRule.enabled = true
```

#### simianarmy.janitor.rule.oldDetachedVolumeRule.detachDaysThreshold
A volume is considered a cleanup candidate after being detached for the number of days specified in this property. The default value is 30.
```
simianarmy.janitor.rule.oldDetachedVolumeRule.detachDaysThreshold = 30
```

#### simianarmy.janitor.rule.oldDetachedVolumeRule.retentionDays
This property specifies the number of business days the volume is retained after a notification is sent about the termination. The default value is 7.
```
simianarmy.janitor.rule.oldDetachedVolumeRule.retentionDays = 7
```

### Setting rule of cleaning up unreferenced snapshots
The properties below are used to configure the rule used for cleaning up snapshots that have no existing images generated from them and launched for certain days.

#### simianarmy.janitor.rule.noGeneratedAMIRule.enabled
This property specifies whether Janitor monkey will clean up unreferenced snapshots. You can set the property to false to disable the rule. The default value is true.
```
simianarmy.janitor.rule.noGeneratedAMIRule.enabled = true
```

#### simianarmy.janitor.rule.noGeneratedAMIRule.ageThreshold
A unreferenced snapshot is considered a cleanup candidate after launching for the number of days specified in this property. The default value is 30.
```
simianarmy.janitor.rule.noGeneratedAMIRule.ageThreshold = 30
```

#### simianarmy.janitor.rule.noGeneratedAMIRule.retentionDays
This property specifies the number of business days the snapshot is retained after a notification is sent about the termination. The default value is 7.
```
simianarmy.janitor.rule.noGeneratedAMIRule.retentionDays = 7
```

### Setting rule of cleaning up empty auto scaling groups
The properties below are used to configure the rule used for cleaning up auto scaling groups that have no active instances and the launch configuration is more than certain days old.

#### simianarmy.janitor.Eureka.enabled
The property below specifies whether or not Eureka/Discovery is available for Janitor monkey to use. Discovery/Eureka is used in the rules for cleaning up auto scaling groups to determine if an auto scaling group has an 'active' instance, i.e. an instance that is registered and up in Discovery/Eureka. You should set this property to false if you do not have Discovery/Eureka set up in your environment. The default value of this property is false.
```
simianarmy.janitor.Eureka.enabled = false
```

#### simianarmy.janitor.rule.oldEmptyASGRule.enabled
This property specifies whether Janitor monkey will clean up empty auto scaling groups. You can set the property to false to disable the rule. The default value is true.
```
simianarmy.janitor.rule.oldEmptyASGRule.enabled = true
```

#### simianarmy.janitor.rule.oldEmptyASGRule.launchConfigAgeThreshold
An an auto-scaling group without active instances is considered a cleanup candidate when its launch configuration is older than the number of days specified in this property. The default value is 50.
```
simianarmy.janitor.rule.oldEmptyASGRule.launchConfigAgeThreshold = 50
```

#### simianarmy.janitor.rule.oldEmptyASGRule.retentionDays
The number of business days an empty auto-scaling group is retained after a notification is sent for the termination. The default value is 10.
```
simianarmy.janitor.rule.oldEmptyASGRule.retentionDays = 10
```

### Setting rule of cleaning up suspended auto scaling groups
The properties below are used to configure the rule used for cleaning up auto-scaling groups that have no active instances and have been suspended from the associated ELB traffic for certain days.

#### simianarmy.janitor.rule.suspendedASGRule.enabled
This property specifies whether Janitor monkey will clean up suspended auto scaling groups. You can set the property to false to disable the rule. The default value is true.
```
simianarmy.janitor.rule.suspendedASGRule.enabled = true
```

#### simianarmy.janitor.rule.suspendedASGRule.suspensionAgeThreshold
An auto scaling group without active instances is considered a cleanup candidate when it has been suspended from the associated ELB traffic for the number of days specified in this property, the default value is 2.
```
simianarmy.janitor.rule.suspendedASGRule.suspensionAgeThreshold = 2
```

#### simianarmy.janitor.rule.suspendedASGRule.retentionDays
This property specifies the number of business days the auto scaling group is retained after a notification is sent for the termination. The default value is 5.
```
simianarmy.janitor.rule.suspendedASGRule.retentionDays = 5
```

