---
title: Használja sys_schema-Azure Database for MySQL
description: Megtudhatja, hogyan használhatja a sys_schema a teljesítménnyel kapcsolatos problémák megkeresésére és az adatbázis karbantartására Azure Database for MySQLokban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: a20510ee2800a54f9a51a2f498ee8ae8a3e51d55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543149"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>A sys_schema használata a teljesítmény finomhangolásához és az adatbázis-karbantartáshoz a Azure Database for MySQL

A MySQL-5,5-ben elsőként elérhető MySQL-performance_schema számos létfontosságú kiszolgálói erőforrást biztosít, mint például a memória kiosztása, a tárolt programok, a metaadatok zárolása stb. A performance_schema azonban több mint 80 táblát tartalmaz, és a szükséges információk beszerzéséhez gyakran szükség van a táblázatokon performance_schema belüli táblákhoz való csatlakozásra, valamint a information_schema tábláira. A performance_schema és information_schema egyaránt kiépíthető, hogy a sys_schema egy csak olvasható adatbázisban a [felhasználóbarát nézetek](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) hatékony gyűjteményét kínálja, és teljes mértékben engedélyezve van a Azure Database for MySQL 5,7-es verziójában.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="sys_schema nézetei":::

A sys_schemaban 52 nézet található, és mindegyik nézet a következő előtagok egyikével rendelkezik:

- Host_summary vagy IO: I/O kapcsolatos késések.
- InnoDB: InnoDB-puffer állapota és zárolásai.
- Memória: a gazdagép és a felhasználók által felhasznált memóriahasználat.
- Séma: sémával kapcsolatos információk, például automatikus növekmény, indexek stb.
- Utasítás: információk az SQL-utasításokról; olyan utasítás lehet, amely teljes táblázatos vizsgálatot vagy hosszú lekérdezési időt eredményezett.
- Felhasználó: a felhasználók által felhasznált és csoportosított erőforrások. Ilyenek például a fájlok I/o-, a kapcsolatok és a memória.
- Várakozás: Várjon, amíg az események gazdagép vagy felhasználó szerint vannak csoportosítva.

Most nézzük meg a sys_schema gyakori használati mintáit. Első lépésként csoportosítjuk a használati mintákat két kategóriába: a **teljesítmény finomhangolását** és az **adatbázis-karbantartást**.

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

Az IO a legdrágább művelet az adatbázisban. Az átlagos IO-késést az *sys.user_summary_by_file_io* nézet lekérdezésével tudjuk megkeresni. Az alapértelmezett 125 GB kiépített tárterület esetén az i/o-késés körülbelül 15 másodperc.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="IO-késés: 125 GB":::

Mivel Azure Database for MySQL az i/o-méretezést a tárterület tekintetében, miután a kiépített tárterületet 1 TB-ra növelte, az i/o-késés a 571 MS-ra csökken.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="IO-késés: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

A gondos tervezés ellenére számos lekérdezés továbbra is teljes táblázatos vizsgálatokat eredményezhet. Az indexek típusaival és optimalizálásával kapcsolatos további információkért tekintse meg a következő cikket: a [lekérdezés teljesítményének megoldása](./howto-troubleshoot-query-performance.md). A teljes táblázatos vizsgálatok erőforrás-igényesek, és csökkentik az adatbázis teljesítményét. A teljes táblázatos vizsgálattal rendelkező táblák keresésének leggyorsabb módja a *sys.schema_tables_with_full_table_scans* nézet lekérdezése.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="teljes táblázatos vizsgálatok":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Az adatbázis-teljesítménnyel kapcsolatos problémák elhárítása érdekében hasznos lehet az adatbázison belül zajló események azonosítása, és a *sys.user_summary_by_statement_type* nézet használata csak ezt a trükköt használhatja.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="Összefoglaló utasítás szerint":::

Ebben a példában a 53 percet töltöttük a üt lekérdezési naplójának 44579-es időpontjának kiürítésével Azure Database for MySQL. Ez hosszú idő és sok IOs. Csökkentheti ezt a tevékenységet a lassú lekérdezési napló letiltásával vagy a lassú lekérdezés-bejelentkezési Azure Portal gyakoriságának csökkentésével.

## <a name="database-maintenance"></a>Adatbázis-karbantartás

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> A nézet lekérdezése hatással lehet a teljesítményre. A hibaelhárítást a munkaidőn kívüli munkaidőben ajánlott elvégezni.

A InnoDB-puffer a memóriában található, és az adatbázis-kezelő és a tároló fő gyorsítótárazási mechanizmusa. A InnoDB-puffer mérete a teljesítmény szintjéhez van kötve, és nem módosítható, kivéve, ha egy másik termék SKU van kiválasztva. Csakúgy, mint az operációs rendszer memóriája, a régi oldalakat felcseréli a rendszer, hogy helyet szabadítson fel a friss adatmennyiséghez. Annak megállapításához, hogy mely táblák használják fel a InnoDB-puffer teljes memóriáját, lekérdezheti a *sys.innodb_buffer_stats_by_table* nézetet.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="InnoDB-puffer állapota":::

A fenti ábrán látható, hogy a rendszertáblák és a nézetek kivételével a mysqldatabase033-adatbázis minden olyan táblája, amely az egyik WordPress-webhelyét üzemelteti, 16 KB-os vagy 1 lapot foglal le a memóriában.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Az indexek nagyszerű eszközöket biztosítanak az olvasási teljesítmény javítására, de további költségekkel járnak a lapkák és a tárolók esetében. *Sys.schema_unused_indexes* és *sys.schema_redundant_indexes* betekintést nyújt a nem használt vagy ismétlődő indexekre.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="nem használt indexek":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="redundáns indexek":::

## <a name="conclusion"></a>Összegzés

Összefoglalva, a sys_schema kiváló eszköz a teljesítmény finomhangolásához és az adatbázis-karbantartáshoz. Győződjön meg arról, hogy kihasználja ezt a funkciót a Azure Database for MySQL. 

## <a name="next-steps"></a>Következő lépések
- Ha szeretné megkeresni a leginkább érintett kérdésekre adott társi válaszokat, vagy új kérdést/választ szeretne küldeni, látogasson el [a Microsoft Q&egy kérdés oldalra](/answers/topics/azure-database-mysql.html) vagy [stack overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).