---
title: WAF konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy webalkalmazási tűzfalat (WAF) a App Service Environment előtt az Azure Application Gateway vagy egy külső gyártótól származó WAF használatával.
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 354568fa3ab3816b643a8f08305ab55868a9b0b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90973708"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Webalkalmazás-tűzfal (WAF) konfigurálása App Service Environment környezetben
## <a name="overview"></a>Áttekintés

A webalkalmazás-tűzfalak (WAF) segítik webalkalmazásai biztonságosabbá tételét azáltal, hogy megvizsgálják a bemenő webes forgalmat, és blokkolják az SQL-injektálásokat, a szkriptet alkalmazó támadásokat, a rosszindulatú feltöltéseket és DDoS-alkalmazásokat és egyéb támadásokat. Ezenkívül megvizsgálják a háttér-webkiszolgálóktól érkező válaszokat is az adatvesztés megelőzése (DLP) érdekében. Az App Service Environment elszigetelésével és további skálázásával kombinálva ideális környezetet nyújt az üzleti szempontból kritikus webalkalmazások tárolására, amelyeknek ellen kell állniuk a kártevő kéréseknek és a nagy forgalomnak. Az Azure az [Application Gateway](../../application-gateway/overview.md) révén WAF képességet is kínál.  Az App Service Environment Application Gatewayjel való integrálásának részleteiért olvassa el az [ILB ASE integrálása egy Application Gatewayjel](./integrate-with-application-gateway.md) témakörről szóló dokumentumot.

