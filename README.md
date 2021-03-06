Fake-SNS
==================
Docker image for the fake_sns gem.  Currently uses a forked copy of the gem, as development has stagnated, and
a bug, which is critical for dockerization, is still outstanding. See: [pull request](https://github.com/yourkarma/fake_sns/pull/5)

Features
---------
* Works off of forked copy of original gem to allow proper dockerization
* Uses thin as webserver to prevent reverse dns lookup (causing big slowdowns)
* Exposes fake_sns database to "/sns" VOLUME for file based verification

Running
-------
`$ docker run -it -p 9292:9292 feathj/fake-sns`

Note that `VIRTUAL_HOST` environment variable can be added if run with dinghy client
`$ docker run -it -p 9292:9292 -e VIRTUAL_HOST=sns.docker feathj/fake-sns`

Or from docker-compose.yml:
```
sns:
  image: feathj/fake-sns
  ports:
    - "9292:9292"
  environment:
    VIRTUAL_HOST: "sns.docker"
```

Getting started
---------------
The following ruby code can be used to verify that the container is running properly
```
require 'aws-sdk'
require 'aws-sdk-core'
require 'aws-sdk-resources'
require 'aws-sdk-v1'

AWS.config(
  use_ssl: false,
  sns_endpoint: 'sns.docker',
  sns_port: 9292
)

sns = AWS::SNS::Client.new(
  access_key_id: '',
  secret_access_key: ''
)

t = sns.create_topic(name: 'testTopic')
resp = sns.publish(topic_arn: t.topic_arn, message: '{test: true}', message_structure: 'json')
puts resp
```

Related Repos
-------------
[fake_sns](https://github.com/yourkarma/fake_sns)
[postfix-blackhole](https://github.com/feathj/docker-postfix-blackhole)
