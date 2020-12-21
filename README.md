# fhirserver_demo

# Context
This is a simple guide about 'how-to' deploy and use a fhir server based on InterSystems IRIS for Health.
The setting up process is detailed in a 'step-by-step' fashion. It is possible/recomended to automated this process (e.g. using docker compose) once you're proficient with all components. The idea here is to provide an explanation for every step.

# Assumptions
You'll need to have a docker deamon running on installation machine. Should you need any details, look here: https://docs.docker.com/engine/install/

# Docker pull/run
In this step we'll set up and run an instance of InterSystems IRIS for Health. We'll be using latest version available here: https://hub.docker.com/_/intersystems-iris-for-health/plans/80ae1325-d535-484e-8307-b643c2865dd8?tab=instructions

1) Pull and run InterSystrms IRIS for Health, running in background (-d), mapping ports (--publish 9091:51773 and --publish 9092:52773) and binding a mount volume (--volume /my_local_host/mount_path:/durable). This container will be named 'irishealth'. Ports 51773 and 52773 inside de container will be exposed on ports 9091 and 9092 outside the container. The folder /my_local_host/mount_path on local machine will be mounted inside the container on /durable mount point. 
Run the following command (change /my_local_host/mount_path to the appropriate path on your system)
$ docker run --name irishealth -d --publish 9091:51773 --publish 9092:52773 --volume /my_local_host/mount_path:/durable containers.intersystems.com/intersystems/irishealth:2020.4.0.524.0

You should be able to acess InterSystems IRIS for Health Management Portal: http://localhost:9092/csp/sys/%25CSP.Portal.Home.zen?$NAMESPACE=HSLIB

2) Start bash console from terminal:
$ docker exec -it irishealth bash

3) Start iris terminal:
$ iris session IRIS

4) Change from namespace 'USER' to namespace 'HSLIB':
USER>zn "HSLIB"

5) Install FHIR server on a new namespace called 'FHIRSERVER':
USER>do ##class(HS.HC.Util.Installer).InstallFoundation("FHIRSERVER")
