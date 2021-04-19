Developer Environment
=====================

Prerequisites
--------------

To set up the developer environment, first either complete the manual or 
automatic installation. This step should set up the postgres server, redis
server, and nginx server.
* :ref:`automatic-installation-label`
* :ref:`manual-installation-label`

You will also need to download the bitbucket repository. Preferrably store this in a
separate directory to where the Local EnteroBase installation files are stored, to
help avoid confusion. e.g. store it in $HOME/local_enterobase_repo.

After the installation has finished, run the following command:

  ::
    
    $ singularity instance stop egp

This stops the server running with the code already inside the container and frees
up the port to be used for development.

Running the Application
------------------------

Entering the Container
''''''''''''''''''''''
To develop the app and run it within the correct environment, you will need to
enter the container, binding your Local EnteroBase repository code to override
what the container stores. This allows you to avoid having to rebuild the container
each time you make changes to the codebase.

  ::

    $ singularity shell /replace/with/path/to/local_enterobase_repo:/var/www/local_enterobase $HOME/local_enterobase_home/local_enterobase/EGP.sif
    $ cd /var/www/local_enterobase
    $ source /venvs/gunicorn-env/bin/activate

What the above commands do is enter the shell, cd to the correct directory, and 
source the python virtual environment.

Development
'''''''''''
To run the application in development mode, set STAGING="development" in manage.py.
This is done to make sure the celery worker is connected to the same database as the
server.

**The following commands are to be run inside the container after following the 
"Entering the Container" stage**

Run Dev Server:
  ::
    
    $ python3 manage.py run_app

Run Celery Worker:

  ::

    $ celery -A manage worker  --loglevel=debug --pidfile=$HOME/celerybeat_myapp_2.pid

Run Celery Beat:

  ::

    $ celery -A manage beat --loglevel=debug --pidfile=$HOME/celerybeat_myapp.pid -s $HOME/celerybeat-schedule:

Production
'''''''''''

To run the application in production mode, set STAGING="production" in manage.py.
This is done to make sure the celery worker is connected to the same database as the
server.

**The following commands are to be run inside the container after following the 
"Entering the Container" stage**

Run Dev Server:
  ::
    
    $ gunicorn -b 0.0.0.0:8000 --timeout 300 --name "local_entero" --log-file=$HOME/logs/gunilog.log --bind=unix:$HOME/sock "manage:create_app_production()"

Run Celery Worker:

  ::

    $ celery -A manage worker  --loglevel=debug --pidfile=$HOME/celerybeat_myapp_2.pid

Run Celery Beat:

  ::

    $ celery -A manage beat --loglevel=debug --pidfile=$HOME/celerybeat_myapp.pid -s $HOME/celerybeat-schedule:

Additional Info
----------------

Every time you change the code, the dev server will auto-reload, but the Celery Worker, Celery Beat will need to be restarted. If you are running it in production mode you will need to manually restart the gunicorn server as well.
