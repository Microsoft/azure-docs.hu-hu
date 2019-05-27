---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150278"
---
1. Az a [az Azure portal](http://portal.azure.com), válassza ki a Resource Manager virtuális hálózatot, amelyhez szeretne egy virtuális hálózati átjáró létrehozása.

2. Az a **beállítások** szakaszban a virtuális hálózat lapjának, válassza ki a **alhálózatok** bontsa ki a **alhálózatok** lap.

3. Az a **alhálózatok** lapon jelölje be **átjáró-alhálózat** megnyitásához a **alhálózat hozzáadása** lapot.

   ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Az átjáró alhálózatának hozzáadása")

4. A **neve** az alhálózat nem automatikusan a értékű autofilled *GatewaySubnet*. Ez az érték megadása kötelező az Azure felismerje az alhálózat egy átjáró alhálózata. Módosítsa a autofilled **címtartomány** értékeit, hogy megfeleljenek a konfigurációs követelményeinek, majd válassza ki **OK** az alhálózat létrehozásához.

   ![Az alhálózat hozzáadása](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Az alhálózat hozzáadása")