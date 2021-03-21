---
title: Animált karakterek észlelése Video Indexer
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használható az animált karakterfelismerés a Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854750"
---
# <a name="animated-character-detection-preview"></a>Animált karakterek felismerése (előzetes verzió)

A Azure Media Services Video Indexer támogatja az animált tartalomban lévő karakterek észlelését, csoportosítását és felismerését [Cognitive Services egyéni jövőképtel](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)való integráció révén. Ez a funkció a Portálon és az API-n keresztül is elérhető.

Egy adott animációs modellel rendelkező animált videó feltöltése után Video Indexer Kinyeri a képkockákat, észleli az animált karaktereket ezekben a keretekben, a hasonló karaktereket csoportosítja, és kiválasztja a legjobb mintát. Ezt követően elküldi a csoportosított karaktereket a Custom Visionnak, amelyek a betanított modellek alapján azonosítják a karaktereket. 

A modell képzésének megkezdése előtt a rendszer a karaktereket észleli namelessly. A nevek hozzáadása és a modell betanítása során a Video Indexer felismeri a karaktereket, és ennek megfelelően nevezi el őket.

## <a name="flow-diagram"></a>Folyamatábra

Az alábbi ábra az animált karakterfelismerési folyamat folyamatát mutatja be.

![Folyamatábra](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Fiókok

A Video Indexer-fiók típusától függően különböző szolgáltatáskészlet-készletek érhetők el. A fiók Azure-hoz való csatlakoztatásával kapcsolatos információkért lásd: az [Azure-hoz csatlakoztatott video Indexer-fiók létrehozása](connect-to-azure.md).

* Próbaverziós fiók: Video Indexer belső Custom Vision fiókot használ a modell létrehozásához és a Video Indexer-fiókhoz való kapcsolódáshoz. 
* Fizetős fiók: a Custom Vision fiókját a Video Indexer-fiókjához kapcsolja (ha még nem rendelkezik ilyennel, először létre kell hoznia egy fiókot).

### <a name="trial-vs-paid"></a>Próbaverzió és fizetett

|Funkció|Próbaverzió|Fizetős|
|---|---|---|
|Custom Vision fiók|A színfalak mögött a Video Indexer által felügyelt. |A Custom Vision-fiókja Video Indexerhoz van csatlakoztatva.|
|Animációs modellek száma|Eggyel|Akár 100 modell/fiók (Custom Vision korlátozás).|
|A modell betanítása|A Video Indexer a modellt a meglévő karakterek további példáit képező új karakterekre.|A fiók tulajdonosa betanítja a modellt, amikor készen állnak a módosítások elvégzésére.|
|Speciális beállítások a Custom Vision|Nincs hozzáférése a Custom Vision portálhoz.|A modelleket saját kezűleg is módosíthatja a Custom Vision-portálon.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Az animált karakterek észlelése a portál és az API használatával

Részletekért lásd: [az animált karakterfelismerés használata a portál és az API használatával](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Korlátozások

* Az "animációs azonosítás" képesség jelenleg nem támogatott East-Asia régióban.
* A videóban kis vagy nagy méretű karakterek nem azonosíthatók megfelelően, ha a videó minősége gyenge.
* Javasoljuk, hogy az animált karakterek (például egy animált sorozat) esetében használjon modellt.

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)