---
title: 'Overview of MQTT Support in Azure Event Grid (preview)'
description: 'Describes the main concepts for the MQTT Support in Azure Event Grid.'
ms.topic: conceptual
ms.date: 05/23/2023
author: george-guirguis
ms.author: geguirgu
---
# Overview of the MQTT Support in Azure Event Grid (Preview)

Azure Event Grid enables your MQTT clients to communicate with each other and with Azure services, to support your Internet of Things (IoT) solutions. 

[!INCLUDE [mqtt-preview-note](./includes/mqtt-preview-note.md)]

Event Grid’s MQTT support enables you to accomplish the following scenarios:

- Ingest telemetry using a many-to-one messaging pattern. This pattern enables the application to offload the burden of managing the high number of connections with devices to Event Grid.
- Control your MQTT clients using the request-response (one-to-one) messaging pattern. This pattern enables any client to communicate with any other client without restrictions, regardless of the clients' roles.
- Broadcast alerts to a fleet of clients using the one-to-many messaging pattern. This pattern enables the application to publish only one message that the service replicates for every interested client.
- Integrate data from your MQTT clients by routing MQTT messages to Azure services and Webhooks through the HTTP Push delivery functionality. This integration with Azure services enables you to build data pipelines that start with data ingestion from your IoT devices.

