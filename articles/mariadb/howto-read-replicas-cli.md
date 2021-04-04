---
title: Olvasási replikák kezelése – Azure CLI, REST API-Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthatja be és kezelheti az olvasási replikákat Azure Database for MariaDB az Azure CLI és a REST API használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 70da1e9c70bf80737065362c68781652dd9ab6e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664953"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB az Azure CLI és a REST API használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MariaDB szolgáltatásban az Azure CLI és a REST API használatával.

## <a name="azure-cli"></a>Azure CLI
Az olvasási replikákat az Azure CLI használatával hozhatja létre és kezelheti.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2,0 telepítése](/cli/azure/install-azure-cli)
- Egy [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely forráskiszolgálóként lesz felhasználva. 

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a forráskiszolgáló ezen díjszabási szintek egyikében található.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan forráshoz hoz létre replikát, amely nem tartalmaz meglévő replikákat, a forrás először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

A következő paranccsal hozhat létre olvasási replika-kiszolgálót:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica create` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, amelybe a replika-kiszolgáló létre lesz hozva.  |
| name | mydemoreplicaserver | A létrehozott új replika-kiszolgáló neve. |
| source-server | mydemoserver | Azon meglévő forráskiszolgáló neve vagy azonosítója, amelyről replikálni kell. |

Egy több régióból származó olvasási replika létrehozásához használja a `--location` paramétert. 

Az alábbi CLI-példa létrehozza a replikát az USA nyugati régiójában.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a főkiszolgáló. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a forrásnál egyenlő vagy annál nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

### <a name="list-replicas-for-a-source-server"></a>Forráskiszolgáló replikáinak listázása

Egy adott forráskiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

A `az mariadb server replica list` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, amelybe a replika-kiszolgáló létre lesz hozva.  |
| server-name | mydemoserver | A forráskiszolgáló neve vagy azonosítója. |

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón

> [!IMPORTANT]
> A kiszolgálók replikálásának leállítása visszafordíthatatlan. Miután leállította a replikálást a forrás és a replika között, nem vonható vissza. A replika-kiszolgáló ezután önálló kiszolgáló lesz, és már támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható létre újra replikába.

Az olvasási replika kiszolgálóra való replikálás a következő parancs használatával állítható le:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

A `az mariadb server replica stop` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a replika-kiszolgáló létezik.  |
| name | mydemoreplicaserver | Annak a replika-kiszolgálónak a neve, amelyen a replikálást le kell állítani. |

### <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Az olvasási replika kiszolgálójának törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Forráskiszolgáló törlése

> [!IMPORTANT]
> A forráskiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a forráskiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A forráskiszolgáló törléséhez futtassa az az **[MariaDB Server delete](/cli/azure/mariadb/server)** parancsot.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Az olvasási replikákat az [Azure REST API](/rest/api/azure/)használatával hozhatja létre és kezelheti.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása
Olvasási replikát a [create API](/rest/api/mariadb/servers/create)használatával hozhat létre:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

Ha nem állította be a `azure.replication_support` paramétert egy általános célú vagy memóriára optimalizált forráskiszolgáló **replikáján** , és újraindította a kiszolgálót, hibaüzenetet kap. A replika létrehozása előtt végezze el a két lépést.

A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható a forráskiszolgálóról függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentési megőrzési időszak. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.


> [!IMPORTANT]
> Mielőtt egy forráskiszolgáló-beállítást új értékre frissít, frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

### <a name="list-replicas"></a>Replikák listázása
A forráskiszolgáló replikáinak listáját a [replika lista API](/rest/api/mariadb/replicas/listbyserver)használatával tekintheti meg:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón
A [frissítési API](/rest/api/mariadb/servers/update)használatával leállíthatja a replikációt egy forráskiszolgáló és egy olvasási replika között.

Miután leállította a replikálást egy forráskiszolgálóról és egy olvasási replikán, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Forrás-vagy replika-kiszolgáló törlése
Forrás-vagy replika-kiszolgáló törléséhez használja a [delete API](/rest/api/mariadb/servers/delete)-t:

Ha töröl egy forráskiszolgáló-kiszolgálót, az összes olvasási replikára történő replikáció leállt. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Következő lépések

- További információ az [olvasási replikáról](concepts-read-replicas.md)