---
title: Oktatóanyag – VMware HCX üzembe helyezése és konfigurálása
description: Ismerje meg, hogyan helyezhet üzembe és konfigurálhat VMware HCX-megoldást az Azure VMware-megoldás privát felhője számára.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: d4607483e64e6b7daa45f576f70ce3f5eeef65ca
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801111"
---
# <a name="deploy-and-configure-vmware-hcx"></a>A VMware HCX üzembe helyezése és konfigurálása

Ez a cikk bemutatja, hogyan helyezheti üzembe és konfigurálhatja a helyszíni VMware HCX-összekötőt az Azure VMware-megoldás privát felhője számára. A VMware HCX használatával áttelepítheti VMware-alapú számítási feladatait az Azure VMware-megoldásba és más csatlakoztatott webhelyekre különböző áttelepítési típusok használatával. Mivel az Azure VMware-megoldás üzembe helyezi és konfigurálja a HCX Cloud Managert, le kell töltenie, aktiválnia és konfigurálnia kell a HCX-összekötőt a helyszíni VMware Datacenter-ben.

A VMware HCX Advanced Connector előre telepítve van az Azure VMware megoldásban. Legfeljebb három helyszíni kapcsolatot támogat (a helyszínen a felhőben vagy a felhőben). Ha három helynél több kapcsolatra van szüksége, küldjön egy [támogatási kérést](https://portal.azure.com/#create/Microsoft.Support) a [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) bővítmény engedélyezéséhez.  

>[!TIP]
>Bár a VMware-konfiguráció maximális eszköze a helyszíni összekötő és a Cloud Manager között legfeljebb 25 hely párokat ír le, a licencelés ezt háromra korlátozza a HCX Advanced és a 10 for HCX Enterprise Edition esetében.

>[!NOTE]
>A VMware HCX Enterprise előzetes verziójú szolgáltatásként érhető el az Azure VMware megoldásban. Ingyenes, és az előzetes verziójú szolgáltatás használati feltételeinek hatálya alá tartozik. A VMware HCX Enterprise szolgáltatás általánosan elérhetővé tétele után 30 napos értesítést kap, hogy a számlázás át fog térni. Lehetősége van a szolgáltatás kikapcsolására vagy letiltására is. A VMware HCX Enterprise és a VMware HCX Advanced közötti egyszerű visszalépési útvonal nem érhető el. Ha úgy dönt, hogy visszalépést végez, újra kell telepítenie az állásidőt.

Első lépésként tekintse át a [szoftver verziójának követelményeit](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) [, valamint](#before-you-begin)az [előfeltételeket](#prerequisites). 

Ezután végigvezeti az összes szükséges eljáráson:

> [!div class="checklist"]
> * Töltse le a VMware HCX-összekötő PETESEJTJEIt.
> * Telepítse a helyszíni VMware HCX-PETESEJTeket (VMware HCX Connector).
> * Aktiválja a VMware HCX-összekötőt.
> * Párosítsa a helyszíni VMware HCX-összekötőt az Azure VMware-megoldás HCX Cloud Managerrel.
> * Konfigurálja az összekötőt (hálózati profil, számítási profil és szolgáltatás háló).
> * A telepítés befejezéséhez ellenőrizze a berendezés állapotát, és ellenőrizze, hogy az áttelepítés lehetséges-e.

Ha elkészült, kövesse a cikk végén javasolt következő lépéseket.  

## <a name="before-you-begin"></a>Előkészületek

Az üzembe helyezés előkészítése során javasoljuk, hogy tekintse át a következő VMware-dokumentációt:

* [A VMware HCX felhasználói útmutatója](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Virtual Machines áttelepítése VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [A VMware HCX üzembe helyezésével kapcsolatos megfontolások](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware blog Series – felhőalapú áttelepítés](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [A VMware HCX szükséges hálózati portok](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Előfeltételek

Ha a VMware HCX Enterprise-t tervezi használni, győződjön meg arról, hogy az Azure VMware-megoldás támogatási csatornáján keresztül kért aktiválást.


### <a name="on-premises-vsphere-environment"></a>Helyszíni vSphere-környezet

Győződjön meg arról, hogy a helyszíni vSphere-környezet (forrásoldali környezet) megfelel a [minimális követelményeknek](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Hálózat és portok

* Az [Azure ExpressRoute Global REACH](tutorial-expressroute-global-reach-private-cloud.md) a helyszíni és az Azure VMware Solution Private Cloud ExpressRoute-áramkörök között van konfigurálva.

* [Minden szükséges port](https://ports.vmware.com/home/VMware-HCX) nyitva van a helyszíni összetevők és az Azure VMware Solution Private közötti kommunikációhoz.

### <a name="ip-addresses"></a>IP-címek

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>A VMware HCX-összekötő PETESEJTJEInek letöltése

Mielőtt telepítené a virtuális berendezést a helyszíni vCenter, le kell töltenie a VMware HCX-összekötő PETESEJTJEIt.  

1. A Azure Portal válassza ki az Azure VMware-megoldás saját felhőjét. 

1. Válassza a  >  **kapcsolat** kezelése lehetőséget, és válassza a **HCX** lapot az Azure VMware-megoldás HCX-kezelőjének IP-címének azonosításához. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Képernyőkép a VMware HCX IP-címről." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Válassza az  >  **identitás** kezelése lehetőséget. 

   A Private Cloud vCenter és a NSX-T Manager kijelző URL-címei és felhasználói hitelesítő adatai.

   > [!TIP]
   > A vCenter jelszava a saját felhő beállításakor lett meghatározva. Ez ugyanaz a jelszó, amelyet az Azure VMware Solution HCX Managerbe való bejelentkezéshez fog használni. Az új **jelszó létrehozása** lehetőség kiválasztásával új VCENTER és NSX-T jelszavak hozhatók majd fel.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Saját Felhőbeli vCenter és NSX-kezelő URL-címek és hitelesítő adatok megjelenítése." border="true":::


1. Nyisson meg egy böngészőablakot, és jelentkezzen be az Azure VMware Solution HCX Managerbe a `https://x.x.x.9` 443-es porton a **cloudadmin \@ vSphere. local** felhasználói hitelesítő adatokkal

1. Válassza az **adminisztrációs**  >  **rendszerfrissítések** lehetőséget, majd a **kérelem letöltési hivatkozását**.

1. Válassza ki az Ön által választott lehetőséget a VMware HCX-összekötő PETESEJTJEI fájl letöltéséhez.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>A VMware HCX-összekötő PETESEJTJEInek üzembe helyezése a helyszínen

1. A helyszíni vCenter válassza ki azt a OVF- [sablont](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) , amelybe telepíteni szeretné a VMware HCX-összekötőt a helyszíni vCenter. 

   > [!TIP]
   > Válassza ki az előző szakaszban letöltött PETESEJT-fájlt.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Képernyőkép a OVF-sablon tallózásáról." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Válassza ki a nevet és a helyet, majd válasszon ki egy erőforrást vagy fürtöt, ahol a VMware HCX-összekötőt telepíti. Ezután tekintse át a részleteket és a szükséges erőforrásokat, és kattintson a **Tovább gombra**.  

1. Tekintse át a licencfeltételeket. Ha elfogadja, válassza ki a szükséges tárterületet és hálózatot, majd válassza a **tovább** lehetőséget.

1. Válassza a tárterület lehetőséget, majd kattintson a **Tovább gombra**.

1. Válassza ki azt a VMware HCX felügyeleti hálózati szegmenst, amelyet korábban az [IP-címek előfeltételei](#ip-addresses) szakaszban definiált.  Ezután válassza a **tovább** lehetőséget.

1. A **sablon testreszabása** lapon adja meg az összes szükséges információt, majd válassza a **tovább** lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Képernyőkép a sablon testreszabásához szükséges mezőkről." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Ellenőrizze a konfigurációt, majd válassza a **Befejezés** lehetőséget a VMware HCX-összekötő petesejtjeinek telepítéséhez.
   
   > [!IMPORTANT]
   > A virtuális berendezést manuálisan kell bekapcsolni.  A bekapcsolás után várjon 10-15 percet, mielőtt továbblép a következő lépésre.

Az eljárás teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: HCX Appliance üzembe helyezési](https://www.youtube.com/embed/UKmSTYrL6AY) videó. 


## <a name="activate-vmware-hcx"></a>VMware-HCX aktiválása

Miután telepítette a VMware HCX Connector-összekötőt a helyszínen, és elindítja a készüléket, készen áll az aktiválásra. Először is be kell szereznie egy licenckulcs az Azure VMware-megoldás portálján.

1. Az Azure VMware-megoldás portálján lépjen a kapcsolat **kezelése**  >  elemre, válassza a **HCX** lapot, majd kattintson a **Hozzáadás** gombra.

1. A **rendszergazdai** hitelesítő adataival jelentkezzen be a helyszíni VMware HCX Managerbe `https://HCXManagerIP:9443` . 

   > [!TIP]
   > A **rendszergazdai** felhasználói jelszót a VMware HCX Manager petesejtjei fájl telepítése során definiálta.

   > [!IMPORTANT]
   > Ügyeljen arra, hogy tartalmazza a `9443` portszámot a VMware HCX Manager IP-címével.

1. A **Licencelés** mezőben adja meg a **HCX speciális kulcs** kulcsát, és válassza az **aktiválás** lehetőséget.  
   
    > [!NOTE]
    > A VMware HCX-kezelőnek nyitva kell lennie az internet-hozzáféréssel vagy a proxy konfigurálásával.

1. Az **Adatközpont helye mezőben** adja meg a legközelebbi helyet a VMware HCX Manager helyszíni telepítéséhez. Ezután válassza a **Folytatás** elemet.

1. A **rendszer neve** területen módosítsa a nevet, vagy fogadja el az alapértelmezett értéket, és válassza a **Folytatás** lehetőséget.
   
1. Válassza **az igen, a folytatás** lehetőséget.

1. A **vCenter összekapcsolásához** adja meg a vCenter-kiszolgáló teljes tartománynevét vagy IP-címét, valamint a megfelelő hitelesítő adatokat, majd válassza a **Folytatás** lehetőséget.
   
   > [!TIP]
   > A vCenter-kiszolgáló a VMware HCX-összekötő üzembe helyezése az adatközpontban.

1. Az **SSO/PSC konfigurálása** lapon adja meg a platform Services-vezérlő teljes tartománynevét vagy IP-címét, majd válassza a **Folytatás** lehetőséget.
   
   > [!NOTE]
   > Általában ugyanaz, mint az vCenter teljes tartományneve vagy IP-címe.

1. Győződjön meg arról, hogy a megadott adatok helyesek, majd válassza az **Újraindítás** lehetőséget.
    
   > [!NOTE]
   > Az újraindítás után késleltetést tapasztal, mielőtt a rendszer megkéri a következő lépéssel.

A szolgáltatások újraindítása után a megjelenő képernyőn zöld színnel jelenik meg a vCenter. Mind a vCenter, mind az SSO-nak rendelkeznie kell a megfelelő konfigurációs paraméterekkel, amelyeknek meg kell egyezniük az előző képernyővel.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Képernyőfelvétel az irányítópultról zöld vCenter állapottal." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást: aktiválja a HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) videót.

   > [!IMPORTANT]
   > A HCX Advanced vagy a HCX Enterprise használata esetén előfordulhat, hogy telepítenie kell a javítást a VMware [tudásbáziscikk 81558](https://kb.vmware.com/s/article/81558)-es cikkében. 

## <a name="configure-the-vmware-hcx-connector"></a>A VMware HCX-összekötő konfigurálása

Most már készen áll egy hely párosításának hozzáadására, egy hálózati és számítási profil létrehozására, valamint olyan szolgáltatások engedélyezésére, mint például az áttelepítés, a hálózati bővítmény vagy a vész-helyreállítás. 

### <a name="add-a-site-pairing"></a>Hely párosításának hozzáadása

A VMware HCX Cloud Managert az adatközpont VMware HCX-összekötője segítségével csatlakoztathatja vagy párosíthatja az Azure VMware megoldásban. 

1. Jelentkezzen be a helyszíni vCenter, és a Home ( **Kezdőlap**) területen válassza a **HCX** lehetőséget.

1. Az **infrastruktúra** területen válassza a **hely párosítása** elemet, majd válassza a **Kapcsolódás távoli helyhez** lehetőséget (a képernyő közepén). 

1. Adja meg a korábban feljegyzett Cloud Manager URL-címet vagy IP-címet az Azure VMware-megoldás HCX `https://x.x.x.9` , az Azure VMware-megoldás cloudadmin \@ vSphere. local Felhasználónév és a jelszó. Ezután válassza a **Csatlakozás** lehetőséget.

   > [!NOTE]
   > Hely párosításának sikeres létrehozása:
   > * A VMware HCX-összekötőnek képesnek kell lennie arra, hogy a 443-as porton keresztül irányítsa a HCX Cloud Manager IP-címét
   >
   > * Ugyanazt a jelszót használja, amelyet a vCenter való bejelentkezéshez használt. Ezt a jelszót a kezdeti üzembe helyezési képernyőn határozta meg.

   Megjelenik egy képernyő, amelyen látható, hogy a VMware HCX Cloud Manager az Azure VMware megoldásban és a helyszíni VMware HCX-összekötő csatlakoztatva van (párosítva).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Képernyőkép, amely a HCX-kezelő párosítását mutatja be az Azure VMware megoldásban és a VMware HCX-összekötőben.":::

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást: HCX-hely párosítási](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) videó.

### <a name="create-network-profiles"></a>Hálózati profilok létrehozása

A VMware HCX-összekötő a több IP-szegmenst igénylő virtuális berendezések (automatizált) egy részhalmazát telepíti. A hálózati profilok létrehozásakor a [VMware HCX hálózati szegmensek előtti üzembe helyezés előkészítése és tervezési szakasz](production-ready-deployment-steps.md#vmware-hcx-network-segments)során azonosított IP-szegmenseket használhatja.

Négy hálózati profilt fog létrehozni:

   - Kezelés
   - vMotion
   - Replikálás
   - Uplink

1. Az **infrastruktúra** területen válassza az **összekötő**  >  **többhelyes szolgáltatás háló**  >  **hálózati profilok**  >  **hálózati profil létrehozása** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="A hálózati profil létrehozásának megkezdéséhez szükséges beállítások képernyőképe." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Minden hálózati profilnál válassza ki a hálózat és a port csoportot, adjon meg egy nevet, és hozza létre a szegmens IP-készletét. Ezután kattintson a **Létrehozás** elemre. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Képernyőkép az új hálózati profil részleteiről.":::

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldást: HCX Network Profile](https://www.youtube.com/embed/O0rU4jtXUxc) videó.


### <a name="create-a-compute-profile"></a>Számítási profil létrehozása

1. Az **infrastruktúra** területen válassza az **összekötő**  >  **számítási profilok**  >  **create számítási profil létrehozása** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Képernyőkép, amely megjeleníti a számítási profil létrehozásának megkezdéséhez szükséges beállításokat." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Adja meg a profil nevét, és válassza a **Folytatás** lehetőséget.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Képernyőkép, amely a számítási profil nevének és a folytatás gombnak a bejegyzését jeleníti meg." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Válassza ki az engedélyezni kívánt szolgáltatásokat, például az áttelepítést, a hálózati bővítményt vagy a vész-helyreállítást, majd válassza a **Folytatás** lehetőséget.
  
   > [!NOTE]
   > Általában semmi sem változik.

1. A **szolgáltatás erőforrásainak kiválasztása** területen válasszon ki egy vagy több szolgáltatási erőforrást (fürtöket) a kiválasztott VMware HCX-szolgáltatások engedélyezéséhez.  

1. Ha a helyszíni adatközpontban látja a fürtöket, válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="A kiválasztott szolgáltatási erőforrásokat és a folytatás gombot bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Az **adattár kiválasztása** lapon válassza ki az adattár tárolási erőforrását a VMware HCX Interconnect berendezések telepítéséhez. Ezután válassza a **Folytatás** elemet.

   Ha több erőforrás van kiválasztva, a VMware HCX az elsőként kiválasztott erőforrást használja a kapacitás kimerülése előtt.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="A kiválasztott adattárolási erőforrást és a Continue (folytatás) gombot megjelenítő képernyőkép." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. A **felügyeleti hálózati profil kiválasztása** lapon válassza ki az előző lépésekben létrehozott felügyeleti hálózati profilt. Ezután válassza a **Folytatás** elemet.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="A felügyeleti hálózati profil és a Folytatás gomb kijelölését bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. A **kimenő hálózati profil kiválasztása** lapon válassza ki az előző eljárás során létrehozott kimenő hálózati profilt. Ezután válassza a **Folytatás** elemet.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="A kimenő hálózati profil és a Folytatás gomb kijelölését bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. A **VMotion hálózati profil kiválasztása** lapon válassza ki az előző lépések során létrehozott vMotion hálózati profilt. Ezután válassza a **Folytatás** elemet.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="A vMotion hálózati profil és a Folytatás gomb kijelölését bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. A **vSphere-replikációs hálózati profil kiválasztása** lapon válassza ki az előző lépések során létrehozott replikációs hálózati profilt. Ezután válassza a **Folytatás** elemet.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="A replikációs hálózati profil és a Folytatás gomb kijelölését bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. A **hálózati bővítmények számára válassza az elosztott kapcsolók lehetőséget**, válassza ki azokat a kapcsolókat, amelyek az Azure VMware-megoldásba migrálni kívánt virtuális gépeket tartalmazzák egy 2. rétegbeli kiterjesztett hálózaton. Ezután válassza a **Folytatás** elemet.

   > [!NOTE]
   > Ha a 2. rétegbeli kiterjesztett hálózatokon nem telepíti át a virtuális gépeket, akkor kihagyhatja ezt a lépést.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Az elosztott virtuális kapcsolók és a Folytatás gomb kijelölését bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Tekintse át a kapcsolatok szabályait, és válassza a **Folytatás** lehetőséget.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Képernyőkép, amely a kapcsolatok szabályait és a folytatás gombot jeleníti meg." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. A számítási profil létrehozásához válassza a **Befejezés** lehetőséget.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="A számítási profil adatait megjelenítő képernyőkép." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Az eljárás teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: számítási profil](https://www.youtube.com/embed/e02hsChI3b8) videó.

### <a name="create-a-service-mesh"></a>Szolgáltatás hálójának létrehozása

Itt az ideje, hogy konfigurálja a Service meshot a helyszíni és az Azure VMware megoldás privát felhője között.



> [!NOTE]
> A Service Mesh Azure VMware-megoldással való sikeres létrehozásához:
>
> * Az 500/4500-as UDP-portok nyitva vannak a helyszíni VMware HCX-összekötő "kimenő" hálózati profiljának címei és az Azure VMware-megoldás HCX Cloud "kimenő" hálózati profiljának címei között.
>
> * Ügyeljen arra, hogy ellenőrizze a [VMware HCX szükséges portokat](https://ports.vmware.com/home/VMware-HCX).

1. Az **infrastruktúra** területen válassza az **összekötő**  >  **szolgáltatás háló**  >  **Létrehozás szolgáltatás háló létrehozása** lehetőséget.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Képernyőkép a kiválasztásokról a Service Mesh létrehozásának megkezdéséhez." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Tekintse át az előre feltöltött helyeket, majd válassza a **Folytatás** lehetőséget. 

   > [!NOTE]
   > Ha ez az első Service Mesh-konfiguráció, nem kell módosítania ezt a képernyőt.  

1. Válassza ki a forrás-és távoli számítási profilokat a legördülő listából, majd válassza a **Folytatás** lehetőséget.  

   A beállítások határozzák meg azokat az erőforrásokat, amelyeken a virtuális gépek a VMware HCX-szolgáltatásokat használhatják.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="A forrás számítási profil kiválasztását bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="A távoli számítási profil kijelölését bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Tekintse át azokat a szolgáltatásokat, amelyek engedélyezve lesznek, majd válassza a **Folytatás** lehetőséget.  

1. A **speciális konfigurációban – a kimenő hálózati profilok felülbírálása** lehetőségnél válassza a **Folytatás** lehetőséget.  

   A kimenő hálózati profilok csatlakoznak ahhoz a hálózathoz, amelyen keresztül a távoli hely összekötő készülékei elérhetők.  
  
1. A **Speciális konfiguráció-hálózati bővítmények Felskálázása** lapon tekintse át, majd válassza a **Folytatás** lehetőséget. 

   A készülékeken akár nyolc VLAN-ot is megadhat, de egy másik készüléket is telepíthet további nyolc VLAN-ok hozzáadásához. Emellett a további berendezések számára is rendelkeznie kell IP-címmel, és a készüléken egyetlen IP-cím adható meg.  További információ: [VMware HCX-konfigurációs korlátok](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Képernyőkép, amely a VLAN-szám növelésének helyét mutatja." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. A **Speciális konfiguráció-Traffic Engineering** szolgáltatásban tekintse át és végezze el a szükséges módosításokat, majd kattintson a **Continue (folytatás**) gombra.

1. Tekintse át a topológia előzetes verzióját, és válassza a **Folytatás** lehetőséget.

1. Adja meg a szolgáltatás rácsvonalának felhasználóbarát nevét, és válassza a **Befejezés gombot a befejezéshez** .  

1. A központi telepítés figyeléséhez válassza a **feladatok megtekintése** lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="A feladatok megtekintésére szolgáló gombot megjelenítő képernyőkép.":::

   Ha a szolgáltatás hálójának üzembe helyezése sikeresen befejeződött, a szolgáltatások zöldként jelennek meg.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="A szolgáltatásokra vonatkozó zöld kijelzőket bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Ellenőrizze a szolgáltatás rácsvonalának állapotát a készülék állapotának ellenőrzésével. 

1. Válassza az **összekötő**  >  **berendezések** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="A berendezés állapotának ellenőrzéséhez szükséges beállításokat bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Az eljárás teljes körű áttekintéséhez tekintse meg az [Azure VMware-megoldást: Service Mesh](https://www.youtube.com/embed/COY3oIws108) videó.

### <a name="optional-create-a-network-extension"></a>Választható Hálózati bővítmény létrehozása

Ha a helyszíni környezetből származó hálózatokat az Azure VMware megoldásba kívánja terjeszteni, kövesse az alábbi lépéseket:

1. A **szolgáltatások** területen válassza a **hálózati bővítmény**  >  **hálózati bővítmény létrehozása** lehetőséget.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Képernyőkép, amely a hálózati bővítmény létrehozásának megkezdésére szolgáló beállításokat jeleníti meg." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Válassza ki az Azure VMware-megoldásra kiterjeszteni kívánt hálózatokat, majd kattintson a **tovább** gombra.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="A hálózat kijelölését bemutató képernyőkép.":::

1. Adja meg a helyszíni átjáró IP-címét minden kibővíteni kívánt hálózat esetében, majd válassza a **Küldés** lehetőséget. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Az átjáró IP-címének bejegyzését bemutató képernyőkép.":::

   A hálózati bővítmény befejeződése néhány percet vesz igénybe. Ha igen, akkor a bővítmény állapotának változása **befejeződött**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="A bővítmény állapotát bemutató képernyőkép." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Az eljárás végpontok közötti áttekintéséhez tekintse meg az [Azure VMware-megoldás: hálózati bővítmény](https://www.youtube.com/embed/gYR0nftKui0) videóját.


## <a name="next-steps"></a>Következő lépések

Ha a HCX Interconnect alagút állapota **fel** és zöld, a VMware HCX használatával áttelepítheti és védetté teheti az Azure VMware megoldás virtuális gépeket. Az Azure VMware-megoldás támogatja a munkaterhelések áttelepítését (hálózati bővítménnyel vagy anélkül). A számítási feladatokat továbbra is áttelepítheti a vSphere-környezetbe, valamint a hálózatok helyszíni létrehozását és a virtuális gépek központi telepítését a hálózatokra.  

A HCX használatával kapcsolatos további információkért látogasson el a VMware technikai dokumentációra:

* [A VMware HCX dokumentációja](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Virtual Machines áttelepítése VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX szükséges portok](https://ports.vmware.com/home/VMware-HCX)
* [Hozzon létre egy HCX-proxyt, mielőtt jóváhagyja a licenckulcs](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
