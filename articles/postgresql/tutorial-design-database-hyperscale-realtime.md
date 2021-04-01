---
title: 'Oktatóanyag: valós idejű irányítópult tervezése – nagy kapacitású (Citus) – Azure Database for PostgreSQL'
description: Ez az oktatóanyag bemutatja, hogyan integrálással valós idejű irányítópult-lekérdezéseket Azure Database for PostgreSQL nagy kapacitású (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 480af87519f8e11c14f009058b0f518bdfc34a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026267"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Oktatóanyag: valós idejű elemzési irányítópult tervezése Azure Database for PostgreSQL – nagy kapacitású (Citus) használatával

Ebben az oktatóanyagban a Azure Database for PostgreSQL-nagy kapacitású (Citus) használatával megismerheti a következőket:

> [!div class="checklist"]
> * Rugalmas skálázási (Citus) kiszolgálócsoport létrehozása
> * Séma létrehozása a psql segédprogram használatával
> * Több csomópont közötti szegmens táblák
> * Mintaadatok létrehozása
> * Kumulatív műveletek végrehajtása
> * Nyers és összesített adatainak lekérdezése
> * Az adatérvényesség lejárta

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Séma létrehozása a psql segédprogram használatával

Miután csatlakozott a Azure Database for PostgreSQL-nagy kapacitású (Citus) a psql használatával, elvégezheti néhány alapvető feladatot. Ez az oktatóanyag végigvezeti Önt a webes elemzések adatforgalmának betöltésén, majd az adatokon alapuló valós idejű irányítópultok biztosításához.

Hozzunk létre egy táblázatot, amely a nyers webes forgalmi adatokat fogja használni. Futtassa az alábbi parancsokat az psql-terminálban:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Létre fogunk hozni egy táblázatot, amely a percenkénti összesítéseket fogja tárolni, és egy táblázatot, amely az utolsó összesítés pozícióját tartja karban. Futtassa a következő parancsokat a psql is:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Az újonnan létrehozott táblákat a következő psql paranccsal tekintheti meg a táblák listájában:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Több csomópont közötti szegmens táblák

A nagy kapacitású központi telepítése a különböző csomópontokon lévő táblázat sorait egy felhasználó által kijelölt oszlop értéke alapján tárolja. Ez a "terjesztési oszlop" azt jelzi, hogy az adatszegmensek hogyan oszlanak el a csomópontok között.

Állítsa be a terjesztési oszlopot, hogy a hely \_ azonosítója, a Szilánk kulcsa legyen. A psql-ben futtassa a következő függvényeket:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

A kiszolgálói csoportnak most már készen kell állnia az egyes adatbevitelre. A következő helyileg futtathatjuk a `psql` kapcsolódást az adatok folyamatos beszúrásához.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A lekérdezés másodpercenként körülbelül nyolc sort szúr be. A sorok tárolása különböző munkavégző csomópontokon történik, a terjesztési oszlop által irányított módon `site_id` .

   > [!NOTE]
   > Hagyja meg a futó adatlétrehozási lekérdezést, és nyisson meg egy második psql-kapcsolatokat az oktatóanyag többi parancsához.
   >

## <a name="query"></a>Lekérdezés

A nagy kapacitású-üzemeltetési beállítás lehetővé teszi, hogy több csomópont is feldolgozza a lekérdezéseket párhuzamosan a sebesség érdekében. Az adatbázis például kiszámítja az összesítéseket, például a munkavégző csomópontok ÖSSZEGét és DARABSZÁMát, és az eredményeket a végső válaszként ötvözi.

Íme egy lekérdezés a webes kérelmek percenkénti számlálásához, néhány statisztikával együtt.
Próbálja meg futtatni a psql-ben, és figyelje meg az eredményeket.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Adattovábbítás

Az előző lekérdezés a korai fázisokban jól működik, de a teljesítménye az adatskála szerint csökken. Az elosztott feldolgozás még az adatok előzetes kiszámítását is lehetővé teszi, mint az ismételt kiszámításához.

Biztosítjuk, hogy az irányítópulton gyorsan maradjon a nyers adatokat összesítő táblázatba. Kísérletezhet az összesítési időtartammal. Egy percenkénti összesítési táblázatot használunk, de az adatmennyiség 5, 15 vagy 60 percre megszakítható.

Ha könnyebben szeretné futtatni ezt a összesítést, egy plpgsql-függvénybe tesszük. Futtassa ezeket a parancsokat a psql a függvény létrehozásához `rollup_http_request` .

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

A függvény helyett hajtsa végre az adatösszesítést:

```sql
SELECT rollup_http_request();
```

Az adatokat pedig előre összevont formában is lekérdezheti a kumulatív táblázatból, hogy ugyanazt a jelentést kapja, mint korábban. Futtassa az alábbi lekérdezést:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Lejáró régi adatértékek

Az összesítések gyorsabb lekérdezéseket tesznek lehetővé, de továbbra is le kell járni a régi adatmennyiségeket a nem kötött tárolási költségek elkerülése érdekében. Döntse el, hogy mennyi ideig szeretné megőrizni az adatokat az egyes részletességekhez, és normál lekérdezésekkel törölje a lejárt adatokat. A következő példában úgy döntöttünk, hogy egy hónapig egy nap és percenkénti összesítések alapján tartjuk a nyers adatmennyiséget:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Éles környezetben ezeket a lekérdezéseket becsomagolhatja egy függvénybe, és percenként meghívja azt egy cron-feladatban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy kiszolgálócsoport számára. Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálót. A kiszolgálócsoport *Áttekintés* lapján kattintson a *Törlés* gombra. Amikor a rendszer rákérdez egy előugró oldalra, erősítse meg a kiszolgálócsoport nevét, és kattintson a végleges *Törlés* gombra.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan építhet ki egy nagy kapacitású-(Citus-) kiszolgáló csoportot. Csatlakoztatta azt a psql-hoz, létrehozott egy sémát és egy elosztott adatkészletet. Megtanulta, hogyan lehet adatokat lekérdezni a nyers űrlapon, rendszeresen összesíti az adatokat, lekérdezni az összesített táblákat, és lejárati a régi adatokat.

- További információ a kiszolgálói csoportok [csomópontjainak típusairól](./concepts-hyperscale-nodes.md)
- A kiszolgálócsoport legjobb [kezdeti méretének](howto-hyperscale-scale-initial.md) meghatározása
