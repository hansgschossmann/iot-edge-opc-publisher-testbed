# Using Microsoft Azure OPC Components
This repository contains several configurations to show how to configure and run Microsofts Azure OPC components:
- [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher)

## Containers used in the configurations
The setup is using 3 containers, which are all available via the Microsoft Container Registry or on Docker Hub.

### OPC PLC
An OPC UA server, based on the OPC UA .NET Standard Console Reference server. The source could be found [here](https://github.com/hansgschossmann/iot-edge-opc-plc).
The nodeset of this server contains various nodes which can be used to generate random data or anomalies.

A container is available as [hansgschossmann\iot-edge-opc-plc](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-plc/) on Docker Hub.

### OPC Publisher
The OPC Publisher using the source code of the official OPC Publisher repository [here](https://github.com/Azure/iot-edge-opc-publisher). 

A container is available as [mcr.microsoft.com/iotedge/opc-publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/) via the Microsoft Container Registry.

### OPC Testserver
An OPC UA server, based on the OPC UA .NET Standard Console Reference server. The source could be found [here](https://github.com/hansgschossmann/iot-edge-opc-publisher-testserver).
The nodeset of this server contains various nodes used by OPC Testclient to test OPC Publisher functionality.

A container is available as [hansgschossmann\iot-edge-opc-publisher-testserver](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-publisher-testserver/) on Docker Hub.

### OPC Testclient
An OPC UA client, based on the .NET Standard Console client, which is calling OPC UA methods of OPC Publisher to publish/unpublish nodes in the testserver. The source could be found [here](https://github.com/hansgschossmann/iot-edge-opc-publisher-testclient.git).
The client does access OPC Publisher to publish/unpublish nodes of OPC Testserver.

A container is available as [hansgschossmann\iot-edge-opc-publisher-testclient](https://hub.docker.com/r/hansgschossmann/iot-edge-opc-publisher-testclient/) on Docker Hub.

### Nomenclature
- <reporoot> is the root directory of this repositories clone

### Preparation
- Define an environment variable `_HUB_CS` and set it to your iothubowner connection string of the IoTHub you are using
- If you are using docker to run the setup, create a docker bridge network with name `iot_edge` with `docker network create iot_edge`. Verify with `docker network ls`
- The samples are using Windows as the host OS, they are using Linux containers (means that LCOW is used) and they use PowerShell as the shell
- If you are using native executables, ensure you have installed Visual Studio, git and the required .NET SDKs
- If you are using configurations with Docker containers. Install Docker and ensure that you have shared the drive where `<reporoot>` is located via the Docker Settings dialog


## Native configurations

### OPC Publisher publishing data from OPC PLC
- Clone the OPC Testserver repository and go to the root of this clone
- Change your working directory to `opcplc`
- Run `dotnet run iot-edge-opc-plc.csproj -- --autoaccept`

- Clone the OPC Publisher repository and go to the root of this clone
- Change your working directory to `opcpublisher`
- Run `dotnet run --project opcpublisher.csproj -- publisher --aa  --pf <reporoot>\publishednodes_localhost.json`

- Verify that data is sent to the IoTHub you configured by setting `_HUB_CS` using [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) or [iothub-explorer](https://github.com/Azure/iothub-explorer)

## Configurations with locally built docker containers

### OPC Publisher publishing data from OPC PLC
- Clone the OPC PLC repository and go to the root of this clone
- Run `docker build -t iot-edge-opc-plc .`
- Run `docker run -h opcplc --name opcplc --network iot_edge --expose 51210 -p 51210:51210 iot-edge-opc-plc --autoaccept`

- Clone the OPC Publisher repository and go to the root of this clone
- Run `docker build -t iot-edge-opc-publisher .`
- Run `docker run -h publisher --name publisher --network iot_edge -v <reporoot>:/appdata -e "_HUB_CS=$env:_HUB_CS" iot-edge-opc-publisher publisher --aa --pf /appdata/publishednodes_opcplc.json`

- Verify that data is sent to the IoTHub you configured by setting `_HUB_CS` using [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) or [iothub-explorer](https://github.com/Azure/iothub-explorer)

## Configurations with prebuilt docker containers

### OPC Publisher publishing data from OPC PLC
- Run `docker run -h opcplc --name opcplc --network iot_edge --expose 51210 -p 51210:51210 hansgschossmann/iot-edge-opc-plc --autoaccept`

- Run `docker run -h publisher --name publisher --network iot_edge -v <reporoot>:/appdata -e "_HUB_CS=$env:_HUB_CS" mcr.microsoft.com/iotedge/opc-publisher publisher --aa --pf /appdata/publishednodes_opcplc.json`

- Verify that data is sent to the IoTHub you configured by setting `_HUB_CS` using [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) or [iothub-explorer](https://github.com/Azure/iothub-explorer)

## Configurations using docker-compose

### A testbed for OPC Publisher
- Run `docker-compose -f testbed.yml up`

- Verify that data is sent to the IoTHub you configured by setting `_HUB_CS` using [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) or [iothub-explorer](https://github.com/Azure/iothub-explorer)
- OPC Testclient is going to use IoTHub direct method calls and OPC method calls to configure OPC Publisher to publish/unpublish nodes from OPC Testserver

### OPC Publisher publishing data from OPC PLC
- Update simple.yml and replace `<reporoot>` in the `volumes:` section with your actual fullqualified directory name
- Run `docker-compose -f simple.yml up`

### PLC for Connected Factory v1
- Update cfv1-simple.yml and replace `<reporoot>` in the `volumes:` section with your actual fullqualified directory name
- Run `docker-compose -f cfv1-simple.yml up`

## How to solve problems
- When starting a container you see the message:

      Error response from daemon: Conflict. The container name "/opcplc" is already in use by container "....". You have to remove (or rename) that container to be able to reuse that name.

  You need to run `docker stop opcplc` and then `docker rm opcplc`