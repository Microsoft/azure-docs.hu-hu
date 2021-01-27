---
title: Oktatóanyag – helyszíni környezetek saját felhőbe
description: Megtudhatja, hogyan hozhat létre ExpressRoute Global Reach-társítást egy Azure VMware-megoldásban található privát felhőbe.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: e7b1e349f67fe63f63183c0ff6d1522498c65f8c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918721"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Oktatóanyag: helyszíni helyszíni környezetek egy privát felhőhöz

A ExpressRoute Global Reach összekapcsolja a helyszíni környezetet az Azure VMware-megoldás saját felhővel. A ExpressRoute Global Reach kapcsolat létrejött a saját felhőalapú ExpressRoute áramkör és egy meglévő ExpressRoute-kapcsolat között a helyszíni környezetekhez. 

Az [Azure – Private felhő hálózatkezelésének konfigurálásakor](tutorial-configure-networking.md) használt ExpressRoute-áramkör engedélyezési kulcsok létrehozását és használatát igényli.  Már használt egy engedélyezési kulcsot a ExpressRoute áramkörből, és ebben az oktatóanyagban egy másodikat fog létrehozni a helyszíni ExpressRoute-áramkörhöz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Második engedélyezési kulcs létrehozása a _Circuit 2_, a Private Cloud ExpressRoute áramkörhöz
> * Használja a [Azure Portal](#azure-portal-method) vagy az Azure CLI-t az _1. áramkör_ -előfizetés egyik [Cloud Shell metódusában](#azure-cli-in-a-cloud-shell-method) , hogy lehetővé tegye a helyszíni – privát Felhőbeli ExpressRoute Global REACH


## <a name="before-you-begin"></a>Előkészületek

Mielőtt engedélyezi a kapcsolatot a két ExpressRoute-áramkör között a ExpressRoute Global Reach használatával, tekintse át a dokumentációt, amely bemutatja, hogyan [engedélyezheti a kapcsolatokat különböző Azure-előfizetésekben](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Előfeltételek

- Kapcsolat létesítése egy Azure VMware-megoldás saját ExpressRoute-áramkörével és egy Azure-beli virtuális hálózatban (VNet) található ExpressRoute-átjáróval, amely a _2. áramköri_ eljárásokból áll.  
- Egy különálló, működő ExpressRoute-áramkör, amely a helyszíni környezetek Azure-hoz való összekapcsolására szolgál – amely az _1. áramköri_ eljárás perspektívája.
- Egy/29 nem átfedésben lévő [hálózati címterület](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) a ExpressRoute Global REACH-társításhoz.
- Győződjön meg arról, hogy minden átjáró, beleértve a ExpressRoute-szolgáltató szolgáltatását, támogatja a 4 bájtos autonóm rendszer számát (ASN). Az Azure VMware-megoldás 4 bájtos nyilvános ASN használ a hirdetési útvonalakhoz.

> [!TIP]
> Ezeknek az előfeltételeknek a kontextusában a helyszíni ExpressRoute áramkör _1_, a saját Felhőbeli ExpressRoute áramkör pedig egy másik előfizetésben és _2_. címkével ellátott áramkörben található. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>ExpressRoute-engedélyezési kulcs létrehozása a privát felhőben

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat > ExpressRoute > az engedélyezési kulcs igénylése** lehetőséget.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Válassza a kapcsolat > ExpressRoute > az új kérés indításához kérjen egy engedélyezési kulcsot.":::

2. Adja meg az engedélyezési kulcs nevét, majd válassza a **Létrehozás** lehetőséget. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Válassza a létrehozás lehetőséget egy új engedélyezési kulcs létrehozásához. ":::

   A létrehozást követően az új kulcs megjelenik a privát felhőhöz tartozó engedélyezési kulcsok listájában. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Győződjön meg arról, hogy az új engedélyezési kulcs megjelenik a titkos felhőhöz tartozó kulcsok listájában. ":::

3. Jegyezze fel az engedélyezési kulcsot és a ExpressRoute AZONOSÍTÓját, valamint a/29-címterület számát. A következő lépésben a társítás befejezéséhez fogja használni őket. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Peer Private Cloud a helyszíni hitelesítéshez engedélyezési kulcs használatával

Most, hogy létrehozott egy engedélyezési kulcsot a saját felhőalapú ExpressRoute áramkörhöz, azt a helyszíni ExpressRoute-áramkörrel is elvégezheti.  A társítás a helyszíni ExpressRoute áramkör szempontjából a [Azure Portal](#azure-portal-method) vagy az Azure parancssori felületének a [Cloud Shellban](#azure-cli-in-a-cloud-shell-method)történő használatával történik. Mindkét módszerrel a saját felhőalapú ExpressRoute-áramkör erőforrás-AZONOSÍTÓját és engedélyezési kulcsát használhatja a társítás befejezéséhez.

### <a name="azure-portal-method"></a>Azure Portal metódus

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel.

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat > ExpressRoute Global REACH > Hozzáadás** lehetőséget.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="A menüben válassza a kapcsolat, a ExpressRoute Global Reach fület, és adja hozzá a következőt:.":::

1. A következő lehetőségek közül választhat a helyszíni Felhőbeli kapcsolatok létrehozásához:

   - Válassza ki a ExpressRoute áramkört a listából.
   - Ha rendelkezik egy áramkör-AZONOSÍTÓval, másolja ki és illessze be.

1. Válassza a **Kapcsolódás** lehetőséget. Az új kapcsolat a helyszíni felhőalapú kapcsolatok listájában jelenik meg.  

>[!TIP]
>A listából törölheti vagy leválaszthatja a kapcsolatot a **továbbiak** lehetőség kiválasztásával.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Helyszíni kapcsolat leválasztása vagy törlése":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI Cloud Shell metódusban

A [CLI-parancsokat](../expressroute/expressroute-howto-set-global-reach-cli.md) speciális részletekkel és példákkal bővítettük, így könnyebben konfigurálható a ExpressRoute Global REACH a helyszíni környezetek között egy Azure VMware-megoldás saját felhőbe.  

> [!TIP]  
> Az Azure CLI-parancs kimenetének rövidített eléréséhez ezek az utasítások egy [ `–query` argumentumot használhatnak egy JMESPath-lekérdezés végrehajtásához, hogy csak a szükséges eredményeket jelenítsék meg](/cli/azure/query-azure-cli).


1. Jelentkezzen be a Azure Portalba ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel, és nyisson meg egy Cloud Shell. Hagyja a shellt bash-ként.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Jelentkezzen be a Azure Portalba, és nyisson meg egy Cloud Shell.":::
 
2. Adja meg az Azure CLI-parancsot a társítás létrehozásához. Használja az adott információt és erőforrás-azonosítót, az engedélyezési kulcsot és/29 CIDR hálózati blokkot. 

   A képen egy példa látható a használni kívánt parancsra, valamint a sikeres társat jelző kimenetre. A példában szereplő parancs a ["ExpressRoute-áramkörök közötti kapcsolat engedélyezése különböző Azure-előfizetésekben" című rész 3. lépésében](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)használt parancson alapul.

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Hozzon létre ExpressRoute Global Reach-társítást az Azure CLI-paranccsal egy Cloud Shell.":::
 
   A helyi környezetekről a saját felhőhöz kapcsolódhat a ExpressRoute Global Reach-társításon keresztül.

> [!TIP]
> Az imént létrehozott társítást törölheti a helyszíni hálózatokra vonatkozó utasításokat követve a [kapcsolat letiltása](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) lehetőséggel.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy második engedélyezési kulcsot a saját felhőalapú ExpressRoute áramkörhöz. Azt is megtanulta, hogyan engedélyezheti a helyszíni – magán Felhőbeli ExpressRoute Global Reach-társítást. 

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti és konfigurálhatja a VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.

> [!div class="nextstepaction"]
> [A VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->