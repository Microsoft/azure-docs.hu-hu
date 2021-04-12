---
title: Az Azure VMware-megoldás üzembe helyezésének megtervezése
description: Ez a cikk egy Azure VMware-megoldás üzembe helyezési munkafolyamatát ismerteti.  A végeredmény egy olyan környezet, amely készen áll a virtuális gép (VM) létrehozására és áttelepítésére.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 60e0a4083c0253d322b2e10472d0df7496722c10
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107244"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Az Azure VMware-megoldás üzembe helyezésének megtervezése

Ez a cikk az üzembe helyezés során használni kívánt információk azonosításának és gyűjtésének tervezési folyamatát ismerteti. Az üzembe helyezés megtervezése során ügyeljen arra, hogy az üzembe helyezés során könnyen elérhető információkat jegyezze fel.

Az ebben a rövid útmutatóban ismertetett lépések a virtuális gépek (VM-EK) és a Migrálás létrehozására szolgáló, éles környezetben felkészített környezetet biztosítanak. 

A gyűjtött adatok nyomon követéséhez szerezze be a [HCX tervezési ellenőrzőlistáját](https://www.virtualworkloads.com/2021/04/hcx-planning-checklist/).

> [!IMPORTANT]
> Fontos, hogy az Azure-beli VMware-megoldási erőforrás létrehozásának előkészítése előtt korán igényeljen egy gazdagépi kvótát. A gazdagép-kvótát most is kérheti, így ha elkészült a tervezési folyamat, készen áll az Azure VMware-megoldás saját felhőbe való üzembe helyezésére. Miután a támogatási csapat megkapja a fogadó kvóta iránti kérelmét, akár öt munkanapot is igénybe vesz, hogy erősítse meg a kérését, és lefoglalja a gazdagépeket. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több gazdagépet szeretne lefoglalni, hajtsa végre ugyanezt a folyamatot. További információkért tekintse meg a következő hivatkozásokat az előfizetés típusától függően:
> - [Nagyvállalati szerződéssel rendelkező ügyfelek](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-ea-customers)
> - [CSP-ügyfelek](enable-azure-vmware-solution.md?tabs=azure-portal#request-host-quota-for-csp-customers)

## <a name="subscription"></a>Előfizetés

Azonosítsa az Azure VMware-megoldás üzembe helyezéséhez használni kívánt előfizetést.  Létrehozhat egy új előfizetést, vagy felhasználhat egy meglévőt.

>[!NOTE]
>Az előfizetést egy Microsoft Nagyvállalati Szerződés vagy egy felhőalapú megoldás-szolgáltató Azure-csomaggal kell társítani. További információ: [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Erőforráscsoport

Azonosítsa az Azure VMware-megoldáshoz használni kívánt erőforráscsoportot.  Általában egy erőforráscsoport jön létre kifejezetten az Azure VMware megoldáshoz, de használhat egy meglévő erőforráscsoportot is.

## <a name="region"></a>Régió

Azonosítsa az Azure VMware-megoldás üzembe helyezéséhez használni kívánt régiót.  További információkért tekintse meg az [Azure-termékek régiónként elérhető útmutatót](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Erőforrás neve

Adja meg az üzembe helyezés során használni kívánt erőforrás nevét.  Az erőforrás neve egy felhasználóbarát és leíró név, amelyben az Azure VMware-megoldás saját felhőjét adja meg.

>[!IMPORTANT]
>A név nem lehet hosszabb 40 karakternél. Ha a név túllépi ezt a korlátot, nem fog tudni nyilvános IP-címeket létrehozni a privát felhővel való használatra. 

## <a name="size-hosts"></a>Gazdagépek mérete

Azonosítsa az Azure VMware-megoldás telepítésekor használni kívánt gazdagépek méretét.  A teljes listát az [Azure VMware megoldás privát felhők és fürtök](concepts-private-clouds-clusters.md#hosts) dokumentációjában találja.

## <a name="number-of-clusters-and-hosts"></a>Fürtök és gazdagépek száma

Az első Azure VMware-megoldás üzembe helyezése egy olyan privát felhőből áll, amely egyetlen fürtöt tartalmaz. Az üzembe helyezéshez meg kell határoznia az első fürtön telepíteni kívánt gazdagépek számát.

>[!NOTE]
>A fürtben lévő gazdagépek minimális száma három, a maximális érték pedig 16. A fürtök maximális száma a saját felhőben négy. 

További információkért tekintse meg az [Azure VMware Solution Private Cloud és a Clusters](concepts-private-clouds-clusters.md#clusters) dokumentációját.

>[!TIP]
>Bármikor kiterjesztheti a fürtöt, és később további fürtöket is hozzáadhat, ha a kezdeti üzembe helyezési számon felül kell lépnie.

## <a name="ip-address-segment-for-private-cloud-management"></a>IP-cím szegmens a saját felhőalapú felügyelethez

Az üzembe helyezés megtervezésének első lépése az IP-szegmentálás megtervezése. Az Azure VMware-megoldáshoz egy/22 CIDR-hálózat szükséges. Ez a Címterület kisebb hálózati szegmensekre (alhálózatokra) van kialakítva, és az Azure VMware megoldás-felügyeleti szegmensekhez használatos, beleértve a vCenter, a VMware HCX, a NSX-T és a vMotion funkciókat. Az alábbi vizualizáció kiemeli, hogy a rendszer hol használja ezt a szegmenst.

Ez a/22 CIDR hálózati címterület nem fedi át a már meglévő hálózati szegmenseket a helyszínen vagy az Azure-ban.

**Példa:** 10.0.0.0/22

Annak részletes részletezése, hogy a/22 CIDR-hálózat hogyan oszlik meg a saját felhőalapú [hálózati tervezési ellenőrzőlistája](tutorial-network-checklist.md#routing-and-subnet-considerations)alapján.

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Azonosítsa az IP-címek szegmensét" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>A virtuális gépek számítási feladataihoz tartozó IP-címek szegmense

A VMware-környezetekhez hasonlóan a virtuális gépeknek is csatlakozniuk kell egy hálózati szegmenshez. Az Azure VMware megoldásban kétféle szegmens létezik, az L2 kiterjesztett szegmensek (később tárgyalt) és a NSX-T hálózati szegmensek. Az Azure VMware-megoldás éles környezetben történő üzembe helyezése során a helyszíni és a helyi NSX-T hálózati szegmensekben gyakran egy L2 kiterjesztett szegmens kombinációja látható. A kezdeti üzembe helyezés megtervezéséhez az Azure VMware megoldásban azonosítson egyetlen hálózati szegmenst (IP-hálózatot). A hálózat nem fedi át a helyszíni vagy a többi Azure-beli hálózati szegmenst, és nem lehet a korábban meghatározott/22 hálózati szegmensen belül.

Ezt a hálózati szegmenst elsősorban tesztelési célokra használják a kezdeti üzembe helyezés során.

>[!NOTE]
>Erre a hálózatra vagy hálózatokra nem lesz szükség a telepítés során. Üzembe helyezés utáni lépésként jönnek létre.
  
**Példa:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="A virtuális gépek számítási feladataihoz tartozó IP-címek szegmensének azonosítása" border="false":::     

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Azure-Virtual Network csatlakoztatása Azure VMware-megoldáshoz

Az Azure VMware-megoldással való kapcsolat biztosításához a ExpressRoute az Azure VMware megoldás saját felhőből egy ExpressRoute virtuális hálózati átjáróra épül.

*Meglévő* vagy *új* ExpressRoute virtuális hálózati átjárót is használhat.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identitás – Azure Virtual Network az Azure VMware-megoldás csatlakoztatásához" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Meglévő ExpressRoute virtuális hálózati átjáró használata

Ha egy *meglévő* ExpressRoute virtuális hálózati átjárót szeretne használni, az Azure VMware megoldás ExpressRoute áramkörét üzembe helyezés utáni lépésként kell létrehozni. Ebben az esetben hagyja üresen a **Virtual Network** mezőt.

Általános javaslatként egy meglévő ExpressRoute virtuális hálózati átjárót használhat. Tervezési célokra jegyezze fel, hogy melyik ExpressRoute virtuális hálózati átjárót fogja használni, majd folytassa a [következő lépéssel](#vmware-hcx-network-segments).

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Új ExpressRoute virtuális hálózati átjáró létrehozása

*Új* ExpressRoute virtuális hálózati átjáró létrehozásakor használhat meglévő Azure-Virtual Network, vagy létrehozhat egy újat.  

- Meglévő Azure-beli virtuális hálózat esetén:
   1. Azonosítson egy olyan Azure-beli virtuális hálózatot, amely nem rendelkezik már meglévő ExpressRoute virtuális hálózati átjárókkal.
   2. Az üzembe helyezés előtt hozzon létre egy [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) az Azure Virtual Networkban.

- Egy új Azure-Virtual Network és virtuális hálózati átjáró létrehozásához létre kell hoznia a telepítést a **Virtual Network** listában az **új létrehozása** hivatkozás kiválasztásával.  Fontos, hogy az üzemelő példány előtt definiálja a Címterület és az alhálózatokat, így a telepítés lépéseinek elvégzése után készen áll az adatok megadására.

Az alábbi képen a **saját Felhőbeli** központi telepítés létrehozása képernyő látható a **Virtual Network** mező kiemelésével.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Képernyőkép az Azure VMware megoldás üzembe helyezési képernyőjéről Virtual Network mező kiemelésével.":::

> [!NOTE]
> A helyszíni környezet és az Azure VMware-megoldás a használni vagy létrehozni kívánt virtuális hálózatokat is láthatja, ezért ügyeljen arra, hogy az ebben a virtuális hálózatban használt IP-szegmensek és alhálózatok ne legyenek átfedésben.

## <a name="vmware-hcx-network-segments"></a>VMware HCX hálózati szegmensek

A VMware HCX egy Azure VMware-megoldással csomagolt technológia. A VMware HCX elsődleges használati esetei a munkaterhelések áttelepítése és a vész-helyreállítás. Ha azt tervezi, hogy ezt a lehetőséget választja, érdemes megtervezni a hálózatkezelést most. Ellenkező esetben kihagyhatja és folytathatja a következő lépéssel.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="optional-extend-your-networks"></a>Választható Hálózatok kiterjesztése

Kiterjesztheti a hálózati szegmenseket a helyszínen az Azure VMware megoldásba. Ha kibővíti a hálózati szegmenseket, most azonosítsa ezeket a hálózatokat.  

Az alábbi szempontokat érdemes figyelembe venni:

- Ha a helyi hálózatok kibővítését tervezi, ezeknek a hálózatoknak csatlakozniuk kell egy [vSphere elosztott kapcsolóhoz (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) a helyszíni VMware-környezetben.  
- A [vSphere standard kapcsolón](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) lévő hálózatok nem terjeszthetők ki.

>[!NOTE]
>Ezek a hálózatok a konfiguráció utolsó lépéseként bővíthetők, nem pedig az üzembe helyezés során.
>
## <a name="next-steps"></a>Következő lépések
Most, hogy összegyűjtötte és dokumentálta a szükséges információkat, folytassa a következő szakasszal, hogy létrehozza az Azure VMware-megoldás saját felhőjét.

> [!div class="nextstepaction"]
> [Az Azure VMware Solution üzembe helyezése](deploy-azure-vmware-solution.md)
> 
