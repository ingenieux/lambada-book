## "Hello World"

Lets start with a simple Lambda Function written with Lambada.

### Starting it up

Start by cloning ingenieux/lambada-skeleton from github:

```
$ git clone https://git@github.com/ingenieux/lambada-skeleton.git
$ cd lambada-skeleton
```

Import lambada-skeleton into your IDE. The project is broken as such:

  * First, the common and repeatable definitions are under ```parent/pom.xml```. Thus, whenever a new version arises, you might simply compare with the one in the repository.
  * The main ```pom.xml``` inherits from parent/pom. In particular, the ```<dependencies/>``` section and any eventual properties you might need.
  * The ```HelloLambda.java``` is where the main code for this Lambda Function is Written. Of particular case, notice the ```sayHello``` method:

```java
    @LambadaFunction(name="hello_sayHelloToLambda",
            description="Says Hello to Lambda",
            timeout=5)
    public void sayHelloToLambda(InputStream is, OutputStream os, Context context) throws Exception {
```

Then, deploy:

```
$ mvn -Pdeploy deploy
[INFO] Scanning for projects...
...
[INFO] --- lambada-maven-plugin:0.0.2-SNAPSHOT:generate (default) @ lambada-skeleton ---
[INFO] Reflections took 75 ms to scan 4 urls, producing 5 keys and 53 values 
[INFO] Skipping API Gateway generation (file doesnt exist: apigateway-swagger.json)
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ lambada-skeleton ---
[INFO] Not copying test resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.2:testCompile (default-testCompile) @ lambada-skeleton ---
[INFO] Not compiling test sources
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ lambada-skeleton ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ lambada-skeleton ---
[INFO] Building jar: /home/aldrin/projetos/sources/lambada-skeleton/target/lambada-skeleton.jar
[INFO] 
[INFO] --- maven-shade-plugin:2.3:shade (default) @ lambada-skeleton ---
[INFO] Including com.amazonaws:aws-lambda-java-core:jar:1.1.0 in the shaded jar.
[INFO] Including io.ingenieux.lambada:lambada-runtime:jar:0.0.2-SNAPSHOT in the shaded jar.
[INFO] Including commons-io:commons-io:jar:2.4 in the shaded jar.
[INFO] Replacing original artifact with shaded artifact.
[INFO] Replacing /home/aldrin/projetos/sources/lambada-skeleton/target/lambada-skeleton.jar with /home/aldrin/projetos/sources/lambada-skeleton/target/lambada-skeleton-0.0.1-SNAPSHOT-shaded.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ lambada-skeleton ---
[INFO] Skipping artifact installation
[INFO] 
[INFO] --- maven-deploy-plugin:2.7:deploy (default-deploy) @ lambada-skeleton ---
[INFO] Skipping artifact deployment
[INFO] 
[INFO] --- beanstalk-maven-plugin:1.4.5-SNAPSHOT:upload-source-bundle (deploy) @ lambada-skeleton ---
[INFO] S3 Bucket not defined.
[INFO] Using defaults, like: elasticbeanstalk-us-east-1-235368163414
[INFO] Target Path: s3://elasticbeanstalk-us-east-1-235368163414/apps/lambada-skeleton/lambada-skeleton-20160412111058.zip
[INFO] Uploading artifact file: /home/aldrin/projetos/sources/lambada-skeleton/target/lambada-skeleton.jar
[INFO] Artifact Uploaded
[INFO] SUCCESS
[INFO] {
[INFO]   "contentMd5" : "0LWc8OkMGxKkVHW6QJG70w==",
[INFO]   "metadata" : {
[INFO]     "userMetadata" : { },
[INFO]     "contentLength" : 0,
[INFO]     "etag" : "d0b59cf0e90c1b12a45475ba4091bbd3",
[INFO]     "requesterCharged" : false,
[INFO]     "rawMetadata" : {
[INFO]       "Content-Length" : 0,
[INFO]       "ETag" : "d0b59cf0e90c1b12a45475ba4091bbd3"
[INFO]     },
[INFO]     "instanceLength" : 0
[INFO]   },
[INFO]   "etag" : "d0b59cf0e90c1b12a45475ba4091bbd3",
[INFO]   "requesterCharged" : false
[INFO] }
[INFO] 
[INFO] --- lambda-maven-plugin:1.4.5-SNAPSHOT:deploy-functions (deploy) @ lambada-skeleton ---
[INFO] Loaded and replaced definitions from file '/home/aldrin/projetos/sources/lambada-skeleton/target/classes/META-INF/lambda-definitions.json'
[INFO] Found 1 definitions: 
[INFO] Merged into 1 definitions: 
[INFO] Deploying Function: hello_sayHelloToLambda (handler: io.ingenieux.hello.HelloLambda::sayHelloToLambda)
[INFO] Function does not exist. Creating it instead.
[INFO] SUCCESS
[INFO] {
[INFO]   "hello_sayHelloToLambda" : {
[INFO]     "name" : "hello_sayHelloToLambda",
[INFO]     "description" : "Says Hello to Lambda",
[INFO]     "memorySize" : 128,
[INFO]     "role" : "arn:aws:iam::235368163414:role/lambda_basic_execution",
[INFO]     "timeout" : 5,
[INFO]     "handler" : "io.ingenieux.hello.HelloLambda::sayHelloToLambda"
[INFO]   }
[INFO] }
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 10.162 s
[INFO] Finished at: 2016-04-12T06:11:09-05:00
[INFO] Final Memory: 28M/290M
[INFO] ------------------------------------------------------------------------
aldrin@aldrin-X555LD lambada-skeleton $
```

At this point, your function was created and successfully deployed.

### What Happened?

Building an AWS Lambda Function in Java requires several steps, involving:

  * Extracting the ```@LambadaFunctions``` and generating the file ```target/classes/META-INF/lambda-definitions.json``` (lambada:generate)
  * Packaging an "Uber Jar", thus generating a jar file with its dependencies built-in (```shade:shade```)
  * Finding - or allocating - a S3 Bucket, and then uploading the jar file into it (```beanstalk:upload-source-bundle```)
  * Resolving your account user id and finding the suitable IAM Role (within ```lambda-maven-plugin:deploy-functions```)
  * Calling the Lambda API in order to register the functions declared from ```lambda-definitions.json``` (```lambda-maven-plugin:deploy-functions```)

As you see, there are lots of small, integrated process. In order to make it easy we wrap into a build profile use it to augment the original build definitions.

### Invoking your function

You can do it from the AWS Console. 

(TODO: show how to use the aws cli)

When done, look under the CloudWatch Logs and see its output.

