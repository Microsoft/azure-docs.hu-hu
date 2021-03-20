---
title: Azure PowerShell-példaszkript – APIM-szolgáltatás létrehozása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre API Management (APIM) szolgáltatást. Tekintse meg a minta parancsfájlt, és tekintse meg a további rendelkezésre álló erőforrásokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 6cfa643d8949f80e8dcf99c2c8e64cf20588b928
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87851001"
---
# <a name="create-an-api-management-service"></a>API Management szolgáltatás létrehozása

Ez a példaszkript létrehozza egy API Management-szolgáltatás fejlesztői termékváltozatát.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell modul 1,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/api-management/create-apim-service/create_apim_service.ps1 "Create a service")]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

Az Azure API Managementhez a [PowerShell-minták](../powershell-samples.md) között találhat további Azure PowerShell-mintákat.
