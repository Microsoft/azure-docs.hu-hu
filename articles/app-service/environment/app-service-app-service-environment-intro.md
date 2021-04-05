---
title: Bevezetés a bevezető v1-be
description: A App Service Environment v1 funkcióinak megismerése. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008624"
---
# <a name="introduction-to-app-service-environment-v1"></a>A App Service Environment v1 bemutatása

> [!NOTE]
> Ez a cikk a App Service Environment v1-es verzióról szól.  A App Service Environment újabb verziója könnyebben használható, és nagyobb teljesítményű infrastruktúrán fut. Ha többet szeretne megtudni az új verzióról, kezdje a [app Service Environment bevezetésével](intro.md).

## <a name="overview"></a>Áttekintés

Az App Service Environment egy [prémium][PremiumTier] szintű [Azure app Service](../overview.md) szolgáltatáscsomag, amely teljesen elkülönített és dedikált környezetet biztosít a Azure app Service alkalmazások biztonságos, nagy léptékű futtatásához, például Web Apps, Mobile apps és API apps számára.  

A App Service környezetek ideálisak az alkalmazások számítási feladataihoz:

* Nagyon nagy léptékű
* Elkülönítés és biztonságos hálózati hozzáférés

Az ügyfelek több App Service környezetet hozhatnak létre egyetlen Azure-régióban, valamint több Azure-régión belül is.  Így App Service környezetek ideálisak a magas RPS-munkaterheléseket támogató, állapot nélküli alkalmazási rétegek horizontális skálázásához.

App Service környezetek el vannak különítve, hogy csak egyetlen ügyfél alkalmazásait futtassák, és mindig virtuális hálózatba vannak telepítve.  Az ügyfelek részletesen szabályozzák mind a bejövő, mind a kimenő alkalmazások hálózati forgalmát, és az alkalmazások nagy sebességű biztonságos kapcsolatokat hozhatnak létre a virtuális hálózatokon a helyszíni vállalati erőforrásokhoz.

Annak áttekintéséhez, hogy App Service környezetek hogyan teszik lehetővé a nagy léptékű és biztonságos hálózati hozzáférést, tekintse meg a [AzureCon][AzureConDeepDive] részletes ismertetése app Service környezetekben!

Ha horizontális skálázást szeretne több App Service környezet használatával, tekintse meg a [földrajzilag elosztott alkalmazás-lábnyom][GeodistributedAppFootprint]beállításával kapcsolatos cikket.

Ha szeretné megtudni, hogyan konfigurálta a AzureCon Deep Diveban bemutatott biztonsági architektúrát, tekintse meg a [többrétegű biztonsági architektúra](app-service-app-service-environment-layered-security.md) app Service környezetekkel való megvalósításával foglalkozó cikket.

A App Service környezetekben futó alkalmazások a felsőbb rétegbeli eszközök, például a webalkalmazási tűzfalak (WAF-EK) számára is elérhetők.  A [WAF app Service környezetek számára történő konfigurálásáról](app-service-app-service-environment-web-application-firewall.md) szóló cikk ismerteti ezt a forgatókönyvet.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikált számítási erőforrások

Egy App Service Environment összes számítási erőforrása kizárólag egyetlen előfizetéshez van hozzárendelve, és egy App Service Environment akár 50 (50) számítási erőforrással is konfigurálható egyetlen alkalmazás kizárólagos használatára.

Az App Service Environment egy előtér-számítási erőforráskészletből, valamint egy-három feldolgozó számítási erőforrás-készletből áll.

Az előtér-készlet a TLS-leállításért felelős számítási erőforrásokat, valamint az alkalmazások kéréseinek automatikus terheléselosztását is tartalmazza egy App Service Environmenton belül.

