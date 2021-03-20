---
title: Az Azure Sentinel-figyelőlisták használata
description: Ez a cikk azt ismerteti, hogyan használható az Azure Sentinel-listák a fenyegetések vizsgálatára, az üzleti adatimportálásra, az engedélyezési és az események bővítésére.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555647"
---
# <a name="use-azure-sentinel-watchlists"></a>Az Azure Sentinel-figyelőlisták használata

> [!IMPORTANT]
> A lists szolgáltatás jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Az Azure Sentinel lists lehetővé teszi a külső adatforrásokból származó adatok összegyűjtését az Azure Sentinel-környezet eseményeivel való megfelelés érdekében. A létrehozást követően a keresés, az észlelési szabályok, a veszélyforrások elleni vadászat és a válasz forgatókönyvek használatával is használhatja a listát. A listák az Azure Sentinel-munkaterületen név-érték párokként tárolódnak, és az optimális lekérdezési teljesítmény és az alacsony késés érdekében vannak gyorsítótárazva.

A listák használatának gyakori forgatókönyvei a következők:

- A **fenyegetések kivizsgálása** és az incidensekre való reagálás gyorsan, az IP-címek, a fájlok kivonatai és a CSV-fájlokból származó egyéb adatok gyors importálásával. Az importálás után a List-Value párokat használhatja a riasztási szabályokban lévő illesztésekhez és szűrőkhöz, a fenyegetések vadászatához, a munkafüzetekhez, a jegyzetfüzetekhez és az általános lekérdezésekhez.

- **Üzleti adathalmazok importálása** listára. Például importálhatja a felhasználói listát a rendszerjogosultságú rendszerhozzáféréssel vagy a leállított alkalmazottakkal, majd a listával létrehozhat engedélyezési és letiltási listát, amelyekkel észlelhetők vagy letilthatók a felhasználók a hálózatra.

- A **riasztások fáradtságának csökkentése**. Hozzon létre engedélyezési listát, amely letiltja a riasztásokat a felhasználók egy csoportjából, például olyan meghatalmazott IP-címekről származó felhasználókat, amelyek általában a riasztást kiváltó feladatokat látnak el, és megakadályozzák, hogy a jóindulatú események riasztások legyenek.

- **Az események bővítése**. A listák használatával gazdagíthatja az események adatait a külső adatforrásokból származtatott név-érték kombinációkkal.

## <a name="create-a-new-watchlist"></a>Új listának létrehozása

1. A Azure Portal navigáljon az **Azure Sentinel**  >  **Configuration**  >  **listához** , majd válassza az **új hozzáadása** elemet.

    > [!div class="mx-imgBorder"]
    > ![új List](./media/watchlists/sentinel-watchlist-new.png)

1. Az **általános** lapon adja meg a listához a nevet, a leírást és az aliast, majd kattintson a **tovább** gombra.

    > [!div class="mx-imgBorder"]
    > ![List General lap](./media/watchlists/sentinel-watchlist-general.png)

1. A **forrás** lapon válassza ki az adatkészlet típusát, töltsön fel egy fájlt, majd kattintson a **tovább** gombra.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="List forrás lapja" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > A fájlfeltöltés jelenleg legfeljebb 3,8 MB méretű fájlokra korlátozódik.

1. Tekintse át az adatokat, ellenőrizze, hogy helyes-e, majd válassza a **Létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![List felülvizsgálati oldal](./media/watchlists/sentinel-watchlist-review.png)

    Megjelenik egy értesítés, ha a listát létrehozták.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="List sikeres létrehozási értesítés" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>A lists használata a lekérdezésekben

1. A Azure Portal navigáljon az **Azure Sentinel**  >  **Configuration**  >  **listához**, válassza ki a használni kívánt listát, majd válassza a **nézet lehetőséget log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="a lists használata a lekérdezésekben" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. A rendszer automatikusan kibontja a listához tartozó elemeket a lekérdezéshez, és az **eredmények** lapon fog megjelenni. Az alábbi példa a **servername** és az **IP** -cím mezők kinyerésének eredményét mutatja.

    > [!NOTE]
    > A lekérdezések időbélyege figyelmen kívül lesz hagyva a lekérdezés felhasználói felületén és az ütemezett riasztásokban is.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="lekérdezések a listával – mezők" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. A listáról bármely táblázat adatait lekérdezheti, ha a listával összekapcsolja az illesztések és a keresések táblázatát.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="lekérdezések a listára kereséssel":::

## <a name="use-watchlists-in-analytics-rules"></a>A lists használata az elemzési szabályokban

Ha az elemzési szabályokban a felsorolásokat szeretné használni, a Azure Portalból navigáljon az **Azure Sentinel**  >  **Configuration**  >  **Analytics** szolgáltatáshoz, és hozzon létre egy szabályt a `_GetWatchlist('<watchlist>')` lekérdezésben szereplő függvénnyel.

1. Ebben a példában hozzon létre egy "ipwatchlist" nevű listát a következő értékekkel:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="a listához tartozó négy elem listája":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="a List létrehozása négy elemmel":::

1. Ezután hozza létre az elemzési szabályt.  Ebben a példában csak a listához tartozó IP-címekről származó eseményeket vesszük fel:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="a lists használata az elemzési szabályokban":::

## <a name="view-list-of-watchlists-aliases"></a>Listázási aliasok listájának megtekintése

A listához tartozó-aliasok listájának lekéréséhez a Azure Portalból navigáljon az **Azure Sentinel**  >  **általános**  >  **naplóihoz**, és futtassa a következő lekérdezést: `_GetWatchlistAlias` . Az aliasok listáját a **Results (eredmények** ) lapon tekintheti meg.

> [!div class="mx-imgBorder"]
> ![listák listázása](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan használhatja a listát az Azure Sentinelben az adatelemzéshez és a vizsgálatok fejlesztéséhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
