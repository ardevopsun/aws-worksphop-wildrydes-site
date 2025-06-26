# Ride Sharing Web App (Serverless with AWS)

This is a full-stack serverless ride-sharing application built using AWS services and GitHub.

---

## Architecture

- **Frontend**: HTML/CSS/JavaScript hosted on **AWS Amplify**
- **Authentication**: Amazon **Cognito**
- **API Layer**: Amazon **API Gateway**
- **Business Logic**: **AWS Lambda**
- **Database**: Amazon **DynamoDB**
- **Code Repository**: **GitHub**

## Features

-  User sign-up/login using Cognito
-  Request ride functionality via REST API
-  Store and retrieve ride details from DynamoDB
- 100% Serverless and scalable

## 1 Deployment Steps

### 1.1 GitHub Repository Setup

- Push your frontend (HTML, CSS, JS) to a new or existing GitHub repo  
  **OR**
- Clone/template this project and create your own GitHub repository


### 1.2 Host Frontend with AWS Amplify

- Go to **AWS Amplify → Host a Web App**
- Connect your GitHub repo
- Choose the repo and branch → click **Save and Deploy**

> Note: Once deployed, open the domain link to verify.


## 2 Configure Cognito Authentication

#### 2.1 Create User Pool:
- Go to **Cognito → User Pools**
- Choose: `Single-page application (SPA)`
- Set:
  - Pool name: wildriders-userpool
  - Sign-in identifier: `Email`
  - Attributes: `Email`
- Click **Create**

> Note: Copy the **User Pool ID** and **Client ID**

**User Pool ID:** Amazon Cognito -> User pools -> select your User pool - Overview -> User Pool ID

**ClientID:** Amazon Cognito -> User pools -> select your User pool - App clients -> Client ID

#### 2.2 Update App with Cognito Details:
- In your project repo, navigate to `js/config.js`
- Replace the following:
  - `userPoolId`
  - `clientId`
  - `region`

> Note: This change triggers auto-deployment via Amplify.

#### 2.3 Test Authentication:
- Launch the frontend using Amplify domain
- Try to **Sign up and Sign in**
- After successful login, save the encrypted **Auth token** for testing API Gateway later

## 3 Create DynamoDB Table

- Go to **DynamoDB → Create Table**
  - Table name: `Rides`
  - Partition key: `rideId`

> Note: Copy the **Table ARN** for IAM policy creation

## 4 Create IAM Role for Lambda

- Go to **IAM → Roles**
- Create a new role: `wildriderslambdarole`
  - Attach the policy: `AWSLambdaBasicExecutionRole`
- Add **Inline Policy** for DynamoDB:
  - Service: `DynamoDB`
  - Actions: `PutItem`
  - Resource: Paste the **DynamoDB ARN**
  - Save


## 5 Create Lambda Function

- Go to **Lambda → Create Function**
  - Runtime: `Node.js 20.x`
  - Permissions: Use existing role `wildriderslambdarole`
- Upload or paste your code from `/code/lambda.py`
- Click **Deploy**

#### 5.1 Test Lambda:
- Use test event code from "/code/test_lambda.txt"
- If response = `201 Created`, check DynamoDB table for new entry


## 6 Create REST API in API Gateway

#### 6.1 Create a REST API

- Go to API Gateway > Create API
  - Choose REST API (Build)
  - Select:
  - Protocol: REST
  - Type: Regional
  - Name: RideSharingAPI
  - Click Create API


#### 6.2 Create a Cognito Authorizer

- Go to API Gateway > your REST API
  - In the left menu, click on Authorizers
  - Click Create New Authorizer
    - Enter:
    - Name: wildryderAuth
    - Type: Cognito
    - Cognito User Pool: select your user pool
    - Token Source: Authorization (default header name)
    - Click Create

> Note: This creates a link between your API and the Cognito User Pool.

**Test the connection:**

 - select the **wildryderAuth**__
   - Test Authorizers:
          Token Values : Paste the encrypted **Auth token** here
   - click button, Test Authorizers

#### 6.3 Create a /ride Resource

Go to API Gateway > your REST API

- In left panel: Resources > Actions > Create Resource
   - Resource Name: ride
   - Resource Path: /
   - Enable CORS 
   - Click Create Resource

#### 6.4 Create POST Method

- Select /ride resource
   - Click Actions > Create Method > POST
   - Integration type: Lambda Function
   - Use Lambda Proxy Integration: enable
   - Lambda Region: us-east-1
   - Lambda Function: rideLambda (your function name)

> Note: Click Save and grant permission if prompted.

#### 6.5 Add Authorizer to Method Request

**In API Gateway, go to:**

  - Resources > /ride > POST
    - Click on Method Request
    - Under Authorization, select the Authorizer you just created (wildryderAuth)
    - Click the checkmark to save

> Note: This tells API Gateway: “Protect this method using tokens from Cognito.”

#### 6.6 Deploy API
Click Actions > Deploy API
Deployment stage: new stage → Name: dev
Click Deploy

> Note: Copy the **Invoke URL**


## 7 Update `config.js`:

- Replace `invokeUrl` with your newly copied API endpoint

    Access wildriders : http://<domain_url>


## 8 Testing the Application

- Sign up/log in using Cognito (via frontend)
- Request a ride using the UI
- Ride data should be stored in DynamoDB
- Verify Lambda logs and API Gateway metrics (optional)



###  Useful AWS Resources

- [AWS Amplify Docs](https://docs.amplify.aws/)
- [Amazon Cognito Docs](https://docs.aws.amazon.com/cognito/)
- [API Gateway Docs](https://docs.aws.amazon.com/apigateway/)
- [AWS Lambda Docs](https://docs.aws.amazon.com/lambda/)
- [DynamoDB Docs](https://docs.aws.amazon.com/dynamodb/)


**Arun**  
🔗 [GitHub: ardevopsun](https://github.com/ardevopsun)

---

## 💡 Tip

> This project is ideal for showcasing your DevOps + Serverless + AWS skills in your portfolio.
