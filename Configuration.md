Monkeys are configured using three configuration files. **simianarmy.properties** configures the global settings that can be shared by all monkeys. **client.properties** configures the client to be used (currently AWS and VSphere are supported). **\<monkey-name\>.properties** configures the behaviour of the specific monkey. For example, **chaos.properties** configures the behavior of Chaos Monkey, like which groups to terminate with which probability etc., and **janitor.properties** contains properties like which resource types to manage and what rules are used to find unused resources.

## Location of the property files
The system property **simianarmy.properties** specifies the location of the properties files used to configure the monkeys.  The default properties files "/simianarmy.properties", "/client.properties", and "\<monkey-name\>.properties" are located at the root of one of the classpath directories.  See the [defaults in the github repo](https://github.com/Netflix/SimianArmy/blob/master/src/main/resources/).
```
    $ java -Dsimianarmy.properties=/path/to/my/simianarmy.properties ...
```

## Customizing simianarmy.properties
**simianarmy.properties** contains the common settings that can be shared by all monkeys, e.g. the schedules of running monkeys or the calendar. You can check the details [here](Global-Settings).

## Customizing client.properties
**client.properties** configures the client to be used. Currently AWS and VSphere are supported. Example configuration file can be seen [here](Client-Settings).

## Customizing specific monkeys
You can also learn how to [Configure Chaos Monkey](Chaos-Settings) and [Configure Janitor Monkey](Janitor-Settings) so you can customize the monkeys to fit your business needs.
