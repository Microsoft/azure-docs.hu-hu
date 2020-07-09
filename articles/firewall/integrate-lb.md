---
title: Az Azure Firewall integrálása az Azure Standard Load Balancerrel
description: Egy Azure Firewall integrálhat egy virtuális hálózatba egy Azure-standard Load Balancer (nyilvános vagy belső).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: 008274c86944b06b168bf52ca501c655bbe78434
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610625"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Az Azure Firewall integrálása az Azure Standard Load Balancerrel

Egy Azure Firewall integrálhat egy virtuális hálózatba egy Azure-standard Load Balancer (nyilvános vagy belső). 

Az előnyben részesített kialakítás egy belső terheléselosztó integrálása az Azure-tűzfallal, mivel ez sokkal egyszerűbb kialakítás. Ha már rendelkezik egy központilag telepített szolgáltatással, és meg szeretné őrizni a helyet, használhat nyilvános Load balancert is. Azonban tisztában kell lennie egy olyan aszimmetrikus útválasztási problémával, amely képes a nyilvános terheléselosztó-forgatókönyvvel való működés megszakítására.

További információ a Azure Load Balancerről: [Mi az Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Nyilvános Load Balancer

Nyilvános terheléselosztó esetén a terheléselosztó nyilvános előtérbeli IP-címmel van üzembe helyezve.

### <a name="asymmetric-routing"></a>Aszimmetrikus útválasztás

Az aszimmetrikus útválasztás azt adja meg, hogy egy csomag hogyan fogadja a célhelyet, és a forráshoz való visszatéréskor egy másik elérési utat vesz fel. Ez a probléma akkor fordul elő, ha egy alhálózat alapértelmezett útvonala a tűzfal magánhálózati IP-címére kerül, és nyilvános Load balancert használ. Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, a visszatérési útvonal azonban a tűzfal magánhálózati IP-címén halad át. Mivel a tűzfal állapot-nyilvántartó, eldobja a visszaadott csomagot, mert a tűzfal nem ismeri az ilyen munkamenetek egy részét.

### <a name="fix-the-routing-issue"></a>Az útválasztási probléma megoldása

Ha egy alhálózaton helyez üzembe egy Azure Firewall, az egyik lépés egy alapértelmezett útvonal létrehozása az alhálózathoz, amely a AzureFirewallSubnet található tűzfal magánhálózati IP-címén keresztül irányítja a csomagokat. További információ: [oktatóanyag: Azure Firewall telepítése és konfigurálása a Azure Portal használatával](tutorial-firewall-deploy-portal.md#create-a-default-route).

Amikor bevezeti a tűzfalat a terheléselosztó-forgatókönyvbe, azt szeretné, hogy az internetes forgalom a tűzfal nyilvános IP-címén legyen elérhető. Innen a tűzfal alkalmazza a tűzfal szabályait, és a csomagokat a terheléselosztó nyilvános IP-címére NAT. A probléma oka. A csomagok a tűzfal nyilvános IP-címére érkeznek, de a magánhálózati IP-címmel térnek vissza a tűzfalra (az alapértelmezett útvonal használatával).
A probléma elkerüléséhez hozzon létre egy további gazdagépi útvonalat a tűzfal nyilvános IP-címéhez. A tűzfal nyilvános IP-címére irányuló csomagok irányítása az interneten keresztül történik. Ezzel elkerülheti az alapértelmezett útvonalat a tűzfal magánhálózati IP-címére.

![Aszimmetrikus útválasztás](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Útválasztási táblázat – példa

Az alábbi útvonalak például a nyilvános IP-20.185.97.136 és a magánhálózati IP-10.0.1.4 tartozó tűzfalakra vonatkoznak.

> [!div class="mx-imgBorder"]
> ![Útvonaltábla](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Példa NAT-szabályra

A következő példában egy NAT-szabály az RDP-forgalmat a 20.185.97.136-re fordítja le a 20.42.98.220-on lévő Load balancerre:

> [!div class="mx-imgBorder"]
> ![NAT-szabály](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Állapotminták

Ne feledje, hogy a terheléselosztó-készletben lévő gazdagépeken futó webszolgáltatással kell rendelkeznie, ha TCP Health-teszteket használ a 80-as vagy a HTTP/HTTPS-alapú mintavételhez.

## <a name="internal-load-balancer"></a>Belső terheléselosztó

Belső terheléselosztó esetén a terheléselosztó saját előtérbeli IP-címmel van üzembe helyezve.

Ez a forgatókönyv nem rendelkezik aszimmetrikus útválasztási problémával. A bejövő csomagok a tűzfal nyilvános IP-címére érkeznek, lefordítva a terheléselosztó magánhálózati IP-címére, majd visszatérnek a tűzfal magánhálózati IP-címére ugyanazzal a visszatérési útvonallal.

Így a nyilvános terheléselosztó-forgatókönyvhöz hasonlóan telepítheti ezt a forgatókönyvet, de nincs szükség a tűzfal nyilvános IP-címének elérési útjára.

## <a name="additional-security"></a>További biztonság

Az elosztott terhelésű forgatókönyvek biztonságának növelése érdekében használhat hálózati biztonsági csoportokat (NSG).

Létrehozhat például egy NSG a háttérbeli alhálózaton, ahol a terheléselosztásos virtuális gépek találhatók. A tűzfal IP-címéről/portjáról származó bejövő forgalom engedélyezése.

![Hálózati biztonsági csoport](media/integrate-lb/nsg-01.png)

További információ a NSG: [biztonsági csoportok](../virtual-network/security-overview.md).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).