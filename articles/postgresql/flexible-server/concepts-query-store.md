---
title: Lekérdezéstár – Azure Database for PostgreSQL – Flex Server
description: Ez a cikk a Lekérdezéstár szolgáltatást ismerteti a Azure Database for PostgreSQL - Flex Server szolgáltatásban.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559082"
---
# <a name="monitor-performance-with-query-store"></a>Teljesítmény figyelése a lekérdezéstár használatával
**A következőkre vonatkozik:** Azure Database for PostgreSQL – A Flex Server 11-es és újabb verziói

A lekérdezéstár szolgáltatás Azure Database for PostgreSQL lehetővé teszi a lekérdezési teljesítmény nyomon követését az idő alatt. A Lekérdezéstár leegyszerűsíti a teljesítmény-hibaelhárítást, mert segít gyorsan megtalálni a leghosszabb ideig futó és leginkább erőforrás-igényes lekérdezéseket. A lekérdezéstár automatikusan rögzíti a lekérdezések és a futásidejű statisztikák előzményeit, és megőrzi azokat a felülvizsgálathoz. Idő szerint szeleteli az adatokat, így láthatja az időbeli használati mintákat. Az összes felhasználó, adatbázis és lekérdezés adatait a  rendszer a azure_sys nevű adatbázisban Azure Database for PostgreSQL tárolja.

> [!IMPORTANT]
> Ne módosítsa a **azure_sys** adatbázisát vagy sémáját. Ezzel megakadályozhatja, hogy a lekérdezéstár és a kapcsolódó teljesítményfunkciók megfelelően működjön.
## <a name="enabling-query-store"></a>A lekérdezéstár engedélyezése
A lekérdezéstár egy engedélyezett funkció, így alapértelmezés szerint nincs engedélyezve a kiszolgálón. A lekérdezéstár globálisan engedélyezve van vagy le van tiltva egy adott kiszolgálón található összes adatbázishoz, és adatbázisonként nem lehet be- vagy kikapcsolni.
### <a name="enable-query-store-using-the-azure-portal"></a>Lekérdezéstár engedélyezése a Azure Portal
1. Jelentkezzen be a Azure Portal, és válassza ki Azure Database for PostgreSQL kiszolgálót.
2. A **menü Beállítások** szakaszában válassza a Kiszolgálóparaméterek lehetőséget. 
3. Keresse meg a `pg_qs.query_capture_mode` paramétert.
4. Állítsa az értéket vagy értékre, majd mentse a `TOP` `ALL` **következőt:**.
Engedélyezze az első köteg adat 20 percig történő tárolását az azure_sys adatbázisban.
## <a name="information-in-query-store"></a>Információk a lekérdezéstárban
A lekérdezéstár egy tárolóval rendelkezik:
- Egy futásidejű stats tároló a lekérdezés-végrehajtási statisztika információinak megőrzéséhez.

A lekérdezéstár használatának gyakori forgatókönyvei a következők:
- A lekérdezések adott időablakban végrehajtott számának meghatározása
- Egy lekérdezés átlagos végrehajtási időének összehasonlítása az időablakok között a nagy változások összehasonlítása érdekében
- Az elmúlt néhány órában a leghosszabb ideig futó lekérdezések azonosítása A lemezterület használatának minimalizálása érdekében a futtatókörnyezet végrehajtási statisztikáit a rendszer egy rögzített, konfigurálható időablakban összesíti. Az ezekben a tárolókban található információk nézetek használatával kérdezhetőek le.
## <a name="access-query-store-information"></a>Lekérdezéstár információinak elérése
A lekérdezéstár adatait a rendszer azure_sys Postgres-kiszolgálón található adatbázisában tárolja. A következő lekérdezés a lekérdezéstárban található lekérdezésekkel kapcsolatos információkat ad vissza:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Beállítási lehetőségek
Ha a Lekérdezéstár engedélyezve van, az 15 perces összesítő ablakokban, ablakonként legfeljebb 500 különböző lekérdezésben menti az adatokat. A lekérdezéstár paramétereinek konfigurálásához az alábbi beállítások érhetők el.

| **Paraméter** | **Leírás** | **Alapértelmezett** | **Tartomány**|
|---|---|---|---|
| pg_qs.query_capture_mode | Beállítja, hogy mely utasítások vannak nyomon követve. | Nincs | none, top, all |
| pg_qs.max_query_text_length | Beállítja a menthető lekérdezések maximális hosszát. A hosszabb lekérdezések csonkolódnak. | 6000 | 100 – 10 ezer |
| pg_qs.retention_period_in_days | Beállítja a megőrzési megőrzési időszakot. | 7 | 1 - 30 |
| pg_qs.track_utility | Beállítja, hogy a rendszer nyomon kövesse-e a segédprogramparancsokat | itt: | on, off |

A [Azure Portal](howto-configure-server-parameters-using-portal.md) paraméterhez más értéket is be lehet szerezni vagy be lehet állítani.

## <a name="views-and-functions"></a>Nézetek és függvények
A lekérdezéstárat az alábbi nézetekkel és függvényekkel lehet megtekinteni és kezelni. A PostgreSQL nyilvános szerepkörében bárki használhatja ezeket a nézeteket az adatok lekérdezéstárban való megtekintéshez. Ezek a nézetek csak a azure_sys **érhetők** el.
A lekérdezések normalizálása a konstansok és konstansok eltávolítása után a szerkezetük alapján lesz normalizálva. Ha két lekérdezés azonos, a konstansértékek kivételével, ugyanaz lesz a lekérdezésazonosítójuk.
### <a name="query_storeqs_view"></a>query_store.qs_view
Ez a nézet a Lekérdezéstár összes adatát visszaadja. Minden külön adatbázis-azonosítóhoz, felhasználói azonosítóhoz és lekérdezésazonosítóhoz egy sor van. 

