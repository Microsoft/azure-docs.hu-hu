---
title: Media szolgáltatás számára fenntartott egységek – Azure
description: A Media szolgáltatás számára fenntartott egységek lehetővé teszik a médiafájlok méretezését és a média-feldolgozási feladatok sebességének meghatározását.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 4b3a06d2d441c918c4c109d9dc624ebfa1cd4c97
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120810"
---
# <a name="media-reserved-units"></a>Media szolgáltatás számára fenntartott egységek

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services lehetővé teszi a médiafájlok feldolgozását a Media szolgáltatás számára fenntartott egységek (MRUs-EK) kezelésével. Egy MRU további számítási kapacitást biztosít a kódolási adathordozók számára. A MRUs száma határozza meg azt a sebességet, amellyel a rendszer feldolgozza a média feladatait, és hogy hány adathordozó-feladatot lehet egyszerre feldolgozni egy fiókban. Ha például a fiókja öt MRUs rendelkezik, és vannak feldolgozható feladatok, az öt adathordozó-feladat egyidejűleg futhat. A rendszer a fennmaradó feladatokat várólistára helyezi, és a folyamatban lévő feladatok befejezését követően egymás után is felveheti a feldolgozást. Minden olyan MRU, amelyet kiépített, a kapacitás foglalása, de nem biztosít dedikált erőforrást. Rendkívül nagy igény esetén előfordulhat, hogy az összes MRUs azonnal nem kezdi meg a feldolgozást.

A feladat egy adott eszközön végzett önálló művelet, például az adaptív adatfolyam-kódolás. Ha elküld egy feladatot, Media Services a feladatokat különálló műveletekre (például a feladatokra) helyezi a feladatot, amelyek ezután külön MRUs lesznek társítva.

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

A díjat a Media szolgáltatás számára fenntartott egységek száma alapján számítjuk fel a fiókban, függetlenül attól, hogy vannak-e olyan feladatok, amelyeken fut. Részletes magyarázatért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) oldalának gyakori kérdések című szakaszát.

## <a name="next-step"></a>Következő lépés
[A Media szolgáltatás számára fenntartott egységek méretezése a parancssori](media-reserved-units-cli-how-to.md) 
 felülettel [Videók elemzése](analyze-videos-tutorial.md)

## <a name="see-also"></a>Lásd még

* [Kvóták és korlátok](limits-quotas-constraints-reference.md)
