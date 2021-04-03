---
title: A S2S VPN használata biztonsági mentésként az Azure ExpressRoute Private-társításához | Microsoft Docs
description: Ez az oldal az Azure ExpressRoute privát S2S VPN-sel való biztonsági mentésére szolgáló építészeti javaslatokat tartalmaz.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207920"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>A S2S VPN használata biztonsági mentésként a ExpressRoute-alapú privát partnerek számára

A ExpressRoute-alapú, a vész- [helyreállítás megtervezése][DR-PP]című cikkből megbeszéltük, hogy szükség van-e a biztonsági mentési megoldásra a ExpressRoute privát kapcsolati kapcsolataihoz, és hogyan használható a Geo-redundáns ExpressRoute-áramkörök a célra. Ebből a cikkből megtudhatja, hogyan hasznosíthatja és kezelheti a helyek közötti (S2S) VPN-t biztonsági másolatként a ExpressRoute privát társításához. 

A Geo-redundáns ExpressRoute áramköröktől eltérően a ExpressRoute-VPN vész-helyreállítási kombinációja csak aktív-passzív módban használható. A passzív üzemmódban a biztonsági mentés hálózati kapcsolatának egyik fő kihívása, hogy a passzív kapcsolat az elsődleges kapcsolat mellett gyakran meghiúsul. A passzív kapcsolatok meghibásodásának gyakori oka az aktív karbantartás hiánya. Ebből a cikkből megtudhatja, hogyan ellenőrizheti és aktívan karbantarthatja a S2S VPN-kapcsolatot, amely biztonsági mentést készít egy ExpressRoute privát társáról.

>[!NOTE] 
>Ha egy adott útvonal a ExpressRoute és a VPN használatával is meghirdetve van, az Azure előnyben részesíti az útválasztást a ExpressRoute.  
>

Ebből a cikkből megtudhatja, hogyan ellenőrizheti a kapcsolatot az Azure perspektívából és az ügyféloldali hálózat peremhálózati perspektívájában. A végpontok bármelyikének ellenőrzésének lehetősége segítséget nyújt attól függetlenül, hogy felügyeli-e a Microsoft hálózati entitásokkal egyenrangú ügyféloldali hálózati eszközöket. 

## <a name="example-topology"></a>Példa topológia

A telepítőnk egy Azure hub-VNet csatlakoztatott helyszíni hálózattal rendelkezik, amely egy ExpressRoute áramkörön és egy S2S VPN-kapcsolaton keresztül is elérhető. Az Azure hub VNet egy küllős VNet van csatlakoztatva, ahogy az alábbi ábrán is látható:

![1][1]

A telepítőben a ExpressRoute áramkört a helyszíni "Customer Edge" (CE) útválasztók egy pár végén leállítja. A helyszíni helyi hálózat a következőhöz csatlakozik a CE-útválasztók számára egy olyan tűzfalon keresztül, amely a Leader-követő módban működik. A S2S VPN közvetlenül a tűzfalakon van lezárva.

A következő táblázat a topológia legfontosabb IP-előtagjait sorolja fel:

| **Entitás** | **Előtag** |
| --- | --- |
| Helyszíni helyi hálózat | 10.1.11.0/25 |
| Azure hub-VNet | 10.17.11.0/25 |
| Azure küllős VNet | 10.17.11.128/26 |
| Helyszíni tesztelési kiszolgáló | 10.1.11.10 |
| Küllős VNet teszt virtuális gép | 10.17.11.132 |
| ExpressRoute elsődleges kapcsolatok P2P-alhálózat | 192.168.11.16/30 |
| ExpressRoute másodlagos kapcsolatok P2P alhálózata | 192.168.11.20/30 |
| VPN-átjáró elsődleges BGP-társ IP-címe | 10.17.11.76 |
| VPN Gateway másodlagos BGP-társ IP-címe | 10.17.11.77 |
| Helyszíni tűzfal VPN BGP társ IP-címe | 192.168.11.88 |
| Elsődleges CE-útválasztó i/f a tűzfal IP-címe felé | 192.168.11.0/31 |
| Az elsődleges CE útválasztó IP-címére irányuló tűzfal i/f | 192.168.11.1/31 |
| Másodlagos CE-útválasztó i/f a tűzfal IP-címe felé | 192.168.11.2/31 |
| Tűzfal i/f a másodlagos CE útválasztó IP-címének eléréséhez | 192.168.11.3/31 |


A következő táblázat a topológia ASN sorolja fel:

| **Autonóm rendszerek** | **ASN** |
| --- | --- |
| Helyszíni | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Virtual Network GW (ExR) | 65515 |
| Virtual Network GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Magas rendelkezésre állás aszimmetrikus nélkül

### <a name="configuring-for-high-availability"></a>Magas rendelkezésre állás konfigurálása

