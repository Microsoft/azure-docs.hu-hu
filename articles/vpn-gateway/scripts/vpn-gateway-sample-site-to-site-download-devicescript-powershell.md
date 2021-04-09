---
title: Azure PowerShell-példaszkript – Eszközkonfigurációs sablon letöltése | Microsoft Docs
description: Megtudhatja, hogyan használható egy PowerShell-szkript a VPN-eszköz konfigurációs sablonjának a kapcsolathoz való letöltéséhez.
services: vpn-gateway
documentationcenter: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 01/09/2020
ms.author: yushwang
ms.openlocfilehash: 283ddb12e497c242f1843840fe1f1ff208712626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88031710"
---
# <a name="download-vpn-device-template-using-powershell"></a>VPN-eszközsablon letöltése a PowerShell használatával

Ez a szkript letölti a VPN-eszközsablont egy kapcsolathoz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport törléséhez. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/az.network/Get-azVirtualNetworkGatewaySupportedVpnDevice) | Felsorolja az összes elérhető VPN-eszközmodellt és verziót. |
| [Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/az.network/Get-azVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | Letölti a kapcsolat konfigurációs sablonját. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).
