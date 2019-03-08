# Flink

## Dependencies

* Docker
* Vaulted shell
* AWS CLI

## Running locally


Start a Kinesis server locally using docker.
```
$ docker-compose --file docker-compose.local.yml up
```

Disable CBOR protocol
```
$ export AWS_CBOR_DISABLE=1
```

With Kinesis running, start two kinesis streams. One for input and one for
output.

You will need to install the AWS CLI.
```
$ brew install awscli
```

Get docker ip (with dinghy)
```
docker-machine ip dinghy
```

Now start the two streams with (you should only have to do this once ever):
```
$ aws --endpoint-url http://192.168.99.100:4567/ kinesis create-stream --stream-name input --shard-count 2 --region us-east-1
$ aws --endpoint-url http://192.168.99.100:4567/ kinesis create-stream --stream-name output --shard-count 2 --region us-east-1
```

Verify streams exist:
```
$ aws kinesis list-streams --endpoint-url http://192.168.99.100:4567
```

Install python modules
```
$ cd src/
$ pip3 install -r requirements.txt
```

Run script
```
$ python3 local/src/produce_pageviews.py input http://192.168.99.100:4567 local/data/page-views.log
```

Run sbt
```
$ sbt "run --aws-region us-east-1 --input-stream-name input --output-stream-name output --aws-endpoint http://192.168.99.100:4567"
```

## Generating test data
Use `twilson.instructure.com` to generate and send page views. The processed
page view events will end up in [this s3 bucket](https://console.aws.amazon.com/s3/buckets/pandata-raw-events-beta/2018/06/28/?region=us-east-1&tab=overview).

Download one of the folders (representing an hour's worth of data). Since both
mobile and Arc send events to the same endpoint, you have to filter out
the Arc events before sending this data through the python consumer.

## Debugging
If you have sent stale data one easy way to reset the input stream is to delete
it then recreate it.

```
$ aws --endpoint-url http://192.168.99.100:4567 kinesis delete-stream --stream-name input
$ aws --endpoint-url http://192.168.99.100:4567/ kinesis create-stream --stream-name input --shard-count 2 --region us-east-1
```

Artifactory secrets for build
```
#!/usr/bin/env bash
# fail if any commands fails
set -e

mkdir -p ~/.sbt/1.0/plugins

echo 'credentials += Credentials(Path.userHome / ".sbt" / ".credentials")' > ~/.sbt/1.0/plugins/user.sbt

cat <<EOF > ~/.sbt/.credentials
realm=Artifactory Realm
host=instructure.jfrog.io
user=bitrise-service-account
password=$ARTIFACTORY_PASS
EOF
```
