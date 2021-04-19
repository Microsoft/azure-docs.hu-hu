---
title: Erőforrások törlése az Azure-ból
description: Erőforrások törlése az Azure-ból
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716078"
---
# <a name="delete-resources-from-azure"></a>Erőforrások törlése az Azure-ból

Ez a cikk azt ismerteti, hogyan törölhet erőforrásokat az Azure-ból.

> [!WARNING]
> Ha törli az erőforrásokat a cikkben leírtak szerint, ezek a műveletek nem visszafordíthatók.

Közvetett kapcsolódási módban a példány Kubernetesből való törlése nem távolítja el azt az Azure-ból, az Azure-ból való törlés pedig nem távolítja el azt a Kubernetesből. Közvetett kapcsolódási mód esetén az erőforrások törlése két lépésből áll, és ez a jövőben tovább fog fejlődni. A Kubernetes lesz az igazság forrása, és a portál frissítve lesz, hogy tükrözze azt.

Bizonyos esetekben előfordulhat, hogy manuálisan kell törölnie Azure Arc-kompatibilis adatszolgáltatások azure-beli erőforrásokat.  Ezeket az erőforrásokat az alábbi lehetőségek bármelyikével törölheti.

- [Erőforrások törlése az Azure-ból](#delete-resources-from-azure)
  - [Teljes erőforráscsoport törlése](#delete-an-entire-resource-group)
  - [Adott erőforrások törlése az erőforráscsoportban](#delete-specific-resources-in-the-resource-group)
  - [Erőforrások törlése az Azure CLI használatával](#delete-resources-using-the-azure-cli)
    - [Felügyelt SQL-példány erőforrásainak törlése az Azure CLI használatával](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [A magas skálázású PostgreSQL-kiszolgálócsoport erőforrásainak törlése az Azure CLI használatával](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Az Azure Arc-erőforrások törlése az Azure CLI használatával](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Erőforráscsoport törlése az Azure CLI használatával](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Teljes erőforráscsoport törlése

Ha egy adott és dedikált erőforráscsoportot használt az Azure Arc-kompatibilis adatszolgáltatások-hoz, és mindent törölni szeretne az erőforráscsoporton belül, törölheti az erőforráscsoportot, amely a benne lévő összes adatokat törli.   

Az erőforráscsoportot a következő Azure Portal törölheti:

- Keresse meg az erőforráscsoportot abban a Azure Portal, ahol Azure Arc-kompatibilis adatszolgáltatások erőforrás létrejött.
- Kattintson az **Erőforráscsoport törlése gombra.**
- Erősítse meg a törlést az erőforráscsoport nevének megadásával, majd kattintson a **Törlés gombra.**

## <a name="delete-specific-resources-in-the-resource-group"></a>Adott erőforrások törlése az erőforráscsoportban

Az erőforráscsoportban Azure Arc-kompatibilis adatszolgáltatások erőforráscsoportban a következő Azure Portal törölheti:

- Keresse meg azt az erőforráscsoportot a Azure Portal, ahol a Azure Arc-kompatibilis adatszolgáltatások erőforrások létrejöttek.
- Válassza ki az összes törölni kívánt erőforrást.
- Kattintson a Törlés gombra.
- Erősítse meg a törlést az "igen" beírásával, majd kattintson a **Törlés gombra.**

## <a name="delete-resources-using-the-azure-cli"></a>Erőforrások törlése az Azure CLI használatával

Az Azure CLI használatával Azure Arc-kompatibilis adatszolgáltatások erőforrásokat törölhet.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Felügyelt SQL-példány erőforrásainak törlése az Azure CLI használatával

Ha törölni szeretné a felügyelt SQL-példány erőforrásait az Azure CLI használatával, cserélje le a helyőrző értékeket az alábbi parancsban, és futtassa azt.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>A magas skálázású PostgreSQL-kiszolgálócsoport erőforrásainak törlése az Azure CLI használatával

Ha törölni szeretne egy magas skálázású PostgreSQL-kiszolgálócsoport-erőforrást az Azure CLI használatával, cserélje le a helyőrző értékeket az alábbi parancsban, és futtassa azt.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Az Azure Arc-erőforrások törlése az Azure CLI használatával

> [!NOTE]
> Mielőtt töröl egy Azure Arc adatkezelőből, törölnie kell az összes adatbázispéldány-erőforrást, amit az adatkezelőnek kell törölnie.

Ha törölni szeretne egy Azure Arc azure-beli adatkezelőt az Azure CLI használatával, cserélje le az alábbi parancsban található helyőrző értékeket, és futtassa azt.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Erőforráscsoport törlése az Azure CLI használatával

Az Azure CLI-t erőforráscsoport törlésére [is használhatja.](../../azure-resource-manager/management/delete-resource-group.md)