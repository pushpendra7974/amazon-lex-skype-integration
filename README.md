# amazon-lex-skype-integration
An AWS Lambda function that integrates Skype Messaging with Amazon Lex using Microsoft BotBuilder framework.

## Architecture

Microsoft Bot Framework <--> API Gateway <--> this AWS Lambda function <--> Amazon Lex

The AWS Lambda function must have the following environment variables configured:
1. MICROSOFT_APP_ID - obtained while configuring bot at dev.botframework.com
2. MICROSOFT_APP_PASSWORD - obtained while configuring bot at dev.botframework.com
3. BOT_NAME - the name of the Amazon Lex bot
4. BOT_ALIAS - the name of the Amazon Lex bot alias

## Prerequisites

1. Create and publish an Amazon Lex bot [https://console.aws.amazon.com/lex]. Note the name and the alias of the bot you create.
2. Create a Microsoft BotBuilder bot [https://dev.botframework.com/bots]. Note the APP_ID and the APP_PASSWORD of the BotBuilder bot.
3. Add Skype as a channel and then click on the Skype channel to add the bot to your Skype contacts so that it shows up in your Skype account.
4. Sign into IAM console [https://console.aws.amazon.com/iam/] and create a new IAM Role with the following custom policy:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": [
                "*"
            ]
        },
	    {
            "Effect": "Allow",
            "Action": [
                "lex:PostText"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

## Usage

1. Clone the repo: `git clone https://github.com/aws-samples/amazon-lex-skype-integration.git`
2. Install the necessary modules:
```
cd amazon-lex-skype-integration
npm install
```
3. Zip the package: `zip -r amazon-lex-skype-integration .`
4. Sign into AWS Lambda [https://console.aws.amazon.com/lambda]
5. Create a new Lambda function. Reference the IAM Role created in prerequisite step 4. Upload the zip file created in step 3.
6. Within the Lambda function configuration, create environment variables with the following names: BOT_NAME, BOT_ALIAS, MICROSOFT_APP_ID, and MICROSOFT_APP_PASSWORD and give them the values that you noted during prerequisite steps 1 and 2.
7. Create an AWS API Gateway API with `Use Lambda Proxy Integration` checked. Point it to to the AWS Lambda function that you just created in step #5:
![Make sure to check Use Lambda Proxy Integration](/images/api-gateway-proxy.png?raw=true "Make sure to check Lambda Proxy Integration")
 [http://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-create-api-as-simple-proxy-for-lambda.html#api-gateway-create-api-as-simple-proxy-for-lambda-build].
8. Deploy the API by choosing Actions, and then choosing Deploy API. When you deploy for the first time, you have to create a new stage. Record the Invoke URL for the stage that you deployed to.
9. Navigate to the Microsoft BotBuilder bot and configure the messaging endpoint with the URL that you noted in the last step. This tells the BotBuilder Framework to forward any message to our API Gateway endpoint.
10. The integration should work now. Test the integration using the Skype mobile or desktop client.

## Troubleshooting

* Within the AWS Lambda console, switch to monitoring and click on CloudWatch Logs to see the error messages generated by AWS Lambda.
* Make sure that when you send a message through Skype, the Lambda function gets called and logs its output into CloudWatch Logs.
