Job Viewer
-----------

This page is used to view the user's jobs. It is built using a vuetify data-table.
This allows for sorting and basic filtering. Either AG Grid or creating separate.
filtering components is necessary to provide better filtering options if desired.

There are currently no live updates on the page itself. The Celery workers run the
process in the background and are triggered by the Celery worker. The status is checked
when the page is loaded. Buttons are only displayed when the assembly has completed 
successfully.

Upload Files
`````````````
The upload files button send the result .fasta and .fastq files to Central EnteroBase.

Download Files
```````````````
The download files button creates a .zip of completed job results and allows the
zip to be downloaded to the user's system.

Files and Functions
````````````````````

The back-end endpoints are located in:

- $PROJECT_ROOT/local_entero/manage_jobs/views.py
    - job_viewer(): render template 

The front-end components are located in:

- $PROJECT_ROOT/local_entero/templates/job_viewer.html
- $PROJECT_ROOT/local_entero/static/js/job_viewer.js
    - uploadStrain(row, item): upload strain to Central EnteroBase
    - downloadStrain(row, item): download strain to local system

