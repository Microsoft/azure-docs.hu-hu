---
title: Azure Relay GYIK | Microsoft Docs
description: Ez a cikk a Azure Relay szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat tartalmaz.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 83cbc4b02bb8b63878abd046ed2314728546e87a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98250051"
---
# <a name="azure-relay-faqs"></a>Azure Relay GYIK

Ez a cikk a [Azure Relay](https://azure.microsoft.com/services/service-bus/)kapcsolatos gyakori kérdésekre (GYIK) ad választ. Az Azure-díjszabással és-támogatással kapcsolatos általános információkért tekintse meg az [Azure-támogatással kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-relay"></a>Mi az Azure Relay?
A [Azure Relay szolgáltatás](relay-what-is-it.md) megkönnyíti a hibrid alkalmazásokat azáltal, hogy biztonságosabban teszi közzé a vállalati vállalati hálózaton belüli szolgáltatásokat a nyilvános felhőben. A szolgáltatásokat tűzfal-kapcsolatok megnyitása nélkül is elérhetővé teheti, és anélkül, hogy zavaró módosításokat kellene létesíteni a vállalati hálózati infrastruktúrában.

### <a name="what-is-a-relay-namespace"></a>Mi az a Relay-névtér?
A [névtér](relay-create-namespace-portal.md) egy hatókör-tároló, amelyet az alkalmazáson belüli továbbítási erőforrások kezelésére használhat. Létre kell hoznia egy névteret a Relay használatához. Ez a bevezetés első lépéseinek egyike.

### <a name="what-happened-to-service-bus-relay-service"></a>Mi történt a Service Bus Relay szolgáltatással?
A korábban elnevezett Service Bus Relay szolgáltatás már [Azure Relay](service-bus-relay-tutorial.md)néven is ismert. Továbbra is használhatja ezt a szolgáltatást a szokásos módon. A Hibrid kapcsolatok funkció egy olyan szolgáltatás frissített verziója, amely az Azure BizTalk Services-ból lett átültetve. WCF Relay és Hibrid kapcsolatok mindkettő továbbra is támogatott.

## <a name="pricing"></a>Díjszabás
Ez a szakasz a Relay díjszabási struktúrájával kapcsolatos gyakori kérdésekre ad választ. Az Azure- [támogatási GYIK](https://azure.microsoft.com/support/faq/) az általános Azure-díjszabással kapcsolatban is látható. A Relay díjszabásával kapcsolatos információkért tekintse meg a [Service Bus díjszabási részleteit][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hogyan számítjuk fel a díjat a Hibrid kapcsolatokért és a WCF Relayért?
A Relay díjszabásával kapcsolatos információkért tekintse meg a [hibrid kapcsolatok és a WCF Relays][Pricing overview] táblát a Service Bus díjszabása lapon. Az ezen az oldalon feltüntetett árakon kívül a kimenő adatforgalomért az alkalmazás üzembe helyezéséhez használt adatközponton kívül is díjat számítunk fel.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Mennyit kell fizetnem a Hibrid kapcsolatokért?
Íme három példa számlázási forgatókönyv a Hibrid kapcsolatok:

*   1. forgatókönyv:
    *   Egyetlen figyelővel rendelkezik, mint például a Hibrid kapcsolatok Manager egy példánya, amely a teljes hónapra folyamatosan fut.
    *   A hónap során 3 GB-nyi adat küldhető el a hálózaton. 
    *   A teljes díj $5.
*   2. forgatókönyv:
    *   Egyetlen figyelővel rendelkezik, mint például a Hibrid kapcsolatok Manager egy példánya, amely a teljes hónapra folyamatosan fut.
    *   A hónap során 10 GB-nyi adat küldhető el a csatlakozáson keresztül.
    *   A teljes díj $7,50. Ez $5 a kapcsolatok és az első 5 GB + $2,50 a további 5 GB adathoz.
*   3. forgatókönyv:
    *   A Hibrid kapcsolatok Manager két példánya van, a és a B, a teljes hónapra folyamatosan fut.
    *   A hónap során 3 GB-nyi adat küldhető el a kapcsolatok között.
    *   A hónap során 6 GB-nyi adat küldhető a B hálózaton keresztül.
    *   A teljes díj $10,50. Ez $5 a (z) B + $0,50 csatlakoztatásához a + $5-hez (a B-vel létesített hatodik gigabájthoz).


### <a name="how-are-hours-calculated-for-relay"></a>Hogyan számítják ki a továbbítási órákat?

WCF Relay csak a standard szintű névterek esetében érhető el. A továbbítások díjszabása és a [kapcsolatok kvótája](../service-bus-messaging/service-bus-quotas.md) más okból nem módosult. Ez azt jelenti, hogy a továbbítások továbbra is az üzenetek (nem az Operations) és a továbbítási órák száma alapján lesznek felszámítva. További információkért tekintse meg a ["hibrid kapcsolatok és WCF Relays"](https://azure.microsoft.com/pricing/details/service-bus/) táblázatot a díjszabás részletei lapon.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Mi a teendő, ha egynél több figyelő csatlakozik egy adott továbbítóhoz?
Bizonyos esetekben egyetlen továbbítóhoz több csatlakoztatott figyelő is tartozhat. A továbbító akkor tekinthető nyitottnak, ha legalább egy továbbító figyelő csatlakoztatva van hozzá. A figyelők egy nyitott továbbítóhoz való hozzáadása további továbbítási órákat eredményez. A Relay-továbbítók száma (az olyan ügyfelek, amelyek továbbítják vagy elküldik az üzeneteket a továbbításoknak) nem befolyásolják a továbbítási órák kiszámítását.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hogyan számítja ki az üzenetek mérőszámát a WCF-továbbítóknak?
(**Ez csak a WCF-továbbítók esetében érvényes. Az üzenetek nem a Hibrid kapcsolatok díja.**)

Általánosságban elmondható, hogy a továbbítások számlázási üzeneteinek kiszámításához ugyanazt a módszert használja, mint a korábban leírt, felügyelt entitások (várólisták, témakörök és előfizetések) esetében. Van azonban néhány jelentős különbség.

Az üzenet elküldéséhez a rendszer "teljes körű" küldéssel küldi el az üzenetet egy Azure Relaynak. A rendszer nem kezeli küldési műveletként a Azure Relay, amelyet a Relay-figyelő kézbesítése követ. A kérés-válasz stílusú szolgáltatás (legfeljebb 64 KB) egy továbbító figyelővel való meghívása két számlázandó üzenetet eredményez: egy számlázandó üzenet a kérelemhez és egy számlázandó üzenet a válaszhoz (feltéve, hogy a válasz a 64 KB vagy kisebb). Ez különbözik az ügyfél és a szolgáltatás közötti közvetítési várólista használatával. Ha várólistát használ az ügyfél és a szolgáltatás közötti közvetítéshez, ugyanazt a kérelem-válasz típusú mintát kell elküldeni a várólistára, amelyet a várólista és a szolgáltatás felé irányuló kézbesítés is követ. Ezt a választ egy másik várólistára küldi a rendszer, valamint az adott várólistáról az ügyfélnek küldött várólistát/kézbesítést. Ha az egész (legfeljebb 64 KB) értékkel azonos méretű feltételezéseket használ, a közvetített üzenetsor-minta 4 számlázandó üzenetet eredményez. Az üzenetek számát kétszer kell kiszámlázni, hogy ugyanazt a mintát használja, mint a Relay használatával. Természetesen a várólisták használatának előnyei is hasznosak ennek a mintának az elérésére, például a tartósságra és a terheléselosztásra. Ezek az előnyök indokolttá tehetik a további költségeket.

A **netTCPRelay** WCF-kötéssel megnyitott továbbítások nem önálló üzenetekként, hanem a rendszeren keresztül áramló adatfolyamként jelennek meg. Ha ezt a kötést használja, csak a küldő és a figyelő tekintheti meg az elküldött és fogadott üzenetek kialakítását. Az **netTCPRelay** -kötést használó relék esetében az összes adattal adatfolyamként kezeli a számlázandó üzenetek kiszámítását. Ebben az esetben a Service Bus 5 percenként kiszámítja az egyes továbbításokon keresztül küldött vagy fogadott adatmennyiséget. Ezt követően a 64 KB-ra osztja el az összes adatmennyiséget, hogy meghatározza a továbbításhoz tartozó számlázandó üzenetek számát az adott időszakban.

## <a name="quotas"></a>Kvóták
| Kvóta neve | Hatókör |  Jegyzetek | Érték |
| --- | --- | --- | --- |
| Párhuzamos figyelők egy továbbítón |Entitás (hibrid kapcsolatok vagy WCF-továbbító) |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívó kód kivételt kap. |25 |
| Egyidejű továbbítási kapcsolatok egy szolgáltatási névtér összes továbbítási végpontján |Névtér |- |5000 |
| Továbbító végpontok szolgáltatási névtérben |Névtér |- |10,000 |
| Az üzenetek mérete a [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) és a [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) relék számára |Névtér |Az ezeket a kvótákat meghaladó bejövő üzenetek elutasításra kerülnek, és a hívó kód kivételt kap. |64 KB |
| Az üzenetek mérete a [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) és a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) relék számára |Névtér |Nincs korlátozva az üzenetek mérete. |Korlátlan |

### <a name="does-relay-have-any-usage-quotas"></a>A továbbító rendelkezik használati kvótával?
Alapértelmezés szerint bármely felhőalapú szolgáltatás esetében a Microsoft egy összesített havi használati kvótát állít be, amelyet az ügyfél összes előfizetése alapján számítunk fel. Megértettük, hogy az igényeknek megfelelően előfordulhat, hogy túllépik ezeket a korlátokat. Bármikor felveheti a kapcsolatot az ügyfélszolgálattal, így tisztában lehet az igényeivel, és megfelelően módosíthatja ezeket a korlátokat. Service Bus esetében az összesített használati kvóták a következők:

* 5 000 000 000 üzenet
* 2 000 000 továbbítási idő

Habár fenntartjuk a jogot arra, hogy letiltson egy olyan fiókot, amely meghaladja a havi használati kvótát, e-mailes értesítést biztosítunk, és a művelet megkezdése előtt több kísérletet is teszünk az ügyféllel való kapcsolatfelvételre. Azok az ügyfelek, akik túllépik ezeket a kvótákat, továbbra is felelősek a költségekért.

### <a name="naming-restrictions"></a>Elnevezési korlátozások
A továbbítási névtér nevének 6 és 50 karakter közöttinek kell lennie.

## <a name="subscription-and-namespace-management"></a>Előfizetés és névtér kezelése
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan áttelepíteni egy névteret egy másik Azure-előfizetésbe?

Ha egy névteret át szeretne helyezni egy Azure-előfizetésből egy másik előfizetésbe, használhatja a [Azure Portal](https://portal.azure.com) vagy használhatja a PowerShell-parancsokat. Ha egy névteret át szeretne helyezni egy másik előfizetésbe, a névtérnek már aktívnak kell lennie. A parancsokat futtató felhasználónak rendszergazdai felhasználónak kell lennie a forrás-és a cél előfizetésekben.

#### <a name="azure-portal"></a>Azure Portal

Ha a Azure Portal használatával szeretné áttelepíteni Azure Relay névtereket az egyik előfizetésből egy másik előfizetésbe, olvassa el az [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)című témakört. 

#### <a name="powershell"></a>PowerShell

Ha a PowerShell használatával szeretne áthelyezni egy névteret egy Azure-előfizetésből egy másik előfizetésbe, használja a következő parancsokat. A művelet végrehajtásához a névtérnek már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak rendszergazdai felhasználónak kell lennie a forrás-és a cél előfizetésekben.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Melyek a Azure Relay API-k által generált kivételek és a javasolt műveletek?
A gyakori kivételek és a javasolt műveletek leírását a [továbbítási kivételek][Relay exceptions]című témakörben tekintheti meg.

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Mi a közös hozzáférési aláírás, és milyen nyelveket használhatok az aláírások létrehozásához?
A közös hozzáférésű aláírások (SAS) az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmus. További információ a saját aláírások létrehozásáról a Node.js, a PHP, a Python, a Java, a C és a C# nyelveken: [Service Bus hitelesítés közös hozzáférési aláírásokkal][Shared Access Signatures].

### <a name="is-it-possible-to-allow-only-some-relay-endpoints"></a>Lehetséges a továbbítási végpontok engedélyezése?
Igen. A továbbító ügyfél teljes tartománynevek használatával kapcsolatot létesít a Azure Relay szolgáltatással. Az ügyfelek hozzáadhatnak egy olyan bejegyzést a `*.servicebus.windows.net` tűzfalakhoz, amely támogatja a DNS-jóváhagyások listáját.

## <a name="next-steps"></a>Következő lépések
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
