---
title: Mi az a munkaterület és a projekt? -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk ismerteti a munkaterület és a projekt közötti különbséget, valamint az egyéni Translator szolgáltatáshoz tartozó projekt-kategóriákat és címkéket.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0abda0307e3cdbd0e73e131bee072172375198eb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898053"
---
# <a name="what-is-a-custom-translator-workspace"></a>Mi az egyéni Translator-munkaterület?

A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához használható munkaterület. A munkaterületek több projektet, modellt és dokumentumot is tartalmazhatnak. Az egyéni fordítóban végzett összes művelet egy adott munkaterületen belül van.

A munkaterület saját maga és a munkaterületen meghívott személyek számára. A nem meghívott személyek nem férhetnek hozzá a munkaterület tartalmához. Tetszőleges számú személyt meghívhat a munkaterületre, és bármikor módosíthatja vagy eltávolíthatja a hozzáférését. Új munkaterületet is létrehozhat. Alapértelmezés szerint a munkaterület nem tartalmaz olyan projektet vagy dokumentumot, amely a többi munkaterületen belül található.

## <a name="what-is-a-custom-translator-project"></a>Mi az egyéni fordítói projekt?

A projekt egy modell, dokumentum és teszt burkolója. Minden projekt automatikusan tartalmazza az adott munkaterületre feltöltött összes dokumentumot, amely a megfelelő nyelvi párral rendelkezik. Ha például egy angol – spanyol és egy spanyol – angol projekt is van, akkor mindkét projektben ugyanazokat a dokumentumokat fogja tartalmazni. Minden projekthez hozzá van rendelve egy Kategóriakód, amely a [V3 API](../reference/v3-0-translate.md?tabs=curl) fordításokhoz való lekérdezéséhez használatos. A Kategóriakód a Custom Translatortel létrehozott, testreszabott rendszerből származó fordítások beolvasására szolgáló paraméter.

## <a name="project-categories"></a>Projektek kategóriái

A kategória azonosítja a tartományt – a használni kívánt terminológia és stílus területét – a projekthez. Válassza ki a dokumentumokhoz leginkább illő kategóriát. Bizonyos esetekben a kategória választása közvetlenül befolyásolja az egyéni fordító viselkedését.

Két alapmodellből álló készletünk van. Általános és technológiai. Ha a kategória **technológiája** be van jelölve, a rendszer a technológiai alapmodelleket fogja használni. Bármely más kategória kiválasztásakor a rendszer az általános alapmodelleket használja. A technológiai alapmodell jól működik a technológiai tartományban, de alacsonyabb minőséget mutat, ha a fordításhoz használt mondatok nem tartoznak a technológiai tartományba. Javasoljuk, hogy az ügyfelek csak akkor válasszanak a kategória technológiát, ha a mondatok szigorúan a technológiai tartományon belül esnek.

Ugyanebben a munkaterületen különböző kategóriákban hozhat létre projekteket ugyanahhoz a nyelvi párosításhoz. Az egyéni fordító meggátolja egy ismétlődő projekt létrehozását ugyanazzal a nyelvi párral és kategóriával. Ha a címkét a projektre alkalmazza, ezzel elkerülhető a korlátozás. Ne használjon feliratokat, kivéve, ha több ügyfél számára készít fordítási rendszereket, mivel a projekthez tartozó egyedi címke hozzáadása a projektek Kategóriakód-ban jelenik meg.

## <a name="project-labels"></a>Projekt feliratai

Az egyéni fordító lehetővé teszi a projekthez tartozó címke hozzárendelését. A projekt felirata megkülönbözteti a több projekt között ugyanazzal a nyelvi párral és kategóriával. Ajánlott eljárásként lehetőleg kerülje a címkék használatát, ha szükséges.

A projekt címkéje a Kategóriakód részeként van használatban. Ha a projekt felirata balra van állítva, vagy azonos módon van beállítva a projektek között, akkor az azonos kategóriájú és *különböző* nyelvi párokkal rendelkező projektek ugyanazt a Kategóriakód-t fogják megosztani. Ez a megközelítés azért előnyös, mert lehetővé teszi, hogy az Ön vagy az ügyfele átváltson a nyelvek között a szöveges Translator API használata nélkül anélkül, hogy az egyes projektekre jellemző Kategóriakód-t kellene aggódnia.

Ha például azt szeretném, hogy a technológiai tartomány fordításait angolról franciára vagy franciáról angolra szeretném engedélyezni, hozzon létre két projektet: egy az angol – \> francia nyelven, egy pedig francia – \> angol nyelven. Ugyanazt a kategóriát (technológiát) szeretném megadni, és a projekt címkéjét üresen hagyják. A két projekthez tartozó Kategóriakód is egyezik, ezért a Kategóriakód módosítása nélkül is lekérhetem az API-t angol és francia nyelvű fordításra.

Ha Ön nyelvi szolgáltató, és több olyan ügyfelet szeretne kiszolgálni, akik különböző modellekkel rendelkeznek, és megtartják ugyanazt a kategóriát és nyelvi párt, akkor a projekt címkéje az ügyfelek közötti különbségtételhez bölcs döntés lenne.

## <a name="next-steps"></a>Következő lépések

- Tájékozódjon a [Betanításról és a modellről, és](training-and-model.md) Ismerje meg, hogyan hozhat létre hatékony fordítási modellt.