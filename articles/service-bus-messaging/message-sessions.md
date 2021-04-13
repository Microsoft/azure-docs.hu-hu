---
title: Azure Service Bus üzenet-munkamenetek | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a munkamenetek a kapcsolódó üzenetek nem kötött sorrendjének közös és rendezett kezelésének engedélyezéséhez.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: c9a1c4fdccbbc8b38805e23d4895448959126f10
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308478"
---
# <a name="message-sessions"></a>Üzenet-munkamenetek
Microsoft Azure Service Bus-munkamenetek lehetővé teszik a kapcsolódó üzenetek nem kötött sorrendjének együttes és rendezett kezelését. A munkamenetek a-ben **, az elsőben (FIFO)** és a **kérés-válasz** mintákban is használhatók. Ez a cikk bemutatja, hogyan használhatja a munkameneteket ezen minták megvalósításához Service Bus használatakor. 

> [!NOTE]
> A Service Bus alapszintű csomagja nem támogatja a munkamenetek használatát. A standard és a prémium szintű csomag támogatja a munkameneteket. A szintek közötti különbségekért tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Első – első kimenő (FIFO) minta
Ha Service Bus-ben a FIFO-garanciát szeretné megvalósítani, használja a munkameneteket. Service Bus nem rendelkezik az üzenetek közötti kapcsolat természetétől, és nem határoz meg egy adott modellt, amely meghatározza, hogy a rendszer hol induljon el vagy végződik.

Bármely feladó létrehozhat egy munkamenetet, amikor üzeneteket küld egy témakörbe vagy várólistába úgy, hogy a **munkamenet-azonosító** tulajdonságot egy olyan alkalmazás által definiált azonosítóra állítja be, amely egyedi a munkamenetben. Az AMQP 1,0 protokoll szintjén ez az érték a *Group-ID* tulajdonságra van leképezve.

A munkamenet-kompatibilis várólistákon vagy előfizetéseken a munkamenetek akkor jönnek létre, ha legalább egy üzenet van a munkamenet-AZONOSÍTÓval. Ha a munkamenet már létezik, nincs definiálva idő vagy API a munkamenet lejáratának vagy eltűnésének idejére. Elméletileg egy üzenet érkezik egy adott munkamenethez, a következő üzenet egy évben, és ha a munkamenet-azonosító megegyezik, a munkamenet megegyezik a Service Bus perspektívával.

Az alkalmazások jellemzően azonban egyértelmű fogalmat mutatnak arról, hogy hol kezdődnek és végződik a kapcsolódó üzenetek halmaza. Service Bus nem állít be konkrét szabályokat. Az alkalmazás például megadhatja a **label (címke** ) tulajdonságot az első üzenet **elindításához**, a köztes üzenetekhez a **tartalomhoz**, valamint az utolsó üzenet **befejezéséhez**. A tartalmi üzenetek relatív helyzete a **Start** Message *sorszám* származó aktuális *sorszám* -különbözetként számítható ki.

A szolgáltatás engedélyezéséhez állítsa be az [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) tulajdonságot a várólistán vagy az előfizetésen a Azure Resource Manageron keresztül, vagy állítsa be a jelölőt a portálon. A kapcsolódó API-műveletek használatának megkísérlése előtt szükség van rá.

A portálon engedélyezheti a munkameneteket, miközben az alábbi példákban látható módon létrehoz egy entitást (Üzenetsor vagy előfizetés). 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Munkamenet engedélyezése a várólista létrehozásának időpontjában":::

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Munkamenet engedélyezése az előfizetés létrehozásának időpontjában":::


> [!IMPORTANT]
> Ha a munkamenetek engedélyezve vannak egy várólistán vagy előfizetésen, az ügyfélalkalmazások ***többé nem*** küldhetnek és fogadhatnak rendszeres üzeneteket. Minden üzenetet el kell juttatni a munkamenet részeként (a munkamenet-azonosító beállításával), és a fogadást a munkamenet elfogadásával.

A munkamenetek API-jai léteznek a várólista-és előfizetési ügyfeleken. Van egy kötelező modell, amely a munkamenetek és üzenetek fogadását vezérli, valamint egy kezelő-alapú modellt, amely elrejti a fogadási hurok kezelésének bonyolultságát. 

