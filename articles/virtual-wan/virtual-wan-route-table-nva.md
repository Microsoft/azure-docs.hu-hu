---
title: 'Virtuális WAN: virtuális központ útválasztási táblázatának létrehozása a NVA: Azure PowerShell'
description: Virtuális WAN virtuális hub útválasztási táblázat a forgalom irányításához egy hálózati virtuális berendezésre.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 40154a9c3cefed69bd3f1639153099b944da79b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267125"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Virtuális központ útválasztási táblázatának létrehozása a forgalom irányításához egy hálózati virtuális berendezésre

Ez a cikk bemutatja, hogyan irányíthatja át a forgalmat egy virtuális hubhoz egy hálózati virtuális berendezésre. 

![Virtuális WAN ábrája](./media/virtual-wan-route-table-nva/vwanroute.png)

Ebben a cikkben az alábbiakkal fog megismerkedni:

* WAN létrehozása
* Elosztó létrehozása
* Hub virtuális hálózati kapcsolatok létrehozása
* Hub-útvonal létrehozása
* Útválasztási táblázat létrehozása
* Az útválasztási táblázat alkalmazása

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ellenőrizze, hogy teljesültek-e az alábbi feltételek:

* Rendelkezik egy hálózati virtuális berendezéssel (NVA). Ez egy külső gyártótól származó szoftver, amelyet általában az Azure Marketplace-en kell kiépíteni egy virtuális hálózaton.
* Rendelkezik egy magánhálózati IP-címmel, amely a NVA hálózati adapterhez van rendelve. 
* A NVA nem helyezhető üzembe a virtuális központban. Ezt külön VNet kell telepíteni. Ebben a cikkben a NVA VNet a "DMZ VNet" néven emlegetik.
* A "DMZ VNet" lehet, hogy egy vagy több virtuális hálózat van csatlakoztatva. Ebben a cikkben ezt a VNet "közvetett küllős VNet"-ként nevezzük. Ezek a virtuális hálózatok a DMZ-VNet a VNet-társítás használatával csatlakoztathatók.
* Ellenőrizze, hogy már létrehozott-e 2 virtuális hálózatok. Ezeket küllős virtuális hálózatok fogjuk használni. Ebben a cikkben a VNet küllős címtartomány a 10.0.2.0/24 és a 10.0.3.0/24. Ha a VNet létrehozásával kapcsolatos információkra van szüksége, tekintse meg [a virtuális hálózat létrehozása a PowerShell használatával](../virtual-network/quick-create-powershell.md)című témakört.
* Győződjön meg arról, hogy nincsenek virtuális hálózati átjárók egyetlen virtuális hálózatok sem.

## <a name="1-sign-in"></a><a name="signin"></a>1. bejelentkezés

Győződjön meg arról, hogy a Resource Manager PowerShell-parancsmagok legújabb verzióját telepíti. A PowerShell-parancsmagok telepítéséről további információt a [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps) (Az Azure PowerShell telepítése és konfigurálása) című témakörben talál. Ez azért fontos, mert a parancsmagok korábbi verziói nem tartalmazzák a feladatok elvégzéséhez szükséges aktuális értékeket.

1. Nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és jelentkezzen be az Azure-fiókjába. Ez a parancsmag kéri a bejelentkezési hitelesítő adatok megadását. A bejelentkezést követően letölti a fiók beállításait, hogy elérhetők legyenek Azure PowerShell számára.

   ```powershell
   Connect-AzAccount
   ```
2. Olvassa be az Azure-előfizetések listáját.

   ```powershell
   Get-AzSubscription
   ```
3. Válassza ki a használni kívánt előfizetést.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. erőforrások létrehozása

1. Hozzon létre egy erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Hozzon létre egy virtuális WAN-t.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Hozzon létre egy virtuális hubot.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. kapcsolatok létrehozása

Hozzon létre hub virtuális hálózati kapcsolatokat a közvetett küllős VNet és a DMZ-VNet a virtuális hubhoz.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. virtuális központ útvonalának létrehozása

Ebben a cikkben a közvetlen küllős VNet 10.0.2.0/24 és 10.0.3.0/24, a DMZ NVA hálózati adapter magánhálózati IP-címe pedig 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. virtuális központ útválasztási táblázatának létrehozása

Hozzon létre egy virtuális hub útválasztási táblázatot, majd alkalmazza a létrehozott útvonalat.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. véglegesítse a módosításokat

Véglegesítse a módosításokat a virtuális központban.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
