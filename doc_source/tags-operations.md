# Using Tag Operations<a name="tags-operations"></a>

Use the following tag operations to add, remove, or list tags on a resource\.


****  

| API | CLI | Action description | 
| --- | --- | --- | 
| TagResource | tag\-resource | Add or overwrite one or more tags on the resource that has the specified ARN\. | 
| UntagResource | untag\-resource | Delete one or more tags from the resource that has the specified ARN\. | 
| ListTagsForResource | list‑tags‑for‑resource | List one or more tags for the resource that has the specified ARN\. | 

**Adding Tags When Creating a Resource**  
To add tags when you create a workgroup or data catalog, use the `tags` parameter with the `CreateWorkGroup` or `CreateDataCatalog` API operations or with the AWS CLI `create-work-group` or `create-data-catalog` commands\.

## Managing Tags Using API Operations<a name="tags-operations-examples-java"></a>

The examples in this section show how to use tag API operations to manage tags on workgroups and data catalogs\. The examples are in the Java programming language\.

**Example TagResource**  <a name="example1"></a>
The following example adds two tags to the workgroup `workgroupA`:  

```
List<Tag> tags = new ArrayList<>();
tags.add(new Tag().withKey("tagKey1").withValue("tagValue1"));
tags.add(new Tag().withKey("tagKey2").withValue("tagValue2"));

TagResourceRequest request = new TagResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA")
    .withTags(tags);

client.tagResource(request);
```
The following example adds two tags to the data catalog `datacatalogA`:  

```
List<Tag> tags = new ArrayList<>();
tags.add(new Tag().withKey("tagKey1").withValue("tagValue1"));
tags.add(new Tag().withKey("tagKey2").withValue("tagValue2"));

TagResourceRequest request = new TagResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:datacatalog/datacatalogA")
    .withTags(tags);

client.tagResource(request);
```
Do not add duplicate tag keys to the same resource\. If you do, Athena issues an error message\. If you tag a resource using an existing tag key in a separate `TagResource` action, the new tag value overwrites the old value\.

**Example UntagResource**  <a name="example2"></a>
The following example removes `tagKey2` from the workgroup `workgroupA`:  

```
List<String> tagKeys = new ArrayList<>();
tagKeys.add("tagKey2");

UntagResourceRequest request = new UntagResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA")
    .withTagKeys(tagKeys);

client.untagResource(request);
```
The following example removes `tagKey2` from the data catalog `datacatalogA`:  

```
List<String> tagKeys = new ArrayList<>();
tagKeys.add("tagKey2");

UntagResourceRequest request = new UntagResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:datacatalog/datacatalogA")
    .withTagKeys(tagKeys);

client.untagResource(request);
```

**Example ListTagsForResource**  <a name="example3"></a>
The following example lists tags for the workgroup `workgroupA`:  

```
ListTagsForResourceRequest request = new ListTagsForResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA");

ListTagsForResourceResult result = client.listTagsForResource(request);

List<Tag> resultTags = result.getTags();
```
The following example lists tags for the data catalog `datacatalogA`:  

```
ListTagsForResourceRequest request = new ListTagsForResourceRequest()
    .withResourceARN("arn:aws:athena:us-east-1:123456789012:datacatalog/datacatalogA");

ListTagsForResourceResult result = client.listTagsForResource(request);

List<Tag> resultTags = result.getTags();
```

## Managing Tags Using the AWS CLI<a name="tags-operations-examples-cli"></a>

The following sections show how to use the AWS CLI to create and manage tags on data catalogs\.

### Adding tags to a resource: tag\-resource<a name="tags-operations-examples-cli-tag-resource"></a>

The `tag-resource` command adds one or more tags to a specified resource\.

**Syntax**  
`aws athena tag-resource --resource-arn arn:aws:athena:region:account_id:datacatalog/catalog_name --tags Key=string,Value=string Key=string,Value=string`

The `--resource-arn` parameter specifies the resource to which the tags are added\. The `--tags` parameter specifies a list of space\-separated key\-value pairs to add as tags to the resource\. 

**Example**  
The following example adds tags to the `mydatacatalog` data catalog\.  

```
aws athena tag-resource --resource-arn arn:aws:athena:us-east-1:111122223333:datacatalog/mydatacatalog --tags Key=Color,Value=Orange Key=Time,Value=Now
```
To show the result, use the `list-tags-for-resource` command\.   
For information on adding tags when using the `create-data-catalog` command, see [Registering a Catalog: create\-data\-catalog](datastores-hive-cli.md#datastores-hive-cli-registering-a-catalog)\.

### Listing the tags for a resource: list\-tags\-for\-resource<a name="tags-operations-examples-cli-list-tags-for-resource"></a>

The `list-tags-for-resource` command lists the tags for the specified resource\.

**Syntax**  
`aws athena list-tags-for-resource --resource-arn arn:aws:athena:region:account_id:datacatalog/catalog_name`

The `--resource-arn` parameter specifies the resource for which the tags are listed\. 

The following example lists the tags for the `mydatacatalog` data catalog\.

```
aws athena list-tags-for-resource --resource-arn arn:aws:athena:us-east-1:111122223333:datacatalog/mydatacatalog
```

The following sample result is in JSON format\.

```
{
    "Tags": [
        {
            "Key": "Time",
            "Value": "Now"
        },
        {
            "Key": "Color",
            "Value": "Orange"
        }
    ]
}
```

### Removing tags from a resource: untag\-resource<a name="tags-operations-examples-cli-untag-resource"></a>

The `untag-resource` command removes the specified tag keys and their associated values from the specified resource\.

**Syntax**  
`aws athena untag-resource --resource-arn arn:aws:athena:region:account_id:datacatalog/catalog_name --tag-keys key_name [key_name ...]` 

The `--resource-arn` parameter specifies the resource from which the tags are removed\. The `--tag-keys` parameter takes a space\-separated list of key names\. For each key name specified, the `untag-resource` command removes both the key and its value\.

The following example removes the `Color` and `Time` keys and their values from the `mydatacatalog` catalog resource\.

```
aws athena untag-resource --resource-arn arn:aws:athena:us-east-1:111122223333:datacatalog/mydatacatalog --tag-keys Color Time
```