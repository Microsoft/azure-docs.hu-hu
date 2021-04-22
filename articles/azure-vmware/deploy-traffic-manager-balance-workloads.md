---
title: Üzembe Traffic Manager a számítási feladatok Azure VMware Solution kiegyensúlyozása érdekében
description: Megtudhatja, hogyan integrálhat Traffic Manager és Azure VMware Solution az alkalmazások számítási feladatainak különböző régiókban lévő végpontok közötti elosztásához.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 029bb9512bd19effd1c7aeb5104c7bb6d7ccdca5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876921"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Üzembe Traffic Manager a számítási feladatok Azure VMware Solution kiegyensúlyozása érdekében

Ez a cikk bemutatja, hogyan [integrálhatja](../traffic-manager/traffic-manager-overview.md) a Azure Traffic Manager a Azure VMware Solution. Az integráció több végpont között egyensúlyja ki az alkalmazás számítási feladatait. Ez a cikk azt is bemutatja, hogyan konfigurálhatja [](../application-gateway/overview.md) úgy a Traffic Manager, hogy a forgalmat három Azure Application Gateway több régióban Azure VMware Solution iránya. 

Az átjárók Azure VMware Solution virtuális gépeket (VM-eket) konfiguráltak háttérkészlettagokként a bejövő 7. rétegbe érkező kérések terheléselosztására. További információ: [Azure Application Gateway webalkalmazások](protect-azure-vmware-solution-with-application-gateway.md) védelme a Azure VMware Solution