|**Név**   |**Típus** | **Hivatkozások**  | **Leírás**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | Azonosító a runtime_stats_entries táblából|
|user_id    |Oid    |pg_authid.oid  |A utasítást végrehajtott felhasználó OID-e|
|db_id  |Oid    |pg_database.oid    |Annak az adatbázisnak az OID-edike, amelyben az utasítást végrehajtották|
|query_id   |bigint  || Belső kivonatkód, amely az utasítás elemzési fájából van kiszámítva|
|query_sql_text |Varchar(10000)  || A reprezentatív nyilatkozat szövege. Az azonos struktúrával rendelkezik különböző lekérdezések csoportosítva vannak; Ez a szöveg a fürt első lekérdezésének szövege.|
|plan_id    |bigint |   |A lekérdezésnek megfelelő terv azonosítója, amely még nem érhető el|
|start_time |időbélyeg  ||  A lekérdezések idő gyűjtők szerint vannak összesítve – a gyűjtők időtartománya alapértelmezés szerint 15 perc. Ez a bejegyzés idő gyűjtőének megfelelő kezdési idő.|
|end_time   |időbélyeg  ||  A bejegyzéshez tartozó idő gyűjtőnek megfelelő záró idő.|
|Hívások  |bigint  || A lekérdezés által végrehajtott műveletek száma|
|total_time |dupla pontosság   ||  Lekérdezések teljes végrehajtási ideje, ezredmásodpercben|
|min_time   |dupla pontosság   ||  A lekérdezés minimális végrehajtási ideje, ezredmásodpercben|
|max_time   |dupla pontosság   ||  Lekérdezések maximális végrehajtási ideje, ezredmásodpercben|
|mean_time  |dupla pontosság   ||  Lekérdezések átlagos végrehajtási ideje, ezredmásodpercben|
|stddev_time|   dupla pontosság    ||  A lekérdezés végrehajtási időének szórása ezredmásodpercben |
|Sorok   |bigint ||  A utasítás által lekért vagy érintett sorok teljes száma|
|shared_blks_hit|   bigint  ||  A megosztott blokkok gyorsítótára által a utasítás által találatok teljes száma|
|shared_blks_read|  bigint  ||  A utasítás által beolvasott megosztott blokkok teljes száma|
|shared_blks_dirtied|   bigint   || A utasítás által lekért megosztott blokkok teljes száma |
|shared_blks_written|   bigint  ||  A utasítás által írt megosztott blokkok teljes száma|
|local_blks_hit|    bigint ||   A helyi blokkgyorsítótár által a utasítás által találatok teljes száma|
|local_blks_read|   bigint   || A utasítás által beolvasott helyi blokkok teljes száma|
|local_blks_dirtied|    bigint  ||  A utasítás által lekért helyi blokkok teljes száma|
|local_blks_written|    bigint  ||  A utasítás által írt helyi blokkok teljes száma|
|temp_blks_read |bigint  || A utasítás által beolvasott ideiglenes blokkok teljes száma|
|temp_blks_written| bigint   || A utasítás által írt ideiglenes blokkok teljes száma|
|blk_read_time  |dupla pontosság    || A blokkok olvasásával töltött teljes idő ezredmásodpercben (ha track_io_timing van engedélyezve, egyébként nulla)|
|blk_write_time |dupla pontosság    || A blokkok írásával töltött teljes idő ezredmásodpercben (ha track_io_timing van engedélyezve, egyébként nulla)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_egyedek_nézete
Ez a nézet lekérdezési szöveges adatokat ad vissza a Lekérdezéstárban. Minden külön oszlophoz egy sor query_text.

| **Név** | **Típus** | **Leírás** |
|--|--|--|
| query_text_id | bigint | A query_texts azonosítója |
| query_sql_text | Varchar(10000) | A reprezentatív nyilatkozat szövege. Az azonos struktúrával rendelkezik különböző lekérdezések csoportosítva vannak; Ez a szöveg a fürt első lekérdezésének szövege. |

### <a name="functions"></a>Functions
`qs_reset` elveti a Lekérdezéstár által eddig gyűjtött statisztikákat. Ezt a függvényt csak a kiszolgálói rendszergazdai szerepkör hajthatja végre.

`staging_data_reset` elveti a lekérdezéstár által a memóriában gyűjtött összes statisztikát (ez azt jelenti, hogy a memóriában tárolt adatok még nem törlődtek az adatbázisba). Ezt a függvényt csak a kiszolgálói rendszergazdai szerepkör hajthatja végre.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
- Ha egy PostgreSQL-kiszolgálón be van default_transaction_read_only paraméter, a lekérdezéstár nem rögzít adatokat.
## <a name="next-steps"></a>Következő lépések
- További információ az [olyan forgatókönyvekről, ahol a Lekérdezéstár különösen hasznos lehet.](concepts-query-store-scenarios.md)
- További információ a [Lekérdezéstár használatának ajánlott eljárásairól.](concepts-query-store-best-practices.md)
