---
title: 'Gyors útmutató: Service Bus témák használata (Ruby)'
description: 'Gyors útmutató: Service Bus témakörök és előfizetések használata az Azure-ban. A kód minták a Ruby-alkalmazásokhoz íródnak.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067562"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata a Ruby használatával
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk azt ismerteti, hogyan használhatók a Ruby-alkalmazásokból származó Service Bus témakörök és előfizetések. A tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése egy témakörnek 
- Üzenetek fogadása egy előfizetésből
- Témakörök és előfizetések törlése


## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörhöz](service-bus-quickstart-topics-subscriptions-portal.md) , és hozzon létre egy Service Bus **névteret** , és szerezze be a **kapcsolódási karakterláncot**. 

    > [!NOTE]
    > Ebben a rövid útmutatóban a **Ruby** használatával hozzon létre egy **témakört** és egy **előfizetést** a témakörhöz. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
Az **Azure:: ServiceBusService** objektum lehetővé teszi a témákkal való munkát. A következő kód egy **Azure:: ServiceBusService** objektumot hoz létre. Témakör létrehozásához használja a `create_topic()` metódust. A következő példa létrehoz egy témakört, vagy kinyomtatja az esetleges hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Az **Azure:: ServiceBus:: topic** objektumot további beállításokkal is átadhatja, amelyek lehetővé teszik az alapértelmezett témakör-beállítások, például az üzenet élettartamának vagy a várólista maximális méretének felülbírálását. Az alábbi példa azt mutatja be, hogy a várólista maximális mérete 5 GB és idő 1 percre van állítva:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Előfizetések létrehozása
A témakör-előfizetések az **Azure:: ServiceBusService** objektummal is létrejönnek. Az előfizetések neve és egy opcionális szűrő, amely korlátozza az előfizetés virtuális várólistára kézbesített üzenetek készletét.

Alapértelmezés szerint az előfizetések állandóak. A rendszer addig továbbra is fennáll, amíg meg nem történik, vagy a hozzájuk társított témakör törölve lett. Ha az alkalmazás logikát tartalmaz egy előfizetés létrehozásához, először ellenőrizze, hogy az előfizetés már létezik-e a getSubscription metódussal.

Az előfizetéseket a [AutoDeleteOnIdle tulajdonság](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)beállításával automatikusan törölheti.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs megadva szűrő az új előfizetés létrehozásakor, a rendszer a **MatchAll** szűrőt (alapértelmezett) használja. A **MatchAll** szűrő használatakor a témakörben közzétett összes üzenet az előfizetés virtuális várólistáján lesz elhelyezve. A következő példa létrehoz egy "All-messages" nevű előfizetést, és az alapértelmezett **MatchAll** szűrőt használja.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Meghatározhatja azokat a szűrőket is, amelyek segítségével megadhatja, hogy egy adott előfizetésen belül mely üzenetek jelenjenek meg a témakörben.

Az előfizetések által támogatott legrugalmasabb típusú szűrő az **Azure:: ServiceBus:: SqlFilter**, amely a SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért tekintse át a [SqlFilter](service-bus-messaging-sql-filter.md) szintaxisát.

Az előfizetéshez az `create_rule()` **Azure:: ServiceBusService** objektum metódusának használatával adhat hozzá szűrőket. Ez a módszer lehetővé teszi új szűrők hozzáadását egy meglévő előfizetéshez.

Mivel az alapértelmezett szűrőt a rendszer automatikusan alkalmazza az összes új előfizetésre, először el kell távolítania az alapértelmezett szűrőt, vagy a **MatchAll** felülbírálja az esetlegesen megadható egyéb szűrőket. Az alapértelmezett szabályt az `delete_rule()` **Azure:: ServiceBusService** objektum metódusának használatával távolíthatja el.

