---
title: Szöveg – beszéd áttekintés – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás szöveg-beszéd funkciója lehetővé teszi, hogy az alkalmazások, eszközök és eszközök szövegét természetes emberi – például szintetizált beszédre alakítsa át. Ez a cikk áttekintést nyújt a szöveg-beszéd szolgáltatás előnyeiről és képességeiről.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: szöveg – beszéd
ms.openlocfilehash: 1a7ff7befc1a5e82c5518cf103a81b35ec38c7db
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449358"
---
# <a name="what-is-text-to-speech"></a>Mi az a szövegfelolvasás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Ebben az áttekintésben megismerheti a szöveg és a beszéd közötti szolgáltatás előnyeit és képességeit, ami lehetővé teszi, hogy alkalmazásai, eszközei vagy eszközei szövegeket alakítsanak ki az emberi, például a szintetizált beszédbe. Válasszon a standard és a neurális hangok közül, vagy hozzon létre egyedi hangvételt a termék vagy a márka számára. a 75 + standard hangok több mint 45 nyelven és területi beállításban érhetők el, és az 5 neurális hang a kiválasztott számú nyelven és területi beállításban érhető el. A támogatott hangok, nyelvek és területi beállítások teljes listájáért lásd: [támogatott nyelvek](language-support.md#text-to-speech).

Ez a dokumentáció a következő cikk-típusokat tartalmazza:

* **A gyors** üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.
* A **útmutatók** útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.
* A **fogalmak** részletesen ismertetik a szolgáltatás funkcióit és funkcióit.
* Az **oktatóanyagok** már olyan útmutatók, amelyek bemutatják, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

