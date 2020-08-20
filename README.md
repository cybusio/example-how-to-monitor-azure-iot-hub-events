# Cybus Learn - Azure IoT

This repository describes how to monitor device events in Azure IoT Hub.

The purpose is to give Cybus Connectware users a basic guideline 
how to verify the proper integration of the Cybus Azure IoT container.

This is especially interesting for existing Azure IoT setups with 
well-defined Device Twins and complex postprocessing on incoming events:
if messages cannot be seen on some routing endpoint (e.g. a Cosmos DB)
this guide may be useful for debugging the processing chain for events.

# Prerequisites

Besides the Cybus Connectware, an Azure IoT Connector service and some
event source you need the following to see raw events on Azure IoT:

- An Azure account and the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- An Azure IoT Hub with an IoT Device

## Use the Azure CLI IoT extension and monitor events

- Install the Azure IoT extension: 

```bash
az extension add --name azure-iot
```

- Generate an access token to the IoT hub 
```bash
az iot hub generate-sas-token --duration 3600 -n <iot-hub-name>
```

- Monitor the raw events
```bash
az iot hub monitor-events --hub-name <iot-hub-name>
```

The `monitor-events` directive may require a dependency update for `uamqp`,
so follow the dialog for this update before watching the events.

If you want to reduce the view onto a single device in this hub, use
a slightly different command line:

```bash
az iot hub monitor-events --hub-name <iot-hub-name> --device-id <device-id>
```

## Configure the Connectware with a Azure IoT Connector service

- Get a [Cybus Connectware](https://www.cybus.io/) (Version 1.0.8 higher) and some license key.

- Prepare a service comissioning file: 
see the [Azure Iot Service Example](azure-iot-connectware-service.yml)

- Set the device connection string in the format: `HostName=<your-iothub-name>.azure-devices.net;DeviceId=<your-device-id>;SharedAccessKey=<access-key-for-the-device>`

- Subscribe to a MQTT topic as the event source for the connector:
In the example we choose a Cybus::Mapping with the target topic `test/AzureIoT`

- Publish to the MQTT topic with some payload from your device (or a node-red flow):
see the [Node-Red flow](node-red-flow-test-event.json) for use with the Connectware workbench.
In the example we choose a source topic `test/deviceEvent` to demonstrate a transformation
rules within the Cybus::Mapping resource.


- Using this the console output of the above mentioned `monitor-events` command shows continuously something like this:
```
{
    "event": {
        "origin": "TestDevice",
        "module": "",
        "interface": "",
        "component": "",
        "payload": "{\"deviceId\":\"TestDevice\",\"payload\":{\"Temperature\":78.567,\"Position\":{\"X\":35.02,\"Y\":12.62,\"Z\":3.45},\"Status\":\"operational\"}}"
    }
}
```

# References

- [Install Microsoft Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Microsoft Azure IoT Homepage](https://azure.microsoft.com//services/iot-hub/)
- [Cybus Homepage](https://www.cybus.io/)
- [Cybus Connectware documentation](https://docs.cybus.io)
