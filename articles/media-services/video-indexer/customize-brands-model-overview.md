---
title: Brands-modell testreszabása Video Indexer-Azure-ban
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt arról, hogy mi a Brands modell a Video Indexerban, és hogyan szabhatja testre.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586074"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Márkák modell testreszabása Video Indexer

A Video Indexer támogatja a beszédfelismerés és a vizualizáció szövegének felderítését a videó-és hangtartalom indexelése és újraindexelése során. A márka észlelési funkciója a Bing márkák adatbázisa által javasolt termékek, szolgáltatások és vállalatok megemlítését azonosítja. Ha például a Microsoft a videóban vagy a hangtartalomban szerepel, vagy ha a videó vizualizációs szövegben jelenik meg, akkor Video Indexer a tartalmat a tartalomban márkaként észleli. A márkák más feltételek alapján disambiguated a kontextus használatával.

A márka észlelése számos különböző üzleti forgatókönyvben hasznos lehet, például a tartalmak archiválása és felderítése, a kontextusbeli reklámozás, a közösségi média elemzése, a kiskereskedelmi verseny elemzése és sok más. A Video Indexer márka észlelése lehetővé teszi a beszéd-és vizualizációs szövegek, a Bing márkák adatbázisának, valamint a testreszabási funkcióinak indexelését az egyes Video Indexer-fiókokhoz tartozó egyéni márkák modell kiépítése révén. A Custom Brands Model szolgáltatással kiválaszthatja, hogy Video Indexer észleli-e a Bing Brands adatbázisából származó márkákat, kizár bizonyos márkákat a rendszerből (lényegében a nem jóváhagyott márkák listáját hozza létre), és olyan márkákat tartalmazzon, amelyeknek a típusa nem a Bing márka-adatbázisában található (lényegében létrehozta a jóváhagyott márkák listáját). A létrehozott egyéni Brands modell csak abban a fiókban lesz elérhető, amelyben létrehozta a modellt.

## <a name="out-of-the-box-detection-example"></a>A Box észlelési példája

A "Microsoft Build 2017 Day 2" bemutatóban a "Microsoft Windows" márka többször is megjelenik. Néha az átiratban, néha vizuális szövegként, és soha nem a szó szerint. A Video Indexer nagy pontossággal észleli, hogy egy kifejezés valójában a kontextuson alapul, amely a 90k márkákra terjed ki a dobozból, és folyamatosan frissül. A 02:25-es számú Video Indexer észleli a márka beszédből való használatát, majd a 02:40-es verziótól kezdve a Windows embléma részét képező vizuális szövegből.

![Márkák áttekintése](./media/content-model-customization/brands-overview.png)

Ha az építőipar kontextusában a Windowsról beszél, a "Windows" szót nem ismeri fel márkaként, és ugyanaz a Box, az Apple, a Fox stb., a speciális Machine Learning algoritmusok alapján, amelyekkel egyértelműsítse a környezetből. A márka észlelése minden támogatott nyelv esetében működik.  

## <a name="next-steps"></a>Következő lépések

Saját márkák létrehozásához tekintse meg a következő témaköröket:

[A Brands modell testreszabása API-k használatával](customize-brands-model-with-api.md)

[A Brands modell testreszabása a webhely használatával](customize-brands-model-with-website.md)
