---
title: Alkalmazás-fejlesztés – ajánlott eljárások – Azure Database for MySQL
description: Ismerje meg az alkalmazások Azure Database for MySQL használatával történő létrehozásával kapcsolatos ajánlott eljárásokat.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 7f98e14c2eae133941f3fc87247a427198ebcdc2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611963"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Ajánlott eljárások az Azure Database for MySQL-lel történő alkalmazásfejlesztéshez 

Íme néhány ajánlott eljárás, amely segítséget nyújt egy felhőalapú alkalmazás létrehozásában Azure Database for MySQL használatával. Ezek az ajánlott eljárások csökkenthetik az alkalmazás fejlesztési idejét. 

## <a name="configuration-of-application-and-database-resources"></a>Az alkalmazás-és adatbázis-erőforrások konfigurálása

### <a name="keep-the-application-and-database-in-the-same-region"></a>Az alkalmazás és az adatbázis megtartása ugyanabban a régióban
Győződjön meg arról, hogy az alkalmazás az Azure-ban való üzembe helyezése során minden függőség ugyanabban a régióban van. A különböző régiókon vagy rendelkezésre állási zónákban lévő példányok terjesztése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 

### <a name="keep-your-mysql-server-secure"></a>A MySQL-kiszolgáló biztonságának megőrzése
Konfigurálja úgy a MySQL-kiszolgálót, hogy [biztonságos](./concepts-security.md) és nyilvánosan nem elérhető legyen. A kiszolgáló biztonságossá tételéhez használja az alábbi lehetőségek egyikét: 
- [Tűzfalszabályok](./concepts-firewall-rules.md)
- [Virtuális hálózatok](./concepts-data-access-and-security-vnet.md) 
- [Azure Private Link](./concepts-data-access-security-private-link.md)

A biztonság érdekében mindig SSL-kapcsolaton keresztül kell csatlakoznia a MySQL-kiszolgálóhoz, és konfigurálnia kell a MySQL-kiszolgálót és az alkalmazást a TLS 1,2 használatára. Lásd: [az SSL/TLS konfigurálása](./concepts-ssl-connection-security.md). 

### <a name="tune-your-server-parameters"></a>Kiszolgáló paramétereinek hangolása
Az olvasási és nagy teljesítményű számítási feladatokhoz a kiszolgálói paramétereket kell `tmp_table_size` `max_heap_table_size` megszabni, és optimalizálni lehet a jobb teljesítményt. A változókhoz szükséges értékek kiszámításához tekintse meg a kapcsolatonként a teljes memória és az alapmemória értékét. A kapcsolatonként megadott memória-paraméterek összege, kivéve a `tmp_table_size` kiszolgáló teljes memóriájának alapmemóriájának fiókjait.

A és a lehető legnagyobb méretének kiszámításához `tmp_table_size` `max_heap_table_size` használja a következő képletet:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> A teljes memória a teljes memória mennyiségét jelzi, amelyet a kiszolgáló a kiépített virtuális mag.  Például egy általános célú virtuális mag Azure Database for MySQL-kiszolgálón a teljes memória 5 GB * 2 lesz. A memóriával kapcsolatos további részletekért tekintse meg a [díjszabási](./concepts-pricing-tiers.md) csomag dokumentációját.
>
> Az alapmemória megadja a memória változóit, például a `query_cache_size` és a `innodb_buffer_pool_size` -t, a MySQL inicializálása és lefoglalása a kiszolgáló indításakor. A (z) és a (z) rendszerhez kapcsolódó memória (például `sort_buffer_size` és `join_buffer_size` ) csak akkor van lefoglalva, ha a lekérdezés igényli.

### <a name="create-non-admin-users"></a>Nem rendszergazda felhasználók létrehozása 
[Hozzon létre nem rendszergazda felhasználókat](./howto-create-users.md) az egyes adatbázisokhoz. A felhasználóneveket általában az adatbázis neve azonosítja.

