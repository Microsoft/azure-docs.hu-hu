---
title: Az olvasási replikák kezelése Azure Database for MySQL rugalmas kiszolgálón az Azure CLI használatával.
description: Ismerje meg, hogyan állíthat be és kezelhet olvasási replikákat Azure Database for MySQL rugalmas kiszolgálón az Azure CLI használatával.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a7d63fd76a88430495c9f55200308f63b11c89d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96494307"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Olvasási replikák létrehozása és kezelése Azure Database for MySQL rugalmas kiszolgálón az Azure CLI használatával

> [!IMPORTANT]
> Az Azure Database for MySQL-rugalmas kiszolgálóban található replikák előzetes verzióban érhetők el.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MySQL rugalmas kiszolgálón az Azure CLI használatával. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).

> [!Note]
> A replika nem támogatott magas rendelkezésre állású kiszolgáló esetén. 

## <a name="azure-cli"></a>Azure CLI
Az olvasási replikákat az Azure CLI használatával hozhatja létre és kezelheti.

### <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI 2,0 telepítése](/cli/azure/install-azure-cli)
- Egy [Azure Database for MySQL rugalmas kiszolgáló](quickstart-create-server-cli.md) , amelyet forráskiszolgálóként fog használni.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan forráshoz hoz létre replikát, amely nem tartalmaz meglévő replikákat, a forrás először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

A következő paranccsal hozhat létre olvasási replika-kiszolgálót:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a forrás. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. A replika-kiszolgáló mindig ugyanabban az erőforráscsoporthoz, ugyanazon a helyen és előfizetésben jön létre, mint a forráskiszolgálón. Ha egy másik erőforráscsoporthoz vagy egy másik előfizetéshez szeretne replikát létrehozni, akkor [a replika-kiszolgálót a létrehozás után helyezheti át](../../azure-resource-manager/management/move-resource-group-and-subscription.md) . Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a forrásnál egyenlő vagy nagyobb értékkel kell megőrizni, hogy a replika képes legyen lépést tartani a forrással.


### <a name="list-replicas-for-a-source-server"></a>Forráskiszolgáló replikáinak listázása

Egy adott forráskiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón

> [!IMPORTANT]
> A kiszolgálók replikálásának leállítása visszafordíthatatlan. Miután leállította a replikálást a forrás és a replika között, nem vonható vissza. A replika-kiszolgáló ezután önálló kiszolgáló lesz, és már támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható létre újra replikába.

Az olvasási replika kiszolgálóra való replikálás a következő parancs használatával állítható le:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Az olvasási replika kiszolgálójának törléséhez futtassa az az **[MySQL Server delete](/cli/azure/mysql/server)** parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Forráskiszolgáló törlése

> [!IMPORTANT]
> A forráskiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a forráskiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A forráskiszolgáló törléséhez futtassa az az **[MySQL flexibilis-Server delete](/cli/azure/mysql/flexible-server)** parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

- További információ az [olvasási replikáról](concepts-read-replicas.md)