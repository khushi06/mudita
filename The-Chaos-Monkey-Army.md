Disambiguation: For the electro-metal dance group that's all the rage in the underground scene, please consult [Rolling Stone](http://www.rollingstone.com/).
 
Originally the Netflix Chaos Monkey would just cleanly shut down an instance through the EC2 APIs.  In order to simulate more failure scenarios, there are now many different ways the chaos monkey can 'break' an instance, to simulate different types of failures.  If your application can cope with all of them, it is more likely to be able to cope with "unknown unknowns" failure scenarios.

After an instance is selected for termination by the chaos monkey, it chooses a chaos strategy randomly, from the list of enabled chaos strategies.  You can enable/disable a strategy by editing chaos.properties.  Also, some strategies are not always applicable; some may require SSH access or may only be applicable to instances with EBS volumes.  If the strategy isn't applicable, you don't have to worry, it just won't be chosen.

Here are the strategies:

### Shutdown instance _(Simius Mortus)_

Shuts down the instance using the EC2 API.  The classic chaos monkey strategy.

Enable with:  ```simianarmy.chaos.shutdowninstance.enabled = true```

### Block all network traffic _(Simius Quies)_

This removes all security groups from the instance, and moves it into a security group that does not allow any access.  Thus the instance is running, but cannot be reached via the network.  This can only work on VPC instances.

Enable with:  ```simianarmy.chaos.blockallnetworktraffic.enabled = true```

Optionally configure the security group to use:  ```simianarmy.chaos.blockallnetworktraffic.group = blocked-network``` (blocked-network is the default value)

The security group is auto-created; if the security group already exists, it will be used as-is.  So if you wanted to leave e.g. port 22 open for diagnostic purposes, add it to the configured security group.


### Detach all EBS volumes _(Simius Amputa)_

This force-detaches all EBS volumes from the instance, simulating an EBS failure.  Thus the instance is running, but EBS disk I/O will fail.

Enable with:  ```simianarmy.chaos.detachvolumes.enabled = true```

Warning: this monkey may cause data loss.  Use this one carefully.

### SSH monkeys

The remaining monkeys all work by running scripts on the instance.  To configure, you must set the SSH key to use to log in to the instance.
 
SSH username:  ```simianarmy.chaos.ssh.user = root``` (The default is root)
SSH key path: ```simianarmy.chaos.ssh.key = ~/monkeyboy/.ssh/id_rsa```  (path to your SSH key)


### Burn-CPU _(Simius Cogitarius)_

This monkey runs CPU intensive processes, simulating a noisy neighbor or a faulty CPU.  The instance will effectively have a much slower CPU.

Enable with:  ```simianarmy.chaos.burncpu.enabled = true```
Requires SSH to be configured.

### Burn-IO _(Simius Occupatus)_

This monkey runs disk intensive processes, simulating a noisy neighbor or a faulty disk.  The instance will effectively have a much slower disk.

Enable with:  ```simianarmy.chaos.burnio.enabled = true```
Requires SSH to be configured.


### Fill Disk _(Simius Plenus)_

This monkey writes a huge file to the root device, filling up the (typically relatively small) EC2 root disk.

Enable with:  ```simianarmy.chaos.filldisk.enabled = true```
Requires SSH to be configured.

### Kill Processes _(Simius Delirius)_

This monkey kills any java or python programs it finds every second, simulating a faulty application, corrupted installation or faulty instance.  The instance is fine, but the java/python application running on it will fail continuously.

Enable with:  ```simianarmy.chaos.killprocesses.enabled = true```
Requires SSH to be configured.

### Null-Route _(Simius Desertus)_

This monkey null-routes the 10.0.0.0/8 network, which is used by the EC2 internal network.  All EC2 <-> EC2 network traffic will fail.

Enable with:  ```simianarmy.chaos.nullroute.enabled = true```
Requires SSH to be configured.

### Fail DNS _(Simius Nonomenius)_

This monkey uses iptables to block port 53 for TCP & UDP; those are the DNS traffic ports.  This simulates a failure of your DNS servers.

Enable with:  ```simianarmy.chaos.faildns.enabled = true```
Requires SSH to be configured.

### Fail EC2 API _(Simius Noneccius)_

This monkey puts dummy host entries into /etc/hosts, so that all EC2 API communication will fail.  This simulates a failure of the EC2 control plane.  Of course, if your application doesn't use the EC2 API from the instance, then it will be completely unaffected.

Enable with:  ```simianarmy.chaos.failec2.enabled = true```
Requires SSH to be configured.

### Fail S3 API _(Simius Amnesius)_

This monkey puts dummy host entries into /etc/hosts, so that all S3 communication will fail.  This simulates a failure of S3.  Of course, if your application doesn't use S3, then it will be completely unaffected.

Enable with:  ```simianarmy.chaos.fails3.enabled = true```
Requires SSH to be configured.

### Fail DynamoDB API _(Simius Nodynamus)_

This monkey puts dummy host entries into /etc/hosts, so that all DynamoDB communication will fail.  This simulates a failure of DynamoDB.  As with its monkey relatives, this will only affect instances that use DynamoDB.

Enable with:  ```simianarmy.chaos.faildynamodb.enabled = true```
Requires SSH to be configured.


### Network Corruption _(Simius Politicus)_

This monkey uses the traffic shaping API to corupt a large fraction of network packets.  This simulates degradation of the EC2 network.

Enable with:  ```simianarmy.chaos.networkcorruption.enabled = true```
Requires SSH to be configured.

### Network Latency _(Simius Tardus)_

This monkey uses the traffic shaping API to introduce latency (1 second +- 50%) to all network packets.  This simulates degradation of the EC2 network.

Enable with:  ```simianarmy.chaos.networklatency.enabled = true```
Requires SSH to be configured.

### Network Loss _(Simius Perditus)_

This monkey uses the traffic shaping API to drop a fraction of all network packets.  This simulates degradation of the EC2 network.

Enable with:  ```simianarmy.chaos.networkloss.enabled = true```
Requires SSH to be configured.


# Grow your own chaos monkey

It is now easy to write your own chaos monkey, by simply writing a script and plugging it in.

1. Choose a name for your chaos type, like "BurnIo" or "NetworkLatency".  We'll call that <key>
1. Create your script src/main/resources/scripts/<key>.sh, adding your own brand of chaos.  This script will be run on the instance.  Copy one of the existing scripts.
1. Create a wrapper classes e.g. src/main/java/com/netflix/simianarmy/chaos/<key>ChaosType.java.  Copy e.g. BurnCpuChaosType.java.  The second parameter to the superclass constructor should be "<key>".
1. Add an instance of your class to the allChaosTypes list that is built in the constructor of src/main/java/com/netflix/simianarmy/basic/chaos/BasicChaosMonkey.java
1. Set your chaos type to be enabled by setting ```simianarmy.chaos.<key>.enabled=true``` in src/main/resources/chaos.properties.
1. ???
1. Profit

Steps 6 and 7 are optional.  Step 1 is often the hardest.
