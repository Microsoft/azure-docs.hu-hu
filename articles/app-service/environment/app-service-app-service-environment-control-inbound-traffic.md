---
title: Bejövő forgalom vezérlése v1
description: Megtudhatja, hogyan vezérelheti a bejövő forgalmat egy App Service Environmentra. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962060"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Bejövő forgalom vezérlése App Service Environment
## <a name="overview"></a>Áttekintés
App Service Environment hozható létre Azure Resource Manager virtuális hálózaton **vagy** **egy klasszikus** üzemi modell [virtuális hálózatban][virtualnetwork].  Egy új virtuális hálózat és egy új alhálózat is meghatározható a App Service Environment létrehozásakor. Ehelyett egy App Service Environment hozható létre egy már meglévő virtuális hálózatban és már meglévő alhálózatban is.  Június 2016-én a ASE olyan virtuális hálózatokon is üzembe helyezhetők, amelyek nyilvános címtartományt vagy RFC1918 (magánhálózati címeket) használnak.  További információ: [app Service Environment létrehozása][HowToCreateAnAppServiceEnvironment].

Mindig hozzon létre egy App Service Environment egy alhálózaton belül. Az alhálózatok hálózati határt biztosítanak, amely a felsőbb rétegbeli eszközök és szolgáltatások mögötti bejövő forgalom zárolására használható. Ez a beállítás csak bizonyos felsőbb rétegbeli IP-címeket engedélyez a HTTP-és HTTPS-forgalom fogadásához.

Az alhálózatok bejövő és kimenő hálózati forgalmát egy [hálózati biztonsági csoport][NetworkSecurityGroups]vezérli. A bejövő forgalom vezérléséhez hozzon létre hálózati biztonsági szabályokat egy hálózati biztonsági csoportban. Ezután rendelje hozzá a hálózati biztonsági csoportot a App Service Environment tartalmazó alhálózathoz.

Miután hozzárendelt egy hálózati biztonsági csoportot egy alhálózathoz, a App Service Environmentban lévő alkalmazások bejövő adatforgalmának engedélyezése vagy letiltása a hálózati biztonsági csoportban definiált engedélyezési és megtagadási szabályok alapján engedélyezett vagy letiltott.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Egy App Service Environmentban használt bejövő hálózati portok
Mielőtt zárolja a bejövő hálózati forgalmat egy hálózati biztonsági csoporttal, ismeri a App Service Environment által használt kötelező és választható hálózati portok készletét.  Bizonyos portok forgalmának véletlen bezárása a App Service Environment funkcióinak elvesztését eredményezheti.

Az alábbi lista az App Service Environment által használt portokat tartalmazza. Az összes port a **TCP**, kivéve, ha más egyértelműen feljegyezték:

* 454: az Azure-infrastruktúra által az App Service környezetek TLS-n keresztül történő kezeléséhez és karbantartásához használt  **szükséges port** .  Ne blokkolja a forgalmat erre a portra.  Ez a port mindig a bevezetés nyilvános VIP-címéhez van kötve.
* 455: az Azure-infrastruktúra által az App Service környezetek TLS-n keresztül történő kezeléséhez és karbantartásához használt  **szükséges port** .  Ne blokkolja a forgalmat erre a portra.  Ez a port mindig a bevezetés nyilvános VIP-címéhez van kötve.
* 80: alapértelmezett port a bejövő HTTP-forgalomhoz App Service-csomagokban futó alkalmazásokban egy App Service Environment.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 443: alapértelmezett port a bejövő TLS-forgalomhoz olyan alkalmazásokhoz, amelyek App Service-csomagokban futnak egy App Service Environment.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 21: vezérlési csatorna az FTP-hez.  Ez a port biztonságosan tiltható le, ha az FTP nincs használatban.  A ILB-kompatibilis beadási szolgáltatásban ez a port a ILB-címekhez köthető.
* 990: vezérlési csatorna a FTPS számára.  Ez a port biztonságosan blokkolható, ha a FTPS nincs használatban.  A ILB-kompatibilis beadási szolgáltatásban ez a port a ILB-címekhez köthető.
* 10001-10020: adatcsatornák FTP-hez.  A vezérlési csatornához hasonlóan ezek a portok biztonságosan blokkolva lehetnek, ha az FTP nincs használatban.  A ILB-kompatibilis kiegészítő szolgáltatásban ez a port a beadási ILB-címnek is köthető.
* 4016: a Visual Studio 2012 használatával történő távoli hibakereséshez használatos.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 4018: a Visual Studio 2013 használatával történő távoli hibakereséshez használatos.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 4020: a Visual Studio 2015 használatával történő távoli hibakereséshez használatos.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Ahhoz, hogy egy App Service Environment megfelelően működjön, a különböző végpontokhoz is kimenő hozzáférést igényel. A szolgáltató által használt külső végpontok teljes listája a [ExpressRoute hálózati konfigurációjának](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) "szükséges hálózati kapcsolat" szakaszában található.

