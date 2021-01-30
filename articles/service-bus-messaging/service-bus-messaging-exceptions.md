---
title: Azure Service Bus – üzenetkezelési kivételek | Microsoft Docs
description: Ez a cikk felsorolja az Azure Service Bus üzenetkezelési kivételeket és a kivétel bekövetkezésekor végrehajtandó javasolt műveleteket.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: bfd6a0c5ae1298b722b456d68d00c5b31e6dd694
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097015"
---
# <a name="service-bus-messaging-exceptions"></a>Üzenetkezelési kivételek Service Bus
Ez a cikk a .NET-keretrendszer API-jai által generált .NET-kivételeket sorolja fel. 

## <a name="exception-categories"></a>Kivételek kategóriái
Az üzenetkezelési API-k olyan kivételeket állítanak elő, amelyek a következő kategóriákba sorolhatók, valamint a hozzájuk tartozó műveletekkel, amelyekkel kipróbálhatja a javítást. A kivételek jelentése és okai az üzenetkezelési entitás típusától függően eltérőek lehetnek:

1. Felhasználói kódolási hiba ([System. ArgumentException](/dotnet/api/system.argumentexception?view=net-5.0), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=net-5.0), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=net-5.0), [System. Runtime. szerializálás. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=net-5.0)). Általános művelet: a folytatás előtt próbálja meg kijavítani a kódot.
2. Telepítési/konfigurációs hiba ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=net-5.0). Általános művelet: Ellenőrizze a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Általános művelet: Próbálja megismételni a műveletet, vagy értesítse a felhasználókat. Az `RetryPolicy` ügyfél-SDK osztálya beállítható úgy, hogy automatikusan kezelje az újrapróbálkozásokat. További információ: [újrapróbálkozási útmutató](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Egyéb kivételek ([System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception?view=net-5.0), [System. timeoutexception osztályról](/dotnet/api/system.timeoutexception?view=net-5.0), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Általános művelet: a kivétel típusára jellemző; Tekintse át a következő szakaszban található táblázatot: 

## <a name="exception-types"></a>Kivételek típusai
Az alábbi táblázat az üzenetkezelési kivételek típusait, valamint azok okait és megjegyzéseit sorolja fel.

| **Kivétel típusa** | **Leírás/ok/példák** | **Javasolt művelet** | **Megjegyzés automatikus/azonnali újrapróbálkozás** |
| --- | --- | --- | --- |
| [Timeoutexception osztályról](/dotnet/api/system.timeoutexception?view=net-5.0) |A kiszolgáló a megadott időn belül nem válaszolt a kért műveletre, amelyet a [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vezérel. Lehet, hogy a kiszolgáló végrehajtotta a kért műveletet. Hálózati vagy más infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotot a konzisztencia érdekében, és szükség esetén próbálkozzon újra. Lásd: [időtúllépési kivételek](#timeoutexception). |Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=net-5.0) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részletekért tekintse meg a kivételt jelző üzenetet. Például a [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ezt a kivételt hozza létre, ha az üzenet [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban érkezett. |Keresse meg a kódot és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvényes. |Az újrapróbálkozás nem segít. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=net-5.0) |Kísérlet történt egy olyan objektum műveletének meghívására, amely már be van zárva, megszakadt vagy el lett távolítva. Ritka esetekben a környezeti tranzakció már el van távolítva. |Ellenőrizze a kódot, és győződjön meg róla, hogy nem hív meg műveleteket egy eldobott objektumon. |Az újrapróbálkozás nem segít. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=net-5.0) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott jogkivonatot beszerezni, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet végrehajtásához szükséges jogcímeket. |Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel lett létrehozva. Vizsgálja meg a Access Control Service konfigurációját. |Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=net-5.0)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=net-5.0)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=net-5.0) |A metódushoz megadott egy vagy több argumentum érvénytelen.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-azonosító szegmens (eke) t tartalmaz.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy- [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-séma érvénytelen. <br />A tulajdonság értéke nagyobb, mint 32 KB. |Ellenőrizze a hívó kódját, és ellenőrizze, hogy helyesek-e az argumentumok. |Az újrapróbálkozás nem segít. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A művelethez társított entitás nem létezik, vagy törölve lett. |Győződjön meg arról, hogy az entitás létezik. |Az újrapróbálkozás nem segít. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Egy megadott sorszámú üzenet fogadására tett kísérlet. Ez az üzenet nem található. |Győződjön meg arról, hogy az üzenet már nem érkezett meg. Tekintse meg a kézbesítetlen levelek-várólistát, és ellenőrizze, hogy az üzenet el lett-e deadlettered. |Az újrapróbálkozás nem segít. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Az ügyfél nem tud kapcsolatot létesíteni Service Bushoz. |Győződjön meg arról, hogy a megadott állomásnév helyes, és a gazdagép elérhető. <p>Ha a kód egy tűzfallal vagy proxyval rendelkező környezetben fut, győződjön meg arról, hogy a Service Bus tartományhoz/IP-címhez és portokhoz tartozó forgalom nincs letiltva.</p>|Az újrapróbálkozás akkor lehet hasznos, ha akadozó kapcsolódási problémák léptek fel. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. |Az ügyfél hosszabb ideig is megvárhat, majd próbálja megismételni a műveletet. |Előfordulhat, hogy az ügyfél bizonyos intervallum után újra próbálkozik. Ha az újrapróbálkozások eltérő kivételt eredményeznek, akkor ellenőrizze, hogy az újrapróbálkozási viselkedést az adott kivétel okozza. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Általános üzenetküldési kivétel, amely a következő esetekben fordulhat elő:<p>Kísérlet történt olyan [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) létrehozására, amely egy másik entitás típusához (például egy témakörhöz) tartozó névvel vagy elérési úttal rendelkezik.</p><p>Kísérlet történt a 256 KB-nál nagyobb üzenetek küldésére. </p>A kiszolgáló vagy szolgáltatás hibát észlelt a kérelem feldolgozása során. A részletekért tekintse meg a kivételt jelző üzenetet. Ez általában átmeneti kivétel.</p><p>A kérést a rendszer leállította, mert folyamatban van az entitás szabályozása. Hibakód: 50001, 50002, 50008. </p> | Ellenőrizze a kódot, és győződjön meg arról, hogy csak szerializálható objektumok használatosak az üzenet törzséhez (vagy használjon egyéni szerializáló). <p>Keresse meg a tulajdonságok támogatott értékeit, és csak a támogatott típusokat használja.</p><p> Keresse meg a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonságot. Ha az **értéke igaz**, megismételheti a műveletet. </p>| Ha a kivételt a szabályozás okozta, várjon néhány másodpercig, és ismételje meg a műveletet. Az újrapróbálkozási viselkedés nincs meghatározva, és előfordulhat, hogy más helyzetekben nem segít.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Kísérlet történt olyan entitás létrehozására, amelynek a neve már használja egy másik entitás által az adott szolgáltatási névtérben. |Törölje a meglévő entitást, vagy válasszon másik nevet a létrehozandó entitás számára. |Az újrapróbálkozás nem segít. |
| [Quotaexceededexception osztályról](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Az üzenetküldési entitás elérte a maximálisan megengedett méretet, vagy túllépte a névtér kapcsolatainak maximális számát. |Hozzon létre helyet az entitásban az entitásból vagy annak alvárólistából érkező üzenetek fogadásával. Lásd: [quotaexceededexception osztályról](#quotaexceededexception). |Az újrapróbálkozás akkor lehet hasznos, ha az üzenetek időközben el lettek távolítva. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |A Service Bus ezt a kivételt adja vissza, ha érvénytelen szabály-műveletet próbál meg létrehozni. Service Bus ezt a kivételt csatolja egy deadlettered-üzenethez, ha hiba történik az üzenetre vonatkozó szabály műveletének feldolgozása során. |Győződjön meg arról, hogy a szabály művelete megfelelő. |Az újrapróbálkozás nem segít. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |A Service Bus ezt a kivételt adja vissza, ha érvénytelen szűrőt próbál létrehozni. A Service Bus ezt a kivételt egy deadlettered-üzenethez csatolja, ha hiba történt az üzenet szűrőjének feldolgozása során. |Győződjön meg a szűrő helyességéről. |Az újrapróbálkozás nem segít. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Kísérlet történt egy munkamenet elfogadására egy adott munkamenet-AZONOSÍTÓval, de a munkamenetet jelenleg egy másik ügyfél zárolta. |Győződjön meg arról, hogy a munkamenetet más ügyfelek is feloldják. |Az újrapróbálkozás segíthet abban az esetben, ha a munkamenetet ideiglenes kiadásban adták ki. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Túl sok művelet része a tranzakciónak. |Csökkentse a tranzakció részét képező műveletek számát. |Az újrapróbálkozás nem segít. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Futásidejű művelet kérése letiltott entitáson. |Aktiválja az entitást. |Az újrapróbálkozás segíthet abban az esetben, ha az entitást ideiglenesen aktiválták. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |A Service Bus ezt a kivételt adja vissza, ha üzenetet küld egy olyan témakörnek, amelyen engedélyezve van az előzetes szűrés, és a szűrők egyike sem egyezik. |Győződjön meg arról, hogy legalább egy szűrő egyezik. |Az újrapróbálkozás nem segít. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Az üzenet tartalma meghaladja az 256-KB korlátot. Az 256-KB korlát a teljes üzenet mérete, amely magában foglalhatja a rendszertulajdonságokat és a .NET-terhelést is. |Csökkentse az üzenet adattartalmát, majd próbálja megismételni a műveletet. |Az újrapróbálkozás nem segít. |
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |A környezeti tranzakció (*tranzakció. Current*) érvénytelen. Lehetséges, hogy a művelet befejeződött vagy meg lett szakítva. A belső kivétel további információkat is biztosíthat. | |Az újrapróbálkozás nem segít. |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception) |Egy művelet egy olyan tranzakcióra próbálkozik, amely kétséges, vagy kísérlet történt a tranzakció véglegesítére, és a tranzakció kétségbe kerül. |Az alkalmazásnak kezelnie kell ezt a kivételt (különleges esetben), mert a tranzakció már véglegesítve lett. |- |

## <a name="quotaexceededexception"></a>Quotaexceededexception osztályról
A [quotaexceededexception osztályról](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelzi, hogy egy adott entitásra vonatkozó kvóta túllépve.

### <a name="queues-and-topics"></a>Várólisták és témakörök
Várólisták és témakörök esetében ez gyakran a várólista mérete. A hibaüzenet tulajdonsága további részleteket tartalmaz, ahogy az alábbi példában is látható:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Az üzenet azt jelzi, hogy a témakör túllépte a méretkorlátot, ebben az esetben 1 GB-ot (az alapértelmezett méretkorlát). 

### <a name="namespaces"></a>Névterek

A névterek esetében a [quotaexceededexception osztályról](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) jelezheti, hogy egy alkalmazás túllépte a névtérrel létesített kapcsolatok maximális számát. Például:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van: a kézbesítetlen levelek várólistája és a nem működő üzenetek fogadói.

1. **[Kézbesítetlen levelek várólistája](service-bus-dead-letter-queues.md)** Az olvasó nem teljesíti az üzeneteket, és az üzenetek az üzenetsor/témakörbe kerülnek vissza, amikor a zárolás lejár. Ez akkor fordulhat elő, ha az olvasó olyan kivételt észlel, amely megakadályozza a [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)meghívását. Az üzenet 10 alkalommal való olvasása után a rendszer alapértelmezés szerint áthelyezi a kézbesítetlen levelek várólistára. Ezt a viselkedést a [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) tulajdonság szabályozza, és az alapértelmezett érték 10. Az üzenetek a kézbesítetlen levelek várólistáján halmoznak fel helyet.
   
    A probléma megoldásához olvassa el és fejezze be az üzeneteket a kézbesítetlen levelek várólistából, ahogy azt bármely más üzenetsor esetében tenné. A [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metódus segítségével formázhatja a kézbesítetlen levelek várólistájának elérési útját.
2. A **fogadó leállt**. Egy fogadó leállt egy üzenetsor vagy előfizetés üzeneteinek fogadásával. Ennek azonosítására az [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) tulajdonságot kell megtekinteni, amely az üzenetek teljes bontását mutatja. Ha a [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) tulajdonság magas vagy növekvő, akkor az üzenetek nem olvashatók be az írás során.

## <a name="timeoutexception"></a>Timeoutexception osztályról
A [timeoutexception osztályról](/dotnet/api/system.timeoutexception?view=net-5.0) azt jelzi, hogy a felhasználó által kezdeményezett művelet a művelet időkorlátja alatt hosszabb időt vesz igénybe. 

Győződjön meg arról, hogy a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=net-5.0) tulajdonság értéke, mivel ez a korlát is okozhat [timeoutexception osztályról](/dotnet/api/system.timeoutexception?view=net-5.0).

Az időtúllépések a karbantartási műveletek (például a Service Bus Service Updates (vagy) operációsrendszer-frissítései a szolgáltatást futtató erőforrásokon vagy azok között történnek. Az operációsrendszer-frissítések során az entitások áthelyezése és a csomópontok frissítése vagy újraindítása történik, ami időtúllépéseket eredményezhet. A Azure Service Bus szolgáltatás szolgáltatói szerződéssel (SLA) kapcsolatos részleteiért lásd: [SLA a Service Bushoz](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Várólisták és témakörök
A várólisták és témakörök esetében az időtúllépést a [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) tulajdonság adja meg a kapcsolati karakterlánc részeként, vagy a [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder)-n keresztül. Maga a hibaüzenet is változhat, de mindig az aktuális művelethez megadott időtúllépési értéket tartalmazza. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Ok

A **MessageLockLostException** akkor kerül kiosztásra, ha a [PeekLock](message-transfers-locks-settlement.md#peeklock) fogadás mód használatával érkezik üzenet, és az ügyfél által megőrzött zárolás lejár a szolgáltatás oldalán.

Előfordulhat, hogy az üzenet zárolása különböző okok miatt lejár: 

  * A zárolási időzítő lejárt az ügyfélalkalmazás megújítása előtt.
  * Az ügyfélalkalmazás megszerezte a zárolást, mentette azt egy állandó tárolóba, majd újraindította. Az újraindítást követően az ügyfélalkalmazás megtekintette a fedélzeti üzeneteket, és megpróbálta befejezni ezeket.

### <a name="resolution"></a>Feloldás

**MessageLockLostException** esetén az ügyfélalkalmazás már nem tudja feldolgozni az üzenetet. Az ügyfélalkalmazás dönthet úgy is, hogy megtekinti a kivételt az elemzéshez, de az ügyfélnek *el kell* dobja az üzenetet.

Mivel az üzenet zárolása lejárt, vissza fog térni a várólistára (vagy előfizetésre), és a következő ügyfélalkalmazás feldolgozható, amely meghívja a fogadást.

Ha túllépte a **MaxDeliveryCount** , a rendszer áthelyezi az üzenetet a **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Ok

A **SessionLockLostException** akkor kerül kiosztásra, ha elfogadják a munkamenetet, és az ügyfél által megőrzött zárolás lejár a szolgáltatás oldalán.

A munkamenet zárolása számos ok miatt lejárhat – 

  * A zárolási időzítő lejárt az ügyfélalkalmazás megújítása előtt.
  * Az ügyfélalkalmazás megszerezte a zárolást, mentette azt egy állandó tárolóba, majd újraindította. Az újraindítást követően az ügyfélalkalmazás megtekintette a fedélzeti munkameneteket, és megpróbálta feldolgozni az üzeneteket a munkamenetekben.

### <a name="resolution"></a>Feloldás

**SessionLockLostException** esetén az ügyfélalkalmazás már nem tudja feldolgozni az üzeneteket a munkamenetben. Az ügyfélalkalmazás fontolóra veheti az elemzések kivételének naplózását, de az ügyfélnek *el kell* dobja az üzenetet.

Mivel a munkamenet zárolása lejárt, visszatérhet a várólistára (vagy előfizetésre), és a következő ügyfélalkalmazás zárolhatja, amely elfogadja a munkamenetet. Mivel a munkamenet-zárolást egyetlen ügyfélalkalmazás egy adott időpontban tartja, a Megrendeléses feldolgozás garantált.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Ok

A **SocketException** az alábbi esetekben dobják el:
   * Ha a kapcsolódási kísérlet sikertelen, mert a gazdagép megadott idő után nem válaszolt megfelelően (10060-es TCP-hibakód).
   * Egy létesített kapcsolat nem sikerült, mert a csatlakoztatott gazdagép nem válaszolt.
   * Hiba történt az üzenet feldolgozásakor, vagy túllépte a távoli gazdagép időtúllépését.
   * A mögöttes hálózati erőforrás problémája.

### <a name="resolution"></a>Feloldás

A **SocketException** hibák azt jelzik, hogy az alkalmazásokat üzemeltető virtuális gép nem tudja átalakítani a nevet `<mynamespace>.servicebus.windows.net` a megfelelő IP-címmé. 

Ellenőrizze, hogy az alábbi parancs sikeres volt-e az IP-címhez való leképezéshez.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

amelynek kimenetét az alábbiak szerint kell megadni

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Ha a fenti név **nem oldható** fel egy IP-címre és a névtér aliasára, ellenőrizze, hogy a hálózati rendszergazda további vizsgálatot végez-e. A névfeloldás a DNS-kiszolgálón, jellemzően az ügyfél-hálózatban lévő erőforráson keresztül történik. Ha a DNS-feloldást Azure DNS hajtja végre, forduljon az Azure ügyfélszolgálatához.

Ha a névfeloldás **a várt módon működik**, ellenőrizze, hogy a Azure Service Bus kapcsolatok [engedélyezve vannak](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues) -e


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>Ok

A **MessagingException** egy általános kivétel, amely különböző okok miatt fordulhat elő. Az alábbi okok némelyike alább látható.

   * Kísérlet történt egy **QueueClient** létrehozására egy **témakörben** vagy **előfizetésben**.
   * Az elküldött üzenet mérete meghaladja az adott szintű korlátot. További információ a Service Bus [kvótákkal és korlátozásokkal](service-bus-quotas.md)kapcsolatban.
   * A szabályozás miatt megszakadt az adott adatsík-kérelem (küldés, fogadás, Befejezés, megszakítás).
   * A szolgáltatás frissítései és újraindítása miatt okozott átmeneti problémák.

> [!NOTE]
> A kivételek fenti listája nem teljes.

### <a name="resolution"></a>Feloldás

A megoldási lépések attól függnek, hogy mi okozta a **MessagingException** .

   * **Átmeneti problémák** esetén (ahol a **_isTransient_*_ értéke _*_true_*_), vagy ha _* szabályozást okozó problémák merülnek** fel, a művelet újrapróbálkozik. Ehhez az SDK alapértelmezett újrapróbálkozási szabályzata használható.
   * Más problémák esetén a kivételben szereplő adatok azt jelzik, hogy a probléma és a megoldás lépései azonosak lehetnek.

## <a name="next-steps"></a>Következő lépések
A teljes Service Bus .NET API-referenciával kapcsolatban tekintse meg az [Azure .NET API-referenciát](/dotnet/api/overview/azure/service-bus).
Hibaelhárítási tippekért tekintse meg a [hibaelhárítási útmutatót](service-bus-troubleshooting-guide.md) .
