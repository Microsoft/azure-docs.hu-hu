---
title: Az Azure Route Serverrel kapcsolatos problémák elhárítása
description: Ismerje meg, hogyan lehet elhárítani az Azure Route Server hibáit.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679861"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Az Azure Route Server problémáinak elhárítása

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bgp-connectivity-issues"></a>BGP-kapcsolati problémák

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Mi a különbség a NVA és az Azure-útválasztó kiszolgáló közötti BGP-társ-összevonással ("csapkodás")?

A lecsapkodás oka a BGP-időzítő beállítása miatt lehet. Alapértelmezés szerint az Azure Route Server életben tartási időzítője 60 másodpercre van beállítva, a lekéréses időzítő pedig 180 másodperc.

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>Miért tudok pingelni a NVA a BGP partneri IP-címmel az Azure Route Serveren, de miután beállítottam a BGP-társítást a között, nem tudom pingelni egyazon IP-címet? Miért leáll a BGP-társak?

Egyes NVA statikus útvonalat kell hozzáadnia az Azure Route Server alhálózathoz. Ha például az Azure Route Server a 10.0.255.0/27-ben van, és a NVA a 10.0.1.0/24-ben van, akkor a következő útvonalat kell felvennie a NVA útválasztási táblájába:

| Útvonal | Következő ugrás |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

a 10.0.1.1 az alapértelmezett átjáró IP-címe abban az alhálózatban, ahol a NVA (vagy pontosabban az egyik hálózati adapter) üzemeltetve van.

## <a name="bgp-route-issues"></a>BGP-útválasztási problémák

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>A NVA miért nem fogadnak útvonalakat az Azure Route Serverről, még akkor is, ha a BGP-társak?

Az Azure Route Server által használt ASN az 65515. Győződjön meg arról, hogy a NVA eltérő ASN-t konfigurál, így a NVA és az Azure-útválasztó kiszolgálója között létrehozhat egy "eBGP" munkamenetet, hogy az útvonal-propagálás automatikusan megtörténjen.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>A NVA és az Azure Route Server közötti BGP-társak. A közöttük lévő útvonalakat is láthatom. Miért nem a NVA útvonalak a virtuális gép tényleges útválasztási táblázatában? 

* Ha a virtuális gép ugyanabban a VNet van, mint a NVA és az Azure Route Server:

     Az Azure Route Server két BGP társ-IP-címet tesz elérhetővé, amelyek két virtuális gépen futnak, amelyek osztoznak az útvonalaknak a virtuális hálózaton futó összes többi virtuális gépen való elküldésének felelősségén. Mindegyik NVA két azonos BGP-munkamenetet kell beállítania (például ugyanazt a számot kell használnia, mint az útvonalat, és ugyanazokat az útvonalakat hirdeti meg) a két virtuális gépen, hogy a virtuális hálózatban lévő virtuális gépek konzisztens útválasztási adatokat kapjanak az Azure Route Servertől. Lásd az alábbi ábrát.

    ![Az útválasztási kiszolgálóval rendelkező hálózati virtuális berendezés ábrája.](./media/faq/network-virtual-appliances.png)

    Ha a NVA két vagy több példánya van, a különböző NVA-példányok esetében különböző elérési utakat *is megadhat* , ha az egyik NVA-példányt aktívként, a másikat pedig passzívként szeretné kijelölni.

* Ha a virtuális gép egy másik virtuális hálózatban található, mint amely a NVA és az Azure Route Servert üzemelteti. Ellenőrizze, hogy a VNet-társítás engedélyezve van-e a két virtuális hálózatok között *, és* ha a távoli útválasztási kiszolgáló használata engedélyezve van a virtuális gép VNet.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [konfigurálhatja az Azure Route Servert](quickstart-configure-route-server-powershell.md)
