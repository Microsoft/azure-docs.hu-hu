---
title: AMQP 1,0 kérelem/válasz művelet Azure Service Bus
description: Ez a cikk a Microsoft Azure Service Bus AMQP kérelmek/válasz alapú műveleteinek listáját határozza meg.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064414"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 Microsoft Azure Service Bus: kérelem-válasz alapú műveletek

Ez a cikk Microsoft Azure Service Bus kérelmek/válaszok alapú műveletek listáját határozza meg. Ez az információ a AMQP Management 1,0-es verziójának munkatervén alapul.  
  
Az 1,0-as részletes AMQP, amely elmagyarázza, hogy Service Bus hogyan valósítja meg és építheti fel az OASIS AMQP technikai specifikációját, tekintse meg a [AMQP 1,0 a Azure Service Bus és a Event Hubs]a[AMQP 1,0 protokoll]útmutatójában.  
  
## <a name="concepts"></a>Alapelvek  
  
### <a name="entity-description"></a>Entitás leírása  

Az entitások leírása Service Bus [QueueDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.topicdescription)vagy [SubscriptionDescription osztályra](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) hivatkozik.  
  
### <a name="brokered-message"></a>Közvetített üzenet  

Egy üzenetet jelöl Service Busban, amely egy AMQP-üzenetre van leképezve. A leképezés a [Service Bus AMQP protokoll útmutatójában](service-bus-amqp-protocol-guide.md)van meghatározva.  
  
## <a name="attach-to-entity-management-node"></a>Csatolás entitás-felügyeleti csomóponthoz  

A jelen dokumentumban ismertetett összes művelet egy kérelem/válasz mintát követ, hatóköre egy entitás, és az entitás-felügyeleti csomóponthoz való csatolás szükséges.  
  
### <a name="create-link-for-sending-requests"></a>Hivatkozás létrehozása a kérelmek küldéséhez  

Létrehoz egy hivatkozást a felügyeleti csomópontra a kérelmek küldéséhez.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Hivatkozás létrehozása a válaszok fogadásához  

Hivatkozást hoz létre a felügyeleti csomóponttól érkező válaszok fogadásához.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Kérelem üzenetének átvitele  

Átviszi a kérelem üzenetét.  
Tranzakció-állapot adható hozzá a tranzakciót támogató műveletek esetén.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Válaszüzenet fogadása  

A válasz hivatkozását kapja meg.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A válaszüzenet a következő formában jelenik meg:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus entitás címe  

Service Bus entitásokat a következőképpen kell megoldani:  
  
|Entitástípus|Cím|Példa|  
|-----------------|-------------|-------------|  
|üzenetsor|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|témakör|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|előfizetést|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Üzenetkezelési műveletek  
  
### <a name="message-renew-lock"></a>Üzenet zárolásának megújítása  

Kiterjeszti egy üzenet zárolását az entitás leírásában megadott idő szerint.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
 A kérelem üzenet törzsének tartalmaznia kell egy amqp szakaszt, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|UUID tömb|Yes|A megújítani kívánt üzenet-zárolási tokenek.|  

> [!NOTE]
> A zárolási tokenek a `DeliveryTag` fogadott üzenetek tulajdonsága. Tekintse meg a következő példát a [.net SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) -ban, amely lekéri ezeket. A jogkivonat a "DeliveryAnnotations" x-opt-Lock-token néven is szerepelhet, ez azonban nem garantált, és a `DeliveryTag` előnyben részesítettnek kell lennie. 
> 
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen volt.|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan amqp szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejáratok|időbélyeg tömbje|Yes|Az üzenet zárolási jogkivonatának új lejárata a kérelem zárolási jogkivonatának megfelelő.|  
  
### <a name="peek-message"></a>Betekintés üzenet  

Üzenetek zárolás nélküli bepillantása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|hosszú|Yes|A betekintési kezdési sorszám.|  
|`message-count`|int|Yes|A bepillantható üzenetek maximális száma.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: nincs tartalom – nincs több üzenet|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Yes|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|bájtok tömbje|Yes|AMQP 1,0 dróttal kódolt üzenet.|  
  
### <a name="schedule-message"></a>Ütemezett üzenet  

Üzeneteket ütemezhet. Ez a művelet támogatja a tranzakciót.
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Yes|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet-azonosító|sztring|Yes|`amqpMessage.Properties.MessageId` karakterláncként|  
|munkamenet-azonosító|sztring|No|`amqpMessage.Properties.GroupId as string`|  
|partíció – kulcs|sztring|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|partíción keresztüli kulcs|sztring|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|bájtok tömbje|Yes|AMQP 1,0 dróttal kódolt üzenet.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen volt.|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|hosszú tömb|Yes|Az ütemezett üzenetek sorszáma. A sorozatszámot a rendszer a megszakításra használja.|  
  
### <a name="cancel-scheduled-message"></a>Ütemezett üzenet megszakítása  

