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

#Create DynamoDB Table

Create the DynamoDB table that the Lambda function uses.

### To create a DynamoDB table

1. Open the DynamoDB console.

2. Choose Create table.

3. Create a table with the following settings.
   - Table name – PowerOfMathDynamoDb
   - Primary key – id (number)

4. Choose Create.

![DynamoDB](https://user-images.githubusercontent.com/98637502/216761540-b523f2f4-8985-4c6c-84ad-b658db3c52b7.jpg)

5. In the list of tables, select the table name, PowerOfMathDynamoDb.

6. In the General information section, show Additional info by selecting the down arrow.

7. Copy the Amazon Resource Name (ARN). You will need it later in this module.

## Create and add IAM policy to Lambda function

1. Now that we have a table, let's edit our Lambda function to be able to write data to it. In a new browser window, open the AWS Lambda console.

2. Select the function we created in module two (if you have been using our examples, it will be called PowerOfMathFunction
. If you don't see it, check the Region dropdown in the upper right next to your name to ensure you're in the same Region you created the function in.

3. We'll be adding permissions to our function so it can use the DynamoDB service, and we'll be using AWS Identity and Access Management (IAM) to do so.

4. Select the Configuration tab and select Permissions from the right side menu.

5. In the Execution role box, under Role name, choose the link. A new browser tab will open.

6. In the Permissions policies box, open the Add permissions dropdown and select Create inline policy.

7. Select the JSON tab.

8. Paste the following policy in the text area, taking care to replace your table's ARN in the Resource field in line 15:

```bash
{
"Version": "2012-10-17",
"Statement": [
    {
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "dynamodb:PutItem",
            "dynamodb:DeleteItem",
            "dynamodb:GetItem",
            "dynamodb:Scan",
            "dynamodb:Query",
            "dynamodb:UpdateItem"
        ],
        "Resource": "YOUR-TABLE-ARN"
    }
    ]
}
```
9. This policy will allow our Lambda function to read, edit, or delete items, but restrict it to only be able to do so in the table we created.

10. Choose the blue Review Policy button.

11. Next to Name, enter PowerOfMathPolicy.

12. Choose the blue Create Policy button.

13. You can now close this browser tab and go back to the tab for your Lambda function.

## Modify Lambda to write to DynamoDB.

1. Select the Code tab and select your function from the navigation pane on the left side of the code editor.

2. Replace the code for your function with the following:

```bash
# import the JSON utility package
import json
# import the Python math library
import math

# import the AWS SDK (for Python the package name is boto3)
import boto3
# import two packages to help us with dates and date formatting
from time import gmtime, strftime

# create a DynamoDB object using the AWS SDK
dynamodb = boto3.resource('dynamodb')
# use the DynamoDB object to select our table
table = dynamodb.Table('PowerOfMathDatabase')
# store the current time in a human readable format in a variable
now = strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

# write result and time to the DynamoDB table using the object we instantiated and save response in a variable
    response = table.put_item(
        Item={
            'ID': str(mathResult),
            'LatestGreetingTime':now
            })

# return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```

![FinalLambdaFunction](https://user-images.githubusercontent.com/98637502/216761878-c9b424f6-e6b6-42a7-a1da-4447dd0e358b.jpg)

3. Choose the Deploy button at the top of the code editor

## Test The Changes

1. Choose the orange Test button.

2. You should see an Execution result: succeeded message with a green background.

3. In a new browser tab, open the DynamoDB console.

4. In the left-hand navigation pane, select Tables > Explore items.

5. Select PowerOfMathDynamoDb, which we created earlier in this module.

6. Select the Items tab on the right.

7. Items matching your test event appear under Items returned. If you have been using our examples, the item ID will be the number and value will be the result of the math operation performed by Lambda.

8. Every time your Lambda function executes, your DynamoDB table will be updated. If the same name is used, only the time stamp will change.

![LambdaIntegrationWithTable](https://user-images.githubusercontent.com/98637502/216762032-a2798da9-6818-4333-981e-370158990d41.jpg)

## Application architecture

let's look at our current architecture:

![image](https://user-images.githubusercontent.com/98637502/216762052-75fe320c-5a15-4af4-9cce-7a8e98b279f8.png)

We added two services in this step: DynamoDB (for storage) and IAM (for managing permissions securely). Both are connected to our Lambda function, so that it can write to our database. The final step is to add code to our client to call the API Gateway.

# Add Interactivity to Your Web App

In this module, we will update the static website we created to invoke the REST API . This will add the ability to display text based on what you input.

## Update Web App with Amplify console

1. Open the index.html file you created.

2. Replace the existing code with the following:

```bash
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>To the Power of Math!</title>
    <!-- Styling for the client UI -->
    <style>
    h1 {
        color: #FFFFFF;
        font-family: system-ui;
		margin-left: 20px;
        }
	body {
        background-color: #222629;
        }
    label {
        color: #86C232;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 20px;
		margin-top: 20px;
        }
     button {
        background-color: #86C232;
		border-color: #86C232;
		color: #FFFFFF;
        font-family: system-ui;
        font-size: 20px;
		font-weight: bold;
        margin-left: 30px;
		margin-top: 20px;
		width: 140px;
        }
	 input {
        color: #222629;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 10px;
		margin-top: 20px;
		width: 100px;
        }
    </style>
    <script>
        // callAPI function that takes the base and exponent numbers as parameters
        var callAPI = (base,exponent)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"base":base,"exponent":exponent});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("YOUR API GATEWAY ENDPOINT", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
</head>
<body>
    <h1>TO THE POWER OF MATH!</h1>
	<form>
        <label>Base number:</label>
        <input type="text" id="base">
        <label>...to the power of:</label>
        <input type="text" id="exponent">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('base').value,document.getElementById('exponent').value)">CALCULATE</button>
    </form>
</body>
</html>
```

3. Make sure you add your API Invoke URL on Line 60 . Note: If you do not have your API's URL, you can get it from the API Gateway console by selecting your API and choosing stages.

4. Save the file.

5. ZIP (compress) only the HTML file.

6. Open the Amplify console.

7. Choose the web app created in module one.

8. Choose the white Choose files button.

9. Select the ZIP file you created in Step 5.

10. When the file is uploaded, a deployment process will automatically begin. Once you see a green bar, your deployment will be complete.

## Test the Updated Web App

1. Choose the URL under Domain.

2. Your updated web app should load in your browser.

![WebApp](https://user-images.githubusercontent.com/98637502/216762317-4d28a9f5-6046-475f-b8aa-fcf83869f0a0.jpg)

3. Fill in the base and exponent value and choose the Call Calculate button.

4. You should see a alert that will give the user the result of the math operation performed.

![WebAppAlert](https://user-images.githubusercontent.com/98637502/216762395-62d9b838-4722-409f-a7fc-01f4163d9d16.jpg)

5. The resultant value will be stored in DynamoDb table we created earlier.

![DynamoDBUpdatedValue](https://user-images.githubusercontent.com/98637502/216762447-31426f0f-8ebf-4692-9654-18e4e7696e11.jpg)

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


    

