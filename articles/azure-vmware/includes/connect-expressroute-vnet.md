---
title: A ExpressRoute és a virtuális hálózati átjáró összekötése
description: A ExpressRoute virtuális hálózati átjáróhoz való kapcsolódásának lépései.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494356"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navigáljon a [vSphere-fürt üzembe helyezése az Azure](../tutorial-create-private-cloud.md) -ban című oktatóanyagban létrehozott privát felhőhöz. A **kezelés** területen válassza a **kapcsolat** lehetőséget, majd válassza a **ExpressRoute** lapot.

1. Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd **az engedélyezési kulcs kérése** lehetőséget kell választania.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd az engedélyezési kulcs kérése lehetőséget kell választania." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Lépjen az előző lépésben létrehozott Virtual Network átjáróra, és a **Beállítások** területen válassza a **kapcsolatok** lehetőséget. A **kapcsolatok** lapon válassza a **+ Hozzáadás** lehetőséget.

1. A **kapcsolatok hozzáadása** lapon adja meg a mezők értékeit, majd kattintson az **OK gombra**. 

   | Mező | Érték |
   | --- | --- |
   | **Név**  | Adjon egy nevet a kapcsolatnak.  |
   | **Kapcsolat típusa**  | Válassza a **ExpressRoute** lehetőséget.  |
   | **Engedély beváltása**  | Győződjön meg arról, hogy a jelölőnégyzet be van jelölve.  |
   | **Virtuális hálózati átjáró** | A korábban létrehozott Virtual Network átjáró.  |
   | **Engedélyezési kulcs**  | Másolja és illessze be az engedélyezési kulcsot az erőforráscsoport ExpressRoute lapjáról. |
   | **Társ áramkör URI-ja**  | Másolja és illessze be a ExpressRoute-azonosítót az erőforráscsoport ExpressRoute lapjáról.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Képernyőkép a kapcsolat hozzáadása lapról a ExpressRoute a virtuális hálózati átjáróhoz való csatlakoztatásához.":::

Létrejön a ExpressRoute-áramkör és a Virtual Network közötti kapcsolat.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Képernyőfelvétel a virtuális hálózati átjáró kapcsolatairól.":::