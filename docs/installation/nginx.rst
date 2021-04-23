.. _nginx-installation-label:

NGINX Web Server
----------------

NGINX acts as a reverse-proxy and load balancer for Local EnteroBase. It will serve requests from the website to all the servers it is installed on.

Prerequisites
=============

Please ensure that Docker is installed. The instructions to install Docker can be found here: :ref:`docker-installation-label`

Installing the Image
=====================
* The first stage is setting up the Docker Container, please note that this requires sudo privileges to set up.
* An optional step is to pull the image first, but Docker will take care of this step for you if it cannot find the image on your local machine.

  ::

    sudo docker pull localenterobase/nginx:1.0


Running the NGINX Container
===========================

To run the container, the user first needs to set up the sub-folder in their own machine. The default NGINX configuration file, certs folder, and logs folder will be copied into this sub folder. This then gets bound to the directory "/home/nginx_user" in the container. The container is also currently saved under the name localenterobase/nginx:1.0.

* The Docker container takes three different args (setup, test, run) detailed below.

  * **setup** - used to setup the file structure on the host machine.
  * **test** - ensure that a valid .conf file is being used for NGINX.
  * **run** - run the NGINX server.
* The directory structure needs to be set up on the host machine. To do this, you can use the docker image by using the following command:

  ::

    sudo docker run --rm -v $HOME/nginx:/home/nginx_user/:z localenterobase/nginx:1.0 setup

* NOTE: The above command will create the folder in the home directory in the local machine. If you wish to install it in a specific location change $HOME to the directory you wish to set up the nginx files in.

* The directory structure is setup as follows:

  ::

    nginx
	|-- nginx.conf
	|-- certs
	| |-- cert.pem
	| |-- key.pem
	|-- logs
	  |-- local_enteroBase_access.log
	  |-- local_enteroBase_error.log

* NOTE: Because the folders are created through Docker, the folder and all its contents are owned by root. You should change the owner to a non-root user on your system. This can be done by running:
  ::

    sudo chown -R <user> $HOME/nginx

* Before proceeding any further, make sure you have downloaded your custom nginx.conf file from the main enterobase website https://35.246.24.128:5566/local_enterobase/nginx_config
* After you have downloaded your nginx.conf file please replace the one initialised from the setup with it, located in the $HOME/nginx directory.
  ::

	mv /path/to/downloaded/nginx.conf $HOME/nginx/nginx.conf

* If you wish you can use the default conf if you replace the 'replaced_by_your_server_uri' with your server ip, but it is strongly recommended to use the one downloaded when setting up your account with enterobase.
  ::

    vi $HOME/nginx/nginx.conf

* You will need to provide a valid ssl certificate which should be saved inside the certs folder inside the user's home folder set up in the step above.
* In case your server does not have a valid ssl certificate, you can temporarily create a self-signed certificate using the following link. Please rename the created certificate and key so that they match those stored in the above directory structure:

  * https://linuxize.com/post/creating-a-self-signed-ssl-certificate/

* Alternatively, you can use the default certs inside the docker container (no modification is needed).
* This should be temporary, as it is important to get a valid ssl certificate from a trusted Certificate Authority.


* Use the following command to validate the configuration of the server. Change the 'nginx.conf' if there is any error message.

  ::

    sudo docker run --rm -p 80:80 -p 443:443 -v $HOME/nginx:/home/nginx_user/ --name nginx_local_enterobase localenterobase/nginx:1.0 test

* Use the following command to start the NGINX server:

  ::

    sudo docker run --rm -d -p 80:80 -p 443:443 -v $HOME/nginx:/home/nginx_user/ --name nginx_local_enterobase localenterobase/nginx:1.0 run

* The instance name is nginx_local_enterobase

* Here is a brief explanation on what each of the flags being used mean:

  * --rm: used to remove the container after it finishes executing.
  * -d: used to run the container in detached mode (so it runs in the background).
  * -v: used to mount a directory in the local machine, inside the container.
  * -p: used to bind  a port in the local machine to a port in the docker image.

    * The reason port 80 is being mapped to port 80 and likewise with port 443, is because port 80 is what http communicates over, and https communicates over 443.
  * --name: used to name the running container.

Verifying Container Is Running
==============================

* First, to verify the image is downloaded, run:

  ::

    sudo docker image ls

* You should see the image 'localenterobase/nginx:1.0' listed, or whatever you called the image you built from scratch.
* To verify the container is running after executing the docker run command, run:

  ::

    sudo docker container ps
    CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                                      NAMES
    1815ee4bc505        localenterobase/nginx:1.0   "docker-entrypoint.s…"   25 hours ago        Up 25 hours         0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   nginx_local_enterobase


* You should see the container which is named 'nginx_local_enterobase', or another name if you renamed the container.
* You may check ports 80 and 443 (or any other ports you are using) to see if traffic is reaching the site.
* Also at this stage, you should see "502 Bad Gateway" if you visit the websites URL. This means NGINX is setup but the internal server (gunicorn) is not just yet.

Restarting the Container
========================

* If you make any changes to the NGINX configuration, you will need to restart the container for those changes to take affect.
* Use the following command to restart the container:

  ::

	sudo docker restart nginx_local_enterobase

Please note this was developed on Ubuntu 20.04, and tested on Ubuntu 20.04, and Debian 9.
