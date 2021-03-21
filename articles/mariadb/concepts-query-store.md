---
title: Lekérdezési tároló – Azure Database for MariaDB
description: További információ a Azure Database for MariaDB található lekérdezés-tárolási szolgáltatásról, amely segítséget nyújt a teljesítmény időbeli nyomon követéséhez.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 164285b1fea3dce18161066e643aa165e47cc496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664197"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>A Azure Database for MariaDB teljesítményének figyelése a lekérdezési tárolóval

**A következőkre vonatkozik:** Azure Database for MariaDB 10,2

Az Azure Database for MariaDB lekérdezés-tárolási funkciója lehetővé teszi a lekérdezési teljesítmény időbeli nyomon követését. A Query Store leegyszerűsíti a teljesítménnyel kapcsolatos hibaelhárítást, így gyorsan megtalálhatja a leghosszabb ideig futó és a legtöbb erőforrás-igényes lekérdezést. A Query Store automatikusan rögzíti a lekérdezések és a futásidejű statisztikák előzményeit, és megőrzi azokat az áttekintéshez. Elkülöníti az adatokat az időablakok alapján, hogy az adatbázis használati mintái láthatók legyenek. A rendszer az összes felhasználóra, adatbázisra és lekérdezésre vonatkozó, a Azure Database for MariaDB-példányban található **MySQL** Schema adatbázisban tárolja az összes felhasználót.

## <a name="common-scenarios-for-using-query-store"></a>A Query Store használatának gyakori forgatókönyvei

A lekérdezési tároló számos esetben használható, többek között az alábbiakkal:

- Romlott lekérdezések észlelése
- A lekérdezések adott időintervallumban történő végrehajtása számának meghatározása
- Lekérdezés átlagos végrehajtási idejének összevetése az időablakok között a nagy eltérések megjelenítéséhez

## <a name="enabling-query-store"></a>A lekérdezési tároló engedélyezése

A lekérdezési tároló egy opt-in funkció, így alapértelmezés szerint nem aktív a kiszolgálón. A lekérdezési tár globálisan engedélyezett vagy le van tiltva az adott kiszolgálón lévő összes adatbázis esetében, és adatbázison nem kapcsolható be és ki.

### <a name="enable-query-store-using-the-azure-portal"></a>A lekérdezési tároló engedélyezése a Azure Portal használatával

1. Jelentkezzen be a Azure Portalba, és válassza ki a Azure Database for MariaDB-kiszolgálót.
2. Válassza ki a **kiszolgáló paramétereit** a menü **Beállítások** szakaszában.
3. Keresse meg a query_store_capture_mode paramétert.
4. Állítsa az értéket az összes értékre, és **mentse**.

A várakozási statisztika engedélyezése a lekérdezési tárolóban:

1. Keresse meg a query_store_wait_sampling_capture_mode paramétert.
2. Állítsa az értéket az összes értékre, és **mentse**.

Akár 20 percet is igénybe vehet, amíg a MySQL-adatbázisban megmarad az első köteg.

## <a name="information-in-query-store"></a>Információk a Query Store-ban

A lekérdezési tároló két tárolót tartalmaz:

- A futásidejű statisztikai tároló a lekérdezés-végrehajtási statisztikai adatok megőrzéséhez.
- A várakozási statisztikák tárolója a várakozó statisztikai adatok megőrzéséhez.

A lemezterület-használat minimalizálásához a futásidejű statisztika tárolójában lévő futtatókörnyezet-végrehajtási statisztika egy rögzített, konfigurálható időablakban van összesítve. Az ezekben a tárolókban található információk láthatók a lekérdezési tár nézeteinek lekérdezésével.

A következő lekérdezés adatokat ad vissza a lekérdezési tárolóban található lekérdezésekről:

```sql
SELECT * FROM mysql.query_store;
```

Vagy a várakozási statisztikák lekérdezése:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Várakozási lekérdezések keresése