Minden munkavégző készlet [app Service csomagokhoz][AppServicePlan]lefoglalt számítási erőforrásokat tartalmaz, amelyek pedig egy vagy több Azure app Service alkalmazást tartalmaznak.  Mivel egy App Service Environment legfeljebb három munkavégző készlettel rendelkezhet, rugalmasan választhat különböző számítási erőforrásokat az egyes munkavégző készletekhez.  

Ez lehetővé teszi például, hogy egy munkavégző készletet hozzon létre, amely kevésbé hatékony számítási erőforrásokkal rendelkezik a fejlesztési vagy tesztelési célokra szánt App Service tervekhez.  Egy második (vagy akár harmadik) munkavégző készlet nagyobb teljesítményű számítási erőforrásokat is használhat, amelyeket éles alkalmazások futtatásához App Service terveztek.

Az előtér-és a feldolgozó készletek számára elérhető számítási erőforrások mennyiségével kapcsolatos további részletekért lásd: [app Service Environment konfigurálása][HowToConfigureanAppServiceEnvironment].  

Az App Service Environment által támogatott számítási erőforrások méretével kapcsolatos részletekért tekintse meg a [app Service díjszabási][AppServicePricing] oldalát, és tekintse át a prémium szintű árképzési szinten app Service környezetekhez elérhető lehetőségeket.

## <a name="virtual-network-support"></a>Virtual Network támogatás

App Service Environment hozható létre Azure Resource Manager virtuális hálózaton **vagy** **egy klasszikus** üzemi modell virtuális hálózatban ([További információ a virtuális hálózatokról][MoreInfoOnVirtualNetworks]).  Mivel egy App Service Environment mindig létezik egy virtuális hálózatban, és pontosabban a virtuális hálózat alhálózatán belül, a virtuális hálózatok biztonsági funkcióit kihasználva vezérelheti a bejövő és a kimenő hálózati kommunikációt is.  

Egy App Service Environment lehet egy nyilvános IP-címmel vagy egy belső, csak az Azure belső Load Balancer (ILB) címmel rendelkező internetkapcsolat.

[Hálózati biztonsági csoportok][NetworkSecurityGroups] használatával korlátozhatja a bejövő hálózati kommunikációt arra az alhálózatra, ahol a app Service Environment található.  Ez lehetővé teszi az alkalmazások futtatását a felsőbb rétegbeli eszközök és szolgáltatások, például a webalkalmazási tűzfalak és a hálózati SaaS-szolgáltatók mögött.

Az alkalmazásoknak gyakran kell hozzáférniük vállalati erőforrásokhoz, például belső adatbázisokhoz vagy webes szolgáltatásokhoz.  Gyakori megközelítés, hogy ezeket a végpontokat csak az Azure-beli virtuális hálózaton belüli belső hálózati forgalom számára tegye elérhetővé.  Miután egy App Service Environment ugyanahhoz a virtuális hálózathoz csatlakozik, mint a belső szolgáltatások, a környezetben futó alkalmazások is hozzáférhetnek, beleértve a [helyek közötti és az][SiteToSite] [Azure ExpressRoute][ExpressRoute] -kapcsolatokon keresztül elérhető végpontokat is.

A virtuális hálózatokkal és a helyszíni hálózatokkal App Service környezetek működésével kapcsolatos további információkért lásd a [hálózati architektúrával][NetworkArchitectureOverview], a [Bejövő forgalom szabályozásával][ControllingInboundTraffic]és a [háttérrendszer biztonságos csatlakoztatásával][SecurelyConnectingToBackends]kapcsolatos alábbi cikkeket. 

## <a name="getting-started"></a>Első lépések

App Service környezetek megismeréséhez tekintse meg a [app Service Environment létrehozása][HowToCreateAnAppServiceEnvironment] című témakört.

Az App Service Environment hálózati architektúra áttekintését a [hálózati architektúra áttekintése][NetworkArchitectureOverview] című cikkben találja.

A ExpressRoute-vel való App Service Environment használatáról a következő cikkben olvashat az [expressz útvonalon és a app Service környezetekben][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->