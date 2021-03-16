---
title: Azure VMware-megoldás összekapcsolása a helyszíni környezettel
description: Ismerje meg, hogyan csatlakoztatható az Azure VMware-megoldás a helyszíni környezethez.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491792"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware-megoldás összekapcsolása a helyszíni környezettel

Ebben a cikkben az Azure VMware-megoldás helyszíni környezethez való csatlakozásának [megtervezése során összegyűjtött információkat](production-ready-deployment-steps.md) fogja használni.

Mielőtt elkezdené, két előfeltétel van az Azure VMware-megoldás helyszíni környezethez való csatlakoztatásához:

- Egy ExpressRoute áramkör a helyszíni környezetből az Azure-ba.
- A/29 nem átfedésben lévő CIDR a ExpressRoute Global Reach-társításhoz, amelyet a [tervezési fázis](production-ready-deployment-steps.md)részeként adott meg.

>[!NOTE]
> Csatlakozhat VPN-kapcsolaton keresztül, de ez a gyors üzembe helyezési dokumentum hatókörén kívül esik.

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute-Global Reach kapcsolat létrehozása

Ha helyszíni kapcsolatot szeretne létesíteni az Azure VMware-megoldás privát felhővel a ExpressRoute Global Reach használatával, kövesse a [helyszíni környezeteket egy privát felhőalapú](tutorial-expressroute-global-reach-private-cloud.md) oktatóanyagban.

Ez az oktatóanyag egy, az ábrán láthatóhoz tartozó kapcsolatokat eredményez.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="A ExpressRoute Global Reach a helyszíni hálózati kapcsolati diagramot." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Helyszíni hálózati kapcsolat ellenőrzése

Ekkor látnia kell a helyszíni **peremhálózati útválasztón** , ahol a ExpressRoute csatlakoztatja a NSX-T hálózati szegmenseket és az Azure VMware megoldás-felügyeleti szegmenseit.

>[!IMPORTANT]
>Mindenki más környezettel rendelkezik, és néhánynak lehetővé kell tennie, hogy ezeket az útvonalakat vissza lehessen terjeszteni a helyszíni hálózatra.  

Egyes környezetek tűzfallal védik a ExpressRoute áramkört.  Ha nincsenek tűzfalak, és nincs útvonal-metszés, Pingelje az Azure VMware-megoldás vCenter-kiszolgálóját vagy egy virtuális gépet a helyszíni környezetből származó [NSX-T szegmensen](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) . Emellett a NSX-T szegmensben lévő virtuális gépről elérheti a helyszíni környezetben lévő erőforrásokat is.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő szakasszal a VMware HCX üzembe helyezéséhez és konfigurálásához

> [!div class="nextstepaction"]
> [A VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)