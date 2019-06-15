---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170056"
---
1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A VNet lap **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok oldalt.
3. Az **Alhálózatok** lap tetején lévő **+Átjáró-alhálózat** elemre kattintva nyissa meg az **Alhálózat hozzáadása** lapot.

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
  
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Az GatewaySubnet érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek.

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
  
5. Az alhálózat létrehozásához kattintson a lap alján található **OK** gombra.