A példákat a [következő lépések](#next-steps) szakaszban található hivatkozásokra kattintva végezheti el. 

### <a name="session-features"></a>Munkamenet-funkciók

A munkamenetek egyidejű, egymással párhuzamosan megjelenő üzeneteket biztosítanak a megrendelt kézbesítés megőrzése és garantálása mellett.

![Egy diagram, amely azt mutatja, hogy a munkamenetek szolgáltatás hogyan őrzi meg a rendezett kézbesítést.][1]

A munkamenet-fogadót egy-munkamenetet elfogadó ügyfél hozza létre. Ha a munkamenetet elfogadják és egy ügyfél tartja, az ügyfél kizárólagos zárolást tart az adott munkamenet **munkamenet-azonosítójával** rendelkező összes üzenetben a várólistán vagy az előfizetésen belül. Emellett a **munkamenet-azonosítóval** rendelkező összes üzenet kizárólagos zárolását is tartalmazza, amely később érkezik.

A zárolás akkor jelenik meg, ha meghívja a lezárt kapcsolódó metódusokat a fogadón, vagy amikor lejár a zárolás. A fogadó módszerekkel megújíthatja a zárolásokat is. Ehelyett használhatja az automatikus zárolás megújítása funkciót, ahol megadhatja azt az időtartamot, ameddig szeretné megőrizni a zárolást. A munkamenet-zárolást úgy kell kezelni, mint egy fájl kizárólagos zárolását, ami azt jelenti, hogy az alkalmazásnak le kell zárnia a munkamenetet, amint már nincs rá szüksége, és/vagy nem vár semmilyen további üzenetet.

Ha több párhuzamos fogadó is lekéri a várólistáról, az adott munkamenethez tartozó üzeneteket a rendszer az adott munkamenethez tartozó zárolást betöltő adott fogadónak küldi el. Ezzel a művelettel az egyik várólistában vagy előfizetésben lévő, egymástól elválasztott üzenetek streamje tiszta módon, különböző fogadók számára érhető el, és ezek a fogadók különböző ügyfélszámítógépeken is élhetnek, mivel a zárolási felügyelet Service Buson belül történik.

Az előző ábrán három egyidejű munkamenet-fogadó látható. Az egyik munkamenetben a `SessionId` = 4 nem rendelkezik aktív, tulajdonosi ügyféllel, ami azt jelenti, hogy az adott munkamenetből nem érkeznek üzenetek. A munkamenetek számos módon működnek, mint például az alárendelt üzenetsor.

A munkamenet-fogadó által tárolt munkamenet-zárolás egy esernyő a *betekintési zárolási* mód által használt üzenetek zárolásához. Egy munkamenetben csak egy fogadó rendelkezhet zárolással. Előfordulhat, hogy egy fogadó több fedélzeti üzenetet tartalmaz, de az üzenetek sorrendben érkeznek. Az üzenet elhagyása azt eredményezi, hogy ugyanazt az üzenetet ismét a következő fogadási művelettel kézbesíti a rendszer.

### <a name="message-session-state"></a>Üzenet-munkamenet állapota

Ha a munkafolyamatokat nagy léptékű, magas rendelkezésre állású felhőalapú rendszerekben dolgozzák fel, akkor az adott munkamenethez társított munkafolyamat-kezelőnek képesnek kell lennie a váratlan hibák helyreállítására, és a részben befejezett munkát egy másik folyamaton vagy gépen végezheti el, ahol a munka megkezdődött.

A munkamenet-állapot lehetőség lehetővé teszi egy üzenet-munkamenet alkalmazás által meghatározott megjegyzésének megadását a közvetítőn belül, így az adott munkamenethez viszonyított rögzített feldolgozási állapot azonnal elérhetővé válik, amikor egy új processzor szerzi be a munkamenetet.

Az Service Bus perspektívából az üzenet-munkamenet állapota egy átlátszatlan bináris objektum, amely egy üzenet méretének tárolására képes, amely 256 KB Service Bus standard és 1 MB a Service Bus Premium esetében. A munkamenet-állapothoz viszonyított feldolgozási állapot a munkamenet-állapotban tartható, vagy a munkamenet-állapot olyan tárolási helyre vagy adatbázis-rekordra mutathat, amely az ilyen adatokat tárolja.

A munkamenet-állapot, a SetState és a GetState kezelésének módszerei a munkamenet-fogadó objektumon találhatók. Egy olyan munkamenet, amely korábban nem tartalmazott munkamenet-állapotot, null hivatkozást ad vissza a GetState. A korábban beállított munkamenet-állapot törölhető úgy, hogy null értéket továbbít a fogadó SetState metódusának.

A munkamenet-állapot addig marad mindaddig, amíg nincs törölve ( **Null értékre** tér vissza), még akkor is, ha a munkamenetben lévő összes üzenetet felhasználják.

Egy várólistában vagy előfizetésben tárolt munkamenet-állapot az entitás tárolási kvótája felé mutat. Ha az alkalmazás egy munkamenettel fejeződött be, ezért ajánlott az alkalmazás számára megőrizni a megőrzött állapotot, hogy elkerülje a külső felügyeleti költségeket.

### <a name="impact-of-delivery-count"></a>A kézbesítések számának következményei

Az üzenetek kézbesítési számának a munkamenetek kontextusában való meghatározása a munkamenetek hiányában a definíciótól némileg eltér. Itt látható egy táblázat, amely összegzi a kézbesítések számának növelését.

| Eset | Az üzenet kézbesítési száma nő |
|----------|---------------------------------------------|
| A munkamenet el van fogadva, de a munkamenet zárolása lejár (időtúllépés miatt) | Yes |
| A munkamenet elfogadása megtörtént, a munkamenetben lévő üzenetek nem lesznek végrehajtva (még akkor is, ha zárolva vannak), és a munkamenet be van zárva. | No |
| A munkamenet elfogadva, az üzenetek befejeződtek, majd a munkamenet explicit módon be van zárva | N/A (ez a standard folyamat. Itt az üzenetek el lesznek távolítva a munkamenetből) |

## <a name="request-response-pattern"></a>Kérelem – válasz minta
A [kérelem-válasz minta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) egy jól bevált integrációs minta, amely lehetővé teszi, hogy a küldő alkalmazás küldjön egy kérést, és módot biztosít arra, hogy a fogadó helyesen küldjön vissza választ a küldő alkalmazásnak. Ez a minta általában egy rövid élettartamú várólistára vagy témakörre van szüksége ahhoz, hogy az alkalmazás válaszokat küldjön. Ebben az esetben a munkamenetek egy egyszerű alternatív megoldást biztosítanak, amely hasonló szemantikai megoldásokkal rendelkezik. 

Több alkalmazás is elküldheti kérelmeit egyetlen kérelem-várólistába, egy adott fejléc-paraméterrel, amely egyedileg azonosítja a küldő alkalmazást. A fogadó alkalmazás képes feldolgozni a várólistára érkező kéréseket, és elküldi a válaszokat a munkamenet-kompatibilis várólistán, a munkamenet-azonosítót pedig arra a egyedi azonosítóra állítja, amelyet a küldő a kérelem üzenetében küldött. A kérést elküldő alkalmazás az adott munkamenet-AZONOSÍTÓra vonatkozó üzeneteket fogadhat, és helyesen dolgozza fel a válaszokat.

> [!NOTE]
> A kezdeti kérelmeket küldő alkalmazásnak ismernie kell a munkamenet-azonosítót, és annak használatával el kell fogadnia a munkamenetet, hogy az a munkamenet, amelyen a válasz vár, zárolva legyen. Érdemes olyan GUID-t használni, amely egyedileg azonosítja az alkalmazás példányát munkamenet-azonosítóként. A várólista munkamenet-fogadóján nincs megadva munkamenet-kezelő vagy időtúllépés, így biztosítható, hogy a válaszok zárolva legyenek, és az adott fogadók feldolgozzák azokat.

## <a name="next-steps"></a>Következő lépések

- [Azure. Messaging. ServiceBus-minták a .NET-hez](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [A Java-hez készült ügyféloldali kódtár Azure Service Bus – minták](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [A Pythonhoz készült ügyféloldali kódtár Azure Service Bus – minták](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [A JavaScript-hez készült ügyféloldali kódtár Azure Service Buse – minták](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus a géppel készült ügyféloldali kódtár – minták](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Microsoft. Azure. ServiceBus-minták .net-hez](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) (munkamenetek és SessionState minták)  

Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a [Service Bus várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)című témakört.

[1]: ./media/message-sessions/sessions.png
