---
title: Gyakori kérdések – Azure Load Balancer
description: Válaszok a Azure Load Balancer kapcsolatos gyakori kérdésekre.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: e9f46b11d9c0b5251ee4d52f64d657926f6f9c5e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222989"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer gyakori kérdések

## <a name="what-types-of-load-balancer-exist"></a>Milyen típusú Load Balancer létezik?
Belső terheléselosztó, amely a VNET és a külső terheléselosztó adatforgalmát kiegyensúlyozza, és egy internetkapcsolattal rendelkező végpont felé irányuló forgalmat egyenlít ki. További információ: [Load Balancer típusok](components.md#frontend-ip-configurations). 

Mindkét típus esetében az Azure egy alapszintű SKU-t és egy standard SKU-t kínál, amely különböző funkcionális, teljesítménybeli, biztonsági és állapot-követési képességekkel rendelkezik. Ezeket a különbségeket az [SKU-összehasonlítási](skus.md) cikkben ismertetjük.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hogyan frissíthetek alapszintről standard Load Balancerra?
Tekintse meg az [alapszintről standard verzióra való frissítést](upgrade-basic-standard.md) egy automatizált parancsfájl és egy Load Balancer SKU verziófrissítésének útmutatója alapján.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Mik a különböző terheléselosztási lehetőségek az Azure-ban?
Tekintse meg a terheléselosztó [technológiai útmutatóját](/azure/architecture/guide/technology-choices/load-balancing-overview)  a rendelkezésre álló terheléselosztási szolgáltatásokhoz és a javasolt felhasználási módokhoz.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Hol találhatók Load Balancer ARM-sablonok?
Tekintse meg a gyakori központi telepítések ARM-sablonjaihoz tartozó Azure Load Balancer rövid útmutatók [listáját](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) .

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Miben különböznek a bejövő NAT-szabályok a terheléselosztási szabályoktól?
A NAT-szabályok olyan háttér-erőforrás megadására szolgálnak, amely a forgalmat irányítja át. Például egy adott terheléselosztó-port konfigurálása egy adott virtuális gépre irányuló RDP-forgalom küldéséhez. A terheléselosztási szabályok segítségével megadhatók a háttér-erőforrások készlete a forgalom átirányításához, valamint a terhelés elosztása az egyes példányok között. Egy terheléselosztó-szabály például átirányíthatja a TCP-csomagokat a terheléselosztó 80-es portjára a webkiszolgálók készletén keresztül.

## <a name="what-is-ip-1686312916"></a>Mi az IP-168.63.129.16?
Az Azure-infrastruktúra Load Balancer címkézett gazdagéphez tartozó virtuális IP-cím, amelyen az Azure Health-Szondák származnak. A háttérbeli példányok konfigurálásakor engedélyezni kell, hogy az adott IP-címről érkező forgalom sikeresen válaszoljon az állapot-próbára. Ez a szabály nem működik együtt a Load Balancer előtér-felületéhez való hozzáféréssel. Ha nem használja a Azure Load Balancer, felülbírálhatja ezt a szabályt. A szolgáltatással kapcsolatos címkékkel kapcsolatban [itt](../virtual-network/service-tags-overview.md#available-service-tags)talál további információt.

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Használhatom a globális VNet-társítást az alapszintű Load Balancerokkal?
Nem. Az alapszintű Load Balancer nem támogatja a globális VNET-társítást. Ehelyett a standard Load Balancer is használhatja. A zökkenőmentes frissítés érdekében tekintse meg az [alapszintről standard verzióra való frissítést](upgrade-basic-standard.md) .

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Hogyan deríthető fel az Azure-beli virtuális gépek által használt nyilvános IP-cím?

A kimenő kapcsolatok nyilvános forrás IP-címének meghatározása számos módon megtörténik. A nyit egy olyan szolgáltatást biztosít, amely a virtuális gép nyilvános IP-címét jeleníti meg.
Az nslookup parancs használatával DNS-lekérdezést küldhet a myip.opendns.com név számára a nyit feloldójának. A szolgáltatás visszaadja a lekérdezés küldéséhez használt forrás IP-címet. Ha a következő lekérdezést futtatja a virtuális gépről, a válasz az adott virtuális géphez használt nyilvános IP-cím:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Hozzáadhatok egy virtuális gépet ugyanabból a rendelkezésre állási csoportból egy Load Balancer különböző háttér-készletekbe?
Nem, ez nem lehetséges.

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Hogyan működnek a kapcsolatok az Azure Storage-ban ugyanabban a régióban?
A fenti forgatókönyvekben a kimenő kapcsolat nem szükséges a virtuális géppel azonos régióban lévő tárolóhoz való csatlakozáshoz. Ha ezt nem szeretné, a fentiekben leírtak szerint használjon hálózati biztonsági csoportokat (NSG). A más régiókban található tároláshoz való kapcsolódáshoz kimenő kapcsolat szükséges. Vegye figyelembe, hogy amikor egy adott régióban található virtuális gépről csatlakozik a tárolóhoz, a tároló diagnosztikai naplóiban található forrás IP-cím egy belső szolgáltatói cím lesz, nem pedig a virtuális gép nyilvános IP-címe. Ha szeretné korlátozni a Storage-fiókhoz való hozzáférést egy vagy több Virtual Network alhálózatban ugyanabban a régióban, akkor a Storage-fiók tűzfalának konfigurálásakor használja [Virtual Network szolgáltatási végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) , és ne a nyilvános IP-címét. A szolgáltatási végpontok konfigurálása után a rendszer a tároló diagnosztikai naplóiban fogja látni a Virtual Network magánhálózati IP-címét, nem pedig a belső szolgáltató címét.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Mik a legjobb gyakorlatok a kimenő kapcsolatok tekintetében?
A standard Load Balancer és a standard nyilvános IP-cím a kimenő kapcsolatok képességeinek és különböző viselkedésének bevezetését mutatja be. Ezek nem azonosak az alapszintű SKU-kal. Ha standard SKU-kal dolgozik a kimenő kapcsolaton, explicit módon meg kell határoznia a standard nyilvános IP-címeket vagy a standard nyilvános Load Balancer. Ez magában foglalja a kimenő kapcsolatok létrehozását belső standard Load Balancer használata esetén. Javasoljuk, hogy mindig használjon kimenő szabályokat egy standard nyilvános Load Balanceron. Ez azt jelenti, hogy ha belső standard Load Balancer használ, meg kell tennie a kimenő kapcsolatok létrehozásához szükséges lépéseket a háttér-készletben lévő virtuális gépekhez, ha kimenő kapcsolatra van szükség. A kimenő kapcsolat kontextusában egyetlen önálló virtuális gép, a rendelkezésre állási csoportban lévő összes virtuális gép a VMSS összes példánya csoportként viselkedik. Ez azt jelenti, hogy ha egy rendelkezésre állási csoport egyetlen virtuális gépe egy szabványos SKU-hoz van társítva, akkor a rendelkezésre állási csoportba tartozó összes virtuálisgép-példány ugyanúgy viselkedik, mintha a standard SKU-hoz társítva van, még akkor is, ha egy adott példány nincs közvetlenül társítva. Ez a viselkedés abban az esetben is megfigyelhető, ha egy önálló virtuális gép több hálózati adapterrel rendelkezik, amelyek egy terheléselosztó számára vannak csatlakoztatva. Ha egy hálózati adaptert önállóként adnak hozzá, akkor ugyanaz lesz a viselkedése. Körültekintően tekintse át a teljes dokumentumot, hogy megismerje az általános fogalmakat, tekintse át [standard Load Balancer](./load-balancer-overview.md) az SKU-ket és a [kimenő szabályokat](load-balancer-outbound-connections.md#outboundrules).
A kimenő szabályok használata lehetővé teszi a kimenő kapcsolatok összes aspektusának részletes szabályozását.
 
## <a name="next-steps"></a>Következő lépések
Ha a kérdés nem szerepel a fentiekben, küldjön visszajelzést erről az oldalról a kérdésével kapcsolatban. Ez egy GitHub-problémát hoz létre a termékért felelős csapat számára, amely biztosítja, hogy az összes értékes vásárlói kérdés megválaszolva legyen.
