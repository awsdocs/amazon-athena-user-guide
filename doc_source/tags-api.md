# Working with Tags Using the API Actions<a name="tags-api"></a>

You can also use the `CreateWorkGroup` API operation with the optional tag parameter that you can use to pass in one or more tags for the workgroup\. To add, remove, or list tags, you can use the following AWS API operations: `TagResource`, `UntagResource`, and `ListTagsForResource`\. 


**Tags API Actions in Athena**  

| API name | Action description | 
| --- | --- | 
| TagResource | Add or overwrite one or more tags to the workgroup with the specified ARN\. | 
| UntagResource | Delete one or more tags from the workgroup with the specified ARN\. | 
| ListTagsForResource | List one or more tags for the workgroup resource with the specified ARN\. | 

For more information, see the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.

**Example TagResource**  <a name="example1"></a>
In the following example, we add two tags to `workgroupA`:  

```
List<Tag> tags = new ArrayList<>();
tags.add(new Tag().withKey("tagKey1").withValue("tagValue1"));
tags.add(new Tag().withKey("tagKey2").withValue("tagValue2"));

TagResourceRequest request = new TagResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA")
    .withTags(tags);

client.tagResource(request);
```
Do not add duplicate tag keys at the same time to the same workgroup\. If you do, Athena issues an error message\. If you tag a workgroup using an existing tag key in a separate `TagResource` action, the new tag value overwrites the old value\.

**Example UntagResource**  <a name="example2"></a>
In the following example, we remove `tagKey2` from `workgroupA`:  

```
List<String> tagKeys = new ArrayList<>();
tagKeys.add("tagKey2");

UntagResourceRequest request = new UntagResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA")
    .withTagKeys(tagKeys);

client.untagResource(request);
```

**Example ListTagsForResource**  <a name="example3"></a>
In the following example, we list tags for `workgroupA`:  

```
ListTagsForResourceRequest request = new ListTagsForResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA");

ListTagsForResourceResult result = client.listTagsForResource(request);

List<Tag> resultTags = result.getTags();
```