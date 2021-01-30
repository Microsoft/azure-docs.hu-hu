---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 8f5a8aa2b949ddd07208cac054147596512f783f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214804"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../overview.md#try-the-speech-service-for-free)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md)
> * Hozzon létre egy olyan robotot, amely a [közvetlen vonalas beszéd csatornához](/azure/bot-service/bot-service-channel-connect-directlinespeech) csatlakozik
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez
>
  > [!NOTE]
  > Tekintse meg [a támogatott régiók listáját a hangsegédek számára](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

## <a name="setup-your-environment"></a>A környezet beállítása

A go. mod fájl frissítése a legújabb SDK-verzióra a sor hozzáadásával
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.15.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal
Cserélje le a forrásfájl tartalmát (például `quickstart.go` ) az alábbira, amely a következőket tartalmazza:

- "fő" csomag definíciója
- a szükséges modulok importálása a Speech SDK-ból
- a robot információinak tárolására szolgáló változók, amelyek a rövid útmutató későbbi részében lecserélve lesznek
- egyszerű implementáció a mikrofon használatával hangbemenethez
- eseménykezelők különböző eseményekhez, amelyek egy beszédfelismerési interakció során lépnek életbe

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Cserélje le a `YOUR_SUBSCRIPTION_KEY` és az `YOUR_BOT_REGION` értékeket a tényleges értékekre a beszédfelismerési erőforrásból.

- Navigáljon a Azure Portal, és nyissa meg a beszédfelismerési erőforrást
- A bal oldali **kulcsok és végpont** alatt két elérhető előfizetési kulcs található
    - `YOUR_SUBSCRIPTION_KEY`Érték helyett használja a értéket
- A bal oldali **Áttekintés** alatt jegyezze fel a régiót, és rendelje hozzá a régió-azonosítóhoz
    - Használja a régió azonosítóját `YOUR_BOT_REGION` érték helyett, például: `"westus"` **USA nyugati** régiója

   > [!NOTE]
   > Tekintse meg [a támogatott régiók listáját a hangsegédek számára](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) , és gondoskodjon arról, hogy az erőforrások az egyik régióban legyenek telepítve.

   > [!NOTE]
   > A robot konfigurálásával kapcsolatos információkért tekintse meg a [közvetlen vonalas beszéd csatorna](/azure/bot-service/bot-service-channel-connect-directlinespeech)robot Framework dokumentációját.

## <a name="code-explanation"></a>Kód magyarázata
A beszédfelismerési konfigurációs objektum létrehozásához a Speech előfizetési kulcs és a régió szükséges. A beszédfelismerési objektum létrehozásához a konfigurációs objektum szükséges.

A felismerő példány több módszert tesz elérhetővé a beszéd felismeréséhez. Ebben a példában a beszédet folyamatosan ismerjük. Ez a funkció lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy sok kifejezést küld az elismerésre, és ha a program leáll, a beszédfelismerés felismerése leáll. Az eredmények kiértékelése után a kód a konzolba írja azokat.

## <a name="build-and-run"></a>Buildelés és futtatás
Most már beállította a projekt felépítését és az egyéni hangsegéd tesztelését a Speech Service használatával.
1. Hozza létre a projektet, például: **"Go build"**
2. Futtassa a modult, és beszéljen egy mondattal vagy mondattal az eszköz mikrofonjában. A beszéd továbbítva lesz a közvetlen vonalas beszéd csatornára, és a kimenetként megjelenő szövegbe kerül.


> [!NOTE]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]