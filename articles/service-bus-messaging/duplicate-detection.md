---
title: Az Azure Service Bus duplikált üzenetek észlelése |} A Microsoft Docs
description: A Service Bus duplikált üzenetek észlelése
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d9f814a49924ca95078f3b3decca4f3922c74c2b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413663"
---
# <a name="duplicate-detection"></a>Duplikálás észlelése

Ha egy alkalmazás miatt sikertelen egy súlyos hiba után azonnal, egy üzenetet küld, és az újraindított alkalmazáspéldány hibásan szerint, hogy a korábbi üzenetkézbesítése nem történt meg, ezt követő küldés hatására ugyanazt az üzenetet kétszer jelenik meg a rendszerben.

A hiba fordul elő egy kicsit korábban az ügyfél vagy hálózati szinten lehetőség arra is, és az elküldött üzenet az üzenetsorban, a nyugtázás a nem sikerült véglegesíteni kell küld vissza az ügyfélnek. Ebben a forgatókönyvben hagyja a küldési művelet eredményének bizonytalan az ügyfél.

Duplikáltelem-észlelési kívül ezekben a helyzetekben a bizonytalan fogadja engedélyezésével a küldő újraküldése ugyanazt az üzenetet, és az üzenetsor vagy témakör elveti minden duplikált példánya.

Duplikált elemek észlelésének engedélyezésével segít az alkalmazás által szabályozott nyomon követheti, *üzenetazonosító* egy adott időszak során egy üzenetsorba vagy témakörbe küldött összes üzenet. Ha bármely új üzenetet küld az *üzenetazonosító* , amely a naplózott az időszakban, az üzenet lesz jelentve elfogadva (a küldési művelet sikeres), azonban az újonnan elküldött üzenet azonnal figyelmen kívül hagyja, és eltávolítja. Az üzenet nem nincs egyéb részei a *üzenetazonosító* minősülnek.

Az azonosító az Alkalmazásvezérlés elengedhetetlen, mert csak az, hogy lehetővé teszi, hogy az alkalmazást, hogy be lehessen vonni a *üzenetazonosító* , egy üzleti folyamat környezet, amelyből ez lehetővé teszi a kiszámítható rekonstruálható hiba esetén.

Az üzleti folyamatok, amelyben több üzeneteket küld az egyes alkalmazás-környezet kezelése a *üzenetazonosító* alkalmazásszintű környezeti azonosító, például a beszerzési rendelésszámukra összetett lehet, és a az üzenetben, például a Tárgy **12345.2017/fizetési**.

A *üzenetazonosító* mindig lehet néhány GUID, de az azonosító az üzleti folyamat horgonyzás poskytne kiszámítható ismételhetőség, amelyek esetében a duplikátumészlelési szolgáltatására támaszkodva gyakorlatilag van szükség.

> [!NOTE]
> Ha a duplikálásészlelés engedélyezve van, és sesion azonosító vagy a partíciós kulcs nincs megadva, az üzenet azonosítója használja partíciókulcsként. Ha még nincs beállítva az üzenet azonosítója, az AMQP és a .NET-kódtárak automatikusan létrehozhat egy üzenet azonosítója, az üzenet. További információkért lásd: [particionáló kulcsok használatát](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Ismétlődések észlelésének engedélyezése

A portálon, a funkció be van kapcsolva az entitás létrehozása során a **ismétlődések észlelésének engedélyezése** jelölőnégyzetet, amely alapértelmezés szerint ki van kapcsolva. Új témakörök létrehozására szolgáló beállítás egyenértékű.

![][1]

> [!IMPORTANT]
> Ön nem engedélyezi/letiltja duplikáltelem-észlelési az üzenetsor létrehozása után. Csak ehhez az üzenetsor létrehozása idején. 

Programozott módon, állítsa be a jelzőt mellékel a [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) tulajdonság a .NET API teljes keretében. Az Azure Resource Manager API-val a beállítás értéke az a [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság.

Alapértelmezés szerint a duplikáltelem-észlelési előzmények az üzenetsorokat és üzenettémákat, a hét napos maximális értéke 30 másodperc. Ez a beállítás az üzenetsorokkal és tulajdonságok ablakban, az Azure Portalon módosíthatja.

![][2]

Programozott módon, konfigurálhatja a óraszáma üzenet-azonosítók megmaradnak, kettős észlelés ablak méretének használatával a [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) tulajdonság a teljes .NET-keretrendszer API-val . Az Azure Resource Manager API-val a beállítás értéke az a [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonság.

Ismétlődések észlelésének engedélyezése és az ablak mérete közvetlen hatással van az üzenetsorokkal (és) átviteli sebességet, mivel az összes rögzített üzenetazonosítók egyeztetni az újonnan elküldött üzenet azonosítója alapján.

Az ablak kis azt jelenti, hogy kevesebb üzenetazonosítók őrződnek meg kell lennie, és egyezést, és az átviteli sebesség tartja negatív kisebb. Duplikáltelem-észlelési igénylő nagy átviteli sebességű entitások az ablak legyen a lehető legkisebb legyen.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
