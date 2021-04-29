Front-End Technologies Overview
-------------------------------

The frontend of Local EnteroBase has been developed using the VueJS Javascript framework accompanied 
by the Vuetify CSS Framework. VueJS has been combined with Flask by serving it alongside 
the app and importing Vue into the Jinja templates. This means that the codebase follows 
the familiar Flask project layout but now incorporates the reactive benefits of Vue.

VueJS 
``````
For each HTML page, a corresponding JavaScript file containing the Vue instance for the 
page is required. These JavaScript files can be found in the /static/js folder of the repo. 
These files match against the HTML template files (found in the templates folder) that they 
are used in, by having the same name. 

Information regarding the Vue instance can be found `here <https://vuejs.org/v2/guide/instance.html>`_.
For each Vue instance, we need to change the default delimiters from '{{', '}}' to '[[', ']]' 
so that they do not clash with the '{{', '}}' delimiters that are used by Jinja. We need to 
provide the Vue instance with an existing DOM element to mount on. For this, we use the top-level 
div that has an id="app". We use the directive 'v-cloak' provided by Vue on this div. This is used 
to hide un-compiled Vue delimiters ([[,]]) bindings until the Vue instance is ready. By adding a 
CSS style we ensure that all elements contained within this div are not displayed. When the Vue 
instance finishes compilation the directive will be removed and the page will be shown.

Vuetify
````````
Vuetify has been incorporated in a similar manner as seen in the "Usage with CDN" section of the 
"Get started with Vuetify documentation" (`<https://vuetifyjs.com/en/getting-started/installation/#usage-with-cdn>`_).
Information regarding the Vuetify markup can be found here: `<https://vuetifyjs.com/en/components/application/>`_.
As can be seen in the base template files in the repo, the key property, 'app', is required in each HTML file. 
This serves as the mount point for many of Vuetify’s components and functionality. The second important element 
is 'main'. This acts as a semantic replacement for the main HTML element and is the root of each of the pages content.

Vuetify offers great documentation through their website (`<https://vuetifyjs.com/en/>`_) 
and has code examples showcasing many of the UI components that have been used throughout Local EnteroBase.


App Structure
``````````````
As mentioned, the codebase follows that of a standard Flask project layout with the HTML files found under 
the templates folder and JavaScript files in the static/js folder. The HTML pages and their corresponding 
Javascript files share the same names.

Template inheritance offered by Jinja has been used to allow for common components that are found across 
multiple pages to be defined in a single HTML template file that children templates can extend. 

The HTML templates for the configuration pages used in Local EnteroBase extend the 'configuration_base.html' template

The HTML templates for the main pages of the application (e.g. home, database_home, job_viewer) extend 
the 'home_base.html' template.