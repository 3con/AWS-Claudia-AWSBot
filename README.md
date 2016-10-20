# AWS Claudia AWSBot
A Slack bot to stop and start selected AWS EC2 instances and generally keep
an eye on your AWS estate. The following commands are available:

    /awsbot help
    /awsbot show all
    /awsbot show running
    /awsbot show stopped
    /awsbot start instance_name
    /awsbot stop instance_name

Clearly, anyone with access to your Slack channel will be able to stop and
start selected instances so use with caution.

The bot is written in Node.js and
runs in AWS Lambda via the API Gateway.
It is deployed using Claudia.js - see
https://claudiajs.com/.

## Installation

Install Claudia.js with:

    npm install claudia -g

Then read https://claudiajs.com/tutorials/hello-world-chatbot.html.

Follow the tutorial and create a project folder
but use the following commands:

    npm init

Give your bot a name - e.g. 'awsbot' - and description
and put your email address
as author. Leave everything else as is. Then install the dependencies with:

    npm install claudia-bot-builder -S
    npm install promise-delay -S
    npm install aws-sdk -S

Put bot.js in the project folder.

Edit the two arrays (starters and stoppers) at the top of bot.js to configure
the names of any instances you want your Slack users to be able
to stop and start (the lists can be different and/or empty).

Follow https://claudiajs.com/tutorials/installing.html to give Claudia.js
enough AWS access to deploy the Lambda function and API Gateway.

Then deploy your bot to AWS with the following command:

    claudia create --region eu-west-1 --api-module bot

Go to https://api.slack.com/ to configure a new integration
for your Slack team. Then run:

    claudia update --region eu-west-1 --api-module bot --timeout 120 --allow-recursion --configure-slack-slash-command

Finally, you need to manually add the IAM policy in
awsbot-stopstartdesc-policy.json to the newly
created IAM role (which is called 'awsbot-executor'
if you use my suggested naming).
DO NOT DO THIS before running the 'claudia update' command above.

That's it, you're done.

If you modify the bot.js code, you can redeploy with:

    claudia update --region eu-west-1 --api-module bot

## Removal

To delete everything, remove the 'awsbot-stopstartdesc-policy' policy from the bot's
IAM role and then try the following:

    claudia destroy
    rm claudia.json

However, sometimes this doesn't seem to work reliably. If so, manually delete
the stuff created under IAM Roles, Lambda functions and API Gateway.
