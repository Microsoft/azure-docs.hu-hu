---
title: Azure Service Bus hálózati biztonsága
description: Ez a cikk a hálózati biztonsági funkciókat, például a szolgáltatási címkéket, az IP-tűzfalszabályok, a szolgáltatási végpontokat és a magánhálózati végpontokat ismerteti.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: db0dd89d1f902699c27b724609505ba681757454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310465"
---
# <a name="network-security-for-azure-service-bus"></a>Azure Service Bus hálózati biztonsága 
Ez a cikk azt ismerteti, hogyan használhatók a következő biztonsági szolgáltatások a Azure Service Bus használatával: 

- Szolgáltatáscímkék
- IP-tűzfalszabályok
- Hálózati szolgáltatási végpontok
- Privát végpontok


## <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. A szolgáltatás címkével kapcsolatos további információkért lásd: [szolgáltatási címkék áttekintése](../virtual-network/service-tags-overview.md).

A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](../virtual-network/network-security-groups-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)is meghatározhat hálózati hozzáférés-vezérlést. A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. A szolgáltatási címke nevének (például **ServiceBus**) megadásával a szabály megfelelő *forrás* vagy *cél* mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

| Szolgáltatáscímke | Cél | Használhat bejövő vagy kimenő adatforgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus a prémium szintű szolgáltatási szintet használó forgalom. | Kimenő | Igen | Yes |


> [!NOTE]
> A szolgáltatási címkéket csak a **prémium** szintű névterek esetében használhatja. Ha **standard** névteret használ, használja a következő parancs futtatásakor megjelenő IP-címet: `nslookup <host name for the namespace>` . Példa: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>IP-tűzfal 
Alapértelmezés szerint a Service Bus névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Ez a funkció olyan helyzetekben hasznos, amikor a Azure Service Bus csak bizonyos jól ismert helyekről lehet elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például a Service Bus az [Azure Express Route] [Express-Route] használatával használja, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről vagy a vállalati NAT-átjáró címeiről érkező forgalmat engedélyezze. 

Az IP-tűzfalszabályok a Service Bus névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Service Bus névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

További információ: az [IP-tűzfal konfigurálása Service Bus névtérhez](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Hálózati szolgáltatási végpontok
Service Bus és [Virtual Network (VNet) szolgáltatás-végpontok](service-bus-service-endpoints.md) integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha úgy konfigurálták, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végponthoz legyen kötve, a megfelelő Service Bus névtér többé nem fogadja el a forgalmat bárhonnan, de engedélyezett virtuális hálózat (ok) ból. A virtuális hálózat szempontjából a Service Bus névtér kötése egy szolgáltatási végponthoz egy elkülönített hálózati alagutat konfigurál a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatáshoz.

Az eredmény az alhálózathoz és a megfelelő Service Bus névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van.

> [!IMPORTANT]
> A virtuális hálózatok csak [prémium szintű](service-bus-premium-messaging.md) Service Bus névterek esetén támogatottak.
> 
> Ha a VNet szolgáltatásbeli végpontokat Service Bus használatával használja, ezeket a végpontokat nem ajánlott olyan alkalmazásokban engedélyezni, amelyek a standard és a prémium szintű Service Bus névtereket keverik. Mivel a standard szint nem támogatja a virtuális hálózatok. A végpont csak a prémium szintű névterek számára van korlátozva.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

A szigorú és compartmentalized biztonságot igénylő megoldások, valamint a virtuális hálózatok alhálózatai biztosítják a compartmentalized szolgáltatások közötti szegmentálást, általánosságban továbbra is szükség van a kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások teljes mértékben szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek a felek közötti váltáskor lemezre is írhatók. Az ugyanahhoz a Service Bus-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból bizalmas Felhőbeli megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek eleve biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat is.

### <a name="bind-service-bus-to-virtual-networks"></a>Service Bus kötése virtuális hálózatokhoz

A *virtuális hálózati szabályok* a tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Service Bus-kiszolgáló egy adott virtuális hálózati alhálózat kapcsolatait fogadja-e.

A Service Bus névterek virtuális hálózathoz való kötése kétlépéses folyamat. Először létre kell hoznia egy **Virtual Network szolgáltatási végpontot** egy Virtual Network alhálózaton, és engedélyeznie kell azt a **Microsoft. ServiceBus** számára a [szolgáltatási végpont áttekintése című](service-bus-service-endpoints.md)részben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Service Bus névteret egy **virtuális hálózati szabállyal** kell kötnie.

A virtuális hálózati szabály a Service Bus névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Service Bus névtérhez. Service Bus maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kapnak hozzáférést az alhálózathoz.

További információ: [virtuális hálózati szolgáltatási végpontok konfigurálása Service Bus névtérhez](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Privát végpontok

Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Service Bus, az Azure Storage és a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-és partneri szolgáltatások elérését a virtuális hálózat **privát végpontján** keresztül.

A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [Mi az az Azure Private link?](../private-link/private-link-overview.md)

> [!NOTE]
> Ez a funkció a Azure Service Bus **Premium** szintjével támogatott. A prémium szintű csomaggal kapcsolatos további információkért tekintse meg a [prémium és standard szintű üzenetkezelési szintek Service Busét](service-bus-premium-messaging.md) ismertető cikket.


További információ: [privát végpontok konfigurálása Service Bus névtérhez](private-link-service.md)


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Az IP-tűzfal konfigurálása Service Bus névtérhez](service-bus-ip-filtering.md)
- [Virtuális hálózati szolgáltatási végpontok konfigurálása Service Bus névtérhez](service-bus-service-endpoints.md)
- [Privát végpontok konfigurálása Service Bus névtérhez](private-link-service.md)