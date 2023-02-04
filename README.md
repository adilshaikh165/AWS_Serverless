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
 
 # Setup
 
 ## Create an AWS Amplify to host a web app
 
 1. Open your favorite text editor on your computer. Create a new file and paste the HTML code mentioned as index-Original.html in provided code
 
 2. Save the file as index.html.

 3. ZIP (compress) only the HTML file.

 4. In a new browser window, log into the Amplify console. Note: I will be using the Asia Pacific (Mumbai)
ap-south-1 Region for this tutorial.
![AmplifyHostNewApp](https://user-images.githubusercontent.com/98637502/216759071-4bb10aaf-12df-4f07-9ded-c37b8e584493.jpg)


 5. In the Get Started section, under Host your web app, choose the orange Get started button.
 
 6. Select Deploy without Git provider. This is what you should see on the screen:
 
 ![AmplifyStep2](https://user-images.githubusercontent.com/98637502/216759093-fcab1b71-9a23-4364-830e-c052960b4d44.jpg)

Choose the Continue button.

8. In the App name field, enter PowerOfMath.

9. For Environment name, enter dev.

10. Select the Drag and drop method. This is what you should see on your screen:

![AmplifyStep3](https://user-images.githubusercontent.com/98637502/216759165-0077f398-091f-4213-a250-244d2426003d.jpg)

11. Choose the Choose files button.

12. Select the ZIP file you created in Step 3.

13. Choose the Save and deploy button.

14. After a few seconds, you should see the message Deployment successfully completed.

![AmplifyImage](https://user-images.githubusercontent.com/98637502/216759219-7d2e72e3-0564-4adf-9a97-098d676a6b9b.jpg)

## Test Your Web App

1. Select Domain Management in the left navigation menu.

2. Copy and paste the URL displayed in the form into your browser.

![Static Web App](https://user-images.githubusercontent.com/98637502/216759290-d87218f7-a49e-4ce4-950e-dab764dc4165.jpg)

## Application architecture

Here is what our architecture looks like right now:

![image](https://user-images.githubusercontent.com/98637502/216759371-64b423a8-d1f1-492d-beb5-aca6379c8c22.png)

It is pretty minimal right now because we are only using the AWS Amplify console. We now have a live web app users can interact with. Next, we will create a Lambda function.

# Create Lambda Function

## To create the function

1. Click "Create function" in AWS Lambda Console

![LambdaStep1](https://user-images.githubusercontent.com/98637502/216759491-1683d1c1-eac5-4fdd-a159-3ec5c813ae1f.jpg)

2. Select "Author from scratch". Use name PowerOfMathFunction , select Python 3.9 as Runtime. Under Permissions, select "Use an existing role".

3. Click "Create function"

![LambdaStep2](https://user-images.githubusercontent.com/98637502/216759585-2c5f5b45-c15f-4cd4-bdd6-007046530320.jpg)

4. Replace the boilerplate coding with the following code snippet and click "Save"

Example Python Code

```bash
# import the JSON utility package
  import json
# import the Python math library
  import math

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

    # return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```

![LambdaStep4(a)](https://user-images.githubusercontent.com/98637502/216759749-7c7b1b92-1660-4d3e-9c6d-44ec34fe896d.jpg)

## Test Lambda Function

Let's test our newly created function. We haven't created DynamoDB and the API yet, so we'll do a sample echo operation. The function should output whatever input we pass.

1. Click the arrow on "Select a test event" and click "Configure test events"

![LambdaTestEvent](https://user-images.githubusercontent.com/98637502/216759806-8b690d9f-fa59-4f21-ab50-34d53a122f48.jpg)

2. Paste the following JSON into the event. 

```bash
{
  "base": 4,
  "exponent": 4
}
```

![LambdaTestConfig](https://user-images.githubusercontent.com/98637502/216759866-96013e87-9aff-4cde-b84d-e5d0574cdcff.jpg)

3. Click "Test", and it will execute the test event. You should see the output in the console

![LambdaTestResult](https://user-images.githubusercontent.com/98637502/216759875-27a25bf9-3eaf-4522-8c2c-cc5bc27a6d9d.jpg)

## Application architecture

After setting up the Lambda Function our architecture will look like this:

![image](https://user-images.githubusercontent.com/98637502/216759918-3624c47d-17d1-44b4-9b97-907c3f58d21b.png)

You will notice we added the AWS Lambda service to the diagram, but it does not yet have a connection to AWS Amplify or our users. We will build that upcoming steps.

# Create API

## To create the API

1. Go to API Gateway console

2. Click Create API

![image](https://user-images.githubusercontent.com/98637502/216759998-0fd28d04-5f26-4cda-9329-02f8a92931c9.png)

3. Scroll down and select "Build" for REST API

![image](https://user-images.githubusercontent.com/98637502/216760015-c4d655f6-8584-4f47-b86e-e92c3fa5fcca.png)

4. Give the API name as "PowerOfMathAPI", keep everything as is, click "Create API"

![APIStep4](https://user-images.githubusercontent.com/98637502/216760064-d1519fec-6bf3-4157-81c9-80a43f7e69af.jpg)

5. Each API is collection of resources and methods that are integrated with backend HTTP endpoints, Lambda functions, or other AWS services. Typically, API resources are organized in a resource tree according to the application logic. At this time you only have the root resource, but let's add a resource next
Click "Actions", then click "Create Resource"

![APIStep5](https://user-images.githubusercontent.com/98637502/216760093-efadb151-76d3-43e0-a045-3f025fc2dac2.jpg)

6. Select "POST" from drop down , then click checkmark

![APIStep6](https://user-images.githubusercontent.com/98637502/216760124-6f2ba6af-0716-4e38-b2d0-614a6cc7ba61.jpg)

7. The integration will come up automatically with "Lambda Function" option selected. Select "PowerOfMathFunction" function that we created earlier. As you start typing the name, your function name will show up.Select and click "Save". A popup window will come up to add resource policy to the lambda to be invoked by this API. Click "Ok"

![APIStep7](https://user-images.githubusercontent.com/98637502/216760182-a18f69bc-b880-4113-9a16-1e8596f0ad3a.jpg)

8. Add Permission to Lambda Function

9. Enable CORS.

![APIStep9](https://user-images.githubusercontent.com/98637502/216760331-da31800b-01a5-48f2-8e31-98aee4b3f11f.jpg)

Our API-Lambda integration is done!

## Validate API

1. In the left navigation pane, select Resources.

2. The methods for our API will now be listed on the right. Choose POST.

3. Choose the small blue lightning bolt.

4. Paste the following into the Request Body field:

```bash
{
  "base" : 2,
  "exponent" : 3
}
```

![ValidateAPI](https://user-images.githubusercontent.com/98637502/216760661-ad649810-8ea6-428b-9757-6187dfd784ce.jpg)


## Deploy the API

In this step, you deploy the API that you created to a stage called dev.

1. Click "Actions", select "Deploy API"

![DeployAPI](https://user-images.githubusercontent.com/98637502/216760426-d0879f69-f810-4ce4-94d3-058141566e4d.jpg)

2. Now it is going to ask you about a stage. Select "[New Stage]" for "Deployment stage". Give "dev" as "Stage name". Click "Deploy"

![DeployAPI2](https://user-images.githubusercontent.com/98637502/216760439-872e3872-d65a-40a6-ac27-324e230d1fd7.jpg)

3. We're all set to run our solution! To invoke our API endpoint, we need the endpoint url. In the "Stages" screen, expand the stage "dev", select "POST" method, and copy the "Invoke URL" from screen

![DeployAPI3](https://user-images.githubusercontent.com/98637502/216760464-fec53e1a-8854-47fd-bc26-63494641e154.jpg)

## Application architecture

We added API Gateway and connected it to our existing Lambda function. Now, we can trigger our function with an API call. We are still missing the ability to generate this call from our web client. We will add our data table first now.

![image](https://user-images.githubusercontent.com/98637502/216760815-b47e2818-6fc8-443e-ac42-02d7b40ebf29.png)




