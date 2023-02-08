# Step 4: Verify Output<a name="app-anomaly-verify-output"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

After configuring the application output in [Step 3: Configure Application Output](app-anomaly-create-ka-app-config-destination.md), use the following AWS CLI commands to read records in the destination stream that is written by the application:

1. Run the `get-shard-iterator` command to get a pointer to data on the output stream\.

   ```
   aws kinesis get-shard-iterator \
   --shard-id shardId-000000000000 \
   --shard-iterator-type TRIM_HORIZON \
   --stream-name OutputStreamTestingAnomalyScores \
   --region us-east-1 \
   --profile adminuser
   ```

   You get a response with a shard iterator value, as shown in the following example response:

   ```
     {      
         "ShardIterator":
         "shard-iterator-value"   }
   ```

   Copy the shard iterator value\. 

1. Run the AWS CLI `get-records` command\.

   ```
   aws kinesis get-records \
   --shard-iterator shared-iterator-value \
   --region us-east-1 \
   --profile adminuser
   ```

   The command returns a page of records and another shard iterator that you can use in the subsequent `get-records` command to fetch the next set of records\.