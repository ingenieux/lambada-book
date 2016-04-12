## The AWS Lambda for Java 8 Runtime Model


Under the Java Model ([fully described here](http://docs.aws.amazon.com/lambda/latest/dg/java-programming-model.html)), you create functions, which represent entry points. A single S3 file might contain multiple entry points, thus making it easy for us to wrap everything into a single zip/jar file into S3. 

When defining those functions, you need to refer to the ```package+classname+method```, as such:

```my.package.name.Class::handlerName```

There, ```handlerName``` is a method defined into the class. This method could be either ```static``` or not. If not static, ensure the class comes with a Public, No Argument Constructor.

When invoking a Lambda function from the AWS Console, the payload (i.e., the input argument) MUST be a JSON valid payload. This includes:

* Scalar values (strings, numbers, Booleans)
* Arrays
* Objects
* null

Now let’s talk method signatures: In the Lambda runtime for Java, you can declare your handler method signature as:

```void handlerName(InputStream is, OutputStream os [, Context ctx])```

In this case, the InputStream instance passed will contain your Payload. It’s up to you to read and parse it, returning its result into the ```OutputStream``` instance. 

In Lambada, we particularly prefer this syntax, and we'll explain the reasons why.

This is an exception, and allows you to express input and output as basically anything, allowing you to bypass the built-in JSON marshalling for and roll your own. However, as the API states, the input payload must be valid JSON (even if the output doesn’t need to).

However, JSON with POJOs is easier to use. Here’s the method signature you need to use:

```ReturnType handlerName(AType is [, Context ctx])```

```AType``` is a type which will be converted from the JSON payload by using a JSON Framework under the hood. In this case, the Lambda runtime will deal with the marshalling of both input parameters and result.

The ```Context``` object ([javadoc](http://javadox.com/com.amazonaws/aws-lambda-java-core/1.1.0/com/amazonaws/services/lambda/runtime/Context.html)) keeps extra metadata about your call, which includes:

* AWS Cognito metadata
* Function name
* Available memory
* Remaining time
* Logger object

The output of the loggers are kept within a CloudWatch log stream and are fully accessible from the console. However, you need to ensure your role is able to write into the CloudWatch logs (the ```logs:``` permissions from the Policy Statement previously shown).

Currently, Lambada doesn't support the ```RequestHandler``` and the ```RequestStreamHandler``` interfaces.
