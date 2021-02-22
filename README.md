# Streaming-Data-with-AWS-Kinesis-and-Lambda
There are two ways of working data; Batch and Stream. Neither is better or worse. They are just different tools doing their jobs. Data engineer understands the business need and find the right solution. The key difference between batch and stream is when you need to use the data.
# Batch
is excellent for larger datasets and more complex analysis but data can be older so the process of moving data is slow. Making daily sales report, Forecasting next month sales or churn prediction.
# Streaming 
is better for simply analysis within short time window. Aggregeting and Filtering functions. Data moves fast. cases like fraud detection, Monitoring and Real time alerting.
# Telemating Streaming- each vehicle has sensors
We use Amazon Kinesis for ingestioning data from unboard vehicle devices next We will combine it AWS Lambda functions to process that data. you will also use Amazon S3, IAM and AMAzon SNS from previous quest. 
Kinesis has data firehouse, data streams and data analytics. you combine these to solve streaming data use cases.
# Firehouse Delivery Stream
Producers - like city vehicles generate the data. They ride it to Firehose Delivery Stream using .put_record(). 
Next firehose stream delivers data destination for storage like S3, Redshift or ElasticResearch.
# Creating Bucket
Vehicle sensors write vehicle speed information to Firehose Delivery Stream which will deliver the data to S3 bucket. Lets make a Sd vehicle data bucket. DC user doesnt have permission to create firehose delivery streams. he doesnt permission to put data into the firehose as well. Lets give DS user an additional permission.Loaded AWS console then Identity Access Management(IAM)> users > Dc User - existing policy directly - click add permission and attached policy.Serach KinesisFirehose Fullaccess click view and then add done.  This way DS user has a power  to create, manage and write on firehose to stream. Firehose stream can not deliver data to vehicle data bucket. After receiving data records from vehicles, stream is acting like Sd user provide records to DS users. 
# Creating Role 
Role is a hat that amazon service puts on, roles have their own permissions when AW service puts on a hat requires those permissions. This hat is only be worn by trusted entity which is specified in the role definition.
Roles and users live in IAM. they can both permissions policies
Roles can not act on their own, do not have keys and logins. Users have.
Our end goal is built a system and collect data from vehicles and streams to Sd vehicle bucket. To create a stream, right data, Dc user need additional permission.
IAM >> ROLES>>Create Role>>Select AWS Service>>Entity Type. Click Kinesis>>KinesisFirehose>>Policy>> Name-FirehoseDeliveryRole
# Working with Firehose Delivery Stream
ARN role code is very import to create stream. We need to specify ARN for specif role. ARN is Amazon unique identifier.
Goto IAM>>Roles>>FirehoseDeliveryRole>>ARN is on top.
When we created, each records from vehicles comes in different time and different vehicles. you do the same thing on click data from multiple browsers or livelogs from Datawarehouses. Data enginering is about understanding the pattern and finding right time to apply. To send a record, Firehose clients put_record method passing Delivery Stream Name as argument, we sent a dictionary to record argument containing data key. these contains the record. what we sent to stream and data key, needs to be string. we convert our record to string. each field is seperated by the space making it easier and reads in the panda later. We use pyhton string join method to bring the values of dictionaries  together into string enforcing string data type across each value. We have a record and converted to the string then push into the stream. We also attach line break in the end so each record is a row.
# Going Serverless
If censor data at variaus intervals and firehose descending data at various intervals to S3 to read and analyse these records. We have to call anlayse_data.py manually. its almost right back to batch so how we run code and response to each new record being inserted. Using Lambda you can execute code in the cloud trigged by the event. Lambda is an example for Serverless insfustructure. Servers managed by cloud provider. you pay for code execution instead of machine run tag. AWS scales up to run your functions. Serverless has  memory in execution time limits it s best for quick functions. 
Lambda Function
1. Trigger-Lambda function execute and response to trigger. These can be API request,S3 event,time trigger and etc. 
2. Handler- Function code defines the logic is the handler. the environment where code executes barebones. 
3. Layers add additional libraries like pandas and numpy.
4. Destination-Finaly, There is an optional destination for use cases like writing another firehose string.With lambda you can transform the data, build APIs and alerts, trigger Alexa actions
The handler takes event argument from trigger. For instance S3 will send different events than API.

AWS Management console--> goto services--> click Lambda--> select Function on the left side bar--> create function--> call the function Record3S3-->runtime, type python 3.8 --> choose or create execution role---> create the function.
you see the main configuration page. Below the designer we have a code editor.







