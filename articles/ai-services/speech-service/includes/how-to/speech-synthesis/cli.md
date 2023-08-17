---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: eur
---

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

## Download and install

[!INCLUDE [SPX Setup](../../spx-setup-quick.md)]

## Synthesize speech to a speaker

Now you're ready to run the Speech CLI to synthesize speech from text. From the command line, change to the directory that contains the Speech CLI binary file. Then run the following command:

```bash
spx synthesize --text "I'm excited to try text to speech"
```

The Speech CLI will produce natural language in English through the computer speaker.

## Synthesize speech to a file

Run the following command to change the output from your speaker to a .wav file:

```bash
spx synthesize --text "I'm excited to try text to speech" --audio output greetings.wav
```

The Speech CLI will produce natural language in English in the `greetings.wav` audio file.

## Run and use a container

Speech containers provide websocket-based query endpoint APIs that are accessed through the Speech SDK and Speech CLI. By default, the Speech SDK and Speech CLI use the public Speech service. To use the container, you need to change the initialization method. Use a container host URL instead of key and region.

For more information about containers, see the [speech containers](../../../speech-container-howto.md#host-urls) how-to guide.

