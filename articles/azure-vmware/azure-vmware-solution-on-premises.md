---
title: Csatlakozás Azure VMware Solution helyszíni környezethez
description: Ismerje meg, hogyan Azure VMware Solution a helyszíni környezethez.
ms.topic: tutorial
ms.date: 04/19/2021
ms.openlocfilehash: 392d82a9aca9b60b394a5d5f4a7e6b0111438e59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725614"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Csatlakozás Azure VMware Solution helyszíni környezethez

Ebben a cikkben továbbra is [](production-ready-deployment-steps.md) a tervezés során összegyűjtött információkat fogja használni a Azure VMware Solution helyszíni környezethez való csatlakozáshoz.

Mielőtt hozzákezd, létre kell hoznunk egy ExpressRoute-kapcsolatkört a helyszíni környezetből az Azure-ba.


>[!NOTE]
> Csatlakozhat VPN-en keresztül, de ez a rövid útmutató nem erre a dokumentumra terjed ki.

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute-kapcsolat Global Reach létrehozása

A helyszíni és a magánfelhőhöz Azure VMware Solution ExpressRoute Global Reach használatával való helyszíni kapcsolat létesítéhez kövesse a helyszíni környezetek magánfelhővel való társviszony-létesítését ismertető [oktatóanyagot.](tutorial-expressroute-global-reach-private-cloud.md)

Ez az oktatóanyag a diagramon látható kapcsolatot hoz létre.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="Az ExpressRoute Global Reach helyszíni hálózati kapcsolat diagramja." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Helyszíni hálózati kapcsolat ellenőrzése

Most már látnia kell a helyszíni **peremhálózati** útválasztón, ahol az ExpressRoute csatlakoztatja az NSX-T hálózati szegmenseket és a Azure VMware Solution felügyeleti szegmenseket.

>[!IMPORTANT]
>Mindenki más környezettel rendelkezik, néhánynak pedig lehetővé kell tennie, hogy ezek az útvonalak újra a helyszíni hálózatra propagálva leselkednek.  

Egyes környezetek tűzfallal védik az ExpressRoute-kapcsolatkört.  Ha nem történik tűzfal és útvonal-csonkolás, pingelni kell a Azure VMware Solution vCenter-kiszolgálót vagy egy, az [NSX-T szegmensben](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) található virtuális gépet a helyszíni környezetből. Emellett az NSX-T szegmensben található virtuális gépről elérheti a helyszíni környezet erőforrásait.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő szakaszokkal a VMware HCX üzembe helyezéséhez és konfiguráláshoz

> [!div class="nextstepaction"]
> [A VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)