EToKi, PostgreSQL and Gunicorn (EGP) Container Installation
------------------------------------------------------------

Prerequisites
==============

* Please follow the previous instructions to install and run Singularity and the NGINX web server before proceeding with the installation:

  * :ref:`Singularity <singularity-installation-label>`
  * :ref:`NGINX <nginx-installation-label>`

* If you are installing on Debian 9 or CentOS 7, please follow the instructions to set matching locale before proceeding with the installation:

  ::

    LC_ALL=en_GB.UTF-8
    LANG=en_GB.UTF-8

* Create a directory to store Local EnteroBase, and by extension the EGP container. The following command uses the default path "$HOME/local_enterobase_home" although you can change this to a location of your choosing.

  ::

    mkdir $HOME/local_enterobase_home

Note: Please follow the installation instructions in the order presented by this document, else the installation may fail.

Default Installation Directory
===============================

* The documentation assumes that Local Enterobase, and by extension the EGP container, will be installed at the default home path: "$HOME/local_enterobase_home".
* The command examples below will use this path by default, although these can be changed if you desire a different location.

Pulling the Container Image
=============================

* There is a single container image (EGP.sif) that stores the installations for EnteroBase Toolkit (EToKi), PostgreSQL and Gunicorn. This needs to be pulled from the Singularity cloud library as follows.
* If you wish to install it in a different location from the default, you can replace this with a location of your choosing.
* EGP.sif is the default name for the image, this can be changed by replacing it with <desired_name>.sif.
* "0.1" is the default image file to pull, this can be changed to a different tag to pull a required image version if required.

  ::

    singularity pull --arch amd64 $HOME/local_enterobase_home/local_enterobase/EGP.sif library://enterobase/default/egp:0.2

Setting Up and Running PostgreSQL Database Server
=================================================

1. Create a folder and subfolders inside the home directory (by default).

  * The folders will be bound to related folders inside the container at runtime for the server to function.
  * The following are commands to create the required directories. Example directory names (postgres, postgres/data, postgres/temp and postgres/logs) have been used that will be referenced by future commands.
  * If different folder names are used, all ocurrences of these directory names need to be replaced with your chosen directory names accordingly in future commands.
  * If you wish to store the postgres data in a different location to the default, you can also replace these with locations of your choosing.

    ::

      mkdir $HOME/local_enterobase_home/postgres
      mkdir $HOME/local_enterobase_home/postgres/data
      mkdir $HOME/local_enterobase_home/postgres/temp
      mkdir $HOME/local_enterobase_home/postgres/logs

    * The "data" folder is used for saving the database data.
    * The "temp" folder is used by the postgres server for temporary files.
    * The "logs" folder is used for saving the database server log files.

2. If the database server is being run for the **first time**, you must first initialise it.

  * The data folder from step 1 (e.g. $HOME/local_enterobase_home/postgres/data) must be empty for this step to succeed.
  * If the default installation directory was changed previously for EGP.sif and/or the postgres folders, replace them accordingly in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app init_db $HOME/local_enterobase_home/local_enterobase/EGP.sif

    * Here is a brief explanation on what each of the flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the respective data, temporary files and log directories are bound to initialise the database cluster folders and files, store temporary running files and database server running logs respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'init_db' script is called to initialise the database cluster and set up a database user for the flask app to interact with.

    * The following error message regarding locale settings may appear when running this command:

      ::

        initdb: invalid locale settings; check LANG and LC_* environment vairables
        pg_ctl: database sustem initialization failed

      * Please refer to and carry out the commands for setting matching locale in the 'Prerequisites' section if the above error occurs, and rerun the original initialisation command.

    * If the created database server data folder is not empty, it is assumed that the database cluster has been initialised and the process will fail with the following output:

      ::

        Database cluster initialisation failed
        Database cluster seems to have been previously initialised since the data directory is non-empty

      * Please ensure that a new database cluster is to be initialised first, and empty the data folder before rerunning the original initialisation command.

