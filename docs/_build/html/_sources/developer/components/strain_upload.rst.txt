.. _strain-upload-label:

=============
Strain Upload
=============

This is the page where the user will submit their strain metadata and upload their 
read files. A grid interface has been created which has been built using ag-grid-vue 
(`Vue Grid: Get Started with AG Grid  <https://www.ag-grid.com/vue-grid/getting-started/>`_). 
Various other Javascript libraries have also been used:

- **Papaparse** (`Papa Parse - Powerful CSV Parser for JavaScript <https://www.papaparse.com/>`_) - parses the tab-delimited text file when importing metadata.
- **MomentJS** (`Moment.js | Home <https://momentjs.com/>`_) - creates date objects when validating the user's input.
- **Vue Leaflet** (`Vue Leaflet <https://vue2-leaflet.netlify.app/>`_) - displays the openstreetmap through which locations can be selected. 


JavaScript Files Directory Structure
====================================
In addition to the main database_home.js javascript file, the Vue components found under the folders 
'cellEditors' and 'cellRenderers' have been created and imported. These are used to create custom cell 
editors and for displaying the contents of certain fields.

 ::

    js
    |-- database_home.js
    |-- cellEditors
    | |-- dateEditor.js
    | |-- locationEditor.js
    | |-- readFilesEditor.js
    | |-- sourceEditor.js
    |-- cellRenderers
    | |-- collectionDayRenderer.js
    | |-- collectionMonthRenderer.js
    | |-- dateRenderer.js
    | |-- locationRenderer.js
    | |-- readFileRenderer.js
    | |-- readFilesRenderer.js
    | |-- selectRenderer.js
    | |-- sourceRenderer.js


Data Received From the Back-end
===============================

When rendering the strain upload page, the following important data is parsed from the back-end:
Columns - the column information needed to set up the grid correctly for the chosen database (TODO: presently, this information is data taken from the 'strain_metadata_for_local_enterobase' JSON file.).
Database information - to display the chosen database name used when submitting the strain data so that the back-end knows which database to store it in.


The Grid Component
==================

The grid has broken down into several key elements.

Configuration
-------------
The Grid is defined in the 'database_home.html' file. This is the ag-grid component definition. Various configuration settings are defined through property bindings and events are handled through event bindings. 

.. figure:: ../../images/ag_grid_component_definition.png
   :align: center
   :alt: AG Grid Component Definition

**Fig. 1 - AG Grid Component Definition**

Some important property bindings are as follows:

* row-data - the list of rows, which will be empty for our grid.
* column-defs - defines the columns and the various properties that each one will have.
* row-selection - this will allow rows to be selected by clicking on them.
* framework-components - this will register the cell editor and renderer components.
* context - sets the context object to allow us to communicate between parent (grid component) and child components (cell editors and renderers).

Some important event bindings are as follows:

* grid-ready - calls the 'onGridReady' function once the grid has been initialised. This is required in order to access the Grid API (both gridApi and columnApi) which are only available after the gridReady event has been fired. We also use this event to resize the column headings so that the grid is neatly displayed.
* cell-context-menu - calls the 'onCellRightClick' function whenever the user right-clicks a cell in the grid. This function will display the context menu if there are rows that are currently selected.
* selection-changed - calls the 'onRowSelectionChange' function whenever the row selection is changed (ie. rows are deselected or new ones selected). This function will update the variable that stores the number of rows that are currently selected. This variable is used throughout the page, for example, disabling the 'delete rows' button and also not displaying the right-click, context menu if there no selected rows.
* cell-value-changed - calls the 'onCellValueChanged' function whenever the user updates the contents of a cell. This function updates the undo and redo buttons.

Creating the Column Definitions
-------------------------------

The VueJS beforeMount life cycle hook (which is called right before the mounting begins) is used to define some of the aforementioned configuration properties (such as the list of frameworkComponents). One of the most important actions that are performed here is the creation of the column definitions through the use of the 'getColumns' function. A column is defined using various properties, the list of which can be found here (`JavaScript Grid: Column Properties <https://www.ag-grid.com/javascript-grid/column-properties/>`_). 

The first two columns that are defined are an ID column which displays the id of each row, and a checkbox column that provides a means for selecting multiple columns. These two columns are pinned to the left side of the grid.

