===================
Future Developments
===================

Suggested future developments for the application components have been summarised below.

-------------
Strain Upload
-------------

* Obtain the grid column information from the back-end as opposed to using the ‘strain_metadata_for_local_enterobase’ JSON file.
* Extension to the metadata text file import functionality.
    * Presently any read file information entered in the text file, will be ignored when imported into the grid. 
    * Perhaps one could store the filenames in the grid and when it comes to uploading the read files, have the user select a folder in which these files could be found.
* Create a custom text file template export function that does not use ag-grid’s export method so that the grid does not have to be empty to create the template file.

----------
Job Viewer
----------

* Add notifications for job status change.
* Potentially use web sockets to provide instant updates when on the page without a hanging request.
* Add button to upload all.
* Add button to retry failed assembly.
* This would involve setting the status to 'Received' so the celery beat worker.

------------------
Configuration Test 
------------------

* Investigate whether websockets are beneficial for this process instead of polling.

------------------
Other Improvements
------------------

* Create a script in the auto-installer to start/stop celery worker and celery beat.
* Make the celery beat run as a daemon process so it can run in the background (https://stackoverflow.com/questions/28105364/how-to-run-celery-beat-as-daemon).