---
title: Oktatóanyag – helyszíni környezetek saját felhőbe
description: Megtudhatja, hogyan hozhat létre ExpressRoute Global Reach-társítást egy Azure VMware-megoldásban található privát felhőbe.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 59ddc2dd4aed850328807a4543d2fabd6f5a99f1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564500"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Oktatóanyag: helyszíni helyszíni környezetek egy privát felhőhöz

A ExpressRoute Global Reach összekapcsolja a helyszíni környezetet az Azure VMware-megoldás saját felhővel. A ExpressRoute Global Reach kapcsolat létrejött a saját felhőalapú ExpressRoute áramkör és egy meglévő ExpressRoute-kapcsolat között a helyszíni környezetekhez. 

Az [Azure-beli VMware Private-felhő hálózatkezelésének konfigurálásakor](tutorial-configure-networking.md) használt ExpressRoute-áramkör engedélyezési kulcsok létrehozását és használatát igényli.  A ExpressRoute áramkör már használ egy engedélyezési kulcsot, és ebben az oktatóanyagban létrehoz egy második engedélyezési kulcsot a társ-és a helyszíni ExpressRoute-áramkörrel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy második engedélyezési kulcsot a _Circuit 2_, a Private Cloud ExpressRoute áramkörhöz.
> * Használja a Azure Portal vagy az Azure CLI-t az _1. áramkör_ -előfizetés egyik Cloud Shell metódusában, hogy lehetővé tegye a helyszíni – magán Felhőbeli ExpressRoute Global REACH-társítást.


## <a name="before-you-begin"></a>Előkészületek

Mielőtt engedélyezi a kapcsolatot a két ExpressRoute-áramkör között a ExpressRoute Global Reach használatával, tekintse át a dokumentációt, amely bemutatja, hogyan [engedélyezheti a kapcsolatokat különböző Azure-előfizetésekben](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Előfeltételek

- Kapcsolat létesítése egy Azure VMware-megoldás saját ExpressRoute-áramkörével és egy Azure-beli virtuális hálózatban (VNet) található ExpressRoute-átjáróval, amely a 2. áramköri eljárásokból áll.
- Egy különálló, működő ExpressRoute-áramkör, amely a helyszíni környezetek Azure-hoz való összekapcsolására szolgál – amely az 1. áramköri eljárás perspektívája.
- Egy/29 nem átfedésben lévő [hálózati címterület](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) a ExpressRoute Global REACH-társításhoz.
- Győződjön meg arról, hogy minden átjáró, beleértve a ExpressRoute-szolgáltató szolgáltatását, támogatja a 4 bájtos autonóm rendszer számát (ASN). Az Azure VMware-megoldás 4 bájtos nyilvános ASN használ a hirdetési útvonalakhoz.

>[!IMPORTANT]
>Ezeknek az előfeltételeknek a kontextusában a helyszíni ExpressRoute áramkör _1_, a saját Felhőbeli ExpressRoute áramkör pedig egy másik előfizetésben és _2_. címkével ellátott áramkörben található.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>ExpressRoute-engedélyezési kulcs létrehozása a Private Cloud ExpressRoute áramkörben

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Peer Private Cloud a helyszíni engedélyezési kulccsal
Most, hogy létrehozott egy engedélyezési kulcsot a saját felhőalapú ExpressRoute áramkörhöz, azt a helyszíni ExpressRoute-áramkörrel is elvégezheti. A társítás a helyszíni ExpressRoute áramkör szempontjából a **Azure Portal** vagy az Azure parancssori felületének a **Cloud Shellban** történő használatával történik. Mindkét módszerrel a saját felhőalapú ExpressRoute-áramkör erőforrás-AZONOSÍTÓját és engedélyezési kulcsát használhatja a társítás befejezéséhez.

### <a name="portal"></a>[Portál](#tab/azure-portal)
 
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel.

1. A Private Cloud **Overview**(kezelés) területen válassza a **kapcsolat**  >  **ExpressRoute Global REACH**  >  **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Képernyőfelvétel: a ExpressRoute Global Reach lapja az Azure VMware Solution Private Cloud-ban.":::

1. Helyszíni felhőalapú kapcsolatok létrehozása. Tegye a következők egyikét, majd válassza a **Létrehozás** lehetőséget:

   - Válassza ki a **ExpressRoute áramkört** a listából, vagy
   - Ha rendelkezik az áramköri AZONOSÍTÓval, illessze be a mezőbe, és adja meg az engedélyezési kulcsot.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Adja meg a ExpressRoute AZONOSÍTÓját és az engedélyezési kulcsot, majd válassza a létrehozás lehetőséget.":::   
   
   Az új kapcsolat a helyszíni felhőalapú kapcsolatok listájában jelenik meg.

>[!TIP]
>A listából törölheti vagy leválaszthatja a kapcsolatot a **továbbiak** lehetőség kiválasztásával.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Helyszíni kapcsolat leválasztása vagy törlése":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [CLI-parancsokat](../expressroute/expressroute-howto-set-global-reach-cli.md) speciális részletekkel és példákkal bővítettük, így könnyebben konfigurálható a ExpressRoute Global REACH a helyszíni környezetek között egy Azure VMware-megoldás saját felhőbe.

>[!TIP]
>Az Azure CLI-parancs kimenetének rövidített eléréséhez ezek az utasítások egy [ `–query` argumentumot](/cli/azure/query-azure-cli) használhatnak egy JMESPath-lekérdezés végrehajtásához, hogy csak a szükséges eredményeket jelenítsék meg.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az előfizetéssel, mint a helyszíni ExpressRoute áramkörrel. 

1. Nyisson meg egy Cloud Shell, és hagyja a shellt bash-ként.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Képernyőfelvétel: a Azure Portal Cloud Shell.":::

1. Hozza létre az 1. áramköri társítást, amely áthalad a 2. áramkör erőforrás-AZONOSÍTÓjában és engedélyezési kulcsában. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Képernyőfelvétel: a parancs és az 1. és 2. áramkör közötti sikeres társítás eredményei.":::

A helyi környezetekről a saját felhőhöz kapcsolódhat a ExpressRoute Global Reach-társításon keresztül.

>[!TIP]
>A [társítást a helyszíni hálózatokra](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) vonatkozó utasításokat követve a kapcsolat letiltása lehetőséggel törölheti.


---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti a helyszíni és a privát Felhőbeli ExpressRoute Global Reach-társítást. 

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan telepítheti és konfigurálhatja a VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.

> [!div class="nextstepaction"]
> [A VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->