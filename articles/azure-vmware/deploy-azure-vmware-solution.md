---
title: Azure VMware-megoldás üzembe helyezése és konfigurálása
description: Megtudhatja, hogyan használhatja a tervezési szakaszban összegyűjtött információkat az Azure VMware-megoldás saját Felhőbeli üzembe helyezéséhez és konfigurálásához.
ms.topic: tutorial
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: bfd057a19ebe26a66d11b52ddf17c285a1f9a308
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652734"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Azure VMware-megoldás üzembe helyezése és konfigurálása

Ebben a cikkben a [tervezési szakaszban](production-ready-deployment-steps.md) található információk segítségével telepítheti és konfigurálhatja az Azure VMware-megoldást. 

>[!IMPORTANT]
>Ha még nem adta meg az adatokat, lépjen vissza a [tervezés szakaszra](production-ready-deployment-steps.md) a folytatás előtt.


## <a name="create-an-azure-vmware-solution-private-cloud"></a>Azure VMware-megoldás saját felhő létrehozása

Kövesse az [Azure VMware Solution Private Cloud](tutorial-create-private-cloud.md) oktatóanyag előfeltételeit és lépéseit. A [Azure Portal](tutorial-create-private-cloud.md#azure-portal) vagy az [Azure parancssori](tutorial-create-private-cloud.md#azure-cli)felületének használatával létrehozhat egy Azure VMware-megoldáshoz tartozó saját felhőt is.  

>[!NOTE]
>Ennek a lépésnek a teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: üzembehelyezési](https://www.youtube.com/embed/gng7JjxgayI) videó.

## <a name="create-the-jump-box"></a>A Jump Box létrehozása

>[!IMPORTANT]
>Ha a **saját felhő létrehozása** képernyő kezdeti kiépítési lépése során üresen hagyta a **Virtual Network** lehetőséget, a szakasz folytatása **előtt** fejezze be a [hálózatkezelés konfigurálása a VMware Private Cloud](tutorial-configure-networking.md) oktatóanyaghoz című témakört.  

Az Azure VMware-megoldás üzembe helyezése után létre kell hoznia a virtuális hálózat vCenter és NSX csatlakozó ugrási mezőjét. Miután konfigurálta a ExpressRoute áramköröket és a ExpressRoute Global Reach, a Jump Box nem szükséges.  Az Azure VMware-megoldásban azonban hasznos lehet a vCenter és a NSX elérése.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Az Azure VMware-megoldás Jump Box létrehozása" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

A virtuális gép (VM) a [telepítési folyamat részeként azonosított vagy létrehozott](production-ready-deployment-steps.md#attach-virtual-network-to-azure-vmware-solution)virtuális hálózatban történő létrehozásához kövesse az alábbi utasításokat: 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Kapcsolódás virtuális hálózathoz a ExpressRoute használatával

>[!IMPORTANT]
>Ha már definiált egy virtuális hálózatot az Azure-beli üzembe helyezési képernyőn, ugorjon a következő szakaszra.

Ha nem adott meg virtuális hálózatot az üzembe helyezési lépésben, és a szándéka az Azure VMware-megoldás ExpressRoute meglévő ExpressRoute-átjáróhoz való összekötése, kövesse az alábbi lépéseket.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Meghirdetett hálózati útvonalak ellenőrzése

A Jump Box abban a virtuális hálózaton található, ahol az Azure VMware megoldás a ExpressRoute áramkörén keresztül csatlakozik.  Az Azure-ban nyissa meg a Jump Box hálózati adapterét, és [tekintse meg a hatályos útvonalakat](../virtual-network/manage-route-table.md#view-effective-routes).

Az érvényben lévő útvonalak listájában az Azure VMware-megoldás üzembe helyezésének részeként létrehozott hálózatokat kell látnia. A [privát felhő létrehozásakor](#create-an-azure-vmware-solution-private-cloud) [ `/22` megadott hálózatról](production-ready-deployment-steps.md#ip-address-segment) több hálózatot is láthat.  

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Az Azure VMware-megoldásból az Azure-ba hirdetett hálózati útvonalak ellenőrzése Virtual Network" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

Ebben a példában az üzembe helyezés során a 10.74.72.0/22 hálózat bemenete a/24 hálózatot eredményezi.  Ha valami hasonlót lát, csatlakozhat a vCenter-hez az Azure VMware megoldásban.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Kapcsolódás és bejelentkezés a vCenter és a NSX-T-re

Jelentkezzen be az előző lépésben létrehozott Jump Box-ba. Miután bejelentkezett, nyisson meg egy webböngészőt, és lépjen be, és jelentkezzen be a vCenter és a NSX-T Manager alkalmazásba.  

Azonosíthatja a vCenter, és a NSX-T kezelő konzol IP-címeit és hitelesítő adatait a Azure Portal.  Válassza ki saját felhőjét, majd az **Áttekintés** nézetben válassza az **identitás > alapértelmezett** lehetőséget. 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Hálózati szegmens létrehozása Azure VMware-megoldással

Az NSX-T Manager használatával új hálózati szegmenseket hozhat létre az Azure VMware-megoldási környezetben.  Definiálta a [tervezés szakaszban](production-ready-deployment-steps.md)létrehozni kívánt hálózatokat.  Ha még nem definiálta őket, lépjen vissza a [tervezés szakaszra](production-ready-deployment-steps.md) a továbblépés előtt.

>[!IMPORTANT]
>Győződjön meg arról, hogy a megadott CIDR hálózati címterület nem fedi át az Azure-ban vagy a helyszíni környezetekben található bármit.  

Kövesse az [NSX-t hálózati szegmens létrehozása az Azure VMware-megoldás](tutorial-nsx-t-network-segment.md) oktatóanyagban NSX-t hálózati szegmens létrehozásához az Azure VMware megoldásban című témakört.

## <a name="verify-advertised-nsx-t-segment"></a>Meghirdetett NSX-T szegmens ellenőrzése

Lépjen vissza a [meghirdetett hálózati útvonalak ellenőrzése](#verify-network-routes-advertised) lépésre. Az előző lépésben létrehozott hálózati szegmenseket jelölő listában más útvonalak is megjelennek.  

A virtuális gépek esetében a [hálózati szegmens létrehozása az Azure VMware megoldásba](#create-a-network-segment-on-azure-vmware-solution) lépésben létrehozott szegmenseket fogja hozzárendelni.  

Mivel a DNS szükséges, azonosítsa a használni kívánt DNS-kiszolgálót.  

- Ha a ExpressRoute Global Reach konfigurálva van, használja a helyileg használni kívánt DNS-kiszolgálót.  
- Ha rendelkezik Azure-beli DNS-kiszolgálóval, használja azt.  
- Ha még nem rendelkezik a szolgáltatással, akkor a Jump Box által használt tetszőleges DNS-kiszolgálót használja.

>[!NOTE]
>Ez a lépés a DNS-kiszolgáló azonosítására szolgál, és ebben a lépésben nem végeznek konfigurációkat.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Választható DHCP-szolgáltatások biztosítása NSX-T hálózati szegmenshez

Ha azt tervezi, hogy DHCP-T használ a NSX-T szegmenseken, folytassa ezt a szakaszt. Ellenkező esetben ugorjon a [virtuális gép hozzáadása elemre a NSX-T hálózati szegmens](#add-a-vm-on-the-nsx-t-network-segment) szakaszban.  

Most, hogy létrehozta a NSX-T hálózati szegmenst, két módon hozhat létre és kezelhet DHCP-T az Azure VMware megoldásban:

* Ha a NSX-T használatával futtatja a DHCP-kiszolgálót, [létre kell hoznia egy DHCP-kiszolgálót és egy](manage-dhcp.md#create-a-dhcp-server) [továbbítót az adott kiszolgálóra](manage-dhcp.md#create-dhcp-relay-service). 
* Ha harmadik féltől származó külső DHCP-kiszolgálót használ a hálózaton, [létre kell hoznia a DHCP-továbbító szolgáltatást](manage-dhcp.md#create-dhcp-relay-service).  Ehhez a beállításhoz [csak a továbbítási konfigurációt kell végrehajtani](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Virtuális gép felvétele a NSX-T hálózati szegmensbe

Az Azure VMware-megoldás vCenter helyezzen üzembe egy virtuális gépet, és használja azt az Azure VMware-megoldási hálózatok kapcsolatának ellenőrzéséhez:

- Az Internet
- Azure virtuális hálózatok
- Helyszíni.  

A virtuális gépet bármilyen vSphere-környezetben üzembe helyezheti.  Csatlakoztassa a virtuális gépet az NSX-T-ben korábban létrehozott hálózati szegmensek egyikéhez.  

>[!NOTE]
>DHCP-kiszolgáló beállítása esetén a virtuális gép hálózati konfigurációja (ne felejtse el beállítani a hatókört).  Ha statikusan konfigurálja a konfigurálást, akkor a szokásos módon konfigurálja a konfigurálást.

## <a name="test-the-nsx-t-segment-connectivity"></a>A NSX-T szegmens kapcsolat tesztelése

Jelentkezzen be az előző lépésben létrehozott virtuális gépre, és ellenőrizze a kapcsolatot;

1. Egy IP-cím pingelése az interneten.
2. Egy böngészőben nyissa meg az internetes webhelyet.
3. Pingelje az Azure Virtual Network található Jump Box-t.

Az Azure VMware-megoldás mostantól működik, és sikeresen létesített kapcsolatot az Azure Virtual Network és az internet között.

## <a name="next-steps"></a>Következő lépések

A következő szakaszban az Azure VMware-megoldást a helyszíni hálózathoz a ExpressRoute-on keresztül kapcsolhatja össze.
> [!div class="nextstepaction"]
> [Azure VMware-megoldás összekapcsolása a helyszíni környezettel](azure-vmware-solution-on-premises.md)
