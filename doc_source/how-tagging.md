# Using Tagging<a name="how-tagging"></a>

This section describes how to add key\-value metadata tags to Kinesis Data Analytics applications\. These tags can be used for the following purposes:
+ Determining billing for individual Kinesis Data Analytics applications\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management Guide*\.
+ Controlling access to application resources based on tags\. For more information, see [Controlling Access Using Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *AWS Identity and Access Management User Guide*\.
+ User\-defined purposes\. You can define application functionality based on the presence of user tags\.

Note the following information about tagging:
+ The maximum number of application tags includes system tags\. The maximum number of user\-defined application tags is 50\.
+ If an action includes a tag list that has duplicate `Key` values, the service throws an `InvalidArgumentException`\.

**Topics**
+ [Adding Tags when an Application is Created](#how-tagging-create)
+ [Adding or Updating Tags for an Existing Application](#how-tagging-add)
+ [Listing Tags for an Application](#how-tagging-list)
+ [Removing Tags from an Application](#how-tagging-remove)

## Adding Tags when an Application is Created<a name="how-tagging-create"></a>

You add tags when creating an application using the `tags` parameter of the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_CreateApplication.html) action\.

The following example request shows the `Tags` node for a `CreateApplication` request:

```
"Tags": [ 
    { 
        "Key": "Key1",
        "Value": "Value1"
    },
    { 
        "Key": "Key2",
        "Value": "Value2"
    }
]
```

## Adding or Updating Tags for an Existing Application<a name="how-tagging-add"></a>

You add tags to an application using the [TagResource](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_TagResource.html) action\. You cannot add tags to an application using the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_UpdateApplication.html) action\.

To update an existing tag, add a tag with the same key of the existing tag\.

The following example request for the `TagResource` action adds new tags or updates existing tags:

```
{
   "ResourceARN": "string",
   "Tags": [ 
      { 
         "Key": "NewTagKey",
         "Value": "NewTagValue"
      },
      { 
         "Key": "ExistingKeyOfTagToUpdate",
         "Value": "NewValueForExistingTag"
      }
   ]
}
```

## Listing Tags for an Application<a name="how-tagging-list"></a>

To list existing tags, you use the [ListTagsForResource](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_ListTagsForResource.html) action\.

The following example request for the `ListTagsForResource` action lists tags for an application:

```
{
   "ResourceARN": "arn:aws:kinesisanalytics:us-west-2:012345678901:application/MyApplication"
}
```

## Removing Tags from an Application<a name="how-tagging-remove"></a>

To remove tags from an application, you use the [UntagResource](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_UntagResource.html) action\.

The following example request for the `UntagResource` action removess tags from an application:

```
{
   "ResourceARN": "arn:aws:kinesisanalytics:us-west-2:012345678901:application/MyApplication",
   "TagKeys": [ "KeyOfFirstTagToRemove", "KeyOfSecondTagToRemove" ]
}
```