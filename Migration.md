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

So please adjust your configuration accordingly.