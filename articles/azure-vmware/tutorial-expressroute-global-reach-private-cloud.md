---
title: Oktatóanyag – helyszíni környezetek saját felhőbe
description: Megtudhatja, hogyan hozhat létre ExpressRoute Global Reach-társítást egy Azure VMware-megoldásban található privát felhőbe.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558790"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Oktatóanyag: helyszíni helyszíni környezetek egy privát felhőhöz

A ExpressRoute Global Reach összekapcsolja a helyszíni környezetet az Azure VMware-megoldás saját felhővel. A ExpressRoute Global Reach kapcsolat létrejött a saját felhőalapú ExpressRoute áramkör és egy meglévő ExpressRoute-kapcsolat között a helyszíni környezetekhez. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Azure Portal használatával engedélyezheti a helyszíni – saját Felhőbeli ExpressRoute Global Reach-társítást.


## <a name="before-you-begin"></a>Előkészületek

Mielőtt engedélyezi a kapcsolatot a két ExpressRoute-áramkör között a ExpressRoute Global Reach használatával, tekintse át a dokumentációt, amely bemutatja, hogyan [engedélyezheti a kapcsolatokat különböző Azure-előfizetésekben](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Előfeltételek

- Egy különálló, működő ExpressRoute-áramkör, amely a helyszíni környezetek Azure-hoz való kapcsolódására szolgál.
- Győződjön meg arról, hogy minden átjáró, beleértve a ExpressRoute-szolgáltató szolgáltatását, támogatja a 4 bájtos autonóm rendszer számát (ASN). Az Azure VMware-megoldás 4 bájtos nyilvános ASN használ a hirdetési útvonalakhoz.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>ExpressRoute-engedélyezési kulcs létrehozása a helyszíni ExpressRoute-áramkörben

1. Az **ExpressRoute-áramkörök** panel beállítások területén válassza az **engedélyek** elemet.

2. Adja meg az engedélyezési kulcs nevét, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Válassza az engedélyek lehetőséget, és adja meg az engedélyezési kulcs nevét.":::
  
     A létrehozást követően az új kulcs megjelenik az áramkörhöz tartozó engedélyezési kulcsok listájában.
 
 4. Jegyezze fel az engedélyezési kulcsot és a ExpressRoute AZONOSÍTÓját. A következő lépésben a társítás befejezéséhez fogja használni őket.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Társ-magán felhő és helyszíni

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat > ExpressRoute Global REACH > Hozzáadás** lehetőséget.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="A menüben válassza a kapcsolat, a ExpressRoute Global Reach fület, és adja hozzá a következőt:.":::

2. Adja meg a ExpressRoute AZONOSÍTÓját és az előző szakaszban létrehozott engedélyezési kulcsot.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Adja meg a ExpressRoute AZONOSÍTÓját és az engedélyezési kulcsot, majd válassza a létrehozás lehetőséget.":::

3. Válassza a **Létrehozás** lehetőséget. Az új kapcsolat a helyszíni felhőalapú kapcsolatok listájában jelenik meg.  

>[!TIP]
>A listából törölheti vagy leválaszthatja a kapcsolatot a **továbbiak** lehetőség kiválasztásával.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Helyszíni kapcsolat leválasztása vagy törlése":::


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti a helyszíni és a privát Felhőbeli ExpressRoute Global Reach-társítást. 

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti és konfigurálhatja a VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.

> [!div class="nextstepaction"]
> [A VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
