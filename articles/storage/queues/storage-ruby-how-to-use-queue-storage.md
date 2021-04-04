---
title: A Queue Storage használata a Ruby-Azure Storage-ból
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue Storage használatával, valamint hogyan szúrhat be, kérhet le és törölhet üzeneteket. A Rubyban írt minták.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587662"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>A Queue Storage használata a Ruby használatával

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket a Microsoft Azure Queue Storage szolgáltatás használatával. A minták a Ruby Azure API használatával íródnak. A tárgyalt forgatókönyvek közé tartozik például a várólista-üzenetek **beszúrása**, **bepillantása**, **beolvasása** és **törlése** , valamint a **várólisták létrehozása és törlése**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása

Ruby-alkalmazás létrehozása. Útmutatásért lásd: [Ruby-alkalmazás létrehozása linuxon app Service](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Az alkalmazás konfigurálása a tároló elérésére

Az Azure Storage használatához le kell töltenie és használnia kell a Ruby Azure-csomagot, amely a tárolási REST-szolgáltatásokkal kommunikáló kényelmi könyvtárakat tartalmaz.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával

1. Használjon egy parancssori felületet, mint például a PowerShell (Windows), a Terminal (Mac), vagy a Bash (Unix).
2. `gem install Azure`A gem és a függőségek telepítéséhez írja be a parancsot a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása

Használja kedvenc szövegszerkesztőjét, adja hozzá a következőt a Ruby-fájl elejéhez, ahol a Storage-t szeretné használni:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure Storage-beli kapcsolatok beállítása

Az Azure-modul beolvassa a környezeti változókat `AZURE_STORAGE_ACCOUNT` , valamint `AZURE_STORAGE_ACCESS_KEY` Az Azure Storage-fiókhoz való kapcsolódáshoz szükséges információkat. Ha ezek a környezeti változók nincsenek beállítva, a következő kóddal kell megadnia a fiókadatok használatát `Azure::QueueService` :

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Ezeket az értékeket a következőképp kérheti le egy klasszikus vagy Resource Manager-tárfiókból az Azure Portalon:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. Navigáljon a használni kívánt Storage-fiókhoz.
3. A jobb oldali **Beállítások** panelen kattintson a **hozzáférési kulcsok** elemre.
4. A megjelenő **hozzáférési kulcsok** panelen megjelenik az 1. és a 2. hozzáférési kulcs. Ezek bármelyikét használhatja.
5. Kattintson a másolás gombra, hogy a kulcsot a vágólapra másolja.

## <a name="how-to-create-a-queue"></a>Útmutató: üzenetsor létrehozása

A következő kód egy objektumot hoz létre `Azure::QueueService` , amely lehetővé teszi a várólistákkal való munkavégzést.

```ruby
azure_queue_service = Azure::QueueService.new
```

A `create_queue()` metódus használatával hozzon létre egy várólistát a megadott névvel.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: üzenet beszúrása egy várólistába

Egy üzenet üzenetsorbe való beszúrásához használja az `create_message()` metódust egy új üzenet létrehozásához, és adja hozzá a várólistához.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: betekintés a következő üzenetbe

A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistába `peek_messages()` . Alapértelmezés szerint `peek_messages()` a betekintés egyetlen üzenetbe kerül. Megadhatja azt is, hogy hány üzenetet szeretne megtekinteni.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: a következő üzenet elküldése

A várólistákból két lépésben törölhet üzenetet.

1. A hívásakor `list_messages()` a következő üzenet jelenik meg egy várólistában, alapértelmezés szerint. Megadhatja azt is, hogy hány üzenetet szeretne kapni. A visszaadott üzenetek `list_messages()` láthatatlanná válnak a várólistáról érkező más kódoknál. A láthatósági időtúllépést másodpercben adja meg paraméterként.
2. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívni a következőt is: `delete_message()` .

Az üzenetek eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód a hardver vagy a szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód meghívása `delete_message()` közvetlenül az üzenet feldolgozása után történik.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: várólistán lévő üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. A következő kód a `update_message()` metódust használja egy üzenet frissítéséhez. A metódus egy olyan rekordot ad vissza, amely tartalmazza a várólista-üzenet pop-fogadását, valamint egy olyan UTC `DateTime` értéket, amely akkor jelenik meg, ha az üzenet látható lesz a várólistán.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Útmutató: a dequeuing üzenetek további beállításai

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.

1. Az üzenetek egy kötegét is lekérheti.
2. Megadhat egy hosszabb vagy rövidebb láthatósági időkorlátot, így a kód több vagy kevesebb időt vehet igénybe az egyes üzenetek teljes feldolgozásához.

A következő kódrészlet a metódus használatával `list_messages()` 15 üzenetet kap egy hívásban. Ezután kinyomtatja és törli az egyes üzeneteket. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: a várólista hosszának beolvasása

A várólistában lévő üzenetek számának becslését is elérheti. A `get_queue_metadata()` metódus a megközelítő üzenetek száma és az egyéb üzenetsor-metaadatok értékét adja vissza.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Útmutató: üzenetsor törlése

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja `delete_queue()` meg a metódust a várólista-objektumon.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte Queue Storage alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

- Tekintse meg az [Azure Storage csapat blogját](/archive/blogs/windowsazurestorage/)
- Látogasson el az [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) adattárára a githubon

A jelen cikkben tárgyalt Azure-Queue Storage és a [Service Bus-várólisták használatát](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)ismertető Azure Service Bus várólisták összehasonlítását lásd: [azure Queue Storage és Service Bus Queues – összehasonlítás és kontrasztos](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
