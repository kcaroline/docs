---
title: AWS Metrics Integration
keywords:
tags: [integrations]
sidebar: doc_sidebar
permalink: integrations_aws_metrics.html
summary: Learn how to send AWS data to Wavefront.
---
Amazon Web Services (AWS), is a collection of cloud-computing services that provide an on-demand computing platform. Wavefront supports cloud integrations, which allow you to ingest metrics directly from AWS and send them to Wavefront without needing to set up a Wavefront proxy.

{% include shared/badge.html content="You must have [Proxy Management permission](permissions_overview.html) to set up an AWS cloud integration. If you do not have permission, UI menu selections and buttons required to perform the tasks are not visible." %}

Wavefront offers [Amazon Web Services](http://aws.amazon.com) (AWS) [CloudWatch](http://aws.amazon.com/cloudwatch), [CloudTrail](http://aws.amazon.com/cloudtrail), and AWS Metrics+ integrations.

- The CloudWatch integration retrieves AWS [metric and
dimension](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CW_Support_For_AWS.html) data.
- The CloudTrail integration retrieves EC2 event information and creates Wavefront **System** events that represent the AWS events.
- The AWS Metrics+ integration retrieves additional metrics using AWS APIs other than CloudWatch.

For information on monitoring AWS metric ingestion, see [AWS Integration](wavefront_monitoring.html#aws-integration).

## Granting Wavefront Read-only Access to your Amazon Account

Adding a cloud integration requires establishing a trust relationship between Amazon and Wavefront by sharing account IDs and an external ID. The external ID can be generated by Wavefront or by your company. If you use an ID generated by Wavefront you run **Set up Amazon Account** to configure all integrations&mdash;CloudWatch, CloudTrail, and AWS Metrics+&mdash;at once. If you want to use an ID generated by your company, you set up each integration individually. 

In summary, the integration options are:

- **Set up Amazon Account** - Integrate CloudWatch, Cloudtrail, and AWS Metrics+ data. Choose this option if:
    - You are an administrator for your company's AWS account.
    - You want to integrate CloudWatch, CloudTrail, and AWS Metrics+ data into Wavefront at the same time.
    - You can use an external ID generated by Wavefront.
    - You want Wavefront to automatically deploy [AWS dashboards](#aws-dashboards).
- **Register [CloudWatch \| CloudTrail \| AWS Metrics+]** - Integrate each type of data individually. Choose this option if:
    - You are not an administrator for your company's AWS account, but will be gathering required information from an administrator.
    - You want to integrate CloudWatch, CloudTrail, and AWS Metrics+ data into Wavefront, but not at the same time.
    - You want to use a custom external ID generated by your company.
    - You can manually deploy AWS dashboards.

First grant Wavefront read-only access to your Amazon account. Run the Amazon Create New Role wizard where you provide Wavefront account and external IDs. In the Create New Role wizard Review, copy the Role ARN containing the Amazon account ID.

![Role ARN](images/role_arn.png)

## Adding a Cloud Integration

1.  Select **Browse &gt;** **Cloud Integrations**.
1.  Select **Add Integration &gt; &lt;Integration Option&gt;**, where  **&lt;Integration Option&gt;** is **Set up Amazon Account** or **Register [CloudWatch \| CloudTrail \| AWS Metrics+]**.
1.  Configure the integration:
    - **Common**
        - **Name** - Name to identify the integration.
        - **Role ARN** - Role ARN from Amazon account. The format of this property is\

          ```
          arn:aws:iam::<Amazon account ID>:role/wavefront
          ```
    - **Set Up Amazon Account and Register Cloudtrail**
        - **Bucket Name -** The S3 bucket containing CloudTrail logs. In AWS, go to **CloudTrail** &gt;**Trails** to see the bucket name.
        - **Prefix** - A log file prefix specified when you created the CloudTrail.
    - **Register [CloudWatch \| CloudTrail \| AWS Metrics+]**
        - **External ID -** External ID generated by your company.
    - **Register CloudWatch**
        - Whitelists and Service Refresh Rate - see <a href="#configure">Configuring CloudWatch Metric Ingestion</a>.
1.  Click **Save**. The selected integration(s) are created and added to the Cloud Integrations list.
1.  Optional. If you selected **Set up Amazon Account** and want to configure the CloudWatch integration, click the link in the Name column in the CloudWatch row.
    1.  Configure CloudWatch properties.
    1.  Click **Save**.

<!--
## Automatic Source Tagging

All sources reporting AWS metrics have the source tag `wavefront.aws.<service>`. For example: `wavefront.aws.ec2`, `wavefront.aws.ebs`, etc.)
-->

## CloudWatch Integration

The Wavefront CloudWatch integration retrieves AWS metric and dimension data from AWS services using the AWS CloudWatch API. The complete list of metrics and dimensions that can be retrieved from AWS CloudWatch is available at [Amazon CloudWatch Metrics and Dimensions Reference](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CW_Support_For_AWS.html). In addition, you can publish [custom AWS metrics](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html) that can also be ingested by the CloudWatch integration.

<a name="configure"></a>

### Configuring CloudWatch Metric Ingestion

You can configure which instances and volumes to ingest metrics from, which metrics to ingest, and the rate at which Wavefront fetches metrics.

- **Instance and Volume Whitelist** fields - Whitelist instances and volumes by specifying [EC2 tags](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html) (as **&lt;key&gt;=&lt;value&gt;** pairs) defined on the instances and volumes. For example, **organization=&lt;yourcompany&gt;**. When specified as a comma-separated list, the tags are OR'd. To use instance and volume whitelisting, you must also add an [AWS Metrics+](#aws-metrics-plus-integration) integration because the AWS tags are imported from the EC2 service. If you don't specify any tags, Wavefront imports metrics from *all* instances and volumes.
- **Metric Whitelist** field - Whitelist metrics by specifying a regular expression. The regular expression must be a complete match of the entire metric name. For example, if you only want CloudWatch data for `elb` and `rds` (which come under `aws.rds`), then use a regular expression such as: `^aws.(elb|rds).*$`. If you do not specify a regular expression, _all_ CloudWatch metrics are retrieved.
- **Service Refresh Rate** - Number of minutes between requesting metrics. Default: 5.

<a name="aws_sources"></a>

### Wavefront Source Field

Wavefront sets the value of the CloudWatch metric [`source`](wavefront_data_format.html) field by service:

- **EC2** - the value of the **hostname**, **host**, or **name** [EC2 tags](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html), if the tags exist and you have an EC2 integration. Otherwise, the source is set to the Amazon instance ID.
- **EBS** - the Amazon instance ID of the EC2 instance the volume is attached to.
- All other services - the value of the *first* CloudWatch dimension. The supported dimensions appear at the bottom of the Amazon service metric documentation topic. For example, see [Amazon EC2 Dimensions](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ec2-metricscollected.html#ec2-metric-dimensions).

### Wavefront Point Tags

Wavefront adds the following point tags to CloudWatch metrics:

- `accountId` - the Amazon account that reported the metric.
- `Region` - The region in which the service is running. Added to EC2 and EBS metrics only.
- CloudWatch dimensions. The dimensions vary by service. For example, for AWS S3, the `BucketName` dimension is added as a point tag.

### CloudWatch Pricing

Standard AWS CloudWatch pricing applies each time Wavefront requests metrics using the CloudWatch API. For pricing information, see [AWS \| Amazon CloudWatch \| Pricing](http://aws.amazon.com/cloudwatch/pricing). After selecting a region, you can find the current expected price under **Amazon CloudWatch API Requests**. In addition, custom metrics have a premium price; see the **Amazon CloudWatch Custom Metrics** section of the pricing page. To limit cost, by default Wavefront queries the API every 5 minutes. However, you can [change the request rate](#configuring-cloudwatch-metric-ingestion), which will change the cost.

As an alternative to using the CloudWatch API for EC2 metrics, you can collect these metrics using [a Telegraf collector](integrations_telegraf.html) on each AWS instance. In this case, to prevent CloudWatch from requesting those metrics, you should set the Metric Whitelist property to allow all metrics except EC2. For example:

```
^aws.(billing|instance|sqs|sns|reservedInstance|ebs|route53.health|ec2.status|elb|s3).*$
```

By default on a new trial, to cap the AWS CloudWatch bill, Wavefront limits the number of unique metrics that can be retrieved from CloudWatch to 10K. If you go to the **Browse > Cloud Integrations** page and see 10000 in the CloudWatch metrics column, you have hit that limit.

### Configuring Billing Metrics

The AWS Billing and Cost Management service sends [billing metrics](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/billing-metricscollected.html) to CloudWatch. You configure AWS to produce `aws.billing.*` metrics by checking the **Receive Billing Alerts** checkbox on the **Preferences** tab in the [AWS Billing and Cost Management console](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/monitor-charges.html):

![aws billing](images/aws_billing.png)

Wavefront reports the single metric `aws.billing.estimatedcharges`. The `source` field and `ServiceName` point tag identify the AWS services. For the total estimated charge metric, `source` is set to `usd` and `ServiceName` is empty. Wavefront also provides the point tags `accountId`, `Currency`, `LinkedAccount`, and `Region`. Billing metrics are typically reported every 4 hours.


## CloudTrail Integration

The Wavefront CloudTrail integration retrieves EC2 event information stored in JSON-formatted log files in an S3 bucket. The integration parses the files for all events that result from an operation that is not a describe, get, or list, and creates a Wavefront [System event](events.html). The operations include: **\[Run\|Start\|Stop\|Terminate\|Monitor\|Unmonitor\]Instances**, **\[Attach\|Detach\]Volume**, **DeleteNetworkInterface**, **AuthorizeSecurityGroupIngress**, **CreateSecurityGroup**, **RequestSpotInstances**, **CancelSpotInstanceRequests**, **ModifyInstanceAttribute**, **CreateTags**, **\[Create\|Delete\]KeyPair**, and **DeregisterImage**.

In the Events browser the events are named **AWS Action: \<Operation\>** and have the [event tag](tags_overview.html) `aws.cloudtrail.ec2`. For example:

![aws start instance](images/aws_start_instances.png)

## AWS Metrics+ Integration

The AWS Metrics+ integration retrieves additional metrics using AWS metrics API calls other than CloudWatch. 
Unless otherwise indicated, Wavefront sets the value of the AWS Metrics+ `source` field to the AWS instance ID. If an EBS volume is detached, its source field is set to the volume ID. The metrics include:

- `aws.instance.price` - EC2 instances and how much they cost per hour. This metric includes the point tags `availabilityZone`, `instanceID`, `instanceLifecycle`, `instanceType`, and `operatingSystem`.
- `aws.reservedinstance.count` - The number of reserved instances in each availability zone by each instance type. This metric includes the point tags `availabilityZone`, `instanceID`, `instanceType`, and `operatingSystem`. This metric appears only if your account has reserved instances.
- EBS metrics - EBS metrics include the point tags `instanceID`, `Region`, `State`, `Status`, `volumeId`, and `volumeType` (see [Amazon EBS Volume Types](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)). The `Status` can be `attached`, `detaching`, or `attaching`. The `State` can be `available` (detached) or `in-use` (attached).
  - `aws.ebs.volumesize` - The volume size of the elastic block store. 
  - `aws.ebs.volumeiops` - The volume I/O operations of the elastic block store.
- SQS - [AWS SQS](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/sqs-metricscollected.html) metrics retrieved every minute from the SQS service.
  - `aws.sqs.approximatenumberofmessagesnotvisible` - The number of messages that are "in flight." Messages are considered in flight if they have been sent to a client but have not yet been deleted or have not yet reached the end of their visibility window.
  - `aws.sqs.approximatenumberofmessagesdelayed` - The number of messages in the queue that are delayed and not available for reading immediately. This can happen when the queue is configured as a delay queue or when a message has been sent with a delay parameter.
  - `aws.sqs.approximatenumberofmessages` aliased to the CloudWatch metric `aws.sqs.approximatenumberofmessagesvisible` - The number of messages available for retrieval from the queue.
- Pricing Metrics - capture the current pricing of EC2 instances. These metrics are available as a preview and subject to change. These metrics have the point tags  `instanceType`, `operatingSystem`, `Region`, `purchaseOption` (All Upfront, Partial Upfront, No Upfront), `leaseContractLength` (1 or 3 years), and `offeringClass` (standard or convertible)). The `source` field is set to the display name of the region. For example, if `Region=us-west2`, then `source=us west (oregon)`. 
  - `~sample.aws.ec2.on-demand.price.hourly` - the hourly price (in US$) of an on-demand instance.
  - `~sample.aws.ec2.reserved.price.upfront` - the up-front payment (in US$) for a reservation.  This metric reports `0` when `purchaseOption` is No Upfront.
  - `~sample.aws.ec2.reserved.price.hourly` - the hourly payment (in US$) for a reservation. This metric reports `0` when the `purchaseOption` is All Upfront.

## Viewing AWS Metrics

You can view AWS metrics by selecting **Browse &gt; Metrics** and searching for metrics beginning with `aws.`:

![aws metrics](images/aws_metrics.png)

You can drill into the folder for a specific service and click a metric to navigate to a chart that displays that set of data. For example, clicking clicking the folder `aws.ec2.`, then the metric `aws.ec2.cpuutilization`, and then refining the query by the `Region` point tag and the `topk` function yields the following chart:

![aws cpu utilization](images/aws_cpu_utilization.png)

### AWS Aggregate Metrics

All AWS metrics return the following aggregate metrics: average, maximum, minimum, sample count, and sum. To view the aggregate metrics,

1.  Search for a specific metric, for example `aws.ec2.cpuutilization`:

    ![aws cpu utilization folder](images/aws_cpu_utilization_metric.png)

2.  Click the metric folder, for example `aws.ec2.cpuutilization.`, to display the aggregate metrics:

    ![aws cpu utilization aggregate metrics](images/aws_cpu_utilization_aggregate_metrics.png)

## AWS Dashboards

Wavefront provides AWS overview dashboards Summary, Pricing, and Billing and the AWS service-specific dashboards: EC2, ECS, ELB, DynamoDB, Lambda, and Redshift. All AWS dashboards have a tag `wavefront.aws.<service>`. For example: `wavefront.aws.ec2`, `wavefront.aws.lambda`, etc. When you use Set up Amazon Account to create your AWS cloud integration, Wavefront automatically deploys the dashboards as _system_ dashboards.

{% include shared/system_dashboard.html %}

If you register a CloudWatch cloud integration AWS dashboards are not deployed automatically. To get the dashboards, download the dashboards from the [AWS dashboard repository](https://github.com/wavefrontHQ/integrations/tree/master/aws/dashboards) and deploy them following the process in [Deploying a Dashboard](dashboards_managing.html#deploying-a-dashboard). Dashboards that you deploy manually are _editable_. Alternatively you can contact [{{site.support_email}}](mailto:{{site.support_email}}) to deploy the dashboards as system dashboards.


