---
title: Szűrés nyelv alapján keresési indexben
titleSuffix: Azure Cognitive Search
description: Szűrési feltételek a többnyelvű keresés támogatásához, a lekérdezés végrehajtásának hatóköre nyelvspecifikus mezőkhöz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 49a12203c833fc817b1898e6179d7f812d0a994e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002521"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Szűrés nyelv alapján az Azure-ban Cognitive Search 

A többnyelvű keresőalkalmazás egyik kulcsfontosságú követelménye, hogy a felhasználó saját nyelvén kereshet át és kérhet le eredményeket. Az Azure Cognitive Search a többnyelvű alkalmazások nyelvi követelményeinek való megfelelés egyik módja, ha a karakterláncok egy adott nyelven való tárolásához dedikált mezőket hoz létre, majd a teljes szöveges keresést csak a lekérdezési időpontra korlátozza.

A kérelemben szereplő lekérdezési paraméterek a keresési művelet hatókörére vonatkoznak, majd levágja azokat a mezőket, amelyek nem biztosítanak tartalmat a kézbesíteni kívánt keresési felülettel.

| Paraméterek | Cél |
|-----------|--------------|
| **searchFields** | A teljes szöveges keresést a megnevezett mezők listájára korlátozza. |
| **$select** | Levágja a választ, hogy csak a megadott mezőket foglalja bele. Alapértelmezés szerint a rendszer az összes beolvasható mezőt visszaadja. A **$Select** paraméterrel kiválaszthatja, hogy melyeket kell visszaadnia. |

Ennek a technikának a sikere a mezők tartalmának integritására támaszkodik. Az Azure Cognitive Search nem fordítja le a karakterláncokat, és nem végez nyelvi észlelést. Így gondoskodhat arról, hogy a mezők tartalmazzák a várt karakterláncokat.

## <a name="define-fields-for-content-in-different-languages"></a>Mezők definiálása különböző nyelveken lévő tartalomhoz

Az Azure Cognitive Searchban a lekérdezések egyetlen indexet céloznak meg. A fejlesztők, akik egy adott keresési élményben szeretnék megadni a nyelvspecifikus karakterláncokat, jellemzően az értékek tárolására szolgáló dedikált mezőket határozzák meg: az angol karakterláncok egy mezője, egy a francia, és így tovább. 

A következő példa a [Real-Estate mintából](search-get-started-portal.md) származik, amelynek több, különböző nyelvű tartalmakat tartalmazó karakterlánc-mezője van. Figyelje meg az index mezőinek nyelvi elemző hozzárendeléseit. A karakterláncokat tartalmazó mezők jobban teljesítik a teljes szöveges keresést, ha a cél nyelv nyelvi szabályainak kezelésére szolgáló elemzővel párosítva van.

  ![Képernyőfelvétel: a Real-Estate minta Fields (mezők) képernyője. A mezők egy csoportja kiemelten kiemeli, hogy a Language Analyzer-hozzárendelések hogyan felelnek meg a Kiemelt mezők nyelveinek.](./media/search-filters-language/lang-fields.png)

> [!Note]
> A következő kódrészletek esetében a nyelvi elemzőket bemutató példákat lásd: [index (.net) definiálása](./search-get-started-dotnet.md) és [index (REST) definiálása](./search-get-started-powershell.md).

## <a name="build-and-load-an-index"></a>Index létrehozása és betöltése

Egy közbenső (és talán nyilvánvaló) lépés az, hogy [az indexet fel kell építenie és fel kell töltenie a](./search-get-started-dotnet.md) lekérdezés kialakítása előtt. Ez a lépés a teljesség kedvéért van megemlítve. Az indexek elérhetővé tételének egyik módja a [portál](https://portal.azure.com)indexek listájának ellenőrzése.

## <a name="constrain-the-query-and-trim-results"></a>A lekérdezés és a vágás eredményének korlátozása

A lekérdezés paraméterei a keresés adott mezőkre való korlátozására szolgálnak, majd az adott forgatókönyvnek nem megfelelő mezők eredményeinek levágása. A francia sztringeket tartalmazó mezők keresésének célja, hogy a **searchFields** az adott nyelven sztringeket tartalmazó mezőkre célozza. 

Alapértelmezés szerint a keresés visszaadja az összes olyan mezőt, amely beolvasható van megjelölve. Ezért érdemes lehet olyan mezőket kizárni, amelyek nem felelnek meg a megadni kívánt nyelvspecifikus keresési élménynek. Pontosabban, ha a keresés egy francia sztringet tartalmazó mezőre korlátozódik, valószínűleg ki szeretné zárni az eredményekből származó angol nyelvű mezőket. A **$Select** lekérdezési paraméterrel szabályozhatja, hogy a rendszer mely mezőket adja vissza a hívó alkalmazásnak.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Bár a lekérdezésnek nincs $filter argumentuma, ez a használati eset erősen kapcsolódik a szűrési fogalmakhoz, ezért szűrési forgatókönyvként jelenik meg.

## <a name="see-also"></a>Lásd még

+ [Szűrők az Azure Cognitive Search](search-filters.md)
+ [Nyelvi elemzők](/rest/api/searchservice/language-support)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Dokumentumok keresése – REST API](/rest/api/searchservice/search-documents)