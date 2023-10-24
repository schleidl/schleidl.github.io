---
layout: post
title:  "AWS Cloud Development Kit Constructs"
date:   2023-10-24 16:27:38 +0200
categories: AWS, CDK, CloudFormation
---
Taking a deeper look at [Amazon Workspaces](https://aws.amazon.com/workspaces/) the virtual desktop solution by Amazon I came across the question whether the creation of an Amazon Workspace can be automated using AWS Cloud Development Kit (CDK) or AWS CloudFormation. The short answer is yes it is possible to create a virtual desktop using [AWS Cloudformation](https://aws.amazon.com/cloudformation/). However, automating the creation of an Amazon Workspace is not straight forward using CDK. The documentation states that there is no L2 construct for Amazon Workspaces available in CDK which brings us to the purpose of this short article. 

In CDK there is a concept of a [Construct](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html#constructs_lib). Constructs are collected in the CDK Construct Library. There are three types of CDK Constructs, L1, L2, and L3. The letter L stands for level. 

I am going to explain these Construct layers in a minute. There is one other important concept of CDK that needs to be understood in order to be able to grasp and understand the Construct concept. At compile time a CDK program is translated to an AWS CloudFormation script in the background. This AWS Cloudformation script is then executed and resources are deployed on AWS. 

With that in mind we can have deeper look at the Constructs concept. 
Using an L1 construct a CDK developer can directly create and work with an AWS Cloudformation resource. The developer can recognize classes that are L1 Constructs by the prefix "Cfn". 

Please see the example below from the AWS documentation: 
```TypeScript
const bucket = new s3.CfnBucket(this, "MyBucket", {
  bucketName: "MyBucket",
  corsConfiguration: {
    corsRules: [{
          allowedOrigins: ["*"],
          allowedMethods: ["GET"]
    }]
  }
});
```
A L2 Construct is much more convenient, because some variable needed to initialize the respective AWS CloudFormation resource are already prefilled in the background with default values. See this example from the respective documentation using an L2 Construct: 

```TypeScript
import * as s3 from 'aws-cdk-lib/aws-s3';

// "this" is HelloCdkStack
new s3.Bucket(this, 'MyFirstBucket', {
  versioned: true
});
```
The most interesting type of Constructs are L3 Constructs. These Constructs represent patterns. Patterns are implementing solutions to more complex reccurring problems. The documentation gives an example for an AWS Fargate container cluster with a load balancer in front of it. Developers can also create their own patterns and use this tool to for example create a base line infrastructure for an development team to use and build-upon.  
