---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328434"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **keresési erőforrások, szolgáltatások és dokumentumok (G +/)** területen írja be a *virtuális hálózat* kifejezést.

   ![Virtual Network erőforrás oldalának megkeresése](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Virtuális hálózati erőforrás keresése lap")
1. A **piactér** eredményei közül válassza a **Virtual Network** lehetőséget.

   ![Virtuális hálózat kiválasztása](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Virtuális hálózati erőforrás keresése lap")
1. A **Virtual Network** lapon válassza a **Létrehozás** lehetőséget.

   ![virtuális hálózat lap](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "A Létrehozás lehetőség kiválasztása")
1. A **Létrehozás** gombra kattintva megnyílik a **virtuális hálózat létrehozása** lap.
1. Az **alapok** lapon adja meg a **Project details** és a **instance details** VNet beállításait.

   ![Alapbeállítások lap](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Alapbeállítások lap") A mezők kitöltése után zöld pipa jelenik meg, ha a mezőben megadott karaktereket érvényesíti a rendszer. Néhány érték autofilled, amelyet a saját értékeivel helyettesíthet:

   - **Előfizetés**: Ellenőrizze, hogy a felsorolt előfizetés megfelelő-e. Az előfizetéseket a legördülő menüben módosíthatja.
   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy kattintson az **új létrehozása** lehetőségre egy új létrehozásához. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Név**: adja meg a virtuális hálózat nevét.
   - **Régió**: válassza ki a VNet helyét. A hely határozza meg, hogy az adott VNet üzembe helyezett erőforrások hol fognak élni.

1. Az **IP-címek** lapon adja meg az értékeket. Az alábbi példákban látható értékek szemléltetési célokat szolgálnak. Módosítsa ezeket az értékeket a szükséges beállításoknak megfelelően.

   ![IP-címek lap](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP-címek lap")  
   - **IPv4-címterület**: alapértelmezés szerint a Címterület automatikusan létrejön. A Címterület elemre kattintva beállíthatja, hogy tükrözze a saját értékeit. További címterület hozzáadására is lehetőség van.
   - **Alhálózat**: Ha az alapértelmezett címtartományt használja, a rendszer automatikusan létrehoz egy alapértelmezett alhálózatot. Ha megváltoztatja a Címterület méretét, hozzá kell adnia egy alhálózatot. Válassza az **+ alhálózat hozzáadása** lehetőséget az **alhálózat hozzáadása** ablak megnyitásához. Adja meg a következő beállításokat, majd válassza a **Hozzáadás** lehetőséget az értékek hozzáadásához:
      - **Alhálózat neve**: ebben a példában a "FrontEnd" alhálózatot nevezték el.
      - **Alhálózati címtartomány**: az alhálózat címtartomány.

1. A **Biztonság** lapon most hagyja meg az alapértelmezett értékeket:

   - **DDos Protection**: alapszintű
   - **Tűzfal**: letiltva
1. A virtuális hálózat beállításainak ellenőrzéséhez válassza a **felülvizsgálat + létrehozás** elemet.
1. A beállítások érvényesítése után válassza a **Létrehozás** lehetőséget.