> [!NOTE]
> A várakozási statisztikát nem szabad engedélyezni a maximális számítási munkaterhelési órákban, vagy határozatlan időre be kell kapcsolni a bizalmas számítási feladatokhoz. <br>A magas CPU-kihasználtsággal vagy az alacsonyabb virtuális mag konfigurált kiszolgálókon futó munkaterhelések esetén körültekintően járjon el a várakozási statisztika engedélyezésekor. Nem lehet határozatlan ideig bekapcsolni. 

A várakozási eseménytípus hasonló módon kombinálja a különböző várakozási eseményeket a gyűjtők között. A lekérdezési tároló a várakozási esemény típusát, az adott várakozási esemény nevét és a kérdéses lekérdezést biztosítja. A várakozási idő és a lekérdezési futtatókörnyezet statisztikájának összekapcsolása azt jelenti, hogy mélyebben meg kell ismernie, hogy mi járul hozzá a teljesítmény jellemzőinek lekérdezéséhez.

Íme néhány példa arra, hogyan szerezhet be több betekintést a számítási feladatokba a lekérdezési tároló várakozási statisztikájának használatával:

| **Általi képernyőfigyelés** | **Művelet** |
|---|---|
|Magas zárolási várakozások | Jelölje be az érintett lekérdezésekhez tartozó lekérdezési szövegeket, és azonosítsa a célként megadott entitásokat. A lekérdezési tárolóban megtekintheti azokat a lekérdezéseket, amelyek ugyanazt az entitást módosítják, amely gyakran és/vagy magas időtartammal van végrehajtva. A lekérdezések azonosítása után érdemes lehet módosítani az alkalmazás logikáját, hogy javítsa a párhuzamosságot, vagy használjon kevésbé korlátozó elkülönítési szintet. |
|Magas puffer IO-várakozások | A lekérdezési tárolóban megkeresheti a nagy számú fizikai olvasással rendelkező lekérdezéseket. Ha egyeznek a nagy i/o-várakozásokkal rendelkező lekérdezésekkel, érdemes lehet egy indexet bevezetni az alapul szolgáló entitáson, hogy a vizsgálat helyett a kereséseket végezze. Ez csökkentheti a lekérdezések i/o-terhelését. Tekintse át a kiszolgáló **teljesítményére vonatkozó javaslatokat** a portálon, és ellenőrizze, hogy vannak-e olyan indexelési javaslatok ehhez a kiszolgálóhoz, amely optimalizálja a lekérdezéseket. |
|Nagy memória-várakozások | A lekérdezési tárolóban megkeresheti a leggyakoribb memóriát használó lekérdezéseket. Ezek a lekérdezések valószínűleg késleltetik az érintett lekérdezések további előrehaladását. Tekintse át a kiszolgáló **teljesítményére vonatkozó javaslatokat** a portálon, és ellenőrizze, hogy vannak-e olyan indexelési javaslatok, amelyek optimalizálják ezeket a lekérdezéseket.|

## <a name="configuration-options"></a>Beállítási lehetőségek

Ha a lekérdezési tároló engedélyezve van, a rendszer 15 perces összesítési ablakokban tárolja az adatvesztést, és ablakban akár 500 különböző lekérdezést is beállíthat.

A lekérdezési tároló paramétereinek konfigurálásához a következő beállítások érhetők el.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány** |
|---|---|---|---|
| query_store_capture_mode | A lekérdezési tároló funkció be-és kikapcsolása az érték alapján. Megjegyzés: Ha a performance_schema ki van kapcsolva, a bekapcsolás query_store_capture_mode bekapcsolja performance_schema és a teljesítmény-séma eszközeinek egy részhalmazát, amely ehhez a szolgáltatáshoz szükséges. | ALL | NINCS, AZ ÖSSZES |
| query_store_capture_interval | A lekérdezési tár rögzítési időköze percben kifejezve. Lehetővé teszi a lekérdezési metrikák összesítésének intervallumának megadását | 15 | 5 - 60 |
| query_store_capture_utility_queries | Be-és kikapcsolás a rendszeren futtatott összes segédprogram-lekérdezés rögzítéséhez. | NO | IGEN, NEM |
| query_store_retention_period_in_days | A lekérdezési tárolóban tárolt adatmegőrzési idő (nap). | 7 | 1 - 30 |

