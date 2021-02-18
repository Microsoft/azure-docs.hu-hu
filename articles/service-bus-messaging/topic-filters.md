---
title: Azure Service Bus témakör szűrők | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan határozhatja meg, hogy az előfizetők hogyan határozhatják meg, hogy mely üzeneteket szeretnék a témakörből a szűrők megadásával.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: f28b26ee112b47b9782823f6c79670dee9a3f082
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651663"
---
# <a name="topic-filters-and-actions"></a>Témakörszűrők és -műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezen üzenetek egy vagy több névvel ellátott előfizetési szabály formájában adhatók meg. Minden szabály egy olyan **szűrési feltétellel** áll, amely adott üzeneteket jelöl ki, és **opcionálisan** tartalmaz egy **műveletet** , amely a kijelölt üzenetet tartalmazza. 

A **műveletek nélküli** összes szabály egy `OR` feltétellel kombinálható, és az előfizetéshez tartozó **egyetlen üzenetet** eredményez, még akkor is, ha több egyezési szabály is van. 

Minden **olyan szabály, amely egy művelettel** rendelkezik, másolatot készít az üzenetről. Ehhez az üzenethez meg kell nevezni egy tulajdonságot, `RuleName` ahol az érték az egyező szabály neve. A művelet hozzáadhat vagy frissíthet tulajdonságokat, vagy törölheti az eredeti üzenet tulajdonságait, hogy üzenetet hozzon létre az előfizetésben. 

Vegyük példaként a következő esetet:

- Az előfizetés öt szabállyal rendelkezik.
- Két szabály tartalmaz műveleteket.
- Három szabály nem tartalmaz műveleteket.

Ebben a példában, ha egy, az összes öt szabálynak megfelelő üzenetet küld, három üzenetet kap az előfizetésben. Ez két üzenet két, műveletekkel és egy üzenettel rendelkező szabályhoz a műveletek nélkül három szabályhoz. 

Minden újonnan létrehozott témakör-előfizetés kezdeti alapértelmezett előfizetési szabállyal rendelkezik. Ha nem ad meg explicit módon egy szűrési feltételt a szabályhoz, az alkalmazott szűrő a **valódi** szűrő, amely lehetővé teszi az összes üzenet kijelölését az előfizetésben. Az alapértelmezett szabályhoz nem tartozik társított megjegyzési művelet.

## <a name="filters"></a>Szűrők
A Service Bus három szűrési feltételt támogat:

-   *SQL-szűrők* – a **SqlFilter** egy SQL-szerű feltételes kifejezést tart fenn, amelyet a rendszer az elküldött üzenetek felhasználó által definiált tulajdonságai és a Rendszertulajdonságok alapján értékel ki a közvetítőn. Az összes rendszertulajdonságot előtaggal kell ellátni `sys.` a feltételes kifejezésben. A [szűrési feltételekhez tartozó SQL-nyelv részhalmaza](service-bus-messaging-sql-filter.md) a tulajdonságok ( `EXISTS` ), a Null érték ( `IS NULL` ), a logikai nem/és/vagy, a rokon operátorok, az egyszerű numerikus aritmetika és az egyszerű szöveges mintázatok meglétét vizsgálja `LIKE` .
-   *Logikai szűrők* – a **TrueFilter** és a **FalseFilter** az összes érkező üzenetet (**igaz**) vagy egyetlen nem az elküldött üzenetet (**false**) sem okozhatja az előfizetés kiválasztásához. Ez a két szűrő az SQL-szűrőből származik. 
-   *Korrelációs szűrők* – a **CorrelationFilter** olyan feltételekkel rendelkezik, amelyek egy vagy több érkező üzenet felhasználó-és rendszertulajdonságával egyeznek. Gyakori használat a **correlationId** tulajdonsággal való egyezés, de az alkalmazás az alábbi tulajdonságokkal is rendelkezhet:

    - **ContentType**
     - **Címke**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Ide:**
     - bármely felhasználó által definiált tulajdonság. 
     
     Ha egy tulajdonsághoz tartozó üzenet értéke megegyezik a korrelációs szűrőben megadott értékkel, akkor egyezés áll fenn. Karakterlánc-kifejezések esetében az összehasonlítás a kis-és nagybetűk megkülönböztetése. Több egyezési tulajdonság megadásakor a szűrő logikai és feltételként kombinálja őket, ami azt jelenti, hogy a szűrőnek egyeznie kell, minden feltételnek egyeznie kell.