You can find code samples that demonstrate these scenarios in [this repository.](https://github.com/Azure-Samples/MqttApplicationSamples)

The MQTT support in Event Grid is ideal for the implementation of automotive and mobility scenarios, among others. See [the reference architecture](mqtt-automotive-connectivity-and-data-solution.md) to learn how to build secure and scalable solutions for connecting millions of vehicles to the cloud, using Azure’s messaging and data analytics services.

:::image type="content" source="media/overview/mqtt-messaging-high-res.png" alt-text="High-level diagram of Event Grid that shows bidirectional MQTT communication with publisher and subscriber clients." border="false":::



## Key concepts:
The following are a list of key concepts involved in MQTT messaging on Event Grid.

### MQTT

MQTT is a publish-subscribe messaging transport protocol that was designed for constrained environments. It has become the go-to communication standard for IoT scenarios due to efficiency, scalability, and reliability. Event Grid enables clients to publish and subscribe to messages over MQTT v3.1.1, MQTT v3.1.1 over WebSockets, MQTT v5, and MQTT v5 over WebSockets protocols. The following list shows some of the feature highlights of Event Grid's MQTT support:
- MQTT v5 features: 
	- **User properties** allow you to add custom key-value pairs in the message header to provide more context about the message. For example, include the purpose or origin of the message so the receiver can handle the message efficiently.
	- **Request-response pattern** enables your clients to take advantage of the standard request-response asynchronous pattern, specifying the response topic and correlation ID in the request for the client to respond without prior configuration. 
	- **Message expiry interval** allows you to declare to Event Grid when to disregard a message that is no longer relevant or valid. For example, disregard stale commands or alerts. 
	- **Topic aliases** helps your clients reduce the size of the topic field, making the data transfer less expensive.
	- **Maximum message size** allows your clients to control the maximum message size that they can handle from the server.
	- **Receive Maximum** allows your clients to control the message rate depending on their capabilities such as processing speed or storage capabilities.
	- **Clean start and session expiry** enable your clients to optimize the reliability and security of the session by preserving the client's subscription information and messages for a configurable time interval.
	- **Negative acknowledgments** allow your clients to efficiently react to different error codes.
	- **Server-sent disconnect packets** allow your clients to efficiently handle disconnects.
- Event Grid is adding more MQTT v5 features in the future to align more with the MQTT specifications. The following items detail the current differences in Event Grid's MQTT support from the MQTT specifications: Will message, Retain flag, Message ordering and QoS 2 aren't supported. 

- MQTT v3.1.1 features: 
	- **Persistent sessions** ensure reliability by preserving the client's subscription information and messages when a client disconnects.
	- **QoS 0 and 1** provide your clients with control over the efficiency and reliability of the communication.
-  Event Grid is adding more MQTT v3.1.1 features in the future to align more with the MQTT specifications. The following items detail the current differences in Event Grid's MQTT support from the MQTT v3.1.1 specification: Will message, Retain flag, Message ordering and QoS 2 aren't supported.
 
[Learn more about Event Grid’s MQTT support and current limitations.](mqtt-support.md) 

### Publish-Subscribe messaging model

The publish-subscribe messaging model provides a scalable and asynchronous communication to clients. It enables clients to offload the burden of handling a high number of connections and messages to the service. Through the Publish-Subscribe messaging model, your clients can communicate efficiently using one-to-many, many-to-one, and one-to-one messaging patterns. 
- The one-to-many messaging pattern enables clients to publish only one message that the service replicates for every interested client. 
- The many-to-one messaging pattern enables clients to offload the burden of managing the high number of connections to Event Grid. 
- The one-to-one messaging pattern enables any client to communicate with any other client without restrictions, regardless of the clients' roles.

### Namespace

Event Grid Namespace is a management container for the resources supporting the MQTT broker functionality, along with the resources supporting the [pull delivery functionality](pull-delivery-overview.md). Your MQTT client can connect to Event Grid and publish/subscribe to messages, while Event Grid authenticates your clients, authorizes publish/subscribe requests, and forwards messages to interested clients. Learn more about [the namespace concept.](mqtt-event-grid-namespace-terminology.md)

### Clients

Clients refer to IoT devices or applications that publish and subscribe to MQTT messages. 

IoT devices are physical objects that are connected to the internet to transmit telemetry and receive commands. These devices can be sensors, appliances, machines, or other objects equipped with embedded sensors and software. The sensors and software enable them to communicate and interact with each other and the environment around them. The value of IoT devices lies in their ability to provide real-time data and insights, enabling businesses and individuals to make informed decisions and improve efficiency and productivity.

IoT applications are software designed to interact with and process data from IoT devices. They typically include components such as data collection, processing, storage, visualization, and analytics. These applications enable users to monitor and control connected devices, automate tasks, and gain insights from the data generated by IoT devices.

### Client authentication

Event Grid has a client registry that stores information about the clients permitted to connect to it. Before a client can connect, there must be an entry for that client in the client registry. As a client connects to Event Grid, it needs to authenticate with Event Grid based on credentials stored in the identity registry. Event Grid supports X.509 certificate authentication that is the industry standard in IoT scenarios.[Learn more about MQTT client authentication.](mqtt-client-authentication.md) 

### Access control

Access control is critical for IoT scenarios considering the enormous scale of IoT environments and the unique security challenges of constrained devices. Event Grid delivers Role-Based Access Control (RBAC) through a flexible access control model that enables you to manage the authorization of clients to publish or subscribe to topics.

Given the enormous scale of IoT environments, assigning permission for each client to each topic is incredibly tedious. Event Grid’s flexible access control tackles this scale challenge through grouping clients and topics into client groups and topic spaces. After creating client groups and topic spaces, you’re able to configure a permission binding to grant access to a client group to either publish or subscribe to a topic space.

:::image type="content" source="media/mqtt-overview/access-control-high-res.png" alt-text="Diagram of the access control model." border="false":::

Topic spaces also provide granular access control by allowing you to control the authorization of each client within a client group to publish or subscribe to its own topic. This granular access control is achieved by using variables in topic templates. [Learn more about access control.](mqtt-access-control.md) 

### Routing

Event Grid allows you to route your MQTT messages to Azure services or webhooks for further processing. Accordingly, you can build end-to-end solutions by using your IoT data for data analysis, storage, and visualizations, among other use cases. The routing configuration enables you to send all your messages from your clients to an [Event Grid custom topic](custom-topics.md), and configuring [Event Grid event subscriptions](subscribe-through-portal.md) to route the messages from that Event Grid topic to the [supported event handlers](event-handlers.md). For example, this functionality enables you to use Event Grid to route telemetry from your IoT devices to Event Hubs and then to Azure Stream Analytics to gain insights from your device telemetry. [Learn more about routing.](mqtt-routing.md)

:::image type="content" source="media/mqtt-overview/routing-high-res.png" alt-text="Diagram of the MQTT message routing." border="false":::

### MQTT Clients Life Cycle Events 

Client Life Cycle events allow applications to react to events about the client connection status or the client resource operations. It allows you to keep track of your client's connection status, react with a mitigation action for client disconnections, and track the namespace that your clients are attached to during automated failovers.Learn more about [MQTT Client Life Cycle Events](mqtt-client-life-cycle-events.md).

## Next steps

Use the following articles to learn more about the MQTT support in Event Grid and its main concepts.

### Quick Start

- [Publish and subscribe to MQTT messages](mqtt-publish-and-subscribe-portal.md)
- [Route MQTT messages to Event Hubs](mqtt-routing-to-event-hubs-portal.md)

### Concepts

- [Terminology](mqtt-event-grid-namespace-terminology.md)
- [Client authentication](mqtt-client-authentication.md) 
- [Access control](mqtt-access-control.md) 
- [MQTT support](mqtt-support.md) 
- [Routing MQTT messages](mqtt-routing.md) 
- [MQTT Client Life Cycle Events](mqtt-client-life-cycle-events.md).