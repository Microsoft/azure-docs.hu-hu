---
title: Bérlői modell létrehozása (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A biztonságos, kompatibilis bérlői modell (Custom Speech Microsoft 365 adataival) automatikus létrehozása, amely a Microsoft 365 adatait használja a szervezetre vonatkozó feltételek optimális beszédfelismeréséhez.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013717"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Oktatóanyag: bérlői modell létrehozása (előzetes verzió)

A bérlői modell (Custom Speech Microsoft 365 adatokkal) olyan Microsoft 365 vállalati ügyfelek számára érhető el, amelyek automatikusan létrehoznak egy egyéni beszédfelismerési modellt a szervezet Microsoft 365 adataiból. A modell a technikai feltételekre, a zsargonra és az emberek nevére van optimalizálva, mindezt biztonságos és megfelelő módon.

> [!IMPORTANT]
> Ha a szervezet a bérlői modell szolgáltatással regisztrálja magát, a beszédfelismerési szolgáltatás elérheti a szervezet nyelvi modelljét. A modell Microsoft 365 nyilvános csoportbeli e-mailekről és dokumentumokból jön létre, amelyeket bárki láthat a szervezetében. A szervezet rendszergazdája be-és kikapcsolhatja a szervezeti szintű nyelvi modell használatát a felügyeleti portálról.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Regisztrálás a bérlői modellben a Microsoft 365 felügyeleti központ használatával
> * Beszédfelismerési előfizetés kulcsának beolvasása
> * Bérlői modell létrehozása
> * Bérlői modell üzembe helyezése
> * A bérlői modell használata a Speech SDK-val

## <a name="enroll-in-the-tenant-model-service"></a>Regisztrálás a bérlői modell szolgáltatásban

A bérlői modell üzembe helyezése előtt regisztrálni kell a bérlői modell szolgáltatásban. A regisztráció a Microsoft 365 felügyeleti központban végezhető el, és csak a rendszergazda által végezhető el.

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com).

