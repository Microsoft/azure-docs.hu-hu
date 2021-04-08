---
title: Válassza ki a kiépített átviteli sebesség és a kiszolgáló nélküli lehetőséget Azure Cosmos DB
description: További információ a kiépített átviteli sebesség és a számítási feladatok kiszolgáló nélküli kiválasztásáról.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3f5c3400f319a3f9d5f1544457b009f90d479634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98049830"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>A kiépített átviteli sebesség és a kiszolgáló nélküli választás
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB két különböző kapacitású üzemmódban érhető el: [kiépített átviteli sebesség](set-throughput.md) és [kiszolgáló](serverless.md)nélküli. Mindkét módban ugyanazokat az adatbázis-műveleteket hajthatja végre, de a műveletek számlázásának módja gyökeresen eltérő lehet. Az alábbi videó ismerteti az ilyen üzemmódok közötti alapvető különbségeket, valamint azt, hogy a különböző típusú munkaterhelések hogyan illeszkednek egymáshoz:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Részletes összehasonlítás

| Feltételek | Kiosztott átviteli sebesség | Kiszolgáló nélküli |
| --- | --- | --- |
| Állapot | Általánosan elérhető | Előzetes verzióban |
| Legmegfelelőbb a következőhöz: | Kiszámítható teljesítményt igénylő, tartós forgalommal rendelkező munkaterhelések | Időszakos vagy kiszámíthatatlan forgalommal rendelkező munkaterhelések, valamint az alacsony átlag – csúcs forgalmi arány |
| Működés | Az egyes tárolók esetében bizonyos mennyiségű átviteli sebességet kell kiépíteni a [kérelmek](request-units.md) száma másodpercenként. Másodpercenként ez a kérelmek mennyisége elérhető az adatbázis műveleteihez. A kiépített átviteli sebesség manuálisan frissíthető, és automatikus [méretezéssel](provision-throughput-autoscale.md)automatikusan módosítható. | Az adatbázis-műveleteket a tárolók nélkül futtathatja anélkül, hogy kapacitást kellene kiépíteni. |
| Földrajzi eloszlás | Elérhető (korlátlan számú Azure-régió) | Nem érhető el (a kiszolgáló nélküli fiókok csak 1 Azure-régióban futhatnak) |
| Tárolók maximális száma | Korlátlan | 50 GB |
| Teljesítmény | < 10 MS késés az SLA által jelzett pont-olvasási és írási műveletekhez | < 10 MS késés a Point-olvasások és a < 30 MS esetében az SLO által jelzett írásokhoz |
| Számlázási modell | A számlázás óránként, a felhasznált RU/s alapján történik, függetlenül attól, hogy hány RUs lett felhasználva. | A számlázás óránként történik az adatbázis műveletei által felhasznált RUs mennyisége alapján. |

> [!IMPORTANT]
> A kiszolgáló nélküli korlátozások némelyike enyhíthető vagy eltávolítható, ha a kiszolgáló nélküli szolgáltatás általánosan elérhetővé válik, és **a visszajelzés** segít nekünk eldönteni! Forduljon a kiszolgáló nélküli felhasználói élményhez: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>A várt felhasználás becslése

Bizonyos helyzetekben előfordulhat, hogy nem egyértelmű, hogy egy adott számítási feladathoz ki kell-e választani a kiépített átviteli sebességet vagy a kiszolgáló nélküli forgalmat. Ha segítségre van szüksége a döntéshez, megbecsülheti a teljes **várható felhasználást**, azaz a hónap során felhasználható RUs teljes számát (ezt megbecsülheti az [itt](plan-manage-costs.md#estimating-serverless-costs)látható táblázat segítségével)

**1. példa**: a számítási feladat várhatóan legfeljebb 500 ru/s értékre vált, és egy hónapban összesen 20 000 000 RUs-t használ fel.

- A kiépített átviteli sebességi üzemmódban a 500 RU/s-tárolót a következő havi költséggel lehet kiépíteni: $0,008 * 5 * 730 = **$29,20**
- Kiszolgáló nélküli módban a felhasznált RUs: $0,25 * 20 = **$5,00**

**2. példa**: a számítási feladat várhatóan legfeljebb 500 ru/s értékre vált, és egy hónapban összesen 250 000 000 RUs-t használ fel.

- A kiépített átviteli sebességi üzemmódban a 500 RU/s-tárolót a következő havi költséggel lehet kiépíteni: $0,008 * 5 * 730 = **$29,20**
- Kiszolgáló nélküli módban a felhasznált RUs: $0,25 * 250 = **$62,50**

(ezek a példák nem jelentik a tárolási költségeket, ami megegyezik a két mód között)

> [!NOTE]
> Az előző példában bemutatott költségek csak demonstrációs célokat szolgálnak. A legfrissebb díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="next-steps"></a>Következő lépések

- További információ az [adatforgalom kiépítési teljesítményéről Azure Cosmos db](set-throughput.md)
- További információ a [Azure Cosmos db kiszolgáló](serverless.md) nélküli használatáról
- Ismerkedjen meg a [kérési egységek](request-units.md) fogalmával
