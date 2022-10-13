# Tag your Amazon FSx resources<a name="tag-resources"></a>

To help you manage your file systems and other Amazon FSx resources, you can assign your own metadata to each resource in the form of *tags*\. With tags, you can categorize your AWS resources in different ways, for example, by purpose, owner, or environment\. This categorization is useful when you have many resources of the same type—you can quickly identify a specific resource based on the tags that you've assigned to it\. This topic describes tags and shows you how to create them\.

**Topics**
+ [Tag basics](#tag-basics)
+ [Tagging your resources](#tagging-your-resources)
+ [Tag restrictions](#tag-restrictions)
+ [Permissions and tagging](#tags-iam)

## Tag basics<a name="tag-basics"></a>

A *tag* is a label that you assign to an AWS resource\. Each tag consists of two parts that you define:
+ A *tag key* \(for example, `CostCenter`, `Environment`, or `Project`\)\. Tag keys are case sensitive\.
+ A *tag value* \(for example, `111122223333` or `Production`\)\. Like tag keys, tag values are case sensitive\. Tag values are optional\.



You can use tags to categorize your AWS resources in different ways, such as, by purpose, owner, or environment\. For example, you could define a set of tags for your account's Amazon FSx file systems that helps you track each instance's owner and stack level\.

We recommend that you devise a set of tag keys that meets your needs for each resource type\. Using a consistent set of tag keys makes it easier for you to manage your resources\. You can search and filter the resources based on the tags that you add\. For more information about how to implement an effective resource tagging strategy, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html) in the *AWS General Reference*\.





Some tagging behaviors to keep in mind: 
+ Tags don't have any semantic meaning to Amazon FSx and are interpreted strictly as a string of characters\.
+ Tags are not automatically assigned to your resources\.
+ You can edit tag keys and values, and you can remove tags from a resource at any time\.
+ You can set the value of a tag to an empty string, but you can't set the value of a tag to `null`\.
+ If you add a tag that has the same key as an existing tag on that resource, the new value overwrites the old value\.
+ If you delete a resource, any tags for the resource are also deleted\.
+ If you're using the Amazon FSx API, the AWS Command Line Interface \(AWS CLI\), or an AWS SDK, you can do the following: 
  + You can use the `TagResource` API action to apply tags to existing resources\. 
  + For some resource\-creating actions, you can specify tags for a resource when the resource is created\. By tagging resources at the time of creation, you can eliminate the need to run custom tagging scripts after resource creation\.

    If tags cannot be applied during resource creation, Amazon FSx rolls back the resource creation process\. This behavior helps ensure that resources are either created with tags or not created at all, and that no resources are left untagged at any time\.
**Note**  
Certain AWS Identity and Access Management \(IAM\) permissions are required for users to tag resources on creation\. For more information, see [Grant permission to tag resources during creation](using-tags-fsx.md#supported-iam-actions-tagging)\.

## Tagging your resources<a name="tagging-your-resources"></a>

You can tag Amazon FSx resources that exist in your account\. If you're using the Amazon FSx console, you can apply tags to resources by using the **Tags** tab on the relevant resource screen\. When you create resources, you can apply the **Name** key with a value, and you can apply tags of your choice when creating a new file system\. However, even though the console organizes resources according to the **Name** key, this key doesn't have any semantic meaning to the Amazon FSx service\.



To implement granular control over the users and groups that can tag resources on creation, you can apply tag\-based resource\-level permissions in your IAM policies to the Amazon FSx API actions that support tagging on creation\. By using such permissions in your policies, you get the following benefits: 
+ Your resources are properly secured from creation\. 
+ Because tags are applied immediately to your resources, any tag\-based resource\-level permissions controlling the use of resources are immediately effective\. 
+ Your resources can be tracked and reported on more accurately\. 
+ You can enforce the use of tagging on new resources, and control which tag keys and values are set on your resources\.

To control which tag keys and values are set on your existing resources, you can apply resource\-level permissions to the `TagResource` and `UntagResource` Amazon FSx API actions in your IAM policies\.

For more information about the permissions required to tag Amazon FSx resources at creation, see [Grant permission to tag resources during creation](using-tags-fsx.md#supported-iam-actions-tagging)\.

For more information about using tags to restrict access to Amazon FSx resources in IAM policies, see [Using tags to control access to your Amazon FSx resources](using-tags-fsx.md#restrict-fsx-access-tags)\.

For information about tagging your resources for billing, see [Using cost allocation tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing User Guide*\.

## Tag restrictions<a name="tag-restrictions"></a>

The following basic restrictions apply to tags:
+ The maximum number of tags per resource is 50\.
+ The maximum key length is 128 Unicode characters in UTF\-8\.
+ The maximum value length is 256 Unicode characters in UTF\-8\.
+ The allowed characters are letters, numbers, and spaces representable in UTF\-8, and the following characters: `+` `-` \(hyphen\) `=` `.` `_` \(underscore\) `:` `/` `@`\.
+ For each resource, each tag key must be unique, and each tag key can have only one value\.
+ Tag keys and values are case sensitive\.
+ The `aws:` prefix is reserved for AWS use\. If a tag has a tag key with this prefix, you can't edit or delete the tag's key or value\. Tags with the `aws:` prefix do not count against your tags per resource limit\.

You can't delete a resource based solely on its tags; you must specify the resource identifier\. For example, to delete a file system that you tagged with a tag key called `DeleteMe`, you must use the `DeleteFileSystem` action with the file system resource identifier, such as `fs-1234567890abcdef0`\.

When you tag public or shared resources, the tags that you assign are available only to your AWS account; no other AWS account will have access to those tags\. For tag\-based access control to shared resources, each AWS account must assign its own set of tags to control access to the resource\.

## Permissions and tagging<a name="tags-iam"></a>

For more information about the permissions required to tag Amazon FSx resources at creation, see [Grant permission to tag resources during creation](using-tags-fsx.md#supported-iam-actions-tagging)\.

For more information about using tags to restrict access to Amazon FSx resources in IAM policies, see [Using tags to control access to your Amazon FSx resources](using-tags-fsx.md#restrict-fsx-access-tags)\.