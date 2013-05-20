## What is Conformity Monkey?

Conformity Monkey is a service which runs in the Amazon Web Services (AWS) cloud looking for instances that are not conforming to predefined
rules for the best practices. The design of Conformity Monkey is flexible enough to allow extending it to work with other cloud providers
and cloud resources. By default, conformity check is performed every hour. The schedule can be easily re-configured to fit your business' need.

Conformity Monkey determines whether an instance is nonconforming by applying a set of rules on it. If any of the rules determines that the
instance is not conforming, the monkey sends an email notification to the owner of the instance. We provide a collection of conformity rules
in the open sourced version that are currently used at Netflix and believed general enough to be used by most users. The design of Conformity
Monkey also makes it simple to customize rules or to add new ones.

There can be exceptions when you want to ignore warnings of a specific conformity rule for some applications. For example, a security group
to open a specific port probably is not needed by instances of some applications. We allow you to customize the set of conformity rules to be
applied to a cluster of instances by excluding unneeded ones. Or you can completely opt out a specific cluster of Conformity Monkey so no
notification is sent for it.


## Why Run Conformity Monkey?

Cloud computing makes it much easier to launch new applications or start new instances. At Netflix, engineers can easily launch a new application
in Asgard with a few clicks. With this freedom there are sometimes consequences where launched applications or instances may not follow some best
practices, maybe the engineer did not know of the best practice or simply forgot about it. For example, some required security groups may be missing
from instances and can cause security gaps. Or perhaps a health check url is not defined for instances in Eureka which would result in automatic
failure detection and failover being disabled.

## How Conformity Monkey Works?

Conformity Monkey works in two stages: mark and notify. First, Conformity Monkey loops through all autoscaling groups in your cloud and applies
the specified set of conformity rules to the instances in each group. If any conformity rule determines an instance as not conforming, the autoscaling
group is marked as nonconforming and the instances that break the rule are recorded. Every autoscaling group is associated with an owner email,
which can be obtained from an internal system, or can be set in a configuration file. The simplest way is using a default email address,
e.g. your team's email list for all the autoscaling groups. Conformity Monkey sends email notification about the nonconforming groups to the owner,
with the details of the broken conformity rule and the instances that failed the conformity check. The application owners can then take necessary
actions to fix the failed instances or to exclude the conformity rule if they believe the conformity check is not necessary for the application.
We allow you to set different frequency for conformity check and notification. For example, at Netflix, conformity check is performed every hour,
and notification is only sent once per day at noon time. This is for reducing the number of emails people receive about the same conformity warning.
The real-time result of conformity check for every autoscaling group is shown in a separate UI.