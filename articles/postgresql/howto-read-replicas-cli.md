---
title: Olvasási replikák kezelése – Azure CLI, REST API-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL egyetlen kiszolgálón az Azure CLI-ből és REST API
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e74a58a14bdcc2a6fe1e9f86305aae415c6abf7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674514"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Olvasási replikák létrehozása és kezelése az Azure CLI-ből, REST API

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat Azure Database for PostgreSQL az Azure CLI és a REST API használatával. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Azure-beli replikálás támogatása
Az [olvasási replikák](concepts-read-replicas.md) és a [logikai dekódolás](concepts-logical.md) a postgres írási előre megadott naplójától (Wal) függ. Ehhez a két szolgáltatáshoz különböző naplózási szintek szükségesek a postgres. A logikai dekódolás magasabb szintű naplózást igényel, mint az olvasási replikák.

A megfelelő naplózási szint konfigurálásához használja az Azure-replikáció támogatási paraméterét. Az Azure-beli replikáció támogatásának három beállítási lehetősége van:

* **Off** – a legkevesebb információt helyezi el a Wal-ben. Ez a beállítás nem érhető el a legtöbb Azure Database for PostgreSQL kiszolgálón.  
* **Replika** – részletesebb, mint a **kikapcsolás**. Az [olvasási replikák](concepts-read-replicas.md) működéséhez szükséges naplózás minimális szintje. Ez a beállítás az alapértelmezett a legtöbb kiszolgálón.
* **Logikai** – részletesebb, mint a **replika**. Ez a minimális szintű naplózás a logikai dekódolás működéséhez. Ebben a beállításban az olvasási replikák is működnek.


> [!NOTE]
> Ha olvasási replikákat helyez üzembe az állandó, nagy írási igényű elsődleges számítási feladatokhoz, a replikációs késés továbbra is növekedni fog, és előfordulhat, hogy soha nem fogja tudni felvenni az elsődlegest. Ez növelheti az elsődleges tárterület-használatot is, mivel a rendszer nem törli a WAL-fájlokat, amíg azok a replikán nem érkeznek.

## <a name="azure-cli"></a>Azure CLI
Az olvasási replikákat az Azure CLI használatával hozhatja létre és kezelheti.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2,0 telepítése](/cli/azure/install-azure-cli)
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-up-azure-cli.md) elsődleges kiszolgálóként.


### <a name="prepare-the-primary-server"></a>Az elsődleges kiszolgáló előkészítése

1. Keresse meg az elsődleges kiszolgáló `azure.replication_support` értékét. Az olvasási replikák működéséhez legalább REPLIKÁnak kell lennie.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Ha `azure.replication_support` a nem legalább replika, állítsa be. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. A módosítás alkalmazásához indítsa újra a kiszolgálót.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

Az az [postgres Server replika Create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) paranccsal a következő paramétereket kell megadni:

| Beállítás | Példaérték | Description  |
| --- | --- | --- |
| resource-group | myResourceGroup |  Az az erőforráscsoport, amelyben a replika-kiszolgáló létre lesz hozva.  |
| name | mydemoserver – replika | A létrehozott új replika-kiszolgáló neve. |
| source-server | mydemoserver | A replikálni kívánt meglévő elsődleges kiszolgáló neve vagy erőforrás-azonosítója. Ha azt szeretné, hogy a replika és a főkiszolgáló erőforráscsoportok eltérőek legyenek, használja az erőforrás-azonosítót. |

Az alábbi CLI-példában a replika a főkiszolgálóval megegyező régióban jön létre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Egy több régióból származó olvasási replika létrehozásához használja a `--location` paramétert. Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

Ha nem állította be a `azure.replication_support` paramétert  egy általános célú vagy a memóriára optimalizált elsődleges kiszolgálón, és újraindította a kiszolgálót, hibaüzenetet kap. A replika létrehozása előtt végezze el a két lépést.

> [!IMPORTANT]
> Tekintse át az [olvasási replika áttekintése című témakör szempontjait](concepts-read-replicas.md#considerations).
>
> Ahhoz, hogy egy elsődleges kiszolgáló beállítása új értékre frissüljön, frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

### <a name="list-replicas"></a>Replikák listázása
Az elsődleges kiszolgáló replikáinak listáját az [az postgres Server replika List](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) parancs használatával tekintheti meg.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón
Az [az postgres Server replika leállítása](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) paranccsal leállíthatja az elsődleges kiszolgáló és az olvasási replika közötti replikációt.

Miután leállította a replikálást egy elsődleges kiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Elsődleges vagy replika kiszolgáló törlése
Az elsődleges vagy a replika kiszolgáló törléséhez használja az az [postgres Server delete](/cli/azure/postgres/server#az-postgres-server-delete) parancsot.

Elsődleges kiszolgáló törlésekor a rendszer leállítja az összes olvasási replikára való replikálást. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Az olvasási replikákat az [Azure REST API](/rest/api/azure/)használatával hozhatja létre és kezelheti.

### <a name="prepare-the-primary-server"></a>Az elsődleges kiszolgáló előkészítése

1. Keresse meg az elsődleges kiszolgáló `azure.replication_support` értékét. Az olvasási replikák működéséhez legalább REPLIKÁnak kell lennie.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Ha `azure.replication_support` a nem legalább replika, állítsa be.

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

2. A módosítás alkalmazásához [indítsa újra a kiszolgálót](/rest/api/postgresql/servers/restart) .

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása
Olvasási replikát a [create API](/rest/api/postgresql/servers/create)használatával hozhat létre:

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
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

Ha nem állította be a `azure.replication_support` paramétert  egy általános célú vagy a memóriára optimalizált elsődleges kiszolgálón, és újraindította a kiszolgálót, hibaüzenetet kap. A replika létrehozása előtt végezze el a két lépést.

A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható az elsődleges kiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentési megőrzési időszak. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.


> [!IMPORTANT]
> Ahhoz, hogy egy elsődleges kiszolgáló beállítása új értékre frissüljön, frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

### <a name="list-replicas"></a>Replikák listázása
Az elsődleges kiszolgálók replikáinak listáját a [replika lista API](/rest/api/postgresql/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón
Az [Update API](/rest/api/postgresql/servers/update)használatával leállíthatja az elsődleges kiszolgáló és az olvasási replika közötti replikációt.

Miután leállította a replikálást egy elsődleges kiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

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

### <a name="delete-a-primary-or-replica-server"></a>Elsődleges vagy replika kiszolgáló törlése
Az elsődleges vagy a replika kiszolgáló törléséhez használja a [delete API](/rest/api/postgresql/servers/delete)-t:

Elsődleges kiszolgáló törlésekor a rendszer leállítja az összes olvasási replikára való replikálást. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Következő lépések
* További információ [az olvasási replikákkal kapcsolatban Azure Database for PostgreSQL](concepts-read-replicas.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portalban](howto-read-replicas-portal.md).