# First Steps

## Before you Start

  * You need a set of AWS Credentials to start with. I recommend you start with all permissions when developing locally, and then trim down its permissions. So this policy would suffice for a new user:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

(Note this is also the prebuilt ```Administrator Access``` policy on the IAM Console)

Once you've got this set of credentials, install [awscli](https://aws.amazon.com/cli/) and run ```awscli configure```

  * This example requires you to declare an IAM Role named ```lambda_basic_execution``` with the following policy added:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        }
    ]
}
```

  * Also, it needs a trust relationship to ```lambda.amazonaws.com```

## Prerelease versions of this guide

This is important: Currently, prior to running this examples, you need to install the non-released versions of both beanstalker and lambada. To do this, clone and mvn install both:

```shell
$ for i in beanstalker lambada ; do \
    git clone https://github.com/ingenieux/$i.git ; \
    (cd $i ; mvn install -DskipTests) ; \
  done
```

## Getting Help

Here are some tips when it comes to troubleshooting:

1. Run mvn -X in order to track the whole flow, including variables and parameters. But be wary that the AWS Calls will take a lot of logging output
2. Always look at the Javadocs. Specially:
  * beanstalk-maven-plugin: http://beanstalker.ingenieux.com.br/beanstalk-maven-plugin/plugin-info.html
  * lambda-maven-plugin: http://beanstalker.ingenieux.com.br/lambda-maven-plugin/plugin-info.html
  * apigateway-maven-plugin: http://beanstalker.ingenieux.com.br/apigateway-maven-plugin/plugin-info.html
  * lambada-maven-plugin: http://lambada.ingenieux.com.br/lambada-maven-plugin/plugin-info.html

When in doubt, read the source code, and use the gitter rooms for [lambada](https://gitter.im/ingenieux/lambada/) and [beanstalker](https://gitter.im/ingenieux/beanstalker/), as well as the [beanstalker-users mailing list](https://groups.google.com/group/beanstalker-users/).
