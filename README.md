# LevelUp! Lab for Serverless

# Lab Overview And High Level Design

Let's start with the High Level Design.

The diagram below provides a visual representation of the services used in this tutorial and how they are connected. This application uses AWS Amplify, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, and AWS Identity and Access Management (IAM).

As we go through the tutorial, we will discuss the services in detail and point to resources that will help you get up to speed with them.
![image](https://user-images.githubusercontent.com/98637502/216758401-5ea49625-922c-4b15-8c96-f580e0645cdf.png)

What Do We Need?

 - AWS Amplify : A way to create/host a webpage
 
 - AWS API Gateway : A way to invoke the math functionality
 
 - AWS Lambda : A way to do some math
 
 - AWS DynamoDB : Somewhere to store/return the math result
 
 - AWS IAM : A way to handle permissions.
 
 # Link to the BLOG for step by step detailed description
 https://adilshaikh165.hashnode.dev/aws-serverless-application

## Conclusion

We have successfully created a serverless APP using Amplify, API Gateway, Lambda, IAM and DynamoDB!

## Cleanup

Let's clean up the resources we have created for this lab.

### Cleaning up DynamoDB

To delete the table, from DynamoDB console, select the table "PowerOfMathDynamoDb", and click "Delete table"

![DeleteTable](https://user-images.githubusercontent.com/98637502/216762590-83681257-7b6c-4088-bb8c-29b7e5f766b6.jpg)

### Cleaning up Lambda

To delete the Lambda, from the Lambda console, select lambda "PowerOfMathFunction", click "Actions", then click Delete

![DeleteLambda](https://user-images.githubusercontent.com/98637502/216762632-ee1660d2-6825-4202-bdc2-67d0979bb828.jpg)

### Cleaning up API Gateway

To delete the API we created, in API gateway console, under APIs, select " PowerOfMathAPI" API, click "Actions", then "Delete"

![DeleteAPIGateway](https://user-images.githubusercontent.com/98637502/216762666-79599fe5-e1a4-42fc-ac58-70fff9e43f3e.jpg)

### Cleaning up Amplify

Select the app "PowerOfMath", and click "Actions", then "Delete App".

![DeleteAmplify](https://user-images.githubusercontent.com/98637502/216762822-c0e13864-9610-4e6b-b1d9-5c401a49b054.jpg)


    

