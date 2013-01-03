### Volume Tagging Monkey Properties
With these properties you can control how volume tagging monkey operates. The monkey is a companion of Janitor Monkey for tagging EBS volumes with attachment information which Janitor Monkey can use for cleaning up unused volumes.

#### simianarmy.volumeTagging.enabled
This property allows the Volume Tagging Monkey to run. The default is "true". Note that an enabled Volume Tagging Monkey does not mean it is also allowed to tag volumes. It must also have the leashed property set to "false".
```
simianarmy.volumeTagging.enabled = true
```

#### simianarmy.volumeTagging.leashed
This is effectively a "dryrun" option. If leashed is "true" then it will look volumes and simulates the tagging process, but it will not be able to really change the tags of the volumes. The default value is "true".
```
simianarmy.volumeTagging.leashed = true
```

#### simianarmy.volumeTagging.ownerEmailDomain
The monkey needs to find the owner email of an volume. If the owner is in the form of the alias without the domain name, you can set this property to convert the owner alias to a valid email address. If this property is not set, the alias will be used directly to tag the volume.
```
simianarmy.volumeTagging.ownerEmailDomain = foo.com
```

#### simianarmy.calendar.isMonkeyTime
To make sure that the volumes are tagged with accurate information about their attachment, ideally the volume tagging monkey should always run.
```
simianarmy.calendar.isMonkeyTime = true
```
