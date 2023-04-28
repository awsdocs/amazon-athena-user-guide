# Creating capacity reservations<a name="capacity-management-creating-capacity-reservations"></a>

To get started, you create a capacity reservation that has the number of DPUs that you require, and then assign one or more workgroups that will use that capacity for their queries\. You can adjust your capacity later as needed to provide more consistent performance or better manage costs\. For information about estimating your capacity requirements, see [Determining capacity requirements](capacity-management-requirements.md)\.

**Important**  
Requests for capacity are not guaranteed and can take up to 30 minutes to complete\.

**To create a capacity reservation**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. Choose **Administration**, **Capacity reservations**\.

1. Choose **Create capacity reservation**\.

1. On the **Create capacity reservation** page, for **Capacity reservation name**, enter name\. The name must be unique, from 1 to 128 characters long, and use only the characters a\-z, A\-Z, 0\-9, \_\(underscore\), \.\(period\) and \-\(hyphen\)\.You cannot change the name after you create the reservation\.

1. For **DPU**, choose or enter the number of data processing units \(DPUs\) that you want in increments of 4\. For more information, see [Understanding DPUs](capacity-management.md#capacity-management-understanding-dpus)\.

1. \(Optional\) Expand the **Tags** option, and then choose **Add new tag** to add one or more custom key/value pairs to associate with the capacity reservation resource\. For more information, see [Tagging Athena resources](tags.md)\.

1. Choose **Review**\.

1. At the **Confirm create capacity reservation** prompt, confirm the number of DPUs, AWS Region, and other information\. If you accept, choose **Submit**\.

   On the details page, your capacity reservation's **Status** shows as **Pending**\. When your reservation capacity is available to run queries, its status shows as **Active**\.

At this point, you are ready to add one or more workgroups to your reservation\. For steps, see [Adding workgroups to a reservation](capacity-management-managing-reservations.md#capacity-management-adding-workgroups-to-a-reservation)\.