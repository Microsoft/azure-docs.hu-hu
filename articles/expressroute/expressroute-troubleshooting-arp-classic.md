---
title: 'Azure ExpressRoute: ARP-táblák – hibaelhárítás: klasszikus'
description: Ez az oldal utasításokat tartalmaz a ExpressRoute Circuit-klasszikus üzemi modell ARP-tábláinak beszerzéséhez.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89394839"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>ARP-táblák lekérése a klasszikus üzemi modellben
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk végigvezeti az Azure ExpressRoute-áramkörhöz tartozó ARP-táblázatok beszerzésének lépésein.

> [!IMPORTANT]
> A dokumentum célja, hogy segítséget nyújtson az egyszerű problémák diagnosztizálásában és megoldásában. Nem helyettesíti a Microsoft támogatási szolgálatát. Ha a következő útmutatással nem tudja megoldani a problémát, nyisson meg egy támogatási kérést [Microsoft Azure Súgó és támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)szolgáltatással.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>A címhozzárendelés protokoll (ARP) és az ARP-táblák
Az ARP egy 2. rétegbeli protokoll, amely az [RFC 826](https://tools.ietf.org/html/rfc826)-ben van meghatározva. Az ARP egy Ethernet-cím (MAC-cím) IP-címhez való leképezésére szolgál.

Az ARP-táblázat egy adott társítás IPv4-címeinek és MAC-címeinek leképezését biztosítja. Az ExpressRoute-kezelői társítás ARP-táblázata az egyes interfészekhez (elsődleges és másodlagos) a következő információkat tartalmazza:

1. Helyszíni útválasztó illesztő IP-címének leképezése MAC-címhez
2. ExpressRoute útválasztó illesztő IP-címének leképezése MAC-címhez
3. A leképezés kora

Az ARP-táblák segíthetnek a 2. rétegbeli konfiguráció érvényesítésében és az alapszintű 2. rétegbeli kapcsolódási problémák elhárításában.

Az alábbi példa egy ARP-táblázatot mutat be:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


A következő szakasz az ExpressRoute Edge-útválasztók által látott ARP-táblák megtekintésével kapcsolatos információkat tartalmaz.

## <a name="prerequisites-for-using-arp-tables"></a>Az ARP-táblák használatának előfeltételei
A folytatás előtt győződjön meg arról, hogy rendelkezik a következőkkel:

* Egy érvényes ExpressRoute-áramkör, amely legalább egy társsal van konfigurálva. Az áramkört teljesen konfigurálni kell a kapcsolat szolgáltatójának. A munkaterületen (vagy a kapcsolat szolgáltatóján) konfigurálnia kell legalább az egyik társat (Azure Private, Azure Public vagy Microsoft).
* A társak konfigurálásához használt IP-címtartományok (Azure Private, Azure Public és Microsoft). Tekintse át az IP-címek hozzárendelésével kapcsolatos példákat az [ExpressRoute-útválasztási követelmények oldalon](expressroute-routing.md) , és Ismerje meg, hogy az IP-címek hogyan vannak leképezve a felületekre az Ön oldalán és a ExpressRoute oldalon. A társítási konfigurációval kapcsolatos információkat a ExpressRoute-társítási [konfiguráció oldalának](expressroute-howto-routing-classic.md)áttekintésével érheti el.
* A hálózati csapattól vagy a kapcsolat szolgáltatójától származó információk az ezen IP-címekhez használt felületek MAC-címeiről.
* Az Azure legújabb Windows PowerShell-modulja (1,50-es vagy újabb verzió).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Az ExpressRoute-áramkör ARP-táblái
Ez a szakasz azt ismerteti, hogyan lehet a PowerShell használatával megtekinteni az egyes társ-típusok ARP-tábláit. A folytatás előtt vagy Ön vagy a kapcsolat szolgáltatójának konfigurálnia kell a társítást. Minden áramkör két elérési úttal rendelkezik (elsődleges és másodlagos). Az ARP-táblázatot egymástól függetlenül is megtekintheti.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-táblák az Azure Private-társához
A következő parancsmag az Azure Private-partnerekhez tartozó ARP-táblákat biztosítja:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Az alábbi példa az egyik útvonal kimenetét jeleníti meg:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>ARP-táblák az Azure nyilvános társításához:
A következő parancsmag az Azure-beli nyilvános partnerek ARP-tábláit tartalmazza:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Az alábbi példa az egyik útvonal kimenetét jeleníti meg:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Az alábbi példa az egyik útvonal kimenetét jeleníti meg:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>ARP-táblák a Microsoft-partnerek számára
A következő parancsmag a Microsoft-partnerek ARP-tábláit biztosítja:

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


A minta kimenete alább látható a következő elérési utak egyikéhez:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Az információk használata
A társítások ARP-táblázata a 2. rétegbeli konfiguráció és a kapcsolat ellenőrzéséhez használható. Ez a szakasz áttekintést nyújt arról, hogy az ARP-táblázatok hogyan néznek ki különböző forgatókönyvekben.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP-tábla, ha egy áramkör működési (várt) állapotban van
* Az ARP-táblázat egy bejegyzést tartalmaz a helyszíni oldalon érvényes IP-és MAC-címmel, valamint egy hasonló bejegyzést a Microsoft oldalán.
* A helyszíni IP-cím utolsó oktettje mindig páratlan szám.
* A Microsoft IP-címének utolsó oktettje mindig páros szám.
* Ugyanaz a MAC-címe jelenik meg a Microsoft oldalán mindhárom (elsődleges/másodlagos) partnernél.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tábla, ha a helyszíni, vagy ha a kapcsolódási szolgáltató oldalán problémák léptek fel
 Az ARP-táblában csak egy bejegyzés jelenik meg. Megjeleníti a MAC-cím és a Microsoft-oldalon használt IP-cím közötti leképezést.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Ha ezt a problémát tapasztalja, nyisson meg egy támogatási kérést a kapcsolat szolgáltatójánál a megoldásához.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tábla, ha a Microsoft oldalán problémák léptek fel
* Ha problémák merülnek fel a Microsoft oldalán, nem jelenik meg a társak számára megjelenített ARP-táblázat.
* Nyisson meg egy támogatási kérést [Microsoft Azure Súgó + támogatással](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Adja meg, hogy van-e probléma a 2. rétegbeli kapcsolattal.

## <a name="next-steps"></a>Következő lépések
* A 3. rétegbeli konfigurációk ellenőrzése a ExpressRoute áramkörhöz:
  * A BGP-munkamenetek állapotának meghatározásához szerezze be az útvonal összegzését.
  * Adja meg azt az útválasztási táblázatot, amely meghatározza, hogy mely előtagokat hirdesse a rendszer a ExpressRoute között.
* Ellenőrizze az adatátvitelt a bájtok és a kimenő adatok áttekintésével.
* Ha továbbra is problémákat tapasztal, nyisson meg egy támogatási kérést [Microsoft Azure Súgó + támogatással](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

