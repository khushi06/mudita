## Setup Auto Scaling Group
First we need to set up an Auto Scaling Group, otherwise Chaos Monkey will have nothing to attack.

### Get Auto Scaling Tools

You can use the new AWS CLI tools:
```shell
    $ sudo pip install awscli
```

If you prefer to use the old tools:
```shell
    $ wget http://ec2-downloads.s3.amazonaws.com/AutoScaling-2011-01-01.zip
    $ unzip AutoScaling-2011-01-01.zip
    $ cd AutoScaling-1.0.61.0/
    $ export AWS_AUTO_SCALING_HOME=`pwd` 
```

Instructions for both are provided.

### Setup Environment

If using the new CLI tools, edit ~/.aws/config:

```
[default]
aws_access_key_id = your_acount_key
aws_secret_access_key = your_secret_key
region = us-west-2
```

If using the old CLI:
```shell
    $ export AWS_AUTO_SCALING_URL=http://autoscaling.us-west-2.amazonaws.com
    $ export ACCOUNT_KEY=your_account_key
    $ export SECRET_KEY=your_secret_key
```
* **Note:** You can find your account key and secret key on your AWS accounts [Security Credentials](https://portal.aws.amazon.com/gp/aws/securityCredentials) page
* **Note:** This example assumes us-west-2 region is being used.

### Create Launch Config
Using the new CLI:
```shell
aws autoscaling create-launch-configuration --launch-configuration-name lc1 --instance-type t1.micro --image-id ami-fcf27fcc --key-name <keyname>
```

--keyname <keyname> is optional, but recommended if you want to SSH into your instances.

Using the old CLI:
```shell 
    $ $AWS_AUTO_SCALING_HOME/bin/as-create-launch-config lc1 --instance-type t1.micro -I $ACCOUNT_KEY -S $SECRET_KEY --image-id ami-fcf27fcc
    OK-Created launch config
```

* **Note:** ami-fcf27fcc is a public ami which contains "Debian 6.0 Squeeze i386 image".  The contents of the ami are not relevant for this Quick Start.  If you are trying to run these examples for a region other than us-west-2, you will need to find a different ami that is available for the region of your choice.  You can find available public amis to test with from the AWS EC2 Management Console.

### Create Auto Scaling Group
Using the new CLI:
```shell
aws autoscaling create-auto-scaling-group --auto-scaling-group-name monkey-target --launch-configuration-name lc1 --availability-zones us-west-2a --min-size 1 --max-size 1
```

Using the old CLI:
```shell
    $ $AWS_AUTO_SCALING_HOME/bin/as-create-auto-scaling-group monkey-target -I $ACCOUNT_KEY -S $SECRET_KEY --launch-configuration lc1 --availability-zones us-west-2a --min-size 1 --max-size 1
    OK-Created AutoScalingGroup
```
* **Note:**  the availability-zone should be set to a zone available to your account.

### See Auto Scaling Group running

Using the new CLI:
```shell
aws autoscaling describe-auto-scaling-groups --output text
```

Using the old CLI:
```shell
    $ $AWS_AUTO_SCALING_HOME/bin/as-describe-auto-scaling-groups -I $ACCOUNT_KEY -S $SECRET_KEY
    AUTO-SCALING-GROUP  monkey-target  lc1  us-west-2a  1  1  1
    INSTANCE  i-8b55fbb8  us-west-2a  InService  Healthy  lc1
```
* **Note:** It might take a few minutes before the instance is Healthy and InService

## Setup User or Role policies
Monkeys within the SimianArmy require permissions to a set of actions. The easiest start is to just set up a user for the monkey that has full permissions for ASG, EC2, SDB and SES, simpler still would be setting up one having full access. However you can of course use a more fine grained policy on the SimianArmy user, please see an example below:
```
{
  "Statement": [
    {
      "Sid": "Stmt1357739573947",
      "Action": [
        "ec2:CreateTags",
        "ec2:DeleteSnapshot",
        "ec2:DescribeImages",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
        "ec2:DescribeVolumes",
        "ec2:TerminateInstances",
        "ses:SendEmail",
        "elasticloadbalancing:*"  
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "Stmt1357739649609",
      "Action": [
        "autoscaling:DeleteAutoScalingGroup",
        "autoscaling:DescribeAutoScalingGroups",
        "autoscaling:DescribeAutoScalingInstances",
        "autoscaling:DescribeLaunchConfigurations"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "Stmt1357739730279",
      "Action": [
        "sdb:BatchDeleteAttributes",
        "sdb:BatchPutAttributes",
        "sdb:DomainMetadata",
        "sdb:GetAttributes",
        "sdb:PutAttributes",
        "sdb:ListDomains",
        "sdb:CreateDomain",
        "sdb:Select"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```
### Permissions through Instance Roles

If you prefer using Instance Roles to set your access policy, you can do so by leaving the simianarmy.client.aws.accountKey and simianarmy.client.aws.secretKey blank or removing the property entirely. More details are seen in [[Client Settings]] and the [AWS User guide on Instance role policies](http://docs.amazonwebservices.com/IAM/latest/UserGuide/role-usecase-ec2app.html).

## Setup SimpleDB Table

### Create SIMIAN_ARMY SimpleDB Table manually

With a recent code merge, the SimpleDB should now be created automatically.  However, if you do need to create it manually for some reason, here is how to do so.

There does not appear to be a simple free toolset offered by Amazon for SimpleDB interaction.  Here is a shell function that does most of what is needed for our setup.  For some reason the signature generation does not seem to work reliably, so if you get an error from the SimpleDB service, try it a few times.
```shell
    $ sdb() {
        case $(uname -s) in
            Darwin) timestamp=$(date -uj  -f %s $(($(date +%s) - (5 * 60)))  +"%FT%TZ" | sed s/:/%3A/g);;
            Linux)  timestamp=$(date -u +"%FT%TZ" --date='5 min ago' | sed s/:/%3A/g);;
        esac
        params="AWSAccessKeyId=$ACCOUNT_KEY"
        params="$params&Action=$1"
        [ -z "$2" ] || params="$params&DomainName=$2"
        params="$params&SignatureMethod=HmacSHA256"
        params="$params&SignatureVersion=2"
        params="$params&Timestamp=$timestamp"
        params="$params&Version=2009-04-15"
        payload="GET\nsdb.us-west-2.amazonaws.com\n/\n$params"
        hash=$(echo -ne $payload | openssl dgst -sha256 -hmac "$SECRET_KEY" -binary | base64)
        curl -H application/x-www-form-urlencoded "https://sdb.us-west-2.amazonaws.com/?$params&Signature=$hash"
    }
```
Now use the sdb function to create the necessary SIMIAN_ARMY domain
```shell
    $ sdb CreateDomain SIMIAN_ARMY
    <?xml version="1.0"?>
    <CreateDomainResponse xmlns="http://sdb.amazonaws.com/doc/2009-04-15/"><ResponseMetadata><RequestId>XXXXXXXX</RequestId><BoxUsage>X.XXXXXXX</BoxUsage></ResponseMetadata></CreateDomainResponse>
    
    $ sdb ListDomains
    <?xml version="1.0"?>
    <ListDomainsResponse xmlns="http://sdb.amazonaws.com/doc/2009-04-15/"><ListDomainsResult><DomainName>SIMIAN_ARMY</DomainName></ListDomainsResult><ResponseMetadata><RequestId>XXXXXXXX</RequestId><BoxUsage>X.XXXXXXXXX</BoxUsage></ResponseMetadata></ListDomainsResponse>
```
* **Note:** If you are not using the us-west-2 region then you will need to change the SimpleDB URI in the above sdb function to correspond to your region.  See [this document](http://docs.amazonwebservices.com/general/latest/gr/rande.html#sdb_region) for SimpleDB Region endpoints.
* **Note:** The sdb function above is a hack, it sort of works most of the time.  If you know of a better way to create a SimpleDB table, then use it.  [Asgard](http://techblog.netflix.com/2012/06/asgard-web-based-cloud-management-and.html) allows you to trivially create SimpleDB tables.

## Setup Simple Email Service account 
If you want to receive email notifications then you need to use a validated email account in SES. Using the console setup and validate any to/from email addresses or validate your domain for the us-east-1 region.

* **Note:** The scripts currently send email through us-east-1 even if your configuration is changed to a different region.

## Build the Monkeys with Gradle
* First check out the code from github and then build with gradle. Make sure you are using a Java 8 JDK or newer. Building requires Java 8 because of dependent libraries that are Java 8, but the source and target compatibility are still set to 1.7.
```shell
    $ git clone git://github.com/Netflix/SimianArmy.git
    Cloning into 'SimianArmy'...
    remote: Counting objects: 968, done.
    remote: Compressing objects: 100% (504/504), done.
    remote: Total 968 (delta 383), reused 784 (delta 199)
    Receiving objects: 100% (968/968), 175.08 KiB | 84 KiB/s, done.
    Resolving deltas: 100% (383/383), done.
    $ cd SimianArmy
    $ ./gradlew build
    :clean
    :compileJava
    :processResources
    :classes
    :jar
    :javadoc
    :javadocJar
    :sourcesJar
    :war
    :assemble
    :checkstyleMain
    :compileTestJava
    :processTestResources
    :testClasses
    :checkstyleTest
    :findbugsMain
    :findbugsTest
    :pmdMain
    :pmdTest
    :test
    :check
    :build
    
    BUILD SUCCESSFUL
    Total time: 43.294 secs
```
* For testing modify the simianarmy.properties and client.properties file
```shell
    vi src/main/resources/client.properties
```
    * set simianarmy.aws.secretKey
    * set simianarmy.aws.account_key
    * set simianarmy.aws.region
```shell
    vi src/main/resources/simianarmy.properties
```
    * set simianarmy.calendar.isMonkeyTime=true (if on weekend or not in 9-3pm)

* For Chaos Monkey, modify chaos.properties file
```shell
    vi src/main/resources/chaos.properties
```
    * set simianarmy.chaos.ASG.enabled=false
    * set simianarmy.chaos.ASG.monkey-target.enabled=true
    * set simianarmy.chaos.ASG.monkey-target.probability=1.0

* **Note:** The simianarmy.chaos.leashed=true setting should still be set, this will run Chaos Monkey in a test-only
  mode, no terminations will be done.  To see what the configuration options mean you can check out [Chaos Monkey Configuration](Chaos-Settings).

* For Janitor Monkey, modify janitor.properties file
```shell
    vi src/main/resources/janitor.properties
```
By default Janitor Monkey is going to run in the test-only mode, no deletions or markings of resources will be done. The monkey looks for cleanup candidates in instances, EBS volumes, EBS snapshots, and auto scaling groups under the AWS account using all available rules. You can see the logs of running in on the console output. To see the Janitor Monkey rules and what the configuration options mean you can check out [Janitor Monkey Configuration](Janitor-Settings).

* For Conformity Monkey, modify conformity.properties file
```shell
    vi src/main/resources/conformity.properties
```
By default Conformity Monkey is going to run in the dryrun mode, no data is saved to DB and not email notification about unconforming clusters is sent to the cluster owners.
It does perform conformity checks and you can see the logs of running in on the console output. To see the Conformity Monkey rules and what the configuration options mean you
can check out [Conformity Monkey Configuration](Conformity-Settings).

* For running the volume tagging monkey that is used to tag volumes with attachment information, modify volumeTagging.properties file
```shell
    vi src/main/resources/volumeTagging.properties
```
To see what the configuration options mean you can check out [Volume Tagging Monkey Configuration](VolumeTagging-Settings).

* Run the gradle jetty server to start up Chaos Monkey, Janitor Monkey, Conformity Monkey, and the Volume Tagging Monkey. Below only shows the output of running Chaos Monkey as example, by default you will also see the running result of other monkeys.
```shell
    $ ./gradlew jettyRun
    :compileJava UP-TO-DATE
    :processResources UP-TO-DATE
    :classes UP-TO-DATE
    :jettyRun
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:/Users/cbennett/.gradle/wrapper/dists/gradle-1.0-milestone-9-bin/7ilkmgo2rn79vvfvd51rqf17ks/gradle-1.0-milestone-9/lib/logback-classic-1.0.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/Users/cbennett/.gradle/caches/artifacts-8/filestore/org.slf4j/slf4j-log4j12/1.6.1/jar/bd245d6746cdd4e6203e976e21d597a46f115802/slf4j-log4j12-1.6.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    2012-07-26 15:43:55.129 - INFO  MonkeyRunner - [MonkeyRunner.java:56] Starting CHAOS Monkey
    2012-07-26 15:43:55.475 - INFO  Monkey - [Monkey.java:107] CHAOS Monkey Running ...
    2012-07-26 15:43:55.689 - INFO  BasicChaosMonkey - [BasicChaosMonkey.java:89] Group monkey-target [type ASG] enabled [prob 1.0]
    2012-07-26 15:43:55.690 - INFO  BasicChaosInstanceSelector - [BasicChaosInstanceSelector.java:57] Group monkey-target [type ASG] got lucky: 0.8292385537513608 > 0.16666666666666666
```
* You will probably see "got lucky" a few times.  The default configuration is to run 6 times a day.  The probability of 1.0 is the daily probability, so each run will have 1.0/6 probability (ie 0.1666).  To get a termination you can restart a few times, it should take on average 6 tries.  If a termination happens another one will not happen again today for that group.  To guarantee that a termination happens, you can set the probability to "6.0" (ie 600% which will make sure it kills an instance on the first run).

Probably after a few restarts you will see:
```
    2012-07-26 15:49:18.650 - INFO  BasicChaosMonkey - [BasicChaosMonkey.java:89] Group monkey-target [type ASG] enabled [prob 1.0]
    2012-07-26 15:49:18.651 - INFO  BasicChaosMonkey - [BasicChaosMonkey.java:94] leashed ChaosMonkey prevented from killing i-0156f832 from group monkey-target [ASG], set simianarmy.chaos.leashed=false
```

Now Unleash the Chaos monkey:
```shell
    vi src/main/resources/chaos.properties
```

  * set simianarmy.chaos.leashed=false

Restart the jettyRun and you should see something like:
```
    2012-07-26 16:08:25.517 - INFO  BasicChaosMonkey - [BasicChaosMonkey.java:89] Group monkey-target [type ASG] enabled [prob 6.0]
    2012-07-26 16:08:26.101 - INFO  BasicChaosMonkey - [BasicChaosMonkey.java:105] Terminated i-8b55fbb8 from group monkey-target [ASG]
```

* After some time you can re-run the describe auto-scaling-group command:
```shell
    $ $AWS_AUTO_SCALING_HOME/bin/as-describe-auto-scaling-groups -I $ACCOUNT_KEY -S $SECRET_KEY
    AUTO-SCALING-GROUP  monkey-target  lc1  us-west-2a  1  1  1
    INSTANCE  i-0951ff3a  us-west-2a  InService  Healthy  lc1
```
* **Note:** It took several minutes for the above command to report a change for me.  To get a more current view you can look at your Instances in the AWS EC2 Management Console.

With the jettyRun still running, you can test the API in a different shell:
```shell
    $ curl http://localhost:8080/simianarmy/api/v1/chaos
    [{"monkeyType":"CHAOS","eventType":"CHAOS_TERMINATION","eventTime":1343344105651,"region":"us-west-2","groupType":"ASG","groupName":"monkey-target"}]
```

If you restart jettyRun again it will not start Chaos Monkey immediately, it will see that a CHAOS Event happened within the last hour (Chaos Monkey runs hourly) and delay the next Chaos run.  You will see something like this:
```
2012-07-26 16:15:18.156 - INFO  BasicScheduler - [BasicScheduler.java:101] Detected previous events within cycle, setting CHAOS start to Thu Jul 26 17:08:25 PDT 2012
```
This is to prevent there case where the server is quickly restarted after a previous run, we don't want to let Chaos Monkey kill too much too rapidly.

## Cleanup
If you don't plan to run Chaos Monkey again for a while, you can delete the SimpleDB table with:
```shell
    $ sdb DeleteDomain SIMIAN_ARMY
```

Then you can get rid of the monkey-target test ASG.  The first step is to reduce the instance count to zero:

New tools:
```shell
aws autoscaling update-auto-scaling-group --auto-scaling-group-name monkey-target --min-size 0 --max-size 0
```

Old tools:
```shell
    $ $AWS_AUTO_SCALING_HOME/bin/as-update-auto-scaling-group --name monkey-target -I $ACCOUNT_KEY -S $SECRET_KEY --min-size 0 --max-size 0
    OK-Updated AutoScalingGroup
```
Then poll with **as-describe-auto-scaling-groups** as above until there are no instances ready. After the instances have been terminated then you can delete the ASG:

New tools:
```shell
aws autoscaling delete-auto-scaling-group --auto-scaling-group-name monkey-target
```

Old tools:
```shell
    $ $AWS_AUTO_SCALING_HOME/bin/as-delete-auto-scaling-group -I $ACCOUNT_KEY -S $SECRET_KEY --auto-scaling-group monkey-target
    
        Are you sure you want to delete this AutoScalingGroup? [Ny]y
    OK-Deleted AutoScalingGroup
```

Finally delete the *lc1* launch config we created:

Old tools:
```shell
aws autoscaling delete-launch-configuration --launch-configuration-name lc1 
```

New tools:
```shell
    $ $AWS_AUTO_SCALING_HOME/bin/as-delete-launch-config lc1 -I $ACCOUNT_KEY -S $SECRET_KEY
    
        Are you sure you want to delete this launch configuration? [Ny]y
    OK-Deleted launch configuration
```