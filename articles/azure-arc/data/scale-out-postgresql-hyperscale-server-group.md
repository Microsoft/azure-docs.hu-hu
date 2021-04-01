---
title: Felskálázás Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport
description: Felskálázás Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17bdae658c7095c44a7ae9f30fd85a6c45bf1546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779974"
---
# <a name="scale-out-your-azure-arc-enabled-postgresql-hyperscale-server-group-by-adding-more-worker-nodes"></a>Több feldolgozó csomópont hozzáadásával bővítheti az Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgáló csoportját
Ez a dokumentum azt ismerteti, hogyan lehet felskálázást végezni egy Azure arc-kompatibilis PostgreSQL-nagy kapacitású-kiszolgálócsoport használatával. Ezt a forgatókönyvek átvételével végezheti el. **Ha nem kívánja futtatni a forgatókönyvet, és szeretné megtekinteni a vertikális felskálázást, ugorjon a bekezdés [felskálázása](#scale-out)lehetőségre**.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-started"></a>Bevezetés
Ha már ismeri az Azure arc engedélyezése PostgreSQL nagy kapacitású vagy Azure Database for PostgreSQL nagy kapacitású (Citus) skálázási modelljét, kihagyhatja ezt a bekezdést. Ha nem, javasoljuk, hogy a Azure Database for PostgreSQL nagy kapacitású (Citus) dokumentációs oldaláról olvassa el ezt a skálázási modellt. A Azure Database for PostgreSQL nagy kapacitású (Citus) ugyanaz a technológia, amely az Azure szolgáltatásként üzemel (a platform szolgáltatásként más néven Pásti), és nem az Azure arc-kompatibilis Data Services részeként elérhető:
- [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
- [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
- [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
- [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
- [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
- [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
- [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* A fenti dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, & **hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus)**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

## <a name="scenario"></a>Eset
Ez a forgatókönyv a PostgreSQL nagy kapacitású-kiszolgáló csoportra hivatkozik, amely az [Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport](create-postgresql-hyperscale-server-group.md) dokumentációjának létrehozása című témakörben példaként jött létre.

### <a name="load-test-data"></a>Tesztadatok betöltése
A forgatókönyv a nyilvánosan elérhető GitHub-adatok mintáját használja, amely a [Citus-adatok webhelyéről](https://www.citusdata.com/) érhető el (a Citus-adatok a Microsoft részét képezik).

#### <a name="connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Kapcsolódás az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgáló csoportjához

##### <a name="list-the-connection-information"></a>A kapcsolatok adatainak listázása
Kapcsolódjon az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgáló csoportjához a kapcsolódási adatok első beszerzésével: a parancs általános formátuma
```console
azdata arc postgres endpoint list -n <server name>
```
Például:
```console
azdata arc postgres endpoint list -n postgres01
```

Példa a kimenetre:

```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

##### <a name="connect-with-the-client-tool-of-your-choice"></a>Kapcsolódjon az Ön által választott ügyfél-eszközhöz.

Futtassa a következő lekérdezést annak ellenőrzéséhez, hogy jelenleg két (vagy több nagy kapacitású Worker csomópont) van, amelyek mindegyike egy Kubernetes Pod-nek felel meg:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(2 rows)
```

#### <a name="create-a-sample-schema"></a>Minta séma létrehozása
Hozzon létre két táblázatot a következő lekérdezés futtatásával:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

A JSONB a PostgreSQL-ben lévő bináris formátumú JSON-adattípus. Egy rugalmas sémát tárol egyetlen oszlopban és a PostgreSQL-sel. A séma egy GIN-indextel fog rendelkezni, hogy indexelje a benne található összes kulcsot és értéket. A GIN-indextel gyorsan és egyszerűen lekérdezheti a különböző feltételeket közvetlenül az adott adattartalomra. Így az adatbetöltése előtt hozzunk létre néhány indexet:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

A standard szintű táblázatok kihelyezéséhez futtasson egy lekérdezést minden táblához. Itt adhatja meg a szegmensbe felvenni kívánt táblázatot, valamint azt a kulcsot, amelyet át szeretne adni. A user_id az eseményeket és a felhasználók táblát is kiosztottuk:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

#### <a name="load-sample-data"></a>Mintaadatok betöltése
Az adatgyűjtés másolása... PROGRAMból:

```sql
COPY github_users FROM PROGRAM 'curl "https://examples.citusdata.com/users.csv"' WITH ( FORMAT CSV );
COPY github_events FROM PROGRAM 'curl "https://examples.citusdata.com/events.csv"' WITH ( FORMAT CSV );
```

#### <a name="query-the-data"></a>Adatok lekérdezése
És most mérjük meg, hogy egy egyszerű lekérdezés milyen hosszú időt vesz igénybe két csomóponttal:

```sql
SELECT COUNT(*) FROM github_events;
```
Jegyezze fel a lekérdezés végrehajtási idejét.


## <a name="scale-out"></a>Horizontális felskálázás
A kibővíthető parancs általános formátuma:
```console
azdata arc postgres server edit -n <server group name> -w <target number of worker nodes>
```

> [!CAUTION]
> Az előzetes verziójú kiadás nem támogatja a visszaskálázást. Például egyelőre nem lehetséges a munkavégző csomópontok számának a csökkentése. Ha erre kényszerülne, ki kell nyernie az adatokat, vagy biztonsági másolatot kell készítenie róluk, el kell vetnie a kiszolgálócsoportot, létre kell hoznia egy új, kevesebb munkavégző csomóponttal rendelkező kiszolgálócsoportot, majd importálnia kell az adatokat.

Ebben a példában a következő parancs futtatásával növeljük a munkavégző csomópontok számát 2 és 4 között:

```console
azdata arc postgres server edit -n postgres01 -w 4
```

Csomópontok hozzáadásakor a rendszer függő állapotot lát a kiszolgálócsoport számára. Például:
```console
azdata arc postgres server list
```

```console
Name        State          Workers
----------  -------------  ---------
postgres01  Pending 4/5    4
```

Ha a csomópontok elérhetővé válnak, a nagy kapacitású szegmens-újrakiegyensúlyozó automatikusan lefut, és újraterjeszti az új csomópontokra. A kibővíthető művelet egy online művelet. Amíg a csomópontok hozzá lettek adva, és az adatforgalom a csomópontokon át lesz osztva, az adatlekérdezések továbbra is elérhetők maradnak.

### <a name="verify-the-new-shape-of-the-server-group-optional"></a>A kiszolgálócsoport új alakjának ellenőrzése (nem kötelező)
Az alábbi módszerek bármelyikével ellenőrizheti, hogy a kiszolgálócsoport mostantól a felvett további munkavégző csomópontokat használja-e.

#### <a name="with-azdata"></a>A azdata:
Futtassa a következő parancsot:
```console
azdata arc postgres server list
```

Visszaadja a névtérben létrehozott kiszolgálócsoportok listáját, és jelzi a munkavégző csomópontok számát. Például:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

#### <a name="with-kubectl"></a>A kubectl:
Futtassa a következő parancsot:
```console
kubectl get postgresql-12
```

Visszaadja a névtérben létrehozott kiszolgálócsoportok listáját, és jelzi a munkavégző csomópontok számát. Például:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   4/4          10.0.0.4:31066      4d20h
```
> [!NOTE]
> Ha a 11 PostgreSQL-es verziójának a 12 helyett a Server-csoportot hozta létre, futtassa inkább a következő parancsot: _kubectl Get PostgreSQL-11_

#### <a name="with-a-sql-query"></a>SQL-lekérdezéssel:
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a következő lekérdezést:

```sql
SELECT * FROM pg_dist_node;
```

```console
 nodeid | groupid |                       nodename                        | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | metadatasynced | shouldhaveshards
--------+---------+-------------------------------------------------------+----------+----------+-------------+----------+----------+-------------+----------------+------------------
      1 |       1 | pg1-1.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      2 |       2 | pg1-2.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      3 |       3 | pg1-3.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
      4 |       4 | pg1-4.pg1-svc.default.svc.cluster.local |     5432 | default  | f           | t        | primary  | default     | f              | t
(4 rows)
```

## <a name="return-to-the-scenario"></a>Visszatérés a forgatókönyvhöz

Ha össze szeretné hasonlítani a Select Count lekérdezés végrehajtási idejét a minta adatkészleten, használja ugyanazt a Count lekérdezést. A négy munkavégző csomóponton, az SQL-utasítás módosítása nélkül is felhasználható.

```sql
SELECT COUNT(*) FROM github_events;
```
Jegyezze fel a végrehajtási időt.


> [!NOTE]
> A környezettől függően – Ha például a test Server `kubeadm` -csoportot egyetlen csomópontos virtuális gépen telepítette, akkor a végrehajtási idő szerény javulást tapasztalhat. Ha jobban szeretné megtudni, hogy milyen típusú teljesítmény-javulást érhet el az Azure arc-kompatibilis PostgreSQL-nagy kapacitású, tekintse meg a következő rövid videókat:
>* [Nagy teljesítményű HTAP az Azure PostgreSQL nagy kapacitású (Citus)](https://www.youtube.com/watch?v=W_3e07nGFxY)
>* [HTAP-alkalmazások fejlesztése a Python & Azure PostgreSQL nagy kapacitású (Citus) használatával](https://www.youtube.com/watch?v=YDT8_riLLs0)


## <a name="next-steps"></a>Következő lépések

- Olvassa el, hogyan [méretezhető fel és le (memória, virtuális mag) az Azure arc engedélyezve PostgreSQL nagy kapacitású Server Group](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- Olvassa el, hogyan állíthatja be a kiszolgálói paramétereket az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban
- Olvassa el a Azure Database for PostgreSQL nagy kapacitású kapcsolatos fogalmakat és útmutatókat az adatok több PostgreSQL nagy kapacitású-csomóponton keresztüli terjesztéséhez, valamint az Azure Database postgres nagy kapacitású nyújtotta előnyök kihasználása érdekében. :
    * [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
    * [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
    * [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

 > \* A fenti dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, & **hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus)**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
