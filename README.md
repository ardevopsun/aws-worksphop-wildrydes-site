# 🚗 Ride Sharing Web App (Serverless with AWS)

This is a full-stack serverless ride-sharing application built using AWS services and GitHub.

---

## 📦 Architecture

- **Frontend**: HTML/CSS/JavaScript hosted on **AWS Amplify**
- **Authentication**: Amazon **Cognito**
- **API Layer**: Amazon **API Gateway**
- **Business Logic**: **AWS Lambda**
- **Database**: Amazon **DynamoDB**
- **Code Repository**: **GitHub**

---

## 🛠 Features

- 🔐 User sign-up/login using Cognito
- 📲 Request ride functionality via REST API
- 🗃️ Store and retrieve ride details from DynamoDB
- ☁️ 100% Serverless and scalable

---

## 🚀 Deployment Steps

### 1️⃣ GitHub Repository Setup

- Push your frontend (HTML, CSS, JS) to a new or existing GitHub repo  
  **OR**
- Clone/template this project and create your own GitHub repository

---

### 2️⃣ Host Frontend with AWS Amplify

- Go to **AWS Amplify → Host a Web App**
- Connect your GitHub repo
- Choose the repo and branch → click **Save and Deploy**

Note: Once deployed, open the domain link to verify.

---

### 3️⃣ Configure Cognito Authentication

#### ✅ Create User Pool:
- Go to **Cognito → User Pools**
- Choose: `Single-page application (SPA)`
- Set:
  - Pool name: wildriders-userpool
  - Sign-in identifier: `Email`
  - Attributes: `Email`
- Click **Create**

> Copy the **User Pool ID** and **Client ID**


User Pool ID: Amazon Cognito -> User pools -> select your User pool - Overview -> User Pool ID
ClientID: Amazon Cognito -> User pools -> select your User pool - App clients -> Client ID

#### ✅ Update App with Cognito Details:
- In your project repo, navigate to `js/config.js`
- Replace the following:
  - `userPoolId`
  - `clientId`
  - `region`

NOte: This change triggers auto-deployment via Amplify.

#### ✅ Test Authentication:
- Launch the frontend using Amplify domain
- Try to **Sign up and Sign in**
- After successful login, save the encrypted **Auth token** for testing API Gateway later

---

### 4️⃣ Create DynamoDB Table

- Go to **DynamoDB → Create Table**
  - Table name: `Rides`
  - Partition key: `rideId`

> 📌 Copy the **Table ARN** for IAM policy creation

---

### 5️⃣ Create IAM Role for Lambda

- Go to **IAM → Roles**
- Create a new role: `wildriderslambdarole`
  - Attach the policy: `AWSLambdaBasicExecutionRole`
- Add **Inline Policy** for DynamoDB:
  - Service: `DynamoDB`
  - Actions: `PutItem`
  - Resource: Paste the **DynamoDB ARN**
  - Save

---

### 6️⃣ Create Lambda Function

- Go to **Lambda → Create Function**
  - Runtime: `Node.js 20.x`
  - Permissions: Use existing role `wildriderslambdarole`
- Upload or paste your code from `/code/lambda.py`
- Click **Deploy**

#### ✅ Test Lambda:
- Use test event (e.g., `/code/test_lambda.txt`)
- If response = `201 Created`, check DynamoDB table for new entry

---

### 7️⃣ Create REST API in API Gateway

1. Create a REST API
Go to API Gateway > Create API

Choose REST API (Build)

Select:
Protocol: REST
Type: Regional
Name: RideSharingAPI
Click Create API


### Create a Cognito Authorizer

Go to API Gateway > your REST API

In the left menu, click on Authorizers

Click Create New Authorizer
Enter:
Name: wildryderAuth
Type: Cognito
Cognito User Pool: select your user pool
Token Source: Authorization (default header name)
Click Create

This creates a link between your API and the Cognito User Pool.

Test the connection:

 - select the wildryderAuth
 - Test Authorizers:
        Token Values : Paste the encrypted **Auth token** here
 - click button, Test Authorizers

2. Create a /ride Resource
In left panel: Resources > Actions > Create Resource

Resource Name: ride
Resource Path: /
Enable CORS

Click Create Resource

3. Create POST Method

Select /ride resource

Click Actions > Create Method > POST

Integration type: Lambda Function
Use Lambda Proxy Integration: enable
Lambda Region: us-east-1
Lambda Function: rideLambda (your function name)

Click Save and grant permission if prompted.

## Add Authorizer to Method Request

In API Gateway, go to:

Resources > /ride > POST
Click on Method Request
Under Authorization, select the Authorizer you just created (wildryderAuth)
Click the checkmark ✅ to save

This tells API Gateway: “Protect this method using tokens from Cognito.”

4. Deploy API
Click Actions > Deploy API
Deployment stage: new stage → Name: dev
Click Deploy

- Copy the **Invoke URL**


#### ✅ Update `config.js`:
- Replace `invokeUrl` with your newly copied API endpoint

Access wildriders : 

---

## 🧪 Testing the Application

- Sign up/log in using Cognito (via frontend)
- Request a ride using the UI
- Ride data should be stored in DynamoDB
- Verify Lambda logs and API Gateway metrics (optional)

---

## 📷 Screenshots

> Add screenshots of:
- Cognito configuration
- API Gateway authorizer setup
- DynamoDB ride entries
- Web UI

---

## 📚 Useful AWS Resources

- [AWS Amplify Docs](https://docs.amplify.aws/)
- [Amazon Cognito Docs](https://docs.aws.amazon.com/cognito/)
- [API Gateway Docs](https://docs.aws.amazon.com/apigateway/)
- [AWS Lambda Docs](https://docs.aws.amazon.com/lambda/)
- [DynamoDB Docs](https://docs.aws.amazon.com/dynamodb/)

---

## 👨‍💻 Author

**Arun**  
🔗 [GitHub: ardevopsun](https://github.com/ardevopsun)

---

## 💡 Tip

> This project is ideal for showcasing your DevOps + Serverless + AWS skills in your portfolio.
