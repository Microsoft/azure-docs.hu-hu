---
title: Adatok migrálása egy PostgreSQL-adatbázisból egy Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportba
titleSuffix: Azure Arc enabled database services
description: Adatok migrálása egy PostgreSQL-adatbázisból egy Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643575"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>A PostgreSQL-adatbázis migrálása az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportba

Ez a dokumentum ismerteti azokat a lépéseket, amelyekkel lekérheti a meglévő PostgreSQL-adatbázist (amely nem az Azure arc-kompatibilis Data Services) az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportba.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Megfontolandó szempontok

Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgáló csoport a PostgreSQL közösségi verziója, és a CitusData bővítmény engedélyezésével fut. Így minden olyan eszköz, amely a PostgreSQL-en kívül működik az Azure arc-on, az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport használatával kell működnie.


Ennek megfelelően a postgres-hez jelenleg használt eszközkészlettel a következőket teheti:
1. A postgres-adatbázis biztonsági mentése az Azure arc szolgáltatáson kívül üzemeltetett példányból
2. Visszaállítás az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban

A teendők a következőket teszik:
- a kiszolgáló paramétereinek alaphelyzetbe állítása
- biztonsági környezetek alaphelyzetbe állítása: a felhasználók, szerepkörök és alaphelyzetbe állítási engedélyek újbóli létrehozása...

Ehhez a biztonsági mentési/visszaállítási művelethez bármilyen olyan eszközt használhat, amely képes a postgres biztonsági mentésére és visszaállítására. Például:
- Azure Data Studio és az postgres bővítmény
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Példa
Lássuk ezeket a lépéseket az eszköz használatával `pgAdmin` .
Vegye figyelembe a következő beállítást:
- **Forrás**  
    Egy operációs rendszer nélküli kiszolgálón futó postgres-kiszolgáló és a JEANYDSRV nevű kiszolgáló. Ez a 12-es verzió, és egy MyOnPremPostgresDB nevű adatbázist üzemeltet, amely egy T1 tábla, amely 1 sor :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="migrálása – forrás":::

- **Cél**  
    Egy Azure arc-környezetben és postgres01 nevű postgres-kiszolgáló. Ez a 12-es verzió. A standard postgres-adatbázis kivételével nem rendelkezik adatbázissal.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrálás – cél":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Készítsen biztonsági másolatot a forrás-adatbázisról a helyszínen

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrálás – forrás – biztonsági mentés":::

Konfigurálás:
1. Adja meg a fájl nevét: **MySourceBackup**
2. A formátum beállítása **Egyéni** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="áttelepítés – forrás – biztonsági mentés – konfigurálás":::

A biztonsági mentés sikeresen befejeződött:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrálás – forrás – biztonsági mentés – kész":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Üres adatbázis létrehozása a célszámítógépen az Azure arc engedélyezve PostgreSQL nagy kapacitású-kiszolgáló csoportjában

> [!NOTE]
> Ha postgres-példányt szeretne regisztrálni az `pgAdmin` eszközön, akkor a példányának nyilvános IP-címét kell használnia a Kubernetes-fürtben, és megfelelően kell beállítania a portot és a biztonsági környezetet. Ezeket a részleteket a `psql` végponti sorban találja a következő parancs futtatása után:

```console
azdata arc postgres endpoint list -n postgres01
```
A következőhöz hasonló kimenetet ad vissza:
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

Nevezze el a céladatbázis **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrálás – cél – adatbázis – létrehozás" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Az adatbázis visszaállítása az ív-telepítőben

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-adatbázis – visszaállítás":::

Állítsa be a visszaállítást:
1. Mutasson arra a fájlra, amely a visszaállítani kívánt biztonsági másolatot tartalmazza: **MySourceBackup**
2. Tartsa meg a formátumot **egyéni vagy tar** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrálás-db-Restore-configure"::: értékre

3. Kattintson a **visszaállítás** gombra.  

   A visszaállítás sikeres.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrálás-db-Restore-Completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Annak ellenőrzése, hogy sikerült-e visszaállítani az adatbázist az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban

Használja az alábbi módszerek egyikét:

**Forrás `pgAdmin` :**  

Bontsa ki az Azure arc Setup-ban üzemeltetett postgres-példányt. Ekkor megjelenik a visszaállított adatbázisban található tábla, és amikor kiválasztja az adathalmazt, amely ugyanazt a sort mutatja, mint a helyszíni példányban:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrálás-db-Restore-ellenőrzés":::

**Az `psql` Azure-ív beállításán belül:**  

Az ív telepítője segítségével csatlakozhat a `psql` postgres-példányhoz, beállíthatja az adatbázis környezetét, `RESTORED_MyOnPremPostgresDB` és lekérdezheti az adatait:

1. A kapcsolódási karakterláncot segítő végpontok listázása `psql` :

   ```console
   azdata arc postgres endpoint list -n postgres01
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

1. A `psql` kapcsolódási karakterláncban használja a `-d` paramétert az adatbázis nevének jelzéséhez. Az alábbi paranccsal a rendszer kérni fogja a jelszót:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` csatlakozik.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Válassza ki a táblázatot, és a helyszíni postgres-példányból visszaállított adatok láthatók:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Nem fogja látni az Azure arc-kompatibilis PostgreSQL-nagy kapacitású futtatásának teljesítménybeli előnyeit, amíg ki nem bővíti a horizontális felskálázást, és a PostgreSQL nagy kapacitású-kiszolgálócsoport munkavégző csomópontjain át kell osztania/terjesztenie az adatmennyiséget. Lásd a [következő lépéseket](#next-steps).
>
> - Ma már nem lehet "bevezetni az Azure-ívet" egy meglévő postgres-példány, amely a helyszínen vagy bármely más felhőben fut. Más szóval nem lehet telepíteni valamilyen "Azure arc-ügynököt" a meglévő postgres-példányra, hogy az Azure arc által engedélyezett postgres-telepítővé váljon. Ehelyett létre kell hoznia egy új postgres-példányt, és át kell helyeznie az adatátvitelt. Használhatja a fentebb bemutatott technikát, vagy használhat tetszőleges ETL-eszközt is.

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Azure Database for PostgreSQL nagy kapacitású kapcsolatos fogalmakat és útmutatókat az adatok több PostgreSQL nagy kapacitású-csomóponton keresztüli terjesztéséhez, valamint a Azure Database for PostgreSQL nagy kapacitású teljesítményének kihasználása érdekében:
    * [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
    * [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
    * [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * Ezekben a dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, és **hozzon létre egy Azure-adatbázist a postgres-nagy kapacitású (Citus) számára**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

- [A rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoport horizontális felskálázása](scale-out-postgresql-hyperscale-server-group.md)
