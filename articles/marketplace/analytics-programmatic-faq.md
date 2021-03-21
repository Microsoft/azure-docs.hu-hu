---
title: Az elemzési adatszolgáltatások általános kérdéseinek programozott hozzáférése
description: Az elemzési adatoknak a partner Centerben való programozott elérésével kapcsolatos gyakori kérdések a kereskedelmi Marketplace-ajánlatokhoz.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583812"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Az elemzési adatszolgáltatások általános kérdéseinek programozott hozzáférése

Ez a cikk gyakran feltett kérdéseket tárgyal arról, hogy miként lehet programozott módon hozzáférni az elemzési adataihoz a partner Centerben a kereskedelmi piactéren kínált ajánlatokhoz.

## <a name="api-responses"></a>API-válaszok

Melyek azok a különböző forgatókönyvek, amelyeken a 200-től eltérő API-válaszok is fogadhatók (sikeres)?

Ez a táblázat ismerteti az API-válaszokat, és mi a teendő, ha megkapja őket.

| Hibaleírás | Hibakód | Hibaelhárítás |
| ------------ | ------------- | ------------- |
| Nem engedélyezett | 401 | Ez egy hitelesítési kivétel. Győződjön meg a Azure Active Directory (Azure AD) token helyességéről. Az Azure AD-jogkivonat 60 percre érvényes, amely után az Azure AD-tokent újra kell létrehoznia. |
| Érvénytelen táblanév | 400 | Az adatkészlet neve nem megfelelő. A "minden adatkészlet lekérése" API meghívásával adja meg újra az adatkészlet nevét. |
| Helytelen oszlopnév | 400| A lekérdezésben szereplő oszlop neve helytelen. Tekintse át az oszlop nevét az "összes adatkészlet beolvasása" API meghívásával, vagy tekintse meg az oszlopok nevét az alábbi táblázatokban:<br><ul><li>[Megrendelések részletei tábla](orders-dashboard.md#orders-details-table)</li><li>[Használat részletei tábla](usage-dashboard.md#usage-details-table)</li><li>[Ügyfél részletei tábla](customer-dashboard.md#customer-details-table)</li><li>[Marketplace-információk részleteinek táblázata](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Null vagy hiányzó érték | 400 | Előfordulhat, hogy az API kérésének részeként hiányzik a kötelező paraméterek egyike. |
| Érvénytelen jelentési paraméterek | 400 | Győződjön meg arról, hogy a jelentés paraméterei helyesek. Előfordulhat például, hogy a paraméternél kevesebb mint 4 értéket ad meg `RecurrenceInterval` . |
| Az ismétlődési időköznek 4 és 90 közöttinek kell lennie | 400 | Győződjön meg arról, hogy a `RecurrenceInterval` kérelem paraméterének értéke 4 és 90 között van. |
| Érvénytelen QueryId | 400 | A generált ismételt ellenőrzési módszer `QueryId` . |
| Érvénytelen jelentés-paraméterek a létrehozáshoz – a jelentés kezdési időpontjának legalább 4 órával az aktuális UTC időponttól számítva kell lennie. | 400 | A kérelem tartalmának részeként a kezdési idő paraméter nem lehet múltbeli. A jelentés kezdési időpontjának legalább 4 órával az aktuális UTC időponttól számítva kell lennie. |
| A kért "string" érték nem található | 400 | Győződjön meg arról, hogy frissítette-e a kérelem paramétereit `callbackurl` vagy a-t `format` . |
| Nem található a megadott szűrőkkel rendelkező tétel. | 404 | Tekintse át a `reportID` jelentés-végrehajtások beolvasása API-ban használt paramétert. |
| Nincsenek olyan végrehajtások, amelyek a megadott szűrési feltételekhez fordultak elő. Ellenőrizze újra a Jelentésazonosító vagy a executionId, majd ismételje meg az API-t a jelentés ütemezett végrehajtási ideje után | 404 | Győződjön meg arról, hogy a `reportId` helyes. Próbálja megismételni az API-t, miután megadta a jelentés ütemezett végrehajtási idejét a kérelem hasznos adatai között. |
| Belső hiba történt a jelentés létrehozásakor. Korrelációs azonosító <> | 500 | Győződjön meg arról, hogy a "kezdő", a "QueryStartTime" és a "QueryEndTime" mezők dátumának formátuma helyes. |
| A szolgáltatás nem érhető el | 500 | Ha folyamatosan kap egy szolgáltatást (5xx hiba), hozzon létre egy [támogatási jegyet](support.md). |
||||

## <a name="no-records"></a>Nincsenek rekordok

Az 200-es API-választ kapok, amikor letöltem a jelentést a biztonságos helyről. Miért nem kapok rekordokat?

Győződjön meg arról, hogy a lekérdezésben szereplő karakterlánc rendelkezik-e az oszlopfejléc egyik megengedett értékével. A lekérdezés például nulla eredményt ad vissza:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

Ebben a példában a SKUBillingType engedélyezett értékeinek kifizetése vagy ingyenes. A különböző oszlopok lehetséges értékeire a következő táblázatokban tájékozódhat:

- [Megrendelések részletei tábla](orders-dashboard.md#orders-details-table)
- [Használat részletei tábla](usage-dashboard.md#usage-details-table)
- [Ügyfél részletei tábla](customer-dashboard.md#customer-details-table)
- [Marketplace-információk részleteinek táblázata](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Következő lépések

- [A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k](analytics-available-apis.md)