Az Azure Application Gatewayen túl több piactéri lehetőség is rendelkezésre áll, mint például a [Barracuda WAF for Azure](https://www.barracuda.com/programs/azure). Ezek az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice)-en érhetők el. A dokumentáció további részre arra fókuszál, hogyan integrálhatja App Service Environment környezetét egy Barracuda WAF-eszközzel.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Telepítés
Ebben a dokumentumban a Barracuda WAF több elosztott terhelésű példánya mögül konfigurálja az App Service Environment környezetet, így csak a WAF-ból érkező forgalom érheti el az App Service Environmentet, a DMZ-ből érkező nem. Ezenkívül Azure Traffic Managert is elhelyezünk a Barracuda WAF példányai előtt, hogy el legyen osztva a terhelés az Azure-adatközpontok és -régiók között. Az összeállítás áttekintő jellegű diagramja az alábbi képen láthatóhoz hasonló:

![A diagram egy opcionális Azure-Traffic Manager, amely a webalkalmazási tűzfal példányaihoz csatlakozik, és A hálózathoz csatlakozik, és csak egy olyan App Service Environment engedélyezi a tűzfalon keresztüli forgalmat, amely egy webes, P I és Mobile alkalmazást tartalmaz két régióban.][Architecture] 

> [!NOTE]
> Az [ILB App Service Environment környezetre irányuló támogatásának](app-service-environment-with-internal-load-balancer.md) bevezetésével konfigurálhatja úgy az ASE-t, hogy az elérhetetlen legyen a DMZ-ről, és csak a privát hálózat számára legyen elérhető. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Az App Service Environment konfigurálása
Egy App Service Environment környezet konfigurálásához tekintse meg az arról készült [dokumentációnkat](app-service-web-how-to-create-an-app-service-environment.md). Ha létrehozta App Service Environment környezetét, létrehozhat ebben a környezetben webalkalmazásokat, API-alkalmazásokat és [mobilalkalmazásokat](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop), amelyek mind védelem alatt fognak állni a következő szakaszban konfigurált WAF mögött.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>A Barracuda WAF Felhőszolgáltatás konfigurálása
A Barracuda [részletes cikkben](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) ismerteti WAF-ja üzembe helyezését egy Azure-beli virtuális gépen. Mivel azonban redundanciát szeretnénk elérni, és nem rendszerkritikus meghibásodási pontot bevezetni, az alábbi utasítások követésekor érdemes legalább két, WAF-példánnyal rendelkező virtuális gépet üzembe helyezni ugyanabban a felhőszolgáltatásban.

### <a name="adding-endpoints-to-cloud-service"></a>Végpontok hozzáadása a felhőszolgáltatásokhoz
Ha 2 vagy annál több WAF VM-példánya van felhőszolgáltatásában, az [Azure Portal](https://portal.azure.com/) használatával hozzáadhat olyan HTTP- vagy HTTPS-végpontokat, amelyeket az alkalmazás használ, ahogy az alábbi képen látható:

![Végpont konfigurálása][ConfigureEndpoint]

Ha az alkalmazásai más végpontokat használnak, azokat is adja hozzá ehhez a listához. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>A Barracuda WAF konfigurálása annak felügyeleti portálján keresztül
A Barracuda WAF a 8000-es TCP-portot használja konfigurálásra felügyeleti portálján keresztül. Ha több WAF VM-példánya is van, minden virtuális gép esetében meg kell ismételni a lépéseket. 

> [!NOTE]
> Ha elkészült a WAF konfigurálásával, távolítsa el a TCP/8000 végpontot mindegyik WAF VM-ről, hogy biztonságos legyen a WAF.
> 
> 

A Barracuda WAF konfigurálásához adja hozzá a felügyeleti végpontot, ahogy az alábbi képen látható.

![Felügyeleti végpont hozzáadása][AddManagementEndpoint]

Egy böngészővel tallózzon felhőszolgáltatása felügyeleti végpontjába. Ha a Felhőszolgáltatás neve test.cloudapp.net, a végpontot a `http://test.cloudapp.net:8000` helyre tallózva érheti el. Az alábbi képen láthatóhoz hasonló bejelentkező oldalt kell látnia, amelyen bejelentkezhet a WAF VM beállítása során megadott bejelentkezési adatokkal.

![Felügyeleti bejelentkezési oldal][ManagementLoginPage]

Ha bejelentkezett, az alábbi képen láthatóhoz hasonló irányítópultot fog látni, amely a WAF-védelem alapvető statisztikáit mutatja be.

![Felügyeleti irányítópult][ManagementDashboard]

A **Szolgáltatások** lapra kattintva konfigurálhatja a WAF-ot azokra a szolgáltatásokra, amelyeket védelmez. A Barracuda WAF további konfigurálásáért lásd a [dokumentációt](https://techlib.barracuda.com/waf/getstarted1). A következő példában egy, a HTTP-n és HTTPS-en forgalmat kiszolgáló App Service alkalmazást konfiguráltunk.

![Felügyelet – Szolgáltatások hozzáadása][ManagementAddServices]

> [!NOTE]
> Attól függően, hogy az alkalmazások hogyan vannak konfigurálva, és milyen funkciókat használ a App Service Environmentban, továbbítania kell a forgalmat a 80-es és 443-es TCP-portok esetében, például ha egy App Service alkalmazáshoz rendelkezik IP TLS-beállítással. Az App Service Environment környezetekben használt hálózati portok listáját a [Bejövő forgalom szabályozása dokumentáció](app-service-app-service-environment-control-inbound-traffic.md) Hálózati portok szakaszában találja.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>A Microsoft Azure Traffic Manager konfigurálása (VÁLASZTHATÓ LEHETŐSÉG)
Ha alkalmazása több régióban is elérhető, akkor érdemes terheléselosztást alkalmazni rájuk az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) mögött. Ehhez hozzáadhat egy végpontot az [Azure Portalon](https://portal.azure.com) a WAF felhőszolgáltatás-nevével a Traffic Managerben, ahogy az alábbi képen is látható. 

![Traffic Manager-végpont][TrafficManagerEndpoint]

Ha az alkalmazás hitelesítést igényel, győződjön meg róla, hogy rendelkezik olyan erőforrásokkal, amelyek nem igényelnek semmilyen hitelesítést a Traffic Managerhez, és amelyek pingelhetik alkalmazása rendelkezésre állását. Az URL-t a **Konfigurálás** lapon, az [Azure Portalon](https://portal.azure.com) konfigurálhatja, ahogy az alábbi képen is látható:

![A Traffic Manager konfigurálása][ConfigureTrafficManager]

A Traffic Manager-pingeknek a WAF-ról az alkalmazásához történő továbbításához be kell állítani a Website Translations szolgáltatást a Barracuda WAF-on, így továbbíthatja a forgalmat az alkalmazásához, ahogy az alábbi példán is látható:

![Website Translations][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Az App Service Environment környezet felé irányuló forgalom biztonságossá tétele hálózati biztonsági csoportok (NSG-k) használatával
Kövesse a [Bejövő forgalom szabályozása dokumentáció](app-service-app-service-environment-control-inbound-traffic.md) utasításait, így megtudhatja, hogyan korlátozza a WAF-ból az App Service Environment környezetébe irányuló forgalmát, csak a felhőszolgáltatás VIP-címét használva. Íme egy PowerShell-parancs a feladat végrehajtásához a 80-es TCP-porton.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

Cserélje le a SourceAddressPrefix részt a WAF felhőszolgáltatásának virtuális IP-címére (VIP-jére).

> [!NOTE]
> A felhőszolgáltatás VIP-je megváltozik, amikor törli és ismét létrehozza a felhőszolgáltatást. Ha ezt elvégzi, ne felejtse el frissíteni az IP-címet a Hálózatierőforrás-csoportban. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png