---
title: Modell tesztelése hangfájlok használatával – Speech Studio
titleSuffix: Azure Cognitive Services
description: Ebben a útmutatóban a Speech Studio használatával tesztelheti a hangfájlok felismerését.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: v-demjoh
ms.openlocfilehash: 93a0c171497faa9e87457a03da0b78003feaa533
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362267"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Modell tesztelése a Speech Studio hangfájljának használatával

Ebben a útmutatóban a Speech Studio használatával konvertálja a hangfájlok szövegét szöveggé. A Speech Studio lehetővé teszi a beszédfelismerési modellek tesztelését, összehasonlítását, fejlesztését és üzembe helyezését a kapcsolódó szöveggel, hangtal, emberi címkével ellátott átiratokkal és az Ön által megadott kiejtési útmutatással.

## <a name="prerequisites"></a>Előfeltételek

A Speech Studio használata előtt [kövesse az alábbi utasításokat egy Azure-fiók létrehozásához és a beszédfelismerési szolgáltatásra való előfizetéshez](../custom-speech-overview.md#set-up-your-azure-account). Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szöveg és a beszéd fordításához, valamint a Speech studióhoz.

## <a name="download-an-audio-file"></a>Hangfájl letöltése

Az alábbi lépéseket követve letöltheti a beszédet tartalmazó hangfájlt, és csomagolhatja azt egy zip-fájlba.

1. Töltse le a **[minta wav-fájlt ebből a hivatkozásból. ehhez](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** kattintson a jobb gombbal a hivatkozásra, és válassza a **hivatkozás mentése másként** lehetőséget. Kattintson a **Save (Mentés** ) gombra a fájl letöltéséhez `whatstheweatherlike.wav` .
2. Egy zip-eszközzel rendelkező fájlkezelő vagy Terminálablak használatával hozzon létre egy nevű zip-fájlt, `whatstheweatherlike.zip` amely tartalmazza a `whatstheweatherlike.wav` letöltött fájlt. A Windowsban nyissa meg a Windows Intézőt, lépjen a `Downloads` mappába, kattintson a jobb gombbal a gombra, kattintson a Küldés elemre, `whatstheweatherliike.wav` kattintson a **tömörített (zip) mappa** elemre, és az alapértelmezett fájlnév elfogadásához nyomja le az ENTER billentyűt.

## <a name="create-a-project-in-the-speech-studio"></a>Projekt létrehozása a Speech Studióban

Kövesse az alábbi lépéseket egy olyan projekt létrehozásához, amely egy hangfájl zip-fájlját tartalmazza.

1. Nyissa meg a [Speech studiót](https://speech.microsoft.com/), és kattintson az **új projekt** elemre. Adja meg a projekt nevét, majd kattintson a **Létrehozás** gombra. A projekt megjelenik a Custom Speech listán.
2. Kattintson a projekt nevére. Az adatok lapon kattintson az **adatok feltöltése** elemre.
3. A beszédfelismerési adattípus alapértelmezés szerint **csak a hanganyagot** adja meg, ezért kattintson a **tovább** gombra.
4. Nevezze el az új beszédfelismerési adatkészletet `MyZipOfAudio` , és kattintson a **tovább** gombra.
5. Kattintson a **Fájlok tallózása...** lehetőségre, keresse meg a `whatstheweatherlike.zip` fájlt, és kattintson a **Megnyitás** gombra.
6. Kattintson az **Upload** (Feltöltés) gombra. A böngésző feltölti a zip-fájlt a Speech studióba, a Speech Studio pedig feldolgozza a tartalmakat.

## <a name="test-a-model"></a>Modell tesztelése

Miután a Speech Studio feldolgozza a zip-fájl tartalmát, lejátszhatja a forrás hangját, miközben megvizsgálhatja az átírást a hibák vagy kihagyások kereséséhez. Az alábbi lépéseket követve ellenőrizheti az átiratok minőségét a böngészőben.

1. Kattintson a **tesztelés** fülre, majd a **teszt hozzáadása** gombra.
2. Ebben a tesztben a csak hangalapú adatok minőségét vizsgáljuk, ezért kattintson a **tovább** gombra a teszt típusának elfogadásához.
3. Nevezze el a tesztet `MyModelTest` , majd kattintson a **tovább** gombra.
4. Kattintson a bal oldali választógombra `MyZipOfAudio` , majd kattintson a **tovább** gombra.
5. Az **1. modell** legördülő lista alapértelmezett értéke a legújabb felismerési modell, ezért kattintson a **Létrehozás** gombra. A hangadatkészlet tartalmának feldolgozása után a teszt állapota **sikeres** lesz.
6. Kattintson a **MyModelTest** elemre. Megjelenik a beszédfelismerés eredményei. Kattintson a kör jobb oldali háromszögére a hang meghallásához, és hasonlítsa össze a szöveget a kör alapján.

## <a name="download-detailed-results"></a>Részletes találatok letöltése

Az átiratokat leíró fájlokat sokkal részletesebben töltheti le. A fájlok közé tartoznak a hangfájlokban lévő beszédek lexikális formája, valamint az egyes szavak eltolását, időtartamát és átírását tartalmazó JSON-fájlok. Ezeket a lépéseket követve tekintheti meg ezeket a fájlokat.

1. Kattintson a **Letöltés** gombra.
2. A letöltés párbeszédpanelen törölje a **hang** kijelölését, majd kattintson a **Letöltés** gombra.
3. Bontsa ki a letöltött zip-fájlt, és vizsgálja meg a kibontott fájlokat.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan javíthatja a beszédfelismerés pontosságát [Egyéni modell betanításával](../how-to-custom-speech-test-and-train.md).