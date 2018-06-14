# Working with Views in the Console<a name="views-console"></a>

In the Athena console, you can:
+ Locate all views in the left pane, where tables are listed\. Athena runs a [SHOW VIEWS](show-views.md) operation to present this list to you\.
+ Filter views\.
+ Preview a view, show its properties, edit it, or delete it\.

**To list the view actions in the console**

A view shows up in the console only if you have already created it\.

1. In the Athena console, choose **Views**, choose a view, then expand it\.

   The view displays, with the columns it contains, as shown in the following example:  
![\[The screenshot of an expanded view that shows columns.\]](http://docs.aws.amazon.com/athena/latest/ug/images/view_expanded.png)

1. In the list of views, choose a view, and open the context \(right\-click\) menu\. The actions menu icon \(⋮\) is highlighted for the view that you chose, and the list of actions opens, as shown in the following example:  
![\[The screenshot of the context menu for views. It lets you preview, show properties, edit, and delete a view.\]](http://docs.aws.amazon.com/athena/latest/ug/images/view_options.PNG)

1. Choose an option\. For example, **Show properties** shows the view name, the name of the database in which the table for the view is created in Athena, and the time stamp when it was created:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/view_properties.PNG)