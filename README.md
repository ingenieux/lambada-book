# The Book of Lambada

A Gentle introduction of [AWS Lambda](https://aws.amazon.com/lambda/) using Java and [Lambada](https://github.com/ingenieux/lambada/).

# Preface

In this book, we'll show you recipes to build Java Functions for [AWS Lambda](https://aws.amazon.com/lambda/) with [Lambada](https://github.com/ingenieux/lambada/) using our platform, [Lambada](https://github.com/ingenieux/lambada/).

## Why Lambda?

The [AWS Lambda](https://aws.amazon.com/lambda/) Service quickly became one of the sexiest products in the AWS Portfolio, and it is deserved. Here are some reasons:

* Usage of various language runtimes, starting with the ubiquitous Node.js Runtime
* Its billing model, based on resource consumption (time + provisioned instance size)
* Integration with other AWS Services - directly or via event sources -, thus allowing one to quikcly automate and create "hooks" under its own infrastructure

In the beginning, Lambda did Javascript only (via Node). Java and Python were then added. 


## Serverless?

Given Lambda, AWS Gateway and Cognito, plus the AWS SDK for Javascript in the Browser helped build a new architectural style, called "Serverless": By offloading computation to Lambda Functions called from your browser, it becomes easier to meet demand on a budget, and quicker lead times.

There's also a aptly-named framework for Node JS. Lambada was also inspired on [Serverless  (the framework)](http://serverless.com/), but taking different decisions.


## Why this book?

Java tends to be the slowest and less used, yet it is as powerful as Node and Python. 

However Java excels at Typing, JVM-based Languages (like [Scala](http://scala-lang.org/)), as well as a large library of pure replacements instead of native binaries. Also, we've noticed that Java allows fewer errors in production.

In this book, we want to document how to use Lambada, in order to build innovative solutions. And in a hurry, so we made as a Cookbook, helping you identify your use case and build from it.

## Why you named it Lambada?

It's the genius yet obvious pun on Lambda. As [wikipedia says](https://en.wikipedia.org/wiki/Lambada):

> Lambada is a dance from Pará, Brazil. Lambada is an Afro-Brazilian dance authentic to people of Black/African descent who brought much of their music and dance culture with them into Brazil upon arrival into the country during the Transatlantic Slave Trade. The Afro-Brazilian dance became internationally popular in the 1980s, especially in Latin America and Caribbean countries. It has adopted aspects of dances such as forró, salsa, merengue, maxixe and the carimbó.

The Lead Maintainer of Lambada was a witness of the Lambada Fever. In fact, he was born in the same city as [Beto Barbosa](https://pt.wikipedia.org/wiki/Beto_Barbosa). Take a time and read about [Belém](https://en.wikipedia.org/wiki/Belém) as well, which happens to be its home town.

Amazon? Lambdas? Puns? It goes great lengths, right?

## But there is other Lambada, right?

Remeber we said genius yet obvious? So obvious others had the same idea.

So Yes, there's [lambadaframework/lambadaframework](https://github.com/lambadaframework/lambadaframework/) from [Çağatay Gürtürk](https://twitter.com/cagataygurturk) on github. We tried to discuss a consensus, but: idiosyncrasies.

But in the end, names are just that: names. In case of confusion, we recommend calling "Lambada by ingenieux" and "Lambada from Çağatay"

## The Vision

Lambada (the tool) was built on your experience writing [beanstalker](https://beanstalker.ingenieux.com.br/beanstalk-maven-plugin/), as well as the [AWS SDK for TypeScript](https://github.com/ingenieux/aws-sdk-typescript/). Its philosophy can summed as:

  * Allow as much flexibility as possible first, and later composing functionality
  * Properties over XML Files
  * Defaults where possible, or move it into a parent pom file.
  * Cognito over AWS API Gateway for Serverless Apps
  * TypeScript over JavaScript (when needed)

## Getting in Touch

Feel free to make any comments on [gitter](https://gitter.im/ingenieux/lambada/) or if you're shy, [just e-mail me](mailto:aldrin@leal.eng.br)

I believe its enough. Lets iterate from here. :)