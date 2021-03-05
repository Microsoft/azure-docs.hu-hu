---
title: A tranzakciók feldolgozásának áttekintése Azure Service Bus
description: Ez a cikk áttekintést nyújt a tranzakciók feldolgozásáról és a Azure Service Buson keresztüli küldés szolgáltatásról.
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2848f41d5557584b0f1a197b548a00a4aef1564
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183743"
---
# <a name="overview-of-service-bus-transaction-processing"></a>A tranzakciók feldolgozásának Service Bus áttekintése

Ez a cikk a Microsoft Azure Service Bus tranzakciós képességeit ismerteti. A vitafórumok nagy részét [Service Bus mintával rendelkező AMQP-tranzakciók](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)szemléltetik. Ez a cikk a tranzakciók feldolgozásának és a Service Buson *keresztüli küldés* funkciójának áttekintésére korlátozódik, míg az Atomic Transactions minta szélesebb és összetettebb a hatókörben.

> [!NOTE]
> A Service Bus alapszintű csomagja nem támogatja a tranzakciókat. A standard és a prémium szintű tranzakciókat támogatja. A szintek közötti különbségekért tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="transactions-in-service-bus"></a>Service Bus tranzakciói

Egy *tranzakció* két vagy több műveletet egyesít egy *végrehajtási hatókörbe*. Természeténél fogva az ilyen tranzakciónak biztosítania kell, hogy az adott műveleti csoportba tartozó összes művelet a sikeres vagy a sikertelen műveletekkel együtt járjon el. Ebben a tekintetben a tranzakciók egy egységként működnek, amelyet gyakran *atominak* nevezünk.

A Service Bus egy tranzakciós üzenetküldési ügynök, és biztosítja az összes belső művelet tranzakciós integritását az üzenet-tárolókban. A Service Buson belüli összes üzenet átvitele, például üzenetek áthelyezése egy [kézbesítetlen levelek várólistájába](service-bus-dead-letter-queues.md) vagy az üzenetek entitások közötti [automatikus továbbítása](service-bus-auto-forwarding.md) , tranzakciós. Ilyen esetben, ha Service Bus fogad egy üzenetet, azt már tárolták, és sorszámmal címkézték. Ettől kezdve a Service Buson belüli összes üzenet a különböző entitások között koordinált műveleteket hajt végre, és nem fog elveszíteni (a forrás sikeres és a cél sikertelen lesz), vagy a Duplikálás (a forrás meghibásodása és a cél sikeressége).

A Service Bus támogatja az egyetlen üzenetküldő entitásra (üzenetsor, témakör, előfizetés) irányuló csoportosítási műveleteket egy tranzakció hatáskörén belül. Küldhet például több üzenetet egy várólistába egy tranzakció hatókörén belül, és az üzenetek csak a várólista naplójába lesznek véglegesítve, amikor a tranzakció sikeresen befejeződik.

## <a name="operations-within-a-transaction-scope"></a>Tranzakciók hatókörén belüli műveletek

A tranzakciók hatókörén belül elvégezhető műveletek a következők:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**:,,,, `Complete` `CompleteAsync` `Abandon` `AbandonAsync` `Deadletter` , `DeadletterAsync` , `Defer` , `DeferAsync` , `RenewLock` , `RenewLockAsync` 

A fogadási műveletek nem szerepelnek, mert feltételezhető, hogy az alkalmazás a [ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) mód használatával, egy fogadási hurokban vagy egy [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) -visszahívással szerzi be az üzeneteket, és csak ezután nyit meg egy tranzakció-hatókört az üzenet feldolgozásához.

Az üzenet (teljes, lemondás, kézbesítetlen levél, elhalasztás) a (z) és a (z) hatókörén belül, a tranzakció általános eredményének függvényében történik.

## <a name="transfers-and-send-via"></a>Átvitel és "Küldés"

Ha engedélyezni szeretné az adatok tranzakciós *átadását* egy várólistáról vagy témakörből egy processzorra, majd egy másik várólistára vagy témakörre, Service Bus támogatja az átvitelt. Az átviteli műveletekben a küldő először üzenetet küld egy *adatátviteli várólistába vagy témakörbe*, és az átviteli várólista vagy a témakör azonnal áthelyezi az üzenetet a kívánt célhelyre vagy témakörre ugyanazzal a robusztus átvitel-megvalósítással, amelyet az autoforward funkció támaszkodik. Az üzenet soha nem lett véglegesítve az adatátviteli várólista vagy a témakör naplójában, így láthatóvá válik az átviteli várólista vagy a témakör felhasználói számára.

A tranzakciós képesség ereje nyilvánvalóvá válik, ha az átviteli várólista vagy a témakör maga a küldő bemeneti üzeneteinek forrása. Más szóval, Service Bus átviheti az üzenetet a célhelyre vagy a (z) "on" üzenetbe az átviteli várólistán vagy témakörön keresztül, miközben teljes (vagy késleltetett) műveletet végez a bemeneti üzenetben, egyetlen atomi művelettel. 

Ha egy témakör-előfizetésből kell bejelentkeznie, majd egy várólistába vagy témakörbe kell küldenie ugyanabban a tranzakcióban, az átvitel entitásának témakörnek kell lennie. Ebben a forgatókönyvben a tranzakció hatókörének megkezdése a témakörben, az előfizetés és a tranzakció hatókörének fogadása, majd az átvitel témakör használatával egy üzenetsor vagy témakör célhelye. 

### <a name="see-it-in-code"></a>Megtekintés a kódban

Az ilyen átvitelek beállításához létre kell hoznia egy üzenetet küldőt, amely az átviteli várólistán keresztül célozza meg a célhelyet. Olyan fogadóval is rendelkezik, amely ugyanazon a várólistán lévő üzeneteket kéri le. Például:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Az egyszerű tranzakciók ezután ezeket az elemeket használják, ahogy az alábbi példában is látható. A teljes példa áttekinthető a [githubon a forráskódban](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>Időtúllépés
Egy tranzakció időtúllépése 2 perc múlva megtörténik. A tranzakció-időzítő akkor indul el, amikor a tranzakció első művelete elindul. 

## <a name="next-steps"></a>Következő lépések

Service Bus várólistákkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [How to use Service Bus Queues](service-bus-dotnet-get-started-with-queues.md) (A Service Bus-üzenetsorok használata)
* [Service Bus entitások láncolása az autoforwarding révén](service-bus-auto-forwarding.md)
* [Autoforward minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomi tranzakciók Service Bus mintával](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Az Azure Queues és a Service Bus-várólisták összehasonlítása](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


