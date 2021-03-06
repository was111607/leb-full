.. _register-local-enterobase-label:

Register Local EnteroBase
-------------------------

The Local EnteroBase registration page, available at the "local_enterobase/register" endpoint, is designed to allow the installer to register their Local EnteroBase instance with Central EnteroBase to perform the upload test and get OAuth credentials. To read more about how the page looks from the point of view of the user, please take a look at the following page in the installation documentation: :ref:`local-enterobase-registration-label`. All code relating to this page is available in the "entero/local_enterobase" directory of the Central EnteroBase repository.

.. figure:: ../../images/local_enterobase_registration_screenshot.png
   :width: 600
   :align: center
   :alt: Local EnteroBase Registration Form

   **Local EnteroBase Registration Form**

The above figure shows a screenshot of what the user sees when they go to the page. Below is a list of the validations enforced by the FlaskForm used to handle the input:

1. Local EnteroBase Name: check if unique (no other instance with the same name) and between 1 and 64 characters.
2. External URL: check if unique (no other instance with the same URL) and valid URL or IP with "https".
3. Description & Justification: check if between 0 and 400 characters.
4. Central EnteroBase Username of Local EnteroBase Admin: check if unique (admin has no other Local EnteroBase instances) and user is logged in as admin.

Once a user has completed this form, a test token is generated for them and displayed in a notification as well as sent to their email address. The test token is used when the user performs the upload test upon installing Local EnteroBase (see  :ref:`upload-test-label`). Their details are stored in an SQLAlchemy model called "LocalEnterobaseClientRequest", this is where the record of their registration remains until their instance has been approved.
