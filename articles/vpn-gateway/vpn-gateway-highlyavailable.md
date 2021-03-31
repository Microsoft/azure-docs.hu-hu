---
title: 'Azure VPN Gateway: áttekintés – a magasan elérhető átjáró-konfigurációk'
description: Ez a cikk áttekintést nyújt a magas rendelkezésre állású konfigurációs lehetőségekről az Azure-alapú VPN-átjárók használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 48756b43e64576a5dd38467bb1dd97e91c168a06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91360854"
---
# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Magas rendelkezésre állású kapcsolatok létesítmények, illetve virtuális hálózatok között
Ez a cikk áttekintést nyújt a létesítmények közötti és a virtuális hálózatok közötti kapcsolatokra vonatkozó magas rendelkezésre állású konfigurációs lehetőségekről az Azure-alapú VPN-átjárók használatával.

## <a name="about-azure-vpn-gateway-redundancy"></a><a name = "activestandby"></a>Tudnivalók az Azure-alapú VPN-átjárók redundanciájáról
Minden egyes Azure-alapú VPN-átjáró két példányból áll, amelyek aktív-készenléti konfigurációban vannak. Az aktív példányt érintő bármilyen tervezett karbantartás vagy nem tervezett kimaradás esetén a készenléti példány automatikusan átveszi az aktív szerepét (feladatátvétel), és fenntartja az S2S VPN- vagy a virtuális hálózatok közötti kapcsolatokat. Az átvétel rövid megszakítással jár. Tervezett karbantartás esetén a kapcsolatnak 10-15 másodpercen belül vissza kell állnia. Nem tervezett problémák esetén a kapcsolat helyreállítása hosszabb időt, kb. egy, vagy legrosszabb esetben másfél percet vehet igénybe. A P2S VPN-ügyfél és az átjáró közötti kapcsolatok esetében a P2S-kapcsolatok megszakadnak, és az ügyfeleknek ismét csatlakozniuk kell az ügyfélgépekről.

