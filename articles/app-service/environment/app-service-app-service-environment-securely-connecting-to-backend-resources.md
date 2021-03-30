---
title: Kapcsolódás a back end V1-hez
description: További információ a háttérbeli erőforrásokhoz való biztonságos kapcsolódásról egy App Service Environment. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961805"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Biztonságos kapcsolódás App Service környezetből a végponti erőforrásokhoz
Mivel a App Service Environment **mindig egy Azure Resource Manager** virtuális hálózatban, **vagy** egy klasszikus üzemi modell [virtuális hálózatban][virtualnetwork]jön létre, a app Service Environmentból más háttér-erőforrások felé irányuló kimenő kapcsolatok kizárólag a virtuális hálózaton keresztül áramlanak. Június 2016-én a ASE olyan virtuális hálózatokon is üzembe helyezhetők, amelyek nyilvános címtartományt vagy RFC1918 (magánhálózati címeket) használnak.  

Előfordulhat például, hogy egy olyan SQL Server fut a virtuális gépek fürtjén, amelyen a 1433-es port le van zárva.  Előfordulhat, hogy a végpont ACLd csak ugyanazon a virtuális hálózaton lévő más erőforrásokból való hozzáférést engedélyezi.  

Egy másik példa, hogy a bizalmas végpontok a helyszínen futnak, és a [helyek közötti][SiteToSite] vagy az [Azure ExpressRoute][ExpressRoute] -kapcsolatokon keresztül csatlakoznak az Azure-hoz.  Ennek eredményeképpen csak a helyek közötti vagy ExpressRoute-alagutakhoz csatlakozó virtuális hálózatok erőforrásai férhetnek hozzá a helyszíni végpontokhoz.

Ezen forgatókönyvek esetében a App Service Environment futó alkalmazások biztonságosan kapcsolódhatnak a különböző kiszolgálókhoz és erőforrásokhoz. Ha az alkalmazásokból érkező kimenő forgalom egy App Service Environment az ugyanazon a virtuális hálózaton lévő privát végpontokra, vagy ugyanahhoz a virtuális hálózathoz csatlakozik, akkor csak a virtuális hálózatra lesz átirányítva.  A privát végpontok felé irányuló kimenő forgalom nem a nyilvános interneten keresztül áramlik.

Az egyik probléma a App Service Environment és a virtuális hálózaton belüli végpontok közötti kimenő forgalomra vonatkozik. App Service környezetek nem tudják elérni a virtuális gépek olyan végpontjait, amelyek **ugyanabban** az alhálózatban találhatók, mint a app Service Environment. Ez a korlátozás általában nem jelent problémát, ha App Service környezeteket olyan alhálózatba helyezik, amely kizárólag a App Service Environment általi használatra van fenntartva.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Ahhoz, hogy egy App Service Environment megfelelően működjön, a különböző végpontokhoz kimenő hozzáférést igényel. A szolgáltató által használt külső végpontok teljes listája a [ExpressRoute hálózati konfigurációjának](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) "szükséges hálózati kapcsolat" szakaszában található.

App Service környezetekhez a virtuális hálózathoz konfigurált érvényes DNS-infrastruktúra szükséges.  Ha a DNS-konfiguráció egy App Service Environment létrehozása után módosul, akkor a fejlesztők az új DNS-konfiguráció kiválasztásához kényszerítheti az App Service Environment. A portál App Service Environment felügyelet paneljének tetején válassza az **Újraindítás** ikont a működés közbeni környezet újraindításának elindításához, ami azt eredményezi, hogy a környezet felveszi az új DNS-konfigurációt.

Azt is javasoljuk, hogy a vnet lévő egyéni DNS-kiszolgálókat a App Service Environment létrehozása előtt előre be kell állítani.  Ha a virtuális hálózat DNS-konfigurációja egy App Service Environment létrehozása során módosul, akkor a App Service Environment létrehozási folyamata sikertelen lesz. A VPN-átjáró másik végén, ha van olyan egyéni DNS-kiszolgáló, amely nem érhető el vagy nem érhető el, a App Service Environment létrehozási folyamata is sikertelen lesz.

## <a name="connecting-to-a-sql-server"></a>Csatlakozás egy SQL Serverhoz
Egy közös SQL Server konfiguráció egy végpontot figyel a 1433-as porton:

![SQL Server végpont][SqlServerEndpoint]

A végpontra irányuló forgalom korlátozásának két módja van:

* [Hálózati Access Control listák][NetworkAccessControlLists] (hálózati ACL-ek)
* [Network Security Groups (Hálózati biztonsági csoportok)][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Hozzáférés korlátozása hálózati ACL-sel
A 1433-es portot hálózati hozzáférés-vezérlési lista használatával lehet biztosítani.  Az alábbi példa hozzáadja a hozzárendelési engedélyeket a virtuális hálózatról származó ügyfelek címeihez, és blokkolja az összes többi ügyfél hozzáférését.

![Példa hálózati Access Control listára][NetworkAccessControlListExample]

A App Service Environmentban futó összes alkalmazás, amely ugyanabban a virtuális hálózaton fut, mint a SQL Server, csatlakozhat az SQL Server-példányhoz. Használja a SQL Server virtuális gép **VNet belső** IP-címét.  

Az alábbi példában szereplő kapcsolati sztring a saját magánhálózati IP-címére hivatkozik a SQL Server.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Bár a virtuális gép nyilvános végponttal is rendelkezik, a hálózati ACL miatt a rendszer a nyilvános IP-cím használatára irányuló csatlakozási kísérleteket is elutasítja. 

## <a name="restricting-access-with-a-network-security-group"></a>Hozzáférés korlátozása hálózati biztonsági csoporttal
A hozzáférés biztonságossá tételének másik módja egy hálózati biztonsági csoport.  A hálózati biztonsági csoportok az egyes virtuális gépekre, illetve a virtuális gépeket tartalmazó alhálózatra is alkalmazhatók.

Először létre kell hoznia egy hálózati biztonsági csoportot:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

A csak a belső forgalomhoz való hozzáférés korlátozása egy hálózati biztonsági csoporttal egyszerű VNet.  A hálózati biztonsági csoport alapértelmezett szabályai csak az azonos virtuális hálózatban lévő más hálózati ügyfelek hozzáférését teszik lehetővé.

Ennek eredményeképpen a SQL Serverhoz való hozzáférés zárolása egyszerű. Csak egy hálózati biztonsági csoportot kell alkalmaznia az alapértelmezett szabályokkal a SQL Server rendszert futtató virtuális gépekre vagy a virtuális gépeket tartalmazó alhálózatra.

Az alábbi minta egy hálózati biztonsági csoportot alkalmaz az azt tartalmazó alhálózatra:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Az utolsó eredmény olyan biztonsági szabályok összessége, amelyek blokkolják a külső hozzáférést, miközben lehetővé teszik a VNet belső hozzáférését:

![Alapértelmezett hálózati biztonsági szabályok][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Első lépések
App Service környezetek használatának megkezdéséhez tekintse [meg a app Service Environment bemutatása][IntroToAppServiceEnvironment] című témakört.

A App Service Environment bejövő forgalmának szabályozásával kapcsolatban lásd: a [Bejövő forgalom vezérlése egy app Service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png