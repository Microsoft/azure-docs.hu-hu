---
title: Ismétlődő üzenetek észlelésének Azure Service Busa | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan észlelhetők az ismétlődő elemek Azure Service Bus üzenetekben. Az ismétlődő üzenet figyelmen kívül hagyható, és eldobása is lehetséges.
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 527c2dea34b02733907372b6e75a40a5ef5fc289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711925"
---
# <a name="duplicate-detection"></a>Duplikálás észlelése

Ha egy alkalmazás az üzenet elküldése után azonnal végzetes hibát jelez, és az újraindított alkalmazás példánya hibásan úgy véli, hogy az előző üzenet kézbesítése nem történt meg, akkor egy későbbi küldés esetén a rendszer kétszer is megjelenik az üzenetben.

Az ügyfél vagy a hálózati szint hibája is előfordulhat, hogy korábban egy pillanatra fordul elő, és egy küldött üzenet véglegesítve lesz a várólistában, és a nyugtát nem sikerült visszaadni az ügyfélnek. Ez a forgatókönyv kétségbe hagyja az ügyfelet a küldési művelet eredményével kapcsolatban.

A duplikált észlelési funkció az ilyen helyzetekben nem teszi lehetővé, hogy a küldő újra elküldi ugyanazt az üzenetet, és a várólista vagy a témakör minden ismétlődő példányt elvet.

> [!NOTE]
> A Service Bus alapszintű csomagja nem támogatja a duplikált észlelést. A standard és a prémium szint támogatja a duplikált észlelést. A szintek közötti különbségekért tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Hogyan működik? 
Az ismétlődő észlelés engedélyezése segít nyomon követni a várólistába vagy témakörbe küldött összes üzenet alkalmazás által vezérelt *MessageID* egy adott időszakra vonatkozóan. Ha a rendszer minden új üzenetet küld az időablakban naplózott *MessageID* , az üzenet elfogadva lesz (a küldési művelet sikeres lesz), de az újonnan elküldött üzenet azonnal figyelmen kívül lesz hagyva, és el lesz dobva. Az üzenet más részei nem tekintendők a *MessageID* .

Az azonosító alkalmazás-vezérlése alapvető fontosságú, mivel csak az lehet, hogy az alkalmazás összekapcsolja a *MessageID* egy üzleti folyamati környezettel, amelyből a hiba bekövetkezésekor kiszámíthatóan újraépíthető.

Egy olyan üzleti folyamat esetében, amelyben több üzenet érkezik az egyes alkalmazási környezetek kezelésére, a *MessageID* az alkalmazás szintű környezet azonosítójának, például a beszerzési rendelés számának és az üzenet tárgyának (például **12345.2017/fizetés**) összetett része lehet.

A *MessageID* mindig lehet egy GUID-azonosító, de az azonosító az üzleti folyamathoz való rögzítése a kiszámítható ismételhetőséget eredményezi, amely a duplikált észlelési funkció hatékony használatához szükséges.

> [!IMPORTANT]
>- Ha a **particionálás** **engedélyezve** van, `MessageId+PartitionKey` az egyediség megállapítására szolgál. Ha a munkamenetek engedélyezve vannak, a partíciós kulcsnak és a munkamenet-AZONOSÍTÓnak azonosnak kell lennie. 
>- Ha a **particionálás** **le van tiltva** (alapértelmezett), `MessageId` a rendszer csak az egyediség meghatározására szolgál.
>- A munkamenet-azonosítóval, a PartitionKey és a MessageId kapcsolatos további információkért lásd: [partíciós kulcsok használata](service-bus-partitioning.md#use-of-partition-keys).
>- A [Premier szintű](service-bus-premium-messaging.md) csomag nem támogatja a particionálást, ezért javasoljuk, hogy egyedi üzenet-azonosítókat használjon az alkalmazásokban, és ne támaszkodjon a partíciós kulcsokra a duplikált észleléshez. 


## <a name="enable-duplicate-detection"></a>Ismétlődő észlelés engedélyezése

A portálon a funkció be van kapcsolva az entitások létrehozásakor a **duplikált észlelés engedélyezése** jelölőnégyzettel, amely alapértelmezés szerint ki van kapcsolva. Az új témakörök létrehozásának beállítása egyenértékű.

![A várólista létrehozása párbeszédpanel képernyőképe a duplikált észlelés engedélyezése lehetőség kiválasztásával, és piros színnel.][1]

> [!IMPORTANT]
> A várólista létrehozása után nem engedélyezheti vagy tilthatja le az ismétlődő észlelést. Ezt csak a várólista létrehozásakor teheti meg. 

Programozott módon beállíthatja a jelzőt a [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) tulajdonsággal a teljes keretrendszer .NET API-ban. A Azure Resource Manager API-val az érték a [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonsággal van beállítva.

A duplikált észlelési időelőzmények alapértelmezés szerint 10 percet vesznek igénybe a várólisták és a témakörök esetében, és a minimális érték 20 másodperc, a maximális érték 7 nap. Ezt a beállítást a Azure Portal üzenetsor és témakör tulajdonságai ablakában módosíthatja.

![Képernyőkép a Service Bus funkcióról a Kiemelt tulajdonságok beállítással, valamint a pirosban megjelenő duplikált észlelési előzmények lehetőséggel.][2]

Programozott módon beállíthatja a duplikált észlelési ablak méretét, amely során az üzenetsor-azonosítók megmaradnak, a [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) tulajdonságot pedig a teljes .NET-keretrendszer API-val. A Azure Resource Manager API-val az érték a [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonsággal van beállítva.

A duplikált észlelés engedélyezése és az ablak mérete közvetlenül befolyásolja a várólista (és a témakör) átviteli sebességét, mivel az összes rögzített üzenet azonosítójának meg kell egyeznie az újonnan küldött üzenet azonosítójával.

A kis ablak megtartása azt jelenti, hogy kevesebb üzenet-azonosítót kell megőrizni és egyeztetni, és az átviteli sebesség kevesebbre van hatással. Az ismétlődő észlelést igénylő nagy átviteli sebességű entitások esetén a lehető legkisebbre kell tartani az ablakot.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Olyan esetekben, amikor az ügyfél kódja nem tud újra elküldeni egy üzenetet ugyanazzal a *MessageID* , mint korábban, fontos, hogy megtervezze a biztonságosan újrafeldolgozható üzeneteket. Ez a [blogbejegyzés a idempotence kapcsolatos](https://particular.net/blog/what-does-idempotent-mean) különféle technikákat ismerteti.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
