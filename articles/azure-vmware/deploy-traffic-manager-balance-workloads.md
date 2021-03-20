---
title: Traffic Manager üzembe helyezése az Azure VMware-megoldás számítási feladatainak elosztásához
description: Ismerje meg, hogyan integrálhatja a Traffic Managert az Azure VMware-megoldással az alkalmazások számítási feladatainak több különböző régióban lévő végponton való kiegyensúlyozásához.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988588"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Traffic Manager üzembe helyezése az Azure VMware-megoldás számítási feladatainak elosztásához

Ez a cikk végigvezeti az [azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) Azure VMware-megoldással való integrálásának lépésein. Az integráció kiegyenlíti az alkalmazások számítási feladatait több végpont között. Ez a cikk azt is bemutatja, hogyan konfigurálhatja a Traffic Managert úgy, hogy a három [Azure-Application Gateway](../application-gateway/overview.md) között átirányítsa a forgalmat, több Azure VMware-megoldási régióban. 

Az átjárók a háttérbeli készlet tagjaiként konfigurált Azure VMware megoldás virtuális gépekkel rendelkeznek a bejövő 7. rétegbeli kérések terheléselosztásához. További információkért lásd: az [azure Application Gateway használata a webalkalmazások Azure VMware-megoldásban való védelemmel való](protect-azure-vmware-solution-with-application-gateway.md) ellátásához

A diagram bemutatja, hogyan biztosítja a Traffic Manager a DNS-szinten lévő alkalmazások terheléselosztását a regionális végpontok között. Az átjárók a háttérbeli készlet tagjai IIS-kiszolgálóként vannak konfigurálva, és az Azure VMware megoldás külső végpontjaiként hivatkoznak rájuk. A két saját felhőalapú régió között a virtuális hálózaton keresztüli kapcsolat egy ExpressRoute-átjárót használ.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Az Azure VMware-megoldással való Traffic Manager integráció architektúrájának ábrája" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Mielőtt elkezdené, tekintse át az [előfeltételeket](#prerequisites) , és végezze el a következő lépéseket:

> [!div class="checklist"]
> * Az Application Gateway és a NSX-T szegmens konfigurációjának ellenőrzése
> * A Traffic Manager profil létrehozása
> * Külső végpontok hozzáadása a Traffic Manager profilhoz

## <a name="prerequisites"></a>Előfeltételek

- Három virtuális gép, amely különböző Azure VMware-megoldási régiókban futtatott Microsoft IIS-kiszolgálóként van konfigurálva: 
   - USA nyugati régiója
   - Nyugat-Európa
   - USA keleti régiója (helyszíni) 

- Egy Application Gateway külső végpontokkal a fentiekben említett Azure VMware megoldás-régiókban.

- Az internetes kapcsolattal rendelkező gazdagép ellenőrzése. 

