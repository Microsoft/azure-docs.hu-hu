---
title: VRealize-műveletek beállítása Azure VMware-megoldáshoz
description: Ismerje meg, hogyan állíthat be vRealize-műveleteket az Azure VMware-megoldás privát felhője számára.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 3d2617f893c34fde6a154f9f93b5bda520c5a91a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738429"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>VRealize-műveletek beállítása Azure VMware-megoldáshoz


a vRealize Operations Manager egy Operations Management platform, amely lehetővé teszi a VMware infrastruktúra-rendszergazdák számára a rendszererőforrások figyelését. Ezek a rendszererőforrások lehetnek alkalmazás-vagy infrastruktúra-szintű (fizikai és virtuális) objektumok. A legtöbb VMware-rendszergazda a vRealize-műveletek használatával figyeli és felügyeli a VMware Private Cloud Components – vCenter, ESXi, NSX-T, vSAN és VMware HCX.  Minden egyes kiépített Azure VMware-megoldás saját vCenter, NSX-T, vSAN és HCX üzembe helyezést tartalmaz. 

Első lépésként alaposan [áttekintheti](#before-you-begin) az [előfeltételeket](#prerequisites) . Ezután végigvezeti a két tipikus üzembe helyezési topológián:

> [!div class="checklist"]
> * [Azure VMware-megoldás üzembe helyezését kezelő helyszíni vRealize-műveletek](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Az Azure VMware-megoldás üzembe helyezésén futó vRealize-műveletek](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Előkészületek
* A vRealize-műveletek telepítésével kapcsolatos további információkért tekintse meg a [vRealize Operations Manager termék dokumentációját](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) . 
* Tekintse át az Azure VMware Solution Software-Defined Datacenter (SDDC) [oktatóanyag-sorozatot](tutorial-network-checklist.md).
* Ha szeretné, tekintse át a [VRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) termékdokumentációt az Azure VMware-megoldás üzembe helyezési lehetőségeit kezelő helyszíni VRealize-műveletekhez. 


## <a name="prerequisites"></a>Előfeltételek
* a [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) telepítve van.
* Egy VPN vagy egy Azure-ExpressRoute, amely a helyszíni és az Azure VMware megoldás SDDC van konfigurálva.
* Azure-beli VMware-megoldás saját felhő üzembe helyezése az Azure-ban.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware-megoldás üzembe helyezését kezelő helyszíni vRealize-műveletek
A legtöbb ügyfél a vRealize-műveletek egy meglévő helyszíni telepítését végzi egy vagy több helyszíni vCenter-tartomány kezeléséhez. Amikor kiépítenek egy Azure VMware-megoldást saját felhőbe, az Azure ExpressRoute vagy egy 3. rétegbeli VPN-megoldás használatával csatlakoznak a helyszíni környezethez a saját felhővel.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware-megoldás üzembe helyezését kezelő helyszíni vRealize-műveletek" border="false":::

A vRealize működési képességeinek az Azure VMware megoldás privát felhőbe való kiterjesztéséhez létre kell hoznia egy adapter- [példányt a saját felhőalapú erőforrásaihoz](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Adatokat gyűjt az Azure VMware-megoldás saját felhőből, és a helyszíni vRealize-műveletekhez. A helyszíni vRealize Operations Manager példány közvetlenül kapcsolódhat a vCenter és a NSX-T kezelőhöz az Azure VMware-megoldáson keresztül. A vRealize Operations Remote Collector üzembe helyezése az Azure VMware megoldás saját felhőben is lehetséges. A gyűjtő tömöríti és titkosítja a titkos felhőből gyűjtött adatokat, mielőtt a ExpressRoute vagy a VPN-hálózaton keresztül elküldi a helyszíni vRealize Operations Manager. 

> [!TIP]
> A vRealize-Operations Manager telepítésének részletes útmutatóját a [VMware dokumentációjában](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) találja. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Az Azure VMware-megoldás üzembe helyezésén futó vRealize-műveletek

Egy másik lehetőség, hogy a vRealize egy példányát Operations Manager egy vSphere-fürtön helyezi üzembe a privát felhőben. 

>[!IMPORTANT]
>A VMware jelenleg nem támogatja ezt a beállítást.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware-megoldáson futó vRealize-műveletek" border="false":::

A példány üzembe helyezését követően konfigurálhatja a vRealize műveleteket a vCenter, ESXi, NSX-T, vSAN és HCX adatok gyűjtéséhez. 



## <a name="known-limitations"></a>Ismert korlátozások

- Az Azure VMware-megoldás **cloudadmin \@ vSphere. local** felhasználója [korlátozott jogosultságokkal](concepts-role-based-access-control.md)rendelkezik.  Az Azure VMware-megoldásban a virtuális gépek (VM-EK) nem támogatják a vendégen belüli memória-gyűjtést a VMware-eszközök használatával.  Ebben az esetben az aktív és a felhasznált memória kihasználtsága továbbra is működik.
- A gazdagép-alapú üzleti célra való munkaterhelés optimalizálása nem működik, mert az Azure VMware-megoldások kezelik a fürt konfigurációit, beleértve a DRS-beállításokat is.
- A fürtön belüli elhelyezés SDDC a fürtökön alapuló üzleti szándék használatával teljes mértékben támogatott a vRealize Operations Manager 8,0-es és újabb verziókban. A számítási feladatok optimalizálása azonban nem ismeri az erőforrás-készleteket, és a virtuális gépeket a fürt szintjén helyezi el. A felhasználók manuálisan is kimódosíthatják azt az Azure VMware-megoldás vCenter Server felületén.
- Az Azure VMware-megoldás vCenter Server hitelesítő adataival nem tud bejelentkezni a vRealize Operations Managerba. 
- Az Azure VMware-megoldás nem támogatja a vRealize Operations Manager beépülő modult.

Ha vCenter Server Cloud-fiókkal csatlakozik az Azure VMware-megoldás vCenter vRealize Operations Manager, akkor a következő figyelmeztetés jelenik meg:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="A figyelmeztetési adapter példányának létrehozása sikerült":::

A figyelmeztetés azért fordul elő, mert az Azure VMware-megoldás **cloudadmin \@ vSphere. local** felhasználója nem rendelkezik megfelelő jogosultsággal a regisztrációhoz szükséges összes vCenter Server művelet elvégzéséhez. A jogosultságok azonban elegendőek ahhoz, hogy az adapter példánya adatgyűjtést hajtson végre, ahogy az alábbi képen is látható:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Az adatgyűjtést végző adapter-példány":::

További információ: vCenter- [adapter példányának konfigurálásához szükséges jogosultságok](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




