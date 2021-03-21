---
title: Teljesítménnyel kapcsolatos javaslatok – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB Performance ajánlási funkcióját ismerteti
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: a20b8a43c8955e1afea7a7157e3e73425fb0d806
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664316"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for MariaDB-ben

**A következőkre vonatkozik:** Azure Database for MariaDB 10,2

A teljesítményre vonatkozó javaslatok szolgáltatás elemzi az adatbázisokat, hogy testreszabott javaslatokat hozzon létre a jobb teljesítmény érdekében. A javaslatok létrehozásához az elemzés különböző adatbázis-jellemzőket vizsgál, beleértve a sémát. Engedélyezze a [lekérdezési tárolót](concepts-query-store.md) a kiszolgálón a teljesítményre vonatkozó javaslatok szolgáltatás teljes körű kihasználásához. Ha a teljesítmény sémája ki van kapcsolva, a lekérdezés-tároló bekapcsolása lehetővé teszi a performance_schema és a szolgáltatáshoz szükséges teljesítmény-séma eszközök egy részhalmazát. A teljesítményre vonatkozó javaslat megvalósítása után tesztelje a teljesítményt a változások hatásának kiértékeléséhez.

## <a name="permissions"></a>Engedélyek

A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg a **teljesítményre vonatkozó javaslatokat** a MariaDB-kiszolgáló Azure Portal lapjának menüsorának **intelligens teljesítmény** szakaszában.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="A Teljesítménnyel kapcsolatos javaslatok kezdőlapja":::

Válassza **az elemzés lehetőséget, és válasszon** egy adatbázist, amely megkezdi az elemzést. A munkaterheléstől függően az elemzés több percet is igénybe vehet. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Az elemzés az adatbázis mélyreható vizsgálatát végzi. Javasoljuk, hogy az elemzést az időszakon kívüli időszakok alatt végezze el.

A **javaslatok** ablak a javaslatok listáját jeleníti meg, valamint a kapcsolódó lekérdezés azonosítóját, amely a javaslatot generálta. A lekérdezés AZONOSÍTÓjának használatával a [MySQL.query_store](concepts-query-store.md#mysqlquery_store) nézettel többet is megtudhat a lekérdezésről.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Teljesítményre vonatkozó javaslatok – új oldal":::

A javaslatok nem lesznek automatikusan alkalmazva. A javaslat alkalmazásához másolja a lekérdezés szövegét, és futtassa azt a választott ügyfélről. A javaslat kiértékeléséhez ne felejtse el tesztelni és figyelni.

## <a name="recommendation-types"></a>Javaslatok típusai

### <a name="index-recommendations"></a>Tárgymutató-javaslatok

Az indexelési javaslatok alapján új indexek *hozhatók létre* , amelyek felgyorsítják a számítási feladatok leggyakrabban futtatott vagy időigényes lekérdezéseit. Ehhez az ajánlási típushoz engedélyezni kell a [query Store](concepts-query-store.md) -t. A lekérdezési tároló gyűjti a lekérdezési adatokat, és megadja az elemzés által a javaslat végrehajtásához használt részletes lekérdezési futtatókörnyezetet és gyakorisági statisztikát.

### <a name="query-recommendations"></a>Javaslatok lekérdezése

A lekérdezési javaslatok arra utalnak, hogy a számítási feladatban lévő lekérdezések optimalizálása és újraírhatók. A MariaDB-lekérdezések elleni minták azonosításával és a velük való kijavításával az időigényes lekérdezések teljesítménye javítható. Ehhez az ajánlási típushoz engedélyezni kell a Query Store-t. A lekérdezési tároló gyűjti a lekérdezési adatokat, és megadja az elemzés által a javaslat végrehajtásához használt részletes lekérdezési futtatókörnyezetet és gyakorisági statisztikát.
## <a name="next-steps"></a>Következő lépések

- További információ a Azure Database for MariaDB [figyeléséről és hangolásáról](concepts-monitoring.md) .