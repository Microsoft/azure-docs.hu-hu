---
title: Hálózati biztonság az Azure Event Hubs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hozzáférés a privát végpontokról
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 9503fc26c22d7dbff13c5754288f577b7bb3242f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010996"
---
# <a name="network-security-for-azure-event-hubs"></a>Hálózati biztonság az Azure Event Hubs 
Ez a cikk azt ismerteti, hogyan használható az Azure Event Hubs a következő biztonsági funkciókkal: 

- Szolgáltatáscímkék
- IP-tűzfalszabályok
- Hálózati szolgáltatási végpontok
- Privát végpontok


## <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. A szolgáltatás címkével kapcsolatos további információkért lásd: [szolgáltatási címkék áttekintése](../virtual-network/service-tags-overview.md).

A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](../virtual-network/network-security-groups-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)is meghatározhat hálózati hozzáférés-vezérlést. A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. A szolgáltatási címke nevének (például **EventHub**) megadásával a szabály megfelelő *forrás* vagy *cél* mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

| Szolgáltatáscímke | Cél | Használhat bejövő vagy kimenő adatforgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Kimenő | Igen | Yes |


## <a name="ip-firewall"></a>IP-tűzfal 
Alapértelmezés szerint a Event Hubs névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Ez a funkció olyan helyzetekben hasznos, amikor az Azure Event Hubs csak bizonyos jól ismert helyekről elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)Event Hubst használja, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről érkező forgalmat engedélyezze. 

Az IP-tűzfalszabályok a Event Hubs névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Event Hubs névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

További információ: az [IP-tűzfal konfigurálása az Event hub-hoz](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Hálózati szolgáltatási végpontok
Event Hubs és [Virtual Network (VNet) szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint például a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha úgy van konfigurálva, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végpontra legyen kötve, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózat szempontjából a Event Hubs névterek egy szolgáltatás-végponthoz kötése egy elkülönített hálózati alagutat állít be a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatásba. 

Az eredmény az alhálózathoz és a megfelelő Event Hubs névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van. Ez a viselkedés kivételt jelent. A szolgáltatás végpontjának engedélyezése alapértelmezés szerint engedélyezi a `denyall` szabályt a virtuális hálózathoz társított [IP-tűzfalon](event-hubs-ip-filtering.md) . Adott IP-címeket adhat hozzá az IP-tűzfalon az Event hub nyilvános végponthoz való hozzáférés engedélyezéséhez. 

> [!IMPORTANT]
> A virtuális hálózatok a **standard** és a **dedikált** Event Hubsban támogatottak. Az alapszintű **csomag** nem támogatja.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Azok a megoldások, amelyek feszes és compartmentalized biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a compartmentalized szolgáltatások közötti szegmentálást, továbbra is szükség van kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások olyan szigetelt kommunikációs útvonalakat biztosítanak, amelyekben a felek közötti átmenet során az üzenetek lemezre is írhatók. Az ugyanahhoz a Event Hubs-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból bizalmas Felhőbeli megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek eleve biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat is.

### <a name="bind-event-hubs-to-virtual-networks"></a>Esemény-hubok kötése virtuális hálózatokhoz

A **virtuális hálózati szabályok** a tűzfal biztonsági funkciója, amely azt szabályozza, hogy az Azure Event Hubs-névtér fogadjon-e kapcsolatokat egy adott virtuális hálózati alhálózatból.

Egy Event Hubs névtér egy virtuális hálózathoz kötése kétlépéses folyamat. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** egy virtuális hálózat alhálózatán, és engedélyeznie kell azt a **Microsoft. EventHub** számára a [szolgáltatási végpont áttekintése című](../virtual-network/virtual-network-service-endpoints-overview.md) cikkben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Event Hubs névteret egy **virtuális hálózati szabállyal** kell kötnie.

A virtuális hálózati szabály a Event Hubs névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Event Hubs névtérhez. Event Hubs maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kap hozzáférést az alhálózathoz.

További információ: [virtuális hálózati szolgáltatás-végpontok konfigurálása az Event hub-hoz](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Privát végpontok

Az [Azure Private link Service](../private-link/private-link-overview.md) lehetővé teszi az Azure-szolgáltatások (például az Azure Event Hubs, az Azure Storage és a Azure Cosmos db) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat **privát végpontján** keresztül.

A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

> [!IMPORTANT]
> Ez a funkció mind a **standard** , mind a **dedikált** szint esetében támogatott. Az alapszintű **csomag** nem támogatja.

További információ: [privát végpontok konfigurálása az Event hub-hoz](private-link-service.md)


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Az IP-tűzfal konfigurálása az Event hub esetében](event-hubs-ip-filtering.md)
- [Virtuális hálózati szolgáltatási végpontok konfigurálása az Event hub esetében](event-hubs-service-endpoints.md)
- [Privát végpontok konfigurálása az Event hub esetében](private-link-service.md)