A [ExpressRoute és a helyek közötti egyidejű kapcsolatok konfigurálása][Conf-CoExist] ismerteti, hogyan lehet konfigurálni a egyidejű ExpressRoute áramköri és S2S VPN-kapcsolatokat. Ahogy a ExpressRoute- [vel való magas rendelkezésre állás kialakításával][HA]foglalkozunk, a ExpressRoute magas rendelkezésre állásának javítása érdekében a telepítő fenntartja a hálózati redundanciát (így elkerülhető az egypontos meghibásodás) egészen a végpontok felé. A ExpressRoute-áramkörök elsődleges és másodlagos kapcsolatai is aktív-aktív módban működnek úgy, hogy a helyszíni előtagokat ugyanúgy, a kapcsolatokon keresztül reklámozzák. 

Az elsődleges CE útválasztó helyszíni útválasztási hirdetménye az ExpressRoute áramkör elsődleges kapcsolatán keresztül jelenik meg (Junos parancsok):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

A másodlagos CE útválasztó helyszíni útválasztási hirdetménye az ExpressRoute áramkör másodlagos kapcsolatán keresztül jelenik meg (Junos parancsok):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

A biztonsági mentési kapcsolat magas rendelkezésre állásának javítása érdekében a S2S VPN is aktív-aktív módban van konfigurálva. Az Azure VPN Gateway konfigurálása alább látható. Vegye figyelembe, hogy a VPN-konfiguráció VPN-ben az átjáró BGP-társ IP-címe (10.17.11.76 és 10.17.11.77) is fel van sorolva.

![2][2]