### <a name="reset-your-password"></a>Új jelszó kérése
A MySQL-kiszolgáló jelszavát a Azure Portal használatával [állíthatja alaphelyzetbe](./howto-create-manage-server-portal.md#update-admin-password) . 

Az éles adatbázis-kiszolgáló jelszavának alaphelyzetbe állítása leállíthatja az alkalmazást. Célszerű alaphelyzetbe állítani az éles számítási feladatokhoz tartozó jelszót, hogy csökkentse az alkalmazás felhasználóira gyakorolt hatást.

## <a name="performance-and-resiliency"></a>Teljesítmény és rugalmasság 
Az alábbiakban néhány olyan eszközt és gyakorlatot talál, amelyekkel az alkalmazással kapcsolatos teljesítményproblémák hibakeresését végezheti el.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>A lassú lekérdezési naplók engedélyezése a teljesítménnyel kapcsolatos problémák azonosításához
Engedélyezheti a [lassú lekérdezési naplókat](./concepts-server-logs.md) [és a naplókat a kiszolgálón](./concepts-audit-logs.md) . A lassú lekérdezési naplók elemzése segíthet a teljesítménybeli szűk keresztmetszetek azonosításában a hibaelhárításhoz. 

A naplók Azure Monitor naplók, Azure Event Hubs és Storage-fiókok Azure Diagnostics naplófájljain keresztül is elérhetők. Lásd: [a lekérdezések teljesítményével kapcsolatos problémák elhárítása](./howto-troubleshoot-query-performance.md).

### <a name="use-connection-pooling"></a>A kapcsolatok készletezésének használata
Az adatbázis-kapcsolatok kezelése jelentős hatással lehet az alkalmazás teljesítményére. A teljesítmény optimalizálása érdekében csökkentenie kell a kapcsolatok létrehozásának, valamint a kapcsolatok létrehozási időpontjának számát a kulcstartó elérési útjaiban. A rugalmasság és a teljesítmény növelése érdekében a [kapcsolati készletezés](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended) használatával kapcsolódjon Azure Database for MySQLhoz. 

A kapcsolatok hatékony kezeléséhez használhatja a [ProxySQL](https://proxysql.com/) -kapcsolat Pooler. A kapcsolati Pooler csökkentheti az üresjárati kapcsolatokat, és újrahasznosíthatja a meglévő kapcsolatokat, ami segít a problémák elkerülésében. További információért lásd: [ProxySQL beállítása](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Újrapróbálkozási logika az átmeneti hibák kezelésére
Előfordulhat, hogy az alkalmazás [átmeneti hibákat](./concepts-connectivity.md#handling-transient-errors) tapasztal, amikor az adatbázishoz való kapcsolódást időnként eldobják vagy elveszítik. Ilyen helyzetekben a kiszolgáló egy-két újrapróbálkozást követően 5 – 10 másodperc múlva működik. 

Egy jó gyakorlat az első újrapróbálkozás előtt 5 másodpercig várni. Ezután kövesse az egyes újrapróbálkozásokat a várakozás fokozatos növelésével, akár 60 másodpercre. Korlátozza az újrapróbálkozások maximális számát, amikor az alkalmazás úgy véli, hogy a művelet meghiúsult, így tovább vizsgálhatja. További információt [a kapcsolódási hibák elhárítása](./howto-troubleshoot-common-connection-issues.md) című témakörben talál. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Olvasási replikálás engedélyezése a feladatátvételek enyhítéséhez
A feladatátvételi forgatókönyvekhez használhatja a [felhőbe irányuló replikálás](./howto-data-in-replication.md) . Ha olvasási replikákat használ, a forrás-és a replika-kiszolgálók közötti automatikus feladatátvétel nem történik meg. 

A forrás és a replika közötti késés tapasztalható, mert a replikáció aszinkron módon történik. A hálózati késést számos tényező befolyásolja, például a forráskiszolgálón futó munkaterhelés mérete, valamint az adatközpontok közötti késleltetés. A legtöbb esetben a replika késése néhány másodperctől pár percig terjed.

## <a name="database-deployment"></a>Adatbázis üzembe helyezése 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>Azure Database for MySQL-feladat konfigurálása a CI/CD üzembe helyezési folyamatában
Alkalmanként az adatbázis módosításait kell telepítenie. Ilyen esetekben a folyamatos integráció (CI) és a folyamatos kézbesítés (CD) az [Azure-folyamatokon](https://azure.microsoft.com/services/devops/pipelines/) keresztül végezhető el, és a [MySQL-kiszolgáló](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment) feladatával frissítheti az adatbázist úgy, hogy egyéni parancsfájlt futtat.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>Hatékony folyamat használata az adatbázis manuális telepítéséhez 
Az adatbázis kézi üzembe helyezése során az alábbi lépéseket követve minimalizálhatja az állásidőt, vagy csökkentheti a sikertelen üzembe helyezés kockázatát: 

1. Hozzon létre egy éles adatbázist egy új adatbázisban a [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) vagy a [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)használatával. 
2. Frissítse az új adatbázist az adatbázisához szükséges új séma-módosításokkal vagy frissítésekkel. 
3. Az éles adatbázist csak olvasható állapotban helyezze el. Az üzemi adatbázisban nem lehet írási művelet, amíg az üzembe helyezés be nem fejeződik. 
4. Tesztelje az alkalmazást az 1. lépésben újonnan frissített adatbázissal.
5. Telepítse az alkalmazás módosításait, és győződjön meg arról, hogy az alkalmazás mostantól a legújabb frissítésekkel rendelkező új adatbázist használja. 
6. Tartsa meg a régi éles adatbázist, hogy vissza tudja állítani a módosításokat. Ezután kiértékelheti, hogy törli-e a régi éles adatbázist, vagy ha szükséges, exportálja azt az Azure Storage-ba. 

>[!NOTE]
>Ha az alkalmazás olyan, mint egy e-kereskedelmi alkalmazás, és nem csak olvasható állapotban helyezheti el, a módosításokat a biztonsági mentés után közvetlenül az éles adatbázisban helyezheti üzembe. A tézisek változását az alkalmazás kis forgalmú, alacsony forgalmú órákon belül kell végrehajtani, hogy minimálisra csökkentsék a hatást, mivel előfordulhat, hogy egyes felhasználók sikertelen kéréseket tapasztalhatnak. 
>
>Győződjön meg arról, hogy az alkalmazás kódja a sikertelen kérelmeket is kezeli.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>A MySQL natív metrikáinak használatával megtudhatja, hogy a számítási feladat meghaladja-e a memóriabeli ideiglenes táblázat méretét
A nagy olvasási idejű számítási feladatokkal a MySQL-kiszolgálón futó lekérdezések túllépik a memóriában tárolt ideiglenes táblázatos méreteket. A nagy olvasási terhelés miatt a kiszolgáló átválthat az ideiglenes táblák lemezre írására, ami hatással lehet az alkalmazás teljesítményére. Annak megállapításához, hogy a kiszolgáló lemezre írása az ideiglenes táblázat méretének meghaladása miatt történt-e, tekintse meg a következő metrikákat:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
A `created_tmp_disk_tables` metrika azt jelzi, hogy hány tábla lett létrehozva a lemezen. A `created_tmp_table` metrika azt jelzi, hogy hány ideiglenes táblát kell létrehozni a memóriában, a számítási feladatok miatt. Annak megállapításához, hogy egy adott lekérdezés futtatása ideiglenes táblákat fog-e használni, futtassa a [magyarázat](https://dev.mysql.com/doc/refman/8.0/en/explain.html) utasítást a lekérdezésen. Az oszlopban szereplő adatok `extra` azt jelzik, `Using temporary` hogy a lekérdezés ideiglenes táblák használatával fog futni.

Ha a számítási feladatok százalékos arányát a lemezekre kiömlött lekérdezésekkel szeretné kiszámítani, használja a mérőszám értékeit a következő képletben:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Ideális esetben ennek a százaléknak kevesebb 25%-nak kell lennie. Ha úgy látja, hogy a százalékos arány 25% vagy nagyobb, javasoljuk, hogy módosítsa a két kiszolgáló paraméterét, tmp_table_size és max_heap_table_size.

## <a name="database-schema-and-queries"></a>Adatbázis-séma és lekérdezések

Íme néhány tipp, amelyet figyelembe kell vennie az adatbázis-séma és a lekérdezések létrehozásakor.

### <a name="use-the-right-datatype-for-your-table-columns"></a>A táblázat oszlopaihoz tartozó megfelelő adattípus használata
Ha a megfelelő adattípust használja a tárolni kívánt adattípus alapján, optimalizálhatja a tárterületet, és csökkentheti a helytelen adattípusok miatt előforduló hibákat.

### <a name="use-indexes"></a>Indexek használata
A lassú lekérdezések elkerüléséhez használhat indexeket. Az indexek segítségével gyorsan megtalálhatja az adott oszlopokból álló sorokat. Lásd: [az indexek használata a MySQL-ben](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="use-explain-for-your-select-queries"></a>A VÁLASZTÓ lekérdezések MAGYARÁZATának használata
Az `EXPLAIN` utasítás segítségével bepillantást nyerhet arról, hogy mit csinál a MySQL a lekérdezés futtatásához. Segítséget nyújt a szűk keresztmetszetek és a lekérdezési problémák észlelésében. Tekintse meg, [hogyan használhatja a magyarázatot a profil lekérdezési teljesítményére](./howto-troubleshoot-query-performance.md).