> [!NOTE]
> A Bing Speech 2019. október 15-én lett leszerelve. Ha alkalmazásai, eszközei vagy termékei a Bing Speech API-kat vagy Custom Speech-t használják, a beszédfelismerési szolgáltatásba való Migrálás megkönnyítéséhez útmutatókat hoztunk létre.
> - [Áttelepítés Bing Speechról a beszédfelismerési szolgáltatásba](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Alapvető funkciók

* Beszédfelismerés – a [SPEECH SDK](./get-started-text-to-speech.md) vagy a [REST API](rest-text-to-speech.md) segítségével normál, neurális vagy egyéni hangok használatával alakíthatja át a szöveg-beszédet.

* A hosszú hang aszinkron szintézise – a [hosszú](long-audio-api.md) hangalapú API használatával aszinkron módon szintetizálhatja a szöveg-beszéd fájlokat 10 percnél hosszabb ideig (például Hangoskönyvek vagy előadások esetén). A Speech SDK vagy a beszéd – szöveg REST API használatával végrehajtott szintézistől eltérően a válaszokat nem valós időben adja vissza a rendszer. A várt érték az, hogy a kérelmeket aszinkron módon küldik el, a rendszer lekérdezi a válaszokat, és letölti a szintetizált hangot, amikor elérhetővé válik a szolgáltatásból. Csak az egyéni neurális hangok támogatottak.

* Standard hangok – statisztikai számszerű szintézis és/vagy összefűzési technikák használatával hozhatók létre. Ezek a hangok jól érthetőek és természetesek. Egyszerűen engedélyezheti, hogy alkalmazásai több mint 45 nyelven beszéljenek, számos hanglehetőséggel. Ezek a hangok magas kiejtési pontosságot biztosítanak, beleértve a rövidítések támogatását, a rövidítések, a dátum/idő értelmezését, a telefonokat és egyebeket. A standard hangok teljes listájáért lásd: [támogatott nyelvek](language-support.md#text-to-speech).

* Neurális hangok – a mély neurális hálózatok a hagyományos beszédfelismerés korlátainak leküzdésére szolgálnak a hangsúlyt és a hanglejtést illetően. A Prosody előrejelzése és a hangszintézis párhuzamosan történik, ami több folyadékot és természetes hangzású kimenetet eredményez. A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakciót természetesebb és vonzó, digitális szövegek, például e-könyvek hangoskönyvekbe való átalakítására és az autós navigációs rendszerek fejlesztésére. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelés fáradtságot, amikor az AI-rendszerekkel kommunikál. A neurális hangok teljes listáját lásd: [támogatott nyelvek](language-support.md#text-to-speech).

* A beszédfelismerési stílusok a SSML-hangszintézis Markup Language (SSML) nyelvvel való beállítása egy XML-alapú Markup-nyelv, amely a beszéd-szöveg kimenetek testreszabására szolgál. A SSML beállíthatja a Pitch, a szüneteltetések hozzáadását, a kiejtés növelését, a sebesség növelését vagy lelassulását, a kötetek növelését és csökkentését, valamint több hang megadását egyetlen dokumentumra. Lásd: [útmutató](speech-synthesis-markup.md) a beszéd stílusainak módosításához.

* Visemes – a [Visemes](how-to-speech-synthesis-viseme.md) a megfigyelt beszédben jelent meg, beleértve az ajkak, az állkapocs és a nyelv pozícióját, amikor egy adott fonéma hoz létre. A Visemes erős korrelációt mutat a hang-és fonémák. Az viseme-események a Speech SDK-ban való használatával az arc-animálási információk is létrehozhatók, amelyekkel animálható az arcok az ajak-olvasási kommunikáció, az oktatás, a szórakoztatás és az ügyfélszolgálat számára.

> [!NOTE]
> A Viseme `en-US-AriaNeural` most már csak hangra működik.

## <a name="get-started"></a>Bevezetés

Tekintse [meg az első lépéseket a szöveg](get-started-text-to-speech.md) és a beszéd megismeréséhez. A szöveg-beszéd szolgáltatás a [SPEECH SDK](speech-sdk.md), a [REST API](rest-text-to-speech.md)és a [Speech CLI](spx-overview.md) használatával érhető el.

## <a name="sample-code"></a>Mintakód

A szöveg és a beszéd mintája a GitHubon érhető el. Ezek a minták a legnépszerűbb programozási nyelvek szöveg-beszéd átalakítását fedik le.

- [Szöveg – beszéd minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Testreszabás

A standard és a neurális hangokon kívül egyéni hangokat is létrehozhat és finomhangolással egyedivé teheti a termékét vagy márkáját. A kezdéshez mindössze néhány hangfájlt és a hozzá tartozó átírásokat kell használnia. További információ: Ismerkedés [az egyéni hanggal](how-to-custom-voice.md)

## <a name="pricing-note"></a>Díjszabási Megjegyzés

A szöveg-beszéd típusú szolgáltatás használatakor a rendszer minden, a beszédre konvertált karakter után számláz, beleértve a központozást is. Míg maga a SSML-dokumentum nem számlázható, a szöveg átalakításának módjára, például a fonémák és a Pitch formátumára szolgáló opcionális elemek számlázandó karakternek számítanak. Az alábbi lista tartalmazza a számlázható tartalmakat:

- A kérelem SSML törzsében a szöveg-beszéd szolgáltatásnak átadott szöveg
- A kérelem törzsének összes jelölése a SSML formátumban, a `<speak>` és a `<voice>` címkék kivételével
- Betűk, írásjelek, szóközök, tabulátorok, jelölések és minden fehér szóköz
- Minden Unicode-ban definiált kód pont

Részletes információkat a [díjszabásban](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)talál.

> [!IMPORTANT]
> Minden kínai, Japán és koreai nyelvi karakter két karakternek számít a számlázáshoz.

## <a name="reference-docs"></a>Dokumentációs dokumentumok

- [Beszéd SDK](speech-sdk.md)
- [REST API: szövegről beszédre](rest-text-to-speech.md)

## <a name="next-steps"></a>Következő lépések

- [Ingyenes beszédfelismerési szolgáltatás előfizetésének beszerzése](overview.md#try-the-speech-service-for-free)
- [A Speech SDK beszerzése](speech-sdk.md)
