---
title: 'Quickstart: Publish and subscribe on an MQTT topic using CLI'
description: 'Quickstart guide to use Azure Event Grid MQTT and Azure CLI to publish and subscribe MQTT messages on a topic'
ms.topic: quickstart
ms.custom: build-2023, devx-track-azurecli
ms.date: 05/23/2023
author: veyaddan
ms.author: veyaddan
---

# Quickstart: Publish and subscribe to MQTT messages on Event Grid Namespace with Azure CLI (Preview)

Azure Event Grid supports messaging using the MQTT protocol.  Clients (both devices and cloud applications) can publish and subscribe MQTT messages over flexible hierarchical topics for scenarios such as high scale broadcast, and command & control.

[!INCLUDE [mqtt-preview-note](./includes/mqtt-preview-note.md)]

In this article, you use the Azure CLI to do the following tasks:
1. Create an Event Grid namespace and enable MQTT
2. Create subresources such as clients, client groups, and topic spaces
3. Grant clients access to publish and subscribe to topic spaces
4. Publish and receive MQTT messages

## Prerequisites
- If you don't have an [Azure subscription](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), create an [Azure free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.
- If you're new to Azure Event Grid, read through [Event Grid overview](/azure/event-grid/overview) before starting this tutorial.
- Register the Event Grid resource provider as per [Register the Event Grid resource provider](/azure/event-grid/custom-event-quickstart-portal#register-the-event-grid-resource-provider).
- Make sure that port 8883 is open in your firewall. The sample in this tutorial uses MQTT protocol, which communicates over port 8883. This port may be blocked in some corporate and educational network environments.
- Use the Bash environment in [Azure Cloud Shell](/azure/cloud-shell/overview). For more information, see [Quickstart for Bash in Azure Cloud Shell](/azure/cloud-shell/quickstart).
- If you prefer to run CLI reference commands locally, [install](/cli/azure/install-azure-cli) the Azure CLI. If you're running on Windows or macOS, consider running Azure CLI in a Docker container. For more information, see [How to run the Azure CLI in a Docker container](/cli/azure/run-azure-cli-docker).
- If you're using a local installation, sign in to the Azure CLI by using the [az login](/cli/azure/reference-index#az-login) command. To finish the authentication process, follow the steps displayed in your terminal. For other sign-in options, see [Sign in with the Azure CLI](/cli/azure/authenticate-azure-cli).
- When you're prompted, install the Azure CLI extension on first use. For more information about extensions, see [Use extensions with the Azure CLI](/cli/azure/azure-cli-extensions-overview).
- Run [az version](/cli/azure/reference-index?#az-version) to find the version and dependent libraries that are installed. To upgrade to the latest version, run [az upgrade](/cli/azure/reference-index?#az-upgrade).
- This article requires version 2.17.1 or later of the Azure CLI. If using Azure Cloud Shell, the latest version is already installed.
- You need an X.509 client certificate to generate the thumbprint and authenticate the client connection.

## Generate sample client certificate and thumbprint
If you don't already have a certificate, you can create a sample certificate using the [step CLI](https://smallstep.com/docs/step-cli/installation/). Consider installing manually for Windows. 

After a successful installation of Step, you should open a command prompt in your user profile folder (Win+R type %USERPROFILE%).

1. To create root and intermediate certificates, run the following command.  Remember the password, which needs to be used in the next step.

```powershell
step ca init --deployment-type standalone --name MqttAppSamplesCA --dns localhost --address 127.0.0.1:443 --provisioner MqttAppSamplesCAProvisioner
```

2. Using the CA files generated to create certificate for the client.  Ensure to use the correct path for the cert and secrets files in the command.

```powershell
step certificate create client1-authnID client1-authnID.pem client1-authnID.key --ca .step/certs/intermediate_ca.crt --ca-key .step/secrets/intermediate_ca_key --no-password --insecure --not-after 2400h
```

3. To view the thumbprint, run the Step command.

```powershell
step certificate fingerprint client1-authnID.pem
```

> [!IMPORTANT]
> The Azure [CLI Event Grid extension](/cli/azure/eventgrid) does not yet support namespaces and any of the resources it contains. We will use [Azure CLI resource](/cli/azure/resource) to create Event Grid resources.

## Create a Namespace

Save the Namespace object in namespace.json file in resources folder.

```json
{
    "properties": {
        "inputSchema": "CloudEventSchemaV1_0",
        "topicSpacesConfiguration": {
            "state": "Enabled"
        },
        "isZoneRedundant": true
    },
    "location": "{Add region name}"
}
```

Use the az resource command to create a namespace.  Update the command with your subscription ID, Resource group ID, and a Namespace name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces --id /subscriptions/{Subscription ID}/resourceGroups/{Resource Group}/providers/Microsoft.EventGrid/namespaces/{Namespace Name} --is-full-object --api-version 2023-06-01-preview --properties @./resources/namespace.json
```

> [!NOTE]
> To keep the QuickStart simple, you'll create a namespace with minimal properties.  For detailed steps about configuring network, security, and other settings on other pages of the wizard, see [create and manage namespaces](create-view-manage-namespaces.md).

## Create clients

Store the client object in client1.json file.  Update the allowedThumbprints field with valid value(s).

```json
{
    "state": "Enabled",
    "authenticationName": "client1-authnID", 
    "clientCertificateAuthentication": {
        "validationScheme": "ThumbprintMatch",
        "allowedThumbprints": [
            "{Your client 1 certificate thumbprint}"
        ]
    }
}
```

Use the az resource command to create the first client.  Update the command with your subscription ID, Resource group ID, and a Namespace name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/clients --id /subscriptions/{Subscription ID}/resourceGroups/{Resource Group}/providers/Microsoft.EventGrid/namespaces/{Namespace Name}/clients/{Client Name} --api-version 2023-06-01-preview --properties @./resources/client1.json
```

> [!NOTE]
>  - To keep the QuickStart simple, you'll be using Thumbprint match for authentication.  For detailed steps on using X.509 CA certificate chain for client authentication, see [client authentication using certificate chain](./mqtt-certificate-chain-client-authentication.md).
> - Also for this exercise, we use the default $all client group, which includes all the clients in the namespace.  To learn more about creating custom client groups using client attributes, see [client groups](mqtt-client-groups.md) document.

## Create topic spaces

Store the below object in topicspace.json file.

```json
{ 
    "topicTemplates": [
        "contosotopics/topic1"
    ]
}
```

Use the az resource command to create the topic space.  Update the command with your subscription ID, Resource group ID, namespace name, and a topic space name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/topicSpaces --id /subscriptions/{Subscription ID}/resourceGroups/{Resource Group}/providers/Microsoft.EventGrid/namespaces/{Namespace Name}/topicSpaces/{Topic Space Name} --api-version 2023-06-01-preview --properties @./resources/topicspace.json
```

## Create PermissionBindings

Store the first permission binding object in permissionbinding1.json file.  Replace the topic space name with your topic space name.  This permission binding is for publisher.

```json
{
    "clientGroupName": "$all",
    "permission": "Publisher",
    "topicSpaceName": "{Your topicspace name}"
}
```

Use the az resource command to create the first permission binding.  Update the command with your subscription ID, Resource group ID, namespace name, and a permission binding name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/permissionBindings --id /subscriptions/{Subscription ID}/resourceGroups/{Resource Group}/providers/Microsoft.EventGrid/namespaces/{Namespace Name}/permissionBindings/{Permission Binding Name} --api-version 2023-06-01-preview --properties @./resources/permissionbinding1.json
```

Store the second permission binding object in permissionbinding2.json file.  Replace the topic space name with your topic space name.  This permission binding is for subscriber.

```json
{
    "clientGroupName": "$all",
    "permission": "Subscriber",
    "topicSpaceName": "{Your topicspace name}"
}
```

Use the az resource command to create the second permission binding.  Update the command with your subscription ID, Resource group ID, namespace name and a permission binding name.

```azurecli-interactive
az resource create --resource-type Microsoft.EventGrid/namespaces/permissionBindings --id /subscriptions/{Subscription ID}/resourceGroups/{Resource Group}/providers/Microsoft.EventGrid/namespaces/{Namespace Name}/permissionBindings/{Permission Binding Name} --api-version 2023-06-01-preview --properties @./resources/permissionbinding2.json
```

## Publish and subscribe MQTT messages

The following sample code is a simple .NET publisher that attempts to connect, and publish to a namespace, and subscribes to the MQTT topic.  You can use the code to modify per your requirement and run the code in Visual Studio or any of your favorite tools.  

You need to install the MQTTnet package (version 4.1.4.563) from NuGet to run this code.  
(In Visual Studio, right click on the project name in Solution Explorer, go to Manage NuGet packages, search for MQTTnet.  Select MQTTnet package and install.)

> [!NOTE]
>The following sample code is only for demonstration purposes and is not intended for production use.

**Sample C# code to connect a client, publish/subscribe MQTT message on a topic**

> [!IMPORTANT]
> Please update the client certificate and key pem file paths depending on location of your client certificate files.  Also, ensure the client authentication name, topic information match with your configuration.

```csharp
using MQTTnet.Client;
using MQTTnet;
using System.Security.Cryptography.X509Certificates;

string hostname = "{Your Event Grid namespace MQTT hostname}";
string clientId = "client1-session1";  //client ID can be the session identifier.  A client can have multiple sessions using username and clientId.
string x509_pem = @" client certificate cer.pem file path\client.cer.pem";  //Provide your client certificate .cer.pem file path
string x509_key = @"client certificate key.pem file path\client.key.pem";  //Provide your client certificate .key.pem file path

var certificate = new X509Certificate2(X509Certificate2.CreateFromPemFile(x509_pem, x509_key).Export(X509ContentType.Pkcs12));

var mqttClient = new MqttFactory().CreateMqttClient();

var connAck = await mqttClient!.ConnectAsync(new MqttClientOptionsBuilder()
    .WithTcpServer(hostname, 8883)
    .WithClientId(clientId)
    .WithCredentials("client1-authnID", "")  //use client authentication name in the username
    .WithTls(new MqttClientOptionsBuilderTlsParameters()
    {
        UseTls = true,
        Certificates = new X509Certificate2Collection(certificate)
    })

    .Build());

Console.WriteLine($"Client Connected: {mqttClient.IsConnected} with CONNACK: {connAck.ResultCode}");

mqttClient.ApplicationMessageReceivedAsync += async m => await Console.Out.WriteAsync($"Received message on topic: '{m.ApplicationMessage.Topic}' with content: '{m.ApplicationMessage.ConvertPayloadToString()}'\n\n");

var suback = await mqttClient.SubscribeAsync("contosotopics/topic1");
suback.Items.ToList().ForEach(s => Console.WriteLine($"subscribed to '{s.TopicFilter.Topic}' with '{s.ResultCode}'"));

while (true)
{
    var puback = await mqttClient.PublishStringAsync("contosotopics/topic1", "hello world!");
    Console.WriteLine(puback.ReasonString);
    await Task.Delay(1000);
}

```

You can replicate and modify the same code for multiple clients to perform publish / subscribe among the clients.

## Next steps
- [Route MQTT messages to Event Hubs](mqtt-routing-to-event-hubs-cli.md)
- For code samples, go to [this repository.](https://github.com/Azure-Samples/MqttApplicationSamples/tree/main)
