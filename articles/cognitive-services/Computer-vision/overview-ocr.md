---
title: Mi az az optikai karakterfelismerés?
titleSuffix: Azure Cognitive Services
description: Az optikai karakterfelismerés (OCR) szolgáltatás kinyeri a képen látható szöveget, és strukturált sztringekként adja vissza.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536735"
---
# <a name="what-is-optical-character-recognition"></a>Mi az az optikai karakterfelismerés?

Az optikai karakterfelismerési (OCR) szolgáltatás lehetővé teszi nyomtatott vagy kézzel írt szövegek kinyerét képekből, például utcatáblákról és termékekről készült fényképekből, valamint dokumentumok számláiból, számláiból, pénzügyi jelentéseiből, cikkeiből &mdash; stb. Mélytanuláson alapuló modelleket használ, és különböző felületeken és háttereken használható szövegekkel.

Az OCR API-k támogatják a nyomtatott szöveg több nyelven [való kinyerét.](./language-support.md) Az első [lépésekhez kövesse](./quickstarts-sdk/client-library.md) a rövid útmutatót.

![OCR-bemutatók](./Images/ocr-demo.gif)

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* A [gyorsútmutatók](./quickstarts-sdk/client-library.md) olyan részletes utasítások, amelyek segítségével hívásokat kezdeményez a szolgáltatáshoz, és rövid idő alatt lekért eredményeket. 
* Az [útmutatók a](./Vision-API-How-to-Topics/call-read-api.md) szolgáltatás pontosabb vagy testreszabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Támogatott nyelvek
Az OCR API-k összesen 73 nyelvet támogatnak a nyomtatási stílusú szövegekhez. Tekintse meg az [OCR által támogatott nyelvek teljes listáját.](./language-support.md#optical-character-recognition-ocr) A kézzel írt stílusú OCR kizárólag az angol nyelvhez támogatott.

## <a name="input-requirements"></a>Bemeneti követelmények

A **Read** hívás bemenete képek és dokumentumok. A követelmények a következők:

* Támogatott fájlformátumok: JPEG, PNG, BMP, PDF és TIFF
* PDF- és TIFF-fájlok esetén legfeljebb 2000 oldal (csak az ingyenes szint első két oldala) lesz feldolgozva.
* A fájlméretnek 50 MB-nál kisebbnek kell lennie (4 MB az ingyenes szint esetében), és legalább 50 x 50 képpont méretűnek és legalább 10000 x 10000 képpontnak kell lennie. 

## <a name="read-api"></a>Read API 

A Computer Vision [Read API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) az Azure legújabb OCR-technológiája[(ismerje](./whats-new.md)meg az újdonságokat), amely nyomtatott szöveget (több nyelven), kézzel írt szöveget (csak angol nyelven), számjegyeket és pénznemszimbólumokat olvas ki képekből és többoldalas PDF-dokumentumokból. Szöveg kinyerére van optimalizálva nagy szöveges képekből és többoldalas PDF-dokumentumokból vegyes nyelvekkel. Támogatja a nyomtatott és a kézzel írt szövegek észlelését is ugyanazon a képen vagy dokumentumon.

![Hogyan konvertálja az OCR a képeket és dokumentumokat strukturált kimenetre kinyert szöveggel](./Images/how-ocr-works.svg)

### <a name="key-features"></a>A legfontosabb jellemzők

A Read API a következő funkciókat tartalmazza. 

* Szövegkinyerés nyomtatása 73 nyelven
* Kézzel írt szöveg kinyerése angol nyelven
* Szövegsorok és szavak hely- és megbízhatósági pontszámokkal
* Nincs szükség nyelvazonosításra
* Vegyes nyelvek, vegyes mód (nyomtatás és kézzel írt) támogatása
* Oldalak és oldaltartományok kiválasztása a nagyméretű, többoldalas dokumentumoktól
* Természetes olvasási sorrend szövegsorok számára
* Kézírás-osztályozás szövegsorok számára
* Elérhető disztribúció nélküli Docker-tárolóként a saját környezetben való üzembe helyezéshez

Ismerje [meg az OCR funkcióinak használatát.](./vision-api-how-to-topics/call-read-api.md)

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>A felhőalapú API használata vagy a felhőben való üzembe helyezés
A Read 3.x felhőalapú API-k használata az előnyben részesített lehetőség a legtöbb ügyfél számára az egyszerű integráció és a gyors hatékonyság miatt. Az Azure és Computer Vision szolgáltatás kezeli a méretezési, teljesítmény-, adatbiztonsági és megfelelőségi igényeket, miközben Ön az ügyfelek igényeinek való megfelelésre összpontosít.

A helyi üzembe helyezéshez a [Read Docker-tároló (előzetes verzió)](./computer-vision-how-to-install-containers.md) lehetővé teszi az új OCR-képességek üzembe helyezését a saját helyi környezetében. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez.

## <a name="ocr-api"></a>OCR API

Az örökölt [OCR API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) egy régebbi felismerési modellt használ, csak képeket támogat, és szinkron módon fut le, és azonnal visszaadja az észlelt szöveget. A támogatott nyelvek [](./language-support.md#optical-character-recognition-ocr) listáját a támogatott nyelvek OCR oszlopában láthatja.

> [!WARNING]
> A Computer Vision 2.0 RecognizeText műveleteinek elavultnak kell lenni a cikkben szereplő új [Read API](#read-api) használata érdekében. A meglévő ügyfeleknek [olvasási műveleteket kell használniuk.](upgrade-api-versions.md)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Mint minden más Cognitive Services, az Computer Vision szolgáltatást használó fejlesztőknek tisztában kell lenniük a Microsoft ügyféladatokkal kapcsolatos szabályzatával. További Cognitive Services [a](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Microsoft Biztonsági és biztonsági központ Cognitive Services oldalon.

## <a name="next-steps"></a>Következő lépések

- Az OCR (olvasás) és az [ügyféloldali kódtár REST API](./quickstarts-sdk/client-library.md)első lépések.
- További tudnivalók a [Read 3.2 REST API.](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
