---
title: Azure Service Bus üzenetészlelés | Microsoft Docs
description: Ez a cikk bemutatja, hogyan észlelheti az ismétlődéseket a Azure Service Bus üzenetekben. Az ismétlődő üzenet figyelmen kívül hagyható és eldobható.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: a9ca9de988f5a3db15da773a870e2d929ab938c8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499478"
---
# <a name="duplicate-detection"></a>Duplikálás észlelése

Ha egy alkalmazás egy végzetes hiba miatt meghiúsul közvetlenül az üzenet küldése után, és az újraindított alkalmazáspéldány tévesen úgy véli, hogy az előző üzenet kézbesítése nem történt meg, egy későbbi küldés ugyanazt az üzenetet kétszer is meg fog jelenni a rendszerben.

Előfordulhat, hogy az ügyfél vagy a hálózat szintjén egy pillanattal korábban hiba lép fel, és az elküldött üzenetek az üzenetsorba kerülnek, és a nyugtázás nem lesz sikeresen visszaadva az ügyfélnek. Ebben a forgatókönyvben az ügyfél nem biztos a küldési művelet eredményében.

A duplikált üzenetek észlelése úgy vonja ki a kétséget az ilyen helyzetekből, hogy lehetővé teszi, hogy a küldő újra elküldje ugyanazt az üzenetet, és az üzenetsor vagy témakör elveti az ismétlődő másolatokat.

> [!NOTE]
> A szolgáltatás alapszintű Service Bus nem támogatja a duplikált elemek észlelését. A standard és a prémium szint támogatja a duplikált elemek észlelését. A csomagok közötti különbségeket a [díjszabást Service Bus tekintse meg.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="how-it-works"></a>Hogyan működik? 
A duplikált üzenetek észlelésének engedélyezése segít nyomon követni az alkalmazás által vezérelt *MessageId-t* az üzenetsorba vagy témakörbe egy adott időablakban küldött összes üzenetről. Ha az időablakban naplózott *üzenetazonosítóval* bármilyen új üzenetet küld a rendszer, az üzenet elfogadottként lesz jelentve (a küldési művelet sikeres), de az újonnan elküldött üzenetet a rendszer azonnal figyelmen kívül hagyja és eldobja. Az üzenetnek a *MessageId-t* nem kell figyelembe vennie.

Az azonosító alkalmazásvezérlése alapvető fontosságú, mivel csak ez teszi lehetővé, hogy az alkalmazás az *üzenetazonosítót* egy olyan üzleti folyamat környezethez kösse, amelyből meghibásodás esetén kiszámítható módon rekonstruálható.

Olyan üzleti folyamatok esetén, amelyekben egy alkalmazáskörnyezet kezelése során több üzenetet küld a rendszer, a *MessageId* lehet az alkalmazásszintű környezeti azonosító, például egy rendelési szám, valamint az üzenet tárgya, például **12345.2017/payment.**

A *MessageId* mindig lehet némi GUID azonosító, de az azonosító üzleti folyamathoz való rögzítése kiszámítható ismételhetőséget eredményez, amely a duplikált észlelési funkció hatékony használatához szükséges.

> [!IMPORTANT]
>- Ha **a particionálás** **engedélyezve van,** a az `MessageId+PartitionKey` egyediség meghatározására használható. Ha a munkamenetek engedélyezve vannak, a partíciókulcsnak és a munkamenet-azonosítónak azonosnak kell lennie. 
>- Ha **a particionálás** **le van tiltva** (alapértelmezett), csak `MessageId` az egyediség meghatározására használható.
>- A SessionId, PartitionKey és MessageId adatokkal kapcsolatos információkért lásd: [Partíciókulcsok használata.](service-bus-partitioning.md#use-of-partition-keys)
>- A [premier szintű csomag](service-bus-premium-messaging.md) nem támogatja a particionálást, ezért javasoljuk, hogy egyedi üzenet-azonosítókat használjon az alkalmazásokban, és ne használjon partíciókulcsokat a duplikált üzenetek észleléséhez. 


## <a name="enable-duplicate-detection"></a>Ismétlődések észlelésének engedélyezése

A duplikált üzenetek észlelésének engedélyezése mellett a duplikált észlelési előzmények időablakának méretét is beállíthatja, amely alatt a rendszer megőrzi az üzenetazonosítókat.
Ez az érték alapértelmezés szerint 10 perc az üzenetsorok és témakörök esetén, és a minimális érték 20 másodperc, de legfeljebb 7 nap.

A duplikált üzenetek észlelésének engedélyezése és az ablak mérete közvetlenül befolyásolja az üzenetsor (és a témakör) átviteli sebességét, mivel az összes rögzített üzenetazonosítót az újonnan elküldött üzenetazonosítóval kell egyezni.

Ha az ablakot kicsiben tartja, kevesebb üzenetazonosítót kell megőriznie és egyeznie, és az átviteli sebességet kisebb hatással van. A duplikált észlelést igénylő nagy átviteli sebességű entitások esetén érdemes a lehető legkisebb méretűre tartani az ablakot.

### <a name="using-the-portal"></a>A portállal

A portálon az entitás létrehozása során a duplikátálásészlelési funkció be van kapcsolva az **Ismétlődések** észlelésének engedélyezése jelölőnégyzet bejelölve, amely alapértelmezés szerint ki van kapcsolva. Az új témakörök létrehozására vonatkozó beállítás egyenértékű.

![Képernyőkép az Üzenetsor létrehozása párbeszédpanelről, az Ismétlődések észlelésének engedélyezése lehetőség kijelölve és piros színre jelölve.][1]

> [!IMPORTANT]
> Az üzenetsor létrehozása után nem engedélyezheti vagy tilthatja le az ismétlődések észlelését. Ezt csak az üzenetsor létrehozásakor lehet megtenni. 

A duplikált észlelési előzmények időablaka az üzenetsor és témakör tulajdonságai ablakban módosítható a Azure Portal.

![Képernyőkép a Service Bus funkcióról, kiemelt Tulajdonságok beállítással és pirossal kiemelt Ismétlődésészlelési előzmények lehetőséggel.][2]

### <a name="using-sdks"></a>SDK-k használata

Bármelyik .NET-, Java-, JavaScript-, Python- és Go-alapú SZOFTVERDK-unkat használhatja az ismétlődő észlelési funkció engedélyezéséhez üzenetsorok és témakörök létrehozásakor. Módosíthatja a duplikált észlelési előzmények időablakát is.
Az ehhez szükséges üzenetsorok és témakörök létrehozásakor frissítenünk kell a tulajdonságokat:
- `RequiresDuplicateDetection`
- `DuplicateDetectionHistoryTimeWindow`

Vegye figyelembe, hogy bár a tulajdonságnevek itt a casing szövegben vannak megszabadulva, a JavaScript- és Python-ALAPÚ ADK-k a camel casing és a casing tulajdonságot fogják használni.

## <a name="next-steps"></a>Következő lépések

Az üzenetkezelésről Service Bus alábbi témakörökben olvashat bővebben:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Olyan forgatókönyvekben, ahol az ügyfélkód nem tud újraküldeni egy üzenetet ugyanazokkal az *üzenetazonosítóval,* mint korábban, fontos a biztonságosan újrafeldolgozható üzenetek tervezése. Ez [az idempotenciával](https://particular.net/blog/what-does-idempotent-mean) kapcsolatos blogbejegyzés ennek különböző módszereit ismerteti.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
