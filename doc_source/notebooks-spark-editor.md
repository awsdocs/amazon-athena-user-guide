# Using the Athena notebook editor<a name="notebooks-spark-editor"></a>

The Athena notebook editor is an interactive environment for writing and running code\. The following sections describe the features of the environment\.

## Command mode vs\. edit mode<a name="notebooks-spark-command-mode-vs-edit-mode"></a>

The notebook editor has a modal user interface: an edit mode for entering text into a cell, and a command mode for issuing commands to the editor itself like copy, paste, or run\.

To use edit mode and command mode, you can perform the following tasks:
+ To enter edit mode, press **ENTER**, or choose a cell\. When a cell is in edit mode, the cell has a green left margin\.
+ To enter command mode, press **ESC**, or click outside of a cell\. Note that commands typically apply only to the currently selected cell, not to all cells\. When the editor is in command mode, the cell has a blue left margin\.
+ In command mode, you can use keyboard shortcuts and the menu above the editor, but not enter text into individual cells\.
+ To select a cell, choose the cell\.
+ To select all cells, press **Ctrl\+A** \(Windows\) or **Cmd\+A** \(Mac\)\.

## Notebook editor menu<a name="notebooks-spark-notebook-editor-menu"></a>

The icons in the menu at the top of the notebook editor offer the following options:
+ **Save** – Saves the current state of the notebook\.
+ **Insert cell below** – Adds a new \(empty\) cell below the currently selected one\.
+ **Cut selected cells** – Removes the selected cell from its current location and copies the cell to memory\.
+ **Copy selected cells** – Copies the selected cell to memory\.
+ **Paste cells below** – Pastes the copied cell below the current cell\.
+ **Move selected cells up** – Moves the current cell above the cell above\.
+ **Move selected cells down** – Moves the current cell below the cell below\.
+ **Run** – Runs the current \(selected\) cell\. The output displays immediately below the current cell\.
+ **Run all** – Runs all cells in the notebook\. The output for each cell displays immediately below the cell\.
+ **Stop \(Interrupt the kernel\)** – Stops the current notebook by interrupting the kernel\.
+ **Format option** – Selects the cell format, which can be one of the following:
  + **Code** – Use for Python code \(the default\)\.
  + **Markdown** – Use for entering text in [GitHub\-style markdown](https://docs.github.com/en/get-started/writing-on-github) format\. To render the markdown, run the cell\.
  + **Raw NBConvert** – Use for entering text in unmodified form\. Cells marked as **Raw NBConvert** can be converted into a different format like HTML by the Jupyter [nbconvert](https://nbconvert.readthedocs.io/en/latest/usage.html) command line tool\.
+ **Heading** – Use to change the heading level of the cell\.
+ **Command palette** – Contains Jupyter notebook commands and their keyboard shortcuts\. For more information about the keyboard shortcuts, see the sections later in this document\.
+ **Session** – Use options in this menu to [view](notebooks-spark-getting-started.md#notebooks-spark-getting-started-viewing-session-and-calculation-details) the details for a session, [edit session parameters](notebooks-spark-getting-started.md#notebooks-spark-getting-started-editing-session-details), or [terminate](notebooks-spark-getting-started.md#notebooks-spark-getting-started-terminating-a-session) the session\. 

## Command mode keyboard shortcuts<a name="notebooks-spark-command-mode-keyboard-shortcuts"></a>

The following are some common notebook editor command mode keyboard shortcuts\. These shortcuts are available after pressing **ESC** to enter command mode\. To see a full list of commands available in the editor, press **ESC \+ H**\.


****  

| Key | Action | 
| --- | --- | 
| 1 \- 6 | Change the cell type to markdown and set the heading level to the number typed | 
| a | Create a cell above the current cell | 
| b | Create a cell below the current cell | 
| c | Copy the current cell to memory | 
| d d | Delete the current cell | 
| h | Display the keyboard shortcut help screen | 
| j | Go one cell down | 
| k | Go one cell up | 
| m | Change the current cell format to markdown | 
| r | Change the current cell format to raw | 
| s | Save the notebook | 
| v | Paste memory contents under the current cell | 
| x | Cut the selected cell or cells | 
| y | Change the cell format to code | 
| z | Undo | 
| Ctrl\+Enter  | Run the current cell and enter command mode | 
| Shift\+Enter or Alt\+Enter | Run the current cell and create a new cell below the output, and enter the new cell in edit mode | 
| Space | Page down | 
| Shift\+Space | Page up | 
| Shift \+ L | Toggle the visibility of line numbers in cells | 

## Editing command mode shortcuts<a name="notebooks-spark-editing-command-mode-shortcuts"></a>

The notebook editor has an option to customize command mode keyboard shortcuts\.

**To edit command mode shortcuts**

1. From the notebook editor menu, the choose the **Command palette**\.

1. From the command palette, choose the **Edit command mode keyboard shortcuts** command\.

1. Use the **Edit command mode shortcuts** interface to map or remap commands that you want to the keyboard\.

   To see instructions for editing command mode shortcuts, scroll to the bottom of the **Edit command mode shortcuts** screen\.

For information about using magic commands in Athena for Apache Spark, see [Using magic commands](notebooks-spark-magics.md)\.