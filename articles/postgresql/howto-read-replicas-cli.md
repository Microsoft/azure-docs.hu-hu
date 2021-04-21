---
title: Olvasási replikák kezelése – Azure CLI, REST API – Azure Database for PostgreSQL – Egyetlen kiszolgáló
description: Megtudhatja, hogyan kezelheti az olvasási replikákat a Azure Database for PostgreSQL – Egyetlen kiszolgáló az Azure CLI-ről és a REST API
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764692"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Olvasási replikák létrehozása és kezelése az Azure CLI-ről REST API

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for PostgreSQL az Azure CLI és a REST API. Az olvasási replikákról az áttekintésben [talál további információt.](concepts-read-replicas.md)

## <a name="azure-replication-support"></a>Azure-replikáció támogatása
[Az olvasási replikák](concepts-read-replicas.md) és a logikai [dekódolás](concepts-logical.md) is a Postgres előre írt naplótól (WAL) függ. A két funkcióhoz a Postgrestől eltérő naplózási szintekre van szükség. A logikai dekódolásnak magasabb naplózási szintre van szüksége, mint az olvasási replikáknak.

A megfelelő naplózási szint konfigurálásához használja az Azure-replikáció támogatási paraméterét. Az Azure-replikáció támogatása három beállítási lehetőséggel rendelkezik:

* **Kikapcsolva** – A legkevesebb információt helyezi el a WAL-ban. Ez a beállítás nem érhető el a legtöbb Azure Database for PostgreSQL kiszolgálón.  
* **Replika** – Részletesebb, mint a **Ki.** Ez az olvasási replikák minimálisan szükséges [naplózási](concepts-read-replicas.md) szintje. Ez a beállítás az alapértelmezett beállítás a legtöbb kiszolgálón.
* **Logikai –** Részletesebb, mint a **replika.** Ez a logikai dekódolás minimális naplózási szintje. Az olvasási replikák ezen a beállításon is működnek.


> [!NOTE]
> Ha olvasási replikákat helyez üzembe állandó nagy írásigényű elsődleges számítási feladatokhoz, a replikációs késés tovább növekedhet, és előfordulhat, hogy soha nem tudja behozni az elsődleges replikát. Ez növelheti az elsődleges tároló használatát is, mivel a WAL-fájlok nem törlődnek, amíg meg nem kapják azokat a replikán.

## <a name="azure-cli"></a>Azure CLI
Olvasási replikákat az Azure CLI használatával hozhat létre és kezelhet.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli)
- Egy [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-up-azure-cli.md) lesz az elsődleges kiszolgáló.


### <a name="prepare-the-primary-server"></a>Az elsődleges kiszolgáló előkészítése

1. Ellenőrizze az elsődleges kiszolgáló `azure.replication_support` értékét. Az olvasási replikák csak REPLIKaként működnek.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Ha `azure.replication_support` a nem legalább REPLIKA, állítsa be. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

Az [az postgres server replica create parancshoz](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) a következő paraméterekre van szükség:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Az erőforráscsoport, amelyben a replikakiszolgáló létre lesz hozva.  |
| name | mydemoserver-replica | A létrehozott új replikakiszolgáló neve. |
| source-server | mydemoserver | A replikálni szükséges meglévő elsődleges kiszolgáló neve vagy erőforrás-azonosítója. Használja az erőforrás-azonosítót, ha azt szeretné, hogy a replika és a főkiszolgáló erőforráscsoportja eltérő legyen. |

Az alábbi CLI-példában a replika ugyanabban a régióban jön létre, mint a főkiszolgáló.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Régiók közötti olvasási replika létrehozásához használja a `--location` paramétert. Az alábbi CLI-példa az USA nyugati régiója régión hozza létre a replikát.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, olvassa el a replikákkal kapcsolatos [fogalmakat.](concepts-read-replicas.md) 

Ha a paramétert nem a REPLICA (REPLIKA) beállításra általános célú vagy a Memóriaoptimalált elsődleges kiszolgálón, és újraindította a `azure.replication_support` kiszolgálót, hibaüzenetet kap.  A replika létrehozása előtt ismételje meg ezt a két lépést.