Az összes szűrő kiértékeli az üzenet tulajdonságait. A szűrők nem tudják kiértékelni az üzenet törzsét.

Az összetett szűrési szabályok feldolgozási kapacitást igényelnek. Az SQL-szűrési szabályok használata általában az előfizetés, a témakör és a névtér szintjének alacsonyabb általános üzenet-átviteli sebességét eredményezi. Ha lehetséges, az alkalmazásoknak olyan korrelációs szűrőket kell választaniuk, mint az SQL-szerű szűrők, mert sokkal hatékonyabbak a feldolgozásban, és kevesebb hatással vannak az átviteli sebességre.

## <a name="actions"></a>Műveletek

SQL-szűrési feltételekkel olyan műveletet határozhat meg, amely a tulajdonságok és azok értékeinek hozzáadásával, eltávolításával vagy cseréjével képes jegyzeteket adni az üzenethez. A művelet [egy olyan SQL-szerű kifejezést használ](service-bus-messaging-sql-filter.md) , amely lazán támaszkodik az SQL Update utasítás szintaxisára. A művelet az üzenet egyeztetése után, az üzenetnek az előfizetésbe való kijelölése előtt történik. Az üzenet tulajdonságainak módosításai magánjellegűek az előfizetésbe másolt üzenetben.

## <a name="usage-patterns"></a>Használati minták

Egy témakör legegyszerűbb használati forgatókönyve, hogy minden előfizetés lekéri a témakörbe küldött összes üzenet másolatát, amely lehetővé teszi a szórási mintát.

A szűrők és a műveletek lehetővé teszik a minták két további csoportjának használatát: particionálás és útválasztás.

A particionálás szűrők használatával továbbítja az üzeneteket számos meglévő témakör-előfizetésben kiszámítható és kölcsönösen kizárható módon. A particionálási mintát akkor kell használni, ha egy rendszer méretezése úgy történik, hogy számos különböző kontextust kezel a funkcionálisan azonos rekeszekben, amelyek mindegyike a teljes adat egy részhalmazát fogja tárolni. például az ügyfél profiljának adatai. A particionálással a közzétevő a particionálási modell ismerete nélkül küldi el az üzenetet egy témakörbe. Ekkor a rendszer áthelyezi az üzenetet a megfelelő előfizetésre, ahonnan a partíció üzenetkezelője lekérheti azt.

Az Útválasztás szűrőket használ az üzenetek különböző témakör-előfizetések közötti elosztására kiszámítható módon, de nem feltétlenül kizárólagos. Az [automatikus továbbítási](service-bus-auto-forwarding.md) szolgáltatással együtt a témakör szűrők segítségével összetett útválasztási diagramokat hozhat létre egy Service Bus névtérben az Azure-régión belüli üzenetek terjesztéséhez. Ha Azure Functions vagy Azure Logic Apps Azure Service Bus névterek közötti híddal, összetett globális topológiákat hozhat létre, amelyek közvetlen integrációt végeznek az üzletági alkalmazásokkal.

## <a name="examples"></a>Példák
Példák: [Service Bus szűrési példák](service-bus-filter-examples.md).



> [!NOTE]
> Mivel a Azure Portal mostantól támogatja a Service Bus Explorer funkcióit, a portálon létrehozhatók és szerkeszthetők az előfizetési szűrők. 

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő mintákat: 

- [.NET – alapszintű küldési és fogadási útmutató szűrőkkel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – témakör szűrők](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-sablon](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)