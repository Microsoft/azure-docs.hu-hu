---
title: 'SQL Server a Azure SQL Managed Instance: Teljesítmény alapkonfigurációja'
description: Megtudhatja, hogyan hozhat létre és hasonlíthatja össze a teljesítményre vonatkozó alapkonfigurációkat a SQL Server-adatbázisok Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a47684bf29f1f34b8c9c59c04b7d33d234505cc2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389706"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>Migrálás teljesítménye: SQL Server Azure SQL Managed Instance teljesítménykonfigurációhoz
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Hozzon létre egy teljesítménykonfigurációt, hogy összehasonlítsa a számítási feladatok teljesítményét egy SQL Managed Instance és az eredeti számítási feladat SQL Server. 

## <a name="create-a-baseline"></a>Alapkonfiguráció létrehozása

Ideális esetben a teljesítmény hasonló vagy jobb a migrálás után, ezért fontos mérni és rögzíteni az alapkonfiguráció teljesítményértékeket a forráson, majd össze kell hasonlítani őket a célkörnyezettel. A teljesítmény-alapkonfiguráció olyan paraméterek készlete, amelyek meghatározzák az átlagos számítási feladatot a forráson. 

Válassza ki azokat a lekérdezéseket, amelyek fontosak a számára, és amelyek az üzleti tevékenységi feladatok reprezentatívak. Mérje meg és dokumentálja a lekérdezések minimális/átlagos/maximális időtartamát és PROCESSZORhasználatát, valamint a forráskiszolgáló teljesítménymetrikákat, például az átlagos/maximális CPU-használatot, az átlagos/maximális lemez I/O-késést, az átviteli sebességet, az IOPS-t, az átlagos/maximális oldal-várható élettartamot és a tempdb átlagos maximális méretét. 

A következő erőforrások segíthetnek meghatározni a teljesítmény alapkonfigurációt: 

   - [CPU-használat figyelése ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Memóriahasználat figyelése](/sql/relational-databases/performance-monitor/monitor-memory-usage)   és határozza meg a különböző összetevők, például a pufferkészlet, a tervgyorsítótár, az oszloptárkészlet, a memóriabeli [OLTP stb.](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)által használt memória mennyiségét. Emellett a Page Life Expectancy memóriateljesítmény-számláló átlag- és csúcsértékét is meg kell találnia. 
   - Monitorja a lemez I/O-használatát a SQL Server példányon a [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)    [teljesítményszámlálók használatával.](/sql/relational-databases/performance-monitor/monitor-disk-usage) 
   - Figyelje a számítási feladatokat és a lekérdezések teljesítményét a dinamikus felügyeleti nézetek vizsgálatával (vagy a Lekérdezéstárat, ha a 2016-os vagy SQL Server-os vagy újabb rendszerről mi történik). A számítási feladat legfontosabb lekérdezései átlagos időtartamának és CPU-használatának azonosítása. 

A forrás-adatforrás teljesítményével kapcsolatos SQL Server a migrálás előtt meg kell oldani. Az ismert problémák új rendszerekre való áttelepítése váratlan eredményeket okozhat, és érvényteleníthet minden teljesítmény-összehasonlítást. 


## <a name="compare-performance"></a>Teljesítmény összehasonlítása 

Az alapkonfiguráció meghatározása után hasonlítsa össze a hasonló számítási feladatok teljesítményét a SQL Managed Instance. A pontosság érdekében fontos, hogy a SQL Managed Instance a lehető legnagyobb mértékben hasonlítható legyen össze SQL Server környezettel. 

Az infrastruktúra SQL Managed Instance, amelyek miatt a teljesítmény egyeztetése nagyon valószínűtlen. Egyes lekérdezések a vártnál gyorsabban futhatnak, míg mások lassabbak. Az összehasonlítás célja annak ellenőrzése, hogy a számítási feladatok felügyelt példányon való teljesítménye megegyezik-e az SQL Server teljesítményével (átlagosan), és azonosítani kell az olyan kritikus fontosságú lekérdezéseket, amelyek teljesítménye nem egyezik meg az eredeti teljesítményével. 

A teljesítmény-összehasonlítás valószínűleg a következő eredményeket eredményezi: 

- A számítási feladatok teljesítménye a felügyelt példányon igazodik vagy jobb, mint a számítási feladatok teljesítménye a SQL Server. Ebben az esetben sikeresen megerősítette, hogy a migrálás sikeres volt. 

