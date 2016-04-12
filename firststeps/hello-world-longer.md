## "Hello, World" (the longer part)

This time, we'll manually do all the steps, avoiding Maven as possible. This is made so you could easily understand the savings that Lambada introduce to you.

### Scaffolding it:

Create a blank maven project:

```
$ mvn archetype:generate -DgroupId=myorg -DartifactId=hello-lambda \
  -Dversion=0.0.1-SNAPSHOT -Dpackage=myorg
```

Then, when prompted, simply type return all the way. Import this project. Lets start by writing the function signature:

```java
package myorg;

public class MyLambda {
  public String sayHello(String in) throws Exception {
    System.out.println("in: " + in);
    
    return "{\"hello\": \"world\"}";
  }
}
```

Also:
  * delete ```App.java``` and ```AppTest.java```
  * remove the ```dependencies``` section```

Then, compile it:

```
$ mvn package
```

Now we'll emulate the deploy part of it. Replace ```<mybucket>``` with a bucket name you picked.

```
$ aws s3 mb s3://<mybucket>
$ aws s3 cp target/hello-lambda-0.0.1-SNAPSHOT.jar s3://mybucket/
$ aws iam list-roles | jq -r .Roles[].Arn | grep lambda_basic_execution 
# Pick your ARN for lambda_basic_execution
$ aws lambda create-function --function-name say-hello --runtime java8 --role arn:aws:iam::<youraccountid>:role/lambda_basic_execution --handler myorg.MyHandler::sayHello --code S3Bucket=<mybucket>,S3Key=hello-lambda-0.0.1-SNAPSHOT.jar
{
    "CodeSha256": "8a9SGPDW6Yy0HWwVUWDLaKEg3OJv/oEACp9uP/60sg4=", 
    "FunctionName": "say-hello", 
    "CodeSize": 2067, 
    "MemorySize": 128, 
    "FunctionArn": "arn:aws:lambda:us-east-1:235368163414:function:say-hello", 
    "Version": "$LATEST", 
    "Role": "arn:aws:iam::235368163414:role/lambda_basic_execution", 
    "Timeout": 3, 
    "LastModified": "2016-04-12T12:54:50.008+0000", 
    "Handler": "myorg.MyHandler::sayHello", 
    "Runtime": "java8", 
    "Description": ""
}
```

There, your function is created! However, a new deploy needs to use a different call: update-function-code:

```
$ aws lambda update-function-code --function-name say-hello --runtime java8 --role arn:aws:iam::235368163414:role/lambda_basic_execution --handler myorg.MyHandler::sayHello --code S3Bucket=mylambda-bucket-aldrin,S3Key=hello-lambda-0.0.1-SNAPSHOT.jar
```

Of course, you can also change the configuration of the function itself from the console.

Plus, if you use dependencies, they aren't included, requiring you to build a fat jar instead.

### Enter Lambada

Lambada + beanstalker automates this process by:

  * @Lambada annotation defines the function code
  * ```lambada:generate``` collecting those annotations and generating a lambda-definitions.json file
  * beanstalker:upload-source-bundle creates (if needed) then uses an S3 Bucket which is always reserved and available for your user account. All it takes is to declare the property ```beanstalk.s3bucket``` to empty.
  * The lambda:deploy-functions reads the output of lambada:generate and replaces your role ids where applicable then figures out which functions to call (```create-function```, ```update-function``` and ```update-function-code```)

### Understanding the Maven Plugins

From now on, we'll refer the Maven Plugins to their real name: Mojos.

### About Type Signatures