App Service környezetekhez a virtuális hálózathoz konfigurált érvényes DNS-infrastruktúra szükséges.  Ha a DNS-konfiguráció egy App Service Environment létrehozása után módosul, akkor a fejlesztők az új DNS-konfiguráció kiválasztásához kényszerítheti az App Service Environment.  Ha az **Újraindítás** ikon használatával indítja el a működés közbeni környezet újraindítását, a környezet felveszi az új DNS-konfigurációt. (Az **Újraindítási** ikon a app Service Environment felügyelet paneljének tetején található, a [Azure Portal][NewPortal].)

Azt is javasoljuk, hogy a vnet lévő egyéni DNS-kiszolgálókat a App Service Environment létrehozása előtt előre be kell állítani.  Ha a virtuális hálózat DNS-konfigurációja megváltozik egy App Service Environment létrehozásakor, a App Service Environment létrehozási folyamata sikertelen lesz.  Hasonlóképpen, ha van olyan egyéni DNS-kiszolgáló, amely egy VPN-átjáró másik végén nem érhető el vagy nem érhető el, akkor a App Service Environment létrehozási folyamata is sikertelen lesz.

## <a name="creating-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
A hálózati biztonsági csoportok működésével kapcsolatos részletes információkért tekintse meg a következő [információkat][NetworkSecurityGroups].  Az alábbi Azure-szolgáltatások kezelési példája a hálózati biztonsági csoportok kiemelését érinti. A példa olyan alhálózatra konfigurálja és alkalmazza a hálózati biztonsági csoportot, amely egy App Service Environment tartalmaz.

**Megjegyzés:** A hálózati biztonsági csoportok a [Azure Portal](https://portal.azure.com) vagy a Azure PowerShell használatával is konfigurálhatók grafikusan.

A hálózati biztonsági csoportok először az előfizetéshez társított önálló entitásként jönnek létre. Mivel a hálózati biztonsági csoportok egy Azure-régióban jönnek létre, hozza létre a hálózati biztonsági csoportot ugyanabban a régióban, mint a App Service Environment.

A következő parancs egy hálózati biztonsági csoport létrehozását mutatja be:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

A hálózati biztonsági csoport létrehozása után a rendszer egy vagy több hálózati biztonsági szabályt ad hozzá.  Mivel a szabályok halmaza idővel változhat, érdemes kizárni a szabály prioritásaihoz használt számozási sémát. Ez a gyakorlat megkönnyíti a további szabályok beszúrását az idő múlásával.

Az alábbi példában egy szabály explicit módon hozzáférést biztosít az Azure-infrastruktúra által a App Service Environment kezeléséhez és karbantartásához szükséges felügyeleti portokhoz.  Az összes felügyeleti forgalom a TLS protokollon keresztül áramlik, és az Ügyféltanúsítványok biztosítják azokat. Bár a portok nyitva vannak, nem érhetők el az Azure felügyeleti infrastruktúráján kívül más entitások.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Ha a 80-es és a 443-as porthoz fér hozzá a "Hide" App Service Environment a felsőbb rétegbeli eszközök vagy szolgáltatások mögött, jegyezze fel a felsőbb rétegbeli IP-címet.  Ha például webalkalmazási tűzfalat (WAF) használ, a WAF saját IP-címe vagy címe lesz. A WAF ezeket használja, amikor egy alsóbb rétegbeli App Service Environmentra irányítja a forgalmat.  Ezt az IP-címet egy hálózati biztonsági szabály *SourceAddressPrefix* paraméterében kell használni.

Az alábbi példában egy adott felsőbb rétegbeli IP-címről érkező bejövő forgalom explicit módon engedélyezett.  A *1.2.3.4* a felsőbb RÉTEGBELI WAF IP-címének helyőrzőjét használja.  Módosítsa az értéket úgy, hogy az megfeleljen a felsőbb rétegbeli eszköz vagy szolgáltatás által használt címnek.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Ha a rendszer FTP-támogatást szeretne használni, használja az alábbi szabályokat sablonként, hogy hozzáférést biztosítson az FTP-vezérlő portjához és az adatcsatorna portjaihoz.  Mivel az FTP állapot-nyilvántartó protokoll, előfordulhat, hogy nem tudja átirányítani az FTP-forgalmat egy hagyományos HTTP/HTTPS-tűzfalon vagy proxy eszközön keresztül.  Ebben az esetben a *SourceAddressPrefix* egy másik értékre kell beállítania, például az FTP-ügyfeleket futtató fejlesztői vagy központi telepítési gépek IP-címeinek tartományát. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**Megjegyzés:**  az adatcsatorna porttartomány változhat az előzetes verzió időtartama alatt.)

