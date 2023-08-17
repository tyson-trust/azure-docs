---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/15/2022
ms.author: eur
---

[!INCLUDE [Header](../../common/python.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

## Set up the environment

The Speech SDK for Python is available as a [Python Package Index (PyPI) module](https://pypi.org/project/azure-cognitiveservices-speech/). The Speech SDK for Python is compatible with Windows, Linux, and macOS. 
- You must install the [Microsoft Visual C++ Redistributable for Visual Studio 2015, 2017, 2019, and 2022](/cpp/windows/latest-supported-vc-redist?view=msvc-170&preserve-view=true) for your platform. Installing this package for the first time might require a restart.
- On Linux, you must use the x64 target architecture.

Install a version of [Python from 3.7 or later](https://www.python.org/downloads/). First check the [SDK installation guide](../../../quickstarts/setup-platform.md?pivots=programming-language-python) for any more requirements 

### Set environment variables

[!INCLUDE [Environment variables](../../common/environment-variables.md)]

## Synthesize to speaker output

Follow these steps to create a new console application.

1. Open a command prompt where you want the new project, and create a new file named `speech_synthesis.py`.
1. Run this command to install the Speech SDK:  
    ```console
    pip install azure-cognitiveservices-speech
    ```
1. Copy the following code into `speech_synthesis.py`: 

    ```Python
    import os
    import azure.cognitiveservices.speech as speechsdk

    # This example requires environment variables named "SPEECH_KEY" and "SPEECH_REGION"
    speech_config = speechsdk.SpeechConfig(subscription=os.environ.get('SPEECH_KEY'), region=os.environ.get('SPEECH_REGION'))
    audio_config = speechsdk.audio.AudioOutputConfig(use_default_speaker=True)

    # The language of the voice that speaks.
    speech_config.speech_synthesis_voice_name='en-US-JennyNeural'

    speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

    # Get text from the console and synthesize to the default speaker.
    print("Enter some text that you want to speak >")
    text = input()

    speech_synthesis_result = speech_synthesizer.speak_text_async(text).get()

    if speech_synthesis_result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
        print("Speech synthesized for text [{}]".format(text))
    elif speech_synthesis_result.reason == speechsdk.ResultReason.Canceled:
        cancellation_details = speech_synthesis_result.cancellation_details
        print("Speech synthesis canceled: {}".format(cancellation_details.reason))
        if cancellation_details.reason == speechsdk.CancellationReason.Error:
            if cancellation_details.error_details:
                print("Error details: {}".format(cancellation_details.error_details))
                print("Did you set the speech resource key and region values?")
    ```

1. To change the speech synthesis language, replace `en-US-JennyNeural` with another [supported voice](~/articles/ai-services/speech-service/language-support.md#prebuilt-neural-voices). All neural voices are multilingual and fluent in their own language and English. For example, if the input text in English is "I'm excited to try text to speech" and you set `es-ES-ElviraNeural`, the text is spoken in English with a Spanish accent. If the voice does not speak the language of the input text, the Speech service won't output synthesized audio.

Run your new console application to start speech synthesis to the default speaker.

```console
python speech_synthesis.py
```

> [!IMPORTANT]
> Make sure that you set the `SPEECH_KEY` and `SPEECH_REGION` environment variables as described [above](#set-environment-variables). If you don't set these variables, the sample will fail with an error message.

Enter some text that you want to speak. For example, type "I'm excited to try text to speech." Press the Enter key to hear the synthesized speech. 

```console
Enter some text that you want to speak > 
I'm excited to try text to speech
```

## Remarks
Now that you've completed the quickstart, here are some additional considerations:

This quickstart uses the `speak_text_async` operation to synthesize a short block of text that you enter. You can also get text from files as described in these guides:
- For information about speech synthesis from a file and finer control over voice styles, prosody, and other settings, see [How to synthesize speech](~/articles/ai-services/speech-service/how-to-speech-synthesis.md) and [Improve synthesis with Speech Synthesis Markup Language (SSML)](~/articles/ai-services/speech-service/speech-synthesis-markup.md).
- For information about synthesizing long-form text to speech, see [batch synthesis](~/articles/ai-services/speech-service/batch-synthesis.md). 

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]
