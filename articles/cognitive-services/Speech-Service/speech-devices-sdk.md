---
title: Speech Devices SDK – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerkedés a Speech Devices SDK-val. A beszédfelismerési szolgáltatás számos különböző eszközzel és hangforrással működik. A Speech Devices SDK egy előre beállított könyvtár, amely a célnak megfelelő, a mikrofon tömbje.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015273"
---
# <a name="what-is-the-speech-devices-sdk"></a>Mi a Speech Devices SDK?

A [beszédfelismerési szolgáltatás](overview.md) számos különböző eszközzel és hangforrással működik. Most a következő szintre állíthatja a beszédfelismerési alkalmazásokat a megfelelő hardverekkel és szoftverekkel. A Speech Devices SDK egy előre beállított könyvtár, amely a célnak megfelelő, a mikrofon tömbje.

A Speech Devices SDK a következőket nyújtja:

- Gyorsan tesztelheti az új hang-forgatókönyveket.
- Könnyebben integrálhatja a felhőalapú beszédfelismerési szolgáltatást az eszközre.
- Kivételes felhasználói élményt hozhat létre ügyfelei számára.

A Speech Devices SDK a [SPEECH SDK](speech-sdk.md)-t használja. Speciális hangfeldolgozási algoritmusok használatával az eszköz mikrofon-tömbje segítségével elküldheti a hanganyagot a [beszédfelismerési szolgáltatásnak](overview.md). A szolgáltatás precízen segíti a széles körű [beszédfelismerést](speech-to-text.md) a zajcsökkentés, az ECHO-lemondás, a beamforming és a visszhang használatával.

A Speech Devices SDK használatával olyan környezeti eszközöket is létrehozhat, amelyek saját [testreszabott kulcsszóval](./custom-keyword-basics.md)rendelkeznek. Az egyéni kulcsszavak egy olyan végszót biztosítanak, amely a márka egyedi felhasználói beavatkozását indítja el.

A Speech Devices SDK többféle hang-kompatibilis forgatókönyvet tesz lehetővé, például a [hangsegédeket](./voice-assistants.md), a meghajtó-átrendezési rendszereket, a [társalgási átírást](./conversation-transcription.md)és az intelligens hangszórókat. Válaszolhat a felhasználókra szöveggel, visszaállíthatja őket egy alapértelmezett vagy [Egyéni hangon](./how-to-custom-voice-create-voice.md), megadhatja a keresési eredményeket, [lefordíthat](speech-translation.md) más nyelvekre, és így tovább. Várjuk, hogy lássuk, mit épít!

## <a name="get-the-speech-devices-sdk"></a>Beszédeszközök SDK letöltése

### <a name="android"></a>Android

Az Androidhoz készült Speech Devices SDK támogatja a [Roobo v1](speech-devices-sdk-roobo-v1.md) és az azzal egyenértékű eszközöket, amelyek az [Android SPEECH Devices SDK](https://aka.ms/sdsdk-download-android)legújabb verzióját töltik le.


Ha más Android-eszközzel, például telefonnal vagy mobiltal rendelkezik, kezdje az [Android SPEECH SDK](speech-sdk.md) -val


### <a name="windows"></a>Windows

A Windows esetében a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Windows rendszeren készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

### <a name="linux"></a>Linux

A Linux rendszerben a minta alkalmazás platformfüggetlen Java-alkalmazásként van megadva. Töltse le a [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)legújabb verzióját.
Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8) készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

További bináris fájlok érhetők el a közelgő eszközök, a [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), a [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), a [GGEC Speaker](https://aka.ms/sdsdk-download-speaker), a [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32)és a [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64)támogatásához.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési eszköz kiválasztása](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)