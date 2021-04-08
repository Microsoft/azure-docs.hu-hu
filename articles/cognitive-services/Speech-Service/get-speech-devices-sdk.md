---
title: Beszédeszközök SDK letöltése
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás számos különböző eszközzel és hangforrással működik. Most a következő szintre állíthatja a beszédfelismerési alkalmazásokat a megfelelő hardverekkel és szoftverekkel. Ebből a cikkből megtudhatja, hogyan érheti el a Speech Devices SDK-t, és megkezdheti a fejlesztést.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026369"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A Cognitive Services Speech Devices SDK beszerzése

A Speech Devices SDK egy előre összehangolt könyvtár, amelynek célja, hogy a célnak megfelelően kialakított fejlesztői készletekkel működjön, és a mikrofonos tömbök különböző konfigurációit használják.

## <a name="choose-a-development-kit"></a>Fejlesztői csomag kiválasztása

|Eszközök|Specifikáció|Leírás|Forgatókönyvek|
|--|--|--|--|
|[Urbetter dev-készlet](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 MIC-tömb, ARM SOC, WIFI, Ethernet, HDMI, USB kamera. <br>Linux|Iparági szintű Speech Devices SDK, amely alkalmazkodik a Microsoft MIC-tömbhöz, és támogatja a kiterjesztett I/O-t, például a HDMI/Ethernet és a több USB-perifériát <br> [Kapcsolatfelvétel a Urbetter](http://www.urbetter.com/products_56/278.html)|Beszélgetések átírása, oktatás, kórház, robotok, OTT Box, hangvezérelt ügynök, Drive Thru|
|[Roobo Smart audio dev Kit](http://ddk.roobo.com)<br>[Telepítő](speech-devices-sdk-roobo-v1.md)  /  Gyors útmutató [](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ![ Roobo Smart audio dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC-tömb, ARM SOC, WIFI, audiokimenet, IO. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|Az első Speech Devices SDK a Microsoft MIC Array és az első feldolgozó SDK hozzáigazításához a kiváló minőségű átírási és beszédfelismerési forgatókönyvek fejlesztéséhez|Beszélgetés átirata, intelligens hangszóró, hangvezérelt ügynök, hordható|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Telepítő](../../kinect-dk/set-up-azure-kinect-dk.md)  /  Gyors útmutató [](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC-tömb RGB és mélységmérő kamerák. <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) / [Linuxos](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Fejlett mesterséges intelligencia (AI) érzékelőkkel rendelkező fejlesztői készlet kifinomult számítógépes jövőkép és beszédfelismerési modellek létrehozásához. Kombinálja a legjobb minőségű térbeli mikrofon tömbjét és részletes kameráját egy videokamera és tájolás érzékelővel – mindezt egyetlen kis eszközön, több mód, lehetőség és SDK használatával, amelyek számos számítási típust biztosítanak.|Beszélgetés átirata, robotika, intelligens kialakítás|
|Roobo Smart audio dev Kit 2<br>[Beállítás](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart audio dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 MIC-tömb, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|A 2. generációs Speech Devices SDK, amely alternatív operációs rendszert és további funkciókat biztosít a költséghatékony hivatkozási tervben.|Beszélgetés átirata, intelligens hangszóró, hangvezérelt ügynök, hordható|


## <a name="download-the-speech-devices-sdk"></a>A Speech Devices SDK letöltése

Töltse le a [Speech Devices SDK](./speech-devices-sdk.md)-t.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Speech Devices SDK-val](./speech-devices-sdk-quickstart.md?pivots=platform-android)