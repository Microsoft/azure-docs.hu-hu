---
title: Az adatbázis-tanácsadó teljesítményével kapcsolatos javaslatok Azure SQL Database
description: Azure SQL Database olyan adatbázisokra vonatkozó ajánlásokat tartalmaz, amelyek javíthatják a lekérdezési teljesítményt a Azure SQL Databaseban.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500852"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>A Azure SQL Database teljesítményére vonatkozó javaslatok Database Advisor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database tanul és alkalmazkodik az alkalmazáshoz. A Azure SQL Database számos adatbázis-tanácsadóval rendelkezik, amelyek testreszabott javaslatokat nyújtanak a teljesítmény maximalizálása érdekében. Ezek az adatbázis-tanácsadók folyamatosan felmérik és elemzik a használati előzményeket, és a teljesítmény javítása érdekében a munkaterhelés-mintákon alapuló javaslatokat nyújtanak.

## <a name="performance-overview"></a>Teljesítmény áttekintése

A teljesítmény áttekintése áttekintést nyújt az adatbázis teljesítményéről, és segít a teljesítmény finomhangolásában és a hibaelhárításban.

![A Azure SQL Database teljesítményének áttekintése](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- A **javaslatok** csempéje az adatbázis hangolási javaslatainak részletezését biztosítja (a három legfontosabb javaslat látható, ha van több). A csempére kattintva megtekintheti a **[teljesítményre vonatkozó javaslat beállításait](database-advisor-find-recommendations-portal.md#viewing-recommendations)**.
- A **hangolási tevékenység** csempe összefoglalja az adatbázis folyamatban lévő és befejezett hangolási műveleteit, így gyors áttekintést nyújt a hangolási tevékenység előzményeiről. Erre a csempére kattintva megtekintheti az adatbázis teljes hangolási előzményeit.
- Az **automatikus hangolás** csempéje az adatbázis **[Automatikus hangolási konfigurációját](automatic-tuning-enable.md)** jeleníti meg (az adatbázisra automatikusan alkalmazott hangolási beállításokat). A csempére kattintva megnyílik az Automation konfigurálása párbeszédpanel.
- Az **adatbázis-lekérdezések** csempe az adatbázis lekérdezési teljesítményének összegzését jeleníti meg (a teljes DTU-használat és a legfelső szintű erőforrás-felhasználású lekérdezések esetében). A csempére kattintva **[lekérdezési terheléselemző](query-performance-insight-use.md)**.

## <a name="performance-recommendation-options"></a>Teljesítménnyel kapcsolatos javaslatok beállításai

A Azure SQL Databaseban elérhető teljesítmény-javaslati lehetőségek a következők:

| Teljesítményre vonatkozó javaslat | Önálló adatbázis és készletezett adatbázis-támogatás | Példány-adatbázis támogatása |
| :----------------------------- | ----- | ----- |
| **Tárgymutató-javaslatok létrehozása** – olyan indexek létrehozását javasolja, amelyek javíthatják a számítási feladatok teljesítményét. | Igen | Nem |
| A **drop index ajánlásai** – a redundáns és ismétlődő indexek napi eltávolítását javasolja, kivéve azokat az egyedi indexeket és indexeket, amelyeket hosszú ideje nem használtak (>90 nap). Vegye figyelembe, hogy ez a beállítás nem kompatibilis a partíciós váltást és az indexelési tippeket használó alkalmazásokkal. A nem használt indexek nem támogatottak a prémium és üzletileg kritikus szolgáltatási szinteken. | Igen | Nem |
| **Parametrizálja-lekérdezések ajánlásai (előzetes verzió)** – a kényszerített paraméterezés ajánlja olyan esetekben, amikor egy vagy több olyan lekérdezéssel rendelkezik, amely állandóan újrafordításra kerül, de ugyanazzal a lekérdezés-végrehajtási tervvel fejeződik be. | Igen | Nem |
| A **séma problémáinak javítása javaslatok (előzetes verzió)** – a séma javítására vonatkozó javaslatok akkor jelennek meg, ha Azure SQL Database észleli az adatbázison előforduló séma-alapú SQL-hibák számát. A Microsoft jelenleg "a séma hibájának javítása" javaslatok elavult. | Igen | Nem |

![Teljesítménnyel kapcsolatos javaslatok a Azure SQL Database](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

A teljesítménnyel kapcsolatos javaslatok alkalmazásához tekintse meg a [javaslatok alkalmazása](database-advisor-find-recommendations-portal.md#applying-recommendations)című témakört. A javaslatok állapotának megtekintéséhez lásd: [figyelési műveletek](database-advisor-find-recommendations-portal.md#monitoring-operations).

A múltban alkalmazott hangolási műveletek teljes előzményeit is megtalálhatja.

## <a name="create-index-recommendations"></a>Tárgymutató-javaslatok létrehozása

Azure SQL Database folyamatosan figyeli a futó lekérdezéseket, és azonosítja azokat az indexeket, amelyek javíthatják a teljesítményt. Ha elég biztos abban, hogy egy adott index hiányzik, létrejön egy új **create index** -javaslat.

Azure SQL Database megbízhatóságot épít az index teljesítményének becslésével. A becsült teljesítménynövekedéstől függően az ajánlások a magas, közepes vagy alacsony kategóriába tartoznak.

A javaslatok használatával létrehozott indexek mindig automatikusan létrehozott indexként vannak megjelölve. Láthatja, hogy mely indexek automatikusan jönnek létre a [sys. Indexes nézet megtekintésével](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Az automatikusan létrehozott indexek nem gátolják meg a MÓDOSÍTÁSi/ÁTNEVEZÉSi parancsokat.

Ha úgy próbálja eldobni az oszlopot, hogy az automatikusan létrehozott indextel rendelkezik, a parancs áthalad. Az automatikusan létrehozott index el lesz dobva a paranccsal is. A normál indexek letiltják az ALTER/Átnevezés parancsot az indexelt oszlopokon.

A Create index javaslat alkalmazása után Azure SQL Database összehasonlítja a lekérdezések teljesítményét az alapteljesítménysel. Ha az új index jobb teljesítményt biztosít, a javaslat sikeresként van megjelölve, és a hatás jelentés elérhető. Ha az index nem javítja a teljesítményt, a rendszer automatikusan visszaállította. Azure SQL Database ezzel a folyamattal biztosítja, hogy a javaslatok javítsák az adatbázis teljesítményét.

A **create index** javaslat olyan biztonsági szabályzatot tartalmaz, amely nem engedélyezi a javaslat alkalmazását, ha egy adatbázis vagy készlet erőforrás-használata magas. A back-off szabályzat a CPU-t, az adatio-t, a log IO-t és a rendelkezésre álló tárolót veszi figyelembe.

Ha a CPU, az adatio vagy a log IO nagyobb, mint 80% az elmúlt 30 percben, a Create index javaslat Elhalasztva lesz. Ha a rendelkezésre álló tárterület az index létrehozása után 10% alá esik, a javaslat hibás állapotba kerül. Ha néhány nap elteltével az automatikus hangolás továbbra is úgy véli, hogy az index hasznos lenne, a folyamat újra elindul.

Ez a folyamat addig ismétlődik, amíg nincs elég elérhető tárterület egy index létrehozásához, vagy amíg az index nem látszik hasznosnak.

## <a name="drop-index-recommendations"></a>Tárgymutató-javaslatok eldobása

A hiányzó indexek észlelése mellett Azure SQL Database folyamatosan elemzi a meglévő indexek teljesítményét. Ha a rendszer nem használ indexet, Azure SQL Database javasolja a ledobását. Az indexek eldobása két esetben ajánlott:

- Az index egy másik index duplikálása (azonos indexelt és belefoglalt oszlop, partíciós séma és szűrők).
- Az indexet nem használták hosszabb ideig (93 nap).

Az indexek eldobására vonatkozó javaslatok a megvalósítás után is meghaladják az ellenőrzést. Ha a teljesítmény javul, a hatás jelentés elérhető. Ha a teljesítmény csökken, a rendszer visszaállít egy javaslatot.

## <a name="parameterize-queries-recommendations-preview"></a>Parametrizálja-lekérdezések javaslatai (előzetes verzió)

A *parametrizálja-lekérdezések* javaslatai akkor jelennek meg, ha egy vagy több olyan lekérdezést tartalmaz, amely állandóan újrafordításra kerül, de ugyanazzal a lekérdezés-végrehajtási tervvel végződik. Ez a feltétel létrehoz egy lehetőséget a kényszerített paraméterezés alkalmazására. A kényszerített paraméterezés lehetővé teszi a lekérdezési tervek gyorsítótárazását és újbóli felhasználását a jövőben, ami javítja a teljesítményt, és csökkenti az erőforrás-használatot.

Először minden lekérdezést le kell fordítani egy végrehajtási terv létrehozásához. Minden létrehozott csomag hozzá lesz adva a terv gyorsítótárába. Ugyanannak a lekérdezésnek a későbbi végrehajtásai újra felhasználhatják ezt a csomagot a gyorsítótárból, így nincs szükség további fordításra.

A nem paraméteres értékekkel rendelkező lekérdezések teljesítménybeli terheléshez vezethetnek, mert a végrehajtási tervet minden alkalommal újra kell fordítani, amikor a nem paraméteres értékek eltérnek. Sok esetben előfordul, hogy ugyanaz a lekérdezés eltérő paraméterértékekkel ugyanazokat a végrehajtási terveket hozza létre. Ezek a tervek azonban továbbra is külön kerülnek be a terv gyorsítótárába.

A végrehajtási tervek újrafordításának folyamata adatbázis-erőforrásokat használ, növeli a lekérdezés időtartamát, és túltelíti a terv gyorsítótárát. Ezek az események így a tervek a gyorsítótárból való kiürítését okozzák. Ez a viselkedés úgy módosítható, ha a kényszerített paraméterezés beállítást állítja be az adatbázison.

A javaslat hatásának becslése érdekében a rendszer összehasonlítja a tényleges CPU-használat és a tervezett CPU-használat közötti összehasonlítást (ahogy a javaslat alkalmazta volna). Ez a javaslat segítséget nyújt a CPU-megtakarítás megszerzésében. Emellett segít csökkenteni a lekérdezési időtartamot és a terhelést a csomag gyorsítótárában, ami azt jelenti, hogy több csomag is maradhat a gyorsítótárban, és újra felhasználható. Ezt az ajánlást gyorsan alkalmazhatja az **Apply** parancs kiválasztásával.

A javaslat alkalmazása után a kényszerített paraméterezés perceken belül elérhetővé válik az adatbázisban. Elindítja a figyelési folyamatot, amely körülbelül 24 órát tart. Ezen időszak után megtekintheti az ellenőrzési jelentést. Ez a jelentés az adatbázis CPU-használatát jeleníti meg a javaslat alkalmazása előtt és után 24 órával. A Azure SQL Database Advisor olyan biztonsági mechanizmussal rendelkezik, amely automatikusan visszaállít egy alkalmazott javaslatot, ha a rendszer a teljesítmény regresszióját észlelte.

## <a name="fix-schema-issues-recommendations-preview"></a>A séma problémáinak javítása javaslatok (előzetes verzió)

> [!IMPORTANT]
> A Microsoft jelenleg "a séma hibájának javítása" javaslatok elavult. Javasoljuk, hogy az adatbázis teljesítményével kapcsolatos problémák figyeléséhez [Intelligent Insights](intelligent-insights-overview.md) használjon, beleértve a sémával kapcsolatos problémákat is, amelyek a korábban lefedett "séma-probléma" megoldásra vonatkoznak.

Ha Azure SQL Database észleli az adatbázisban előforduló séma-alapú SQL-hibák számát, akkor a **sémával kapcsolatos problémák megoldására** vonatkozó javaslatok jelennek meg. Ez az ajánlás általában akkor jelenik meg, ha az adatbázis több sémával kapcsolatos hibát észlel (érvénytelen oszlopnév, érvénytelen objektumnév stb.) egy órán belül.

A "séma problémái" a szintaktikai hibák osztálya. Akkor fordulnak elő, ha az SQL-lekérdezés definíciója és az adatbázis-séma definíciója nincs igazítva. Előfordulhat például, hogy a lekérdezés által várt oszlopok egyike hiányzik a célként megadott táblából, vagy fordítva.

A "séma javítása" javaslat akkor jelenik meg, ha Azure SQL Database észleli az adatbázison futó, a sémával kapcsolatos SQL-hibák számát. A következő táblázat a séma problémáira vonatkozó hibákat mutatja be:

| SQL-hibakód | Üzenet |
| --- | --- |
| 201 |A (z) "" eljárás vagy függvény a (z) ""*paramétert várja*, amely nem lett megadva. |
| 207 |Érvénytelen oszlopnév: "*". |
| 208 |Érvénytelen objektumnév: "*". |
| 213 |Az oszlop neve vagy a megadott értékek száma nem felel meg a tábla definíciójának. |
| 2812 |A következő tárolt eljárás nem található: "*". |
| 8144 |Az eljárás vagy a függvény túl sok argumentumot adott meg. |

## <a name="custom-applications"></a>Egyéni alkalmazások

A fejlesztők fontolóra vehetik az egyéni alkalmazások fejlesztését a Azure SQL Database teljesítményével kapcsolatos javaslatok használatával. Az adatbázis-portálon felsorolt összes javaslat a [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API-n keresztül érhető el.

## <a name="next-steps"></a>Következő lépések

- Az adatbázis-indexek és a lekérdezés-végrehajtási tervek automatikus finomhangolásával kapcsolatos további információkért lásd: [Azure SQL Database automatikus hangolás](automatic-tuning-overview.md).
- Az adatbázis teljesítményének automatikus és a teljesítménnyel kapcsolatos problémák kiváltó okának elemzésével kapcsolatos további információkért lásd: [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
- A leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megismeréséhez tekintse meg a [lekérdezési teljesítmény](query-performance-insight-use.md) elemzését ismertető témakört.