---
title: Service limits for Azure Communication Services
titleSuffix: An Azure Communication Services how-to document
description: Learn how to
author: tophpalmer
manager: shahen
services: azure-communication-services

ms.author: chpalm
ms.date: 03/31/2023
ms.topic: how-to
ms.service: azure-communication-services
ms.subservice: data
---
# Service limits for Azure Communication Services

This document explains the limitations of Azure Communication Services APIs and possible resolutions.

## Throttling patterns and architecture
When you hit service limitations, you will receive an HTTP status code 429 (Too many requests). In general, the following are best practices for handling throttling:

- Reduce the number of operations per request.
- Reduce the frequency of calls.
- Avoid immediate retries because all requests accrue against your usage limits.

You can find more general guidance on how to set up your service architecture to handle throttling and limitations in the [Azure Architecture](/azure/architecture) documentation for [throttling patterns](/azure/architecture/patterns/throttling). Throttling limits can be increased through [a request to Azure Support](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## Acquiring phone numbers
Before acquiring a phone number, make sure your subscription meets the [geographic and subscription](./telephony/plan-solution.md) requirements. Otherwise, you can't purchase a phone number. The below limitations apply to purchasing numbers through the [Phone Numbers SDK](./reference.md) and the [Azure portal](https://portal.azure.com/).

| Operation | Scope | Timeframe | Limit (number of requests) |
|---|--|--|--|
| Purchase phone number | Azure tenant | - | 1 |
| Search for phone numbers | Azure tenant | one week | 5 |

### Action to take

For more information, see the [phone number types](./telephony/plan-solution.md) concept page and the [telephony concept](./telephony/telephony-concept.md) overview page.

If you want to purchase more phone numbers or place a special order, follow the [instructions here](https://github.com/Azure/Communication/blob/master/special-order-numbers.md). If you would like to port toll-free phone numbers from external accounts to their Azure Communication Services account, follow the [instructions here](https://github.com/Azure/Communication/blob/master/port-numbers.md).

## Identity

| Operation | Timeframes (seconds) | Limit (number of requests) |
|---|--|--|
| **Create identity** | 30 | 1000|
| **Delete identity** | 30 | 500|
| **Issue access token** | 30 | 1000|
| **Revoke access token**  | 30 | 500|
| **createUserAndToken**| 30 | 1000 |
| **exchangeTokens**| 30 | 500 |

### Action to take
We recommend acquiring identities and tokens before creating chat threads or starting calls. For example, when the webpage loads or the application starts. 

For more information, see the [identity concept overview](./authentication.md) page.

## SMS
When sending or receiving a high volume of messages, you might receive a ```429``` error. This error indicates you're hitting the service limitations, and your messages will be queued to be sent once the number of requests is below the threshold.

Rate Limits for SMS:

|Operation|Scope|Timeframe (seconds)| Limit (number of requests) | Message units per minute|
|---------|-----|-------------|-------------------|-------------------------|
|Send Message|Per Number|60|200|200|

### Action to take
If you require to send a volume of messages that exceed the rate limits, email us at phone@microsoft.com.

For more information on the SMS SDK and service, see the [SMS SDK overview](./sms/sdk-features.md) page or the [SMS FAQ](./sms/sms-faq.md) page.

## Email
Sending a high volume of messages has a set of limitations on the number of email messages you can send. If you hit these limits, your messages won't be queued to be sent. You can submit these requests again, once the Retry-After time expires.

### Rate Limits 

|Operation|Scope|Timeframe (minutes)| Limit (number of emails) |
|---------|-----|-------------|-------------------|
|Send Email|Per Subscription|1|30|
|Send Email|Per Subscription|60|100|
|Get Email Status|Per Subscription|1|60|
|Get Email Status|Per Subscription|60|200|

### Size Limits

| **Name**         | Limit  |
|--|--|
|Number of recipients in Email|50 |
|Total email request size (including attachments) |10 MB |

### Action to take
This sandbox setup is designed to help developers begin building the application. Once the application is ready for production, you can gradually request to increase the sending volume. If you need to send more messages than the rate limits allow, submit a support request to raise your desired email sending limit. The reviewing team will consider your overall sender reputation, which includes factors such as your email delivery failure rates, your domain reputation, and reports of spam and abuse, when determining approval status.

## Chat

### Size Limits

| **Name**         | Limit  |
|--|--|
|Number of participants in thread|250 |
|Batch of participants - CreateThread|200 |
|Batch of participants - AddParticipant|200 |
|Page size - ListMessages|200 |

### Operation Limits

| **Operation** | **Bucketed by** | **Limit per 10 seconds** | **Limit per minute** |
|--|--|--|--|
|Create chat thread|User|10|-|
|Delete chat thread|User|10|-|
|Update chat thread|Chat thread|5|-|
|Add participants / remove participants|Chat thread|10|30|
|Get chat thread / List chat threads|User|50|-|
|Get chat message|User and chat thread|50|-|
|Get chat message|Chat thread|250|-|
|List chat messages|User and chat thread|50|200|
|List chat messages|Chat thread|250|400|
|Get read receipts|User and chat thread|5|-|
|Get read receipts|Chat thread|250|-|
|List chat thread participants|User and chat thread|10|-|
|List chat thread participants|Chat thread|250|-|
|Send message / update message / delete message|Chat thread|10|30|
|Send read receipt|User and chat thread|10|30|
|Send typing indicator|User and chat thread|5|15|
|Send typing indicator|Chat thread|10|30|

### Chat storage
Chat messages are stored for 90 days. Submit [a request to Azure Support](../../azure-portal/supportability/how-to-create-azure-support-request.md) if you require storage for longer time period. If the time period is less than 90 days for chat messages, use the delete chat thread APIs.

## Voice and video calling

### PSTN Call limitations

| **Name**         | Limit  |
|--|--|
|Number of outbound concurrent calls | 2 

### Call maximum limitations

| **Name**         | Limit  |
|--|--|
|Number of participants | 350 

### Calling SDK streaming support
The Communication Services Calling SDK supports the following streaming configurations:

| Limit                                                         | Web                         | Windows/Android/iOS        |
| ------------------------------------------------------------- | --------------------------- | -------------------------- |
| **Maximum # of outgoing local streams that you can send simultaneously**     | one video or one screen sharing | one video + one screen sharing |
| **Maximum # of incoming remote streams that you can render simultaneously** | four videos + one screen sharing | six videos + one screen sharing |

While the Calling SDK will not enforce these limits, your users may experience performance degradation if they're exceeded.

### Calling SDK timeouts

The following timeouts apply to the Communication Services Calling SDKs:

| Action                                                                      | Timeout in seconds |
| --------------------------------------------------------------------------- | ------------------ |
| Reconnect/removal participant                                               | 120                |
| Add or remove new modality from a call (Start/stop video or screen sharing) | 40                 |
| Call Transfer operation timeout                                             | 60                 |
| 1:1 call establishment timeout                                              | 85                 |
| Group call establishment timeout                                            | 85                 |
| PSTN call establishment timeout                                             | 115                |
| Promote 1:1 call to a group call timeout                                    | 115                |


### Action to take

For more information about the voice and video calling SDK and service, see the [calling SDK overview](./voice-video-calling/calling-sdk-features.md) page or [known issues](./known-issues.md).

## Teams Interoperability and Microsoft Graph
Using a Teams interoperability scenario, you'll likely use some Microsoft Graph APIs to create [meetings](/graph/cloud-communications-online-meetings).  

Each service offered through Microsoft Graph has different limitations; service-specific limits are [described here](/graph/throttling) in more detail.

### Action to take
When you implement error handling, use the HTTP error code 429 to detect throttling. The failed response includes the ```Retry-After``` response header. Backing off requests using the ```Retry-After``` delay is the fastest way to recover from throttling because Microsoft Graph continues to log resource usage while a client is being throttled.

You can find more information on Microsoft Graph [throttling](/graph/throttling) limits in the [Microsoft Graph](/graph/overview) documentation.

## Network Traversal

| Operation | Timeframes (seconds) | Limit (number of requests) |
|---|--|--|
| **Issue TURN Credentials** | 5 | 30000|
| **Issue Relay Configuration** | 5 | 30000|

### Action to take
We recommend acquiring tokens before starting other transactions, like creating a relay connection. 

For more information, see the [network traversal concept overview](./network-traversal.md) page.

## Next steps
See the [help and support](../support.md) options.