- A számítási feladatban a teljesítményparaméterek és a lekérdezések többsége a várt módon működik, és néhány kivétel csökkent teljesítményt eredményez. Ebben az esetben azonosítsa a különbségeket és azok fontosságát. Ha vannak olyan fontos lekérdezések, amelyek teljesítménye csökkent, vizsgálja meg, hogy a mögöttes SQL-csomagok módosultak-e, vagy a lekérdezések túllépik-e az erőforráskorlátokat. Ezt csökkentheti, ha közvetlenül vagy tervútmutatókkal alkalmaz néhány tippet a kritikus lekérdezésekre (például a kompatibilitási szint módosítása vagy az örökölt számosságbecsülő). Győződjön meg arról, hogy a statisztikák és az indexek naprakészek és egyenértékűek mindkét környezetben. 

- A legtöbb lekérdezés lassabban fut egy felügyelt példányon, mint a SQL Server példányon. Ebben az esetben próbálja azonosítani a különbség kiváltó [](../../managed-instance/resource-limits.md#service-tier-characteristics) okait, például az erőforráskorlátok (például az I/O, a memória vagy a példánynaplók sebességére vonatkozó korlátok) elérésének okait. Ha nincsenek a különbséget okozó erőforráskorlátok, próbálja meg módosítani az adatbázis kompatibilitási szintjét, vagy módosítsa az adatbázis beállításait, például az örökölt számosságbecslést, majd futtassa újra a tesztet. Tekintse át a felügyelt példány vagy a lekérdezéstár nézetei által nyújtott javaslatokat a regressziós teljesítményű lekérdezések azonosításához. 

SQL Managed Instance rendelkezik egy beépített automatikus tervkorrekciós funkcióval, amely alapértelmezés szerint engedélyezve van. Ez a funkció biztosítja, hogy a múltban jól működött lekérdezések ne romolnak a jövőben. Ha ez a funkció nincs engedélyezve, futtassa a számítási feladatot a régi beállításokkal, hogy SQL Managed Instance megismerheti a teljesítmény alapkonfigurációját. Ezután engedélyezze a funkciót, és futtassa újra a számítási feladatot az új beállításokkal. 

Az igényeinek megfelelő számítási feladat teljesítményének optimális konfigurációjának elérése érdekében módosítja a teszt paramétereit, vagy frissítsen magasabb szolgáltatási szintekre. 

## <a name="monitor-performance"></a>Teljesítmény figyelése 

SQL Managed Instance speciális eszközöket biztosít a monitorozáshoz és a hibaelhárításhoz, és érdemes használni őket a példány teljesítményének figyelése érdekében. A figyelt legfontosabb metrikák a következőek: 

- Cpu-használat a példányon annak meghatározásához, hogy a kiépített virtuális magok száma megfelel-e a számítási feladatnak. 
- A felügyelt példány lap-várható élettartama annak meghatározásához, hogy szüksége van-e [további memóriára.](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)
-  Olyan statisztikák INSTANCE_LOG_GOVERNOR mint a INSTANCE_LOG_GOVERNOR vagy a PAGEIOLATCH, amelyek azonosítják általános célú tárolási I/O-problémákat, különösen az általános célú szinten, ahol előfordulhat, hogy a jobb I/O-teljesítmény érdekében előre le kell foglalnia a fájlokat. 


## <a name="considerations"></a>Megfontolandó szempontok  

A teljesítmény összehasonlíthatóságakor vegye figyelembe a következőket: 

- A beállítások megegyeznek a forrás és a cél között. Ellenőrizze, hogy a különböző példány-, adatbázis- és tempdb-beállítások egyenértékűek-e a két környezet között. A konfiguráció, a kompatibilitási szintek, a titkosítási beállítások, a nyomkövetési jelzők stb. eltérései mind eltérést mutatnak a teljesítményben. 

- A tárolás az ajánlott eljárások [szerint van konfigurálva.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) Előfordulhat például, általános célú a teljesítmény javítása érdekében előre le kell foglalnia a fájlok méretét. 

- A [környezetben alapvető különbségek lehetnek,](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) amelyek a felügyelt példányok és a felügyelt példányok közötti teljesítménybeli SQL Server. Azonosítsa a környezet szempontjából releváns kockázatokat, amelyek hozzájárulhatnak a teljesítménybeli problémákhoz. 

- A lekérdezéstárat és az automatikus hangolást engedélyezni kell a SQL Managed Instance mivel ezek segítenek a számítási feladatok teljesítményének mérésében és a potenciális teljesítménybeli problémák automatikus csökkentésében. 



## <a name="next-steps"></a>Következő lépések

Az új környezet optimalizálásával kapcsolatos további Azure SQL Managed Instance az alábbi forrásokban található: 

- [Hogyan lehet azonosítani, hogy miért különbözik a számítási feladatok Azure SQL Managed Instance teljesítménye a SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [A teljesítménybeli különbségek fő okai a SQL Managed Instance és a SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Tárolási teljesítményre vonatkozó ajánlott eljárások és szempontok Azure SQL Managed Instance (általános célú)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Valós idejű teljesítményfigyelés Azure SQL Managed Instance (archivált, ez a cél?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)