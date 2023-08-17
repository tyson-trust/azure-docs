---
title: Interoperability of Azure Communications Gateway with Microsoft Teams
description: Understand how Azure Communications Gateway fits into your existing fixed and mobile networks and into Microsoft Teams
author: rcdun
ms.author: rdunstan
ms.service: communications-gateway
ms.topic: conceptual
ms.date: 04/26/2023
ms.custom: template-concept
---

# Interoperability of Azure Communications Gateway with Microsoft Teams

Azure Communications Gateway sits at the edge of your network. This position allows it to manipulate signaling and media to meet the requirements of your networks and the Microsoft Phone System. Azure Communications Gateway includes many interoperability settings by default, and you can arrange further interoperability configuration.

## Role and position in the network

Azure Communications Gateway sits at the edge of your fixed line and mobile networks. It connects these networks to the Microsoft Phone System, allowing you to support Operator Connect (for fixed line networks) and Teams Phone Mobile (for mobile networks). The following diagram shows where Azure Communications Gateway sits in your network.

:::image type="complex" source="media/azure-communications-gateway-architecture.png" alt-text="Architecture diagram for Azure Communications Gateway connecting to fixed and mobile networks":::
    Architecture diagram showing Azure Communications Gateway connecting to the Microsoft Phone System, a softswitch in a fixed line deployment and a mobile IMS core. Azure Communications Gateway contains certified SBC function and the MCP application server for anchoring mobile calls.
:::image-end:::

Calls flow from endpoints in your networks through Azure Communications Gateway and the Microsoft Phone System into Microsoft Teams clients.

Azure Communications Gateway provides all the features of a traditional session border controller (SBC). These features include:

- Signaling interworking features to solve interoperability problems
- Advanced media manipulation and interworking
- Defending against Denial of Service attacks and other malicious traffic
- Ensuring Quality of Service

Azure Communications Gateway also offers metrics for monitoring your deployment.

You must provide the networking connection between Azure Communications Gateway and your core networks.

### Compliance with Certified SBC specifications

Azure Communications Gateway supports the Microsoft specifications for Certified SBCs for Operator Connect and Teams Phone Mobile. For more information about certification and these specifications, see [Session Border Controllers certified for Direct Routing](/microsoftteams/direct-routing-border-controllers) and the Operator Connect or Teams Phone Mobile documentation provided by your Microsoft representative.

### Call control integration for Teams Phone Mobile

[Teams Phone Mobile](/microsoftteams/operator-connect-mobile-plan) allows you to offer Microsoft Teams call services for calls made from the native dialer on mobile handsets, for example presence and call history. These features require anchoring the calls in Microsoft's Intelligent Conversation and Communications Cloud (IC3), part of the Microsoft Phone System.

The Microsoft Phone System relies on information in SIP signaling to determine whether a call is:

- To a Teams Phone Mobile subscriber.
- From a Teams Phone Mobile subscriber or between two Teams Phone Mobile subscribers.

Your core mobile network must supply this information to Azure Communications Gateway, by using unique trunks or by correctly populating an `X-MS-FMC` header as defined by the Teams Phone Mobile SIP specifications. If you don't have access to these specifications, contact your Microsoft representative or your onboarding team.

Your core mobile network must also be able to anchor and divert calls into the Microsoft Phone System. You can choose from the following options.

- Using Mobile Control Point (MCP) in Azure Communications Gateway. MCP is an IMS Application Server that queries the Teams Phone Mobile Consultation API to determine whether the call involves a Teams Phone Mobile Subscriber. MCP then adds X-MS-FMC headers and updates the signaling to divert the call into the Microsoft Phone System through Azure Communications Gateway. For more information, see [Mobile Control Point in Azure Communications Gateway for Teams Phone Mobile](mobile-control-point.md).
- Deploying an on-premises version of Mobile Control Point (MCP) from Metaswitch. For more information, see the [Metaswitch description of Mobile Control Point](https://www.metaswitch.com/products/mobile-control-point). This version of MCP isn't included in Azure Communications Gateway.
- Using other routing capabilities in your core network to detect Teams Phone Mobile subscribers and route INVITEs to or from these subscribers into the Microsoft Phone System through Azure Communications Gateway.

> [!IMPORTANT]
> If an INVITE has an X-MS-FMC header, the core must not route the call to Microsoft Teams. The call has already been anchored in the Microsoft Phone System.

## SIP signaling

Azure Communications Gateway includes SIP trunks to your own network and can interwork between your existing core networks and the requirements of the Microsoft Phone System. For example, Azure Communications Gateway automatically interworks calls to support the following requirements from Operator Connect and Teams Phone Mobile:

- SIP over TLS
- X-MS-SBC header (describing the SBC function)
- Strict rules on a= attribute lines in SDP bodies
- Strict rules on call transfer handling

SIP trunks between your network and Azure Communications Gateway are multi-tenant, meaning that traffic from all your customers share the same trunk. By default, traffic sent from the Azure Communications Gateway contains an X-MSTenantID header. This header identifies the enterprise that is sending the traffic and can be used by your billing systems.

You can arrange more interworking function as part of your initial network design or at any time by raising a support request for Azure Communications Gateway. For example, you might need extra interworking configuration for:

- Advanced SIP header or SDP message manipulation
- Support for reliable provisional messages (100rel)
- Interworking between early and late media
- Interworking away from inband DTMF tones
- Placing the unique tenant ID elsewhere in SIP messages to make it easier for your network to consume, for example in `tgrp` parameters

## RTP and SRTP media

The Microsoft Phone System typically requires SRTP for media. Azure Communications Gateway supports both RTP and SRTP, and can interwork between them. Azure Communications Gateway offers further media manipulation features to allow your networks to interoperate with the Microsoft Phone System.

### Media handling for calls

You must select the codecs that you want to support when you deploy Azure Communications Gateway. If the Microsoft Phone System doesn't support these codecs, Azure Communications Gateway can perform transcoding (converting between codecs) on your behalf.

Operator Connect and Teams Phone Mobile require core networks to support ringback tones (ringing tones) during call transfer. Core networks must also support comfort noise. If your core networks can't meet these requirements, Azure Communications Gateway can inject media into calls.

### Media interworking options

Azure Communications Gateway offers multiple media interworking options. For example, you might need to:

- Change handling of RTCP
- Control bandwidth allocation
- Prioritize specific media traffic for Quality of Service

For full details of the media interworking features available in Azure Communications Gateway, raise a support request.

## Compatibility with monitoring requirements

The Azure Communications Gateway service includes continuous monitoring for potential faults in your deployment. The metrics we monitor cover all metrics that operators must monitor as part of the Operator Connect program and include:

- Call quality
- Call errors and unusual behavior (for example, call setup failures, short calls, or unusual disconnections)
- Other errors in Azure Communications Gateway

We'll investigate the potential fault, and determine whether the fault relates to Azure Communications Gateway or the Microsoft Phone System. We may require you to carry out some troubleshooting steps in your networks to help isolate the fault.

Azure Communications Gateway provides metrics that you can use to monitor the overall health of your Azure Communications Gateway deployment. If you notice any concerning metrics, you can raise an Azure Communications Gateway support ticket.

## Next steps

- Learn about [monitoring Azure Communications Gateway](monitor-azure-communications-gateway.md).
- Learn about [requesting changes to Azure Communications Gateway](request-changes.md).
