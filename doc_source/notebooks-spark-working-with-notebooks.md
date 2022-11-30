# Working with notebooks<a name="notebooks-spark-working-with-notebooks"></a>

You manage your notebooks in the Athena notebook explorer and edit and run them in sessions using the Athena notebook editor\. You can configure DPU usage for your notebook sessions according to your requirements\.

When you stop a notebook, you terminate the associated session\. All files are saved, but changes underway in declared variables, functions and classes are lost\. When you restart the notebook, Athena reloads the notebook files and you can run your code again\.

## Sessions and calculations<a name="notebooks-spark-sessions-and-calculations"></a>

Each notebook is associated with a single Python kernel and runs Python code\. A notebook can have one or more cells that contain commands\. To run the cells in a notebook, you first create a session for the notebook\. Sessions keep track of the variables and state of notebooks\. 

Running a cell in a notebook means running a calculation in the current session\. Calculations progress the state of the notebook and may perform tasks like reading from Amazon S3 or writing to other data stores\. As long as a session is running, calculations use and modify the state that is maintained for the notebook\.

When you no longer need the state, you can end a session\. When you end a session, the notebook remains, but the variables and other state information are destroyed\. If you need to work on multiple projects at the same time, you can create a session for each project, and the sessions will be independent from each other\.

Sessions have dedicated compute capacity, measured in DPU\. When you create a session, you can assign the session a number of DPUs\. Different sessions can have different capacities depending on the requirements of the task\.