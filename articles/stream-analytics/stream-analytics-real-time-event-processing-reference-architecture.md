---
title: Valós idejű események feldolgozása Azure Stream Analytics használatával
description: Ez a cikk azt ismerteti, hogyan lehet a valós idejű események feldolgozását és elemzését Azure Stream Analytics használatával elérni.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 858ec5fef065acba6934945a96c21fb1c26b3685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012087"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Hivatkozási architektúra: valós idejű események feldolgozása Microsoft Azure Stream Analytics
A Azure Stream Analytics használatával történő valós idejű események hivatkozási architektúrája arra szolgál, hogy általános tervet nyújtson be a valós idejű platform szolgáltatásként történő üzembe helyezéséhez Microsoft Azure használatával.

## <a name="summary"></a>Összefoglalás
Az elemzési megoldások hagyományosan olyan képességek alapján lettek kialakítva, mint például az ETL (kinyerés, átalakítás, betöltés) és az adattárházak, ahol az adatok tárolása az elemzés előtt történik. A követelmények módosítása, beleértve a gyorsabban érkezőket is, a meglévő modellt a korlátra küldi. Az adatátviteli streameken belüli adatelemzés lehetősége egyetlen megoldás, és noha nem új képesség, a megközelítés nem lett széles körben elfogadva az iparági vertikális környezetekben. 

Microsoft Azure széles körű katalógust nyújt az elemzési technológiákról, amelyek képesek a különböző megoldási forgatókönyvek és követelmények tömbjét támogatni. Ha kiválasztja, hogy mely Azure-szolgáltatások telepíthetők egy teljes körű megoldásra, az ajánlatok szélessége is kihívást jelenthet. A tanulmány célja, hogy leírja a különböző Azure-szolgáltatások, amelyek támogatják az esemény-adatfolyamos megoldást. Emellett ismerteti azokat a forgatókönyveket is, amelyekben az ügyfelek kihasználhatják az ilyen típusú megközelítést.

## <a name="contents"></a>Tartalom
* Végrehajtási összefoglaló
* A Real-Time Analytics bemutatása
* Real-Time-adatmennyiségek kiosztása az Azure-ban
* Gyakori forgatókönyvek Real-Time elemzésekhez
* Architektúra és összetevők
  * Adatforrások
  * Data-Integration réteg
  * Valós idejű elemzési réteg
  * Adattárolási réteg
  * Megjelenítés/fogyasztás réteg
* Összegzés

**Szerző:** Charles Feddersen, megoldás-építész, az adatelemzési központ kiválósága, Microsoft Corporation

**Közzététel dátuma:** Január 2015

**Változat:** 1,0

**Letöltés:** [valós idejű események feldolgozása Microsoft Azure stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Segítség kérése
További segítségért próbálkozzon a [Microsoft Q&egy kérdés oldalával Azure stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)