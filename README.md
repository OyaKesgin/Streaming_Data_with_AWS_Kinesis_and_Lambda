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
# Serverless APIs
Above, we set up a streaming data pipeline takes data from vehicles and generates daily reports. It s possible to give an access to data S3, however, data engineer think how to give access to data and subset programmatically. APis allow an external developer to write code that interact with your data in a programmatic control way. Controller decided to integrate speeding data into HR employees performance system.
we are going to make quick API using Lambda. Lets create new lambda function using Pyhton 3.8 called speederReporterApi-->> AWS Data Wrangler Layer--> create a trigger using API getway choose HTTP API with open security called getSpeedersByDate  and click Add. in the main function view you will see new API URL.
if you request data for today, lets recalculate the data and create a speeder file even it s not midnight yet.To do this,We need to trigger speeder aggregater from speederReportAPI. 

# Chapter3.Transformational Lambda
In chapter 1, we use firehose to collect data from multiple sources in store. in chapter 2, We use lambda act on that data. Now it s time to work on data as a moving firehose string. In chapter 1 or 2, We built a lambda to reach data from S3 after being written by firehose. We will able to accomplish a lot that way but this approach is not always the best if we process once the data hit the S3. We uses Transformational Lambda fired on object in S3. Lambda transform manupulate data with mid-firehose stream. That means S3 takes a little bit longer to start processing data than lambda transforming. With S3 we have to store raw data before processing whereas lambda transform  we can transform data before being stored. Finally, using lambda transform allow us to multiple destinations for your firehose stream not just S3. Both equally important and depends on the situation.

Transforming Data inside a stream-- AWS console--> Lambda--> create new function-->FunctionName=timeStampTransformer, click on new role--> test event that contains two records--> we can use Kinesis Firehose then we can add a code for lambda function. Also we need to update timeout over 1min. now test the function.
Next Lets make a few tweets to our stream. First we have to give firehose role permission to execute our lambda. We do this by granting AWS Lambda full access policy. Next under transform source records we select the our lambda function and we are good to go.Now we can write firehose stream.
 Chapter3.Transformational Lambda

# Chapter4. Analyzing Data in the Stream 

In the last lesson, We looked at how to transform data in the firehose stream using AWS Lambda. In this chapter, We learn how to analyse data as it moves to stream using a Kinesis data analytics application. Kinesis data analytics application excepts a most one source firehose or data stream. We can optionally reach data using statistic reference file from S3. It execute SQL operation on streaming data in reaching it aggregating it for analysing it. Finally, the output set one, two or three destinations such as firehose stream or lambda. Once there is another way to manipulate data in stream why? Firehose has min buffer size . if we already used tranformation of lambda, we can only look at fixed size and time window. Kinesis Data Analytics gives us more flexibility also prevent us having heavier analysis focus lamda function within a stream.  

Kinesis Data Analytics versus Transformation Lambdas. 
In a lambda function, we use panda and pyhton , In the Kinesis Data Analytics we use SQL. Both  let us filter data and perform aggretion the data over a certain window. However, with data kinesis analytics we can specfy the window. we can join reference data to stream and look at the matrix of stream in different time frames as supposed to be interval for enforced by firehose. With Data Kinesis Analytics we can combine multiple streams.  Finally with data kinesis analytics, We can send the output to other destinations. Source streams based on how many streams you have represents source of streaming data in kinesis. Destination SQL stream represents the stream that is the result of SQL processing. Finally, the stream pump is a continious insert query running that inserts data from one in application to another application stream in. 

Creating a Kinesis data analytics application;
AWS services>> Kinesis>> Create Data analystic application>> connect streaming data >> Kinenis Delivery Stream >>> Discover Schema>>> SQL Stream>>....

Using multiple streams

In this part, we will slightly extend that arthitecture to learn about the ways that we can combine multiple streams. You have been asked to take over Vin and figure out device IDs that has been installed and write the results in separete location for the bucket. First, We will add some reference data to kinesis data analytics application to match the vehicle identification number to sensor id has been installed. Reference data is just a table matching Vin and Sensor_id..

Delivering data from Kinesis Analytics

