---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7dd255e9767309c7b273dccfa0ee5675eed18568
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380488"
---
További címelőtagok felvétele:

1. Állítsa be a LocalNetworkGateway változóját.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. Módosítsa az előtagokat.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

Címelőtagok eltávolítása:

  Hagyja ki azokat címelőtagokat, amelyekre már nincs szüksége. Ebben a példában már nincs szükségünk a 10.101.2.0/24 előtagra (az előző példából), ezért a helyi hálózati átjárót úgy frissítjük, hogy az előtagot kizárja.

1. Állítsa be a LocalNetworkGateway változóját.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
1. Állítsa be az átjárót a frissített előtagokkal.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