A helyszíni útvonalat a tűzfal a VPN-átjáró elsődleges és másodlagos BGP-társának hirdeti. Az útvonal-hirdetmények alább láthatók (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Ha a S2S VPN-t aktív-aktív módban konfigurálja, nem csak magas rendelkezésre állást biztosít a vész-helyreállítási biztonsági mentési hálózati kapcsolathoz, de a biztonsági mentési kapcsolathoz is magasabb átviteli sebességet biztosít. Más szóval a S2S VPN konfigurálása aktív-aktív módban javasolt, mivel a több alapul szolgáló alagutat hoz létre.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurálás szimmetrikus adatforgalomhoz

Megjegyezték, hogy ha egy adott helyszíni útvonalat a ExpressRoute és a S2S VPN használatával hirdetnek meg, az Azure előnyben részesíti a ExpressRoute elérési útját. Ahhoz, hogy az Azure inkább a S2S VPN-elérési utat részesíti előnyben az egyidejű ExpressRoute, a VPN-kapcsolaton keresztül több konkrét útvonalat kell meghirdetni (nagyobb alhálózati maszkkal hosszabb előtaggal). A cél az, hogy a VPN-kapcsolatokat csak biztonsági mentésként használja. Így az Azure alapértelmezett útvonal-kiválasztási viselkedése a célunk. 

Felelősségünk biztosítani, hogy az Azure-ba érkező, helyszíni forgalom a ExpressRoute útvonalon is előnyben részesíti a S2S VPN-t. A helyszíni telepítőben a CE-útválasztók és tűzfalak alapértelmezett helyi beállításai a 100. Tehát a 100-nál nagyobb privát ExpressRoute keresztül fogadott útvonalak helyi preferenciájának konfigurálásával (mondjuk 150) az Azure-ba irányuló forgalom a ExpressRoute áramkört stabil állapotba helyezheti.

Alább látható az elsődleges CE-útválasztó BGP-konfigurációja, amely leállítja az ExpressRoute áramkör elsődleges kapcsolatát. Figyelje meg, hogy a iBGP-munkamenetben hirdetett útvonalak helyi preferencia értéke 150. Hasonlóképpen biztosítani kell, hogy a ExpressRoute áramkör másodlagos kapcsolatát lezáró másodlagos CE-útválasztó helyi beállításai is 150-re legyenek konfigurálva.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

A helyszíni tűzfalak útválasztási táblázata (lásd alább) azt jelzi, hogy az Azure-ba irányuló helyszíni forgalom esetében az előnyben részesített útvonal a ExpressRoute, állandó állapotban van.

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

A fenti útválasztási táblázatban a hub és a küllős VNet útvonalakon – 10.17.11.0/25 és 10.17.11.128/26 – a ExpressRoute áramkör előnyben részesített VPN-kapcsolatokon keresztül. A 192.168.11.0 és a 192.168.11.2 a CE-útválasztók felé irányuló IP-címek.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Útvonal-Exchange S2S VPN-en keresztüli ellenőrzése

A cikk korábbi szakaszaiban ellenőrizte a tűzfalak helyszíni útválasztási hirdetményét a VPN-átjáró elsődleges és másodlagos BGP-társának. Továbbá erősítse meg, hogy az Azure-útvonalak a VPN-átjáró elsődleges és másodlagos BGP-társaitól érkeznek a tűzfalakon.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

Hasonlóképpen ellenőrizzük az Azure VPN Gateway által fogadott helyszíni hálózati útvonalak előtagjait. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

A fentiekben látható módon a VPN-átjáró a VPN-átjáró elsődleges és másodlagos BGP-társai által fogadott útvonalakkal rendelkezik. Emellett az elsődleges és másodlagos ExpressRoute-kapcsolatokon keresztül fogadott útvonalakon is látható (a 12076-AS útvonal-előtagértéke). A VPN-kapcsolatokon keresztül fogadott útvonalak ellenőrzéséhez ismernie kell a kapcsolatok helyszíni BGP-társi IP-címét. A telepítés során figyelembe kell venni a 192.168.11.88, és látjuk a tőle kapott útvonalakat.

Ezután ellenőrizze az Azure VPN Gateway által hirdetett útvonalakat a helyszíni tűzfal BGP-társával (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Nem sikerült megtekinteni a kapcsolódási hibát jelző útválasztási adatcserét. Lásd [: Hibaelhárítás: az Azure-helyek közötti VPN-kapcsolat nem tud csatlakozni, és nem működik][VPN Troubleshoot] a VPN-kapcsolat hibaelhárítása érdekében.

## <a name="testing-failover"></a>Feladatátvétel tesztelése

Most, hogy megerősítettük a sikeres útvonalak cseréjét a VPN-kapcsolaton (vezérlési síkon), úgy van beállítva, hogy a ExpressRoute-kapcsolaton keresztül a VPN-kapcsolat felé váltson adatforgalmat (adatsíkon). 

>[!NOTE] 
>Éles környezetekben a feladatátvételi tesztet az ütemezett hálózati karbantartási munka-ablak alatt kell elvégezni, mivel a szolgáltatás zavaró lehet.
>

A forgalmi kapcsoló megkezdése előtt nyomon követheti az aktuális elérési útvonalat a helyszíni tesztelési kiszolgálóról a küllős VNet lévő tesztelési virtuális gépre.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

A beállítás elsődleges és másodlagos ExpressRoute pont-pont típusú kapcsolatainak alhálózatai a következők: 192.168.11.16/30 és 192.168.11.20/30. A fenti nyomkövetési útvonalon, a 3. lépésben láthatjuk, hogy 192.168.11.18 vagyunk, amely az elsődleges MSEE felületi IP-címe. A MSEE-felület jelenléte megerősíti, hogy az aktuális útvonal a ExpressRoute felett van.

Az [ExpressRoute-áramköri kapcsolatok alaphelyzetbe állításakor][RST]a következő PowerShell-parancsokkal tilthatja le az ExpressRoute áramkör elsődleges és másodlagos társítását is.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

A feladatátvételi váltás ideje a BGP-konvergencia időpontjától függ. A telepítőben a feladatátvételi kapcsoló eltarthat néhány másodpercig (kevesebb mint 10). A kapcsoló után a traceroute ismétlése a következő elérési utat mutatja:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

A traceroute-eredmény megerősíti, hogy a S2S VPN-en keresztüli biztonsági mentési kapcsolat aktív, és biztosítja a szolgáltatás folytonosságát, ha az elsődleges és a másodlagos ExpressRoute-kapcsolat nem sikerül. A feladatátvételi teszt befejezéséhez engedélyezzük a ExpressRoute-kapcsolatok visszakapcsolását és normalizálni a forgalmat a következő parancsok használatával.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Annak ellenőrzéséhez, hogy a forgalom vissza lett-e állítva a ExpressRoute, ismételje meg a traceroute-t, és győződjön meg arról, hogy a ExpressRoute privát társán keresztül zajlik.

## <a name="next-steps"></a>Következő lépések

A ExpressRoute magas rendelkezésre állású, és a Microsoft-hálózaton belül egyetlen meghibásodási pont hiányában van kialakítva. A ExpressRoute áramkör továbbra is egyetlen földrajzi régióra és egy szolgáltatóra korlátozódik. A S2S VPN lehet jó vész-helyreállítási passzív biztonsági mentési megoldás egy ExpressRoute-áramkörhöz. A megbízható passzív biztonsági mentési megoldás esetében fontos a passzív konfiguráció és az időszakos ellenőrzés rendszeres karbantartása. Elengedhetetlen, hogy a VPN-konfiguráció elavult legyen, és rendszeres időközönként (mondjuk minden negyedévben) ismételje meg a jelen cikkben ismertetett ellenőrzési és feladatátvételi teszteket a karbantartási időszak során.

A VPN Gateway metrikái alapján történő figyelés és riasztások engedélyezéséhez tekintse meg [a riasztások beállítása VPN Gateway mérőszámokon][VPN-alerts]című témakört.

ExpressRoute-meghibásodást követő BGP-konvergencia felgyorsításához [konfigurálja a BFD][BFD]-t a ExpressRoute-en keresztül.

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "az érintett topológia"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW-konfiguráció"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md