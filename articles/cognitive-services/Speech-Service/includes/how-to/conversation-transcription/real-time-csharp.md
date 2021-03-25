---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 3985e2bb2058a033bcbbccde286ba3aa7aa77b96
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105756"
---
## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET-keretrendszer </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-voice-signatures"></a>Hangaláírások létrehozása

Első lépésként hozzon létre hangaláírásokat a beszélgetés résztvevőinek, hogy egyedi hangszórókként lehessen azonosítani őket. A `.wav` hangaláírások létrehozásához használt bemeneti hangfájlnak 16 bitesnek, 16 kHz-es mintavételi sebességnek és egy egycsatornás (monó) formátumúnak kell lennie. Az egyes hangminták javasolt hossza harminc másodperc és két perc között lehet. A `.wav` fájlnak egy **személy** hangjának kell lennie, hogy egyedi hangprofilt hozzon létre.

Az alábbi példa bemutatja, hogyan hozhat létre hangaláírást [a C# REST API használatával](https://aka.ms/cts/signaturegenservice) . Vegye figyelembe, hogy a `subscriptionKey` , a `region` és a minta fájl elérési útját kell helyettesítenie `.wav` .

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

A függvény futtatása a `GetVoiceSignatureString()` hangaláírási karakterláncot a megfelelő formátumban adja vissza. Futtassa kétszer a függvényt, hogy két karakterláncot használjon bemenetként a változóknál `voiceSignatureStringUser1` és az `voiceSignatureStringUser2` alábbiakban.

> [!NOTE]
> A hangaláírásokat **csak** a REST API használatával lehet létrehozni.

## <a name="transcribe-conversations"></a>Beszélgetések átirata

Az alábbi mintakód bemutatja, hogyan lehet valós időben átírni a beszélgetéseket két beszélő számára. Feltételezi, hogy már létrehozott egy hangaláírási karakterláncot az egyes hangszórókhoz a fent látható módon. Helyettesítse be a valódi információit a, a, a `subscriptionKey` `region` és az elérési utat az átírni `filepath` kívánt hanghoz.

Ez a mintakód a következő műveleteket végzi el:

* Létrehoz egy `AudioStreamReader` fájlt a mintából az átíráshoz `.wav` .
* Létrehoz egy-t a `Conversation` használatával `CreateConversationAsync()` .
* Létrehoz egy- `ConversationTranscriber` t a konstruktor használatával, és előfizet a szükséges eseményekre
* Résztvevőket vesz fel a beszélgetésbe. A karakterláncok `voiceSignatureStringUser1` és a `voiceSignatureStringUser2` függvényből származó lépések kimenetként kell származnia `GetVoiceSignatureString()` .
* Csatlakozik a beszélgetéshez, és elkezdi az átírást.

Hívja meg a függvényt a `TranscribeConversationsAsync()` beszélgetés átírásának megkezdéséhez.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Transcription;

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioStreamReader.OpenWavFile(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                conversationTranscriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
        }
    }
}
```