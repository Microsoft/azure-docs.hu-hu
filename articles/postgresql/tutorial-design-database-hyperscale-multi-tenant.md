---
title: 'Oktatóanyag: több-bérlős adatbázis tervezése – nagy kapacitású (Citus) – Azure Database for PostgreSQL'
description: Ez az oktatóanyag bemutatja, hogyan lehet egy skálázható, több-bérlős alkalmazást Azure Database for PostgreSQL nagy kapacitású (Citus) bekapcsolni.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: d8dee086f55c5596af50245b5271d98536eb7e57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026199"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Oktatóanyag: több-bérlős adatbázis tervezése Azure Database for PostgreSQL – nagy kapacitású (Citus) használatával

Ebben az oktatóanyagban a Azure Database for PostgreSQL-nagy kapacitású (Citus) használatával megismerheti a következőket:

> [!div class="checklist"]
> * Rugalmas skálázási (Citus) kiszolgálócsoport létrehozása
> * Séma létrehozása a psql segédprogram használatával
> * Több csomópont közötti szegmens táblák
> * Mintaadatok betöltése
> * Bérlői adatbázis lekérdezése
> * Az adatmegosztás a bérlők között
> * A séma testreszabása bérlő szerint

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Séma létrehozása a psql segédprogram használatával

Miután csatlakozott a Azure Database for PostgreSQL-nagy kapacitású (Citus) a psql használatával, elvégezheti néhány alapvető feladatot. Ez az oktatóanyag végigvezeti egy olyan webalkalmazás létrehozásának lépésein, amely lehetővé teszi a hirdetők számára a kampányok nyomon követését.

Több vállalat is használhatja az alkalmazást, ezért hozzunk létre egy táblázatot, amely a vállalatokat és egy másikat tart fenn a kampányok számára. Futtassa a következő parancsokat a psql-konzolon:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Minden kampány a hirdetések futtatására fog fizetni. Vegyen fel egy táblázatot a hirdetéseket is, ha a fenti kód után futtatja a következő kódot a psql-ben:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Végezetül nyomon követjük az egyes ad-kattintások és benyomások statisztikáit:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Az újonnan létrehozott táblákat a psql mostantól a következő parancs futtatásával tekintheti meg:

```postgres
\dt
```

A több-bérlős alkalmazások csak bérlők számára kényszerítik az egyediséget, ezért az elsődleges és a külső kulcsok esetében is a vállalati azonosító is érvényes.

## <a name="shard-tables-across-nodes"></a>Több csomópont közötti szegmens táblák

A nagy kapacitású központi telepítése a különböző csomópontokon lévő táblázat sorait egy felhasználó által kijelölt oszlop értéke alapján tárolja. Ez a "terjesztési oszlop" jelzi, hogy melyik bérlő tulajdonosa a soroknak.

Állítsa be a terjesztési oszlopot vállalati \_ azonosítóként, a bérlő azonosítójának megadásával. A psql-ben futtassa a következő függvényeket:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

A psql kívül most a normál parancssorban töltse le a minta adatkészleteket:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

A psql-ben újra betölti az adatmennyiséget. Ügyeljen arra, hogy a psql ugyanabban a könyvtárban fusson, ahol letöltötte az adatfájlokat.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Ezek az adatfeldolgozó csomópontok között lesznek elosztva.

## <a name="query-tenant-data"></a>Bérlői adatbázis lekérdezése

Ha az alkalmazás egyetlen bérlő számára kér le adatkérést, akkor az adatbázis egyetlen feldolgozó csomóponton hajthatja végre a lekérdezést. Az egybérlős lekérdezések egyetlen bérlői azonosító alapján szűrhetők. A következő lekérdezési szűrők például a `company_id = 5` hirdetéseket és a benyomásokat jelenítik meg. Próbálja meg futtatni a psql az eredmények megtekintéséhez.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Az adatmegosztás a bérlők között

Addig is, amíg az összes táblát el nem terjesztette `company_id` , de bizonyos adatok természetesen nem "tartoznak" egyetlen bérlőhöz sem, és megoszthatók. Előfordulhat például, hogy az ad platform összes vállalata az IP-címek alapján földrajzi adatokat szeretne kapni a célközönségnek.

Hozzon létre egy táblázatot a közös földrajzi információk tárolásához. Futtassa a következő parancsokat a psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Ezután hozzon `geo_ips` egy "hivatkozási táblázatot" a tábla egy példányának tárolásához minden munkavégző csomóponton.

```sql
SELECT create_reference_table('geo_ips');
```

Töltse be például az adathalmazt. Ne felejtse el futtatni ezt a parancsot a psql-ben abban a könyvtárban, ahol letöltötte az adatkészletet.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

A Clicks táblázat a Geo IP-címekkel való csatlakoztatása \_ minden csomóponton hatékonyan működik.
Itt találja az ad-ra rákattintott mindenki helyét
290. Próbálja meg futtatni a lekérdezést a psql-ben.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>A séma testreszabása bérlő szerint

Előfordulhat, hogy minden bérlőnek a mások számára nem szükséges speciális adatokat kell tárolnia. Azonban minden bérlő közös infrastruktúrát oszt meg azonos adatbázis-sémával. Hol lehet a további adatvesztés?

Az egyik trükk az, hogy egy nyílt végű típusú oszlopot, például a PostgreSQL JSONB használja.  A séma tartalmaz egy JSONB mezőt a `clicks` híváshoz `user_data` .
Egy vállalat (mondjuk az öt vállalat) az oszlopot használva nyomon követheti, hogy a felhasználó mobil eszközön van-e.

A következő lekérdezésből megtudhatja, hogy ki és Mikor szeretne többet: mobil vagy hagyományos látogató.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Ezt a lekérdezést egyetlen vállalat számára is optimalizálhatja [részleges index](https://www.postgresql.org/docs/current/static/indexes-partial.html)létrehozásával.

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Általánosabban létrehozhatunk egy gin- [indexeket](https://www.postgresql.org/docs/current/static/gin-intro.html) az oszlopban található összes kulcson és értéken.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy kiszolgálócsoport számára. Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálót. A kiszolgálócsoport *Áttekintés* lapján kattintson a *Törlés* gombra. Amikor a rendszer rákérdez egy előugró oldalra, erősítse meg a kiszolgálócsoport nevét, és kattintson a végleges *Törlés* gombra.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan építhet ki egy nagy kapacitású-(Citus-) kiszolgáló csoportot. Csatlakoztatta azt a psql-hoz, létrehozott egy sémát és egy elosztott adatkészletet. Megtanulta, hogy a bérlők között és között is lekérdezze az adatlekérdezést, valamint a séma személyre szabását a bérlőn belül.

- További információ a kiszolgálói csoportok [csomópontjainak típusairól](./concepts-hyperscale-nodes.md)
- A kiszolgálócsoport legjobb [kezdeti méretének](howto-hyperscale-scale-initial.md) meghatározása