Az ütemezett üzenetek megszakítása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|hosszú tömb|Yes|A megszakítani kívánt ütemezett üzenetek sorozatszámai.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen volt.|  
|statusDescription|sztring|No|Az állapot leírása.|   
  
## <a name="session-operations"></a>Munkamenet-műveletek  
  
### <a name="session-renew-lock"></a>Munkamenet-megújítás zárolása  

Kiterjeszti egy üzenet zárolását az entitás leírásában megadott idő szerint.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Yes|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: nincs tartalom – nincs több üzenet|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|lejárati|időbélyeg|Yes|Új lejárat.|  
  
### <a name="peek-session-message"></a>Betekintési munkamenet üzenete  

A munkamenet üzeneteinek zárolás nélküli bepillantása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszám alapján|hosszú|Yes|A betekintési kezdési sorszám.|  
|üzenetek száma|int|Yes|A bepillantható üzenetek maximális száma.|  
|munkamenet-azonosító|sztring|Yes|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: nincs tartalom – nincs több üzenet|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy térképet tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Yes|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
 Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|message|bájtok tömbje|Yes|AMQP 1,0 dróttal kódolt üzenet.|  
  
### <a name="set-session-state"></a>Munkamenet állapotának beállítása  

Egy munkamenet állapotának beállítása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Yes|Munkamenet-azonosító.|  
|munkamenet-állapot|bájtok tömbje|Yes|Átlátszatlan bináris adatértékek.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
### <a name="get-session-state"></a>Munkamenet-állapot beolvasása  

Egy munkamenet állapotának beolvasása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-azonosító|sztring|Yes|Munkamenet-azonosító.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|munkamenet-állapot|bájtok tömbje|Yes|Átlátszatlan bináris adatértékek.|  
  
### <a name="enumerate-sessions"></a>Munkamenetek számbavétele  

Üzenetküldési entitások munkameneteinek enumerálása.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|utolsó frissítés időpontja|időbélyeg|Yes|A szűréssel csak egy adott időpont után frissülő munkamenetek jelennek meg.|  
|kihagyása|int|Yes|Több munkamenet kihagyása.|  
|top|int|Yes|A munkamenetek maximális száma.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – több üzenettel rendelkezik<br /><br /> 204: nincs tartalom – nincs több üzenet|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kihagyása|int|Yes|A kihagyott munkamenetek száma, ha az állapotkód 200.|  
|munkamenetek – azonosítók|sztringek tömbje|Yes|Munkamenet-azonosítók tömbje, ha az állapotkód 200.|  
  
## <a name="rule-operations"></a>Szabály műveletei  
  
### <a name="add-rule"></a>Szabály hozzáadása  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály neve|sztring|Yes|Szabály neve, nem tartalmazza az előfizetés és a témakör nevét.|  
|szabály – Leírás|map|Yes|A szabály leírása a következő szakaszban megadott módon.|  
  
A **szabály-leírási** térképnek tartalmaznia kell a következő bejegyzéseket, ahol az **SQL-Filter** és a **korrelációs szűrő** kölcsönösen kizárható:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|SQL-szűrő|map|Yes|`sql-filter`, a következő szakaszban leírtak szerint.|  
|korrelációs szűrő|map|Yes|`correlation-filter`, a következő szakaszban leírtak szerint.|  
|SQL-szabály – művelet|map|Yes|`sql-rule-action`, a következő szakaszban leírtak szerint.|  
  
Az SQL-Filter térképnek tartalmaznia kell a következő bejegyzéseket:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Yes|SQL-szűrő kifejezése|  
  
