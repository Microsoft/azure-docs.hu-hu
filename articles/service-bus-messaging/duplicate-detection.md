---
title: Azure Service Bus üzenetészlelés | Microsoft Docs
description: Ez a cikk bemutatja, hogyan észlelheti az ismétlődéseket a Azure Service Bus üzenetekben. Az ismétlődő üzenet figyelmen kívül hagyható és eldobható.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751273"
---
# <a name="duplicate-detection"></a>Duplikálás észlelése

Ha egy alkalmazás egy végzetes hiba miatt meghiúsul közvetlenül az üzenet küldése után, és az újraindított alkalmazáspéldány tévesen úgy véli, hogy az előző üzenet kézbesítése nem történt meg, egy későbbi küldés ugyanazt az üzenetet kétszer is meg fog jelenni a rendszerben.

Előfordulhat, hogy egy hiba az ügyfél vagy a hálózat szintjén egy pillanattal korábban fordul elő, és hogy egy elküldött üzenetet a rendszer az üzenetsorba fog tenni, és a nyugtázást nem sikerül visszaadni az ügyfélnek. Ebben a forgatókönyvben az ügyfél nem biztos a küldési művelet eredményében.

A duplikált üzenetek észlelése úgy vonja el a kétséget az ilyen helyzetekből, hogy lehetővé teszi, hogy a küldő újra elküldje ugyanazt az üzenetet, az üzenetsor vagy témakör pedig elveti az ismétlődő másolatokat.

> [!NOTE]
> A szolgáltatás alapszintű Service Bus nem támogatja a duplikált elemek észlelését. A standard és a prémium szint támogatja a duplikált elemek észlelését. A tarifacsomagok közötti különbségekért lásd: [Service Bus díjszabása.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="how-it-works"></a>Hogyan működik? 
A duplikált üzenetek észlelésének  engedélyezése segít nyomon követni az adott időszakban üzenetsorba vagy témakörbe küldött összes üzenet alkalmazás által vezérelt MessageId-ját. Ha bármilyen új üzenetet küld az időablakban naplózott *MessageId-val,* az üzenet elfogadottként lesz jelentve (a küldési művelet sikeres), de az újonnan küldött üzenetet a rendszer azonnal figyelmen kívül hagyja és eldobja. Az üzenetnek a *MessageId-t* nem kell figyelembe vennie.

Az azonosító alkalmazásvezérlése alapvető fontosságú, mivel csak ez teszi lehetővé, hogy az alkalmazás a *MessageId* azonosítót olyan üzleti folyamatkörnyezethez kösse, amelyből hiba esetén kiszámítható módon rekonstruálható.

Olyan üzleti folyamatok esetén, amelyekben bizonyos alkalmazáskörnyezetek kezelése során több üzenet is megjelenik, a *MessageId* lehet az alkalmazásszintű környezeti azonosító, például egy rendelési szám és az üzenet tárgya, például **12345.2017/payment.**

A *MessageId* mindig lehet némi GUID azonosító, de az azonosítónak az üzleti folyamathoz való rögzítése kiszámítható ismételhetőséget eredményez, ami a duplikált észlelési funkció hatékony használatához szükséges.

> [!IMPORTANT]
>- Ha **a particionálás** **engedélyezve van,** a `MessageId+PartitionKey` rendszer az egyediséget határozza meg. Ha a munkamenetek engedélyezve vannak, a partíciókulcsnak és a munkamenet-azonosítónak azonosnak kell lennie. 
>- Ha **a particionálás** **le van tiltva** (alapértelmezett), a rendszer csak az `MessageId` egyediséget határozza meg.
>- A SessionId, PartitionKey és MessageId beállításokkal kapcsolatos információkért lásd: [Partíciókulcsok használata.](service-bus-partitioning.md#use-of-partition-keys)
>- A [premier szintű csomag](service-bus-premium-messaging.md) nem támogatja a particionálást, ezért azt javasoljuk, hogy egyedi üzenet-azonosítókat használjon az alkalmazásokban, és ne használjon partíciókulcsokat a duplikált üzenetek észleléséhez. 


## <a name="duplicate-detection-window-size"></a>Duplikált elemek észlelése ablakméret

A duplikált elemek észlelésének engedélyezése mellett a duplikált észlelési előzmények időablakának méretét is konfigurálhatja, amely alatt a rendszer megőrzi az üzenetazonosítókat.
Ez az érték alapértelmezés szerint 10 perc az üzenetsorok és témakörök esetén, és a minimális érték 20 másodperc, de legfeljebb 7 nap.

A duplikált üzenetek észlelésének engedélyezése és az ablak mérete közvetlenül befolyásolja az üzenetsor (és a témakör) átviteli sebességét, mivel az összes rögzített üzenetazonosítót egyezni kell az újonnan elküldött üzenetazonosítóval.

Ha az ablakot kicsiben tartja, az azt jelenti, hogy kevesebb üzenetazonosítót kell megőrizni és egyezni, és ez az átviteli sebességet csökkenti. A duplikált észlelést igénylő nagy átviteli sebességű entitások esetén érdemes a lehető legkisebbre tartani az ablakot.

## <a name="next-steps"></a>Következő lépések
A duplikált üzenetek észlelését a következő Azure Portal, PowerShell, parancssori felület, Resource Manager sablon, .NET, Java, Python és JavaScript használatával engedélyezheti. További információ: [Ismétlődő üzenetek észlelésének engedélyezése.](enable-duplicate-detection.md) 

Olyan esetekben, amikor az ügyfélkód nem tud újraküldeni egy üzenetet ugyanazokkal az *Üzenetazonosítóval,* mint korábban, fontos a biztonságosan újrafeldolgozható üzenetek tervezése. Ez [az idempotenciával kapcsolatos blogbejegyzés](https://particular.net/blog/what-does-idempotent-mean) ennek különböző módszereit ismerteti.

Próbálja ki a mintákat a választott nyelven, és fedezze fel Azure Service Bus funkcióit. 

- [Azure Service Bus java-ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus Pythonhoz készült ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus JavaScripthez való ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus TypeScript ügyféloldali kódtárminái](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-minták a .NET-hez](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Az alábbiakban a régebbi .NET- és Java-ügyfélkódtárakhoz találhat mintákat:
- [Microsoft.Azure.ServiceBus-minták a .NET-hez](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-minták Javához](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

