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
ms.openlocfilehash: 60c5ddcc67db6e4a0649458cfbd5c2949aa9a32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202042"
---
# <a name="delete-resources-from-azure"></a>Erőforrások törlése az Azure-ból

> [!NOTE]
>  A cikkben szereplő erőforrások törlésének lehetőségei visszavonhatatlanok!

> [!NOTE]
>  Mivel az Azure arc-kompatibilis adatszolgáltatások számára elérhető egyetlen kapcsolati mód jelenleg a közvetett kapcsolódási mód, a Kubernetes-példány törlése nem távolítja el az Azure-ból, és nem törli az Azure-példányokat az Azure-ból, és nem távolítja el a Kubernetes.  Most, hogy az erőforrás törlése két lépésből áll, és ez a jövőben is javulni fog.  A Kubernetes az igazság forrása lesz, és az Azure frissülni fog, hogy tükrözze.

Bizonyos esetekben előfordulhat, hogy manuálisan kell törölnie az Azure arc-kompatibilis adatszolgáltatások erőforrásait Azure Resource Manager (ARM)-ben.  Ezeket az erőforrásokat a következő lehetőségek bármelyikével törölheti.

- [Erőforrások törlése az Azure-ból](#delete-resources-from-azure)
  - [Teljes erőforráscsoport törlése](#delete-an-entire-resource-group)
  - [Az erőforráscsoport adott erőforrásainak törlése](#delete-specific-resources-in-the-resource-group)
  - [Erőforrások törlése az Azure CLI használatával](#delete-resources-using-the-azure-cli)
    - [SQL felügyelt példány erőforrásainak törlése az Azure CLI használatával](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [A PostgreSQL nagy kapacitású-kiszolgáló csoport erőforrásainak törlése az Azure CLI használatával](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Azure arc-adatvezérlő erőforrásainak törlése az Azure CLI használatával](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Erőforráscsoport törlése az Azure CLI használatával](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Teljes erőforráscsoport törlése
Ha egy adott és dedikált erőforráscsoportot használ az Azure arc-kompatibilis adatszolgáltatásokhoz, és az erőforráscsoport *összes* adatát törölni szeretné, törölheti az erőforráscsoportot, amely a benne lévő összes adat törlését is lehetővé teszi.  

A Azure Portal az alábbi módon törölheti az erőforráscsoportot:

- Keresse meg az erőforráscsoportot azon a Azure Portalon, ahol az Azure arc-kompatibilis adatszolgáltatások erőforrásai létrejöttek.
- Kattintson az **erőforráscsoport törlése** gombra.
- Erősítse meg a törlést az erőforráscsoport nevének megadásával, majd kattintson a **Törlés** gombra.

## <a name="delete-specific-resources-in-the-resource-group"></a>Az erőforráscsoport adott erőforrásainak törlése

Az Azure arc-kompatibilis adatszolgáltatás-erőforrásait a Azure Portal egy erőforráscsoporthoz is törölheti a következő módon:

- Keresse meg az erőforráscsoportot azon a Azure Portalon, ahol az Azure arc-kompatibilis adatszolgáltatások erőforrásai létrejöttek.
- Válassza ki az összes törölni kívánt erőforrást.
- Kattintson a Törlés gombra.
- Erősítse meg a törlést az "igen" beírásával, és kattintson a **Törlés** gombra.

## <a name="delete-resources-using-the-azure-cli"></a>Erőforrások törlése az Azure CLI használatával

Az Azure parancssori felületének használatával törölheti az Azure arc-kompatibilis adatszolgáltatások erőforrásait.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>SQL felügyelt példány erőforrásainak törlése az Azure CLI használatával

Ha az Azure CLI használatával szeretné törölni az SQL felügyelt példányának erőforrásait az Azure-ból, cserélje le az alábbi parancs helyőrző értékeit, és futtassa azt.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>A PostgreSQL nagy kapacitású-kiszolgáló csoport erőforrásainak törlése az Azure CLI használatával

Ha törölni szeretne egy PostgreSQL nagy kapacitású Server Group-erőforrást az Azure-ból az Azure CLI használatával, cserélje le az alábbi parancs helyőrző értékeit, és futtassa azt.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Azure arc-adatvezérlő erőforrásainak törlése az Azure CLI használatával

> [!NOTE]
> Egy Azure-beli ív-adatkezelő törlése előtt törölje az összes kezelt adatbázis-példány erőforrást.

Ha törölni szeretne egy Azure arc-adatkezelőt az Azure-ból az Azure CLI használatával, cserélje le az alábbi parancs helyőrző értékeit, és futtassa azt.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Erőforráscsoport törlése az Azure CLI használatával

Az Azure CLI-t is használhatja az [erőforráscsoport törléséhez](../../azure-resource-manager/management/delete-resource-group.md).