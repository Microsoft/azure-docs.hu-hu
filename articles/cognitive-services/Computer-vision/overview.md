---
title: Mi a Computer Vision?
titleSuffix: Azure Cognitive Services
description: A Computer Vision szolgáltatás a rendszerképek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális algoritmusokhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: Computer vízió, Computer víziós alkalmazások, számítógépes jövőkép szolgáltatás
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286134"
---
# <a name="what-is-computer-vision"></a>Mi a Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Computer Vision szolgáltatásával olyan speciális algoritmusokhoz férhet hozzá, amelyek képek feldolgozását végzik, és a vizuális funkciók alapján adnak vissza információkat. 

| Szolgáltatás|Leírás|
|---|---|
|[Optikai karakterfelismerés (OCR)](overview-ocr.md)|Az optikai karakterfelismerési (OCR) szolgáltatás szöveget gyűjt a képekből. Az új olvasási API használatával kinyerheti a nyomtatott és a kézírásos szöveget a fényképekből és a dokumentumokból. Mély tanuláson alapuló modelleket használ, és számos felületen és háttérbeli szöveggel működik együtt. Ilyenek például az üzleti dokumentumok, a számlák, a nyugták, a plakátok, az üzleti kártyák, a levelek és a táblák. Az OCR API-k [több nyelven](./language-support.md)is támogatják a nyomtatott szöveg kinyerését. Az első lépésekhez kövesse az [OCR](quickstarts-sdk/client-library.md) rövid útmutatóját.|
|[Képelemzés](overview-image-analysis.md)| A rendszerkép-elemzési szolgáltatás számos vizuális funkciót Kinyer a képekből, például az objektumokból, az arcokból, a felnőtt tartalomból és az automatikusan generált szöveges leírásokból. A kezdéshez kövesse a [rendszerkép elemzése](quickstarts-sdk/image-analysis-client-library.md) rövid útmutatót.|
| [Térbeli elemzés](intro-to-spatial-analysis-public-preview.md)| A térbeli elemzési szolgáltatás elemzi a személyek jelenlétét és mozgását a videó-hírcsatornán, és olyan eseményeket állít elő, amelyeket más rendszerek is válaszolnak. Telepítse a [térbeli elemzési tárolót](spatial-analysis-container.md) a kezdéshez.|

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision a digitális eszközök kezeléséhez

A Computer Vision számos digitális Asset Management-(DAM-) forgatókönyvet képes kihasználni. A DAM a gazdag média-eszközök rendszerezésének, tárolásának és lekérésének üzleti folyamata, valamint a digitális jogok és engedélyek kezelése. Előfordulhat például, hogy egy vállalat szeretné csoportosítani és azonosítani a képeket a látható emblémák, arcok, objektumok, színek és egyebek alapján. Vagy előfordulhat, hogy automatikusan kívánja [létrehozni a képekhez tartozó feliratokat](./Tutorials/storage-lab-tutorial.md) és kulcsszavakat csatolni, hogy azok kereshetőek legyenek. Az Cognitive Services, az Azure Cognitive Search és az intelligens jelentéskészítés szolgáltatást használó teljes körű megoldásért tekintse meg a Knowledge Reporting [Solution-gyorsító útmutatót](https://github.com/Azure-Samples/azure-search-knowledge-mining) a githubon. Más DAM-példákért tekintse meg a [Computer Vision megoldási sablonok](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) tárházát.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A Computer Vision a következő követelményeknek megfelelő képeket képes elemezni:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont
  - Az olvasási API esetében a rendszerkép méretei 50 x 50 és 10000 x 10000 képpont közé kell, hogy legyenek.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Computer Vision szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Egy rövid útmutató segítségével implementálhatja és futtathatja a szolgáltatást az előnyben részesített fejlesztői nyelven.

* [Gyors útmutató: optikai karakterfelismerés (OCR)](quickstarts-sdk/client-library.md)
* [Gyors útmutató: képek elemzése](quickstarts-sdk/image-analysis-client-library.md)
* [Gyors útmutató: térbeli elemzési tároló](spatial-analysis-container.md)
