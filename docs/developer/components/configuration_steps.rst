Configuration Steps
-------------------

Overview
``````````
This is the page where the user will configure their installation of Local EnteroBase. 
The configuration process has been broken down into a procedural fashion through the use 
of Vuetify's v-stepper component (`Stepper component — Vuetify <https://vuetifyjs.com/en/components/steppers/>`_). 
Similar configuration fields such as the database configuration have been grouped into steps 
that the user will progress through. 

The following JavaScript libraries have also been used:

- **Vuelidate** - this is used to perform validation checks on each step. The user can only progress to the next stage in the configuration process when all checks are passed.
- **CropperJS** - this is used to create a modal image cropper window used when selecting a Local EnteroBase icon.

Front-End
``````````

Note: The HTML templates for the configuration pages used in Local EnteroBase extend the 'configuration_base.html' template.

The main aspect of the configuration steps page is Vuetify's 'v-stepper' component (`Stepper component — Vuetify <https://vuetifyjs.com/en/components/steppers/>`_). Steps covering General, Database, Mail Server and Messaging (Celery) configuration have been created. The Mail Server step has been commented out for now as the email system presently has not been developed. An important Vue instance variable is 'currStepNumber'. This defines which step content is displayed. For example, currStepNumber=0 will display the General Configuration page. 

For each step, all the input fields must pass the validation checks before the user can progress. To progress, we can simply increment 'currStepNumber'. To perform these validation checks, the Vuelidate JavaScript library has been used (`Vuelidate | A Vue.js model validation library <https://vuelidate.js.org/>`_). The following JSFiddle shows how this library has been incorporated: `Vuelidate example <https://jsfiddle.net/b5v4faqf/>`_ through the use of the browser-ready bundle served alongside the app. Please see the comments in the 'configuration_steps.js' which in conjunction with the Vuelidate documentation found on their website, should help explain how to use this library. The important feature is the 'validations' option that we define in the Vue instance. The keys must correspond to the variable names of input fields. For the values, we specify the validations that will be performed. For example, required, is used for most fields.

A final JavaScript library that is used for this page is Cropper js (`Cropper.js v1.5.11 <https://fengyuanchen.github.io/cropperjs/>`_). This library has been used to create a modal image cropper window used when selecting a Local EnteroBase icon.