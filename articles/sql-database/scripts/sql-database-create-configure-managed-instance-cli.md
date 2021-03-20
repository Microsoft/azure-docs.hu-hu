---
title: 'Azure CLI: felügyelt példány létrehozása'
description: Azure CLI-példa parancsfájl egy felügyelt példány létrehozásához az Azure SQL felügyelt példányában
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497273"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Azure SQL felügyelt példány létrehozása a CLI használatával

Ez az Azure CLI-parancsfájl példa egy Azure SQL felügyelt példányt hoz létre egy dedikált alhálózaton egy új virtuális hálózaton belül. Emellett egy útválasztási táblázatot és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A szkript sikeres futtatása után a felügyelt példány a virtuális hálózatról vagy egy helyszíni környezetből is elérhető. Lásd: [az Azure virtuális gép konfigurálása egy felügyelt Azure SQL-példányhoz való kapcsolódáshoz].. /.. /Azure-SQL/Managed-instance/Connect-VM-instance-configure.MD) és [egy pont – hely kapcsolat konfigurálása egy Azure SQL felügyelt példányhoz a helyszínen](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](../../azure-sql/managed-instance/resource-limits.md#supported-regions) és [támogatott előfizetési típusok](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az Network vnet](/cli/azure/network/vnet) | Virtuális hálózati parancsok. |
| [az Network vnet subnet](/cli/azure/network/vnet/subnet) | Virtuális hálózati alhálózati parancsok. |
| [az Network Route-Table](/cli/azure/network/route-table) | Hálózati útválasztási táblázat parancsai. |
| [az SQL mi](/cli/azure/sql/mi) | SQL felügyelt példányok parancsai. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../../azure-sql/database/az-cli-script-samples-content-guide.md) találhat.
