---
title: Azure Service Bus várólisták használata a Ruby használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Ruby-alkalmazásokat egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához.
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85336652"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Gyors útmutató: Service Bus várólisták használata a Ruby használatával

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Ruby-alkalmazásokat egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához. A minták a Rubyban íródnak, és az Azure gem-t használják.

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. [Service Bus üzenetsor létrehozásához kövesse az Azure Portal használata](service-bus-quickstart-portal.md) című témakör lépéseit.
    1. Olvassa el Service Bus **várólisták** gyors **áttekintését** . 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolatok karakterláncának** beolvasása. 

        > [!NOTE]
        > Az oktatóanyagban a Ruby használatával hozzon létre egy **várólistát** a Service Bus névtérben. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Üzenetsor létrehozása
Az **Azure:: ServiceBusService** objektum lehetővé teszi a várólistákkal való munkavégzést. Várólista létrehozásához használja a `create_queue()` metódust. A következő példa létrehoz egy várólistát, vagy kinyomtatja az esetleges hibákat.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Átadhat egy **Azure:: ServiceBus:: várólista** -objektumot további beállításokkal, amelyekkel felülbírálhatja az alapértelmezett üzenetsor-beállításokat, például az üzenetek élettartamát vagy a várólista maximális méretét. Az alábbi példa bemutatja, hogyan állíthatja be a maximális várólista méretét 5 GB-ra és az idő 1 percen belüli élettartamára:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Üzenetek küldése egy várólistára
Ha üzenetet szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás meghívja a `send_queue_message()` metódust az **Azure:: ServiceBusService** objektumon. A (z) Service Bus várólistákból küldött üzenetek az **Azure:: ServiceBus:: BrokeredMessage** objektumok, valamint a szabványos tulajdonságok (például és) egy készlete `label` , amely az `time_to_live` Egyéni alkalmazásspecifikus tulajdonságok tárolására, valamint a tetszőleges alkalmazásadatok törzsére szolgál. Egy alkalmazás beállíthatja az üzenet törzsét úgy, hogy egy sztringet küld az üzenetnek, és a szükséges standard tulajdonságokat az alapértelmezett értékekkel tölti fel.

Az alábbi példa bemutatja, hogyan küldhet tesztüzenet üzenetet a nevű várólistára a következő `test-queue` paranccsal `send_queue_message()` :

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Az üzenetek egy várólistából érkeznek az `receive_queue_message()` **Azure:: ServiceBusService** objektum metódusának használatával. Alapértelmezés szerint az üzenetek az üzenetsor törlése nélkül olvashatók és zárolva lesznek. Az üzenetek a várólistáról való törlését azonban a hamis érték beállításával is törölheti `:peek_lock` . 

Az alapértelmezett viselkedés lehetővé teszi a kétfázisú művelet olvasását és törlését, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóként tárolja azt a későbbi feldolgozáshoz), a metódus meghívásával végrehajtja a fogadási folyamat második szakaszát, `delete_queue_message()` és megadja az üzenet paraméterként való törlését. A `delete_queue_message()` metódus megjelöli az üzenetet, hogy a rendszer felhasználja, és eltávolítja azt a várólistából.

Ha a `:peek_lock` paraméter értéke **hamis**, az olvasás és az üzenet törlése a legegyszerűbb modell lesz, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, az alkalmazás újraindításakor és az üzenetek újrafelhasználásának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Az alábbi példa bemutatja, hogyan fogadhat és dolgozhat fel üzeneteket a használatával `receive_queue_message()` . A példa először fogad és töröl egy üzenetet `:peek_lock` **hamis** értékre, majd egy másik üzenetet kap, majd törli az üzenetet a következő használatával `delete_queue_message()` :

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor az az `unlock_queue_message()` **Azure:: ServiceBusService** objektumon hívhatja meg a metódust. Ez a hívás azt eredményezi, hogy Service Bus az üzenetet a várólistán belül, és elérhetővé teszi azt újra, akár egyazon alkalmazás, akár egy másik alkalmazás.

A várólistán lévő üzenethez is van egy időkorlát társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és lehetővé teszi az újbóli fogadását.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `delete_queue_message()` metódus hívása előtt, akkor az üzenet az újraindításkor újra megjelenik az alkalmazásban. Ezt a folyamatot gyakran nevezik *legalább egyszer feldolgozásra*; Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az `message_id` üzenet tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek között.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, az alábbi hivatkozásokból tudhat meg többet.

* A [várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)áttekintése.
* Látogasson el az [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) adattárára a githubon.

Az ebben a cikkben tárgyalt Azure Service Bus-várólisták és az Azure-várólisták közötti összehasonlítást lásd a [hogyan kell használni az üzenetsor-tárolás a Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) -ben című cikket: [Azure queues és Azure Service Bus Queues – összehasonlítás és kontrasztos](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

