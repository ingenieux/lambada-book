## Under the Hood

Although AWS doesn't make its details public, there's nothing wrong about reversing whats available to us. In fact, some already did it before.

In this example, we'll work on Lambda Shell, a tool to grab parts of the AWS Java Runtime for Lambda, and make a few investigations of our own.[^1]

[^1]: Credit goes to Eric Hammond of Alestic for doing [this first](https://alestic.com/2014/11/aws-lambda-environment/) 

### Downloading the Examples

From now on, we'll refer to the lambda examples as the contents of the lambada-book-examples git repository, under https://github.com/ingenieux/lambada-book-examples. At this point, all you need to do is to clone it using git, and import into your favorite IDE.

Regarding the Decompilation part of the drill, I'll be using IntelliJ Idea with its Decompiler Plugin. There's no reason to think the Community Version can't do this, so I'm taking it for granted for now.

### Introducing Lambda Shell

So, what if we could run applications under Lambda? There's nothing wrong about it, as long as you remember to keep your memory footprint low, since Java has several problems when it comes to executing large process.

Under the lambda-shell project, there's a lambda function which allows us to run commands. Lets give a look at it.

NOTE: For this example, in particular, you need to create a ```lambda_s3_full``` role with the ```AmazonS3Full``` policy for Lambda on IAM.

Once you've got the permission, you can deploy it with:

$ mvn -f lambda-shell/pom.xml -Pdeploy deploy

### Testing the Environment

Now, open your AWS Console, and select the lb_shell function. Then on "Actions" | "Configure Test Event", enter these commands:

``[
    "env",
    "ps -aux",
    "find / -type f -iname '*.jar'"
]
```

Then click "Test" and wait. Here are my results:

```json
{
  "env": [
    "+ env",
    "AWS_LAMBDA_FUNCTION_VERSION=$LATEST",
    "AWS_SESSION_TOKEN=AQoDYXdzEDAawAJN9qlL8Svy3ITqY9r8dduc1cxfH++yt2QnZTGvcnhBdLCqG60lOo0uV1rKaviSr542nbYxdDsMYvrBflUPtALmNjf4BCQd4ipVdaKcR6omEYj98siQCrqZwgz4T9ymVQO1F2Ptd/UCRja8+amnyKaSzBw+1q+FRGcPTfP+O8I+8BDSLc8j8284Oj05N1xLKa7pRf8V76EdUvpUlb/E5kymJZmy0nKZhhH9DwVa1oV+ZtiNEAEONLgLbU8fuXZ4EndeGLaTy5SrkcLEGQflY3DD4+jsJ6O6h7ykA/Vdg0mHWXkO4UckICMq6xDQkEvEHJKFWuu3NAGek0A+QKKgZx5EwPqXGgDnpqmP1DDR0dEUID04itCuryk+oxIbW0SgojFwDZqO3e20uD6hnAtTDX5WMZnKZfdOb/0q7Mfa7fw5bSDTj7S4BQ==",
    "LD_LIBRARY_PATH=/lib64:/usr/lib64:/var/runtime:/var/task:/var/task/lib",
    "AWS_LAMBDA_LOG_GROUP_NAME=/aws/lambda/lb_runCommand",
    "LAMBDA_TASK_ROOT=/var/task",
    "AWS_LAMBDA_LOG_STREAM_NAME=2016/04/12/[$LATEST]d34bbf383e8c4f80b0684b09f7471611",
    "NLSPATH=/usr/dt/lib/nls/msg/%L/%N.cat",
    "AWS_LAMBDA_FUNCTION_NAME=lb_runCommand",
    "PATH=/usr/local/bin:/usr/bin/:/bin",
    "AWS_DEFAULT_REGION=us-east-1",
    "PWD=/var/task",
    "AWS_SECRET_ACCESS_KEY=ZGPFZ9Z0SeY2WdAl7V5JUHScPdgOwpVC9mNQTz5g",
    "LAMBDA_RUNTIME_DIR=/var/runtime",
    "AWS_REGION=us-east-1",
    "XFILESEARCHPATH=/usr/dt/app-defaults/%L/Dt",
    "AWS_ACCESS_KEY_ID=ASIAJ5YESJ3CS6ROQXZA",
    "SHLVL=1",
    "CLASSPATH=/var/runtime:/var/runtime/lib/LambdaSandboxJavaAPI-1.1.jar",
    "AWS_ACCESS_KEY=ASIAJ5YESJ3CS6ROQXZA",
    "AWS_LAMBDA_FUNCTION_MEMORY_SIZE=512",
    "AWS_SECRET_KEY=ZGPFZ9Z0SeY2WdAl7V5JUHScPdgOwpVC9mNQTz5g",
    "_=/usr/bin/env"
  ],
  "ps -aux": [
    "+ ps -aux",
    "Warning: bad syntax, perhaps a bogus '-'? See /usr/share/doc/procps-3.2.8/FAQ",
    "USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND",
    "496          1  0.1  1.5 2425956 58548 ?       Ssl  14:36   0:00 java -XX:MaxHeapSize=445645k -XX:MaxMetaspaceSize=52429k -XX:ReservedCodeCacheSize=26214k -XX:+UseSerialGC -Xshare:on -XX:-TieredCompilation lambdainternal.LambdaRTEntry",
    "496         51  0.0  0.0  11608  2540 ?        S    14:43   0:00 /bin/bash -x /tmp/tmp-6740848046668077297.sh",
    "496         53  0.0  0.0  13592  2024 ?        R    14:43   0:00 ps -aux"
  ],
  "find / -type f -iname '*.jar'": [
    "+ find / -type f -iname '*.jar'",
    "find: `/root': Permission denied",
    "find: `/proc/tty/driver': Permission denied",
    "/tmp/runtime.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/dnsns.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/nashorn.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/sunec.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/sunpkcs11.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/zipfs.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/cldrdata.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/sunjce_provider.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/jaccess.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/ext/localedata.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/charsets.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/security/US_export_policy.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/security/local_policy.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/rt.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/jce.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/jsse.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/management-agent.jar",
    "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.71-2.b15.8.amzn1.x86_64/jre/lib/resources.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/ext/dnsns.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/ext/sunpkcs11.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/ext/zipfs.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/ext/sunjce_provider.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/ext/localedata.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/charsets.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/security/US_export_policy.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/security/local_policy.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/rt.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/jce.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/jsse.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/management-agent.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/rhino.jar",
    "/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.95.x86_64/jre/lib/resources.jar",
    "find: `/builddir': Permission denied",
    "find: `/etc/pki/CA/private': Permission denied",
    "find: `/var/cache/ldconfig': Permission denied",
    "/var/runtime/lib/joda-time-2.8.2.jar",
    "/var/runtime/lib/lambda-sandbox.jar",
    "/var/runtime/lib/jackson-databind-2.5.x.jar",
    "/var/runtime/lib/jackson-core-2.5.x.jar",
    "/var/runtime/lib/gson-2.3.1.jar",
    "/var/runtime/lib/jackson-annotations-2.5.x.jar",
    "/var/runtime/lib/LambdaSandboxJavaAPI-1.1.jar",
    "find: `/var/lib/yum/history/2016-03-30/2': Permission denied",
    "find: `/var/lib/yum/history/2016-03-30/4': Permission denied",
    "find: `/var/lib/yum/history/2016-03-30/1': Permission denied",
    "find: `/var/lib/yum/history/2016-03-30/3': Permission denied"
  ]
}
```

From here, we can make a few statements:

* Your lambda code runs under a container (notice no parent process), and it is *NOT* a root process
* Lambda sets you the AWS Permissions using IAM, and it is reflected in its Environment Variables
* There's stub java code under ```/var/runtime```

If you read the LambdaShell source code again, you'll notice that it contains a function to copy files to S3. Create a S3 Bucket and invoke lb_runCommand with this payload:

```json
[
    "tar czvf /tmp/runtime.jar /var/runtime/",
    "!aws s3 cp /tmp/runtime.jar s3://<yourbucket>/aws-lambda-runtime.jar"
]
```

Now you can copy it locally to your machine:

```
$ aws cp s3://<yourbucket>/aws-lambda-runtiume.jar .
```

Plus, you can open into your IDE and Decompile it.

### Understanding the Source

Lets see the relevant parts of the source:

```java
/**
 * Represents a Shell Interface to AWS Lambda
 */
public class LambdaShell {
    public static void main(String[] args) throws Exception {
        final Map<String, List<String>> result = new LambdaShell().runCommands(Collections.singletonList("ps -aux"));

        System.err.println(result);
    }

    @LambadaFunction(name="lb_runCommand", memorySize = 512, timeout = 300, role="*/lambda_s3_full")
    public Map<String, List<String>> runCommands(List<String> inCommands) throws Exception {
        Map<String, List<String>> result = new LinkedHashMap<>();

        for (String e : inCommands) {
            List<String> commandResult = runCommand(e);

            result.put(e, commandResult);
        }

        return result;
    }

    public List<String> runCommand(String command) throws Exception {
        String[] args = command.split("\\s+");

        if (command.startsWith("!aws s3 cp")) {
            // !aws s3 cp <sourceFile> <targetPath>
            String sourceFile = args[3];

            String targetPath = args[4];

            copyFile(sourceFile, targetPath);
        } else {
            final ByteArrayOutputStream os = new ByteArrayOutputStream();

            runCommandArray(os, args);

            return Arrays.asList(new String(os.toByteArray()).split("\n"));
        }

        return null;
    }

    private void copyFile(String sourceFile, String targetPath) {
        AmazonS3 s3Client = new AmazonS3Client();

        AmazonS3URI uri = new AmazonS3URI(targetPath);

        String key = uri.getKey();

        String bucketName = uri.getBucket();

        s3Client.putObject(new PutObjectRequest(bucketName, key, new File(sourceFile)));
    }
}
```

The main() method is a test stub. Lambda won't use it under any circunstances, so it could be used as a way to test/debug it locally on my workstation. However, the next method is more interesting:

```java
    @LambadaFunction(name="lb_runCommand", memorySize = 512, timeout = 300, role="*/lambda_s3_full")
    public Map<String, List<String>> runCommands(List<String> inCommands) throws Exception {
```

It tells us several things:
  * By default, we use the aws_basic_execution role (it is configurable on the lambda-maven-plugin). But here, we are overwriting its role to use "lambda_s3_full" - which we created previously.
  * We are also overwriting memory size and timeout. Timeouts larger than 60 (seconds) don't have their output shown into the AWS Lambda Console (but you can see it under CloudWatch Logs though)
  * We're using bare structures. Reading an array and return a map of list of strings.

The method ```runCommand``` actually delegates between spawning a shell (wrapped into a .sh file) and/or running the ```aws s3 cp``` command (which is defined on the ```copyFile``` method.

## Concluding

This method is useful, since we can validate the contents of the built-in container used for AWS Lambda (it is often updated - [see its docs for reference](http://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html)), as well as investigate its inner workings and allowing us to run commands.


