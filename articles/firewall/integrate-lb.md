---
title: Az Azure Firewall integrálása az Azure Standard Load Balancerrel
description: A virtuális Azure Firewall azure-standard Load Balancer (nyilvános vagy belső) használatával integrálhatja a virtuális hálózatba.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505904"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Az Azure Firewall integrálása az Azure Standard Load Balancerrel

A virtuális Azure Firewall azure-standard Load Balancer (nyilvános vagy belső) használatával integrálhatja a virtuális hálózatba. 

Az előnyben részesített kialakítás egy belső terheléselosztó integrálása az Azure-tűzfallal, mivel ez egy sokkal egyszerűbb kialakítás. Ha már van üzembe helyezett nyilvános terheléselosztója, és meg akarja tartani, használhatja azt is. Tisztában kell lennie azonban egy aszimmetrikus útválasztási problémával, amely megakadályozhatja a nyilvános terheléselosztót alkalmazó forgatókönyv működését.

További információ a Azure Load Balancer: [Mi a Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Nyilvános Load Balancer

Nyilvános terheléselosztás esetén a terheléselosztás egy nyilvános előtere IP-címmel lesz üzembe va.

### <a name="asymmetric-routing"></a>Aszimmetrikus útválasztás

Az aszimmetrikus útválasztás az a hely, ahol a csomag az egyik útvonalat a célhoz viszi, és egy másik útvonalat a forráshoz való visszatéréskor. Ez a probléma akkor fordul elő, ha egy alhálózat alapértelmezett útvonala a tűzfal magánhálózati IP-címére irányít, és nyilvános terheléselosztást használ. Ebben az esetben a bejövő terheléselelosztási forgalom a nyilvános IP-címen keresztül érkezik, de a visszatérési útvonal a tűzfal magánhálózati IP-címére halad át. Mivel a tűzfal állapot-jellegű, eldobja a visszatérő csomagot, mert a tűzfal nem tud erről a létrehozott munkamenetről.

### <a name="fix-the-routing-issue"></a>Az útválasztási probléma kijavítva

Amikor üzembe helyez egy Azure Firewall alhálózatban, az egyik lépés egy alapértelmezett útvonal létrehozása az alhálózat számára, amely a tűzfal AzureFirewallSubnet alhálózatán található magánhálózati IP-címére irányítja a csomagokat. További információ: [Oktatóanyag: Üzembe helyezés és konfigurálás Azure Firewall a Azure Portal.](tutorial-firewall-deploy-portal.md#create-a-default-route)

Amikor bemutatja a tűzfalat a terheléselosztási forgatókönyvben, azt szeretné, hogy az internetes forgalom a tűzfal nyilvános IP-címével jöjjön be. Innen a tűzfal alkalmazza a tűzfalszabályokat, és a NAT-ket a terheléselosztás nyilvános IP-címére alkalmazza. Itt jelentkezik a probléma. A csomagok a tűzfal nyilvános IP-címére érkeznek, de a magánhálózati IP-címen (az alapértelmezett útvonal használatával) térnek vissza a tűzfalra.
A probléma elkerülése érdekében hozzon létre egy további állomásútvonalat a tűzfal nyilvános IP-címére. A tűzfal nyilvános IP-címére küldött csomagok az interneten keresztül vannak irányítva. Ezzel elkerülhető, hogy az alapértelmezett útvonal a tűzfal magánhálózati IP-címére legyen irányítva.

![Aszimmetrikus útválasztás](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Példa útválasztási táblázatra

A következő útvonalak például a 20.185.97.136 nyilvános IP-címen található tűzfalhoz, a magánhálózati IP-cím pedig a 10.0.1.4 ip-címhez vannak meg.

> [!div class="mx-imgBorder"]
> ![Útvonaltábla](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>PÉLDA NAT-szabályra

A következő példában egy NAT-szabály a 20.185.97.136-os porton keresztüli RDP-forgalmat fordítja le a terheléselosztásra a 20.42.98.220 porton:

> [!div class="mx-imgBorder"]
> ![NAT-szabály](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Állapotminták

Ne feledje, hogy a terheléselosztási készlet gazdagépeien futó webszolgáltatásra van szükség, ha TCP-állapot-mintavételeket használ a 80-as porton, vagy HTTP/HTTPS-mintavételeket.

## <a name="internal-load-balancer"></a>Belső terheléselosztó

Belső terheléselosztással a terheléselosztás egy privát előtere IP-címmel lesz üzembe va.

Ebben a forgatókönyvben nincs aszimmetrikus útválasztási probléma. A bejövő csomagok megérkeznek a tűzfal nyilvános IP-címére, le vannak fordítva a terheléseltöltő magánhálózati IP-címére, majd ugyanezen a visszatérési útvonalon térnek vissza a tűzfal magánhálózati IP-címére.

Így ezt a forgatókönyvet a nyilvános terheléselosztási forgatókönyvhöz hasonlóan helyezheti üzembe, de nincs szükség a tűzfal nyilvános IP-címének gazdagépútvonalra.

A háttérkészlet virtuális gépei kimenő internetkapcsolattal is Azure Firewall. Konfigurál egy felhasználó által megadott útvonalat a virtuális gép alhálózatán a tűzfallal következő ugrásként.


## <a name="additional-security"></a>További biztonság

Az elosztott terhelésű forgatókönyv biztonságának további növelése érdekében használhat hálózati biztonsági csoportokat (NSG-ket).

Létrehozhat például egy NSG-t a háttér-alhálózaton, ahol az elosztott terhelésű virtuális gépek találhatók. Engedélyezze a tűzfal IP-címről/portról érkező bejövő forgalmat.

![Hálózati biztonsági csoporttal](media/integrate-lb/nsg-01.png)

További információ az NSG-kről: [Biztonsági csoportok.](../virtual-network/network-security-groups-overview.md)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhatja a Azure Firewall.](tutorial-firewall-deploy-portal.md)