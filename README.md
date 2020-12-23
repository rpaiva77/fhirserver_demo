# fhirserver_demo

# Context
This is a simple guide about 'how-to' deploy and use a fhir server based on InterSystems IRIS for Health.
The setting up process is detailed in a 'step-by-step' fashion. It is possible/recomended to automated this process (e.g. using docker compose) once you're proficient with all components. The idea here is to provide an explanation for every step.

# Assumptions
You'll need to have a docker deamon running on installation machine. Should you need any details, look here: https://docs.docker.com/engine/install/
<br>You'll need to able to run a jupyter notebook. Should you need any details, look here: https://test-jupyter.readthedocs.io/en/latest/running.html#running

# Docker pull/run
In this step we'll set up and run an instance of InterSystems IRIS for Health. We'll be using latest version available here:<br> 
https://hub.docker.com/_/intersystems-iris-for-health/plans/80ae1325-d535-484e-8307-b643c2865dd8?tab=instructions

1) Pull and run InterSystrms IRIS for Health, running in background (-d), mapping ports (--publish 9091:51773 and --publish 9092:52773) and binding a mount volume (--volume /my_local_host/mount_path:/durable). This container will be named 'irishealth'. Ports 51773 and 52773 inside de container will be exposed on ports 9091 and 9092 outside the container. The folder /my_local_host/mount_path on local machine will be mounted inside the container on /durable mount point.<br><br>
Run the following command (change /my_local_host/mount_path to the appropriate path on your system):<br>
$ docker run --name irishealth -d --publish 9091:51773 --publish 9092:52773 --volume /my_local_host/mount_path:/durable containers.intersystems.com/intersystems/irishealth:2020.4.0.524.0<br><br>You should be able to acess InterSystems IRIS for Health Management Portal:<br> http://localhost:9092/csp/sys/%25CSP.Portal.Home.zen?$NAMESPACE=HSLIB

2) Start bash console from terminal:<br>
$ docker exec -it irishealth bash

3) Start iris terminal:<br>
$ iris session IRIS

# Install/Configure FHIR Server

4) Change from namespace 'USER' to namespace 'HSLIB':<br>
USER>zn "HSLIB"

5) Install FHIR server on a new namespace called 'FHIRSERVER':<br>
HSLIB>do ##class(HS.HC.Util.Installer).InstallFoundation("FHIRSERVER")

6) From the InterSystems IRIS for Health Management Portal homepage, switch to the FHIRSERVER namespace and navigate to Health > FHIR Configuration > Server Configuration to create a FHIR R4 server endpoint that stores FHIR data as JSON in a FHIR resource repository. Click the plus sign (+) and enter the following settings:<br>
  metadata: HL7v40<br>
  interaction strategy: FHIRServer.Storage.Json.InteractionsStrategy<br>
  URL: /csp/healthshare/fhirserver/fhir/r4<br>

7) Now we will load sample data from patients located on local file systems into the FHIR server. You should start by downloading patient data to your local file system.<br>Run the following commands from InterSystems IRIS for Health Terminal:<br>
  set $namespace = "FHIRSERVER"<br>
  set sc = ##class(HS.FHIRServer.Tools.DataLoader).SubmitResourceFiles("/local_path/for_your_fhir_data","FHIRServer",   "/csp/healthshare/fhirserver/fhir/r4")<br>
  write<br><br>
  This may take a few minutes - depending on the number and content of patient bundles. In the end you should see 1 as the output of 'write sc' command.
  
8) Attached you'll find a Jupyter Notebook with a few interactions with FHIR Server API. All interactions are based on the data you'll find in fhir_data folder. Should you use different data sets please review every interaction.

