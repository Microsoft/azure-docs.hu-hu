---
title: Az Azure VMware-megoldás üzembe helyezésének megtervezése
description: Ez a cikk egy Azure VMware-megoldás üzembe helyezési munkafolyamatát ismerteti.  A végeredmény egy olyan környezet, amely készen áll a virtuális gép (VM) létrehozására és áttelepítésére.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f1895f14361b7121ae0d78950cdf8eca3cf7eb52
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462422"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Az Azure VMware-megoldás üzembe helyezésének megtervezése

Ez a cikk az üzembe helyezés során használni kívánt információk azonosításának és gyűjtésének tervezési folyamatát ismerteti. Az üzembe helyezés megtervezése során ügyeljen arra, hogy az üzembe helyezés során könnyen elérhető információkat jegyezze fel.

Az ebben a rövid útmutatóban ismertetett lépések a virtuális gépek (VM-EK) és a Migrálás létrehozására szolgáló, éles környezetben felkészített környezetet biztosítanak. 

>[!IMPORTANT]
>Az Azure VMware-megoldási erőforrás létrehozása előtt kövesse az [Azure VMware megoldás-erőforrás engedélyezése](enable-azure-vmware-solution.md) című cikket egy támogatási jegy elküldéséhez, hogy a gazdagépek le legyenek foglalva. Miután a támogatási csapat megkapja a kérést, akár öt munkanapot is igénybe vesz, hogy erősítse meg kérelmét, és foglalja le a gazdagépeket. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több gazdagépet szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie. 


## <a name="subscription"></a>Előfizetés

Azonosítsa az Azure VMware-megoldás üzembe helyezéséhez használni kívánt előfizetést.  Létrehozhat egy új előfizetést, vagy felhasználhat egy meglévőt.

