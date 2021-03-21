---
title: Azure Firewall üzembe helyezése több nyilvános IP-címmel a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy Azure Firewall több nyilvános IP-címmel a Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023669"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure Firewall üzembe helyezése több nyilvános IP-címmel az Azure PowerShell használatával

Ez a szolgáltatás a következő forgatókönyveket teszi lehetővé:

- **DNAT** – a háttér-kiszolgálókra több szabványos port-példányt is lefordíthat. Ha például két nyilvános IP-címmel rendelkezik, akkor mindkét IP-címhez lefordíthatja a 3389-es (RDP) TCP-portot.
- **SNAT** – további portok érhetők el a kimenő SNAT-kapcsolatokhoz, ami csökkenti a SNAT-portok kimerülésének lehetséges lehetőségét. Ekkor Azure Firewall véletlenszerűen kiválasztja a forrás nyilvános IP-címét, amelyet a rendszer a kapcsolódáshoz használ. Ha a hálózaton bármilyen lefelé irányuló szűrés van érvényben, engedélyeznie kell a tűzfalhoz társított összes nyilvános IP-címet. Érdemes lehet egy [nyilvános IP-cím előtagot](../virtual-network/public-ip-address-prefix.md) használni a konfiguráció egyszerűsítéséhez.
 
A több nyilvános IP-címmel rendelkező Azure Firewall a Azure Portal, a Azure PowerShell, az Azure CLI, a REST és a sablonok használatával érhető el. Akár 250 nyilvános IP-címmel rendelkező Azure Firewall is üzembe helyezhet.

Az alábbi Azure PowerShell példák bemutatják, hogyan konfigurálhatja, adhatja hozzá és távolíthatja el a Azure Firewall nyilvános IP-címeit.

> [!NOTE]
> Az első ipConfiguration nem távolítható el a Azure Firewall nyilvános IP-cím konfigurációjának oldaláról. Ha módosítani kívánja az IP-címet, használhatja a Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Hozzon létre két vagy több nyilvános IP-címmel rendelkező tűzfalat

Ez a példa két nyilvános IP-címmel rendelkező virtuális hálózati *vnet* csatolt tűzfalat hoz létre.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Nyilvános IP-cím hozzáadása meglévő tűzfalhoz

Ebben a példában a nyilvános IP- *azFwPublicIp1* a tűzfalhoz van csatlakoztatva.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Nyilvános IP-cím eltávolítása meglévő tűzfallal

Ebben a példában a nyilvános IP-cím *azFwPublicIp1* le van választva a tűzfalból.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: Azure Firewall létrehozása több nyilvános IP-címmel – Resource Manager-sablon](quick-create-multiple-ip-template.md)