Next, we loop over the list of column information that was received from the back-end and for each one define the column to be created in the ag-grid component with the appropriate properties. For each one, a default definition is created which defines the field and header name as well as sets them to be resizable and editable. It's important to set lockVisible to true to prevent the user from being able to remove columns by dragging them outside of the grid.

We now apply various properties depending on the column type and name. For example, if the column is required indicated by (column.validator being equal to 'requiredFieldValidator'), we define a 'cellClassRule' function. This function will display the cell with a red background if its contents are empty or undefined. For some of the cell editor columns, separate cellClassRule functions need to be defined. For example, for the read file editor column, the cellClassRule observes the two columns that store the read file objects. If one or more of these read file objects are missing, we display the read file editor cell with a red background.

Another two important properties that we need to apply are 'cellRenderer' and 'cellEditor'. These are assigned with their corresponding Vue components and these are used for the read files, source details, collection date and location columns, where complex user input beyond simple text input is required.

For these complex fields, we also create expandable column groups. These consist of the main column that is used to get the user's input. The subsequent children columns, which by default are collapsed, are read-only (indicated by the grey background). These values are editable through the main input column. For example, for the read files group, the main column opens a pop-up editor where the user selects two read files. The children columns are 'file1' and 'file2' and display the chosen files. These columns that display the two files are read-only and can be changed through the main pop-up editor.


Cell Editors
------------

An important aspect of the grid that helps improve the usability and ease of data entry, is the use of custom written cell-editors. These are required for complex fields such as the read files editor where inputs beyond simple text are required. The cell editors are VueJS components that are imported and used when the user enters editing mode on the columns on which they have been registered on.

Cell editors have been created for the following complex columns:

* Read file editor - a 'double-click to add' column which opens a popup editor where the user selects the two read files via file inputs.
* Source editor - a 'double-click to add' column which opens a popup editor where the user first queries the source details, and an API is used to predict the source niche and type.
* Collection date editor - a column with the 'calendar icon' which is primarily a text input field but also offers a calendar widget that is opened on pressing the calendar icon.
* Location editor - a 'double click to add' column which opens a popup editor where the OpenStreetMaps slippy map will be displayed using leaflet.

The code for each of these can be found in the /js/cellEditors folder. 

For information regarding the operation of these components, please observe the 'Cell Renderer Component' on the following webpage: `Vue Grid: Cell Editors <https://www.ag-grid.com/vue-grid/component-cell-editor/#cell-renderer-component-1>`_. This provides an overview of the interface that these Vue components must follow.

Here are some of the important functions that need to be implemented:

* isCancelAfterEnd() - this is one of the most crucial functions used in the collection date editor to perform input validation checks. This function gets called once when editing is finished (enter pressed or save button pressed etc.). In the case where the validation checks fail e.g. invalid date format, we return true, and the result of the edit will be ignored.
* getValue() - this returns the value of cell editing from the Vue component to the grid. It is the final function that is called (if editing has not been cancelled). For each editor, we must set the values of their associated read-only columns. For example, when the user has finished selecting the read files, the read file 1 and read file 2 columns are populated with the chosen read file objects.
* isPopup() - for the popup editors (read files, source and location) we need to return true from this function to ensure that the editor will appear in a popup.

Collection Date Cell Editor Example
```````````````````````````````````

To help further explain the operations of the cell editor components, I will go over the collection date cell editor, the file for which can be found in ‘js/cellEditors/dateEditor.js’. When the component is created, we use the setInitialState function to set the component value to that of the cell contents. This is accessed through params.value. Once the user has entered their input and finished the editing (pressing enter or clicking outside of the editor), the isCancelAfterEnd function is called. Returning true from this function will mean that the result of the editing is ignored. We check that the format of the provided date and return true if the format is invalid thus not saving the user’s input and returning from the cell editor component. If the date is valid, we must save it. This is done through the getValue function which is subsequently called. We return this value to the grid and also set the read-only collection year, month and day columns.

Cell Renderers
--------------

Custom cell-renderers (`Vue Grid: Cell Renderer <https://www.ag-grid.com/vue-grid/component-cell-renderer/>`_) have been written to allow for more complex HTML to be displayed within the cells of certain columns, as opposed to displaying simple text. These components consist of a template that will contain the HTML for displaying, for example, an icon next to some text. The beforeMount life cycle hook is then used to retrieve the cell contents and display the appropriate information. 