> [!IMPORTANT]
> Tekintse át [az Olvasási replika áttekintése című cikk megfontolandó szempontokat ismertető szakaszát.](concepts-read-replicas.md#considerations)
>
> Mielőtt egy elsődleges kiszolgáló beállítása új értékre frissül, frissítse a replikabeállítást egy egyenlő vagy nagyobb értékre. Ez a művelet segít a replikának lépést tartani a főkiszolgálón végrehajtott módosításokkal.

### <a name="list-replicas"></a>Replikalista
Az elsődleges kiszolgáló replikáinak listáját az [az postgres server replica list paranccsal tudja](/cli/azure/postgres/server/replica#az_postgres_server_replica_list) megtekinteni.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Replikáció leállítása replikakiszolgálóra
Az elsődleges kiszolgáló és az olvasási replika közötti replikációt [az az postgres server replica stop paranccsal állíthatja](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) le.

Miután leállítja az elsődleges kiszolgálóra és az olvasási replikára történő replikációt, az már nem vonható vissza. Az olvasási replika önálló kiszolgálóvá válik, amely olvasást és írást is támogat. Az önálló kiszolgálót nem lehet újra replikába tenni.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Elsődleges vagy replikakiszolgáló törlése
Elsődleges vagy replikakiszolgáló törléséhez használja [az az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete) parancsot.

Ha töröl egy elsődleges kiszolgálót, az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól az olvasást és az írást is támogatják.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Olvasási replikákat az [Azure](/rest/api/azure/)REST API.

### <a name="prepare-the-primary-server"></a>Az elsődleges kiszolgáló előkészítése

1. Ellenőrizze az elsődleges kiszolgáló `azure.replication_support` értékét. Az olvasási replikák csak AKKOR működnek, ha a REPLIKA.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Ha `azure.replication_support` a nem legalább REPLIKA, állítsa be.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [A módosítás alkalmazásához](/rest/api/postgresql/servers/restart) indítsa újra a kiszolgálót.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása
Olvasási replikát a create API használatával [hozhat létre:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, olvassa el a replikákkal kapcsolatos [fogalmakat.](concepts-read-replicas.md) 

Ha a paramétert nem a REPLICA (REPLIKA) beállításra általános célú vagy a Memóriaoptimalált elsődleges kiszolgálón, és újraindította a `azure.replication_support` kiszolgálót, hibaüzenetet kap.  A replika létrehozása előtt ismételje meg ezt a két lépést.

A replika a főkiszolgálóval azonos számítási és tárolási beállítások használatával jön létre. A replika létrehozása után számos beállítás módosítható az elsődleges kiszolgálótól függetlenül: számítási generáció, virtuális magok, tárterület és biztonsági másolatok megőrzési időtartama. A tarifacsomag egymástól függetlenül is módosítható, az Alapszintű csomag kivételével.


> [!IMPORTANT]
> Mielőtt egy elsődleges kiszolgáló beállítása új értékre frissül, frissítse a replikabeállítást egyenlő vagy nagyobb értékre. Ez a művelet segít a replikának lépést tartani a főkiszolgálón végrehajtott módosításokkal.

### <a name="list-replicas"></a>Replikalista
Az elsődleges kiszolgáló replikáinak listáját a replikalista API használatával [tudja megtekinteni:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replikáció leállítása replikakiszolgálóra
Az elsődleges kiszolgáló és az olvasási replika közötti replikációt a frissítési [API használatával állíthatja le.](/rest/api/postgresql/servers/update)

Miután leállítja az elsődleges kiszolgálóra és egy olvasási replikára történő replikációt, az már nem vonható vissza. Az olvasási replika önálló kiszolgálóvá válik, amely az olvasást és az írást is támogatja. Az önálló kiszolgálót nem lehet újra replikába tenni.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Elsődleges vagy replikakiszolgáló törlése
Elsődleges vagy replikakiszolgáló törléséhez használja a [delete API-t:](/rest/api/postgresql/servers/delete)

Amikor töröl egy elsődleges kiszolgálót, az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól az olvasást és az írást is támogatják.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Következő lépések
* További információ az [olvasási replikákról a Azure Database for PostgreSQL.](concepts-read-replicas.md)
* Ismerje meg, [hogyan hozhat létre és kezelhet](howto-read-replicas-portal.md)olvasási replikákat a Azure Portal.