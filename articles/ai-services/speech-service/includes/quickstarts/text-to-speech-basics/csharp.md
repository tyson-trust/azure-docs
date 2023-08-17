---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/15/2022
ms.author: eur
---

[!INCLUDE [Header](../../common/csharp.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

## Set up the environment
The Speech SDK is available as a [NuGet package](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) and implements .NET Standard 2.0. You install the Speech SDK later in this guide, but first check the [SDK installation guide](../../../quickstarts/setup-platform.md?pivots=programming-language-csharp) for any more requirements. 

### Set environment variables

[!INCLUDE [Environment variables](../../common/environment-variables.md)]

## Synthesize to speaker output

Follow these steps to create a new console application and install the Speech SDK.

1. Open a command prompt where you want the new project, and create a console application with the .NET CLI. The `Program.cs` file should be created in the project directory.
    ```dotnetcli
    dotnet new console
    ```
1. Install the Speech SDK in your new project with the .NET CLI.
    ```dotnetcli
    dotnet add package Microsoft.CognitiveServices.Speech
    ```
1. Replace the contents of `Program.cs` with the following code. 
    
    ```csharp
    using System;
    using System.IO;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
        
    class Program 
    {
        // This example requires environment variables named "SPEECH_KEY" and "SPEECH_REGION"
        static string speechKey = Environment.GetEnvironmentVariable("SPEECH_KEY");
        static string speechRegion = Environment.GetEnvironmentVariable("SPEECH_REGION");

        static void OutputSpeechSynthesisResult(SpeechSynthesisResult speechSynthesisResult, string text)
        {
            switch (speechSynthesisResult.Reason)
            {
                case ResultReason.SynthesizingAudioCompleted:
                    Console.WriteLine($"Speech synthesized for text: [{text}]");
                    break;
                case ResultReason.Canceled:
                    var cancellation = SpeechSynthesisCancellationDetails.FromResult(speechSynthesisResult);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                        Console.WriteLine($"CANCELED: Did you set the speech resource key and region values?");
                    }
                    break;
                default:
                    break;
            }
        }
    
        async static Task Main(string[] args)
        {
            var speechConfig = SpeechConfig.FromSubscription(speechKey, speechRegion);      
    
            // The language of the voice that speaks.
            speechConfig.SpeechSynthesisVoiceName = "en-US-JennyNeural"; 
    
            using (var speechSynthesizer = new SpeechSynthesizer(speechConfig))
            {
                // Get text from the console and synthesize to the default speaker.
                Console.WriteLine("Enter some text that you want to speak >");
                string text = Console.ReadLine();
    
                var speechSynthesisResult = await speechSynthesizer.SpeakTextAsync(text);
                OutputSpeechSynthesisResult(speechSynthesisResult, text);
            }
    
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
    ```

1. To change the speech synthesis language, replace `en-US-JennyNeural` with another [supported voice](~/articles/ai-services/speech-service/language-support.md#prebuilt-neural-voices). All neural voices are multilingual and fluent in their own language and English. For example, if the input text in English is "I'm excited to try text to speech" and you set `es-ES-ElviraNeural`, the text is spoken in English with a Spanish accent. If the voice does not speak the language of the input text, the Speech service won't output synthesized audio.

[Build and run](/cpp/build/vscpp-step-2-build) your new console application to start speech synthesis to the default speaker.

```console
dotnet run
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

This quickstart uses the `SpeakTextAsync` operation to synthesize a short block of text that you enter. You can also get text from files as described in these guides:
- For information about speech synthesis from a file and finer control over voice styles, prosody, and other settings, see [How to synthesize speech](~/articles/ai-services/speech-service/how-to-speech-synthesis.md) and [Improve synthesis with Speech Synthesis Markup Language (SSML)](~/articles/ai-services/speech-service/speech-synthesis-markup.md).
- For information about synthesizing long-form text to speech, see [batch synthesis](~/articles/ai-services/speech-service/batch-synthesis.md). 

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]
