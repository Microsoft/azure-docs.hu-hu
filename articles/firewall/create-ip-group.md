---
title: IP-csoportok létrehozása a Azure Firewallban
description: Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394526"
---
# <a name="create-ip-groups"></a>IP-csoportok létrehozása

Az IP-csoportok lehetővé teszik Azure Firewall szabályok IP-címeinek csoportosítását és kezelését. Rendelkezhetnek egyetlen IP-címmel, több IP-címmel vagy egy vagy több IP-címtartományok.

## <a name="create-an-ip-group---azure-portal"></a>IP-csoport létrehozása – Azure Portal

IP-csoport létrehozása a Azure Portal használatával:

1. A Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
1. A keresőmezőbe írja be az **IP-csoportok** kifejezést, majd válassza az **IP-csoportok** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza ki előfizetését.
1. Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat.
1. Adja meg az IP-csoport egyedi nevét, majd válasszon ki egy régiót.
1. Válassza a **Tovább: IP-címek** lehetőséget.
1. Írjon be egy IP-címet, több IP-címet vagy IP-címtartományt.

   Az IP-címeket kétféleképpen adhatja meg:
   - Manuálisan is megadhatja őket
   - Importálhatja őket egy fájlból.

   Fájlból való importáláshoz válassza az **Importálás fájlból** lehetőséget. Húzza a fájlt a mezőbe, vagy válassza a **Fájlok tallózása** lehetőséget. Ha szükséges, megtekintheti és szerkesztheti a feltöltött IP-címeit.

   Ha IP-címet ad meg, a portál ellenőrzi, hogy az átfedések, ismétlődések és formázási problémák ellenőrzéséhez szükségesek-e.

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-an-ip-group---azure-powershell"></a>IP-csoport létrehozása – Azure PowerShell

Ez a példa egy IP-csoportot hoz létre egy cím előtaggal és egy IP-címmel Azure PowerShell használatával:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>IP-csoport létrehozása – Azure CLI

Ez a példa egy IP-csoportot hoz létre egy cím előtaggal és egy IP-címmel az Azure CLI használatával:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Következő lépések

- [További információ az IP-csoportokról](ip-groups.md)