3. Start up the database server.

  * "flask_password" is the default database user password for the flask app. If this is changed during the local instance configuration then this must also be changed in the command to match.
  * The default port number for the database server is 5432. If this is changed in the local configuration, then you must replace 5432 with the new port number
  * If the default installation directory was changed previously for EGP.sif and/or the postgres folders, replace them accordingly in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.
  * Do not stop the database server immediately after setup as it is required to complete the installation and configuration of Local EnteroBase.

    ::

      SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app start_server $HOME/local_enterobase_home/local_enterobase/EGP.sif -p 5432

    * Here is a brief explanation on what each of the flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the respective data, temporary files and log directories are bound to initialise the database cluster folders and files, store temporary running files and database server running logs respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'start_server' script is called to begin running the database server.

      * -p:

        * The port number for the database server to run on, this can be changed depending on the local configuration although 5432 is the default value.

    * The output may 'hang' when the command is entered i.e. become seemingly stuck on the output as follows:

      ::

        waiting for server to start.... done
        server started
        |

      * Press the return/enter key to restore the normal running terminal state where inputs can be entered.
      * The database server is set to run as a background process thus will continue to do so when the potential hang is cleared.

**Usage Tooltips**

* Stop the database server:

  * Note: Stopping the running instance of the Singularity image will not stop the running of the database server.

    ::

      SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app stop_server $HOME/local_enterobase_home/local_enterobase/EGP.sif

    * Here is a brief explanation on what each of the flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the respective data, temporary files and log directories are bound to store the database cluster folders and files, store temporary running files and database server running logs respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'stop_server' script is called to safely stop the running database server. 'start_server' is called to start the running database server.

* To apply a system configuration change, the database server and application must be restarted using the following commands:

  ::

    singularity instance stop egp
    SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app stop_server $HOME/local_enterobase_home/local_enterobase/EGP.sif
    SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app start_server $HOME/local_enterobase_home/local_enterobase/EGP.sif -p 5432
    singularity instance start $HOME/local_enterobase_home/local_enterobase/EGP.sif egp

* To apply a database configuration change, the database server must be restarted using the following command:

  ::

    SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/postgres/data:/usr/local/pgsql/data -B $HOME/postgres/temp:/var/run/postgresql/ -B $HOME/postgres/logs:/usr/local/pgsql/logs --app restart_server $HOME/local_enterobase_home/local_enterobase/EGP.sif -p 5432

  * The database server restart can also be performed using start and stop commands if required:

    ::

      SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app stop_server $HOME/local_enterobase_home/local_enterobase/EGP.sif
      SINGULARITYENV_POSTGRES_PASSWORD=flask_password singularity run -B $HOME/local_enterobase_home/postgres/data:/usr/local/pgsql/data -B $HOME/local_enterobase_home/postgres/temp:/var/run/postgresql/ -B $HOME/local_enterobase_home/postgres/logs:/usr/local/pgsql/logs --app start_server $HOME/local_enterobase_home/local_enterobase/EGP.sif -p 5432

* Add new database users (with default SELECT, INSERT, UPDATE and DELETE permissions):

  ::

    singularity run --app create_dbuser $HOME/local_enterobase_home/local_enterobase/EGP.sif -u <username> -p <password>

  * Replace <username> and <password> with the required credentials.
  * The provided username must not already be an existing database user.

Running the Gunicorn Application
================================

1. Set up a username and password for the system app administrator so that you can use the web interface to configure the application, register your Local EnteroBase with Central EnteroBase at Warwick (Warwick EnteroBase) and test upload files to Warwick EnteroBase.

  * This enables the configured URL (the default being the localhost IP 127.0.0.1) to be used as an input into the browser to access the application configuration pages.
  * Set up the username and password by replacing "username" and "mypassword" with your own details.
  * If the default installation directory was changed previously for EGP.sif, replace it in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      singularity run --app set_user $HOME/local_enterobase_home/local_enterobase/EGP.sif -u <username> -p <password>

    * Here is a brief explanation on what each of the flags being used mean:

      * --app: Runs a specific script defined by the image.

        * Here, the 'set_user' script is called to set the user's details such that they can be used to access the application.

      * -u: Identifies the following argument as a username.

      * -p: Identifies the following argument as a password.

  * Please note that an error message regarding database configuration may appear when running this command, but it can be ignored at this stage. E.g:

    ::

      [2020-11-16 09:52:13,656] INFO in __init__: Database error: <class 'sqlalchemy.exc.OperationalError'>, error is (psycopg2.OperationalError) could not connect to server: Connection refused
    	  Is the server running on host "localhost" (127.0.0.1) and accepting
    	  TCP/IP connections on port 5432?