Cell renderers have been made for each of the columns that have cell editors. The code for each of these can be found in the /js/cellRenderers folder. For the read files, source details and location, a plus symbol is displayed, with a message such as "double-click to add" or "double-click to edit" to inform the user how to interact with the cells. Other feedback is also provided through these. For example, for the read files column, if only 1 file is provided, a message saying that 2 read files are required will be displayed. 

For the collection date editor column, a cell renderer has been made that displays the calendar icon next to the chosen date. Cell renderers have also been made for two of the read-only columns that are part of the collection date group - collection day and collection month. For collection day, we display the day along with its ordinal indicator (e.g. 1st, 2nd). For collection month, we display the month name.

Row Sorting and Column Filtering
--------------------------------

Row sorting and Column filtering have been enabled on all columns except the read files editor column as this is only used to select the read files and populate the two read-only read file columns. Row sorting is performed by clicking on the column headers, toggling between the different sorting modes. Column filtering is performed by hovering on the column headings and pressing the filter button that will appear on the right.

To allow for sorting of the date column, where a range of valid input formats is allowed, a custom date comparator has been defined on the date editor column definition. This can be found in the 'dateComparator' function. MomentJS is used to create date objects upon which comparisons can be made.


Unique Strain Names and Read Files
==================================

A requirement from the client was that the strain name and read files are unique throughout the grid. To ensure this, two dictionaries are created where their key represents the strain name or read file and their value, the row id in which they are used in. Whenever a new input in either of these columns is made, a check is made to see if the entered value is present as one of the keys in these dictionaries. If it is, it means that the strain name/read file has already been used. The input will be rejected and a notification will be displayed informing the user of the row id (obtained from the value of the key in the dictionary) where the duplicate data can be found.

Whenever the user edits a cell in these columns, we must update the dictionary keys. For the strain name, this is handled using a 'valueSetter' function (`Vue Grid: Value Setters <https://www.ag-grid.com/vue-grid/value-setters/>`_). For the read files, this is handled in the custom read file editor component through the user of the context object (`Vue Grid: Context <https://www.ag-grid.com/vue-grid/context/>`_). This allows the cell editor component to access and make changes to the read file dictionary variable stored in the parent grid component. The update to the dictionary keys is also performed when we remove rows from the grid.



Metadata Import/Export
======================

A large part of the development of the metadata text file import/export functionality has been completed. Some aspects could be improved on in future development which is discussed.

Exporting The Template File
---------------------------

The 'exportTextFileTemplate' function is used to generate the template text file. This function uses the ag-grid 'exportDataAsCsv' (`JavaScript Grid: Export <https://www.ag-grid.com/javascript-grid/export/>`_) to generate the file. Important parameters are set for this export, namely removing column headings from the export and setting the column separator to "\t" as the file must be tab-delimited. In addition to this, we do not wish to export every column in the grid, for example, the complex column editors. 'getExportColumnIds' is used to get the export column ids. 

TODO: Presently, the grid must be empty to create the text file template, otherwise, all the rows present in the group at the time of export will also be written to the file.

Importing a Metadata Text File
------------------------------

A dropzone around the grid has been defined. Dragging a file on top of the grid will open the text file import modal upon which the file can be dropped on. Only '.txt' files will be accepted. A file explorer upload window can also be used by pressing the cloud icon. The function 'importMetaDataFile' handles the import process. To parse the text file, the Papaparse JavaScript library has been used. The text file input is streamed by defining a callback function. The first row in the file must be the header row. This row is checked to ensure all the headers match up against the expected headers. If these do not match, the text file is not of the correct format i.e. an additional column has been added, or the column headers have changed. In this case, parsing is aborted and the appropriate error message is shown. Otherwise, parsing continues. For each subsequent row, we check that there is at least one field that has been filled in. If there is, we store the row, otherwise, we discard it. This prevents the grid from being filled with empty rows.

With the parsing complete, we now have to populate the grid with the metadata. This is performed in the 'addFileDataToGrid' function. Creating a row involves defining a dictionary where the keys correspond to the column ids. The column ids are retrieved using the 'getExportColumnIds' function. As the file is of the correct format, these will match up with the column headings. We loop over each of the parsed rows and create the row dictionaries. For some of the column headings, extra processing is required to ensure that the entered data is valid. 