A következő példa egy "magas szintű üzenet" nevű előfizetést hoz létre egy **Azure:: ServiceBus:: SqlFilter** , amely csak olyan üzeneteket jelöl ki, amelyek `message_number` 3. nagyobb egyéni tulajdonsággal rendelkeznek:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Hasonlóképpen, a következő példa létrehoz egy nevű előfizetést `low-messages` egy **Azure:: ServiceBus:: SqlFilter** , amely csak olyan üzeneteket jelöl `message_number` ki, amelyek tulajdonsága kisebb vagy egyenlő, mint 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Ha a rendszer elküldi az üzenetet `test-topic` , a rendszer mindig kézbesíti a témakör-előfizetésre előfizetett fogadóknak `all-messages` , és szelektíven kézbesíti azokat a fogadóknak, akik előfizetettek a `high-messages` és `low-messages` témakör-előfizetésekre (az üzenet tartalmától függően).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ha üzenetet szeretne küldeni egy Service Bus témakörnek, az alkalmazásnak a metódust kell használnia `send_topic_message()` az **Azure:: ServiceBusService** objektumon. A Service Bus témakörökbe küldött üzenetek az **Azure:: ServiceBus:: BrokeredMessage** objektumok példányai. **Azure:: ServiceBus:: a BrokeredMessage** objektumok szabványos tulajdonságokkal rendelkeznek (például `label` és `time_to_live` ), az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgáló szótár, valamint egy karakterlánc-adattörzs. Egy alkalmazás beállíthatja az üzenet törzsét úgy, hogy karakterlánc-értéket továbbít a `send_topic_message()` metódusnak, és a szükséges standard tulajdonságokat az alapértelmezett értékek szerint tölti ki.

Az alábbi példa bemutatja, hogyan küldhet öt tesztüzenet a következőnek: `test-topic` . Az `message_number` egyes üzenetek egyéni tulajdonságának értéke a hurok iterációjában változik (ez határozza meg, hogy melyik előfizetés kapja meg):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Az üzenetek az `receive_subscription_message()` **Azure:: ServiceBusService** objektum metódusának használatával érkeznek az előfizetésből. Alapértelmezés szerint az üzenetek olvasási (csúcs) és zárolva vannak anélkül, hogy törölné az előfizetésből. Az üzenetet az előfizetésből olvashatja és törölheti, ha a `peek_lock` beállítást **hamis** értékre állítja.

Az alapértelmezett viselkedés lehetővé teszi a kétfázisú művelet olvasását és törlését, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), a metódus meghívásával végrehajtja a fogadási folyamat második szakaszát, `delete_subscription_message()` és megadja az üzenet paraméterként való törlését. A `delete_subscription_message()` metódus felhasználja az üzenetet, és eltávolítja azt az előfizetésből.

Ha a `:peek_lock` paraméter **hamis** értékre van beállítva, az olvasás és az üzenet törlése a legegyszerűbb modell lesz, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. Vegyünk egy olyan forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, akkor az alkalmazás újraindításakor és az üzenetek újbóli használatának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Az alábbi példa bemutatja, hogyan fogadhatók és dolgozhatók fel az üzenetek a használatával `receive_subscription_message()` . A példa először fogad és töröl egy üzenetet az `low-messages` előfizetésből `:peek_lock` **hamis** értékre állítva, majd egy másik üzenetet kap, `high-messages` majd törli az üzenetet a következő használatával `delete_subscription_message()` :

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor az az `unlock_subscription_message()` **Azure:: ServiceBusService** objektumon hívhatja meg a metódust. Ennek hatására Service Bus az üzenet zárolásának feloldására az előfizetésen belül, és elérhetővé teheti, hogy ugyanazt a fogyasztó vagy egy másik alkalmazás használja.

Az előfizetésen belül zárolt üzenethez is tartozik időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi azt újra.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `delete_subscription_message()` metódus hívása előtt, akkor az üzenet az újraindításkor újra megjelenik az alkalmazásban. Általában legalább egyszer kell meghívni *a feldolgozásra*; Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez a logika gyakran az `message_id` üzenet tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek között.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A témakörök és az előfizetések állandóak, kivéve, ha a [AutoDeleteOnIdle tulajdonság](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) be van állítva. A [Azure Portal][Azure portal] vagy programozott módon törölhetők. Az alábbi példa bemutatja, hogyan törölheti a nevű témakört `test-topic` .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan törölheti a nevű előfizetést `high-messages` a `test-topic` témakörből:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte Service Bus témakörök alapjait, kövesse az alábbi hivatkozásokat további információért.

* Lásd: [várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md).
* Az [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) API-referenciája.
* Látogasson el az [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) adattárára a githubon.

[Azure portal]: https://portal.azure.com