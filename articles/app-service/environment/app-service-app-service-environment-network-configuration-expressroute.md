---
title: Az Azure ExpressRoute v1 konfigurálása
description: A PowerApps App Service Environment hálózati konfigurációja az Azure ExpressRoute. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961822"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Hálózati konfiguráció részletei App Service Environment PowerApps az Azure ExpressRoute

Az ügyfelek az Azure [ExpressRoute][ExpressRoute] áramkört a virtuális hálózati infrastruktúrához kapcsolódhatnak a helyszíni hálózat Azure-ra való kiterjesztéséhez. A App Service Environment a [virtuális hálózati][virtualnetwork] infrastruktúra alhálózatában jön létre. A App Service Environmenton futó alkalmazások biztonságos kapcsolatokat létesít a ExpressRoute-kapcsolaton keresztül elérhető háttérbeli erőforrásokhoz.  

App Service Environment a következő forgatókönyvekben hozhatók létre:
- Azure Resource Manager virtuális hálózatok.
- Klasszikus üzembe helyezési modell virtuális hálózatok.
- Nyilvános címtartományt vagy RFC1918 (azaz magánhálózati címet) használó virtuális hálózatok. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Szükséges hálózati kapcsolat

App Service Environment olyan hálózati kapcsolati követelményekkel rendelkezik, amelyek kezdetben nem teljesülnek a ExpressRoute csatlakozó virtuális hálózatokban.

App Service Environment működéséhez a következő hálózati kapcsolati beállítások szükségesek:

* Az Azure Storage-végpontok felé irányuló kimenő hálózati kapcsolat a 80-as és a 443-es porton egyaránt elérhető. Ezek a végpontok ugyanabban a régióban találhatók, mint a App Service Environment és más Azure-régiók is. Az Azure Storage-végpontok a következő DNS-tartományok alatt oldhatók fel: table.core.windows.net, blob.core.windows.net, queue.core.windows.net és file.core.windows.net.  

* Kimenő hálózati kapcsolat a 445-es porton futó Azure Files szolgáltatással.

* Kimenő hálózati kapcsolat Azure SQL Database-végpontokhoz, amelyek ugyanabban a régióban találhatók, mint App Service Environment. SQL Database végpontok a database.windows.net tartomány alatt oldhatók fel, amelyhez nyitott hozzáférés szükséges a 1433, 11000-11999 és 14000-14999 portokhoz. A SQL Database V12-es port használatának részleteit lásd: [1433-nál nagyobb portok a ADO.NET 4,5-hez](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md).

* Kimenő hálózati kapcsolat az Azure Management-Plane-végpontokkal (a klasszikus Azure üzemi modell és a Azure Resource Manager végpontok). Az ezekhez a végpontokhoz való kapcsolódás magában foglalja a management.core.windows.net és a management.azure.com tartományokat. 

* Kimenő hálózati kapcsolat a ocsp.msocsp.com, a mscrl.microsoft.com és a crl.microsoft.com tartományokkal. Ezen tartományokhoz való kapcsolódás szükséges a TLS-funkciók támogatásához.

* A virtuális hálózat DNS-konfigurációjának képesnek kell lennie a cikkben említett összes végpont és tartomány feloldására. Ha a végpontok nem oldhatók fel, App Service Environment a létrehozás sikertelen lesz. Minden meglévő App Service Environment nem megfelelőként van megjelölve.

* A DNS-kiszolgálókkal folytatott kommunikációhoz a 53-es porton kimenő hozzáférés szükséges.

* Ha egy VPN-átjáró másik végén létezik egy egyéni DNS-kiszolgáló, a DNS-kiszolgálónak elérhetőnek kell lennie az App Service Environmentt tartalmazó alhálózatból. 

* A kimenő hálózati elérési út nem tud utazni a belső vállalati proxyn keresztül, és nem kényszeríthető a helyszíni bújtatás. Ezek a műveletek megváltoztatják az App Service Environment kimenő hálózati forgalmának tényleges NAT-címeit. A App Service Environment kimenő hálózati forgalom NAT-címeinek változásai a különböző végpontok csatlakozási hibáit okozzák. A App Service Environment létrehozása sikertelen. Minden meglévő App Service Environment nem megfelelőként van megjelölve.

* A App Service Environment szükséges portok bejövő hálózati hozzáférését engedélyezni kell. Részletekért lásd: a [Bejövő forgalom vezérlése app Service Environmentra][requiredports].

