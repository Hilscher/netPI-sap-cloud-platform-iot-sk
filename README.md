## Accessing SAP Cloud Platform Internet of Things with sample codes

Made for [netPI](https://www.netiot.com/netpi/), the Raspberry Pi 3 Architecture based industrial suited Open Edge Connectivity Ecosystem

### Debian Stretch with SAP Cloud Platform Internet of Things access, Starter Kit sample, SSH server and user root

The image provided hereunder deploys a container with installed Starter Kit for SAP Cloud Platform (SCP) Internet of Things and Python code snippets (and more) giving you an idea of how to communicate to your personal SAP Cloud after a short setup.

Base of this image builds a tagged version of [debian:stretch](https://hub.docker.com/r/resin/armv7hf-debian/tags/) with enabled [SSH](https://en.wikipedia.org/wiki/Secure_Shell), created user 'root' and preinstalled Stater Kit for the development environment Neo from [here](https://github.com/SAP/iot-starterkit/tree/master/neo).

Before using the sample you have to sign up with [SAP Cloud Platform](https://cloudplatform.sap.com/index.html) and create an account (trial). At the time of image preparation SAP Cloud's "Neo Trial" (please choose this environment when asked) allows you to try out a limited set of features for free at an unlimited trial duration including the Internet of Things platform.

Basic information about the SAP Cloud Platform Internet of Things for the Neo Environment is explained [here](https://help.sap.com/viewer/product/SAP_CP_IOT_NEO/Cloud/en-US).

#### Container prerequisites

##### Port mapping

For remote login to the container across SSH the container's SSH port `22` needs to be mapped to any free netPI host port.

#### Getting started

STEP 1. Open netPI's landing page under `https://<netpi's ip address>`.

STEP 2. Click the Docker tile to open the [Portainer.io](http://portainer.io/) Docker management user interface.

STEP 3. Enter the following parameters under **Containers > Add Container**

* **Image**: `hilschernetpi/netpi-sap-cloud-platform-iot-sk`

* **Port mapping**: `Host "22" (any unused one) -> Container "22"` 

* **Restart policy"** : `always`

STEP 4. Press the button **Actions > Start container**

Pulling the image may take a while (5-10mins). In some cases a web browser specific timeout may be exceeded interrupting the load process. In this case repeat the **Actions > Start container** action.

#### Accessing

The container starts the SSH server automatically. Open a terminal connection to it with an SSH client such as [putty](http://www.putty.org/) using netPI's IP address at your mapped port.

Use the credentials `root` as user and `root` as password when asked and you are logged in as root user `root`.

##### SAP Cloud Platform Cockpit

Follow the well documented "Getting started in the Cloud" chapter located [here](https://github.com/SAP/iot-starterkit/tree/master/neo#getting-started-in-the-cloud) to setup your SAP Cloud Internet of Things. Getting the Python sample code mentioned there running as it comes proceed slightly different to what is documented

STEP 1: Instead of declaring the fields sensor, variable, timestamp for the "OutboundMessage" just define timestamp as only field. Instead of the fields opcode and operand for the "InboundMessage" declare field opcode only.

STEP 2: ... in the end you will have created 1 Device Type with 2 associated Message Types and 1 Device as documented. As information you received `OAuth Access Token` e.g. "8bf27947504ea4bbcac8051d9ad69c7" (value will be hidden after creation, so note down) for the device.

STEP 3: Follow the next documented chapter "Deploy the Message Management Service (MMS) located [here](https://github.com/SAP/iot-starterkit/tree/master/neo/prerequisites/mms) that deploya as JAVA application "iotmms".

STEP 4: You have now all parameters ready to use the Starter Kit sample code

```
Your Personal Trial Account ID: e.g. "p2000540182trial" (got during "Internet of Things" activation)
Device ID: e.g."30e8204f-a401-4c03-88b3-3748ecfd4be0" (got during device creation)
OAuth Token: e.g. "8bf27947504ea4bbcac8051d9ad69c7" (got during device creation)
Message Type ID OutboundMessage: e.g. "a0d01c41e595cfea60f7" (got during message type creation)
Message Type ID InboundMessage: e.g. "b80ada08e104b55d11b2" (got during message type creation) 
```


STEP 5: The Message Management Service (MMS) JAVA application "iotmms" needs to be started now. But before you need to bind this application to a database source as descrbed [here](https://help.sap.com/viewer/7436c3125dd5491f939689f18954b1e9/Cloud/en-US/da35019231f04cdda975c530baafbb0d.html).

STEP 6: Click `SAP HANA/SAP ASE / Databases & Schemas` in the navigation pane, then click `New` and enter a name for the database such as "mydb", a SYSTEM user's password and then click `Create`. Creation will take 10 minutes. Hint: With a trial account the data base will be stopped after 12 hours.

STEP 7: Click `Applications/Java Applications` and then `iotmms` in the list of existing JAVA applications. Then click `Configuration/Data Sourec Bindings` and `New Binding`. Keep the value "<default>" as `Data Source`. Enter "SYSTEM" as `User Name` and the SYSTEM user password as `Custom Logon` and then click `Save`.

STEP 8: Click `Overview` and the button `Start`. Wait about 5 minutes to get the JAVA application started. In the end a green "Started" notification indicates it is running. Click then the `Application URL` and you will be forwarded to the `Message Management Service Cockpit`.

##### Starter Kit

STEP 1: The default command prompt folder will be `/iot-starterkit/neo/examples/python/binary_transfer`

STEP 2: Rename the file in this folder from `template-config.py` to `config.py` with `mv template-config.py config.py` to let the Python scripts include this configuration file later

STEP 3: Edit the `config.py` file. Delete the first line, replace the given placeholders with your parameters and save the file. 

Set the values
```
host='your_hcp_accout_id.hanatrial.ondemand.com'
device_id='configure in IoT Cockpit'
oauth_token='configure in IoT Cockpit'
credentials='account_username:account_password'

message_type_upstream='configure in IoT Cockpit'
message_type_downstream='configure in IoT Cockpit'

fieldname_upstream='configure in IoT Cockpit'
fieldname_downstream='configure in IoT Cockpit'
```

to 

```
host='iotmmsp2000540182trial.hanatrial.ondemand.com' ("iotmms"+trial account ID+".hanatrial.ondemand.com")
device_id='30e8204f-a401-4c03-88b3-3748ecfd4be0'
oauth_token='8bf27947504ea4bbcac8051d9ad69c7'
credentials='account_username:account_password' (replace with your Cloud credentials)

message_type_upstream='b80ada08e104b55d11b2'
message_type_downstream='a0d01c41e595cfea60f7'

fieldname_upstream='opcode'
fieldname_downstream='timestamp'
```


STEP 4: Initiate a HTTP POST request in accordance with the [Message Management Service API](https://help.sap.com/viewer/7436c3125dd5491f939689f18954b1e9/Cloud/en-US/7c71e35a19284736a806fb25a19dde41.html) using the command `python starterkit_binary_post.py`.

STEP 5: In the `Message Management Service Cockpit` watch the posted message and value under `Display Stored Messages`.

STEP 6: Continue to test the rest of the code snippets in the other Starter Kit's folders.

#### Tags

* **hilscher/netPI-sap-cloud-platform-iot-sk** - non-tagged (but tested OK) latest development output of the GitHub project master branch.

#### GitHub sources

The image is built from the GitHub project [netPI-sap-cloud-platform-iot-sk](https://github.com/Hilscher/netPI-sap-cloud-platform-iot-sdk). It complies with the [Dockerfile](https://docs.docker.com/engine/reference/builder/) method to build a Docker image [automated](https://docs.docker.com/docker-hub/builds/).

View the license information for the software in the Github project. As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained). As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.

Hint: Cross-building the image for an ARM architecture based CPU on [Docker Hub](https://hub.docker.com/)(x86 CPU based servers) the Dockerfile uses the method described here [resin.io](https://resin.io/blog/building-arm-containers-on-any-x86-machine-even-dockerhub/). If you want to build the image on a Raspberry Pi directly then comment out the two lines `RUN [ "cross-build-start" ]` and `RUN [ "cross-build-end" ]` in the file Dockerfile before.

[![N|Solid](http://www.hilscher.com/fileadmin/templates/doctima_2013/resources/Images/logo_hilscher.png)](http://www.hilscher.com)  Hilscher Gesellschaft fuer Systemautomation mbH  www.hilscher.com
