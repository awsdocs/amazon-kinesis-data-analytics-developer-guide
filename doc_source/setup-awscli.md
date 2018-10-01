# Step 2: Set Up the AWS Command Line Interface \(AWS CLI\)<a name="setup-awscli"></a>

Follow the steps to download and configure the AWS Command Line Interface \(AWS CLI\)\.

**Important**  
You don't need the AWS CLI to perform the steps in the Getting Started exercise\. However, some of the exercises in this guide use the AWS CLI\. You can skip this step and go to [ Step 3: Create Your Starter Amazon Kinesis Data Analytics Application](get-started-exercise.md), and then set up the AWS CLI later when you need it\.

**To set up the AWS CLI**

1. Download and configure the AWS CLI\. For instructions, see the following topics in the *AWS Command Line Interface User Guide*: 
   + [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)
   + [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)

1. Add a named profile for the administrator user in the AWS CLI config file\. You use this profile when executing the AWS CLI commands\. For more information about named profiles, see [Named Profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-multiple-profiles) in the *AWS Command Line Interface User Guide*\.

   ```
   [profile adminuser]
   aws_access_key_id = adminuser access key ID
   aws_secret_access_key = adminuser secret access key
   region = aws-region
   ```

   For a list of available AWS Regions, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *Amazon Web Services General Reference*\.

1. Verify the setup by entering the following help command at the command prompt: 

   ```
   aws help
   ```

## Next Step<a name="setting-up-next-step-3"></a>

[ Step 3: Create Your Starter Amazon Kinesis Data Analytics Application](get-started-exercise.md)