---
title: Mi az optikai karakterfelismerés?
titleSuffix: Azure Cognitive Services
description: Az optikai karakterfelismerési (OCR) szolgáltatás kibontja a képen látható szöveget, és strukturált karakterláncként adja vissza.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 41b3552a633c9cebce1138fa042dbd154eee0cb5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314116"
---
# <a name="what-is-optical-character-recognition"></a>Mi az optikai karakterfelismerés?

Az optikai karakterfelismerési (OCR) szolgáltatás lehetővé teszi a nyomtatott vagy kézírásos szöveg kinyerését a képekből, például az utcai jelek és termékek képeit, valamint a dokumentumok &mdash; számláit, a számlákat, a pénzügyi jelentéseket, a cikkeket és egyebeket. Mély tanuláson alapuló modelleket használ, és számos felületen és háttérbeli szöveggel működik együtt.

Az OCR API-k [több nyelven](./language-support.md)is támogatják a nyomtatott szöveg kinyerését. [A kezdéshez kövesse az első](./quickstarts-sdk/client-library.md) lépéseket.

![OCR-bemutatók](./Images/ocr-demo.gif)

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* A [rövid](./quickstarts-sdk/client-library.md) útmutatók részletes útmutatást tesznek lehetővé, amelyekkel hívásokat indíthat a szolgáltatásba, és rövid idő alatt lekérheti az eredményeket. 
* A [útmutató útmutatók](./Vision-API-How-to-Topics/call-read-api.md) a szolgáltatás használatára vonatkozó utasításokat tartalmaznak részletesebb vagy testreszabott módokon.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Támogatott nyelvek
Az OCR API-k összesen 73 nyelvet támogatnak a nyomtatási stílus szövegeként. Tekintse meg az [OCR által támogatott nyelvek](./language-support.md#optical-character-recognition-ocr)teljes listáját. A kézírásos stílusú OCR kizárólag angol nyelven támogatott.

## <a name="input-requirements"></a>Bemeneti követelmények

Az **olvasási** hívás a képeket és a dokumentumokat veszi fel bemenetként. A követelmények a következők:

* Támogatott fájlformátumok: JPEG, PNG, BMP, PDF és TIFF
* PDF-és TIFF-fájlok esetén akár 2000-oldalas (csak az ingyenes szinthez tartozó első két oldal) lesz feldolgozva.
* A fájlméretnek kevesebbnek kell lennie, mint 50 MB (4 MB az ingyenes szinten), és legalább 50 x 50 képpont és legfeljebb 10000 x 10000 képpont méretűnek kell lennie. 
* A PDF-méreteknek legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.

## <a name="read-api"></a>API olvasása 

A Computer Vision [READ API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) az Azure legújabb OCR-technológiája (Ismerje meg az[újdonságokat](./whats-new.md)), amelyek kinyerik a nyomtatott szöveget (több nyelven), a kézírásos szövegeket (csak angol nyelven), számjegyeket és pénznem szimbólumokat a képekből és a többoldalas PDF-dokumentumokból. A szolgáltatás úgy van optimalizálva, hogy szövegből származó képekből és többoldalas PDF-dokumentumokból kinyerje a kevert nyelveket. Támogatja mind a nyomtatott, mind a kézírásos szöveg észlelését ugyanabban a képen vagy dokumentumban.

![Hogyan alakítja át az OCR a képeket és a dokumentumokat strukturált kimenetre a kinyert szöveggel](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>A felhőalapú API használata vagy helyszíni üzembe helyezés
Az olvasási 3. x Felhőbeli API-k az előnyben részesített lehetőségek a legtöbb ügyfél számára, mivel a könnyű integráció és a gyors termelékenység a dobozból. Az Azure és a Computer Vision szolgáltatás kezeli a méretezést, a teljesítményt, az adatbiztonságot és a megfelelőségi igényeket, miközben az ügyfelek igényeinek kielégítésére koncentrál.

A helyszíni telepítéshez az [olvasási Docker-tároló (előzetes verzió)](./computer-vision-how-to-install-containers.md) lehetővé teszi az új OCR-funkciók üzembe helyezését a saját helyi környezetében. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez.

## <a name="ocr-api"></a>OCR API

A régi [OCR API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) egy régebbi felismerési modellt használ, csak a képeket támogatja, és szinkron módon hajtja végre az észlelt szöveggel való azonnali visszatérést. A támogatott nyelvek listáját a [támogatott nyelvek](./language-support.md#optical-character-recognition-ocr) OCR oszlopában tekintheti meg.

> [!WARNING]
> A Computer Vision 2,0 RecognizeText műveletek folyamatban van, hogy az ebben a cikkben tárgyalt új [OLVASÁSI API](#read-api) javára elavulttá vált. A meglévő ügyfeleknek [át kell térniük az olvasási műveletek használatára](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Computer Vision szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg az [OCR (read) REST API vagy az ügyféloldali kódtár](./quickstarts-sdk/client-library.md)rövid útmutatóinak használatába.
- További információ az [olvasási 3,2 Rest APIról](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
