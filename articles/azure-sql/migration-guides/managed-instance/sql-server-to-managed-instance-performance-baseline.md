---
title: 'SQL Server a felügyelt SQL-példányhoz: teljesítmény elemzése'
description: Ismerje meg, hogyan hozhatja létre és hasonlíthatja össze a teljesítmény alapkonfigurációját, amikor áttelepíti SQL Server adatbázisait az Azure SQL felügyelt példányára.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: 28eedd3a22a0a5adc6e9a37c9e0f0fa0214a4e47
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564439"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Áttelepítési teljesítmény: SQL Server a felügyelt SQL-példányok teljesítmény-elemzéséhez
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Hozzon létre egy alapkonfigurációt, amely összehasonlítja a számítási feladatok teljesítményét egy SQL felügyelt példányon a SQL Serveron futó eredeti munkaterheléssel. 

## <a name="create-a-baseline"></a>Alapterv létrehozása

Ideális esetben a teljesítmény a Migrálás után hasonló vagy jobb, ezért fontos, hogy az alapértékeket a forráson méri és jegyezze fel, majd összehasonlítsa őket a célként megadott környezettel. A teljesítmény-alapterv olyan paraméterek összessége, amelyek meghatározzák az átlagos számítási feladatot a forráson. 

Válasszon ki egy fontos és az üzleti számítási feladatnak megfelelő lekérdezési készletet. Mérje fel és dokumentálja a lekérdezésekhez tartozó minimális/átlagos/maximális időtartamot és CPU-használatot, valamint a forráskiszolgáló teljesítmény-metrikáit, például az átlagos/maximális CPU-használatot, az átlagos/maximális lemez i/o-késést, az átviteli sebességet, a IOPS, az átlagos/maximális oldal élettartamát, valamint a tempdb átlagos maximális méretét. 

A következő erőforrások segíthetnek a teljesítmény alaptervének definiálásában: 

   - [CPU-használat figyelése ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Memóriahasználat](/sql/relational-databases/performance-monitor/monitor-memory-usage)   figyelése és határozza meg a különböző összetevők által használt memória mennyiségét, például a puffer készletét, a terv gyorsítótárát, az erőforráskészlet-készletet, [a memóriabeli OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)stb. Emellett meg kell találnia az oldal átlagos és csúcsérték értékét a memória várható élettartama teljesítményszámláló számára. 
   - A lemez i/o-használatának figyelése a forrás SQL Server példányon a [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   nézet vagy [teljesítményszámlálók](/sql/relational-databases/performance-monitor/monitor-disk-usage)használatával. 
   - A számítási feladatok és a lekérdezési teljesítmény figyelése a dinamikus felügyeleti nézetek (vagy a lekérdezési tároló) vizsgálatával, ha SQL Server 2016-es és újabb verzióról végez áttelepítést). Azonosítsa a számítási feladatok legfontosabb lekérdezésének átlagos időtartamát és CPU-felhasználását. 

A forrás-SQL Server teljesítményével kapcsolatos problémákat a Migrálás előtt kell kezelni. Az ismert problémák bármely új rendszerbe történő áttelepítése váratlan eredményeket eredményezhet, és érvénytelenítheti a teljesítmény összehasonlítását. 


## <a name="compare-performance"></a>Teljesítmény összehasonlítása 

Miután meghatározta az alapkonfigurációt, hasonlítsa össze a hasonló számítási feladatok teljesítményét a célként megadott SQL felügyelt példányon. A pontosság érdekében fontos, hogy az SQL felügyelt példányának környezete a lehető legnagyobb mértékben összehasonlítható legyen a SQL Server környezettel. 

A felügyelt SQL-példányok infrastruktúrájának eltérései pontosan nem valószínűek, hogy a megfelelő teljesítményt használják. Előfordulhat, hogy egyes lekérdezések a vártnál gyorsabban futnak, míg mások lassabbak lehetnek. Ennek az összehasonlításnak a célja annak ellenőrzése, hogy a felügyelt példány terhelési teljesítménye megfelel-e SQL Server (átlagos) teljesítményének, valamint az eredeti teljesítménnyel nem egyező teljesítménnyel rendelkező kritikus lekérdezések azonosításának. 

A teljesítmény-összehasonlítás valószínűleg a következő eredményeket eredményezi: 

- A felügyelt példányon a munkaterhelés teljesítménye igazodik, vagy jobb, mint a forrás-SQL Server munkaterhelés-teljesítménye. Ebben az esetben sikeresen megerősítette, hogy az áttelepítés sikeres volt. 

