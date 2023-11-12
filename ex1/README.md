# go-al2

This is a sample template for Go on Amazon Linux 2 (AL2) - Below is a brief explanation of what we have generated for you:

```bash
.
├── README.md         <- This file>
├── hello-world
│   ├── Makefile      <- Make to automate build>
│   ├── go.mod        <- Dependencies
│   ├── main.go       <- Lambda function code
│   └── main_test.go  <- Unit tests
├── samconfig.toml
└── template.yaml
```

## Requirements

* AWS CLI already configured with Administrator permission
* [Docker installed](https://www.docker.com/community-edition)
* [Golang](https://golang.org)
* SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)


### SAM CLI
#### installation
1. instructions: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html
2. download the aws sam package ()
3. sudo installer -pkg /Users/robert/Downloads/aws-sam-cli-macos-arm64.pkg -target /
4. `which sam`
5. `sam --version`

## Local development
1. `mkdir hello-world`
2. `cd hello-world`
3. `go mod init github.com/robertocamp/lambda-v4`
4. `go get github.com/aws/aws-lambda-go/events`
5. `touch Makefile`
6. develop main.go and main_test.go
7. run tests:  `go test -v` (from inside **hello-world** directory)


### sam build

For AWS SAM projects, the `sam build` command should typically be run from the root directory of your project, where the `template.yaml` file is located. This is because `sam build` uses the `template.yaml` file to understand the structure of your serverless application, including the location of the Lambda function source code, runtime configurations, and other resources.

In your case, the project root seems to be `/Users/robert/go/src/github.com/lambda-v4/ex1`, as indicated by the presence of the `template.yaml` file in this directory. Here's what you should do:

1. **Navigate to the Project Root:**
   Change your current working directory to the project root. In your case, it should be:

   ```bash
   cd /Users/robert/go/src/github.com/lambda-v4/ex1
   ```

2. **Run `sam build`:**
   Once you're in the project root directory, run the `sam build` command:

   ```bash
   sam build
   ```

   This command will read the `template.yaml` file to identify the resources to build, including your Go Lambda function located in the `hello-world` directory.

3. **Ensure Correct Configuration in `template.yaml`:**
   Make sure that your `template.yaml` correctly points to the `hello-world` directory for the Lambda function code. For example, the CodeUri property under your Lambda function definition in `template.yaml` should be pointing to the `hello-world` directory.

By running `sam build` from the project root, you ensure that all the configurations and paths defined in your `template.yaml` are correctly interpreted and that the SAM CLI can build the necessary resources for your serverless application.

In this example we use the built-in `sam build` to automatically download all the dependencies and package our build target.   
Read more about [SAM Build here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-build.html)

#### sam build output

```
❯ sam build

        SAM CLI now collects telemetry to better understand customer needs.

        You can OPT OUT and disable telemetry collection by setting the
        environment variable SAM_CLI_TELEMETRY=0 in your shell.
        Thanks for your help!

        Learn More: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-telemetry.html

Building codeuri: /Users/robert/go/src/github.com/lambda-v4/ex1/hello-world runtime: provided.al2 metadata: {'BuildMethod': 'makefile'} architecture: x86_64       
functions: HelloWorldFunction                                                                                                                                      
HelloWorldFunction: Running CustomMakeBuilder:CopySource                                                                                                           
HelloWorldFunction: Running CustomMakeBuilder:MakeBuild                                                                                                            
HelloWorldFunction: Current Artifacts Directory : /Users/robert/go/src/github.com/lambda-v4/ex1/.aws-sam/build/HelloWorldFunction                                  
GOOS=linux go build -o bootstrap
cp ./bootstrap /Users/robert/go/src/github.com/lambda-v4/ex1/.aws-sam/build/HelloWorldFunction/.

Build Succeeded

Built Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yaml

Commands you can use next
=========================
[*] Validate SAM template: sam validate
[*] Invoke Function: sam local invoke
[*] Test Function in the Cloud: sam sync --stack-name {{stack-name}} --watch
[*] Deploy: sam deploy --guided
```

#### sam test with local api
- `sam local start-api`
- http://127.0.0.1:3000/hello

#### package for deployment
- `sam deploy --guided`

If the previous command ran successfully you should now be able to hit the following local endpoint to invoke your function `http://localhost:3000/hello`

**SAM CLI** is used to emulate both Lambda and API Gateway locally and uses our `template.yaml` to understand how to bootstrap this environment (runtime, where the source code is, etc.) - The following excerpt is what the CLI will read in order to initialize an API and its routes:

```yaml
...
Events:
    HelloWorld:
        Type: Api # More info about API Event Source: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#api
        Properties:
            Path: /hello
            Method: get
```

## Packaging and deployment

AWS Lambda Golang runtime requires a flat folder with the executable generated on build step. SAM will use `CodeUri` property to know where to look up for the application:

```yaml
...
    FirstFunction:
        Type: AWS::Serverless::Function
        Properties:
            CodeUri: hello_world/
            ...
```

To deploy your application for the first time, run the following in your shell:

```bash
sam deploy --guided
```

The command will package and deploy your application to AWS, with a series of prompts:

* **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **AWS Region**: The AWS region you want to deploy your app to.
* **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
* **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modified IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
* **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

You can find your API Gateway Endpoint URL in the output values displayed after deployment.

### Testing

We use `testing` package that is built-in in Golang and you can simply run the following command to run our tests:

1. cd <PROJECT ROOT>/hello-world
2. go test -v


## Bringing to the next level

Here are a few ideas that you can use to get more acquainted as to how this overall process works:

* Create an additional API resource (e.g. /hello/{proxy+}) and return the name requested through this new path
* Update unit test to capture that
* Package & Deploy

Next, you can use the following resources to know more about beyond hello world samples and how others structure their Serverless applications:

* [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/)

## reference: local testing , package and build
```
❯ sam local start-api
Mounting HelloWorldFunction at http://127.0.0.1:3000/hello [GET]                                                                                                   
You can now browse to the above endpoints to invoke your functions. You do not need to restart/reload SAM CLI while working on your functions, changes will be     
reflected instantly/automatically. If you used sam build before running local commands, you will need to re-run sam build for the changes to be picked up. You only
need to restart SAM CLI if you update your AWS SAM template                                                                                                        
2023-11-12 16:17:06 WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:3000
2023-11-12 16:17:06 Press CTRL+C to quit
2023-11-12 16:17:21 127.0.0.1 - - [12/Nov/2023 16:17:21] "GET / HTTP/1.1" 403 -
2023-11-12 16:17:22 127.0.0.1 - - [12/Nov/2023 16:17:22] "GET /favicon.ico HTTP/1.1" 403 -
Invoking bootstrap (provided.al2)                                                                                                                                  
Local image was not found.                                                                                                                                         
Removing rapid images for repo public.ecr.aws/sam/emulation-provided.al2                                                                                           
Building image.......................................................................................................
Using local image: public.ecr.aws/lambda/provided:al2-rapid-x86_64.                                                                                                
                                                                                                                                                                   
Mounting /Users/robert/go/src/github.com/lambda-v4/ex1/.aws-sam/build/HelloWorldFunction as /var/task:ro,delegated, inside runtime container                       
START RequestId: 0294e0b8-d4a2-4950-914c-0e3a7df8bc81 Version: $LATEST
END RequestId: 0294e0b8-d4a2-4950-914c-0e3a7df8bc81
REPORT RequestId: 0294e0b8-d4a2-4950-914c-0e3a7df8bc81  Init Duration: 1.07 ms  Duration: 568.29 ms     Billed Duration: 569 ms Memory Size: 128 MB     Max Memory Used: 128 MB

No Content-Type given. Defaulting to 'application/json'.                                                                                                           
2023-11-12 16:23:23 127.0.0.1 - - [12/Nov/2023 16:23:23] "GET /hello HTTP/1.1" 200 -
2023-11-12 16:23:23 127.0.0.1 - - [12/Nov/2023 16:23:23] "GET /favicon.ico HTTP/1.1" 403 -
^C
Commands you can use next
=========================
[*] Validate SAM template: sam validate
[*] Test Function in the Cloud: sam sync --stack-name {{stack-name}} --watch
[*] Deploy: sam deploy --guided

❯ 
❯ 
❯ sam deploy --guided

Configuring SAM deploy
======================

        Looking for config file [samconfig.toml] :  Not found

        Setting default arguments for 'sam deploy'
        =========================================
        Stack Name [sam-app]: 
        AWS Region [us-east-2]: 
        #Shows you resources changes to be deployed and require a 'Y' to initiate deploy
        Confirm changes before deploy [y/N]: y
        #SAM needs permission to be able to create roles to connect to the resources in your template
        Allow SAM CLI IAM role creation [Y/n]: y
        #Preserves the state of previously provisioned resources when an operation fails
        Disable rollback [y/N]: y
        HelloWorldFunction has no authentication. Is this okay? [y/N]: y
        Save arguments to configuration file [Y/n]: y
        SAM configuration file [samconfig.toml]: 
        SAM configuration environment [default]: 

        Looking for resources needed for deployment:
        Creating the required resources...
        Successfully created!

        Managed S3 bucket: aws-sam-cli-managed-default-samclisourcebucket-atos002jsajn
        A different default S3 bucket can be set in samconfig.toml and auto resolution of buckets turned off by setting resolve_s3=False

        Saved arguments to config file
        Running 'sam deploy' for future deployments will use the parameters saved above.
        The above parameters can be changed by modifying samconfig.toml
        Learn more about samconfig.toml syntax at 
        https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-config.html

        Uploading to sam-app/9e9110c8a64953bca97a9511b94a5c60  4801383 / 4801383  (100.00%)

        Deploying with following values
        ===============================
        Stack name                   : sam-app
        Region                       : us-east-2
        Confirm changeset            : True
        Disable rollback             : True
        Deployment s3 bucket         : aws-sam-cli-managed-default-samclisourcebucket-atos002jsajn
        Capabilities                 : ["CAPABILITY_IAM"]
        Parameter overrides          : {}
        Signing Profiles             : {}

Initiating deployment
=====================

        Uploading to sam-app/2bd675a85e491b6e2b93935741d32606.template  1239 / 1239  (100.00%)


Waiting for changeset to be created..

CloudFormation stack changeset
-------------------------------------------------------------------------------------------------------------------------------------------------------------
Operation                               LogicalResourceId                       ResourceType                            Replacement                           
-------------------------------------------------------------------------------------------------------------------------------------------------------------
+ Add                                   HelloWorldFunctionCatchAllPermissionP   AWS::Lambda::Permission                 N/A                                   
                                        rod                                                                                                                   
+ Add                                   HelloWorldFunctionRole                  AWS::IAM::Role                          N/A                                   
+ Add                                   HelloWorldFunction                      AWS::Lambda::Function                   N/A                                   
+ Add                                   ServerlessRestApiDeployment47fc2d5f9d   AWS::ApiGateway::Deployment             N/A                                   
+ Add                                   ServerlessRestApiProdStage              AWS::ApiGateway::Stage                  N/A                                   
+ Add                                   ServerlessRestApi                       AWS::ApiGateway::RestApi                N/A                                   
-------------------------------------------------------------------------------------------------------------------------------------------------------------


Changeset created successfully. arn:aws:cloudformation:us-east-2:240195868935:changeSet/samcli-deploy1699828345/a0f15db2-f62e-46cd-bd55-84eaf6f4cf6c


Previewing CloudFormation changeset before deployment
======================================================
Deploy this changeset? [y/N]: y

2023-11-12 16:32:53 - Waiting for stack create/update to complete

CloudFormation events from stack operations (refresh every 5.0 seconds)
-------------------------------------------------------------------------------------------------------------------------------------------------------------
ResourceStatus                          ResourceType                            LogicalResourceId                       ResourceStatusReason                  
-------------------------------------------------------------------------------------------------------------------------------------------------------------
CREATE_IN_PROGRESS                      AWS::CloudFormation::Stack              sam-app                                 User Initiated                        
CREATE_IN_PROGRESS                      AWS::IAM::Role                          HelloWorldFunctionRole                  -                                     
CREATE_IN_PROGRESS                      AWS::IAM::Role                          HelloWorldFunctionRole                  Resource creation Initiated           
CREATE_COMPLETE                         AWS::IAM::Role                          HelloWorldFunctionRole                  -                                     
CREATE_IN_PROGRESS                      AWS::Lambda::Function                   HelloWorldFunction                      -                                     
CREATE_IN_PROGRESS                      AWS::Lambda::Function                   HelloWorldFunction                      Resource creation Initiated           
CREATE_COMPLETE                         AWS::Lambda::Function                   HelloWorldFunction                      -                                     
CREATE_IN_PROGRESS                      AWS::ApiGateway::RestApi                ServerlessRestApi                       -                                     
CREATE_IN_PROGRESS                      AWS::ApiGateway::RestApi                ServerlessRestApi                       Resource creation Initiated           
CREATE_COMPLETE                         AWS::ApiGateway::RestApi                ServerlessRestApi                       -                                     
CREATE_IN_PROGRESS                      AWS::ApiGateway::Deployment             ServerlessRestApiDeployment47fc2d5f9d   -                                     
CREATE_IN_PROGRESS                      AWS::Lambda::Permission                 HelloWorldFunctionCatchAllPermissionP   -                                     
                                                                                rod                                                                           
CREATE_IN_PROGRESS                      AWS::Lambda::Permission                 HelloWorldFunctionCatchAllPermissionP   Resource creation Initiated           
                                                                                rod                                                                           
CREATE_COMPLETE                         AWS::Lambda::Permission                 HelloWorldFunctionCatchAllPermissionP   -                                     
                                                                                rod                                                                           
CREATE_IN_PROGRESS                      AWS::ApiGateway::Deployment             ServerlessRestApiDeployment47fc2d5f9d   Resource creation Initiated           
CREATE_COMPLETE                         AWS::ApiGateway::Deployment             ServerlessRestApiDeployment47fc2d5f9d   -                                     
CREATE_IN_PROGRESS                      AWS::ApiGateway::Stage                  ServerlessRestApiProdStage              -                                     
CREATE_IN_PROGRESS                      AWS::ApiGateway::Stage                  ServerlessRestApiProdStage              Resource creation Initiated           
CREATE_COMPLETE                         AWS::ApiGateway::Stage                  ServerlessRestApiProdStage              -                                     
CREATE_COMPLETE                         AWS::CloudFormation::Stack              sam-app                                 -                                     
-------------------------------------------------------------------------------------------------------------------------------------------------------------

CloudFormation outputs from deployed stack
----------------------------------------------------------------------------------------------------------------------------------------------------------------
Outputs                                                                                                                                                        
----------------------------------------------------------------------------------------------------------------------------------------------------------------
Key                 HelloWorldFunctionIamRole                                                                                                                  
Description         Implicit IAM Role created for Hello World function                                                                                         
Value               arn:aws:iam::240195868935:role/sam-app-HelloWorldFunctionRole-7AzMkh8ixzrj                                                                 

Key                 HelloWorldAPI                                                                                                                              
Description         API Gateway endpoint URL for Prod environment for First Function                                                                           
Value               https://y1f21v5xs0.execute-api.us-east-2.amazonaws.com/Prod/hello/                                                                         

Key                 HelloWorldFunction                                                                                                                         
Description         First Lambda Function ARN                                                                                                                  
Value               arn:aws:lambda:us-east-2:240195868935:function:sam-app-HelloWorldFunction-7itvw8E8ETXr                                                     
----------------------------------------------------------------------------------------------------------------------------------------------------------------


Successfully created/updated stack - sam-app in us-east-2


╭─ 🦮   ~/go/src/github.com/lambda-v4/ex1    initial-commit !4 ?4 ····························································· ✔  2m 54s   16:33:35  ─╮
╰─                                                                                                                                                              ─╯
```
## links
- [sampe app:](https://github.com/aws-samples/sessions-with-aws-sam/tree/master/go-al2)

