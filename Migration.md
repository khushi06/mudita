From time to time there are changes that are not backwards compatible. In order to facilitate the upgrade to a new version, here is a list of changes that need to be made in order to upgrade.

## Version 2.2 -> 2.3
We introduced an alternative client (VMWare in addition to AWS), thus some of the existing client config needs to be refactored.

These properties in version 2.2 in file simianarmy.properties

```
    simianarmy.aws.accountKey  
    simianarmy.aws.secretKey  
    simianarmy.aws.region  
```

moved to file client.properties and were renamed (added .client prefix) in version 2.3

```
    simianarmy.client.aws.accountKey  
    simianarmy.client.aws.secretKey  
    simianarmy.client.aws.region  
```

## Version 2.3 -> 2.4
After introducing Janitor Monkey in version 2.4, we re-structured the configuration files. Monkey specific settings are now in their own file. For Chaos Monkey it is **chaos.properties**, and for Janitor Monkey it is **janitor.properties**.

The property to specify the SimpleDB domain for storing the monkey events is now changed. Previously it was
```
simianarmy.sdb.domain
```

In the new version it is changed to 
```
simianarmy.recorder.sdb.domain
```

So please adjust your configuration accordingly.