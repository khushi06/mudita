## client.properties
This property file is used to configure which client and context to use.  
The default implementation is to use an AWS Client, equaling an optional property like the following:
```
    simianarmy.client.context.class=com.netflix.simianarmy.basic.BasicContext
```
In order to use the AWS client, skip this property and use the default.
In order to use the VSphere Client instead, change this property to
```
    simianarmy.client.context.class=com.netflix.simianarmy.client.vsphere.VSphereContext
```
### AWS Client Properties
These are the properties needed to access Amazon EC2, Amazon Auto Scaling and Amazon SimpleDB. You have to configure this if you use the AWS client.
Even if the VSphere Client is used instead of the AWS client,  the ```simianarmy.client.aws.*``` and ```simianarmy.sdb.domain``` properties still have to be configured since ChaosMonkey uses these credentials to read and write state data to an Amazon SimpleDB.
 
#### simianarmy.client.aws.accountKey
The Access Key Id from [AWS Security Credentials](https://portal.aws.amazon.com/gp/aws/securityCredentials).
```
    simianarmy.client.aws.accountKey = accountKey
```

If left empty, the AWS SDK will attempt to receive the accountKey from a Environment variable, a Java System property or the Meta data service in case the policy has been set as an Instance Role.
See [AWS Roles in Java](http://docs.aws.amazon.com/AWSSdkDocsJava/latest/DeveloperGuide/java-dg-roles.html) for more details.

#### simianarmy.client.aws.secretKey
The Secret Access Key Id from [AWS Security Credentials](https://portal.aws.amazon.com/gp/aws/securityCredentials).
```
    simianarmy.client.aws.secretKey = secretKey
```

Same as the accountKey the secretKey can also be left empty or blank such that it can be received through the AWS SDK.

#### simianarmy.client.aws.region
This specifies the Amazon region where the Monkeys will run.  It is expected that if you want to run the monkeys in multiple regions then you will a separate installation of SimianArmy in each region.  The default region is "us-east-1".
```
    simianarmy.client.aws.region = us-east-1
```
#### simianarmy.client.aws.assumeRoleArn
If the Monkeys are to run under an assumed role, this will be the ARN of the role to be assumed.
All actions will be performed under this assumed role. There is some additional set up required within AWS (the sts:AssumeRole action must be allowed on this role), see [AssumeRole API](http://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) for more details

```
   simianarmy.client.aws.assumeRoleArn = arn:aws:iam::ACCOUNT:role/ROLE
```

#### simianarmy.client.aws.accountName
Common account name to make it easier to identify emails by subject.  If you are running multiple SimianArmy instances in multiple accounts you will get multiple emails a day from Janitor Monkey.  By default it can be hard to determine which account the instances, ASG's, etc, pertain to in each email. By setting this property you can see an account identifier in the Janitor Monkey emails. 

```
simianarmy.client.aws.accountName = 
```

#### simianarmy.client.aws.proxy
Use these properties if a proxy is needed to connect to AWS APIs.
proxyHost and proxyPort are required to connect through a proxy.  proxyUser and proxyPassword are optional.

```
simianarmy.client.aws.proxyHost=
simianarmy.client.aws.proxyPort=
simianarmy.client.aws.proxyUser=
simianarmy.client.aws.proxyPassword=
```

### VSpehere Client Properties
When using the VSphere Client, the client has to be configured with the following VSphere Client specific properties. As stated above, in addition the aws client properties to access the Amazon SimpleDB also have to be correctly configured.

#### simianarmy.client.vsphere.url
This is the URL to the API of your VSphere instance. The client will use this URL to open connections and issue termination commands.
```
    simianarmy.client.vsphere.url=https://YOUR_VSPHERE_SERVER/sdk
```
#### simianarmy.client.vsphere.username
The username used to authenticate against the API of your VSphere instance.
```
    simianarmy.client.vsphere.username=YOUR_SERVICE_ACCOUNT_USERNAME
```
#### simianarmy.client.vsphere.password
The password used to authenticate against the API of your VSphere instance.
```
    simianarmy.client.vsphere.password=YOUR_SERVICE_ACCOUNT_PASSWORD
```
#### simianarmy.client.vsphere.terminationStrategy.property.name
The VSpehere client uses a TerminationStrategy for terminating VirtualMachines. There currently is only one termination strategy. This ```PropertyBasedTerminationStrategy``` sets a property in the VirtualMachine to a specific value as a trigger and restets the VM. The underlying infrastructure automation is responsible to respond to the trigger by deleting the VM or kickstarting it for reinstallation.
You can configure which property and value will be set prior to resetting the VirtualMachine.

#### simianarmy.client.vsphere.terminationStrategy.property.name
The name of the property to set. The default is "Force Boot".
```
    simianarmy.client.vsphere.terminationStrategy.property.name=Force Boot
```
#### simianarmy.client.vsphere.terminationStrategy.property.value
The value of the property to set. The default is "server".
```
    simianarmy.client.vsphere.terminationStrategy.property.value=server
```