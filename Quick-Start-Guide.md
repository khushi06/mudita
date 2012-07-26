
download http://ec2-downloads.s3.amazonaws.com/AutoScaling-2011-01-01.zip
unzip AutoScaling-2011-01-01.zip
cd AutoScaling-1.0.61.0/
export AWS_AUTO_SCALING_HOME=`pwd`
export AWS_AUTO_SCALING_URL=http://autoscaling.us-west-2.amazonaws.com

./bin/as-create-launch-config lc1 --instance-type t1.micro -I $ACCOUNT_KEY -S $SECRET_KEY --image-id ami-fcf27fcc

./bin/as-create-auto-scaling-group monkey-sacrafice -I $ACCOUNT_KEY -S $SECRET_KEY --launch-configuration lc1 --availability-zones us-west-2a --min-size 1 --max-size 1

$ ./bin/as-describe-auto-scaling-groups -I $ACCOUNT_KEY -S $SECRET_KEY
AUTO-SCALING-GROUP  monkey-sacrafice  lc1  us-west-2a  1  1  1
INSTANCE  i-6d0aa55e  us-west-2a  InService  Healthy  monkey-sacrafice

### Create SIMIAN_ARMY SimpleDB Table
    createDomain() {
        timestamp=$(date -u +"%FT%TZ" --date='5 min ago' | sed s/:/%3A/g)
        params="AWSAccessKeyId=$ACCESS_KEY&Action=CreateDomain&DomainName=$1&SignatureMethod=HmacSHA256&SignatureVersion=2&Timestamp=$timestamp&Version=2009-04-15"
        hash=$(echo -ne "GET\nsdb.us-west-2.amazonaws.com\n/\n$params" | openssl sha256 -hmac "$SECRET_KEY" -binary | base64)
        curl "https://sdb.us-west-2.amazonaws.com/?$params&Signature=$hash"
    }
    createDomain SIMIAN_ARMY

## build
git clone git@github.com:Netflix/SimianArmy.git
cd SimianArmy
./gradlew build
edit src/main/resources/simianarmy.properties
 - set secretKey
 - set account_key
 - set aws.region 
 - set isMonkeyTime=true (if on weekend or not in 9-3pm)
 - set default asg.enabled off
 - set monkey-sacrafice.enabled on
 - set monkey-sacrafice.probability=1.0

* start up, need to fix logging when we terminate something
* restart if you dont see a termination

$ curl http://localhost:8080/simianarmy/api/v1/chaos
[{"monkeyType":"CHAOS","eventType":"CHAOS_TERMINATION","eventTime":1343287826066,"groupType":"ASG","groupName":"monkey-sacrafice"}]

restart to verify another termination does not happpen (will only run once an hour and will not terminate same group again all day)

* verify that the instance has changed
$ ./bin/as-describe-auto-scaling-groups -I $ACCOUNT_KEY -S $SECRET_KEY
AUTO-SCALING-GROUP  monkey-sacrafice  lc1  us-west-2a  1  1  1
INSTANCE i-5b218e68 us-west-2a  InService  Healthy  monkey-sacrafice
