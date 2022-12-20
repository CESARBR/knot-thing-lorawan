# KNoT-VirtualThing-LoRaWAN

KNoT VirtualThing-LoRaWAN is based on the ChirpStack Application Server, which is one of the services that ChirpStack uses for the LoRaWAN network.

KNoT VirtualThing-LoRaWAN add to the ChirpStack App Server a new integration that allows interaction with KNoT Cloud services. So, to send the LoRaWAN data to the KNoT Cloud you will need to run all the ChirpStack Services including the KNoT VirtualThing-LoRaWAN instead ChirpStack Application Server.

# Basic installation and usage
### Requirement
- Docker
    - Install docker: https://www.chirpstack.io/project/install/docker/
    
- Docker Compose
    - Install docker-compose: https://www.chirpstack.io/project/guides/docker-compose/ 

### Configuration

- To run all services go to /ChirpStack_Docker_Compose and unzip the chirpstack-docker.zip and move the directory /chirpstack-docker to out of the /knot-thing-lorawan.
    - The configuration/chirpstack-application-server/chirpstack-application-server.toml file contains the general specifications of the application. Parameters:
        - [application_server.integration] : inform what integration will be set on the platform to send the data.
            - enabled: the array of integrations enabled
        - [application_server.integration.knot]: give to the knot integration all the information that is needed to communicate with the cloud.
            - url : AMQP url used by the KNoT cloud.
            - name: name of the application on the KNoT cloud.
            - user_token: ( important ) Token authenticates the connection with KNoT cloud.
            
        - [[application_server.integration.knot.devices\]] : Array of the KNoT devices configuration. This is an array, for more information about .toml files, go to https://toml.io/en/
            - deveui: LoRaWAN EUI Device
            - token: if you want to put a KNoT id and token that you already have, please first config this section with all the information about the sensors, run the application and then the file will be generated on /knot/deviceContext.yaml, set the right permission to edit the file and put there your ids and tokens then run the application again to load your old ids and tokens.
            - name: device name.
            - [application_server.integration.knot.devices.config.schema] and [application_server.integration.knot.devices.config.event] More informarion about KNoT device config values, go to: https://knot-devel.cesar.org.br/doc/thing/unit-type-value.html?highlight=value%20type
    - On the file docker-compose.yml you will find the information about all the services including the subnetwork used on this compose, you can change it if you wanted on the section networks: lora: ipam: config: - subnet: 
    - **Important**, to run the knot-lorawan-service you need to upload or build the knot-chirpstack-app image as you can see on the section **chirpstack-application-server**. 
- To send data to the KNoT cloud, the device decoder has to follow a return format: The decode must return an OBJ with a data array as follow: 

```json
    {
        "data": [
            {
                "name": "temperature",
                "sensorId": 2,
                "value": 26.9
            },
            {
                "name": "humidity",
                "sensorId": 1,
                "value": 77.5
            }
        ]
    }
```
# Docker build
## Building and running
Change the working directory to the project root:
```bash
$ cd <path/to/knot-thing-lora>
```

To create the docker image:
```bash
$ docker build . --file Dockerfile --tag knot-chirpstack-app
```

Some files need to be persisted outside the container to avoid data loss. Go to **/chirpstack-docker** and write:
```bash
$ sudo chmod 777 -R configuration/knot/
```

To run all services, go to **/chirpstack-docker** and write:
```bash
$ sudo docker-compose up -d
```

To look at knot-sql logs in container:
```bash
$ docker logs -f <docker-container>
```

# ChirpStack Application Server

![Tests](https://github.com/brocaar/chirpstack-application-server/actions/workflows/main.yml/badge.svg?branch=master)

ChirpStack Application Server is an open-source LoRaWAN Application Server, part of the
[ChirpStack](https://www.chirpstack.io/) open-source LoRaWAN Network Server stack. It is responsible
for the node "inventory" part of a LoRaWAN infrastructure, handling of received
application payloads and the downlink application payload queue. It comes
with a web-interface and API (RESTful JSON and gRPC) and supports authorization
by using JWT tokens (optional). Received payloads are published over MQTT
and payloads can be enqueued by using MQTT or the API.

## Architecture

![architecture](https://www.chirpstack.io/static/img/graphs/architecture.dot.png)

### Component links

* [ChirpStack Gateway Bridge](https://www.chirpstack.io/gateway-bridge/)
* [ChirpStack Network Server](https://www.chirpstack.io/network-server/)
* [ChirpStack Application Server](https://www.chirpstack.io/application-server/)

## Links

* [Downloads](https://www.chirpstack.io/application-server/overview/downloads/)
* [Docker image](https://hub.docker.com/r/chirpstack/chirpstack-application-server/)
* [Documentation & screenshots](https://www.chirpstack.io/application-server/) and [Getting started](https://www.chirpstack.io/application-server/getting-started/)
* [Building from source](https://www.chirpstack.io/application-server/community/source/)
* [Contributing](https://www.chirpstack.io/application-server/community/contribute/)
* Support
  * [Support forum](https://forum.chirpstack.io)
  * [Bug or feature requests](https://github.com/brocaar/chirpstack-application-server/issues)

## Sponsors

[![CableLabs](https://www.chirpstack.io/img/sponsors/cablelabs.png)](https://www.cablelabs.com/)
[![SIDNFonds](https://www.chirpstack.io/img/sponsors/sidn_fonds.png)](https://www.sidnfonds.nl/)
[![acklio](https://www.chirpstack.io/img/sponsors/acklio.png)](http://www.ackl.io/)

## License

ChirpStack Application Server is distributed under the MIT license. See also
[LICENSE](https://github.com/brocaar/chirpstack-application-server/blob/master/LICENSE).
