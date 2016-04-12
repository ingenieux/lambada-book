# Introduction

## Requirements

This book supposes you already know:

  * Java
  * Maven
  * Git
  * JSON and its parsing (specially using Jackson)

Possibly, you also know the basics of AWS, including:

  * awscli (and its brother, jq)
  * Basic AWS Services, such as S3

## The Vision

Lambada (the tool) was built on your experience writing beanstalker, as well as the AWS SDK for TypeScript. Its philosophy can summed as:

  * Allow as much flexibility as possible first, then composing functionality later
  * Properties over XML Files
  * Defaults where possible, or move it into a parent pom file.
  * Cognito over AWS API Gateway for Serverless Apps
  * TypeScript over JavaScript (when needed)

I believe its enough. Lets iterate from here. :)

