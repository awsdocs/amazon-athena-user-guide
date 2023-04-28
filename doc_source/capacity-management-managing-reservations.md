# Managing reservations<a name="capacity-management-managing-reservations"></a>

You can view and manage your capacity reservations on the **Capacity reservations** page\. You can perform management tasks like adding or reducing DPUs, modifying workgroup assignments, and tagging or cancelling reservations\.

**To view and manage capacity reservations**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. Choose **Administration**, **Capacity reservations**\.

1. On the capacity reservations page, you can perform the following tasks:
   + To [create](capacity-management-creating-capacity-reservations.md#capacity-management-creating-capacity-reservations.title) a capacity reservation, choose **Create capacity reservation**\.
   + Use the search box to filter reservations by name or number of DPUs\.
   + Choose the status drop\-down menu to filter by capacity reservation status \(for example, **Active** or **Cancelled**\)\. For information about reservation status, see [Understanding reservation status](#capacity-management-understanding-reservation-status)\.
   + To view details for a capacity reservation, choose the link for the reservation\. The details page for the reservation includes options for [editing capacity](#capacity-management-editing-capacity-reservations), [adding workgroups](#capacity-management-adding-workgroups-to-a-reservation), [removing workgroups](#capacity-management-removing-a-workgroup-from-a-reservation), and for [cancelling](#capacity-management-cancelling-a-capacity-reservation) the reservation\.
   + To edit a reservation \(for example, by adding or removing DPUs\), select the button for the reservation, and then choose **Edit**\.
   + To cancel a reservation, select the button for the reservation, and then choose **Cancel**\.

## Understanding reservation status<a name="capacity-management-understanding-reservation-status"></a>

The following table describes the possible status values for a capacity reservation\.


****  

| Status | Description | 
| --- | --- | 
| Pending | Athena is processing your capacity request\. Capacity is not ready to run queries\. | 
| Active | Capacity is available to run queries\. | 
| Failed | Your request for capacity was not completed successfully\. Note that fulfillment of capacity requests is not guaranteed\. Failed reservations count towards your account DPU limits\. To release the usage, you must cancel the reservation\. | 
| Update pending | Athena is processing a change to the reservation\. For example, this status occurs after you edit the reservation to add or remove DPUs\. | 
| Cancelling | Athena is processing a request to cancel the reservation\. Queries that are still running in the workgroups that were using the reservation are allowed to finish, but other queries in the workgroup will use on\-demand \(non\-provisioned\) capacity\. | 
| Cancelled |  The capacity reservation cancellation is complete\. Cancelled reservations remain in the console for 45 days\. After 45 days, Athena will delete the reservation\. During the 45 days, you cannot re\-purpose or reuse the reservation, but you can refer to its tags and view its details for historical reference\. Cancelled capacity is not guaranteed to be re\-reservable at a future date\. Capacity cannot be transferred to another reservation, AWS account or AWS Region\.   | 

## Understanding Active DPUs and Target DPUs<a name="capacity-management-understanding-dpu-status"></a>

In the list of capacity reservations in the Athena console, your reservation displays two DPU values: **Active DPU** and **Target DPU**\.
+ **Active DPU** – The number of DPUs that are available in your reservation to run queries\. For example, if you request 100 DPUs, and your request is fulfilled, **Active DPU** displays **100**\.
+ **Target DPU** – The number of DPUs that your reservation is in the process of moving to\. **Target DPU** displays a value different than **Active DPU** when a reservation is being created, or an increase or decrease in the number of DPUs is pending\.

For example, after you submit a request to create a reservation with 24 DPUs, the reservation **Status** will be **Pending**, **Active DPU** will be **0**, and the **Target DPU** will be **24**\.

If you have a reservation with 100 DPUs, and edit your reservation to request an increase of 20 DPUs, the **Status** will be **Update pending**, **Active DPU** will be **100**, and **Target DPU** will be **120**\.

If you have a reservation with 100 DPUs, and edit your reservation to request a decrease of 20 DPUs, the **Status** will be **Update pending**, **Active DPU** will be **100**, and **Target DPU** will be **80**\.

During these transitions, Athena is actively working to acquire or reduce the number of DPUs based on your request\. When **Active DPU** becomes equal to **Target DPU**, the target number has been reached and no changes are pending\.

To retrieve these values programmatically, you can call the [GetCapacityReservation](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetCapacityReservation.html) API action\. The API refers to **Active DPU** and **Target DPU** as `AllocatedDpus` and `TargetDpus`\.

**Topics**
+ [Understanding reservation status](#capacity-management-understanding-reservation-status)
+ [Understanding Active DPUs and Target DPUs](#capacity-management-understanding-dpu-status)
+ [Editing capacity reservations](#capacity-management-editing-capacity-reservations)
+ [Adding workgroups to a reservation](#capacity-management-adding-workgroups-to-a-reservation)
+ [Removing a workgroup from a reservation](#capacity-management-removing-a-workgroup-from-a-reservation)
+ [Cancelling a capacity reservation](#capacity-management-cancelling-a-capacity-reservation)

## Editing capacity reservations<a name="capacity-management-editing-capacity-reservations"></a>

After you create a capacity reservation, you can adjust its number of DPUs and add or remove its custom tags\.

**To edit a capacity reservation**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. Choose **Administration**, **Capacity reservations**\.

1. In the list of capacity reservations, do one of the following:
   + Select the button next to the reservation, and then choose **Edit**\.
   + Choose the reservation link, and then choose **Edit**\.

1. For **DPU**, choose or enter the number of data processing units that you want in increments of 4\. The minimum number of DPUs that you can have is 24\. For more information, see [Understanding DPUs](capacity-management.md#capacity-management-understanding-dpus)\.
**Note**  
You can add DPUs to an existing capacity reservation at any time\. However, you cannot decrease the number of DPUs until 8 hours after you create the reservation or add DPUs to it\.

1. \(Optional\) For **Tags**, choose **Remove** to remove a tag, or choose **Add new tag** to add a new tag\.

1. Choose **Submit**\. The details page for the reservation shows the updated configuration\.

## Adding workgroups to a reservation<a name="capacity-management-adding-workgroups-to-a-reservation"></a>

After you create a capacity reservation, you can add up to 20 workgroups to the reservation\. Adding a workgroup to a reservation tells Athena which queries should execute on your reserved capacity\. Queries from workgroups that are not associated with a reservation continue to run using the default per terabyte \(TB\) scanned pricing model\.

When a reservation has two or more workgroups, queries from those workgroups can use the reservation's capacity\. You can add and remove workgroups at any time\. When you add or remove workgroups, queries that are running are not interrupted\.

When your reservation is in a pending state, queries from workgroups that you have added continue to run using the default per terabyte \(TB\) scanned pricing model until the reservation becomes active\.

**To add one or more workgroups to your capacity reservation**

1. On the details page for the capacity reservation, choose **Add workgroups**\.

1. On the **Add workgroups** page, select the workgroups that you want to add, and then choose **Add workgroups**\. You cannot assign a workgroup to more than one reservation\.

   The details page for your capacity reservation lists the workgroups that you added\. Queries that run in those workgroups will use the capacity that you reserved when the reservation is active\.

## Removing a workgroup from a reservation<a name="capacity-management-removing-a-workgroup-from-a-reservation"></a>

If you no longer require dedicated capacity for a workgroup or want to move a workgroup to its own reservation, you can remove it at any time\. Removing a workgroup from a reservation is a straightforward process\. After you remove a workgroup from a reservation, queries from the removed workgroup default to using on\-demand \(non\-provisioned\) capacity and are billed based on terabytes \(TB\) scanned\.

**To remove one or more workgroups from a reservation**

1. On the details page for the capacity reservation, select the workgroups that you want to remove\.

1. Choose **Remove workgroups**\. The **Remove workgroups?** prompt informs you that all currently active queries will finish before the workgroup is removed from the reservation\.\.

1. Choose **Remove**\. The details page for your capacity reservation show that the removed workgroups are no longer present\.

## Cancelling a capacity reservation<a name="capacity-management-cancelling-a-capacity-reservation"></a>

If you no longer want to use a capacity reservation, you can cancel it\. Queries that are still running in the workgroups that were using the reservation will be allowed to finish, but other queries in the workgroup will no longer use the reservation\.

**Note**  
Cancelled capacity is not guaranteed to be re\-reservable at a future date\. Capacity cannot be transferred to another reservation, AWS account or AWS Region\. 

**To cancel a capacity reservation**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. Choose **Administration**, **Capacity reservations**\.

1. In the list of capacity reservations, do one of the following:
   + Select the button next to the reservation, and then choose **Cancel**\.
   + Choose the reservation link, and then choose **Cancel capacity reservation**\.

1. At the **Cancel capacity reservation?** prompt, enter **cancel**, and then choose **Cancel capacity reservation**\.

   The reservation's status changes to **Cancelling**, and a progress banner informs you that the cancellation is in progress\.

   When the cancellation is complete, the capacity reservation remains, but its status shows as **Cancelled**\. The reservation will be deleted 45 days after cancellation\. During the 45 days, you cannot re\-purpose or reuse a reservation that has been cancelled, but you can refer to its tags and view it for historical reference\.