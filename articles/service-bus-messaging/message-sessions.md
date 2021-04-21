---
title: Azure Service Bus munkamenetek | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a munkamenetek a kapcsolódó üzenetek kötetlen sorozatai közös és rendezett kezelésére.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: e22dfb2aa7372a227f70fd2bfa8f72d2161cda17
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750751"
---
# <a name="message-sessions"></a>Üzenet-munkamenetek
Microsoft Azure Service Bus munkamenetek lehetővé teszik a kapcsolódó üzenetek kötetlen sorozatának együttes és rendezett kezelését. A munkamenetek első be- és **lekéréses (FIFO)** és **kérés-válasz mintákban használhatók.** Ez a cikk bemutatja, hogyan implementálja ezeket a mintákat munkamenetek használatával a Service Bus. 

> [!NOTE]
> A szolgáltatás alapszintű Service Bus nem támogatja a munkameneteket. A standard és a prémium szint támogatja a munkameneteket. A tarifacsomagok közötti különbségekért lásd: [Service Bus díjszabása.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="first-in-first-out-fifo-pattern"></a>Elsőnek be, elsőnek ki (FIFO) minta
A FIFO-garanciának a Service Bus használhatja a munkameneteket. Service Bus nem előíró az üzenetek közötti kapcsolat természetére, és nem határoz meg egy adott modellt az üzenetsorozatok indulási vagy végi helyének meghatározásához.

Bármely küldő létrehozhat egy munkamenetet, amikor üzeneteket küld  egy témakörbe vagy üzenetsorba úgy, hogy a munkamenet-azonosító tulajdonságot egy, a munkamenet egyedi, alkalmazás által meghatározott azonosítójára határozzák meg. Az AMQP 1.0 protokollszinten ez az érték a *group-id* tulajdonságra van leképezve.

A munkamenet-et azonosító üzenetsorok vagy előfizetések esetén a munkamenetek akkor jönnek létre, ha legalább egy üzenet a munkamenet-azonosítóval rendelkezik. Ha a munkamenet már létezik, nincs meghatározott idő vagy API arra az időpontra, amikor a munkamenet lejár vagy eltűnik. Elméletileg egy ma, egy év következő üzenete egy munkamenetben is kaphat üzenetet, és ha a munkamenet-azonosító egyezik, akkor a munkamenet ugyanaz, mint Service Bus szempontjából.

Az alkalmazások azonban általában egyértelműen le vannak állítva arról, hogy hol kezdődik és végződik a kapcsolódó üzenetek készlete. Service Bus nem ad meg konkrét szabályokat. Az alkalmazás beállíthatja például, hogy az első üzenet **Label** (Címke) tulajdonsága indul **el,** a köztes üzenetek esetében a **content**(tartalom) legyen, az utolsó üzenet pedig a végére **ér.** A tartalomüzenetek relatív helyzete a SequenceNumber kezdőüzenetből származó  aktuális *SequenceNumber-változásként* *számítható ki.*

> [!IMPORTANT]
> Ha a munkamenetek engedélyezve vannak egy üzenetsorban  vagy előfizetésben, az ügyfélalkalmazások többé nem tudnak normál üzeneteket küldeni/fogadni. Minden üzenetet egy munkamenet részeként kell elküldeni (a munkamenet-azonosító beállításával), és a munkamenet elfogadásával kell fogadni.

A munkamenetek API-i az üzenetsor- és előfizetési ügyfeleken létezik. Íme egy imperatív modell, amely a munkamenetek és üzenetek fogadását szabályozza, valamint egy kezelőalapú modell, amely elrejti a fogadási hurok kezelésének összetettségét. 

Mintákhoz használja a Következő lépések [szakaszban található hivatkozásokat.](#next-steps) 

### <a name="session-features"></a>Munkamenet-funkciók

A munkamenetek a összefont üzenetstreamek párhuzamos de multiplexelését biztosítják, miközben megőrzik és garantálják a rendezett kézbesítést.

![Diagram arról, hogy a Munkamenetek funkció hogyan őrzi meg a rendezett kézbesítést.][1]

A munkamenet fogadója egy munkamenetet elfogadó ügyféllel jön létre. Amikor egy ügyfél elfogadja és tartja a munkamenetet, az ügyfél kizárólagos zárolást tart minden üzenetre, amely az adott munkamenet-azonosítóval rendelkezik az üzenetsorban vagy előfizetésben.  Emellett kizárólagos zárolást is tartalmaz az összes olyan üzenetre, amely a későbbiekben **érkező** munkamenet-azonosítóval együtt érkezik.

A zárolás akkor szabadul fel, amikor a fogadón a lezáráshoz kapcsolódó metódusokat hívja meg, vagy amikor a zárolás lejár. A fogadón metódusok is vannak a zárolások megújítására. Ehelyett használhatja az automatikus zárolás-megújítási funkciót, amellyel megadhatja azt az időtartamot, amelyre vonatkozóan meg szeretné újítani a zárolást. A munkamenet-zárolást fájl kizárólagos zárolásként kell kezelni, ami azt jelenti, hogy az alkalmazásnak be kell zárnia a munkamenetet, amint már nincs rá szüksége, és/vagy nem vár további üzeneteket.

Amikor több egyidejű fogadót is leküld az üzenetsorból, az egy adott munkamenethez tartozó üzeneteket a rendszer annak a fogadónak küldi, amely jelenleg az adott munkamenet zárolását tartalmazza. Ezzel a művelettel az egyik üzenetsorban vagy előfizetésben lévő összefonható üzenetstreamet a rendszer tiszta módon de multiplexálta különböző fogadókra, és ezek a fogadók különböző ügyfélszámítógépen is élőek, mivel a zároláskezelés szolgáltatásoldalon történik a Service Bus.

Az előző ábrán három egyidejű munkamenet-fogadó látható. Az egyik munkamenet a = 4 értékkel nem rendelkezik aktív, a tulajdonában lévő ügyfél, ami azt jelenti, hogy a rendszer nem kézbesít üzeneteket `SessionId` ebből a munkamenetből. A munkamenetek számos módon, például egy alsorként viselkednek.

A munkamenet fogadója által tartott munkamenet-zárolás a betekintés-zárolás kiegyenlítési mód által használt *üzenetzárolások* gyűjtője. Csak egy fogadó zárolhat egy munkamenetet. Előfordulhat, hogy egy fogadó számos utatalló üzenetet tartalmaz, de az üzenetek sorrendben lesznek megkapva. Ha felhagy egy üzenettel, a rendszer a következő fogadási művelettel ismét kiszolgálja ugyanazt az üzenetet.

### <a name="message-session-state"></a>Üzenet-munkamenet állapota

Ha a munkafolyamatokat nagy méretű, magas rendelkezésre állású felhőrendszerekben dolgozzák fel, az adott munkamenethez társított munkafolyamat-kezelőnek képesnek kell lennie a váratlan hibákból való helyreállításra, és képesnek kell lennie a részlegesen befejezett munka folytatására egy másik folyamaton vagy gépen, ahonnan a munka kezdődött.

A munkamenet-állapot létesítménye lehetővé teszi egy üzenet-munkamenet alkalmazás által definiált jegyzetét a közvetítőn belül, így az adott munkamenethez viszonyított rögzített feldolgozási állapot azonnal elérhetővé válik, amikor a munkamenetet egy új processzor szerzi be.

A Service Bus szempontjából az üzenet-munkamenet állapota egy átlátszatlan bináris objektum, amely egy üzenet méretű adatot képes tárolni, amely 256 KB Service Bus Standard és 1 MB a Service Bus Premiumhoz. A munkamenethez viszonyított feldolgozási állapot a munkamenet-állapotban tartható, vagy a munkamenet állapota mutathat egy olyan tárolási helyre vagy adatbázisrekordra, amely ilyen információkat tartalmaz.

A munkamenet-állapot (SetState és GetState) kezelésére szolgáló módszerek a munkamenet fogadóobjektumán találhatók. Egy munkamenet, amely korábban nem volt munkamenet-állapot, null értékű hivatkozást ad vissza a GetState értékhez. A korábban beállított munkamenet-állapot úgy ürítható ki, hogy null értéket ad át a SetState metódusnak a fogadón.

A munkamenet-állapot addig marad, amíg nincs törölve (null értéket ad **vissza),** még akkor is, ha egy munkamenetben az összes üzenet fel van használva.

Az üzenetsorban vagy előfizetésben lévő munkamenet-állapot beleszámol az adott entitás tárolási kvótájába. Ha az alkalmazás befejezte a munkamenetet, javasoljuk, hogy a külső felügyeleti költségek elkerülése érdekében az alkalmazás megtisztítsa a megőrzendő állapotát.

### <a name="impact-of-delivery-count"></a>A kézbesítések számának hatása

A kézbesítések száma üzenetenként a munkamenetek kontextusában némileg eltér a munkamenetek hiányában a definíciótól. Az alábbi táblázat összefoglalja, hogy mikor növekszik a kézbesítések száma.

| Eset | Növekszik az üzenet kézbesítési száma? |
|----------|---------------------------------------------|
| A rendszer elfogadja a munkamenetet, de a munkamenet-zárolás lejár (időtúllépés miatt) | Yes |
| A rendszer elfogadja a munkamenetet, a munkameneten belüli üzenetek nem fejeződnek be (még akkor sem, ha zárolva vannak), és a munkamenet lezárul | No |
| A munkamenet elfogadva, az üzenetek befejeződnek, majd a munkamenet explicit módon lezárul | N/A (ez a szabványos folyamat. Itt az üzenetek törlődnek a munkamenetből) |

## <a name="request-response-pattern"></a>Kérés-válasz minta
A [kérés-válasz](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) minta egy jól bevált integrációs minta, amely lehetővé teszi, hogy a küldőalkalmazás kérést küldjön, és lehetővé teszi, hogy a fogadó megfelelően küldjön vissza egy választ a küldőalkalmazásnak. Ennek a mintának általában rövid életű üzenetsorra vagy témakörre van szüksége ahhoz, hogy az alkalmazás válaszokat küldjön. Ebben a forgatókönyvben a munkamenetek egy egyszerű alternatív megoldást kínálnak hasonló szemantikával. 

Több alkalmazás is küldheti a kéréseit egyetlen kérés-üzenetsorba, és egy adott fejlécparaméter van beállítva a küldőalkalmazás egyedi azonosításához. A fogadóalkalmazás feldolgozhatja az üzenetsorba érkező kéréseket, és válaszokat küldhet a munkamenet-kompatibilis üzenetsorra, és a munkamenet-azonosítót a küldő által a kérésüzenetben küldött egyedi azonosítóra használhatja. A kérést elküldő alkalmazás ezután üzeneteket fogadhat az adott munkamenet-azonosítón, és megfelelően feldolgozhatja a válaszokat.

> [!NOTE]
> A kezdeti kéréseket küldő alkalmazásnak tudnia kell a munkamenet-azonosítóról, és annak használatával el kell fogadnia a munkamenetet, hogy az a munkamenet zárolva legyen, amelyre a választ várja. Jó ötlet olyan GUID azonosítót használni, amely munkamenet-azonosítóként egyedileg azonosítja az alkalmazás példányát. Az üzenetsor munkamenet-fogadója nem lehet munkamenet-kezelő vagy időtúllépés, amely biztosítja, hogy a válaszok zárolva és feldolgozva érhetők el adott fogadók számára.

## <a name="next-steps"></a>Következő lépések
Üzenetsor létrehozásakor üzenet-munkameneteket engedélyezhet az Azure Portal, a PowerShell, a parancssori felület, Resource Manager .NET, Java, Python és JavaScript használatával. További információ: [Üzenet-munkamenetek engedélyezése.](enable-message-sessions.md) 

Próbálja ki a mintákat a választott nyelven, és fedezze fel Azure Service Bus funkcióit. 

- [Azure Service Bus java-ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus Pythonhoz készült ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus JavaScripthez való ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus TypeScript ügyféloldali kódtárminái](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-minták a .NET-hez](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Az alábbiakban a régebbi .NET- és Java-ügyfélkódtárakhoz találhat mintákat:
- [Microsoft.Azure.ServiceBus-minták a .NET-hez](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-minták Javához](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

