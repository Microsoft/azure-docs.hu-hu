---
title: 'Azure CLI: felügyelt példány hozzáadása a feladatátvételi csoporthoz'
description: Ismerje meg, hogyan hozhat létre két felügyelt példányt, hogyan veheti fel őket egy feladatátvételi csoportba, majd tesztelheti a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: afefc556bcac096d67051f3014c31e449dbbca32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323566"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>Azure SQL felügyelt példány létrehozása a parancssori felület használatával feladatátvételi csoportba

Ez az Azure CLI-példa két felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd teszteli a feladatátvételt az elsődleges felügyelt példányról a másodlagos felügyelt példányra.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-scripts"></a>Mintaparancsfájlok

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást. Az erőforráscsoportot kétszer kell eltávolítania. Az erőforráscsoport eltávolításakor a rendszer először eltávolítja a felügyelt példányt és a virtuális fürtöket, de a hibaüzenettel meghiúsul `az group delete : Long running operation failed with status 'Conflict'.` . Futtassa az az Group delete parancsot a második alkalommal, hogy eltávolítsa a fennmaradó erőforrásokat és az erőforráscsoportot.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az Network vnet](/cli/azure/network/vnet) | Virtuális hálózati parancsok.  |
| [az Network vnet subnet](/cli/azure/network/vnet/subnet) | Virtuális hálózati alhálózati parancsok. |
| [az Network NSG](/cli/azure/network/nsg) | Hálózati biztonsági csoport parancsai. |
| [az Network NSG Rule](/cli/azure/network/nsg/rule)| Hálózati biztonsági szabály parancsai. |
| [az Network Route-Table](/cli/azure/network/route-table) | Útválasztási táblázat parancsai |
| [az SQL mi](/cli/azure/sql/mi) | SQL felügyelt példányok parancsai. |
| [az Network Public-IP](/cli/azure/network/public-ip) | Hálózati nyilvános IP-címek parancsai. |
| [az Network vnet-Gateway](/cli/azure/network/vnet-gateway) | Virtual Network átjáró parancsai |
| [az SQL instance-feladatátvétel-Group](/cli/azure/sql/instance-failover-group) | SQL felügyelt példány feladatátvételi csoportjának parancsai. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../../azure-sql/database/az-cli-script-samples-content-guide.md) találhat.