>[!NOTE]
>Az előfizetést egy Microsoft Nagyvállalati Szerződés vagy egy felhőalapú megoldás-szolgáltató Azure-csomaggal kell társítani. További információ: [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Erőforráscsoport

Azonosítsa az Azure VMware-megoldáshoz használni kívánt erőforráscsoportot.  Általában egy erőforráscsoport jön létre kifejezetten az Azure VMware megoldáshoz, de használhat egy meglévő erőforráscsoportot is.

## <a name="region"></a>Region

Azonosítsa az Azure VMware-megoldás üzembe helyezéséhez használni kívánt régiót.  További információkért tekintse meg az [Azure-termékek régiónként elérhető útmutatót](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Erőforrás neve

Adja meg az üzembe helyezés során használni kívánt erőforrás nevét.  Az erőforrás neve egy felhasználóbarát és leíró név, amelyben az Azure VMware-megoldás saját felhőjét adja meg.

>[!IMPORTANT]
>A név nem lehet hosszabb 40 karakternél. Ha a név túllépi ezt a korlátot, nem fog tudni nyilvános IP-címeket létrehozni a privát felhővel való használatra. 

## <a name="size-hosts"></a>Gazdagépek mérete

Azonosítsa az Azure VMware-megoldás telepítésekor használni kívánt gazdagépek méretét.  A teljes listát az [Azure VMware megoldás privát felhők és fürtök](concepts-private-clouds-clusters.md#hosts) dokumentációjában találja.

## <a name="number-of-clusters-and-hosts"></a>Fürtök és gazdagépek száma

Az Azure VMware megoldásban egy privát felhőt kell üzembe helyeznie, és több fürtöt is létre kell hoznia. Az üzembe helyezéshez meg kell határoznia a fürtök számát és az egyes fürtökben telepíteni kívánt f-állomásokat. A fürtben lévő gazdagépek minimális száma három, a maximális érték pedig 16. A fürtök maximális száma a saját felhőben négy. A privát felhőben a csomópontok maximális száma 64.

További információkért tekintse meg az [Azure VMware Solution Private Cloud és a Clusters](concepts-private-clouds-clusters.md#clusters) dokumentációját.

>[!TIP]
>Később is kiterjesztheti a fürtöt, ha a kezdeti üzembe helyezési számon túl kell lépnie.

## <a name="vcenter-admin-password"></a>vCenter rendszergazdai jelszava
Adja meg a vCenter rendszergazdai jelszavát. Az üzembe helyezés során létre kell hoznia egy vCenter-rendszergazdai jelszót. A cloudadmin@vsphere.local vCenter létrehozása során a rendszer a rendszergazdai fiókhoz rendeli a jelszót. Ezeket a hitelesítő adatokat fogja használni a vCenter való bejelentkezéshez.

## <a name="nsx-t-admin-password"></a>NSX-T rendszergazdai jelszó
Adja meg a NSX-T rendszergazdai jelszót. Az üzembe helyezés során létre fog hozni egy NSX-T rendszergazdai jelszót. A jelszó a NSX-fiókban a rendszergazda felhasználóhoz van hozzárendelve a NSX Build során. Ezeket a hitelesítő adatokat fogja használni a NSX-T kezelőjébe való bejelentkezéshez.

## <a name="ip-address-segment-for-private-cloud-management"></a>IP-cím szegmens a saját felhőalapú felügyelethez

Az üzembe helyezés megtervezésének első lépése az IP-szegmentálás megtervezése. Az Azure VMware-megoldáshoz egy/22 CIDR-hálózat szükséges. Ez a Címterület kisebb hálózati szegmensekre (alhálózatokra), valamint a vCenter, a VMware HCX, a NSX-T és a vMotion funkciókhoz használatos.

Ez a/22 CIDR hálózati címterület nem fedi át a már meglévő hálózati szegmenseket a helyszínen vagy az Azure-ban.

**Példa:** 10.0.0.0/22

Az Azure VMware-megoldás egy belső ExpressRoute-Global Reach áramkörön keresztül kapcsolódik a Microsoft Azure Virtual Networkhoz (D-MSEE a vizualizáció alatt). Ez a funkció az Azure VMware megoldás szolgáltatás részét képezi, és nem számítunk fel díjat.

További információ: [Network Planning ellenőrzőlista](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Azonosítsa az IP-címek szegmensét" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>A virtuális gépek számítási feladataihoz tartozó IP-címek szegmense

Azonosítson egy IP-szegmenst, hogy az első hálózatot létrehozza a saját felhőben lévő számítási feladatok (NSX-szegmens) számára. Más szóval létre kell hoznia egy hálózati szegmenst az Azure VMware megoldásban, hogy üzembe helyezheti a virtuális gépeket az Azure VMware megoldásban.

Még akkor is létre kell hoznia egy hálózati szegmenst, amely ellenőrzi a helyi hálózatokat az Azure VMware-megoldásban (L2).

Ne feledje, hogy a létrehozott IP-szegmenseknek egyedinek kell lenniük az Azure-ban és a helyszíni adatlábnyomban.
  
**Példa:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="A virtuális gépek számítási feladataihoz tartozó IP-címek szegmensének azonosítása" border="false":::     

## <a name="optional-extend-networks"></a>Választható Hálózatok kiterjesztése

Kiterjesztheti a hálózati szegmenseket a helyszíni rendszerből az Azure VMware megoldásba, és ha így tesz, ezeket a hálózatokat is azonosíthatja.  

Ne feledje, hogy:

- Ha a helyi hálózatok kibővítését tervezi, ezeknek a hálózatoknak csatlakozniuk kell egy [vSphere elosztott kapcsolóhoz (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) a helyszíni VMware-környezetben.  
- Ha a hálózat (ok) [vSphere standard kapcsolón](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)szeretné kiterjeszteni az élő hálózatot, akkor nem bővíthetők.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Azure-Virtual Network csatlakoztatása Azure VMware-megoldáshoz

Ebben a lépésben egy ExpressRoute virtuális hálózati átjárót és az Azure VMware Solution ExpressRoute áramkörhöz való kapcsolódáshoz használt támogató Azure-Virtual Network fogja azonosítani.  A ExpressRoute áramkör lehetővé teszi, hogy az Azure VMware-megoldás saját felhőjét más Azure-szolgáltatásokhoz, Azure-erőforrásokhoz és helyszíni környezetekhez lehessen csatlakoztatni.

*Meglévő* vagy *új* ExpressRoute virtuális hálózati átjárót is használhat.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identitás – Azure Virtual Network az Azure VMware-megoldás csatlakoztatásához" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Meglévő ExpressRoute virtuális hálózati átjáró használata

Ha *meglévő* ExpressRoute virtuális hálózati átjárót használ, az Azure VMware megoldás ExpressRoute áramkörét a rendszer a privát felhő üzembe helyezése után hozza meg. Ebben az esetben hagyja üresen a **Virtual Network** mezőt.  

Jegyezze fel, hogy melyik ExpressRoute virtuális hálózati átjárót fogja használni, és folytassa a következő lépéssel.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Új ExpressRoute virtuális hálózati átjáró létrehozása

*Új* ExpressRoute virtuális hálózati átjáró létrehozásakor használhat meglévő Azure-Virtual Network, vagy létrehozhat egy újat.  

- Meglévő Azure-beli virtuális hálózat esetén:
   1. Győződjön meg arról, hogy a virtuális hálózatban nincsenek előre meglévő ExpressRoute virtuális hálózati átjárók. 
   1. Válassza ki a meglévő Azure-Virtual Network a **Virtual Network** listából.

- Új Azure-Virtual Network esetén előre vagy üzembe helyezés közben is létrehozhatja. Válassza az **új létrehozása** hivatkozást a **Virtual Network** listában.

Az alábbi képen látható a **saját Felhőbeli** üzembe helyezési képernyő létrehozása a kiemelt **Virtual Network** mezővel.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Képernyőkép az Azure VMware megoldás üzembe helyezési képernyőjéről Virtual Network mező kiemelésével.":::

>[!NOTE]
>A helyszíni környezet és az Azure VMware-megoldás a használni vagy létrehozni kívánt virtuális hálózatokat is láthatja, ezért ügyeljen arra, hogy az ebben a virtuális hálózatban használt IP-szegmensek és alhálózatok ne legyenek átfedésben.

## <a name="vmware-hcx-network-segments"></a>VMware HCX hálózati szegmensek

A VMware HCX egy olyan technológia, amely az Azure VMware megoldással együtt van kiépítve. A VMware HCX elsődleges használati esetei a munkaterhelések áttelepítése és a vész-helyreállítás. Ha azt tervezi, hogy ezt a lehetőséget választja, érdemes megtervezni a hálózatkezelést most.   Ellenkező esetben kihagyhatja és folytathatja a következő lépéssel.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy összegyűjtötte és dokumentálta a szükséges információkat, folytassa a következő szakasszal, hogy létrehozza az Azure VMware-megoldás saját felhőjét.

> [!div class="nextstepaction"]
> [Az Azure VMware Solution üzembe helyezése](deploy-azure-vmware-solution.md)
