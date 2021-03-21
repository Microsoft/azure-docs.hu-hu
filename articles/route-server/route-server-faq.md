---
title: Az Azure Route Serverrel kapcsolatos gyakori kérdések
description: Válaszokat talál az Azure Route Serverrel kapcsolatos gyakori kérdésekre.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485419"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (előzetes verzió) – gyakori kérdések

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Mi az az Azure Route Server?

Az Azure Route Server egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a hálózati virtuális berendezés (NVA) és a virtuális hálózat közötti útválasztás egyszerű kezelését.

### <a name="is-azure-route-server-just-a-vm"></a>Csak egy virtuális gép az Azure Route Server?

Nem. Az Azure Route Server magas rendelkezésre állással tervezett szolgáltatás. Ha egy [Availability Zonest](../availability-zones/az-overview.md)támogató Azure-régióban van üzembe helyezve, akkor a zóna szintű redundancia lesz.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Milyen útválasztási protokollok támogatják az Azure Route Servert?

Az Azure Route Server csak Border Gateway Protocol (BGP) használatát támogatja. A NVA támogatnia kell a Többugrásos külső BGP-t, mert az Azure Route Servert a virtuális hálózat egy dedikált alhálózatán kell telepítenie. Az Ön által választott [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) -nek eltérőnek kell lennie az Azure Route-kiszolgálótól, amikor a BGP-t KONFIGURÁLJA a NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Az Azure Route Server irányítja az adatforgalmat a saját NVA és a saját virtuális gépek között?

Nem. Az Azure Route Server csak BGP-útvonalakat cserél a NVA. Az adatforgalom közvetlenül a NVA a kiválasztott virtuális gépre, és közvetlenül a virtuális gépről a NVA kerül.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Ha az Azure Route Server ugyanazt az útvonalat kapja több NVA, akkor az útvonal összes példányát (de mindegyiket egy másik következő ugrással) fogja programozni a virtuális hálózatban lévő virtuális gépekre?

Igen, csak akkor, ha az útvonal megegyezik az elérési út hosszával. Ha a virtuális gépek forgalmat küldenek az útvonal célhelyére, a virtuálisgép-gazdagépek Equal-Cost többutas (ECMP) útválasztást tesznek elérhetővé. Ha azonban az egyik NVA az útvonalat rövidebb, mint a többi NVA, akkor az útvonalat is elküldheti. Az Azure Route Server csak azt az útvonalat fogja programozni, amelyen a következő ugrás van beállítva erre a NVA a virtuális hálózatban lévő virtuális gépekre.

### <a name="does-azure-route-server-support-vnet-peering"></a>Támogatja az Azure Route Server a VNet-társítást?

Igen. Ha olyan VNet, amely az Azure Route Servert egy másik VNet üzemelteti, és engedélyezi a távoli átjáró használatát a VNet. Az Azure Route Server megismeri a VNet, és elküldi őket az összes társ NVA.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Milyen autonóm rendszerszámokat (ASN) használhatok?

Saját nyilvános ASN vagy saját ASN is használhat a hálózati virtuális berendezésben. Az Azure vagy az IANA által fenntartott tartományok nem használhatók.
Az Azure vagy az IANA az alábbi ASN foglalja le:

* Az Azure által fenntartott ASN:
    * Nyilvános ASN-ek: 8074, 8075, 12076
    * Privát ASN-ek: 65515, 65517, 65518, 65519, 65520
* [Az IANA által fenntartott](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)ASN:
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Használhatok 32 bites (4 bájtos) ASN?

Nem, az Azure Route Server csak a 16 bites (2 bájtos) ASN támogatja.

## <a name="route-server-limits"></a><a name = "limitations"></a>Útvonal-kiszolgáló korlátai

Az Azure Route Server a következő korlátozásokkal rendelkezik (üzemelő példányok esetében).

| Erőforrás | Korlát |
|----------|-------|
| Támogatott BGP-társak száma | 8 |
| Az egyes BGP-társak által az Azure Route Server-be hirdetett útvonalak száma | 200 |
| Az Azure Route Server által a ExpressRoute-vagy VPN-átjáróra hirdetett útvonalak száma | 200 |

Ha a NVA több útvonalat hirdet meg a korlátnál, a BGP-munkamenet el lesz dobva. Ha ez történik az átjáróval és az Azure Route Serverrel, akkor a helyszíni hálózatról az Azure-ba való kapcsolódás elvész. További információ: Azure-beli [virtuális gépek útválasztási problémájának diagnosztizálása](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [konfigurálhatja az Azure Route Servert](quickstart-configure-route-server-powershell.md).
