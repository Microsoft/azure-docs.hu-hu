---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bd096e5a562bd9117ee895534c087d6fde48cb7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217939"
---
A "Get-AzureVNetConnection" parancsmag használatával ellenőrizheti, hogy a sikeres-e a kapcsolatok.

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. A virtuális hálózat nevének idézőjelben kell lennie, ha szóközt tartalmaz.

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG TestVNet1"
   ```
2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapot "Connected" állapotúként jelenik meg, és láthatja a bejövő és a kimenő bájtokat.

   ```output
   ConnectivityState         : Connected
   EgressBytesTransferred    : 181664
   IngressBytesTransferred   : 182080
   LastConnectionEstablished : 10/19/22020 12:40:54 AM
   LastEventID               : 24401
   LastEventMessage          : The connectivity state for the local network site 'F7F7BFC7_SiteVNet4' changed from Connecting to
                               Connected.
   LastEventTimeStamp        : 10/19/2020 12:40:54 AM
   LocalNetworkSiteName      : F7F7BFC7_SiteVNet4
   ```
