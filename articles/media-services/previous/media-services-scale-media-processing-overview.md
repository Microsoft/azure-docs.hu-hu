---
title: A Media szolgáltatás számára fenntartott egységek áttekintése | Microsoft Docs
description: Ez a cikk áttekintést nyújt az adathordozók feldolgozásának Azure Media Servicessal való méretezéséről.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012717"
---
# <a name="media-reserved-units"></a>Fenntartott médiaegységek

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Azure Media Services lehetővé teszi a médiafájlok feldolgozását a Media szolgáltatás számára fenntartott egységek (MRUs-EK) kezelésével. Egy MRU további számítási kapacitást biztosít a kódolási adathordozók számára. A MRUs száma határozza meg azt a sebességet, amellyel a rendszer feldolgozza a média feladatait, és hogy hány adathordozó-feladatot lehet egyszerre feldolgozni egy fiókban. Ha például a fiókja öt MRUs rendelkezik, és vannak feldolgozható feladatok, az öt adathordozó-feladat egyidejűleg futhat. A rendszer a fennmaradó feladatokat várólistára helyezi, és a folyamatban lévő feladatok befejezését követően egymás után is felveheti a feldolgozást. Minden olyan MRU, amelyet kiépített, a kapacitás foglalása, de nem biztosít dedikált erőforrást. Rendkívül nagy igény esetén előfordulhat, hogy az összes MRUs azonnal nem kezdi meg a feldolgozást.

## <a name="choosing-between-different-reserved-unit-types"></a>Különböző fenntartott egységek típusának kiválasztása

A következő táblázat segít dönteni a különböző kódolási sebességek kiválasztásakor.  Egy 7 perces, a használatban lévő MRU-től függően 1080p-videóként jeleníti meg a kódolás időtartamát.

|RU-típus|Eset|Példa a 7 perces 1080p videó eredményeire |
|---|---|---|
| **S1**|Egyetlen bitráta kódolása. <br/>A fájlok SD-vagy alacsonyabb felbontásban, nem érzékenyek, alacsonyak.|A "H264 Single bitráta SD 16x9" használatával az egyetlen sávszélességű SD-feloldási MP4-fájl kódolása körülbelül 7 percet vesz igénybe.|
| **S2**|Egyetlen bitráta és több bitráta-kódolás.<br/>Normál használat SD és HD kódoláshoz.|A "H264 Single bitráta 720p" beállítású kódolás körülbelül 6 percet vesz igénybe.<br/><br/>A "H264 Multiple bitráta 720p" beállítású kódolás körülbelül 12 percet vesz igénybe.|
| **S3**|Egyetlen bitráta és több bitráta-kódolás.<br/>Teljes HD-és 4K-felbontású videók. Időérzékeny, gyorsabb átfutási idő kódolása.|A "H264 Single bitráta 1080p" beállítással rendelkező kódolás körülbelül 3 percet vesz igénybe.<br/><br/>A "H264 Multiple bitráta 1080p" beállítással rendelkező kódolás körülbelül 8 percet vesz igénybe.|

> [!NOTE]
> Ha nem hoz létre MRU-et a fiókjához, a rendszer a média feladatait az S1 MRU és a feladatok egymás utáni feladataival dolgozza fel. A rendszer nem foglal le feldolgozási kapacitást, így a várakozási idő egy feladat befejezése és a következő kezdés a rendszeren lévő erőforrások rendelkezésre állásának függvénye lesz.

## <a name="considerations"></a>Megfontolandó szempontok

* Az Media Services v3 vagy Video Indexer által aktivált hangelemzési és videó-elemzési feladatokhoz ajánlott a fiók tíz S3 egységgel való üzembe helyezése. Ha több mint 10 S3 MRUs van szüksége, nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com/)használatával.
* A MRUs nem rendelkező kódolási feladatoknál nincs felső korlát ahhoz, hogy a tevékenységek várólistán lévő állapotban legyenek, és legfeljebb egy feladat fog futni egyszerre.

## <a name="billing"></a>Számlázás

A számlázás a fiókban a Media szolgáltatás számára fenntartott egységek száma alapján történik. Ez attól függetlenül történik, hogy van-e a fiókjában futó feladat. Részletes magyarázatért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) oldalának gyakori kérdések című szakaszát.

## <a name="quotas-and-limitations"></a>Kvóták és korlátozások

A kvótákkal és korlátozásokkal, valamint a támogatási jegyek megnyitásával kapcsolatos információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Következő lépések

Próbálja ki a médiafájlok feldolgozását az alábbi technológiák egyikével:

[.Net](media-services-dotnet-encoding-units.md) 
 [Portál](media-services-portal-scale-media-processing.md) 
 [Rest](/rest/api/media/operations/encodingreservedunittype) 
 [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples) 
 [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)