1. A bal oldali ablaktáblán válassza a **Beállítások** lehetőséget, majd a beágyazott menüben válassza a **Beállítások** lehetőséget, majd válassza az **Azure Speech Services** lehetőséget a főablakban.

   ![A "szolgáltatások & beépülő modulok" panel](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Jelölje be a **szervezeti szintű nyelvi modell engedélyezése** jelölőnégyzetet, majd kattintson a **módosítások mentése** gombra.

   ![Az Azure Speech Services ablaktábla](media/tenant-language-model/tenant-language-model-enrollment-2.png)

A bérlői modell példányának kikapcsolása:
1. Ismételje meg az előző 1. és 2. lépést.
1. Törölje a **szervezeti szintű nyelvi modell engedélyezése** jelölőnégyzet jelölését, majd kattintson a **módosítások mentése** gombra.

## <a name="get-a-speech-subscription-key"></a>Beszédfelismerési előfizetés kulcsának beolvasása

Ha a Speech SDK-val szeretné használni a bérlői modellt, szüksége lesz egy beszédfelismerési erőforrásra és a hozzá tartozó előfizetési kulcsra.

1. Jelentkezzen be az [Azure Portalra](https://aka.ms/azureportal).
1. Válassza az **Erőforrás létrehozása** lehetőséget.
1. A **keresőmezőbe** írja be a **Speech** kifejezést.
1. Az eredmények listájában válassza a **beszéd** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
1. Az erőforrás létrehozásához kövesse a képernyőn megjelenő utasításokat. Ügyeljen a következőkre:
   * A **hely** a **eastus** vagy a **westus** értékre van beállítva.
   * A **díjszabás** a **S0** értékre van állítva.
1. Kattintson a **Létrehozás** gombra.

   Néhány perc elteltével létrejön az erőforrás. Az előfizetési kulcs az erőforrás **Áttekintés** szakaszában érhető el.

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

Miután a rendszergazda engedélyezte a bérlői modellt a szervezet számára, létrehozhat egy nyelvi modellt, amely a Microsoft 365 adatain alapul.

1. Jelentkezzen be a [Speech studióba](https://speech.microsoft.com/).
1. A jobb felső sarokban válassza a **Beállítások** (fogaskerék ikon) lehetőséget, majd válassza ki a **bérlői modell beállításait**.

   ![A "bérlői modell beállításai" hivatkozás](media/tenant-language-model/tenant-language-settings.png)

   A Speech Studio megjelenít egy üzenetet, amelyből megtudhatja, hogy jogosult-e a bérlői modell létrehozására.

   > [!NOTE]
   > A Észak-Amerika vállalati ügyfelei jogosultak a bérlői modell (angol) létrehozására. Ha Ön Ügyfélszéf, az ügyfél kulcsa vagy az Office 365 Government-ügyfél, akkor ez a funkció nem érhető el. A következő témakörben találhatja meg, hogy Ügyfélszéf vagy az ügyfél kulcsának ügyfele-e:
   > * [Ügyfélszéf](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Ügyfél kulcsa](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365-kormány](https://www.microsoft.com/microsoft-365/government)

1. Válassza a **jóváhagyási** lehetőséget.

   Ha a bérlői modell elkészült, egy megerősítő e-mail-üzenetet kap további utasításokkal.

## <a name="deploy-your-tenant-model"></a>A bérlői modell üzembe helyezése

Ha a bérlői modell példánya elkészült, telepítse azt a következő módon:

1. A megerősítő e-mail-üzenetben kattintson a **modell megtekintése** gombra. Vagy jelentkezzen be a [Speech studióba](https://speech.microsoft.com/).
1. A jobb felső sarokban válassza a **Beállítások** (fogaskerék ikon) lehetőséget, majd válassza ki a **bérlői modell beállításait**.

   ![A "bérlői modell beállításai" hivatkozás](media/tenant-language-model/tenant-language-settings.png)

1. Válassza az **Üzembe helyezés** lehetőséget.

   A modell üzembe helyezése után az állapot *üzembe helyezésre* változik.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>A bérlői modell használata a Speech SDK-val

Most, hogy üzembe helyezte a modellt, használhatja azt a Speech SDK-val. Ebben a szakaszban mintakód használatával hívja meg a beszédfelismerési szolgáltatást Azure Active Directory (Azure AD) hitelesítés használatával.

Nézzük meg azt a kódot, amelyet a Speech SDK meghívásához használ a C# nyelven. Ebben a példában a beszédfelismerést a bérlői modell használatával hajtja végre. Ez az útmutató feltételezi, hogy a platform már be van állítva. Ha segítségre van szüksége a telepítéshez, tekintse meg a következőt: rövid útmutató [: beszédfelismerés felismerése, C# (.net Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

Másolja a projektbe a következő kódot:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Ezután újra kell építenie és futtatnia a projektet a parancssorból. A parancs futtatása előtt frissítsen néhány paramétert a következő módon:

1. `<Username>` `<Password>` Egy érvényes bérlői felhasználó értékeit cserélje le.
1. Cserélje le a szöveget `<Subscription-Key>` a beszédfelismerési erőforráshoz tartozó előfizetési kulcsra. Ez az érték a [Azure Portal](https://aka.ms/azureportal)beszédfelismerési erőforrásának **Áttekintés** szakaszában érhető el.
1. Cserélje le `<Endpoint-Uri>` a helyére a következő végpontot. Győződjön meg arról, hogy a lecserélte azt `{your region}` a régiót, ahol a beszédfelismerési erőforrás létrejött. A következő régiók támogatottak: `westus` , `westus2` és `eastus` . A régióval kapcsolatos információk a [Azure Portal](https://aka.ms/azureportal)beszédfelismerési erőforrásának **Áttekintés** szakaszában találhatók.
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Futtassa az alábbi parancsot:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Microsoft 365-adatelemzést egy egyéni beszédfelismerési modell létrehozásához, üzembe helyezéséhez és a Speech SDK-val való használathoz.

## <a name="next-steps"></a>További lépések

* [Speech Studio](https://speech.microsoft.com/)
* [Beszéd SDK](speech-sdk.md)