A DNS-követelmények teljesítéséhez győződjön meg arról, hogy érvényes DNS-infrastruktúra van konfigurálva és karbantartva a virtuális hálózat számára. Ha a DNS-konfiguráció App Service Environment létrehozása után módosul, akkor a fejlesztők kényszerítheti App Service Environment az új DNS-konfiguráció felvételére. A működés közbeni környezet újraindítását a [Azure Portal][NewPortal]app Service Environment kezelése elem **Újraindítási** ikonjának használatával aktiválhatja. Az újraindítás hatására a környezet felveszi az új DNS-konfigurációt.

A bejövő hálózati hozzáférési követelmények teljesítéséhez állítson be egy [hálózati biztonsági csoportot (NSG)][NetworkSecurityGroups] a app Service Environment alhálózaton. A NSG lehetővé teszi a szükséges hozzáférés [vezérlését app Service Environment felé irányuló bejövő forgalom szabályozásához][requiredports].

## <a name="outbound-network-connectivity"></a>Kimenő hálózati kapcsolat

Alapértelmezés szerint az újonnan létrehozott ExpressRoute-áramkör egy alapértelmezett útvonalat hirdet meg, amely lehetővé teszi a kimenő internetkapcsolatot. App Service Environment használhatja ezt a konfigurációt más Azure-végpontokhoz való kapcsolódáshoz.

Egy közös ügyfél-konfiguráció a saját alapértelmezett útvonalának (0.0.0.0/0) meghatározása, amely kikényszeríti az internetes forgalmat a helyszíni forgalom felé. Ez a forgalmi folyamat mindig megszakítja App Service Environment. A kimenő forgalom a helyszínen vagy a NAT-on keresztül nem felismerhető olyan címtartomány, amely már nem működik különböző Azure-végpontokkal.

A megoldás egy (vagy több) felhasználó által megadott útvonal (UDR) definiálása a App Service Environmentt tartalmazó alhálózaton. A UDR olyan alhálózat-specifikus útvonalakat határoznak meg, amelyeket az alapértelmezett útvonal helyett tiszteletben tartanak.

Ha lehetséges, használja a következő konfigurációt:

* A ExpressRoute-konfiguráció a 0.0.0.0/0 értéket hirdeti. Alapértelmezés szerint a konfigurációs kényszeríti a helyszíni összes kimenő forgalmat.
* Az App Service Environmentt tartalmazó alhálózatra alkalmazott UDR a következő ugrás típusú internettel rendelkező 0.0.0.0/0 értéket határozza meg. Ennek a konfigurációnak a leírását a cikk későbbi részében találja.

Ennek a konfigurációnak az együttes hatása, hogy az alhálózat szintű UDR elsőbbséget élveznek a ExpressRoute kényszerített bújtatásával szemben. A App Service Environment kimenő internet-hozzáférése garantált.

> [!IMPORTANT]
> A UDR meghatározott útvonalaknak elég egyedinek kell lenniük ahhoz, hogy elsőbbséget élvezzenek a ExpressRoute-konfiguráció által meghirdetett útvonalakkal szemben. A következő szakaszban ismertetett példa a széles 0.0.0.0/0 címtartományt használja. Ez a tartomány véletlenül felülbírálható a több megadott címtartományt használó útválasztási hirdetményekben.
> 
> A App Service Environment nem támogatott olyan ExpressRoute-konfigurációk esetén, amelyek az útvonalakat a nyilvános társítási útvonalról a privát társítási útvonalra hirdetik át. A nyilvános ExpressRoute konfigurált konfigurációk a Microsofttól érkező útválasztási hirdetményeket fogadnak Microsoft Azure IP-címtartományok nagy készlete számára. Ha ezeket a címtartományt a magánhálózati társítási útvonalon keresztül hirdetik, a App Service Environment alhálózat összes kimenő hálózati csomagjai kényszerítve lesznek az ügyfél helyszíni hálózati infrastruktúrájának bújtatására. Ez a hálózati folyamat jelenleg nem támogatott a App Service Environment. Az egyik megoldás az, hogy leállítja a hirdetési útvonalakat a nyilvános társi útvonalról a privát társítási útvonalra.
> 
> 

A felhasználó által megadott útvonalakkal kapcsolatos háttér-információkat lásd: [virtuális hálózati forgalom útválasztása][UDROverview].  