![A diagram egy helyszíni helyet mutat be privát I P-alhálózatokkal és helyszíni V P N-vel, amely aktív Azure V P N átjáróhoz csatlakozik az Azure-ban üzemeltetett alhálózatokhoz való csatlakozáshoz, és elérhető egy készenléti átjáró.](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Létesítmények közötti magas rendelkezésre állású kapcsolatok
Van néhány lehetőség, amellyel jobb rendelkezésre állás biztosítható a létesítmények közötti kapcsolat számára:

* Több helyszíni VPN-eszköz
* Aktív-aktív Azure-alapú VPN-átjáró
* A kettő kombinációja

### <a name="multiple-on-premises-vpn-devices"></a><a name = "activeactiveonprem"></a>Több helyszíni VPN-eszköz
Az alábbi ábrán látható módon több VPN-eszközt is használhat a helyszíni hálózatából az Azure-alapú VPN-átjáróhoz való csatlakozásra:

![Több helyszíni VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Ez a konfiguráció több aktív alagutat biztosít ugyanabból az Azure-alapú VPN-átjáróból az azonos helyen lévő helyszíni eszközeihez. Van néhány követelmény és megkötés:

1. Több S2S VPN-kapcsolatot kell létesítenie a VPN-eszközök és az Azure között. Ha ugyanarról a helyszíni hálózatról csatlakoztat több VPN-eszközt az Azure-hoz, létre kell hoznia egy helyi hálózati átjárót minden egyes VPN-eszközhöz, és egy kapcsolatot az Azure VPN gatewaytől az egyes helyi hálózati átjárókkal.
2. A VPN-eszközöknek megfelelő helyi hálózati átjáróknak egyedi nyilvános IP-címmel kell rendelkezniük a GatewayIpAddress tulajdonságban.
3. Ehhez a konfigurációhoz BGP szükséges. A VPN-eszközöknek megfelelő összes helyi hálózati átjáróhoz meg kell adnia egy egyedi BGP társ IP-címet a BgpPeerIpAddress tulajdonságban.
4. Az egyes helyi hálózati átjárók AddressPrefix tulajdonságmezői nem lehetnek egymással átfedésben. A BgpPeerIpAddress tulajdonságot /32 CIDR formátumban kell megadnia az AddressPrefix mezőben, például így: 10.200.200.254/32.
5. Az azonos helyszíni hálózatok előtagjait BGP használatával kell meghirdetnie az Azure-alapú VPN-átjárónak, hogy a forgalom továbbítása egyszerre történjen ezeken az alagutakon keresztül.
6. A több elérési úttal rendelkező útválasztást (ECMP) kell használnia.
7. Minden kapcsolat beleszámít az Azure-alapú VPN-átjáró alagútjainak maximális számába: 10 az alapszintű és standard, illetve 30 a HighPerformance termékváltozat esetében. 

Ebben a konfigurációban az Azure-alapú VPN-átjáró továbbra is aktív-készenléti állapotban van, ezért a feladatátvétel és a rövid megszakítás továbbra is bekövetkezik a [fent](#activestandby) leírtak szerint. Ez a beállítás viszont védelmet nyújt a hibák vagy megszakítások ellen a helyszíni hálózat és a VPN-eszközök esetében.

### <a name="active-active-azure-vpn-gateway"></a>Aktív-aktív Azure-alapú VPN-átjáró
Most már létrehozhat egy Azure-alapú VPN-átjárót egy aktív-aktív konfigurációban, amelyben az átjáró virtuális gépeinek mindkét példánya S2S VPN-alagutakat létesít a helyszíni VPN-eszközökkel az alábbi ábrán látható módon:

![A diagram egy helyszíni helyet mutat be privát I P-alhálózatokkal és helyszíni V P N-vel, amely két aktív Azure V N-átjáróhoz csatlakozik az Azure-ban üzemeltetett alhálózatokhoz való csatlakozáshoz.](./media/vpn-gateway-highlyavailable/active-active.png)

Ebben a konfigurációban minden egyes Azure-átjárópéldány egyedi nyilvános IP-címmel rendelkezik, és mindegyik IPsec/IKE S2S VPN-alagutat létesít a helyi hálózati átjáróban és kapcsolatban meghatározott helyszíni VPN-eszközzel. Vegye figyelembe, hogy valójában mindkét VPN-alagút azonos kapcsolat része. Továbbra is konfigurálnia kell a helyszíni VPN-eszközt, hogy elfogadjon vagy létesítsen két S2S VPN-alagutat a két Azure-alapú VPN-átjáró nyilvános IP-címéhez.

Mivel az Azure-átjárópéldányok konfigurációja aktív-aktív, az Azure virtuális hálózat és a helyszíni hálózat közötti forgalom továbbítása még akkor is egyszerre történik a két alagúton, ha a helyszíni VPN-eszköz előnyben részesíti az egyik alagutat a másikhoz képest. Vegye figyelembe, hogy a TCP- vagy UDP-folyamat mindig ugyanazon az alagúton vagy útvonalon halad keresztül, hacsak egy karbantartási esemény nem történik az egyik példányon.

Ha az egyik átjárópéldány esetében tervezett karbantartás vagy nem tervezett esemény következik be, az adott példány és a helyszíni VPN-eszköz közötti IPsec-alagút megszakad. A VPN-eszköz megfelelő útvonalait a rendszer automatikusan eltávolítja vagy visszavonja, hogy a forgalom a másik aktív IPsec-alagútra kerüljön át. Az Azure részéről az érintett példányról az aktív példányra való átállás automatikusan megtörténik.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Kettős redundancia: aktív-aktív VPN-átjárók az Azure és a helyszíni hálózatok számára egyaránt
A legbiztosabb megoldás, ha kombinálja a hálózat és az Azure aktív-aktív átjáróit az alábbi ábrán látható módon.

![Kettős redundancia](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Itt létrehozhatja és beállíthatja az Azure-alapú VPN-átjáró aktív-aktív konfigurációját, továbbá létrehozhat két helyi hálózati átjárót és két kapcsolatot a két helyszíni VPN-eszköz számára a fent leírt módon. Az eredmény 4 IPsec-alagút szoros hálókapcsolata az Azure virtuális hálózat és a helyszíni hálózat között.

Az Azure oldaláról minden átjáró és alagút aktív, így a forgalom egyszerre oszlik el mind a 4 alagút között, bár az egyes TCP- és UDP-folyamatok ismét ugyanazt az alagutat vagy útvonalat követik az Azure oldaláról. A forgalom elosztása ellenére valamennyivel jobb átviteli sebességet tapasztalhat az IPsec-alagutak esetében, de ennek a konfigurációnak a magas rendelkezésre állás az elsődleges célja. Az elosztás statisztikai jellege miatt nehéz felmérni, hogy a különböző alkalmazások adatforgalmának körülményei milyen hatással lesznek az összesített átviteli sebességre.

Ez a topológia két helyi hálózati átjárót és két kapcsolatot igényel a helyszíni VPN-eszközpár támogatásához, a két kapcsolat azonos helyszíni hálózathoz való csatlakozásának engedélyezéséhez pedig BGP szükséges. Ezek a követelmények megegyeznek a [fentiekkel](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Magas rendelkezésre állású virtuális hálózatok közötti kapcsolatok az Azure VPN Gateway-átjárókon keresztül
Ugyanez az aktív-aktív konfiguráció az Azure virtuális hálózatok közötti kapcsolatokra is alkalmazható. Mindkét virtuális hálózat számára létrehozhat VPN-átjárókat, és az összekapcsolásukkal ugyanolyan 4 alagutas szoros hálókapcsolódást érhet el a két virtuális hálózat között, mint amilyen az alábbi ábrán látható:

![A diagram két olyan Azure-régiót mutat be, amelyek privát I P alhálózatokat üzemeltetnek, és két Azure V P N átjárót, amelyeken keresztül a két virtuális hely csatlakozik.](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Ez biztosítja, hogy bármilyen tervezett karbantartási esemény esetében legyen két alagút a két virtuális hálózat között, ami még jobb rendelkezésre állást eredményez. Bár az azonos topológia a létesítmények közötti kapcsolatok esetében két kapcsolatot igényel, a fent bemutatott virtuális hálózatok közötti topológiához az egyes átjárókhoz csak egy kapcsolat szükséges. Emellett a BGP használata nem kötelező, kivéve, ha a virtuális hálózatok közötti kapcsolat esetében átmenő útválasztásra van szükség.

## <a name="next-steps"></a>Következő lépések
A létesítmények közötti és a virtuális hálózatok közötti aktív-aktív kapcsolatok konfigurálásának lépéseit lásd: [Aktív-aktív VPN Gateway-átjárók konfigurálása létesítmények közötti és virtuális hálózatok közötti kapcsolatokhoz](vpn-gateway-activeactive-rm-powershell.md).

