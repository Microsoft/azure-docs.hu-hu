---
title: Hálózati architektúra v1
description: App Service környezetek hálózati topológiájának építészeti áttekintése. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701813"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>App Service környezetek hálózati architektúrájának áttekintése
App Service környezetek mindig egy [virtuális hálózat][virtualnetwork] alhálózatán belül jönnek létre – a app Service Environmentban futó alkalmazások képesek kommunikálni az azonos virtuális hálózati topológián belül található privát végpontokkal.  Mivel az ügyfelek lefoglalhatják a virtuális hálózati infrastruktúrájuk egyes részeit, fontos megérteni, hogy milyen típusú hálózati kommunikációs folyamatok történnek egy App Service Environment.

## <a name="general-network-flow"></a>Általános hálózati folyamat
Ha egy App Service Environment (beadási) nyilvános virtuális IP-címet (VIP) használ az alkalmazásokhoz, az összes bejövő forgalom a nyilvános VIP-címen érkezik.  Ez magában foglalja az alkalmazások HTTP-és HTTPS-forgalmát, valamint az FTP, a Távoli hibakeresési funkció és az Azure felügyeleti műveleteinek egyéb forgalmát.  A nyilvános VIP-címen elérhető megadott portok teljes listájáért tekintse meg a bejövő forgalom App Service Environmentra való [szabályozásáról][controllinginboundtraffic] szóló cikket. 

App Service környezetek támogatják az olyan alkalmazások futtatását is, amelyek csak a virtuális hálózat belső címeihez kötődnek, amelyet ILB (belső terheléselosztó) címnek is neveznek.  Az alkalmazások ILB, valamint a HTTP-és HTTPS-forgalom az alkalmazásokhoz és a Távoli hibakeresési hívásokhoz a ILB-címen érhető el.  A ILB-ben a leggyakoribb, az FTP-/FTPS-forgalom a ILB-címen is megérkezik.  Az Azure felügyeleti műveletei azonban továbbra is a 454/455-es portokra áramlanak egy ILB-t támogató bekapcsoló nyilvános VIP-címen.

Az alábbi ábra áttekintést nyújt a különböző bejövő és kimenő hálózati folyamatokról egy olyan App Service Environment, ahol az alkalmazások nyilvános virtuális IP-címhez vannak kötve:

![Általános hálózati folyamatok][GeneralNetworkFlows]

Egy App Service Environment számos privát ügyfél-végponttal kommunikálhat.  A App Service Environment futó alkalmazások például csatlakozhatnak a IaaS virtuális gépeken futó adatbázis-kiszolgáló (k) hoz ugyanazon a virtuális hálózati topológiában.