- A teljesítménnyel kapcsolatos paraméterek és lekérdezések többsége a vártnak megfelelően működik, és bizonyos kivételek miatt csökkenhet a teljesítmény. Ebben az esetben azonosítsa a különbségeket és azok fontosságát. Ha van olyan fontos lekérdezés, amely csökkentett teljesítményű, akkor vizsgálja meg, hogy az alapul szolgáló SQL-csomagok módosultak-e, vagy hogy a lekérdezések elérik-e az erőforrás-korlátokat. Ezt úgy is elháríthatja, ha a kritikus lekérdezésekre mutató javaslatokat alkalmaz (például a kompatibilitási szint módosítása, az örökölt kardinális kalkulátor) vagy közvetlenül, vagy a tervezési útmutatók használatával. Győződjön meg arról, hogy a statisztikák és az indexek naprakészek és egyenértékűek mindkét környezetben. 

- A legtöbb lekérdezés lassabb egy felügyelt példányon a forrás-SQL Server példánnyal összehasonlítva. Ebben az esetben próbálja meg megállapítani a különbség kiváltó okait, például az [erőforrás-korlátok elérését](../../managed-instance/resource-limits.md#service-tier-characteristics) , például az IO, a memória vagy a példány naplózási korlátját. Ha nincs olyan erőforrás-korlátozás, amely a különbséget okozza, próbálja meg módosítani az adatbázis kompatibilitási szintjét, vagy módosítsa az adatbázis beállításait, például a örökölt kardinális becslést, majd futtassa újra a tesztet. Tekintse át a felügyelt példány vagy a lekérdezési tár nézetei által megadott javaslatokat a romlott teljesítményű lekérdezések azonosításához. 

A felügyelt SQL-példány beépített automatikus javítási funkcióval rendelkezik, amely alapértelmezés szerint engedélyezve van. Ez a funkció biztosítja, hogy a múltban ledolgozott lekérdezések nem csökkennek a jövőben. Ha ez a funkció nincs engedélyezve, futtassa a munkaterhelést a régi beállításokkal, így az SQL felügyelt példánya megismerheti a teljesítmény alapkonfigurációját. Ezután engedélyezze a szolgáltatást, és futtassa újra a munkafolyamatot az új beállításokkal. 

Módosítsa a teszt paramétereit, vagy frissítsen magasabb szolgáltatási szintre, hogy elérje az igényeinek megfelelő munkaterhelés-teljesítmény optimális konfigurációját. 

## <a name="monitor-performance"></a>Teljesítmény figyelése 

Az SQL felügyelt példánya fejlett eszközöket biztosít a figyeléshez és a hibaelhárításhoz, és a példány teljesítményének figyeléséhez használja őket. A figyelni kívánt főbb mérőszámok a következők: 

- A példány CPU-használata annak megállapítására, hogy a kiépített virtuális mag száma megfelelő-e a számítási feladathoz. 
- Lap – várható élettartam a felügyelt példányon annak megállapítása érdekében, hogy szüksége van-e [további memóriára](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Statisztikák, például INSTANCE_LOG_GOVERNOR vagy PAGEIOLATCH, amelyek a tárolási IO-problémákat azonosítják, különösen a általános célú szinten, ahol szükség lehet a fájlok előzetes lefoglalására a jobb IO-teljesítmény eléréséhez. 


## <a name="considerations"></a>Megfontolandó szempontok  

A teljesítmény összehasonlításakor vegye figyelembe a következőket: 

- A beállítások egyeznek a forrás és a cél között. Ellenőrizze, hogy a különböző példányok, adatbázisok és tempdb beállítások egyenértékűek-e a két környezet között. A konfigurációban, a kompatibilitási szintekben, a titkosítási beállításokban, a nyomkövetési jelzők stb.-ben a teljesítmény megváltoztatható. 

- A tárterület az [ajánlott eljárásoknak](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)megfelelően van konfigurálva. Például a általános célú esetében előfordulhat, hogy a teljesítmény növelése érdekében a fájlok méretét előre le kell foglalni. 

- Vannak olyan [kulcsfontosságú környezeti eltérések](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) , amelyek a felügyelt példányok és a SQL Server közötti teljesítménybeli különbségeket okozhatják. Azonosítsa a környezethez kapcsolódó kockázatokat, amelyek hozzájárulhatnak a teljesítménnyel kapcsolatos problémákhoz. 

- A lekérdezési tárolót és az automatikus finomhangolást engedélyezni kell az SQL felügyelt példányán, mivel segítenek a számítási feladatok teljesítményének mérésében és a lehetséges teljesítménybeli problémák automatikus enyhítésében. 



## <a name="next-steps"></a>Következő lépések

Az új Azure SQL felügyelt példány környezetének optimalizálásával kapcsolatos további információkért tekintse meg a következő forrásokat: 

- [Hogyan állapítható meg, hogy az Azure SQL felügyelt példányain miért különbözik a számítási feladatok teljesítményének SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Az SQL felügyelt példány és a SQL Server közötti teljesítménybeli különbségek fő okai](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Tárolási teljesítmény – ajánlott eljárások és szempontok az Azure SQL felügyelt példányaihoz (általános célú)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Valós idejű Teljesítményfigyelés a felügyelt Azure SQL-példányhoz (ez archivált, ez a cél?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)