---
title: Rétegzett biztonság v1
description: Ismerje meg, hogyan valósítható meg egy rétegzett biztonsági architektúra a App Service-környezetben. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 813160ee19c8447fdfcb705e7a9162a41a0c308c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739508"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Többrétegű biztonsági architektúra megvalósítása App Service környezetekkel
Mivel App Service környezetek egy virtuális hálózaton üzembe helyezett elkülönített futtatókörnyezeti környezetet biztosítanak, a fejlesztők létrehozhatnak egy rétegzett biztonsági architektúrát, amely különböző szintű hálózati hozzáférést biztosít az egyes fizikai alkalmazások szintjeihez.

Gyakori a célja, hogy elrejtse az API-t az általános Internet-hozzáféréstől, és csak a felsőbb rétegbeli webalkalmazások számára engedélyezze az API-k meghívását.  A [hálózati biztonsági csoportok (NSG-k)][NetworkSecurityGroups] a app Service környezeteket tartalmazó alhálózatokon használhatók az API-alkalmazásokhoz való nyilvános hozzáférés korlátozására.

Az alábbi ábrán egy App Service Environmenton üzembe helyezett WebAPI-alapú alkalmazás architektúrája látható.  Három különálló, három külön App Service környezetben üzembe helyezett, különálló webalkalmazás-példány, amely a WebAPI-alkalmazáshoz készít háttér-hívásokat.

![Fogalmi architektúra][ConceptualArchitecture] 

A zöld pluszjel azt jelzi, hogy a "apiase" nevű alhálózaton lévő hálózati biztonsági csoport engedélyezi a felsőbb rétegbeli webalkalmazások bejövő hívásait, valamint magától a hívást.  Ugyanakkor ugyanez a hálózati biztonsági csoport kifejezetten megtagadja az internetről érkező általános bejövő forgalom elérését. 

A cikk további része végigvezeti a hálózati biztonsági csoport konfigurálásához szükséges lépéseken a "apiase" nevű alhálózaton.

## <a name="determining-the-network-behavior"></a>A hálózati viselkedés meghatározása
Ha tudni szeretné, hogy milyen hálózati biztonsági szabályokra van szükség, meg kell határoznia, hogy mely hálózati ügyfelek érhetik el az API-alkalmazást tartalmazó App Service Environment, és hogy mely ügyfelek lesznek blokkolva.

Mivel a [hálózati biztonsági csoportok (NSG)][NetworkSecurityGroups] az alhálózatokra vannak alkalmazva, és app Service környezetek alhálózatokra vannak telepítve, a NSG szereplő szabályok a app Service Environment futó **összes** alkalmazásra érvényesek lesznek.  A jelen cikk minta architektúrájának használatával, miután a rendszer a "apiase" nevű alhálózatra alkalmazza a hálózati biztonsági csoportot, a "apiase" App Service Environment futó összes alkalmazást ugyanazokkal a biztonsági szabályokkal fogja védeni. 

* **A felsőbb rétegbeli hívók kimenő IP-címének meghatározása:**  Mi a felsőbb rétegbeli hívók IP-címe vagy címe?  Ezeknek a címeknek explicit módon engedélyezniük kell a hozzáférést a NSG.  Mivel a App Service környezetek közötti hívások "Internet" hívásnak minősülnek, a három felsőbb rétegbeli App Service környezethez rendelt kimenő IP-címet a "apiase" alhálózat NSG kell engedélyezni.   A App Service Environment futó alkalmazások kimenő IP-címének meghatározásával kapcsolatos további információkért tekintse meg a [hálózati architektúra][NetworkArchitecture] áttekintését ismertető cikket.
* **Meg kell hívni a háttérbeli API-alkalmazást?**  Néha a háttérbeli alkalmazásnak meg kell hívnia a helyzetet.  Ha egy App Service Environment háttérbeli API-alkalmazásnak önmagára kell hívnia, akkor az "Internet" hívásként is kezeli.  A minta architektúrában ehhez a "apiase" App Service Environment kimenő IP-címéről is engedélyezni kell a hozzáférést.

## <a name="setting-up-the-network-security-group"></a>A hálózati biztonsági csoport beállítása
A kimenő IP-címek készletének ismerete után a következő lépés egy hálózati biztonsági csoport összeépítése.  A hálózati biztonsági csoportok a Resource Manager-alapú virtuális hálózatokhoz és a klasszikus virtuális hálózatokhoz is létrehozhatók.  Az alábbi példák azt mutatják be, hogyan hozhat létre és konfigurálhat egy NSG egy klasszikus virtuális hálózaton a PowerShell használatával.

A minta architektúrához a környezetek az USA déli középső régiójában találhatók, így az adott régióban üres NSG jön létre:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Először egy explicit engedélyezési szabályt ad hozzá az Azure felügyeleti infrastruktúrához a App Service környezetek [bejövő forgalmáról][InboundTraffic] szóló cikkben leírtak szerint.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

A következő két szabályt ad hozzá, hogy engedélyezze a HTTP-és HTTPS-hívásokat az első felsőbb rétegbeli App Service Environmenttól ("fe1ase").

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Öblítsük le és ismételje meg a második és a harmadik felsőbb rétegbeli App Service környezeteket ("fe2ase" és "fe3ase").

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Végül biztosítson hozzáférést a háttérbeli API App Service Environment kimenő IP-címéhez, hogy az képes legyen visszahívni önmagába.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Nincs szükség további hálózati biztonsági szabályokra, mert az összes NSG alapértelmezett szabályokkal rendelkezik, amelyek letiltják a bejövő hozzáférést az internetről, alapértelmezés szerint.

A hálózati biztonsági csoportban található szabályok teljes listája alább látható.  Figyelje meg, hogy az utolsó olyan szabály, amely ki van emelve, blokkolja a bejövő hozzáférést minden olyan hívótól, amely kifejezetten hozzáférést kapott.

![NSG-konfiguráció][NSGConfiguration] 

Utolsó lépésként alkalmazza a NSG a "apiase" App Service Environment tartalmazó alhálózatra.

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

Az alhálózatra alkalmazott NSG csak a három felsőbb rétegbeli App Service környezet és az API-háttért tartalmazó App Service Environment hívhatók meg a "apiase" környezetbe.

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A [hálózati biztonsági csoportokkal](../../virtual-network/network-security-groups-overview.md)kapcsolatos információk.

A [kimenő IP-címek][NetworkArchitecture] és app Service környezetek ismertetése.

App Service környezetek által használt [hálózati portok][InboundTraffic] .

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png