> [!IMPORTANT]
> A hálózati diagramon a "más számítási erőforrások" a App Service Environment egy másik alhálózatán vannak telepítve. Ahhoz, hogy az erőforrások ugyanabban az alhálózatban legyenek telepítve, a központtal együtt, letiltja a bevezetési kapcsolatot a bevezetéstől az adott erőforrásokhoz (kivéve a konkrét, a belső Telepítsen helyet egy másik alhálózatra (ugyanabban a VNET). Ekkor a App Service Environment csatlakozni tud. Nincs szükség további konfigurálásra.
> 
> 

App Service környezetek is kommunikálnak az SQL DB-vel és az Azure Storage-erőforrásokkal, amelyek a App Service Environment felügyeletéhez és működtetéséhez szükségesek.  A App Service Environment kommunikáló SQL-és tárolási erőforrások némelyike ugyanabban a régióban található, mint a App Service Environment, míg mások távoli Azure-régiókban találhatók.  Ennek eredményeképpen az internet felé irányuló kimenő kapcsolat mindig szükséges ahhoz, hogy egy App Service Environment megfelelően működjön. 

Mivel egy App Service Environment üzembe helyezése egy alhálózaton történik, a hálózati biztonsági csoportok használatával szabályozható az alhálózat bejövő forgalma.  A App Service Environment bejövő forgalmának szabályozásáról a következő [cikkben][controllinginboundtraffic]olvashat bővebben.

A App Service Environment kimenő internetkapcsolatának engedélyezéséről a következő cikkben olvashat az [Express Route][ExpressRoute]használatáról.  A cikkben leírt módszer a helyek közötti kapcsolat és a kényszerített bújtatás használata esetén is érvényes.

## <a name="outbound-network-addresses"></a>Kimenő hálózati címek
Amikor egy App Service Environment kimenő hívásokat végez, az IP-cím mindig a kimenő hívásokhoz van társítva.  A használt IP-cím attól függ, hogy a meghívott végpont a virtuális hálózati topológián belül vagy a virtuális hálózati topológián kívül található-e.

Ha a hívott végpont a virtuális hálózati topológián **kívül esik** , akkor a használt kimenő cím (más néven a kimenő NAT-cím) a app Service ENVIRONMENT nyilvános VIP-je.  Ez a címe a portál felhasználói felületén található App Service Environment a Tulajdonságok panelen.

![Kimenő IP-cím][OutboundIPAddress]

Ez a cím olyan ASE is meghatározható, amelyeknek csak nyilvános VIP-je van, ha létrehoz egy alkalmazást a App Service Environmentban, majd az alkalmazás címén egy *nslookupt* hajt végre. Az eredő IP-cím a nyilvános VIP, valamint a App Service Environment kimenő NAT-címe.

Ha a hívott végpont a virtuális hálózati topológián **belül** van, a hívó alkalmazás kimenő címe az alkalmazást futtató egyedi számítási erőforrás belső IP-címe lesz.  Azonban a virtuális hálózatok belső IP-címeinek nem állandó hozzárendelése az alkalmazásokhoz.  Az alkalmazások áthelyezhetők különböző számítási erőforrások között, és a App Service Environment rendelkezésre álló számítási erőforrások készlete a skálázási műveletek miatt változhat.

Mivel azonban egy App Service Environment mindig egy alhálózaton belül található, garantált, hogy az alkalmazást futtató számítási erőforrások belső IP-címe mindig az alhálózat CIDR esik.  Ennek eredményeképpen, ha részletes ACL-eket vagy hálózati biztonsági csoportokat használ a virtuális hálózaton belüli más végpontokhoz való hozzáférés biztosítására, akkor a App Service Environmentt tartalmazó alhálózat tartományának hozzáférést kell biztosítania.

A következő ábra részletesebben ismerteti ezeket a fogalmakat:

![Kimenő hálózati címek][OutboundNetworkAddresses]

A fenti ábrán:

* Mivel a App Service Environment nyilvános VIP-je 192.23.1.2, ez a kimenő IP-cím, amelyet az "Internet" végpontok hívásakor használ a rendszer.
* A App Service Environment tartalmazó alhálózat CIDR-tartománya 10.0.1.0/26.  Az azonos virtuális hálózati infrastruktúrában található egyéb végpontok az alkalmazásból érkező hívásokat is láthatják, amelyek ettől a címtartományból származnak.

## <a name="calls-between-app-service-environments"></a>Hívások App Service környezetek között
Összetettebb forgatókönyv akkor fordulhat elő, ha több App Service környezetet telepít ugyanabban a virtuális hálózatban, és a kimenő hívásokat az egyik App Service Environment egy másik App Service Environment hajtja végre.  Az ilyen típusú App Service Environment hívásokat "Internet" hívásként is kezeli a rendszer.

Az alábbi ábrán egy olyan többrétegű architektúra látható, amely egy App Service Environment (például "bejárati ajtó" webalkalmazások) alkalmazásokat hív meg egy második App Service Environment (például belső háttérbeli API-alkalmazások, amelyek nem az internetről elérhetők). 

![Hívások App Service környezetek között][CallsBetweenAppServiceEnvironments] 

A fenti példában a "beadási egy" App Service Environment a 192.23.1.2 kimenő IP-címe van.  Ha egy, a App Service Environmenton futó alkalmazás kimenő hívást kezdeményez egy, az ugyanazon a virtuális hálózatban található második App Service Environment ("a" 2. ") futó alkalmazáshoz, a kimenő hívás" Internet "hívásként lesz kezelve.  Ennek eredményeképpen a második App Service Environment érkező hálózati forgalom a 192.23.1.2-ből származóként fog megjelenni (azaz nem az első App Service Environment alhálózati címtartomány).

Bár a különböző App Service környezetek közötti hívások "Internet" hívásként vannak kezelve, ha mindkét App Service környezet ugyanabban az Azure-régióban található, a hálózati forgalom a regionális Azure-hálózaton marad, és nem fog fizikailag átáramlani a nyilvános interneten.  Ennek eredményeképpen hálózati biztonsági csoportot használhat a második App Service Environment alhálózatán, hogy csak az első App Service Environment bejövő hívásait engedélyezze (amelynek kimenő IP-címe 192.23.1.2), így biztosítva a biztonságos kommunikációt a App Service környezetek között.

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A App Service környezetek által használt bejövő portokkal és a bejövő forgalom vezérlésére szolgáló hálózati biztonsági csoportokkal kapcsolatos részletek [itt][controllinginboundtraffic]érhetők el.

Ebben a [cikkben][ExpressRoute]a felhasználó által megadott útvonalak használatával biztosíthatja a kimenő internet-hozzáférést app Service környezetek számára. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