2. Run the Gunicorn application.

  * If the default installation directory was changed previously for EGP.sif, replace it accordingly in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.
  * "egp" is the given name of the running image instance, this can be changed to a name of your choosing.
  * The following gunicorn app running options are the set defaults, these values can be changed if desired.

    ::

      singularity instance start $HOME/local_enterobase_home/local_enterobase/EGP.sif egp -b 0.0.0.0:8000 --timeout 300 --name "local_entero" --log-file=$HOME/logs/gunilog.log --bind=unix:$HOME/sock

    * Here is a brief explanation on what each of the flags being used mean:

      * -b: Defines a server socket to bind.

        * Here, both the sockets 0.0.0.0:8000 and unix:$HOME/sock are defined to run the gunicorn app off of.

      * --timeout: Specifies the time to wait for activity from silent workers before killing and restarting them.

        * Here, a 300 second (5 minute) waiting time is defined.

      * --name: The base process name.

        * Here, it is named "local_entero".

      * --log-file: The path of the log file to write errors to.

        * Here, the default home directory and log file name are used.

**Usage Tooltips**

* To ensure that the Local EnteroBase instance is running, use the following command to list all the running Singularity instances:

  ::

    singularity instance list

  * The output will include the running instance name "i.e. local_enterobase", it will look something like this:

  +------------------------+------------+----------+----------------------------------+
  | INSTANCE NAME          | PID        | IP       |IMAGE                             |
  +========================+============+==========+==================================+
  | egp       | 23456      |            | /home/user/local_enterobase.sif             |
  +------------------------+------------+----------+----------------------------------+

  * If you want to restart the system, you should stop the instance first, then run it again using the commands for applying a system configuration change below.

Redis Setup and Usage
=====================

1. Create a folder and subfolders inside the home directory (by default).

  * The folders will be bound to related folders inside the container at runtime for the server to function.
  * The following are commands to create the required directories. Example directory names (redis, redis/data, redis/temp and redis/logs) have been used that will be referenced by future commands.
  * If different folder names are used, all ocurrences of these directory names need to be replaced with your chosen directory names accordingly in future commands.
  * If you wish to store the redis data in a different location to the default, you can also replace these with locations of your choosing.

    ::

      mkdir $HOME/local_enterobase_home/redis
      mkdir $HOME/local_enterobase_home/redis/data
      mkdir $HOME/local_enterobase_home/redis/temp
      mkdir $HOME/local_enterobase_home/redis/logs

    * The "data" folder is used for saving the Redis data.
    * The "temp" folder is used by the Redis server for temporary files. If there are any pid-related errors when starting Redis such as "/var/run/redis_6379.pid exists, process is already running or crashed", delete the ".pid" file in this directory and the problem should be resolved.
    * The "logs" folder is used for saving the Redis server log files.

2. Run the Redis server.

  * If the default installation directory was changed previously for EGP.sif and/or the Redis folders, replace them accordingly in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      singularity run -B $HOME/local_enterobase_home/redis/data:/var/redis/6379 -B $HOME/local_enterobase_home/redis/temp:/var/run/ -B $HOME/local_enterobase_home/redis/logs:/var/log/redis --app start_redis $HOME/local_enterobase_home/local_enterobase/EGP.sif

    * Here is a brief explanation on what each of the flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the respective data, temporary files and log directories are bound to store the Redis server folders and files, store temporary running files and Redis server running logs respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'start_redis' script is called to start running the Redis server.

**Usage Tooltips**

* Check if the Redis server is up and running:

  * If the default installation directory was changed previously for EGP.sif and/or the Redis folders, replace them accordingly in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      singularity run -B $HOME/local_enterobase_home/redis/data:/var/redis/6379 -B $HOME/local_enterobase_home/redis/temp:/var/run/ -B $HOME/local_enterobase_home/redis/logs:/var/log/redis --app ping_redis $HOME/local_enterobase_home/local_enterobase/EGP.sif

    * Here is a brief explanation on what each of the flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the respective data, temporary files and log directories are bound to store the Redis server folders and files, store temporary running files and Redis server running logs respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'ping_redis' script is called to check if the Redis server is running.

