---
title: Alkalmazás-fejlesztés – ajánlott eljárások – Azure Database for PostgreSQL
description: Ismerje meg az alkalmazások Azure Database for PostgreSQL használatával történő létrehozásával kapcsolatos ajánlott eljárásokat.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364601"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Ajánlott eljárások alkalmazások létrehozásához Azure Database for PostgreSQL

Íme néhány ajánlott eljárás, amely segítséget nyújt egy felhőalapú alkalmazás létrehozásában Azure Database for PostgreSQL használatával. Ezek az ajánlott eljárások csökkenthetik az alkalmazás fejlesztési idejét.

## <a name="configuration-of-application-and-database-resources"></a>Az alkalmazás-és adatbázis-erőforrások konfigurálása

### <a name="keep-the-application-and-database-in-the-same-region"></a>Az alkalmazás és az adatbázis megtartása ugyanabban a régióban
Győződjön meg arról, hogy az alkalmazás az Azure-ban való üzembe helyezése során minden függőség ugyanabban a régióban van. A különböző régiókon vagy rendelkezésre állási zónákban lévő példányok terjesztése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére.

### <a name="keep-your-postgresql-server-secure"></a>A PostgreSQL-kiszolgáló biztonságának megőrzése
Konfigurálja a PostgreSQL-kiszolgálót úgy, hogy a [Biztonság](./concepts-security.md) és a nyilvánosságra ne legyen nyilvánosan elérhető. A kiszolgáló biztonságossá tételéhez használja az alábbi lehetőségek egyikét:
- [Tűzfalszabályok](./concepts-firewall-rules.md)
- [Virtuális hálózatok](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

A biztonság érdekében mindig az SSL protokollon keresztül kell csatlakoznia a PostgreSQL-kiszolgálóhoz, és konfigurálnia kell a PostgreSQL-kiszolgálót és az alkalmazást a TLS 1,2 használatára. Lásd: [az SSL/TLS konfigurálása](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Kiszolgáló paramétereinek hangolása
Az olvasási és nagy teljesítményű számítási feladatokhoz a kiszolgálói paramétereket kell `tmp_table_size` `max_heap_table_size` megszabni, és optimalizálni lehet a jobb teljesítményt. A változókhoz szükséges értékek kiszámításához tekintse meg a kapcsolatonként a teljes memória és az alapmemória értékét. A kapcsolatonként megadott memória-paraméterek összege, kivéve a `tmp_table_size` kiszolgáló teljes memóriájának alapmemóriájának fiókjait.

### <a name="use-environment-variables-for-connection-information"></a>Környezeti változók használata a kapcsolatok adataihoz
Az adatbázis hitelesítő adatait ne mentse az alkalmazás kódjába. Az előtér-alkalmazástól függően kövesse az útmutatást a környezeti változók beállításához. Az App Service használatát lásd: az Alkalmazásbeállítások és az Azure Kuberentes szolgáltatás[konfigurálása](../app-service/configure-common.md#configure-app-settings) . a Kubernetes- [titkok használata](https://kubernetes.io/docs/concepts/configuration/secret/)című témakörben talál további információt.

## <a name="performance-and-resiliency"></a>Teljesítmény és rugalmasság
Az alábbiakban néhány olyan eszközt és gyakorlatot talál, amelyekkel az alkalmazással kapcsolatos teljesítményproblémák hibakeresését végezheti el.

### <a name="use-connection-pooling"></a>A kapcsolatok készletezésének használata
A kapcsolatok készletezése során a rendszer az indítási idő és a karbantartás során rögzített kapcsolati kapcsolatokat hoz létre. Ez segít csökkenteni a memória töredezettségét azon a kiszolgálón, amelyet az adatbázis-kiszolgálón létrehozott dinamikus új kapcsolatok okoznak. A kapcsolatok készletezése az alkalmazás oldalán konfigurálható, ha az alkalmazás-keretrendszer vagy az adatbázis-illesztőprogram támogatja azt. Ha ez nem támogatott, a másik ajánlott lehetőség egy proxy kapcsolat Pooler szolgáltatásának kihasználása, például az alkalmazáson kívül futó [PgBouncer](https://pgbouncer.github.io/) vagy [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) , valamint az adatbázis-kiszolgálóhoz való csatlakozás. A PgBouncer és a Pgpool egyaránt olyan közösségi eszközök, amelyek a Azure Database for PostgreSQLkal működnek.

### <a name="retry-logic-to-handle-transient-errors"></a>Újrapróbálkozási logika az átmeneti hibák kezelésére
Előfordulhat, hogy az alkalmazás átmeneti hibákat tapasztal, amikor az adatbázishoz való kapcsolódást időnként eldobják vagy elveszítik. Ilyen helyzetekben a kiszolgáló egy-két újrapróbálkozást követően 5 – 10 másodperc múlva működik. Egy jó gyakorlat az első újrapróbálkozás előtt 5 másodpercig várni. Ezután kövesse az egyes újrapróbálkozásokat a várakozás fokozatos növelésével, akár 60 másodpercre. Korlátozza az újrapróbálkozások maximális számát, amikor az alkalmazás úgy véli, hogy a művelet meghiúsult, így tovább vizsgálhatja. További információt [a kapcsolódási hibák elhárítása](./concepts-connectivity.md) című témakörben talál.

### <a name="enable-read-replication-to-mitigate-failovers"></a>Olvasási replikálás engedélyezése a feladatátvételek enyhítéséhez
A feladatátvételi forgatókönyvekhez használhatja a [felhőbe irányuló replikálás](./concepts-read-replicas.md) . Ha olvasási replikákat használ, a forrás-és a replika-kiszolgálók közötti automatikus feladatátvétel nem történik meg. A forrás és a replika közötti késés tapasztalható, mert a replikáció aszinkron módon történik. A hálózati késést számos tényező befolyásolja, például a forráskiszolgálón futó munkaterhelés mérete, valamint az adatközpontok közötti késleltetés. A legtöbb esetben a replika késése néhány másodperctől pár percig terjed.


## <a name="database-deployment"></a>Adatbázis üzembe helyezése

### <a name="configure-cicd-deployment-pipeline"></a>A CI/CD telepítési folyamatának konfigurálása
Alkalmanként az adatbázis módosításait kell telepítenie. Ilyen esetekben a folyamatos integráció (CI) segítségével a PostgreSQL-kiszolgáló [GitHub-műveletein](https://github.com/Azure/postgresql/blob/master/README.md) keresztül frissítheti az adatbázist úgy, hogy egyéni parancsfájlt futtat.

### <a name="define-manual-database-deployment-process"></a>Az adatbázis manuális üzembe helyezési folyamatának meghatározása
Az adatbázis kézi üzembe helyezése során az alábbi lépéseket követve minimalizálhatja az állásidőt, vagy csökkentheti a sikertelen üzembe helyezés kockázatát:

- Hozzon létre egy éles adatbázis másolatát egy új adatbázison pg_dump használatával.
- Frissítse az új adatbázist az adatbázisához szükséges új séma-módosításokkal vagy frissítésekkel.
- Az éles adatbázist csak olvasható állapotban helyezze el. Az üzemi adatbázisban nem lehet írási művelet, amíg az üzembe helyezés be nem fejeződik.
- Tesztelje az alkalmazást az 1. lépésben újonnan frissített adatbázissal.
- Telepítse az alkalmazás módosításait, és győződjön meg arról, hogy az alkalmazás mostantól a legújabb frissítésekkel rendelkező új adatbázist használja.
- Tartsa meg a régi éles adatbázist, hogy vissza tudja állítani a módosításokat. Ezután kiértékelheti, hogy törli-e a régi éles adatbázist, vagy ha szükséges, exportálja azt az Azure Storage-ba.

>  [!NOTE]
> Ha az alkalmazás olyan, mint egy e-kereskedelmi alkalmazás, és nem csak olvasható állapotban helyezheti el, a módosításokat a biztonsági mentés után közvetlenül az éles adatbázisban helyezheti üzembe. A tézisek változását az alkalmazás kis forgalmú, alacsony forgalmú órákon belül kell végrehajtani, hogy minimálisra csökkentsék a hatást, mivel előfordulhat, hogy egyes felhasználók sikertelen kéréseket tapasztalhatnak. Győződjön meg arról, hogy az alkalmazás kódja a sikertelen kérelmeket is kezeli.

## <a name="database-schema-and-queries"></a>Adatbázis-séma és lekérdezések
Íme néhány tipp, amelyet figyelembe kell vennie az adatbázis-séma és a lekérdezések létrehozásakor.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>BIGINT vagy UUID használata az elsődleges kulcsokhoz
Egyéni alkalmazás vagy egyes keretrendszerek létrehozásakor, amelyeket `INT` `BIGINT` az elsődleges kulcsok helyett használhatnak. A használatakor ```INT``` a rendszer azt a kockázatot futtatja, hogy az adatbázisban lévő érték túllépheti az ```INT``` adattípusok tárolási kapacitását. Ha ezt a módosítást egy meglévő éles alkalmazásban szeretné megtekinteni, a fejlesztési idő drágább lehet. Egy másik lehetőség az [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) használata az elsődleges kulcsokhoz. Ez az azonosító egy automatikusan generált 128 bites karakterláncot használ, például: ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . További információ a [PostgreSQL-adattípusokról](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Indexek használata

A postgres számos különböző típusú [index](https://www.postgresql.org/docs/9.1/indexes.html) létezik, amelyek többféleképpen is használhatók. Az index használatával a kiszolgáló sokkal gyorsabban megkeresi és lekéri a megadott sorokat, mint az index nélkül. Az indexek azonban az adatbázis-kiszolgálóhoz is hozzáadhatnak terhelést, így elkerülhető, hogy túl sok index van.

### <a name="use-autovacuum"></a>Autoporszívó használata
A kiszolgálót Azure Database for PostgreSQL-kiszolgálón lévő autoporszívóval is optimalizálhatja. A PostgreSQL nagyobb adatbázis-párhuzamosságot tesz lehetővé, de minden frissítési eredmény INSERT és DELETE. A Delete (Törlés) elemnél a rekordok nem láthatók, és később törlődnek. Ezeknek a feladatoknak a végrehajtásához a PostgreSQL egy vákuum-feladatot futtat. Ha nem vákuumot időről időre, a felhalmozott rekordok a következőket eredményezheti:

- Az adatközelítés, például nagyobb adatbázisok és táblák.
- Nagyobb, optimálisnál rosszabb indexek.
- Nagyobb I/O-műveletek.

További információ az [autoporszívóval való optimalizálásról](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Pg_stats_statements használata
Pg_stat_statements egy PostgreSQL-bővítmény, amely alapértelmezés szerint engedélyezve van a Azure Database for PostgreSQLban. A bővítmény segítségével nyomon követhető a kiszolgáló által végrehajtott összes SQL-utasítás végrehajtási statisztikája. Lásd: [a pg_statement használata](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>A lekérdezési tároló használata
A Azure Database for PostgreSQL [lekérdezés-tárolási](./concepts-query-store.md) funkciója hatékonyabb módszert biztosít a lekérdezési statisztikák nyomon követésére. Ezt a funkciót a pg_stats_statements használatának alternatívájaként ajánljuk.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Tömeges beszúrások optimalizálása és átmeneti adatmennyiség használata
Ha olyan munkaterhelési műveletekkel rendelkezik, amelyek átmeneti adatokat tartalmaznak, vagy nagyméretű adatkészleteket szúrnak be ömlesztve, érdemes lehet a nem naplózott táblákat használni. A szolgáltatás alapértelmezés szerint az atomi és a tartósságot biztosítja. A sav tulajdonságait az atomenergia, a konzisztencia, az elkülönítés és a tartósság teszi elérhetővé. Lásd: [tömeges beszúrások optimalizálása](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Következő lépések
[Postgres útmutató](http://postgresguide.com/)
