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
ms.openlocfilehash: 91dc6ba0bcd455aefe9de2efdff2feb20938152d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376385"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>1. lépés: navigáljon a virtuális hálózati átjáróhoz

1. A [Azure Portal](https://portal.azure.com)navigáljon az **összes erőforráshoz**. 
2. A virtuális hálózati átjáró megnyitásához nyissa meg a virtuális hálózati átjárót, és kattintással jelölje ki. 

### <a name="step-2-delete-connections"></a>2. lépés: kapcsolatok törlése

1. A virtuális hálózati átjáró lapján kattintson a **kapcsolatok** elemre az átjáró összes kapcsolatának megtekintéséhez.
2. Kattintson a **"..."** elemre a kapcsolatok nevének sorában, majd válassza a **Törlés** lehetőséget a legördülő menüből.
3. Kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni kívánja a kapcsolódást. Ha több kapcsolattal rendelkezik, törölje az összes kapcsolatot.

### <a name="step-3-delete-the-virtual-network-gateway"></a>3. lépés: a virtuális hálózati átjáró törlése

Ügyeljen arra, hogy ha a S2S-konfiguráción kívül P2S-konfigurációval rendelkezik ehhez a VNet, a virtuális hálózati átjáró törlésével a rendszer figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.

1. A virtuális hálózati átjáró lapon kattintson az **Áttekintés** elemre.
2. Az átjáró törléséhez az **Áttekintés** lapon kattintson a **Törlés** elemre.