Specific Column Import Validation Checks
````````````````````````````````````````

As touched upon, some columns require extra handling before being saved into the grid. For the 'Collection Date' column, we check the date in a similar process as in the Collection Date cell editor. If it is valid, we need to populate the read-only columns collection_year, collection_month and collection_day. If it is not valid, we do not set the date. The cell will appear red in the grid, informing the user that an issue with their entered value has occurred.

For the strain name, we need to check each input against the dictionary of unique strain names. If matching keys are found, we discard the name and as with invalid dates, these cells will show up as red in the grid. If the strain name has not been used, we add it to the row data and store the name as a key in the dictionary. We must first add the row to the grid in order to get the row id that will be the value of this key.

TODO - Presently any read file information entered in the text file will be ignored when imported into the grid. The user must use the read file editor to select these files. Future development would be to store the filenames in the grid and when it comes to uploading the read files, have the user select a folder in which these files could be found.

Submitting the Strain Data
==========================

When the user presses the “Submit” button, two processes are carried out. We first check that all the required fields have been provided. If all rows pass this validation check 

Checking Required Columns
-------------------------

To make a request to the back-end to submit the strain data, all the required columns must be filled in. The function 'checkRequiredColumns' is used to check if each row to ensure this condition is met. This function returns an object containing a list of row ids where required fields are missing, a list of the data for each row and a list of readFiles. For each row, we first check if all the required column keys are present. If all the keys are present, it could be the case that the user entered a value but later cleared it. Therefore we need to check that all the fields are non-empty. If there are some non-empty fields, or if some of the keys were missing, we store the row id and do not save any of the row data or read files. If the row passed the required fields validation check, we store the read file objects in the read files array. We now need to do some processing on the row data and modify the read files column to be the name of the read file as opposed to the file object itself. This is required for how the back-end handles the read file upload process. Having done this, we store the row in the list of row data.

Having performed the check over the rows, we check if there were any returned row ids indicating rows that are missing required fields. If there are, strain data submission is cancelled and we display a message informing the user which row ids they need to complete (or a general message if there are more than 10). We also flash the cells in these rows to provide extra feedback.

Otherwise, we send the list of row data and the list of read files to the back-end where the read file upload process will begin.

Uploading the Read Files
------------------------

On submission of the data, the strain upload process will start and a modal window will appear where a circular progress component will be displayed along with a message telling the user that the strains are being uploaded. This modal is not dismissible and the user must remain on the page until all strains are uploaded.

During this process, before uploading the read files for a row, the back-end will check that the read files have not already been used by the user in a previous assembly to prevent duplicate assemblies from occurring. If this is the case, this row will be skipped and not be uploaded. On completion, the back-end will send a list of all the strain names that could not be uploaded because of this. This list is named 'invalid'. Several front-end outcomes could now occur.

* The length of the 'invalid' response list will be empty, thus all strains were uploaded successfully. A success message will be shown and all the strains in the grid will be removed (using the 'clearAllRows' method) as they have been successfully uploaded and the assembly jobs queued.
* The length of the 'invalid' response list is not empty, thus some strains could not be uploaded. An error message will be displayed informing the user of this along with the list of strain names and their row ids. The grid contents will then be updated through the use of the 'updateRowsAfterUpload' method. This method takes the list of strain names. It then loops over each row and only deletes the row if the strain name is not in this list. This, therefore, updates the grid so that only the rows in which the upload failed remain and the successfully uploaded strains removed.
* An error is caught aborting the upload process. A general error message is displayed.

For each of these cases, having received a response from the back-end, indicating that the upload process has finished, a close button will appear on the bottom right of the modal. This will allow the user to dismiss the modal window and return to the grid to make the required changes to any rows that could not be uploaded.


Column Information Summary Table
--------------------------------

To conclude the strain upload page documentation, the following table has been provided which summarises columns of the grid, the validation checks that are carried out and which ones allow column filling through the context menu.


.. figure:: ../../images/strain_upload_column_information.png
   :align: center
   :alt: Column Information Summary Table

**Fig. 2 - Column Information Summary Table**