A **korreláció-szűrő** térképnek tartalmaznia kell legalább az alábbi bejegyzések egyikét:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|korrelációs azonosító|sztring|No||  
|üzenet-azonosító|sztring|No||  
|felhasználóként a(z)|sztring|No||  
|Válasz címzettje|sztring|No||  
|címke|sztring|No||  
|munkamenet-azonosító|sztring|No||  
|Válasz – munkamenet-azonosító|sztring|No||  
|Content-Type|sztring|No||  
|properties|map|No|Leképezi Service Bus [BrokeredMessage. properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Az **SQL-Rule-Action** térképnek tartalmaznia kell a következő bejegyzéseket:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|kifejezés|sztring|Yes|SQL-művelet kifejezése|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
### <a name="remove-rule"></a>Szabály eltávolítása  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály neve|sztring|Yes|Szabály neve, nem tartalmazza az előfizetés és a témakör nevét.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
### <a name="get-rules"></a>Szabályok beolvasása

#### <a name="request"></a>Kérés

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:

|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  

A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|top|int|Yes|Az oldalon beolvasni kívánt szabályok száma.|  
|kihagyása|int|Yes|A kihagyni kívánt szabályok száma. Meghatározza a szabályok listájának kezdő indexét (+ 1). | 

#### <a name="response"></a>Reagálás

A válaszüzenet a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|szabályok| Térkép tömbje|Yes|A szabályok tömbje. Minden szabályt egy Térkép képvisel.|

A tömb minden leképezési bejegyzése a következő tulajdonságokat tartalmazza:

|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|szabály – Leírás|leírt objektumok tömbje|Yes|`com.microsoft:rule-description:list` a AMQP-ben leírt kód 0x0000013700000004| 

`com.microsoft.rule-description:list` a a leírt objektumok tömbje. A tömb a következőket tartalmazza:

|Index|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | leírt objektumok tömbje | Yes | `filter` az alább megadott módon. |
| 1 | a leírt objektum tömbje | Yes | `ruleAction` az alább megadott módon. |
| 2 | sztring | Yes | a szabály neve. |

`filter` a következő típusok egyike lehet:

| Leíró neve | Leíró kódja | Érték |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-szűrő |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelációs szűrő |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Igaz szűrő, amely 1 = 1 értéket jelöl |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Hamis szűrő, amely az 1 = 0 értéket jelöli |

`com.microsoft:sql-filter:list` a egy leírt tömb, amely a következőket tartalmazza:

|Index|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
| 0 | sztring | Yes | SQL-szűrő kifejezése |

`com.microsoft:correlation-filter:list` a egy leírt tömb, amely a következőket tartalmazza:

|Index (ha létezik)|Érték típusa|Érték tartalma|  
|---------|----------------|--------------|
| 0 | sztring | Korrelációs azonosító |
| 1 | sztring | Üzenet azonosítója |
| 2 | sztring | Művelet |
| 3 | sztring | Válasz címzettje |
| 4 | sztring | Címke |
| 5 | sztring | Munkamenet-azonosító |
| 6 | sztring | Válasz a munkamenet-AZONOSÍTÓra|
| 7 | sztring | Tartalom típusa |
| 8 | Térkép | Alkalmazás által definiált tulajdonságok leképezése |

`ruleAction` a következő típusok egyike lehet:

| Leíró neve | Leíró kódja | Érték |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Üres szabály művelet – nincs szabály-művelet |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL-szabály művelete |

`com.microsoft:sql-rule-action:list` a olyan leírt objektumok tömbje, amelyek első bejegyzése egy olyan karakterlánc, amely tartalmazza az SQL-szabály műveletének kifejezését.

## <a name="deferred-message-operations"></a>Késleltetett üzenetküldési műveletek  
  
### <a name="receive-by-sequence-number"></a>Beérkezés sorszám szerint  

A késleltetett üzenetet fogadja sorszám alapján.  
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|sorozatszámok|hosszú tömb|Yes|Sorozatszámok.|  
|fogadó – rendezés – üzemmód|ubyte|Yes|A **fogadó rendezési** módja a AMQP Core 1.0-s verzióban megadott módon.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|sztring|No|Az állapot leírása.|  
  
A válaszüzenet törzsének olyan **amqp** szakaszból kell állnia, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|üzenet|térképek listája|Yes|Azon üzenetek listája, amelyekben minden Térkép egy üzenetet jelöl.|  
  
Az üzenetet jelölő térképnek a következő bejegyzéseket kell tartalmaznia:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|zárolási token|uuid|Yes|A zárolási token if `receiver-settle-mode` értéke 1.|  
|message|bájtok tömbje|Yes|AMQP 1,0 dróttal kódolt üzenet.|  
  
### <a name="update-disposition-status"></a>Törlési állapot frissítése  

Frissíti a késleltetett üzenetek törlési állapotát. Ez a művelet támogatja a tranzakciókat.
  
#### <a name="request"></a>Kérés  

A kérelem üzenetének tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|művelet|sztring|Yes|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No|A műveleti kiszolgáló időtúllépése ezredmásodpercben.|  
  
A kérelem üzenet törzsének tartalmaznia kell egy **amqp** szakaszt, amely egy **térképet** tartalmaz a következő bejegyzésekkel:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|törlési állapot|sztring|Yes|befejeződött<br /><br /> elhagyott<br /><br /> függesztve|  
|zárolási tokenek|UUID tömb|Yes|Üzenet zárolási jogkivonatai a törlési állapot frissítéséhez.|  
|kézbesítetlen levelek – ok|sztring|No|Akkor állítható be, ha a törlési állapot **felfüggesztve** értékre van állítva.|  
|kézbesítetlen levelek – Leírás|sztring|No|Akkor állítható be, ha a törlési állapot **felfüggesztve** értékre van állítva.|  
|tulajdonságok – módosítás|map|No|A módosítani kívánt Service Bus felügyelt üzenet tulajdonságainak listája.|  
  
#### <a name="response"></a>Reagálás  

A válaszüzenetnek tartalmaznia kell a következő alkalmazás-tulajdonságokat:  
  
|Kulcs|Érték típusa|Kötelező|Érték tartalma|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|HTTP-válasz kódja [RFC2616]<br /><br /> 200: OK – sikeres, máskülönben sikertelen|  
|statusDescription|sztring|No|Az állapot leírása.|

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a AMQP és a Service Busről, látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]
* [A Windows Server Service Bus AMQP]

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md
[A Windows Server Service Bus AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)