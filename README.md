# iot-edge-opc-publisher-testbed
This is a docker compose configuration which is used to test the OPC UA method interface of [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher.git).

The setup is using 3 containers, which are all avaialbe on Docker Hub.

## hansgschossmann/iot-edge-opc-publisher-testserver
An OPC UA server, based on the OPC UA .NET Standard Console Reference server. The source could be found [here](https://github.com/hansgschossmann/iot-edge-opc-publisher-testserver.git).

A container is available as [hansgschossmann\iot-edge-opc-publisher-testserver](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-publisher-testserver/) on Docker Hub.
## microsoft/iot-edge-opc-publisher
The OPC Publisher using the source code of the official OPC Publisher repository [here](https://github.com/Azure/iot-edge-opc-publisher.git). 

A container is available as [microsoft/iot-edge-opc-publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/) on Docker Hub.
## hansgschossmann/iot-edge-opc-publisher-testclient
An OPC UA client, based on the .NET Standard Console client, which is calling OPC UA methods of OPC Publisher to publish/unpublish nodes in the testserver. The source could be found [here](https://github.com/hansgschossmann/iot-edge-opc-publisher-testclient.git).

A container is available as [hansgschossmann\iot-edge-opc-publisher-testclient](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-publisher-testclient/) on Docker Hub.

## Operation
- Set the environment varialbe `_HUB_CS` to the iothubownerconnetion string of the IoTHub OPC Publisher can send telemetry to.
- Start the testbed with `docker-compose up`
- Press CTRL-C to stop the testbed