- Az [Azure VMware megoldásban létrehozott NSX-T hálózati szegmens](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Az Application Gateway-konfiguráció ellenőrzése

A következő lépésekkel ellenőrizheti az Application Gateway-átjárók konfigurációját.

1. A Azure Portal az **Application Gateways (alkalmazás-átjárók** ) elemre kattintva megtekintheti az aktuális Application Gateway-átjárók listáját:

   - AVS-GW-WUS
   - AVS-GW-EUS (helyszíni)
   - AVS-GW-NYEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Képernyőkép az Application Gateway oldalról, amely a konfigurált Application Gateway-átjárók listáját jeleníti meg." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Válassza ki a korábban telepített Application Gateway-átjárók egyikét. 

   Megnyílik egy ablak, amely az Application Gateway különböző információit jeleníti meg. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Képernyőkép az Application Gateway oldalról, amely a kiválasztott Application Gateway részleteit jeleníti meg." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Válassza ki a **háttér-készletek** lehetőséget a háttérbeli készletek egyikének a konfigurációjának ellenőrzéséhez. Egy, a 172.29.1.10 IP-címét tartalmazó webkiszolgálóként konfigurált virtuálisgép-háttérbeli készlet tagja jelenik meg.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Képernyőkép a háttérbeli készlet szerkesztése oldalról a cél IP-címmel kiemelve.":::

1. Ellenőrizze a többi Application Gateway-és háttér-készlet tagjainak konfigurációját. 

## <a name="verify-the-nsx-t-segment-configuration"></a>A NSX-T szegmens konfigurációjának ellenőrzése

A következő lépésekkel ellenőrizheti a NSX-T szegmens konfigurációját az Azure VMware-megoldás környezetében.

1. Válassza ki a **szegmenseket** a konfigurált szegmensek megtekintéséhez.  Láthatja, hogy a contoso-segment1 csatlakozik a contoso-T01 átjáróhoz, amely egy 1. rétegbeli rugalmas útválasztó.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="A NSX-T Manager szegmens profiljait ábrázoló képernyőkép." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Válassza ki az **1. rétegbeli átjárókat** a csatolt szegmensek számával rendelkező 1. szintű átjárók listájának megtekintéséhez. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="A kiválasztott szegmens átjárójának címeit bemutató képernyőkép.":::    

1. Válassza ki a contoso-T01 társított szegmenst. Megnyílik egy ablak, amely a réteg-01 útválasztón konfigurált logikai felületet mutatja. Átjáróként szolgál a szegmenshez csatlakoztatott háttérbeli készlet tagja virtuális géphez.

1. A vSphere-ügyfélen válassza ki a virtuális gépet a részletek megtekintéséhez. 

   >[!NOTE]
   >Az IP-címe megegyezik a virtuális gép backend-készletének azon tagjával, amely webkiszolgálóként van konfigurálva az előző szakaszból: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Képernyőfelvétel: a virtuális gép részletei a vSphere-ügyfélen." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Válassza ki a virtuális gépet, majd válassza a **műveletek > a beállítások szerkesztése** lehetőséget a NSX-T szegmenshez való kapcsolódás ellenőrzéséhez.

## <a name="create-your-traffic-manager-profile"></a>A Traffic Manager profil létrehozása

1. Jelentkezzen be az [Azure Portalra](https://rc.portal.azure.com/#home). Az **Azure-szolgáltatások > hálózatkezelés** területen válassza a **Traffic Manager profilok** lehetőséget.

2. Új Traffic Manager-profil létrehozásához válassza a **+ Hozzáadás** lehetőséget.
 
3. Adja meg a következő adatokat, majd válassza a **Létrehozás** lehetőséget:

   - Profilnév
   - Útválasztási módszer ( [súlyozott](../traffic-manager/traffic-manager-routing-methods.md) használata
   - Előfizetés
   - Erőforráscsoport

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Külső végpontok hozzáadása a Traffic Manager profilhoz

1. Válassza ki a Traffic Manager profilt a keresési eredmények ablaktáblán, válassza a **végpontok**, majd a **+ Hozzáadás** lehetőséget.

1. A különböző régiókban található külső végpontok esetében adja meg a szükséges adatokat, majd válassza a **Hozzáadás** lehetőséget: 
   - Típus
   - Name
   - Teljes tartománynév (FQDN) vagy IP-cím
   - Súlyozás (1 súlyozást rendel az egyes végpontokhoz). 

   A létrehozás után mindhárom megjelenik a Traffic Manager profilban. Mindhárom figyelő állapotának **online** állapotúnak kell lennie.

3. Válassza az **Áttekintés** lehetőséget, és másolja az URL-címet a **DNS-név** területen.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Képernyőfelvétel: Traffic Manager végpont áttekintése a DNS-név kiemelésével." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Illessze be a DNS-név URL-címét egy böngészőben. A képernyőképen a Nyugat-európai régió felé irányuló forgalom látható.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Képernyőfelvétel: a böngészőablakban a Nyugat-Európába irányított forgalom látható."::: 

5. Frissítse a böngészőjét. A képernyőképen az USA nyugati régiójában lévő háttérbeli készlet tagjainak egy másik készletére irányítja át a forgalmat.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Képernyőkép – a böngészőablakban az USA nyugati régiójában átirányított forgalom látható."::: 

6. Frissítse újra a böngészőt. A képernyőképen a háttérrendszer-készlet tagjainak végső készletét ábrázoló forgalom látható a helyszínen.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Képernyőkép a böngészőablakban átirányított forgalomról.":::

## <a name="next-steps"></a>Következő lépések

Most, hogy már az Azure Traffic Manager Azure VMware-megoldással való integrálását tárgyalta, érdemes megismernie az alábbiakat:

- [Azure Application Gateway használata az Azure VMware-megoldással](protect-azure-vmware-solution-with-application-gateway.md).
- [Traffic Manager útválasztási metódusok](../traffic-manager/traffic-manager-routing-methods.md).
- [Terheléselosztási szolgáltatások kombinálása az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Traffic Manager teljesítmény mérése](../traffic-manager/traffic-manager-performance-considerations.md).
