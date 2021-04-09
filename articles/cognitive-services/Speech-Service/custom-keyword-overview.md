---
title: Egyéni kulcsszavak – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni kulcsszavak funkcióinak, képességeinek és korlátozásának áttekintése a Speech szoftverfejlesztői készlet (SDK) használatával.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91356604"
---
# <a name="what-is-a-keyword"></a>Mi az a kulcsszó?

A kulcsszó egy szó vagy rövid kifejezés, amely lehetővé teszi, hogy a termék hangvezérelt legyen. Például a "Hey Cortana" a Cortana segéd kulcsszava. A Hangaktiválás lehetővé teszi, hogy a felhasználók a kulcsszóval teljesen megkezdhetik a termékkel való interakciót. Mivel a termék folyamatosan figyeli a kulcsszót, az összes hang helyileg lesz feldolgozva a felhasználó eszközén mindaddig, amíg észlelés történik, hogy az adatok a lehető legmagának legyenek.

## <a name="core-features-of-custom-keyword"></a>Az egyéni kulcsszó alapvető funkciói

Az egyéni kulcsszavak testreszabási, teljesítmény-és integrációs funkciói révén a hangalapú aktiválást testre szabhatja a termék jövőképének és felhasználói igényeinek megfelelően.

| Szolgáltatás | Leírás |
|----------|----------|
| Kulcsszó testreszabása | A márka kiterjesztéseként egy kulcsszó megerősíti az ügyfeleivel létrehozott saját tőkét. A Speech Studio egyéni kulcsszavas portálján bármely olyan szót vagy rövid kifejezést megadhat, amely a legjobban jelképezi a márkáját. A kulcsszót a megfelelő kiejtések kiválasztásával személyre szabhatja, amelyet a generált Kulcsszóválasztó modell tiszteletben tart.
| Kulcsszó ellenőrzése | Ha a kulcsszót helyileg észlelik, a rendszer a hangot a felhőbe küldi, hogy további ellenőrzés után a felhasználó a kulcsszót is elmondja. A kulcsszó-ellenőrzés egy további biztonsági réteget biztosít azáltal, hogy csökkenti a helytelen helyi észlelés és a felhasználói adatok védelmének hatásait.
| Hangsegéd & Speech SDK-integráció | A Speech Studióban az egyéni kulcsszó alapján létrehozott kulcsszavak könnyen integrálhatók az eszközön vagy alkalmazáson belül a Speech SDK használatával. Egyszerűen mutasson az SDK-ra a Speech Studio által biztosított kulcsszó-modellre, és a termék hangvezérelt lesz, a kulcsszó-ellenőrzés által támogatottak. A saját [hangsegéd](voice-assistants.md)létrehozásával elvégezheti a termék hangélményét.

## <a name="get-started-with-custom-keywords"></a>Ismerkedés az egyéni kulcsszavakkal

* Az alapszintű használat és a kialakítási minták [Egyéni kulcsszavainak alapjai](custom-keyword-basics.md) .
* A [termék aktiválása a SPEECH SDK-val a C# használatával](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>Érvényes kulcsszó kiválasztása

Hatékony kulcsszó létrehozása elengedhetetlen ahhoz, hogy az eszköz következetesen és pontosan válaszoljon. A kulcsszó személyre szabása hatékony módszert tesz lehetővé az eszköz megkülönböztetésére és a saját arculatának megerősítésére. A kulcsszó kiválasztásakor vegye figyelembe a következő irányelveket:

> [!div class="checklist"]
> * A kulcsszónak angol szónak vagy kifejezésnek kell lennie.
> * Nem lehet hosszabb két másodpercnél.
> * A 4 – 7 szótag szavai a legjobban működnek. Például a "Hé, számítógép" egy jó kulcsszó. Csak a "Hey" egy rossz.
> * A kulcsszavaknak a gyakori angol kiejtési szabályokat kell követniük.
> * Egy egyedi vagy akár egy, a közös angol Kiejtési szabályt követő szó is csökkentheti a téves pozitív értéket. Előfordulhat például, hogy a "computerama" kulcsszó jó.
> * Ne válasszon közös szót. Például az "Eat" és a "Go" kifejezés olyan szavakat mutat be, amelyekben gyakran előfordul a hétköznapi beszélgetés. Lehet, hogy az eszköz hamis eseményindítókat tartalmaz.
> * Ne használjon olyan kulcsszót, amely alternatív kiejtésekkel rendelkezhet. A felhasználóknak ismerniük kell a "jobb" kiejtést, hogy az eszközük válaszoljon. Például: "509" lehet "5 0 9", "5 0 9" vagy "509". "R.E.I." "r-e-i" vagy "Ray" lehet. Az "élő" kifejezés "/līv/" vagy "/Liv/" lehet.
> * Ne használjon speciális karaktereket, szimbólumokat vagy számokat. Például a "Go #" és a "20 + Cats" lehet problematikus kulcsszó. Azonban a "Go Sharp" vagy a "húsz Plus Cats" is működhet. Továbbra is használhatja a branding szimbólumokat, és a marketing és a dokumentáció használatával megerősítheti a megfelelő kiejtést.

> [!NOTE]
> Ha kulcsszóként választ egy védjeggyel ellátott szót, akkor győződjön meg arról, hogy a védjegy tulajdonosa, vagy hogy a védjegy tulajdonosának van-e engedélye a szó használatára. A Microsoft nem vállal felelősséget az Ön által választott kulcsszótól esetlegesen felmerülő jogi problémákért.

## <a name="see-samples-on-github"></a>Minták megtekintése a GitHubon

* [Kulcsszavak felismerése a Speech SDK-val Univerzális Windows-platform a C használatával #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Kulcsszavak felismerése a Speech SDK-val, Androidon a Java használatával](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
* [A Speech SDK beszerzése](speech-sdk.md)
* [További információ a hangsegédekről](voice-assistants.md)