AWS Management Console>> Kinesis Console >> Data Analytics Application>>>Connect Reference Data>>> Chose S3 Vehicle Data ( from Amazon S3 Bucket)>>> provide the path to AMazon S3 Object ( write down reference) >>>press Discovery Schema>>> Edit Schema to upcase the column names>> EXIT >> GO TO SQL console.>>> Update the SQL query save and run. Then we are gonna add another Firehose Delivery stream as a destination  for analytics application. This stream will deliver over PIN devices. 

Kinesis >> Firehose >>Create Delivery Stream>> Stream Name>> For Destination select S3 >>> pick SD Vehicle Bucket>> for prefix enter  the name of Buggy>>> change S3 buffer size and interval >> lets use firehose   delivery role since the stream will be writing on the same as S3 bucket.
Lets head back to Amazon Kinesis Analystics
>>> Select connect destination.>>> Select Firehose delivery stream>>> Select buggy stream from the list>> for the in application stream section >> Select the SQL application stream we created in Kinesis Data Analystics.

# Chapter5. Monitoring and visualizing streaming data

5.1. Streaming data case study

Visualising last 15 min. data stored in S3 will be difficult. Since S3 is designed for static storage. Luckily, Firehose has two other options; Redshift and Elasticsearch. Lets take a closer look at storage piece. Storing data in S3 for real time analysis isnt really efficient. Firehose can send data to redshift and elastic search. Redshift is designed for storing large clean tables of data. Schema is defined in databases. Elastic search is for schemaless, it is good for unstructured data for logs and text. Recreate the schema during the query. Redshift uses SQL for queries, while elastic research use its own language for queries. Redshift works great with BI tools like Tableau. Elastic search has its own - KIBANA.

Creating an Elastic Search Cluster

AWS Services---> Elastic Search Dashboard-->Create a new Domain-->Select Development and Testing--> Write the name TW-date domain-->Next --> Select Public Access-->Access Policy --> next and confirm.5.2. Monitoring Performance

We created Elastic Search instance. Now, we can create lambda transform function that will run each tweet against amazon comprehend EPI. Regular Transform Function , we initilize boto3 comprehen client in the handler , create a list to store result then loop over incoming records. We load the data and decode in b64 then call the detect sentiment method. then we put altogether in a record that matches the model firehose expect to receive from lambda. Now, wire everything is up. Lets update fireHoseDeliveryRole to have full access to elastic search. When we create new firehose delivery stream, we repeat all the steps from chapter 1 and except we will select elastic search as the destination. We select the domain as the elastic search domain created. Lastly we specify the index which will act like a table where all tweets get written into. We also specify S3 bucket where our tweets get backed up. Lets make sure everything is running smoothly to meet our requirements loosing as little data as possible. To make this happen, We are gonna use AWS cloudwatch service. Cloudwatch monitors numerous metrics for every AWS service. There four crucial components, they are all built on top each other. Logs are the row data sent to cloudwatch at AWS services, those are analysed to present metrics which one measures various activities of service. Metrics can be used to trigger alarms no notifications. Lastly metrics can be visualised in dashboards.
Elastic Search and CloudWacth is very similar for providing monitoring, alerts, visualisation and dashboards. yet, they are different. Cloudwatch AWS centric, it can accept custom data but it s not best tool for general visualisation. Cloud watch is also great for working with logs. Elastic search open source tool and can except variaty of data from different source. visualisation are more reboust than cloudwatch.

-Working with ElasticSearch using Kibana

ElasticSearchDashBoard-->Select Cluster--> Click Kibana Link-->it will ask us to identify first index pattern-->This needs to match what we told firehose for elasticsearch to know that we are dealing with the table of data. after we define the pattern it will show us all the fields coming into firehouse. We can see the raw data coming under source field--> Select the fields and save the view. A view is  a subset of index pattern we will use it later as we define the visualisations and dashboard.Next lets head to visualations and create new one. Under buckets Select split series and terms from aggregation --- sentiment keywords from field--> save it as a tweet sentiment. 

Let head to dashboard--> create new dashboard--> add all graphics--> save the dashboard. finally lets create alert--> Click Destination
Monitor-->watch the data and needs alert to be triggered.
