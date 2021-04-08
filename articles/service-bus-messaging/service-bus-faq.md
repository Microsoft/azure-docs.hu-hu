---
title: Azure Service Bus gyakori kérdések (GYIK) | Microsoft Docs
description: Ez a cikk a Azure Service Bus kapcsolatos gyakori kérdések (GYIK) néhány válaszát tartalmazza.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693400"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus – gyakran ismételt kérdések (GYIK)

Ez a cikk a Microsoft Azure Service Busokkal kapcsolatos gyakori kérdéseket tárgyalja. Az Azure- [támogatási GYIK](https://azure.microsoft.com/support/faq/) általános Azure-díjszabást és támogatási információkat is talál.


## <a name="general-questions-about-azure-service-bus"></a>Általános kérdések a Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?
A [Azure Service Bus](service-bus-messaging-overview.md) egy aszinkron üzenetkezelő felhőalapú platform, amely lehetővé teszi a leválasztott rendszerek közötti adatküldést. A Microsoft ezt a szolgáltatást szolgáltatásként kínálja, ami azt jelenti, hogy nem szükséges saját hardvert üzemeltetni a használatához.

### <a name="what-is-a-service-bus-namespace"></a>Mi az Service Bus névtér?
A [névtér](service-bus-create-namespace-portal.md) egy hatókör-tárolót biztosít az alkalmazáson belüli Service Bus erőforrások kezeléséhez. A névtér létrehozása szükséges a Service Bus használatához, és az első lépések egyike.

### <a name="what-is-an-azure-service-bus-queue"></a>Mi az Azure Service Bus üzenetsor?
A [Service Bus üzenetsor](service-bus-queues-topics-subscriptions.md) olyan entitás, amelyben az üzenetek tárolódnak. A várólisták akkor hasznosak, ha több alkalmazással vagy egy elosztott alkalmazás több részével kell kommunikálni egymással. A várólista hasonló ahhoz a terjesztési központhoz, amelyben több termék (üzenet) érkezett, majd az adott helyről lesz küldve.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Mik azok a Azure Service Bus témakörök és előfizetések?
A témakörök üzenetsorként és több előfizetés használata esetén is megjeleníthetők, így gazdagabb üzenetkezelési modellvé válnak. lényegében egy-a-többhöz kommunikációs eszköz. Ez a közzétételi/előfizetési modell (vagy a *pub/sub*) lehetővé teszi egy alkalmazás számára, hogy több előfizetéssel rendelkező témakörhöz üzenetet küldjön, hogy több alkalmazás fogadja az üzenetet.

### <a name="what-is-a-partitioned-entity"></a>Mi az a particionált entitás?
Egy hagyományos üzenetsor vagy témakör kezelése egyetlen Message Broker által történik, és egy üzenetkezelő tárolóban tárolódik. A csak az alapszintű és a standard szintű üzenetkezelési szinten támogatott, a [particionált üzenetsor vagy a témakör](service-bus-partitioning.md) több üzenet-átvitelszervező által kezelhető, és több üzenetkezelési tárolóban tárolódik. Ez a funkció azt jelenti, hogy egy particionált üzenetsor vagy témakör teljes átviteli sebességét már nem korlátozza egyetlen Message Broker vagy üzenetküldési tároló teljesítménye. Emellett az üzenetküldési tároló átmeneti kimaradása nem jeleníti meg a particionált üzenetsor vagy témakör nem érhető el.

Particionált entitások használata esetén a rendezés nem biztosítható. Abban az esetben, ha egy partíció nem érhető el, továbbra is küldhet és fogadhat üzeneteket a többi partícióról.

 A particionált entitások már nem támogatottak a [Premium SKU](service-bus-premium-messaging.md)-ban. 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Hol tárolja Azure Service Bus az adattárolást?
A standard szintű Azure Service Bus Azure SQL Database a háttérbeli tárolási réteghez. A Dél-Brazília és Délkelet-Ázsia kivételével minden régió esetében az adatbázis biztonsági mentése egy másik régióban (általában az Azure párosított régióban) található. A Dél-Brazília és Délkelet-ázsiai régiók esetében az adatbázis biztonsági másolatait ugyanabban a régióban tárolják, hogy megfeleljenek az ezen régiókra vonatkozó adattárolási követelményeknek.

A prémium szintű Azure Service Bus a kiválasztott régiókban tárolja a metaadatokat és az adatokat. Ha a Geo-vész-helyreállítás egy Azure Service Bus Premium-névtérre van beállítva, a metaadatok át lesznek másolva a kiválasztott másodlagos régióba.


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Milyen portokat kell megnyitni a tűzfalon? 
Az üzenetek küldéséhez és fogadásához a következő protokollokat használhatja Azure Service Bus:

- Advanced Message Queueing Protocol 1,0 (AMQP)
- 1,1 Hypertext Transfer Protocol TLS-vel (HTTPS)

Az alábbi táblázat tartalmazza azokat a kimenő TCP-portokat, amelyeket meg kell nyitni, hogy a protokollok használatával kommunikáljanak a Azure Service Bus:

| Protokoll | Port | Részletek | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP TLS-vel. Lásd: [AMQP protokoll – útmutató](service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Ez a port a HTTP/REST API és a AMQP-WebSockets esetében használatos |

A kimenő kommunikációhoz általában a HTTPS-portra van szükség, ha a AMQP a 5671-as porton keresztül használja, mivel az ügyfél SDK-k több felügyeleti műveletet hajtanak végre, és a tokenek beszerzése Azure Active Directory (ha használatban van) HTTPS protokollon keresztül történik. 

A hivatalos Azure SDK-k általában az AMQP protokollt használják a Service Bus üzenetek küldésére és fogadására. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

A .NET-keretrendszer régebbi WindowsAzure. ServiceBus csomagja lehetőséget biztosít az örökölt "Service Bus Messaging Protocol" (SBMP) használatára, más néven "NetMessaging". Ez a protokoll a 9350-9354-es TCP-portot használja. A csomag alapértelmezett módja annak automatikus észlelése, hogy a portok elérhetők-e a kommunikációhoz, és az 443-as porton keresztül a TLS-t használó WebSockets-re váltson, ha ez nem így van. Felülbírálhatja ezt a beállítást, és kényszerítheti ezt a módot úgy, hogy a `Https` beállítás [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) állítja be [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) , amely globálisan vonatkozik az alkalmazásra.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Milyen IP-címeket kell hozzáadni az engedélyezési listához?
Az alábbi lépéseket követve megkeresheti a kapcsolatok listájához hozzáadandó megfelelő IP-címeket:

1. Futtassa a következő parancsot egy parancssorból: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Jegyezze fel a visszaadott IP-címet `Non-authoritative answer` . 

Ha a **zóna redundanciát** használja a névtérhez, néhány további lépést is végre kell hajtania: 

1. Először futtassa az nslookupt a névtéren.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Jegyezze fel a nevet a **nem mérvadó válasz** szakaszban, amely az alábbi formátumok egyike: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Futtassa az nslookupt mindegyikhez az S1, az S2 és az S3 utótaggal a három rendelkezésre állási zónában futó mindhárom példány IP-címeinek lekéréséhez. 

    > [!NOTE]
    > A parancs által visszaadott IP-cím `nslookup` nem statikus IP-cím. Azonban állandó marad, amíg a mögöttes központi telepítést nem törlik, vagy áthelyezik egy másik fürtre.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Hol találhatom meg az ügyfél által a névtérbe küldött/fogadott üzenetek IP-címét? 
Nem naplózjuk a névtérbe irányuló üzeneteket küldő vagy fogadó ügyfelek IP-címeit. Kulcsok újragenerálása annak érdekében, hogy az összes meglévő ügyfél nem tudja hitelesíteni és áttekinteni az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)beállításait, hogy csak az engedélyezett felhasználók vagy alkalmazások férhessenek hozzá a névtérhez. 

Ha **prémium** szintű névteret használ, használja az [IP-szűrést](service-bus-ip-filtering.md), a [virtuális hálózati szolgáltatási végpontokat](service-bus-service-endpoints.md)és a [magánhálózati végpontokat](private-link-service.md) a névtérhez való hozzáférés korlátozására. 

## <a name="best-practices"></a>Ajánlott eljárások
### <a name="what-are-some-azure-service-bus-best-practices"></a>Milyen Azure Service Bus ajánlott eljárások?
Lásd: [ajánlott eljárások a teljesítmény fejlesztéséhez Service Bus használatával][Best practices for performance improvements using Service Bus] – ez a cikk azt ismerteti, hogyan optimalizálható a teljesítmény az üzenetek cseréjekor.

### <a name="what-should-i-know-before-creating-entities"></a>Mit kell tudni az entitások létrehozása előtt?
A várólista és a témakör következő tulajdonságai nem változtathatók meg. Vegye figyelembe ezt a korlátozást az entitások kiépítésekor, mivel ezek a tulajdonságok nem módosíthatók új helyettesítő entitás létrehozása nélkül.

* Particionálás
* Munkamenetek
* Duplikálás észlelése
* Expressz entitás

## <a name="pricing"></a>Díjszabás
Ez a szakasz a Service Bus árképzési struktúrával kapcsolatos gyakori kérdésekre ad választ.

A [Service Bus díjszabása és a számlázási](https://azure.microsoft.com/pricing/details/service-bus/) cikk a Service Bus számlázási mérőszámait ismerteti. A Service Bus díjszabási lehetőségeivel kapcsolatos további információkért tekintse meg a [Service Bus díjszabási részleteit](https://azure.microsoft.com/pricing/details/service-bus/).

Az Azure- [támogatási GYIK](https://azure.microsoft.com/support/faq/) általános Azure-díjszabási információit is felkeresheti. 

### <a name="how-do-you-charge-for-service-bus"></a>Hogyan díjköteles a Service Bus?
A Service Bus díjszabásával kapcsolatos információkért tekintse meg a [Service Bus díjszabási részleteit][Pricing overview]. A feljegyzett díjak mellett a kimenő adatforgalomért is fizetnie kell azon adatközponton kívül, amelyben az alkalmazás üzembe lett helyezve.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Milyen Service Bus használatára vonatkozik az adatátvitel? Mi nem?
Az adott Azure-régión belüli adatforgalom díjmentesen, valamint a bejövő adatforgalomban is elérhető. A régión kívüli adatforgalomra a kimenő forgalom díja vonatkozik, amely [itt](https://azure.microsoft.com/pricing/details/bandwidth/)található.

### <a name="does-service-bus-charge-for-storage"></a>Service Bus díjat a tárterületért?
Nem. A Service Bus nem számít fel díjat a tárterületért. Van azonban egy kvóta, amely korlátozza a várólistára vagy témakörre vonatkozó maximálisan megőrzött adatmennyiséget. Tekintse meg a következő gyakori kérdéseket.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Service Bus standard névtérrel rendelkezem. Miért jelenik meg a díjak a (z) $system erőforráscsoport alatt?
Azure Service Bus nemrég frissítettük a számlázási összetevőket. Ennek a változásnak a miatt, ha Service Bus standard névtérrel rendelkezik, a (z) "$system" erőforráscsoport alatt a "/Subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" erőforráshoz tartozó sorok jelenhetnek meg.

Ezek a díjak az Azure-előfizetések által Service Bus standard névteret kiépített alapdíj alapján jelennek meg. 

Fontos megjegyezni, hogy ezek a díjak nem újak, azaz az előző számlázási modellben is léteztek. Az egyetlen változás, hogy most már a "$system" alatt vannak felsorolva. Ez az új számlázási rendszer megkötései miatt történik, amelyek az előfizetések szintjén felszámított díjakat, nem az adott erőforráshoz kötődnek, a "$system" erőforrás-azonosító alatt.

## <a name="quotas"></a>Kvóták

Service Bus korlátok és kvóták listáját a [Service Bus kvóták áttekintésében][Quotas overview]találhatja meg.

### <a name="how-to-handle-messages-of-size--1-mb"></a>1 MB méretű üzenetek kezelése >
Service Bus Messaging Services (várólisták és témakörök/előfizetések) lehetővé teszik, hogy az alkalmazás legfeljebb 256 KB-os (standard szintű) vagy 1 MB (prémium szint) méretű üzeneteket küldjön. Ha 1 MB-nál nagyobb méretű üzenetekkel dolgozik, használja a [blogbejegyzésben](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)ismertetett jogcím-ellenőrzési mintát.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem lehet névteret létrehozni egy másik előfizetésből való törlés után? 
Ha töröl egy névteret egy előfizetésből, várjon 4 órát, mielőtt újra létrehozza azt ugyanazzal a névvel egy másik előfizetésben. Ellenkező esetben a következő hibaüzenet jelenhet meg: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Melyek a Azure Service Bus API-k által generált kivételek és a javasolt műveletek?
A lehetséges Service Bus kivételek listáját lásd: [kivételek áttekintése][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Mi a közös hozzáférési aláírás, és milyen nyelveket támogat az aláírás létrehozása?
A közös hozzáférésű aláírások az SHA-256 biztonságos kivonatokon vagy URI-kon alapuló hitelesítési mechanizmusok. További információ a saját aláírások létrehozásáról Node.js, PHP, Java, Python és C# nyelven: [közös hozzáférésű aláírások][Shared Access Signatures] című cikk.

## <a name="subscription-and-namespace-management"></a>Előfizetés és névtér kezelése
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hogyan áttelepíteni egy névteret egy másik Azure-előfizetésbe?

A névteret áthelyezheti egyik Azure-előfizetésből egy másikba, a [Azure Portal](https://portal.azure.com) vagy a PowerShell-parancsok használatával. A művelet végrehajtásához a névtérnek már aktívnak kell lennie. A parancsokat végrehajtó felhasználónak a forrás-és a cél előfizetések rendszergazdájának kell lennie.

#### <a name="portal"></a>Portál

Ha a Azure Portal segítségével szeretné áttelepíteni Service Bus névtereket egy másik előfizetésbe, kövesse az [alábbi](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)utasításokat. 

#### <a name="powershell"></a>PowerShell

A PowerShell-parancsok következő sora egy névteret helyez át egy másik Azure-előfizetésből. A művelet végrehajtásához a névtérnek már aktívnak kell lennie, és a PowerShell-parancsokat futtató felhasználónak a forrás-és a cél előfizetések rendszergazdájának kell lennie.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>Le lehet tiltani a TLS 1,0 vagy a 1,1 Service Bus névtereken?
Nem. Nem lehet letiltani a TLS 1,0 vagy a 1,1 Service Bus névterekben. A Service Bushoz kapcsolódó ügyfélalkalmazások esetében használja a TLS 1,2-es vagy újabb verzióját. További információ: [a TLS 1,2 használatának kényszerítése Azure Service Bus-Microsoft technikai Közösséggel](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912).

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Service Busről, tekintse meg a következő cikkeket:

* [Azure Service Bus Premium bemutatása (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium (Channel9) bemutatása](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus áttekintése](service-bus-messaging-overview.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