Ha a Visual Studióval végzett távoli hibakeresést használ, a következő szabályok bemutatják, hogyan lehet hozzáférést biztosítani.  A Visual Studio minden támogatott verziójához külön szabály van, mivel mindegyik verzió egy másik portot használ a távoli hibakereséshez.  Az FTP-hozzáféréshez hasonlóan előfordulhat, hogy a Távoli hibakeresési forgalom nem működik megfelelően egy hagyományos WAF vagy proxy eszközön.  A *SourceAddressPrefix* Ehelyett a Visual studiót futtató fejlesztői gépek IP-címeire lehet beállítani.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Hálózati biztonsági csoport társítása egy alhálózathoz
A hálózati biztonsági csoport rendelkezik egy alapértelmezett biztonsági szabállyal, amely megtagadja az összes külső forgalom elérését. Ha ezt a szabályt a fenti hálózati biztonsági szabályokkal kombinálja, a rendszer csak az *engedélyezési* művelethez társított forráscím-tartományokból érkező adatforgalmat küldi el a app Service Environmenton futó alkalmazások számára.

A hálózati biztonsági csoport biztonsági szabályokkal való feltöltése után rendelje hozzá a App Service Environmentt tartalmazó alhálózathoz.  A hozzárendelési parancs két nevet hivatkozik: annak a virtuális hálózatnak a nevére, amelyben a App Service Environment található, valamint annak az alhálózatnak a neve, ahol a App Service Environment létrejött.  

Az alábbi példa egy alhálózathoz és virtuális hálózathoz rendelt hálózati biztonsági csoportot mutat be:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

A hozzárendelés egy hosszan futó művelet, és néhány percet is igénybe vehet. A hálózati biztonsági csoport hozzárendelésének sikeressége után csak az *engedélyezési* szabályoknak megfelelő bejövő forgalom fogja elérni az alkalmazásokat a app Service Environment.

A teljesség kedvéért a következő példa azt szemlélteti, hogyan távolíthatja el és szüntetheti meg a hálózati biztonsági csoportot az alhálózatból:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Speciális szempontok explicit IP-hez – SSL
Ha egy alkalmazás egy explicit IP-SSL-címmel van konfigurálva ( *csak* a nyilvános VIP-mel rendelkező ASE vonatkozik), a app Service Environment alapértelmezett IP-címének használata helyett a http-és HTTPS-forgalom a 80-as és 443-es portokon kívül más portokon keresztül áramlik az alhálózatba.

Az egyes IP-SSL-címek által használt egyes portok megkereséséhez nyissa meg a portált, és tekintse meg a App Service Environment részleteit az UX panelen.  Válassza **a minden beállítás**  >  **IP-címek** lehetőséget.  Az **IP-címek** panelen látható a app Service Environment számára explicit módon konfigurált IP-SSL-címek táblázata. A panelen az egyes IP-alapú SSL-címekhez társított HTTP-és HTTPS-forgalom útválasztásához használt speciális port is látható.  Ezt a portszámot használja a DestinationPortRange paraméterekhez a hálózati biztonsági csoport szabályainak konfigurálásakor.

Ha egy adatszolgáltatón belüli alkalmazás IP-SSL használatára van konfigurálva, a külső ügyfelek nem látják vagy nem kell aggódniuk a speciális port párok leképezésével kapcsolatban.  Az alkalmazások felé irányuló forgalom általában a beállított IP-SSL-címen fog folyni.  A speciális portra való fordítás automatikusan történik belsőleg, az útválasztási forgalom utolsó szakasza alatt a központot tartalmazó alhálózatba. 

## <a name="getting-started"></a>Első lépések
App Service környezetek használatának megkezdéséhez tekintse [meg a app Service Environment bemutatása][IntroToAppServiceEnvironment]című témakört.

További információ: [biztonságos csatlakozás a háttérbeli erőforrásokhoz egy app Service Environment][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->