A felhasználó által definiált útvonalak létrehozásával és konfigurálásával kapcsolatos információkért lásd: [hálózati forgalom irányítása útválasztási táblázattal a PowerShell használatával][UDRHowTo].

## <a name="udr-configuration"></a>UDR-konfiguráció

Ez a szakasz a App Service Environment UDR-konfigurációját mutatja be.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a Azure PowerShellt az [Azure letöltések oldaláról][AzureDownloads]. Válasszon egy, a 2015-es vagy újabb verziót tartalmazó letöltést.   >  A legújabb PowerShell-parancsmagok telepítéséhez a parancssori eszközök **Windows PowerShell** területén válassza a **telepítés** lehetőséget.

* Hozzon létre egy egyedi alhálózatot, amely kizárólagos használatra App Service Environment. Az egyedi alhálózat biztosítja, hogy az alhálózatra alkalmazott UDR csak App Service Environment számára legyen nyitott kimenő forgalom.

> [!IMPORTANT]
> A konfigurációs lépések elvégzése után csak App Service Environment telepítése. A lépések végrehajtásával biztosítható, hogy a kimenő hálózati kapcsolat elérhető legyen App Service Environment telepítése előtt.

### <a name="step-1-create-a-route-table"></a>1. lépés: útválasztási táblázat létrehozása

Hozzon létre egy **DirectInternetRouteTable** nevű útválasztási táblázatot az USA nyugati régiója Azure-régióban, az alábbi kódrészletben látható módon:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>2. lépés: útvonalak létrehozása a táblában

A kimenő internet-hozzáférés engedélyezéséhez vegyen fel útvonalakat az útválasztási táblázatba.  

Konfigurálja a kimenő hozzáférést az internethez. Adjon meg egy útvonalat a 0.0.0.0/0 számára az ebben a kódrészletben látható módon:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

a 0.0.0.0/0 egy széles címtartomány. A tartományt a ExpressRoute által hirdetett címtartomány felülbírálja. A 0.0.0.0/0 útvonalú UDR egy olyan ExpressRoute-konfigurációval együtt kell használni, amely csak a 0.0.0.0/0 értéket hirdeti meg. 

Alternatív megoldásként töltse le az Azure által használt CIDR-tartományok aktuális, átfogó listáját. Az összes Azure IP-címtartomány XML-fájlja elérhető a [Microsoft letöltőközpontból][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Az Azure IP-címtartományok az idő múlásával változnak. A felhasználó által definiált útvonalaknak rendszeres manuális frissítésekre van szükségük, hogy szinkronban maradjanak.
>
> Egyetlen UDR alapértelmezett felső korlátja 100 útvonal. Az 100-Route korláton belül az Azure IP-címek tartományait össze kell illeszteni. A UDR megadott útvonalaknak pontosabbnak kell lenniük, mint az ExpressRoute-kapcsolatok által meghirdetett útvonalaknál.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>3. lépés: a tábla hozzárendelése az alhálózathoz

Rendelje hozzá az útválasztási táblázatot arra az alhálózatra, ahová App Service Environment szeretné telepíteni. Ez a parancs a **DirectInternetRouteTable** táblát társítja a **ASESubnet** alhálózathoz, amely tartalmazni fogja a app Service Environment.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>4. lépés: az útvonal tesztelése és megerősítése

Miután az útválasztási táblázat az alhálózathoz van kötve, tesztelje és erősítse meg az útvonalat.

Helyezzen üzembe egy virtuális gépet az alhálózaton, és erősítse meg a következő feltételeket:

* Az ebben a cikkben ismertetett Azure-és nem Azure-végpontokra irányuló kimenő forgalom **nem** halad le a ExpressRoute áramkörön. Ha az alhálózatról érkező kimenő forgalom kényszerített bújtatást végez a helyszínen, App Service Environment a létrehozás mindig sikertelen lesz.
* Az ebben a cikkben ismertetett végpontok DNS-keresései mindegyike megfelelően oldódik meg. 

Miután elvégezte a konfigurációs lépéseket, és erősítse meg az útvonalat, törölje a virtuális gépet. App Service Environment létrehozásakor az alhálózatnak "üresnek" kell lennie.

Most már készen áll a App Service Environment üzembe helyezésére!

## <a name="next-steps"></a>Következő lépések

A PowerApps App Service Environment használatának megkezdéséhez tekintse meg a [app Service Environment bemutatása][IntroToAppServiceEnvironment]című témakört.

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->