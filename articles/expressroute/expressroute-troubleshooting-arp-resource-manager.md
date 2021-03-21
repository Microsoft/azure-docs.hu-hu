---
title: 'Azure ExpressRoute: ARP-táblák – hibaelhárítás'
description: Ez az oldal útmutatást nyújt a ExpressRoute-áramkörhöz tartozó ARP-táblázatok beszerzéséhez
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97561579"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>ARP-táblák lekérése a Resource Manager-alapú üzemi modellben
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-troubleshooting-arp-classic.md)
> 
> 

Ez a cikk végigvezeti a ExpressRoute-áramkör ARP-tábláinak megismeréséhez szükséges lépéseken.

> [!IMPORTANT]
> A dokumentum célja, hogy segítséget nyújtson az egyszerű problémák diagnosztizálásában és megoldásában. Nem helyettesíti a Microsoft támogatási szolgálatát. Ha nem tudja megoldani a problémát az alább ismertetett útmutatással, meg kell nyitnia egy támogatási jegyet a [Microsoft ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>A címhozzárendelés protokoll (ARP) és az ARP-táblák
A címhozzárendelés protokoll (ARP) az [RFC 826](https://tools.ietf.org/html/rfc826)-ben definiált 2. rétegbeli protokoll. Az ARP az Ethernet-cím (MAC-cím) IP-címmel való leképezésére szolgál.

Az ARP-tábla az alábbi információkat tartalmazza mind az elsődleges, mind a másodlagos csatolóhoz az egyes társítási típusok esetében:

1. Helyszíni útválasztó illesztő IP-címének hozzárendelése a MAC-címhez
2. A ExpressRoute útválasztó felületének IP-címének hozzárendelése a MAC-címhez
3. A leképezés kora

Az ARP-táblázatok segítségével ellenőrizheti a 2. réteg konfigurációját, és elháríthatja az alapszintű 2. rétegbeli csatlakozási problémákat 

Példa ARP-táblázatra: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


A következő szakasz arról nyújt tájékoztatást, hogyan tekintheti meg az ExpressRoute Edge-útválasztók által látott ARP-táblákat. 

## <a name="prerequisites-for-learning-arp-tables"></a>Az ARP-táblák megismerésének előfeltételei
A folytatás előtt győződjön meg arról, hogy az alábbi információk teljesülnek:

* Legalább egy társítással konfigurált érvényes ExpressRoute-áramkör. Az áramkört teljesen konfigurálni kell a kapcsolat szolgáltatójának. Ön vagy a kapcsolat szolgáltatójának legalább az Azure Private, az Azure nyilvános vagy a Microsoft-társítást kell konfigurálnia ezen az áramkörön.
* A társak konfigurálásához használt IP-címtartományok. Tekintse át az IP-címek hozzárendelésével kapcsolatos példákat az [ExpressRoute-útválasztási követelmények lapon](expressroute-routing.md) , hogy megtudja, hogyan legyenek leképezve az IP-címek A társítási konfigurációval kapcsolatos információkat a ExpressRoute-társítási [konfiguráció oldalának](expressroute-howto-routing-arm.md)áttekintésével érheti el.
* A hálózati csapat/kapcsolat szolgáltatójától származó információk az ezen IP-címekkel használt felületek MAC-címein.
* Az Azure-hoz készült legújabb PowerShell-modulnak (1,50-es vagy újabb verzió) kell lennie.

> [!NOTE]
> Ha a 3. réteg a szolgáltató által biztosított, és az ARP-táblák üresek az alábbi portálon/kimenetben, frissítse az áramköri konfigurációt a portál frissítés gombjával. Ez a művelet a megfelelő útválasztási konfigurációt alkalmazza az áramkörön. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>A ExpressRoute-áramkör ARP-tábláinak beolvasása
Ez a szakasz útmutatást nyújt arról, hogy miként tekintheti meg egymással az ARP-táblákat a PowerShell használatával. Az Ön vagy a kapcsolat szolgáltatójának a folytatás előtt konfigurálnia kell a társítást. Minden áramkör két elérési úttal rendelkezik (elsődleges és másodlagos). Az ARP-táblázatot egymástól függetlenül is megtekintheti.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-táblák az Azure Private-társához
A következő parancsmag az Azure privát társának ARP-táblázatait tartalmazza

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

A minta kimenete alább látható az egyik elérési útra

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>ARP-táblák az Azure nyilvános társának
A következő parancsmag az Azure nyilvános társ-összevonásához használható ARP-táblákat tartalmazza

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


A minta kimenete alább látható az egyik elérési útra

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>ARP-táblák a Microsoft-partnerek számára
A következő parancsmag a Microsoft-partnerek ARP-tábláit biztosítja

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


A minta kimenete alább látható az egyik elérési útra

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Az információk használata
A társítások ARP-táblázata a 2. réteg konfigurációjának és a kapcsolatok ellenőrzésének meghatározására használható. Ez a szakasz áttekintést nyújt arról, hogy az ARP-táblázatok hogyan fognak megjelenni a különböző forgatókönyvekben.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tábla, ha egy áramkör működési állapotban van (várt állapot)
* Az ARP-táblázat egy érvényes IP-címet és MAC-címet tartalmazó bejegyzéssel fog rendelkezni a helyszíni oldalon. Ugyanez a Microsoft oldalán is látható. 
* A helyszíni IP-cím utolsó oktettje mindig páratlan szám lesz.
* A Microsoft IP-címének utolsó oktettje mindig páros szám lesz.
* Ugyanez a MAC-címe fog megjelenni a Microsoft oldalán mindhárom (elsődleges/másodlagos) partner számára. 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tábla, ha a helyszín/kapcsolatszolgáltató oldalán problémák léptek fel
Ha a helyszíni vagy a kapcsolati szolgáltatóval kapcsolatos probléma merül fel, az ARP-tábla két dolog egyikét fogja megjeleníteni. A helyszíni MAC-címek megjelenítése nem fejeződött be, vagy csak a Microsoft bejegyzés jelenik meg az ARP táblában.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
vagy
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Az ilyen problémák hibakereséséhez nyisson meg egy támogatási kérést a kapcsolat szolgáltatójánál. Ha az ARP-tábla nem rendelkezik a MAC-címekhez hozzárendelt adapterek IP-címeivel, tekintse át a következő információkat:
> 
> 1. Ha a MSEE-PR és a MSEE közötti kapcsolathoz hozzárendelt/30 alhálózat első IP-címe a MSEE-PR felületén van használatban. Az Azure mindig a második IP-címet használja a Msee.
> 2. Ellenőrizze, hogy az ügyfél (C-tag) és a szolgáltatás (S-tag) VLAN-címkék egyeznek-e mind a MSEE, mind a MSEE párban.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tábla, ha a Microsoft oldalán problémák léptek fel
* Ha problémák merülnek fel a Microsoft oldalán, nem jelenik meg a társak számára megjelenített ARP-táblázat. 
* Nyisson meg egy támogatási jegyet a [Microsoft ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Adja meg, hogy van-e probléma a 2. rétegbeli kapcsolattal. 

## <a name="next-steps"></a>Következő lépések
* Ellenőrizze az ExpressRoute áramkör 3. rétegbeli konfigurációit.
  * A BGP-munkamenetek állapotának megállapításához lekérheti az útvonal összegzését.
  * Az útválasztási táblázat lekérésével meghatározhatja, hogy mely előtagokat hirdessen meg a rendszer a ExpressRoute között.
* Ellenőrizze az adatátvitelt a bájtok vagy kimenő adatok áttekintésével.
* Ha továbbra is problémákat tapasztal, nyisson meg egy támogatási jegyet a [Microsoft ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