* Stop the Redis server:

  * If the default installation directory was changed previously for EGP.sif and/or the Redis folders, replace them accordingly in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      singularity run -B $HOME/local_enterobase_home/redis/data:/var/redis/6379 -B $HOME/local_enterobase_home/redis/temp:/var/run/ -B $HOME/local_enterobase_home/redis/logs:/var/log/redis --app stop_redis $HOME/local_enterobase_home/local_enterobase/EGP.sif

    * Here is a brief explanation on what each of the flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the respective data, temporary files and log directories are bound to store the Redis server folders and files, store temporary running files and Redis server running logs respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'stop_redis' script is called to stop running the Redis server.


Configuring EToKi
=================

1. Create a folder inside the home directory (by default).

  * The folder will be bound to a related folder inside the container at runtime to enable the correct functionality of EToKi for Local EnteroBase.
  * The following is a command to create the required directories. Their names have been used as defaults and will be referenced by future commands.
  * If a different folder name is used, all ocurrences of this need to be replaced with your chosen directory name accordingly in future commands.
  * The default installation location is $HOME/local_enterobase_home. If you wish to install it in a different location, you can also replace this with a location of your choosing.

    ::

      mkdir $HOME/local_enterobase_home/EToKi_externals
      mkdir $HOME/local_enterobase_home/EToKi
      mkdir $HOME/local_enterobase_home/EToKi/reads
      mkdir $HOME/local_enterobase_home/EToKi/prep_out
      mkdir $HOME/local_enterobase_home/EToKi/asm_out

    * "EToKi_externals" is used for saving external files to be used by EToKi.
    * "EToKi" folder is used for saving files to be used by EToKi and store results of their preparation and assembly.
    * "EToKi/reads" is used for storing read files to be prepared and assembled.
    * "EToKi/prep_out" is used for storing the preparation results of the initial read files.
    * "EToKi/asm_out" is used to store the assembly results of the prepared read files.

2. Copy the required configure data file (configure.ini) to the working directory of EToKi. $HOME/local_enterobase_home/EToKi is used by default.

  * Navigate to the main directory of EToKi first before copying the configure data file.
  * If the default installation directory was changed previously for EGP.sif, replace it in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      cd $HOME/local_enterobase_home/EToKi
      singularity run --app cp_configure $HOME/local_enterobase_home/local_enterobase/EGP.sif

3. Download usearch and the MiniKraken2 database for EToKi to function correctly. **(Not required for the beta test)**.

  * If the default directory for saving external files to be used by EToKi ($HOME/local_enterobase_home/EToKi_externals) was changed previously, replace it in the installation commands for usearch and MiniKraken2 with the changed directory.
  * Please ensure that usearch and MiniKraken2 to the same directory.
  * The following commands downloads usearch:

    ::

      cd $HOME/local_enterobase_home/EToKi_externals
      wget https://www.drive5.com/downloads/usearch11.0.667_i86linux32.gz
      chmod 755 usearch11.0.667_i86linux32.gz
      gzip -d usearch11.0.667_i86linux32.gz

    *

  * The following commands downloads the MiniKraken2 database:

    ::

      cd $HOME/local_enterobase_home/EToKi_externals
      wget https://github.com/DerrickWood/kraken2/archive/v2.0.8-beta.tar.gz
      tar xf v2.0.8-beta.tar.gz
      mv kraken2-2.0.8-beta minikraken2

4. Configure EToKi. **(Not required for the beta test)**.

  * If the name EToKi_externals has been changed, replace its occurrence in the following command by the new name.
  * If the storage location for configure.ini has been changed, replace its path in the following command by its location.
  * If the Kraken database has a different directory name other than the default "minikraken2" upon installation, you can leave it unchanged or change it to this/another appropriate name and replace its occurrence in the following command accordingly.
  * If the default installation directory was changed previously for EGP.sif and/or EToki_Externals, replace them in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.

    ::

      singularity run -B $HOME/local_enterobase_home/EToKi/configure.ini:/code/EToKi/modules/configure.ini -B $HOME/local_enterobase_home/EToKi_externals:/code/EToKi/local_externals --app run_etoki $HOME/local_enterobase_home/local_enterobase/EGP.sif configure --usearch /code/EToKi/local_externals/usearch11.0.667_i86linux32 --link_krakenDB /code/EToKi/local_externals/minikraken2/

    * Here is a brief explanation on what some flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the EToKi configuration file and the local externals folder storing usearch and minikraken2 are bound to enable updating the configuration paths and internally access usearch and minkraken2 respectively.

      * --app: Runs a specific script defined by the image.

        * Here, the 'run_etoki' script is called to pass in commands leading to the execution of EToKi functions, in this case it is cp_configure.

      * --usearch: Used to pass the locally downloaded usearch file to the container.

        * As /code/EToKi/local_externals is bound by the local externals folder, the internal container path that usearch is saved to can be used.

      * --link_krakenDB: Used to pass the locally downloaded Kraken database to the container.

        * As /code/EToKi/local_externals is bound by the local externals folder, the internal container path that the database directory is saved to can be used.

