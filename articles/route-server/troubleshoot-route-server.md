---
title: Az Azure Route Serverrel kapcsolatos problémák elhárítása
description: Ismerje meg, hogyan lehet elhárítani az Azure Route Server hibáit.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 9fa0f73d06bda02d784628823ee70bc538b375e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695804"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Az Azure Route Server problémáinak elhárítása

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Csatlakozási problémák

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>A NVA miért veszítik el az internetkapcsolatot, miután meghirdette az alapértelmezett útvonalat (0.0.0.0/0) az Azure Route Serverre?
Amikor a NVA meghirdeti az alapértelmezett útvonalat, az Azure Route Server a virtuális hálózatban lévő összes virtuális géphez, beleértve a NVA is. Ez az alapértelmezett útvonal a következő ugrásként állítja be a NVA az internethez kötött forgalomhoz. Ha a NVA internetkapcsolatra van szüksége, konfigurálnia kell egy [felhasználó által megadott útvonalat](../virtual-network/virtual-networks-udr-overview.md) , hogy felülírja ezt az alapértelmezett útvonalat a NVA, és csatolja a UDR ahhoz az alhálózathoz, ahol a NVA üzemeltetve van (lásd az alábbi példát). Ellenkező esetben a NVA gazdagépe továbbra is az interneten keresztüli forgalmat küldi el, beleértve a NVA által küldött adatokat is a NVA.

| Útvonal | Következő ugrás |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>Miért tudok pingelni a NVA a BGP partneri IP-címmel az Azure Route Serveren, de miután beállítottam a BGP-társítást a között, nem tudom pingelni egyazon IP-címet? Miért leáll a BGP-társak?

Egyes NVA statikus útvonalat kell hozzáadnia az Azure Route Server alhálózathoz. Ha például az Azure Route Server a 10.0.255.0/27-ben van, és a NVA a 10.0.1.0/24-ben van, akkor a következő útvonalat kell felvennie a NVA útválasztási táblájába:

| Útvonal | Következő ugrás |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

a 10.0.1.1 az alapértelmezett átjáró IP-címe abban az alhálózatban, ahol a NVA (vagy pontosabban az egyik hálózati adapter) üzemeltetve van.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Miért veszítem el a helyszíni hálózatot a ExpressRoute és/vagy az Azure VPN-en keresztül, ha olyan virtuális hálózathoz telepítem az Azure Route Servert, amely már rendelkezik ExpressRoute-átjáróval és/vagy Azure-beli VPN-átjáróval?
Amikor az Azure Route Servert egy virtuális hálózatra telepíti, frissítenie kell az átjárók és a virtuális hálózat közötti vezérlési síkot. A frissítés során a virtuális hálózatban lévő virtuális gépek elveszítik a kapcsolatot a helyszíni hálózattal. Javasoljuk, hogy az Azure Route Server üzembe helyezéséhez az éles környezetben végezze el a karbantartási ütemtervet.  

## <a name="control-plane-issues"></a>A sík problémáinak szabályozása

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Mi a különbség a NVA és az Azure-útválasztó kiszolgáló közötti BGP-társ-összevonással ("csapkodás")?

A lecsapkodás oka a BGP-időzítő beállítása miatt lehet. Alapértelmezés szerint az Azure Route Server életben tartási időzítője 60 másodpercre van beállítva, a lekéréses időzítő pedig 180 másodperc.

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>A NVA miért nem fogadnak útvonalakat az Azure Route Serverről, még akkor is, ha a BGP-társak?

Az Azure Route Server által használt ASN az 65515. Győződjön meg arról, hogy a NVA eltérő ASN-t konfigurál, így a NVA és az Azure-útválasztó kiszolgálója között létrehozhat egy "eBGP" munkamenetet, hogy az útvonal-propagálás automatikusan megtörténjen. Győződjön meg arról, hogy engedélyezi a "Többugrásos" beállítást a BGP-konfigurációban, mert a NVA és az Azure-útválasztó kiszolgáló különböző alhálózatokon található a virtuális hálózaton.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>A NVA és az Azure Route Server közötti BGP-társak. A közöttük lévő útvonalakat is láthatom. Miért nem a NVA útvonalak a virtuális gép tényleges útválasztási táblázatában? 

* Ha a virtuális gép ugyanabban a VNet van, mint a NVA és az Azure Route Server:

     Az Azure Route Server két BGP társ-IP-címet tesz elérhetővé, amelyek két virtuális gépen futnak, amelyek osztoznak az útvonalaknak a virtuális hálózaton futó összes többi virtuális gépen való elküldésének felelősségén. Mindegyik NVA két azonos BGP-munkamenetet kell beállítania (például ugyanazt a számot kell használnia, mint az útvonalat, és ugyanazokat az útvonalakat hirdeti meg) a két virtuális gépen, hogy a virtuális hálózatban lévő virtuális gépek konzisztens útválasztási adatokat kapjanak az Azure Route Servertől. Lásd az alábbi ábrát.

    ![Az útválasztási kiszolgálóval rendelkező hálózati virtuális berendezés ábrája.](./media/faq/network-virtual-appliances.png)

    Ha a NVA két vagy több példánya van, a különböző NVA-példányok esetében különböző elérési utakat *is megadhat* , ha az egyik NVA-példányt aktívként, a másikat pedig passzívként szeretné kijelölni.

* Ha a virtuális gép egy másik virtuális hálózatban található, mint amely a NVA és az Azure Route Servert üzemelteti. Ellenőrizze, hogy a VNet-társítás engedélyezve van-e a két virtuális hálózatok között *, és* ha a távoli útválasztási kiszolgáló használata engedélyezve van a virtuális gép VNet.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [konfigurálhatja az Azure Route Servert](quickstart-configure-route-server-powershell.md)
