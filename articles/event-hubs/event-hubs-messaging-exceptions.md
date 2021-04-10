---
title: Azure Event Hubs – kivételek (örökölt)
description: Ez a cikk az Azure Event Hubs üzenetkezelési kivételek és a javasolt műveletek listáját tartalmazza.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: a76c98ec7d6d1f3370ed8787bf10d1d16a7baaa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390895"
---
# <a name="event-hubs-messaging-exceptions---net-legacy"></a>Üzenetkezelési kivételek Event Hubs – .NET (örökölt)
Ez a szakasz a .NET-keretrendszer API-jai által generált .NET-kivételeket sorolja fel. 

> [!IMPORTANT]
> A cikkben felsorolt kivételek némelyike csak az örökölt Event Hubs .NET-könyvtárra vonatkozik. Például: Microsoft. ServiceBus. * kivételek.
> 
> További információ az új .NET-kódtár által generált EventHubsException: [EventHubsException-.net](exceptions-dotnet.md)

## <a name="exception-categories"></a>Kivételek kategóriái

A Event Hubs .NET API-k olyan kivételeket hoznak elő, amelyek a következő kategóriákba sorolhatók, valamint a hozzájuk tartozó műveletekkel, amelyekkel kipróbálhatja a javítást:

 - Felhasználói kódolási hiba: 
 
   - [System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)
   - [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true)
   - [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true)
   - [System. Runtime. szerializálás. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)
   
   Általános művelet: a folytatás előtt próbálja meg kijavítani a kódot.
 
 - Telepítési/konfigurációs hiba: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true)
   
   Általános művelet: Ellenőrizze a konfigurációt, és szükség esetén módosítsa.
   
 - Átmeneti kivételek: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft. ServiceBus. Messaging. ServerBusyException](#serverbusyexception)
   - [Microsoft. Azure. EventHubs. ServerBusyException](#serverbusyexception)
   - [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Általános művelet: Próbálja megismételni a műveletet, vagy értesítse a felhasználókat.
 
 - Egyéb kivételek: 
 
   - [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true)
   - [System. Timeoutexception osztályról](#timeoutexception)
   - [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Általános művelet: a kivétel típusára jellemző; Tekintse át a következő szakaszban található táblázatot. 

## <a name="exception-types"></a>Kivételek típusai
Az alábbi táblázat az üzenetkezelési kivételek típusait, valamint azok okait és megjegyzéseit sorolja fel.

| Kivétel típusa | Leírás/ok/példák | Javasolt művelet | Megjegyzés automatikus/azonnali újrapróbálkozás |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [Timeoutexception osztályról](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |A kiszolgáló a megadott időn belül nem válaszolt a kért műveletre, amelyet a [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vezérel. Lehet, hogy a kiszolgáló végrehajtotta a kért műveletet. Ez a kivétel hálózati vagy más infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotot a konzisztencia érdekében, és szükség esetén próbálkozzon újra.<br /> Lásd: [timeoutexception osztályról](#timeoutexception). | Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részletekért tekintse meg a kivételt jelző üzenetet. A [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) például ezt a kivételt hozza létre, ha az üzenet [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) módban érkezett. | Keresse meg a kódot és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvényes. | Az újrapróbálkozás nem segít. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) | Kísérlet történt egy olyan objektum műveletének meghívására, amely már be van zárva, megszakadt vagy el lett távolítva. Ritka esetekben a környezeti tranzakció már el van távolítva. | Ellenőrizze a kódot, és győződjön meg róla, hogy nem hív meg műveleteket egy eldobott objektumon. | Az újrapróbálkozás nem segít. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) | A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott jogkivonatot beszerezni, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet végrehajtásához szükséges jogcímeket. | Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel lett létrehozva. Vizsgálja meg a Access Control Service konfigurációját. | Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) | A metódushoz megadott egy vagy több argumentum érvénytelen. A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-azonosító szegmens (eke) t tartalmaz. A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy- [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-séma érvénytelen. A tulajdonság értéke nagyobb, mint 32 KB. | Ellenőrizze a hívó kódját, és ellenőrizze, hogy helyesek-e az argumentumok. | Az újrapróbálkozás nem segít. |
| [Microsoft. ServiceBus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A művelethez társított entitás nem létezik, vagy törölték. | Győződjön meg arról, hogy az entitás létezik. | Az újrapróbálkozás nem segít. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Az ügyfél nem tud kapcsolatot létesíteni az Event hub szolgáltatással. |Győződjön meg arról, hogy a megadott állomásnév helyes, és a gazdagép elérhető. | Az újrapróbálkozás akkor lehet hasznos, ha akadozó kapcsolódási problémák léptek fel. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. | Az ügyfél hosszabb ideig is megvárhat, majd próbálja megismételni a műveletet. <br /> Lásd: [ServerBusyException](#serverbusyexception). | Előfordulhat, hogy az ügyfél bizonyos intervallum után újra próbálkozik. Ha az újrapróbálkozások eltérő kivételt eredményeznek, akkor ellenőrizze, hogy az újrapróbálkozási viselkedést az adott kivétel okozza. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Általános üzenetküldési kivétel, amely a következő esetekben fordulhat elő: kísérlet történt egy [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) létrehozására egy másik típusú entitáshoz tartozó névvel vagy elérési úttal (például egy témakör). 1 MB-nál nagyobb üzenet küldésére történt kísérlet. A kiszolgáló vagy szolgáltatás hibát észlelt a kérelem feldolgozása során. A részletekért tekintse meg a kivételt jelző üzenetet. Ez a kivétel általában átmeneti kivétel. | Ellenőrizze a kódot, és győződjön meg arról, hogy csak szerializálható objektumok használatosak az üzenet törzséhez (vagy használjon egyéni szerializáló). Keresse meg a tulajdonságok támogatott értékeit, és csak a támogatott típusokat használja. Keresse meg a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonságot. Ha az értéke **igaz**, megismételheti a műveletet. | Az újrapróbálkozási viselkedés nincs meghatározva, és lehet, hogy nem segít. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Kísérlet történt olyan entitás létrehozására, amelynek a neve már használja egy másik entitás által az adott szolgáltatási névtérben. | Törölje a meglévő entitást, vagy válasszon másik nevet a létrehozandó entitás számára. | Az újrapróbálkozás nem segít. |
| [Quotaexceededexception osztályról](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Az üzenetküldési entitás elérte a maximálisan engedélyezett méretet. Ez a kivétel akkor fordulhat elő, ha a fogadók maximális száma (amely 5) már meg van nyitva felhasználónkénti csoport szintjén. | Hozzon létre helyet az entitásban az entitásból vagy annak alvárólistából érkező üzenetek fogadásával. <br /> Lásd: [quotaexceededexception osztályról](#quotaexceededexception) | Az újrapróbálkozás akkor lehet hasznos, ha az üzenetek időközben el lettek távolítva. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Futásidejű művelet kérése letiltott entitáson. |Aktiválja az entitást. | Az újrapróbálkozás segíthet abban az esetben, ha az entitást ideiglenesen aktiválták. |
| [Microsoft. ServiceBus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Az üzenet tartalma meghaladja az 1 MB-os korlátot. Ez az 1 MB-os korlát az összes üzenet, amely tartalmazhatja a rendszer tulajdonságait és a .NET-terhelést. | Csökkentse az üzenet adattartalmát, majd próbálja megismételni a műveletet. |Az újrapróbálkozás nem segít. |

## <a name="quotaexceededexception"></a>Quotaexceededexception osztályról
A [quotaexceededexception osztályról](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitásra vonatkozó kvóta túllépve.

Ez a kivétel akkor fordulhat elő, ha a fogadók maximális száma (5) már meg van nyitva felhasználónkénti csoport szintjén.

### <a name="event-hubs"></a>Event Hubs
Az Event Hubs legfeljebb 20 fogyasztói csoportot tartalmaz. Ha további kísérletet szeretne létrehozni, [quotaexceededexception osztályról](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception)kap. 

## <a name="timeoutexception"></a>Timeoutexception osztályról
A [timeoutexception osztályról](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) azt jelzi, hogy a felhasználó által kezdeményezett művelet a művelet időkorlátja alatt hosszabb időt vesz igénybe. 

Event Hubs esetében az időtúllépés a kapcsolati sztring részeként vagy a [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)-n keresztül van megadva. Maga a hibaüzenet is változhat, de mindig az aktuális művelethez megadott időtúllépési értéket tartalmazza. 

Az időtúllépések a karbantartási műveletek (például a Event Hubs Service Updates (vagy) operációsrendszer-frissítései a szolgáltatást futtató erőforrásokon vagy azok között történnek. Az operációsrendszer-frissítések során az entitások áthelyezése és a csomópontok frissítése vagy újraindítása történik, ami időtúllépéseket eredményezhet. Az Azure Event Hubs szolgáltatás szolgáltatói szerződéssel (SLA) kapcsolatos részleteiért lásd: [SLA a Event Hubshoz](https://azure.microsoft.com/support/legal/sla/event-hubs/). 


### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van: helytelen konfiguráció vagy átmeneti szolgáltatási hiba.

- **Helytelen konfiguráció** Lehet, hogy a művelet időtúllépése túl kicsi a működési feltételnél. Az ügyfél-SDK-ban a művelet időtúllépésének alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy a kód értéke túl kicsi-e. A hálózat és a CPU-használat feltétele befolyásolhatja az adott művelet befejezéséhez szükséges időt, így a művelet időtúllépése nem állítható be kis értékre.
- **Átmeneti szolgáltatás hibája** Időnként előfordulhat, hogy a Event Hubs szolgáltatás késéseket tapasztal a feldolgozási kérelmekben; például a nagy forgalmú időszakokban. Ilyen esetekben egy késleltetés után újra megpróbálkozhat a művelettel, amíg a művelet nem sikerült. Ha ugyanez a művelet több kísérlet után is meghiúsul, látogasson el az [Azure szolgáltatás állapota webhelyre](https://azure.microsoft.com/status/) , és ellenőrizze, hogy vannak-e ismert szolgáltatások.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) vagy A [Microsoft. Azure. EventHubs. ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) azt jelzi, hogy egy kiszolgáló túlterhelt. Ehhez a kivételhez két megfelelő hibakód van.

### <a name="error-code-50002"></a>50002-es hibakód
Ez a hiba két okból fordulhat elő:

- A terhelés nem egyenletesen oszlik el az Event hub összes partícióján, és egy partíció a helyi átviteli egységre vonatkozó korlátozást eredményez.
    
    **Megoldás**: a partíció-terjesztési stratégia felülvizsgálata vagy a [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kipróbálása segíthet.

- A Event Hubs névtérnek nincs elegendő átviteli egysége (a [Azure Portal](https://portal.azure.com) Event Hubs névtér ablakában ellenőrizheti a **mérőszámok** képernyőt a megerősítéshez). A portál összesített (1 perces) adatokat jelenít meg, de az átviteli sebességet valós időben mérjük, így csak becslést lehet készíteni.

    **Megoldás**: a névtérben található átviteli egységek növelése segíthet. 

    Az átviteli egységeket a Azure Portal **Event Hubs névtér** oldalának **Méretezés** lapján vagy **Áttekintés** lapján konfigurálhatja. Vagy használhatja az [automatikus](event-hubs-auto-inflate.md)kiemelést is, amely az átviteli egységek számának növelésével automatikusan méretezhető, hogy megfeleljen a használati igényeknek.

    Az átviteli egységek (TUs) az Event Hubs-névtérben lévő összes esemény-hubhoz érvényesek. Ez azt jelenti, hogy a rendszer a névtér szintjén vásárolja meg az TUs-t, és az adott névtér alá tartozó Event hubok között meg van osztva. Mindegyik TU a következő képességekhez jogosítja fel a névteret:

    - Akár 1 MB/s a bejövő események másodpercenkénti száma (esemény központba elküldve), de legfeljebb 1000 bejövő esemény, felügyeleti művelet vagy vezérlési API-hívás másodpercenként.
    - Akár 2 MB/s a kimenő események másodpercenkénti száma (az Event hub által felhasznált események), de legfeljebb 4096 kimenő esemény.
    - Akár 84 GB-nyi esemény-tárterület (elegendő az alapértelmezett 24 órás megőrzési időszakhoz).
    
    Az **Áttekintés** lap **mérőszámok megjelenítése** szakaszában váltson át az **átviteli sebesség** lapra. A diagramot kiválasztva megnyithatja azt egy nagyobb ablakban, az x tengelyen pedig 1 perces időközökkel. Tekintse meg a csúcsérték-értékeket, és ossza meg őket 60-re a bejövő bájtok/másodpercek vagy a kimenő bájtok/másodpercek lekéréséhez. Hasonló megközelítéssel számíthatja **ki a kérések lapon a** kérelmek másodpercenkénti számát. 

    Ha az értékek nagyobbak, mint az adatmennyiség * korlát (1 MB/s a bejövő forgalomhoz, vagy 1000 bejövő forgalom/másodperc, 2 MB/s a kimenő forgalomhoz), növelje az TUs számát egy Event Hubs névtér **méretével** (a bal oldali menüben), vagy használja a Event Hubs [automatikus](event-hubs-auto-inflate.md) méretezési funkcióját. Vegye figyelembe, hogy az automatikus feltöltés legfeljebb 20 TUS-t tud emelni. Ahhoz, hogy pontosan 40 TUs-re növelje, küldjön egy [támogatási kérést](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="error-code-50008"></a>50008-es hibakód

Ennek a hibának ritkán kell történnie. Ez akkor történik meg, amikor a névtér kódját futtató tárolója alacsony a CPU-ban – nem több, mint néhány másodperc, mielőtt megkezdődik a Event Hubs Load Balancer.

**Megoldás**: korlátozza a GetRuntimeInformation metódus felé irányuló hívásokat. Az Azure Event Hubs felhasználónkénti csoportonként legfeljebb 50 hívást támogat a GetRuntimeInfo másodpercenként. A korlát elérésekor a következőhöz hasonló kivétel jelenhet meg:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50008. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](./event-hubs-about.md)
* [Event hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)