**Usage Tooltips**

* Preparing Read Files for Assembly:

  * If the default installation directory was changed previously for EGP.sif, replace it in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.
  * pe_example_1.fastq.gz, pe_example_2.fastq.gz and example_dir are example names for the read files to be prepared and the subdirectory in which to store their preparation results in. Replace these as required.

    ::

      singularity run -B $HOME/local_enterobase_home/EToKi/prep_out:/code/EToKi/prep_out -B $HOME/local_enterobase_home/EToKi/reads:/code/EToKi/reads --app run_etoki $HOME/local_enterobase_home/local_enterobase/EGP.sif prepare --pe /code/EToKi/reads/pe_example_1.fastq.gz,/code/EToKi/reads/pe_example_2.fastq.gz -p /code/EToKi/prep_out/example_dir/file_suffix

    * Here is a brief explanation on what some flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the folders for storing the read files and preparation results are bound to pass the locally stored read files to the container and receive the prepared files respectively.

      * --pe: Links one or more paired-end read files to prepare.

        * Here, 2 paired-end read files have been passed in as an example.
        * The flag --se for passing single-end read files can also be used if these are initially present.

      * -p: Links a target path to store preparation results.

        * Here, an example subdirectory within EToKi/prep_out has been passed in as an example, with 'file_suffix' being appended onto all generated filenames e.g. 'file_suffix_L1_R1.fastq.gz'

* Assembling Prepared Read Files:

  * If the default installation directory was changed previously for EGP.sif, replace it in the following command with the correct installation directory.
  * If the pulled image name "EGP.sif" was changed previously, replace it in the following command with your chosen name.
  * pe_example_1.fastq.gz, pe_example_2.fastq.gz and example_dir are example names for the resulting prepared read files from the previous step. Use the names of your resulting files accordingly.

    ::

      singularity run -B $HOME/local_enterobase_home/EToKi/asm_out:/code/EToKi/asm_out -B $HOME/local_enterobase_home/EToKi/prep_out:/code/EToKi/prep_out --app run_etoki $HOME/local_enterobase_home/local_enterobase/EGP.sif assemble --pe /code/EToKi/prep_out/pe_example_1.fastq.gz,/code/EToKi/prep_out/pe_example_2.fastq.gz --se /code/EToKi/prep_out/se_example_2.fastq.gz -p /code/EToKi/asm_out/example_dir/file_suffix

    * Here is a brief explanation on what some flags being used mean:

      * -B: Used to bind a directory on the local system to one inside the container to allow data to be read and written simultaneously since Singularity images are read-only otherwise.

        * Here, the folders for storing the prepared read files and assmbly results are bound to pass the prepared read files stored locally from the previous step to the container and receive the assembly results respectively.

      * --pe: Links one or more paired-end read files to assemble.

        * Here, 2 paired-end prepared read files have been passed in as an example.

      * --se: Links one or more single-end read files to prepare.

        * Here, a singular prepared read file has been passed in as an example. This is a possible result from preparing only paired-end read files.
        * This flag is optional as it depends on the initial read files and their preparation results.

      * -p: Links a target directory to store preparation results.

        * Here, an example subdirectory within EToKi/prep_out has been passed in as an example, with 'file_suffix' being appended onto all generated filenames e.g. 'file_suffix.result.fastq'

Additional Notes
================

* The local installation configuration file is saved in your home folder (.local_configuration_file.yml), you can edit it directly using any text editor (e.g. vim) or it can be alerted using “/update_system_configuration”  link from the web interface (it will be the default main web page if the database is not configured or not configured correctly).

* The application is accessible by the provided URL/IP address, set during NGINX configuration (in the nginx.conf file).
