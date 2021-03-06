Integrations in Central EnteroBase
----------------------------------

This section provides reference to the developments required in Central EnteroBase in order to run instances of Local EnteroBase. The majority of code relevant to Local EnteroBase can be found within the "entero/local_enterobase" and "entero/oauth" subdirectories of the Central EnteroBase repository. There are some additional functions that have been added to other parts of the codebase in order to maintain consistency, "entero/databases/system" (for defining database models) and "entero/admin" (for administrator purposes) are some examples of these.

.. toctree::
   :maxdepth: 2

   nginx_config
   register_local_enterobase
   administration
   oauth2_server