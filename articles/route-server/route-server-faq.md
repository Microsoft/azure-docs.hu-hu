---
title: Gyakori kérdések az Azure Route Serverről
description: Választ találhat az Azure Route Serverrel kapcsolatos gyakori kérdésekre.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 0bbe16fb63a4546b4b4745df16074f6a4b0cb26b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599536"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (előzetes verzió) – gyakori kérdések

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Mi az az Azure Route Server?

Az Azure Route Server egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a hálózati virtuális berendezés (NVA) és a virtuális hálózat közötti útválasztás egyszerű kezelését.

### <a name="is-azure-route-server-just-a-vm"></a>Az Azure Route Server csak egy virtuális gép?

Nem. Az Azure Route Server egy magas rendelkezésre állású szolgáltatás. Ha olyan Azure-régióban van üzembe helyezni, amely támogatja a [Availability Zones,](../availability-zones/az-overview.md)zónaszintű redundanciával fog kiesni.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Hány útvonalkiszolgálót hozhatok létre egy virtuális hálózaton?

Egy virtuális hálózatban csak egy útvonalkiszolgálót hozhat létre. Ezt a *RouteServerSubnet* nevű kijelölt alhálózaton kell üzembe helyezni.

### <a name="does-azure-route-server-support-vnet-peering"></a>Támogatja az Azure Route Server a virtuális hálózatok közötti társviszonyt?

Igen. Ha társviszonyt létesít egy Azure Route Servert üzemeltető virtuális hálózat és egy másik virtuális hálózat között, és engedélyezi a Távoli átjáró használata az utóbbi virtuális hálózatban, az Azure Route Server megtanulja a virtuális hálózat címterét, és elküldi azokat az összes virtuális társviszonyban található NVA-nak. Emellett beprogramja az NVA-k útvonalait a virtuális társhálózatban található virtuális gépek útválasztási táblázatába. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Milyen útválasztási protokollokat támogat az Azure Route Server?

Az Azure Route Server csak Border Gateway Protocol (BGP)-t támogat. Az NVA-nak támogatnia kell a többugrásos külső BGP-t, mert az Azure Route Servert a virtuális hálózat egy dedikált alhálózatán kell üzembe helyeznie. A [választott ASN-nek](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) eltérnie kell az Azure Route Server által használttól, amikor konfigurálja a BGP-t az NVA-n.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Az Azure Route Server irányít adatforgalmat az NVA és a virtuális gépek között?

Nem. Az Azure Route Server csak BGP-útvonalakat cseréje az NVA-val. Az adatforgalom közvetlenül az NVA-ból a cél virtuális gépre, és közvetlenül a virtuális gépről az NVA-hoz kerül.

### <a name="does-azure-route-server-store-customer-data"></a>Az Azure Route Server ügyféladatokat tárol?
Nem. Az Azure Route Server csak BGP-útvonalakat hoz létre az NVA-val, majd propagálja őket a virtuális hálózatra.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Ha az Azure Route Server ugyanazt az útvonalat kapja egynél több NVA-tól, hogyan kezeli őket?

Ha az útvonal hossza megegyezik az AS elérési út hosszával, az Azure Route Server az útvonal több példányát programja a virtuális hálózat virtuális gépei felé, mindegyiket eltérő következő ugrással. Amikor a virtuális gépek forgalmat küldenek ennek az útvonalnak a céljához, a virtuálisgép-Equal-Cost (ECMP) útválasztást. Ha azonban egy NVA a többi NVA-hoz rövidebb AS-útvonalhosszúságot küld, mint a többi NVA, az Azure Route Server csak azt az útvonalat programja, amelynél a következő ugrás erre az NVA-re van beállítva a virtuális hálózat virtuális gépei felé.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Megőrzi az Azure Route Server a kapott útvonal BGP-közösségét?

Igen, az Azure Route Server propagálja az útvonalat a BGP-közösségekkel.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Milyen autonómrendszer-számokat (ASN-eket) használhatok?

Saját nyilvános ASN-eket vagy privát ASN-eket használhat a hálózati virtuális berendezésben. Az Azure vagy az IANA által fenntartott tartományok nem használhatók.
Az Azure vagy az IANA az alábbi ASN-eket foglalta le:

* Az Azure által fenntartott ASN-ek:
    * Nyilvános ASN-ek: 8074, 8075, 12076
    * Privát ASN-ek: 65515, 65517, 65518, 65519, 65520
* Az IANA által [fenntartott ASN-ek:](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Használhatok 32 bites (4 bájtos) ASN-eket?

Nem, az Azure Route Server csak a 16 bites (2 bájtos) ASN-eket támogatja.

## <a name="route-server-limits"></a><a name = "limitations"></a>Útválasztási kiszolgáló korlátai

Az Azure Route Serverre a következő korlátozások vonatkoznak (üzemelő példányonként).

| Erőforrás | Korlát |
|----------|-------|
| A támogatott BGP-társviszonyok száma | 8 |
| Az egyes BGP-társ által az Azure Route Serveren meghirdethet útvonalak száma | 200 |
| Az Azure Route Server által az ExpressRoute-ban vagy VPN-átjáróban meghirdetni képes útvonalak száma | 200 |

Ha az NVA a korlátnál több útvonalat hirdet meg, a BGP-munkamenet el lesz dobva. Ha ez történik az átjáróval és az Azure Route Serverrel, megszakad a kapcsolat a helyszíni hálózat és az Azure között. További információ: [Azure-beli virtuális gépek útválasztási problémájának diagnosztizálása.](../virtual-network/diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [konfigurálhatja az Azure Route Servert.](quickstart-configure-route-server-powershell.md)
