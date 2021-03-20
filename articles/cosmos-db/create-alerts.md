---
title: Riasztások létrehozása Azure Cosmos DBhoz a Azure Monitor használatával
description: Megtudhatja, hogyan állíthatja be a Azure Cosmos DB riasztásait Azure Monitor használatával.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339546"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Riasztások létrehozása Azure Cosmos DBhoz a Azure Monitor használatával
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A riasztások az ismétlődő tesztek beállítására szolgálnak a Azure Cosmos DB erőforrások rendelkezésre állásának és reagálásának figyeléséhez. A riasztások e-mailben küldhetnek értesítést, vagy futtathatnak egy Azure-függvényt, ha az egyik mérőszám eléri a küszöbértéket, vagy ha egy adott eseményt naplóznak a tevékenység naplójában.

Riasztást a metrikák, illetve az Azure Cosmos-fiókjában található műveletnapló-események alapján fogadhat:

* **Metrikák** – a riasztás akkor aktiválódik, ha egy adott metrika értéke átlépi a hozzárendelt küszöbértéket. Például, ha a felhasznált kérelmek teljes mennyisége meghaladja a 1000 RU/s-t. Ez a riasztás akkor is aktiválódik, ha a feltétel először teljesül, majd ezt követően, ha a feltétel már nem teljesül. Tekintse meg a [figyelési adatok referenciáját](monitor-cosmos-db-reference.md#metrics) ismertető cikket a Azure Cosmos DBban elérhető különböző mérőszámokról.

* **Műveletnapló eseményei** – ez a riasztás egy adott esemény bekövetkezésekor aktiválódik. Például, ha az Azure Cosmos-fiók kulcsai hozzáférnek vagy frissülnek.

Riasztásokat állíthat be a Azure Cosmos DB ablaktáblán vagy a Azure Portal Azure Monitor szolgáltatásban. Mindkét csatoló ugyanezeket a beállításokat kínálja. Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Cosmos DB riasztásait Azure Monitor használatával.

## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre riasztást, ha a 429-as HTTP-állapotkódot kapja, amely akkor érkezik, amikor a kérések díjszabása korlátozott. Előfordulhat például, hogy riasztást szeretne kapni, ha 100 vagy több korlátozott számú kérelem van. Ebből a cikkből megtudhatja, hogyan konfigurálhat egy riasztást ilyen forgatókönyvhöz a HTTP-állapotkód használatával. A hasonló lépések használatával más típusú riasztásokat is konfigurálhat, csak a követelmény alapján kell választania egy másik feltételt.

1. Jelentkezzen be a [Azure Portalba.](https://portal.azure.com/)

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **riasztások** lehetőséget.

1. Az új riasztási szabály gomb megnyomásával nyissa meg a riasztási szabály létrehozása panelt.  

1. Töltse ki a **hatókör** szakaszt:

   * Nyissa meg az **erőforrás kiválasztása** ablaktáblát, és konfigurálja a következőket:

   * Válassza ki az **előfizetés** nevét.

   * Válassza ki **Azure Cosmos db fiókokat** az **erőforrás típusához**.

   * Az Azure Cosmos-fiók **helye** .

   * A részletek kitöltése után megjelenik a kiválasztott hatókörben található Azure Cosmos-fiókok listája. Válassza ki azt, amelyhez riasztásokat szeretne konfigurálni, majd válassza a **kész** lehetőséget.

1. Töltse ki a **feltétel** szakaszt:

   * A **feltétel kiválasztása** panel megnyitásával nyissa meg a **jel logikai beállítása** lapot, és konfigurálja a következőket:

   * Válasszon ki egy jelet. A **jel típusa** lehet **metrika** vagy **tevékenység naplója**. Válassza ki a forgatókönyv **mérőszámait** . Mivel riasztást szeretne kapni, ha a kérelmek maximális száma metrikával van korlátozva.

   * Az **összes** kijelölése a **figyelő szolgáltatáshoz**

   * Válassza ki a **jel nevét**. Ha riasztást szeretne kapni a HTTP-állapotkódok esetében, válassza ki a **kérelmek teljes számát jelző számot** .

   * A következő lapon megadhatja a riasztás indítására szolgáló logikát, és a diagram használatával megtekintheti az Azure Cosmos-fiók trendjét. A **kérelmek teljes** mérőszáma támogatja a dimenziókat. Ezek a méretek lehetővé teszik a mérőszám szűrését. Ha nincs kiválasztva dimenzió, a rendszer figyelmen kívül hagyja ezt az értéket.

   * A **dimenzió neveként** válassza a **statuscode** lehetőséget. Válassza az **Egyéni érték hozzáadása** lehetőséget, és állítsa az állapotkódot 429-re.

   * A **riasztási logikában** állítsa a **küszöbértéket** **statikusra**. A statikus küszöbérték felhasználó által definiált küszöbértéket használ a szabály kiértékeléséhez, míg a dinamikus küszöbértékek a beépített gépi tanulási algoritmusok segítségével folyamatosan megismerhetik a mérőszám viselkedési mintáját, és automatikusan kiszámítják a küszöbértékeket.

   * Állítsa az **operátort** **nagyobb** értékre, az **összesítési típust** a **Total** értékre, a küszöbértéket pedig a **100** **értékre** . Ezzel a logikával, ha az ügyfél több mint 100, 429-as állapotkóddal rendelkező kérelmet lát, a riasztás aktiválódik. Az Összesítés típusát, az Összesítés részletességét és az értékelés gyakoriságát is konfigurálhatja a követelmények alapján.

   * Az űrlap kitöltése után válassza a **kész** lehetőséget. Az alábbi képernyőképen a riasztás logikájának részletei láthatók:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Állítsa be a logikát, hogy riasztásokat kapjon a ráta Limited/429-kérelmek esetében":::

1. Töltse ki a **műveleti csoport** szakaszt:

   * A **szabály létrehozása** panelen válasszon ki egy meglévő műveleti csoportot, vagy hozzon létre egy új műveleti csoportot. A műveleti csoport lehetővé teszi, hogy meghatározza a riasztási feltétel bekövetkezésekor végrehajtandó műveletet. Ebben a példában hozzon létre egy új műveleti csoportot, amely e-mailben értesítést kap a riasztás indításakor. Nyissa meg a **műveleti csoport hozzáadása** panelt, és adja meg a következő adatokat:

   * **Műveleti csoport neve** – a műveleti csoport nevének egyedinek kell lennie az erőforráscsoport területén.

   * **Rövid név** – a műveleti csoport rövid neve, ez az érték szerepel az e-mailek és az SMS-értesítések között, amelyek alapján megállapítható, hogy melyik műveleti csoport volt az értesítés forrása.

   * Válassza ki az előfizetést és azt az erőforráscsoportot, amelyben ez a műveleti csoport létrejön.  

   * Adja meg a művelet nevét, és válassza az **e-mail/SMS-üzenet/leküldés/hang** lehetőséget a **művelet típusaként**. A következő képernyőkép a Művelettípus részleteit mutatja be:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="A riasztás fogadásához adja meg a művelet típusát (például e-mail értesítés)":::

1. Töltse ki a **riasztási szabály részletei** szakaszt:

   * Adja meg a szabály nevét, adjon meg egy opcionális leírást, a riasztás súlyossági szintjét, adja meg, hogy engedélyezi-e a szabályt a szabályok létrehozásakor, majd válassza a **szabály létrehozása riasztás** elemet a metrikai szabály riasztásának létrehozásához.

A riasztás létrehozása után 10 percen belül aktív lesz.

## <a name="common-alerting-scenarios"></a>Gyakori riasztási forgatókönyvek

Az alábbiakban néhány példát láthat a riasztások használatára:

* Ha az Azure Cosmos-fiók kulcsai frissülnek.
* Ha egy tároló, adatbázis vagy régió adat-vagy index-használata meghaladja a megadott számú bájtot.
* Ha a normalizált RU/s-felhasználás nagyobb, mint bizonyos százalékban. A normalizált RU-használati metrika a maximális átviteli sebességet adja meg egy replikakészlet-készleten belül. További információ: a [normalizált ru/s-cikkek figyelése](monitor-normalized-request-units.md) .  
* Egy régió hozzáadásakor, eltávolításakor vagy offline állapotba helyezésekor.
* Adatbázis vagy tároló létrehozásakor, törlésekor vagy frissítésekor.
* Az adatbázis vagy a tároló átviteli sebessége megváltozik.

## <a name="next-steps"></a>Következő lépések

* A [normalizált ru/s metrika figyelése](monitor-normalized-request-units.md) az Azure Cosmos-tárolóban.
* A művelet [teljesítményének figyelése, illetve a művelet kérése](monitor-request-unit-usage.md) Azure Cosmos DBban.