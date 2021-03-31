---
title: Azure PowerShell-példaszkript – Biztonságos háttérrendszer | Microsoft Docs
description: Megtudhatja, hogyan használhat Azure PowerShell parancsfájlt a háttérrendszer kölcsönös Tanúsítványos hitelesítéssel történő biztonságossá tételéhez.
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
ms.openlocfilehash: cb6b1cfcb6a8f482bdcbc9d6befc99197f024f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87905635"
---
# <a name="secure-back-end"></a>Biztonságos háttérrendszer

Ez a példaszkript kölcsönös tanúsítványhitelesítéssel védi a háttérrendszert.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell modul 1,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/api-management/secure-backend-with-mutual-certificate-authentication/secure_backend_with_mutual_certificate_authentication.ps1 "Secures backend")]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

Az Azure API Managementhez a [PowerShell-minták](../powershell-samples.md) között találhat további Azure PowerShell-mintákat.
