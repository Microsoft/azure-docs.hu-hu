---
title: Azure Service Bus – üzenetek tallózása
description: Az üzenetek tallózása Service Bus és betekintése lehetővé teszi, Azure Service Bus ügyfél enumeráljon egy üzenetsorban vagy előfizetésben lévő összes üzenetet.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520122"
---
# <a name="message-browsing"></a>Üzenetek tallózása
Az üzenetböngészés vagy betekintés lehetővé teszi, hogy a Service Bus-ügyfél diagnosztikai és hibakeresési célból enumeráljon egy üzenetsorban vagy előfizetésben lévő összes üzenetet.

Az üzenetsorra vagy előfizetésre vonatkozó Betekintés művelet a kért számú üzenetet adja vissza. Az alábbi táblázat a Betekintés művelet által visszaadott üzenettípusokat mutatja be. 

| Üzenetek típusa | Tartalmazza? | 
| ---------------- | ----- | 
| Aktív üzenetek | Igen |
| Nem küldött üzenetek | Nem | 
| Zárolt üzenetek | Igen |
| Lejárt üzenetek |  Lehet (mielőtt a rendszer elhalt leveleket ad vissza) |
| Ütemezett üzenetek | Igen az üzenetsorok számára. Nem az előfizetések esetén |

## <a name="dead-lettered-messages"></a>Nem küldött üzenetek
Az üzenetsor  vagy előfizetés nem küldött üzeneteibe való betekintéshez a betekintés műveletet az üzenetsorhoz vagy előfizetéshez társított üzenetsoron kell futtatni. További információkért lásd: A nem elérhető [üzenetsorok elérése.](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)

## <a name="expired-messages"></a>Lejárt üzenetek
A lejárt üzenetek a Betekintés művelet által visszaadott eredményekben is lehetnek. A felhasznált és lejárt üzeneteket egy aszinkron "szemétgyűjtési" futtatás tisztítja meg. Ez a lépés nem feltétlenül történik meg közvetlenül az üzenetek lejárata után. Ezért előfordulhat, hogy egy betekintés művelet már lejárt üzeneteket ad vissza. Ezek az üzenetek el lesznek távolítva, vagy a nem küldött üzenetek el lesznek távolítva, amikor legközelebb meghívnak egy fogadási műveletet az üzenetsoron vagy az előfizetésen. Ezt a viselkedést tartsa szem előtt, amikor késleltetett üzeneteket próbál helyreállítani az üzenetsorból. 

A lejárt üzenetek már nem jogosultak más módon normál lekérésre, még akkor sem, ha a Peek visszaadja őket. Ezeket az üzeneteket a tervezés szerint adja vissza, mivel a Betekintés egy diagnosztikai eszköz, amely a napló aktuális állapotát tükrözi.

## <a name="locked-messages"></a>Zárolt üzenetek
A Betekintés azokat az üzeneteket is visszaadja, amelyek **zárolva** vannak, és más fogadók dolgoznak fel. Mivel azonban a Betekintés leválasztott pillanatképet ad vissza, az üzenetek zárolási állapota nem figyelhető meg a bepillantott üzeneteknél.

## <a name="peek-apis"></a>Betekintés AZ API-kba
A betekintés az üzenetsorokkal, az előfizetésekkel és a levélküldetésekkel kapcsolatos üzenetsorokkal is működik. 

Ha többször is meg van hívva, a betekintés művelet sorrendben számba veszi az üzenetsorban vagy előfizetésben lévő összes üzenetet, a legalacsonyabb elérhető sorszámtól a legmagasabbig. Az üzenetek sorrendje a sorrend, nem pedig az üzenetek lekérésének sorrendje.

A SequenceNumber egy betekintő műveletbe is átadhat egy műveletet. A segítségével határozható meg, hogy honnan kezdjen betekinteni. A betekintés művelet további hívásait a további számbavételhez szükséges paraméter megadása nélkül is kezdeményezheti.

## <a name="next-steps"></a>Következő lépések
Próbálja ki a mintákat a választott nyelven a betekintés vagy az üzenetböngészés funkció felfedezéséhez:

- [Azure Service Bus java-ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Betekintés az üzenetmintába**
- [Azure Service Bus Pythonhoz készült ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek.py minta**
- [Azure Service Bus Kódtárminták JavaScripthez](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.js** minta
- [Azure Service Bus TypeScript ügyféloldali kódtárminái](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **browseMessages.ts** minta
- [Azure.Messaging.ServiceBus-minták a .NET-hez](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) – A referenciadokumentációban tekintse meg a metódusok betekintés a fogadóosztályok [esetében részt.](/dotnet/api/azure.messaging.servicebus)

Az alábbiakban példákat talál a régebbi .NET- és Java-ügyfélkódtárakhoz:
- [Microsoft.Azure.ServiceBus-minták a .NET-hez](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  **Üzenetböngészés (betekintés) –** minta 
- [azure-servicebus-minták Javához](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **Üzenet tallózása** – minta. 