A következő lehetőségek kifejezetten a várakozási statisztikára vonatkoznak.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Engedélyezi a várakozási statisztika bekapcsolását vagy kikapcsolását. | NEZ egy | NINCS, AZ ÖSSZES |
| query_store_wait_sampling_frequency | Megváltoztatja a várakozási mintavételezés gyakoriságát másodpercben. 5 – 300 másodperc. | 30 | 5-300 |

> [!NOTE]
> Jelenleg **query_store_capture_mode** felülbírálja ezt a konfigurációt, ami azt jelenti, hogy mind a **query_store_capture_mode** , mind pedig a **query_store_wait_sampling_capture_modenak** engedélyezve kell lennie a várakozási statisztikák működéséhez. Ha a **query_store_capture_mode** ki van kapcsolva, a várakozási statisztikák ki vannak kapcsolva, a várakozási statisztikák pedig a performance_schema engedélyezve lehetőséget, a lekérdezési tároló által rögzített query_texteket is.

A [Azure Portal](howto-server-parameters.md) használatával beolvashatja vagy beállíthatja a paraméterhez tartozó eltérő értéket.

## <a name="views-and-functions"></a>Nézetek és függvények

A lekérdezési tárolót a következő nézetekkel és függvényekkel tekintheti meg és kezelheti. A [Select jogosultsági nyilvános szerepkörben](howto-create-users.md#create-more-admin-users) bárki megtekintheti ezeket a nézeteket a lekérdezési tárolóban lévő információk megjelenítéséhez. Ezek a nézetek csak a **MySQL** -adatbázisban érhetők el.

A lekérdezések normalizálása úgy történik, hogy a konstansok és konstansok eltávolítása után megvizsgálják a szerkezetét. Ha két lekérdezés megegyezik a literális értékektől, akkor ugyanazzal a kivonattal fog rendelkezni.

### <a name="mysqlquery_store"></a>mysql.query_store

Ez a nézet a lekérdezési tárolóban lévő összes adathalmazt adja vissza. Minden különböző adatbázis-AZONOSÍTÓhoz, felhasználói AZONOSÍTÓhoz és lekérdezési AZONOSÍTÓhoz egy sor van.

| **Név** | **Adattípus** | **IS_NULLABLE** | **Leírás** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | A séma neve |
| `query_id`| bigint (20) | NO| Az adott lekérdezéshez generált egyedi azonosító, ha ugyanazt a lekérdezést különböző sémában hajtja végre, a rendszer új azonosítót fog generálni. |
| `timestamp_id` | időbélyeg| NO| A lekérdezés végrehajtásának időbélyegzője. Ez a query_store_interval konfiguráción alapul.|
| `query_digest_text`| LONGTEXT| NO| A normalizált lekérdezés szövege az összes literál eltávolítása után|
| `query_sample_text` | LONGTEXT| NO| A tényleges lekérdezés első megjelenése literálokkal|
| `query_digest_truncated` | bit| IGEN| Azt jelzi, hogy a lekérdezés szövege csonkítva lett-e. Az érték igen, ha a lekérdezés hosszabb 1 KB-nál|
| `execution_count` | bigint (20)| NO| A lekérdezésnek az időbélyeg-AZONOSÍTÓhoz/a beállított intervallum időszakában végrehajtott végrehajtásainak száma|
| `warning_count` | bigint (20)| NO| A belső művelet során a lekérdezés által generált figyelmeztetések száma|
| `error_count` | bigint (20)| NO| A lekérdezés által az intervallum során generált hibák száma|
| `sum_timer_wait` | double| IGEN| A lekérdezés teljes végrehajtási ideje az intervallumban|
| `avg_timer_wait` | double| IGEN| A lekérdezés átlagos végrehajtási ideje az intervallumban|
| `min_timer_wait` | double| IGEN| A lekérdezés minimális végrehajtási ideje|
| `max_timer_wait` | double| IGEN| Maximális végrehajtási idő|
| `sum_lock_time` | bigint (20)| NO| A lekérdezés végrehajtásához tartozó összes zárolásra fordított teljes idő az adott időszak alatt|
| `sum_rows_affected` | bigint (20)| NO| Érintett sorok száma|
| `sum_rows_sent` | bigint (20)| NO| Az ügyfélnek eljuttatott sorok száma|
| `sum_rows_examined` | bigint (20)| NO| Megvizsgált sorok száma|
| `sum_select_full_join` | bigint (20)| NO| Teljes illesztések száma|
| `sum_select_scan` | bigint (20)| NO| Kijelölési vizsgálatok száma |
| `sum_sort_rows` | bigint (20)| NO| Rendezett sorok száma|
| `sum_no_index_used` | bigint (20)| NO| Ennyi alkalommal, amikor a lekérdezés nem használ indexeket|
| `sum_no_good_index_used` | bigint (20)| NO| Ennyi alkalommal, amikor a lekérdezés-végrehajtó motor nem használ jó indexeket|
| `sum_created_tmp_tables` | bigint (20)| NO| A létrehozott ideiglenes táblák száma összesen|
| `sum_created_tmp_disk_tables` | bigint (20)| NO| A lemezen létrehozott ideiglenes táblák teljes száma (I/O)|
| `first_seen` | időbélyeg| NO| A lekérdezés első előfordulása (UTC) az összesítési ablakban|
| `last_seen` | időbélyeg| NO| A lekérdezés utolsó előfordulása (UTC) ebben az összesítési ablakban|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Ez a nézet visszaadja az események várakozási idejének értékét a lekérdezési tárolóban. Minden különböző adatbázis-AZONOSÍTÓhoz, felhasználói AZONOSÍTÓhoz, lekérdezési AZONOSÍTÓhoz és eseményhez egy sor van.

| **Név**| **Adattípus** | **IS_NULLABLE** | **Leírás** |
|---|---|---|---|
| `interval_start` | időbélyeg | NO| Az időköz kezdete (15 perces növekmény)|
| `interval_end` | időbélyeg | NO| Az intervallum vége (15 perces növekmény)|
| `query_id` | bigint (20) | NO| Generált egyedi azonosító a normalizált lekérdezésen (a lekérdezési tárolóból)|
| `query_digest_id` | varchar (32) | NO| A normalizált lekérdezés szövege az összes literál eltávolítása után (a lekérdezési tárolóból) |
| `query_digest_text` | LONGTEXT | NO| A tényleges lekérdezés első megjelenése literálokkal (a lekérdezési tárolóból) |
| `event_type` | varchar (32) | NO| A várakozási Esemény kategóriája |
| `event_name` | varchar (128) | NO| A várakozási esemény neve |
| `count_star` | bigint (20) | NO| A lekérdezés intervallumában mintavétel alatt álló várakozási események száma |
| `sum_timer_wait_ms` | double | NO| A lekérdezés teljes várakozási ideje (ezredmásodpercben) az intervallumban |

### <a name="functions"></a>Functions

| **Név**| **Leírás** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | A lekérdezési tár összes adatának kiürítése a megadott időbélyegző előtt |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | A várakozási esemény összes adatának törlése a megadott időbélyegző előtt |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Kiüríti azokat a javaslatokat, amelyek lejárta a megadott időbélyeg előtt van |

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

- Ha a MariaDB-kiszolgáló rendelkezik a paraméterrel `default_transaction_read_only` , a Query Store nem tudja rögzíteni az adatmennyiséget.
- A lekérdezés-tárolási funkció megszakítható, ha hosszú Unicode-lekérdezéseket ( \> = 6000 bájt) tapasztal.
- A várakozási statisztikák megőrzési időtartama 24 óra.
- A várakozási statisztikában a példa az események töredékét rögzíti. A gyakoriság módosítható a paraméter használatával `query_store_wait_sampling_frequency` .

## <a name="next-steps"></a>Következő lépések

- További információ a [lekérdezési teljesítményről](concepts-query-performance-insight.md)
