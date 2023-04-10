# Using CloudFormation with Kinesis Data Analytics<a name="lambda-cfn-flink"></a>

The following exercises shows how to start a Flink application created via AWS CloudFormation using a Lambda function in the same stack\. 

## Before you begin<a name="before-you-begin"></a>

Before you begin this exercise, follow the steps on creating a Flink application using AWS CloudFormation at [AWS::KinesisAnalytics::Application](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-kinesisanalytics-application.html)\.

## Writing a Lambda function<a name="write-lambda-function"></a>

To start a Flink application after creation or update, we use the kinesisanalyticsv2 [start\-application](https://docs.aws.amazon.com/cli/latest/reference/kinesisanalyticsv2/start-application.html) API\. The call will be triggered by an AWS CloudFormation event after Flink application creation\. We’ll discuss how to set up the stack to trigger the Lambda function later in this exercise, but first we focus on the Lambda function declaration and its code\. We use `Python3.8` runtime in this example\. 

```
StartApplicationLambda:
    Type: AWS::Lambda::Function
    DependsOn: StartApplicationLambdaRole
    Properties:
      Description: Starts an application when invoked.
      Runtime: python3.8
      Role: !GetAtt StartApplicationLambdaRole.Arn
      Handler: index.lambda_handler
      Timeout: 30
      Code:
        ZipFile: |
          import logging
          import cfnresponse
          import boto3
          
          logger = logging.getLogger()
          logger.setLevel(logging.INFO)
          
          def lambda_handler(event, context):
            logger.info('Incoming CFN event {}'.format(event))
            
            try:
              application_name = event['ResourceProperties']['ApplicationName']
              
              # filter out events other than Create or Update,
              # you can also omit Update in order to start an application on Create only.
              if event['RequestType'] not in ["Create", "Update"]:
                logger.info('No-op for Application {} because CFN RequestType {} is filtered'.format(application_name, event['RequestType'])) 
                cfnresponse.send(event, context, cfnresponse.SUCCESS, {})
                
                return
              
              # use kinesisanalyticsv2 API to start an application.
              client_kda = boto3.client('kinesisanalyticsv2', region_name=event['ResourceProperties']['Region'])
              
              # get application status.
              describe_response = client_kda.describe_application(ApplicationName=application_name)
              application_status = describe_response['ApplicationDetail']['ApplicationStatus']
              
              # an application can be started from 'READY' status only.
              if application_status != 'READY':
                logger.info('No-op for Application {} because ApplicationStatus {} is filtered'.format(application_name, application_status)) 
                cfnresponse.send(event, context, cfnresponse.SUCCESS, {})
                
                return
              
              # create RunConfiguration. 
              run_configuration = { 
                'ApplicationRestoreConfiguration': {
                  'ApplicationRestoreType': 'RESTORE_FROM_LATEST_SNAPSHOT',
                }
              }
                            
              logger.info('RunConfiguration for Application {}: {}'.format(application_name, run_configuration)) 
              
              # this call doesn't wait for an application to transfer to 'RUNNING' state.
              client_kda.start_application(ApplicationName=application_name, RunConfiguration=run_configuration)
              
              logger.info('Started Application: {}'.format(application_name)) 
              cfnresponse.send(event, context, cfnresponse.SUCCESS, {})
            except Exception as err:
              logger.error(err)
              cfnresponse.send(event,context, cfnresponse.FAILED, {"Data": str(err)})
```

In the preceding code, Lambda will process incoming AWS CloudFormation events, filter out everything besides `Create` and `Update`, get the application state and start it if the state is `READY`\. In order to get the application state, you need to create the Lambda role, as shown following:

## Creating a Lambda role<a name="create-lambda-role"></a>

You create a role for Lambda to successfully “talk” to the application and write logs\. This role will use default managed policies, but you might want to narrow it down using custom policies\.

```
StartApplicationLambdaRole:
    Type: AWS::IAM::Role
    DependsOn: TestFlinkApplication
    Properties:
      Description: A role for lambda to use while interacting with an application.
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
            Action:
              - sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonKinesisAnalyticsFullAccess
        - arn:aws:iam::aws:policy/CloudWatchLogsFullAccess
      Path: /
```

Note that the Lambda resources will be created after creation of the Flink application in the same stack because they depend on it\.

## Invoking the Lambda function<a name="invoking-lambda-function"></a>

Now all that is left is to invoke the Lambda function\. This is done using a [custom resource](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cfn-customresource.html)\.

```
StartApplicationLambdaInvoke:
    Description: Invokes StartApplicationLambda to start an application.
    Type: AWS::CloudFormation::CustomResource
    DependsOn: StartApplicationLambda
    Version: "1.0"
    Properties:
      ServiceToken: !GetAtt StartApplicationLambda.Arn
      Region: !Ref AWS::Region
      ApplicationName: !Ref TestFlinkApplication
```

This is all you need to start your Flink application using Lambda\. You are now ready to create your own stack or use the full example below to see how all those steps work in practice\.

## Full example<a name="lambda-cfn-flink-full-example"></a>

The following example is a slightly extended version of the steps above with an additional `RunConfiguration` tweaking done via [template parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)\. This is a working stack for you to try\. Be sure to read the accompanying notes: 

stack\.yaml

```
Description: 'KinesisAnalyticsV2 CloudFormation Test Application'
Parameters:
  ApplicationRestoreType:
    Description: ApplicationRestoreConfiguration option, can be SKIP_RESTORE_FROM_SNAPSHOT, RESTORE_FROM_LATEST_SNAPSHOT or RESTORE_FROM_CUSTOM_SNAPSHOT.
    Type: String
    Default: SKIP_RESTORE_FROM_SNAPSHOT
    AllowedValues: [ SKIP_RESTORE_FROM_SNAPSHOT, RESTORE_FROM_LATEST_SNAPSHOT, RESTORE_FROM_CUSTOM_SNAPSHOT ]
  SnapshotName:
    Description: ApplicationRestoreConfiguration option, name of a snapshot to restore to, used with RESTORE_FROM_CUSTOM_SNAPSHOT ApplicationRestoreType.
    Type: String
    Default: ''
  AllowNonRestoredState:
    Description: FlinkRunConfiguration option, can be true or false.
    Default: true
    Type: String
    AllowedValues: [ true, false ]
  CodeContentBucketArn:
    Description: ARN of a bucket with application code.
    Type: String
  CodeContentFileKey:
    Description: A jar filename with an application code inside a bucket.
    Type: String
Conditions:
  IsSnapshotNameEmpty: !Equals [ !Ref SnapshotName, '' ]
Resources:
  TestServiceExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service: 
                - kinesisanalytics.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonKinesisFullAccess
        - arn:aws:iam::aws:policy/AmazonS3FullAccess
      Path: /
  InputKinesisStream:
    Type: AWS::Kinesis::Stream
    Properties:
      ShardCount: 1
  OutputKinesisStream:
    Type: AWS::Kinesis::Stream
    Properties:
      ShardCount: 1
  TestFlinkApplication:
    Type: 'AWS::KinesisAnalyticsV2::Application'
    Properties:
      ApplicationName: 'CFNTestFlinkApplication'
      ApplicationDescription: 'Test Flink Application'
      RuntimeEnvironment: 'FLINK-1_15'
      ServiceExecutionRole: !GetAtt TestServiceExecutionRole.Arn
      ApplicationConfiguration:
        EnvironmentProperties:
          PropertyGroups:
            - PropertyGroupId: 'KinesisStreams'
              PropertyMap:
                INPUT_STREAM_NAME: !Ref InputKinesisStream
                OUTPUT_STREAM_NAME: !Ref OutputKinesisStream
                AWS_REGION: !Ref AWS::Region
        FlinkApplicationConfiguration:
          CheckpointConfiguration:
            ConfigurationType: 'CUSTOM'
            CheckpointingEnabled: True
            CheckpointInterval: 1500
            MinPauseBetweenCheckpoints: 500
          MonitoringConfiguration:
            ConfigurationType: 'CUSTOM'
            MetricsLevel: 'APPLICATION'
            LogLevel: 'INFO'
          ParallelismConfiguration:
            ConfigurationType: 'CUSTOM'
            Parallelism: 1
            ParallelismPerKPU: 1
            AutoScalingEnabled: True
        ApplicationSnapshotConfiguration:
          SnapshotsEnabled: True
        ApplicationCodeConfiguration:
          CodeContent:
            S3ContentLocation:
              BucketARN: !Ref CodeContentBucketArn
              FileKey: !Ref CodeContentFileKey
          CodeContentType: 'ZIPFILE'     
  StartApplicationLambdaRole:
    Type: AWS::IAM::Role
    DependsOn: TestFlinkApplication
    Properties:
      Description: A role for lambda to use while interacting with an application.
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
            Action:
              - sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AmazonKinesisAnalyticsFullAccess
        - arn:aws:iam::aws:policy/CloudWatchLogsFullAccess
      Path: /
  StartApplicationLambda:
    Type: AWS::Lambda::Function
    DependsOn: StartApplicationLambdaRole
    Properties:
      Description: Starts an application when invoked.
      Runtime: python3.8
      Role: !GetAtt StartApplicationLambdaRole.Arn
      Handler: index.lambda_handler
      Timeout: 30
      Code:
        ZipFile: |
          import logging
          import cfnresponse
          import boto3
          
          logger = logging.getLogger()
          logger.setLevel(logging.INFO)
          
          def lambda_handler(event, context):
            logger.info('Incoming CFN event {}'.format(event))
            
            try:
              application_name = event['ResourceProperties']['ApplicationName']
              
              # filter out events other than Create or Update,
              # you can also omit Update in order to start an application on Create only.
              if event['RequestType'] not in ["Create", "Update"]:
                logger.info('No-op for Application {} because CFN RequestType {} is filtered'.format(application_name, event['RequestType'])) 
                cfnresponse.send(event, context, cfnresponse.SUCCESS, {})
                
                return
              
              # use kinesisanalyticsv2 API to start an application.
              client_kda = boto3.client('kinesisanalyticsv2', region_name=event['ResourceProperties']['Region'])
              
              # get application status.
              describe_response = client_kda.describe_application(ApplicationName=application_name)
              application_status = describe_response['ApplicationDetail']['ApplicationStatus']
              
              # an application can be started from 'READY' status only.
              if application_status != 'READY':
                logger.info('No-op for Application {} because ApplicationStatus {} is filtered'.format(application_name, application_status)) 
                cfnresponse.send(event, context, cfnresponse.SUCCESS, {})
                
                return
              
              # create RunConfiguration from passed parameters. 
              run_configuration = { 
                'FlinkRunConfiguration': {
                  'AllowNonRestoredState': event['ResourceProperties']['AllowNonRestoredState'] == 'true'
                },
                'ApplicationRestoreConfiguration': {
                  'ApplicationRestoreType': event['ResourceProperties']['ApplicationRestoreType'],
                }
              }
              
              # add SnapshotName to RunConfiguration if specified.
              if event['ResourceProperties']['SnapshotName'] != '':
                run_configuration['ApplicationRestoreConfiguration']['SnapshotName'] = event['ResourceProperties']['SnapshotName']
              
              logger.info('RunConfiguration for Application {}: {}'.format(application_name, run_configuration)) 
              
              # this call doesn't wait for an application to transfer to 'RUNNING' state.
              client_kda.start_application(ApplicationName=application_name, RunConfiguration=run_configuration)
              
              logger.info('Started Application: {}'.format(application_name)) 
              cfnresponse.send(event, context, cfnresponse.SUCCESS, {})
            except Exception as err:
              logger.error(err)
              cfnresponse.send(event,context, cfnresponse.FAILED, {"Data": str(err)})
  StartApplicationLambdaInvoke:
    Description: Invokes StartApplicationLambda to start an application.
    Type: AWS::CloudFormation::CustomResource
    DependsOn: StartApplicationLambda
    Version: "1.0"
    Properties:
      ServiceToken: !GetAtt StartApplicationLambda.Arn
      Region: !Ref AWS::Region
      ApplicationName: !Ref TestFlinkApplication
      ApplicationRestoreType: !Ref ApplicationRestoreType
      SnapshotName: !Ref SnapshotName
      AllowNonRestoredState: !Ref AllowNonRestoredState
```

Again, you might want to adjust the roles for Lambda as well as an application itself\.

Before creating the stack above, don’t forget to specify your parameters\.

parameters\.json

```
[
  {
    "ParameterKey": "CodeContentBucketArn",
    "ParameterValue": "YOUR_BUCKET_ARN"
  },
  {
    "ParameterKey": "CodeContentFileKey",
    "ParameterValue": "YOUR_JAR"
  },
  {
    "ParameterKey": "ApplicationRestoreType",
    "ParameterValue": "SKIP_RESTORE_FROM_SNAPSHOT"
  },
  {
    "ParameterKey": "AllowNonRestoredState",
    "ParameterValue": "true"
  }
]
```

Replace `YOUR_BUCKET_ARN` and `YOUR_JAR` with your specific requirements\. You can follow this [guide](https://docs.aws.amazon.com/kinesisanalytics/latest/java/get-started-exercise.html) to create an Amazon S3 bucket and an application jar\.

Now create the stack \(replace YOUR\_REGION with a region of your choice, e\.g\. us\-east\-1\):

```
aws cloudformation create-stack --region YOUR_REGION --template-body "file://stack.yaml" --parameters "file://parameters.json" --stack-name "TestKDAStack" --capabilities CAPABILITY_NAMED_IAM
```

You can now navigate to [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation) and view the progress\. Once created you should see your Flink application in `Starting` state\. It may take a few minutes until it will start `Running`\. 

For more information, see the following:
+ [Four ways to retrieve any AWS service property using AWS CloudFormation \(Part 1 of 3\)](https://aws.amazon.com/blogs/mt/four-ways-to-retrieve-any-aws-service-property-using-aws-cloudformation-part-1/)\.
+ [Walkthrough: Looking up Amazon Machine Image IDs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-custom-resources-lambda-lookup-amiids.html)\.