Az ábra bemutatja, Traffic Manager hogyan biztosít terheléselosztást az alkalmazások számára a regionális végpontok közötti DNS-szinten. Az átjárók IIS-kiszolgálóként vannak konfigurálva, és külső végpontokként hivatkoznak Azure VMware Solution háttérkészlet tagjaira. A két magánfelhő-régió közötti virtuális hálózaton keresztüli kapcsolat ExpressRoute-átjárót használ.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Architektúradiagram a Traffic Manager integrációja Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Mielőtt hozzákezd, először tekintse át az [előfeltételeket,](#prerequisites) majd végigveszi az eljárásokat a következő műveleteken:

> [!div class="checklist"]
> * Az Alkalmazásátjárók és az NSX-T szegmens konfigurációjának ellenőrzése
> * Saját Traffic Manager létrehozása
> * Külső végpontok hozzáadása a Traffic Manager profilhoz

## <a name="prerequisites"></a>Előfeltételek

- Három, különböző régiókban microsoftos IIS-kiszolgálóként konfigurált virtuális Azure VMware Solution: 
   - USA nyugati régiója
   - Nyugat-Európa
   - USA keleti régiója (helyszíni) 

- A fent említett régiókban külső végpontokkal Azure VMware Solution Application Gateway.

- Állomás internetkapcsolattal az ellenőrzéshez. 

- A következőben létrehozott [NSX-T hálózati Azure VMware Solution.](tutorial-nsx-t-network-segment.md)

## <a name="verify-your-application-gateways-configuration"></a>Az Alkalmazásátjárók konfigurációjának ellenőrzése

Az alábbi lépések az Alkalmazásátjárók konfigurációját ellenőrzik.

1. A Azure Portal válassza az **Application Gateways (Alkalmazásátjárók)** lehetőséget az aktuális alkalmazásátjárók listájának megtekintéséhez:

   - AVS-GW-WUS
   - AVS-GW-EUS (helyszíni)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Képernyőkép az Application Gateway oldaláról a konfigurált Alkalmazásátjárók listájával." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Válassza ki az egyik korábban üzembe helyezett alkalmazásátjárót. 

   Megnyílik egy ablak, amely az Application Gateway különböző információit mutatja. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Képernyőkép az Application Gateway oldalról a kiválasztott Application Gateway részleteivel." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Válassza **a Háttérkészletek lehetőséget** az egyik háttérkészlet konfigurációjának ellenőrzéséhez. Egy, a 172.29.1.10 IP-címmel konfigurált virtuálisgép-háttérkészlettag látható webkiszolgálóként.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="A Háttérkészlet szerkesztése lap képernyőképe a kiemelt cél IP-címmel.":::

1. Ellenőrizze a többi alkalmazásátjáró és a háttérkészlet tagjainak konfigurációját. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Az NSX-T szegmenskonfiguráció ellenőrzése

A következő lépések az NSX-T szegmens konfigurációját ellenőrzik a Azure VMware Solution környezetben.

1. Válassza **a Szegmensek** lehetőséget a konfigurált szegmensek megtekintéséhez.  Láthatja, hogy a Contoso-segment1 a Contoso-T01 átjáróhoz csatlakozik, amely egy 1. rétegbeli rugalmas útválasztó.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Képernyőkép az alkalmazás szegmensprofiljairól NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Az **1.** rétegbeli átjárók kiválasztásával megjelenik az 1. rétegbeli átjárók listája a csatolt szegmensek számával. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="A kiválasztott szegmens átjárócímét bemutató képernyőkép.":::    

1. Válassza ki a Contoso-T01-hez csatolt szegmenst. Megnyílik egy ablak, amely a Tier-01 útválasztón konfigurált logikai felületet mutatja. Átjáróként szolgál a szegmenshez csatlakoztatott háttérkészlet tag virtuális gépe számára.

1. A vSphere-ügyfélben válassza ki a virtuális gépet a részletek megtekintéséhez. 

   >[!NOTE]
   >Az IP-címe megegyezik az előző szakaszból webkiszolgálóként konfigurált virtuálisgép-háttérkészlet tagjának ip-címével: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Képernyőkép a virtuális gép részleteiről a vSphere-ügyfélben." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Válassza ki a virtuális gépet, majd válassza > **Beállítások** szerkesztése lehetőséget az NSX-T szegmenshez való csatlakozás ellenőrzéséhez.

## <a name="create-your-traffic-manager-profile"></a>Saját Traffic Manager létrehozása

1. Jelentkezzen be az [Azure Portalra](https://rc.portal.azure.com/#home). Az **Azure-szolgáltatások > alatt** válassza a Traffic Manager profilok **lehetőséget.**

2. Új **profil létrehozásához** válassza a + Hozzáadás Traffic Manager lehetőséget.
 
3. Adja meg az alábbi adatokat, majd válassza a **Létrehozás lehetőséget:**

   - Profilnév
   - Útválasztási módszer (súlyozott [használata](../traffic-manager/traffic-manager-routing-methods.md)
   - Előfizetés
   - Erőforráscsoport

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Külső végpontok hozzáadása a Traffic Manager profilhoz

1. Válassza ki Traffic Manager profilt a keresési eredmények panelen, válassza a **Végpontok** lehetőséget, majd **a + Hozzáadás lehetőséget.**

1. A különböző régiókban található külső végpontok mindegyikéhez adja meg a szükséges adatokat, majd válassza a Hozzáadás **lehetőséget:** 
   - Típus
   - Name
   - Teljes tartománynév (FQDN) vagy IP
   - Súlyozás (minden végponthoz 1-es súlyozást rendel). 

   A létrehozás után mindhárom megjelenik a Traffic Manager profilban. Mindhárom monitor állapotának Online állapotúnak **kell lennie.**

3. Válassza **az Áttekintés lehetőséget,** és másolja ki a **DNS-név alatti URL-címet.**

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Képernyőkép a Traffic Manager áttekintésről, kiemelt DNS-névvel." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Illessze be a DNS-név URL-címét egy böngészőbe. A nyugat-európai régióba irányított forgalmat bemutató képernyőkép.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="A nyugat-európai forgalom böngészőablakának képernyőképe."::: 

5. Frissítse a böngészőjét. A képernyőképen a háttérkészlet más tagjaira irányított forgalom látható az USA nyugati régiójában.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Az USA nyugati régiója felé irányt mutató böngészőablak képernyőképe."::: 

6. Frissítse újra a böngészőt. A képernyőképen a háttérkészlet helyszíni tagjainak végső készlete által irányított forgalom látható.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="A helyszínire irányítható forgalmat megjelenítő böngészőablak képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan integrálhatja Azure Traffic Manager a Azure VMware Solution, a következővel ismerkedhet meg:

- [A Azure Application Gateway használata a Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)
- [A Traffic Manager útválasztási módszerei](../traffic-manager/traffic-manager-routing-methods.md)
- [Terheléselosztási szolgáltatások kombinálása az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [A Traffic Manager mérése](../traffic-manager/traffic-manager-performance-considerations.md)
