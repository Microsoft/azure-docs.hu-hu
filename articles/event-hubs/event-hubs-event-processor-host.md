---
title: Események fogadása Event Processor Host – Azure Event Hubs használatával |} A Microsoft Docs
description: Ez a cikk bemutatja az Event Processor Host, az Azure Event Hubs, amely leegyszerűsíti a felügyeleti az ellenőrzőpontok használata, bérlés, valamint az események adatmegőrzési párhuzamos olvasását.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 07/16/2019
ms.author: shvija
ms.openlocfilehash: 013200295f3a6a48d6d96663f98bce506808cd70
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277376"
---
# <a name="event-processor-host"></a>Event Processor Host

Az Azure Event Hubs szolgáltatás a egy hatékony telemetriai adatokat betöltő szolgáltatás, amely több millió esemény áramoltatása biztosítva alacsony költségek mellett használhatók. Ez a cikk bemutatja, hogyan használja a feldolgozott események felhasználásához a *Event Processor Host* (EPH); egy intelligens Felhasználóügynök, amely leegyszerűsíti az ellenőrzőpontok használata, a bérlés, valamint a párhuzamos eseményolvasókat kezelését.  

Az Event Hubs méretezhető a lényeg az elgondolásra épül, particionált fogyasztókat. Ellentétben a [versengő fogyasztó számára](https://msdn.microsoft.com/library/dn568101.aspx) mintát, a particionált felhasználói mintán lehetővé teszi a nagy méretű eltávolításával a versengés szűk keresztmetszetet, és teljes körű párhuzamosság megkönnyítése.

## <a name="home-security-scenario"></a>Otthoni biztonsági forgatókönyv

Fontolja meg egy otthoni biztonsági vállalat, amely figyeli a 100 000 házak egy példán keresztül. Percenként, például a motion detector használatával, ajtó vagy-ablakban megnyitott érzékelő, vészhelyzeti break detector használatával, stb., mindegyik a saját telepített különböző érzékelőadatok nyúlna adatok. A vállalat lakosoknak tevékenységének figyelését, közel valós időben otthon webhelyet biztosít.

Minden érzékelő adatokat leküldi az eseményközpontba. Az event hubs 16 partícióval rendelkező van konfigurálva. A fogyasztó végén kell egy mechanizmust, olvassa el ezeket az eseményeket, egyesítheti őket (szűrés, összesítése, stb.) és a storage-blobba, amely egy felhasználóbarát weblapra majd előre összesített dump.

## <a name="write-the-consumer-application"></a>A fogyasztó alkalmazás írása

A fogyasztó elosztott környezetben tervezésekor a forgatókönyv az alábbi követelményeknek kell kezelnie:

1. **Méretezési** Több fogyasztót is létrehozhat, és minden fogyasztónak több Event Hubs partícióból kell elolvasnia a tulajdonjogát.
2. **Terheléselosztás:** Növelje vagy csökkentse dinamikusan a fogyasztókat. Például új érzékelő típus (például egy szénmonoxid detector használatával) minden egyes kezdőlapjára kerül, ha az események száma növekszik. Ebben az esetben az operátor (emberi) növeli a fogyasztói példányok száma. Ezt követően a fogyasztókészletek is újraegyensúlyozására saját, partíciók száma a terhelés megosztása a újonnan felvett felhasználók.
3. **Zökkenőmentes folytatás a hibáknál:** Ha a fogyasztó (**a fogyasztó**) nem sikerül (például a fogyasztót futtató virtuális gép hirtelen összeomlik), akkor a többi fogyasztónak képesnek kell lennie az **a fogyasztó** által birtokolt és folytatott partíciók felvételére. Emellett a folytatási pont, nevű egy *ellenőrzőpont* vagy *eltolás*, kell lennie, ahol a pontos ponton **A fogyasztói** sikertelen, vagy kis mértékben, amely előtt.
4. **Események felhasználása:** Míg az előző három pont a fogyasztó felügyeletével foglalkozik, az események felhasználásához programkódot kell használni, és ehhez hasznosnak kell lennie. például összesítheti és feltöltheti a blob Storage-ba.

Helyett létrehozhat saját megoldásokat a, az Event Hubs – ezt a funkciót biztosít a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felület és a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) osztály.

## <a name="ieventprocessor-interface"></a>Ievent Processor felület implementálása

Első lépésként az alkalmazások az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületet implementálják, amely négy módszerből áll: [OpenAsync, CloseAsync, ProcessErrorAsync és ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ez az interfész felhasználni az eseményeket küld az Event Hubs, kódot tartalmaz. A következő kód bemutatja egy egyszerű végrehajtását:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Következő lépésként hozza létre az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány. Attól függően, a túlterhelés, amikor létrehozza a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány a konstruktorban, a következő paraméterek használhatók:

- **Állomásnév:** minden egyes felhasználói példány nevét. A **EventProcessorHost** minden példányának egyedi értékkel kell rendelkeznie ehhez a változóhoz egy fogyasztói csoporton belül, ezért ne jegyezze fel ezt az értéket.
- **eventHubPath:** Az Event hub neve.
- **consumerGroupName:** A Event Hubs a **$default** használja az alapértelmezett fogyasztói csoport neveként, de célszerű létrehozni egy fogyasztói csoportot a feldolgozás konkrét aspektusához.
- **eventHubConnectionString:** Az Event hub kapcsolati karakterlánca, amely a Azure Portalból kérhető le. Ezt a kapcsolati karakterláncot kell **figyelésére** engedélyeket az eseményközpontban.
- **storageConnectionString:** A belső erőforrás-kezeléshez használt Storage-fiók.

Végül a feldolgozók regisztrálása a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány az Event Hubs szolgáltatással. Események feldolgozása processzor eseményosztály Regisztrálás az EventProcessorHost példányát elindul. Regisztrálás arra utasítja az Event Hubs szolgáltatás várható, hogy a fogyasztó alkalmazás használ-e a partíciókat némelyike eseményeit, és a meghívni a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációs kódot, amikor leküldi az események felhasználásához. 


### <a name="example"></a>Példa

Például tegyük fel, hogy nincsenek-e 5 virtuális gépeken (VM) események és a egy egyszerű konzolalkalmazást az egyes virtuális gépek számára kijelölt, amely a tényleges használat működik. Ezután minden egyes konzolalkalmazást hoz létre egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példány, és regisztrálja az Event Hubs szolgáltatás.

Ebben a példában a forgatókönyvben tegyük fel, hogy az 5 16 partíciók kiosztott **EventProcessorHost** példányok. Néhány **EventProcessorHost** példányok előfordulhat, hogy saját néhány további partíciók, mint mások. Minden partíció, amely egy **EventProcessorHost** példány tulajdonosa, létrehoz egy példányát a `SimpleEventProcessor` osztály. Ezért olyan 16 példányai `SimpleEventProcessor` egy minden egyes partícióhoz rendelt teljes.

A következő lista foglalja össze az ebben a példában:

- Az event Hubs-partíciók 16.
- 5 virtuális gép, 1 fogyasztói alkalmazások (például Consumer.exe) minden virtuális gépre.
- 5 EPH-példánnyal regisztrált, az egyes virtuális gépek által Consumer.exe 1.
- 16 `SimpleEventProcessor` az 5 EPH példányok által létrehozott objektumok.
- 1 Consumer.exe alkalmazás tartalmazhat 4 `SimpleEventProcessor` objektumok, mivel a 1 EPH példány rendelkezhet saját 4 partíciók.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjog nyomon követése

Egy partíció EPH példányát (vagy egy fogyasztó) tulajdonjogát nyomon követés megadott Azure Storage-fiók használatával van. A követési egyszerű táblázatként módon jelenítheti meg. A blobok mellett a megadott tárfiók megvizsgálásával tekintheti meg a tényleges implementációt:

| **Fogyasztói csoport neve** | **Partícióazonosító** | **Állomásnév (tulajdonos)** | **Bérlet (vagy a tulajdonjoga) megszerzett idő** | **A partíció (ellenőrzőpont) eltolása** |
| --- | --- | --- | --- | --- |
| $Alapértelmezett | 0 | Fogyasztói\_vm3 virtuális gép | 2018-04-15T01:23:45 | 156 |
| $Alapértelmezett | 1 | Fogyasztói\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Alapértelmezett | 2 | Fogyasztói\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Alapértelmezett | 15 | Fogyasztói\_vm3 virtuális gép | 2018-04-15T01:22:56 | 976 |

Itt minden állomás egy bizonyos időtartamon (a címbérlet élettartama) szerez be partíció tulajdonjogát. Ha egy gazdagép meghibásodik (virtuális Gépet leállítja), majd a bérleti jog vége. Más gazdagépek próbálják meg beolvasni a partíció tulajdonjogát, és a gazdagépet sikeres lesz. Ez a folyamat visszaállítja a bérlet az új tulajdonost a partíción. Ezzel a módszerrel egyszerre csak egyetlen olvasó egy fogyasztói csoporton belül bármely adott partícióról olvashatja.

## <a name="receive-messages"></a>Üzenetek fogadása

Egyes hívások [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) kézbesíti az eseményeket gyűjteménye. A feladata ezeket az eseményeket kezelésére. Ha azt szeretné, hogy a feldolgozó gazdagépe legalább egyszer dolgozza fel az összes üzenetet, meg kell írnia a saját Keep rewrite kódját. Legyen óvatos a mérgezett üzenetekkel kapcsolatban.

Javasoljuk, hogy végrehajtja-e a dolgok viszonylag gyorsan; azt jelenti tegye a csekély a feldolgozás, amennyire csak lehetséges. Ehelyett használja a fogyasztói csoportok. Ha a tárhelyre kell írnia, és el kell végeznie néhány útválasztást, érdemes két fogyasztói csoportot használnia, és két [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementációja van, amelyek külön futnak.

A feldolgozás során egy ponton érdemes nyomon követheti, mi elolvasta és befejeződött. Így nyomon követheti, kritikus fontosságú, ha újra kell indítania az olvasó, így nem tér a stream elején. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) egyszerűbbé teszi a nyomon követési használatával *ellenőrzőpontok*. Egy ellenőrzőpont egy helyet, vagy az eltolás egy adott partíció egy adott felhasználói csoportban, mely pont, a rendszer meggyőződik arról, hogy az üzenetek feldolgozta. Az ellenőrzőpont jelölés **EventProcessorHost** meghívásával történik a [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódust a [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektum. Ez a művelet belül történik a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metódus is megtehető, de [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Ellenőrzőpontok használata

A [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódus két túlterheléssel rendelkezik: az első, paraméter nélküli, a legmagasabb esemény eltolása a gyűjtemény által visszaadott ellenőrzőpontok [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Az eltolás egy "jó" vízjel; azt feltételezi, feldolgozta az összes legutóbbi esemény fogja meghívni. Ha ezzel a módszerrel ezt a módszert használja, vegye figyelembe, hogy várhatóan meghívására, miután a eseményfeldolgozás kódot adott vissza. A második túlterhelés teszi lehetővé egy [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) ellenőrzőpont-példány. Ez a módszer lehetővé teszi, hogy más típusú vízjel ellenőrzőpont. A vízjel is alkalmazhat egy "alacsony" vízjel: inkonzisztensek előkészített legalacsonyabb esemény feldolgozása megtörtént. Ez a túlterhelés megadott eltolás kezelése a rugalmasság.

Az ellenőrző pont hajtja végre, amikor egy JSON-fájlt a partíció-specifikus adatait (pontosabban az eltolásnak), a tárfiók konstruktor megadott írt [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Ez a fájl folyamatosan frissül. Rendkívül fontos figyelembe venni az ellenőrzőpontok használata a környezetben – unwise ellenőrzőpontra lenne az összes üzenetet. Az ellenőrzőpontok használata valószínűleg használt tárfiók ugyanúgy kezeli a terhelés, de még fontosabb ellenőrzőpontok használata minden eseményhez, amelynek egy Service Bus-üzenetsorba, mint egy eseményközpontba jobb megoldás lehet egy aszinkron üzenetkezelési minta jelezhetnek. Az Event Hubs működési, hogy az "legalább egyszeri" szállítási nagy méretekben. Azáltal, hogy az alsóbb rétegbeli rendszerek idempotens, azt könnyen helyreállni hibák után vagy újraindítja, amelyek ugyanazokat az eseményeket több alkalommal fogadja.

## <a name="thread-safety-and-processor-instances"></a>Biztonság és a processzor-példányok hozzászóláslánc

Alapértelmezés szerint [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) szál biztonságos-e, és szinkron módon garanciát az példány működését [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). A partíció események érkezésekor [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) a neve a **IEventProcessor** példányt, amely particionálja, és letiltja a további hívásainak **ProcessEventsAsync**a partíció. Ezt követő üzenetek és a hívások **ProcessEventsAsync** várólistára helyezését a háttérben, az üzenet szivattyú továbbra is fut a háttérben más szálak. A hozzászóláslánc biztonsági kiküszöböli az szálbiztos gyűjtemények, és jelentősen növeli a teljesítményt.

## <a name="shut-down-gracefully"></a>Biztonságos leállítása

Végül [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) lehetővé teszi, hogy az összes partíció olvasók kerüljön tiszta Leállítás utáni, és mindig kell meghívni, ha leáll egy példányát [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Ezt is késésekhez vezethet, ha a többi példány indítása **EventProcessorHost** címbérlet lejárati és alapidőpont ütközések miatt. Az időszakos felügyeletet részletesen ismertetjük a cikk [EPOCH](#epoch) (alapkorszak) szakaszában. 

## <a name="lease-management"></a>Partícióbérlés-kezeléssel
Események feldolgozása processzor eseményosztály Regisztrálás az EventProcessorHost példányát elindul. A gazdagép-példány szerzi be az egyes partíciók az Event Hubs-bérletek valószínűleg lekérhetem néhány más gazdagép-példányokról, úgy, hogy a partíciók az egyenletes eloszlás szerveződik át az összes gazdagép-példányok között. Minden bérelt partíció esetében a gazdagép-példány hoz létre a megadott event processor osztály egy példányát, majd fogadja az eseményeket az adott partíció és azokat az esemény processzor-példányba továbbítja. További példányok hozzáadja, és további bérleteket is olvassa be a program, az EventProcessorHost végül elosztja a terhelést minden felhasználó között.

Korábban leírtak a nyomkövetési táblát jelentősen leegyszerűsíti az automatikus skálázási jellege [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Példányaként **EventProcessorHost** elindul, annyi bérleteket beszerzi a lehető, és megkezdi az események olvasását. A bérletek lejáró, mint **EventProcessorHost** próbál megújítani őket úgy, hogy a foglalást. Ha a bérlet megújítási érhető el, a processzor folytatja, de nem érhető el, ha az Adatolvasó be van-e zárva és [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) nevezzük. **CloseAsync** egy jó ideje után megmaradó fölösleges utolsó az adott partícióhoz.

**EventProcessorHost** tartalmaz egy [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) tulajdonság. Ez a tulajdonság lehetővé teszi, hogy a partícióbérlés-kezeléssel felett. Ezekkel a beállításokkal regisztrálása előtt a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) végrehajtására.

## <a name="control-event-processor-host-options"></a>Event Processor Host beállítások

Emellett több túlterhelésének [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) vesz igénybe egy [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) paraméterként objektum. A paraméter használatával viselkedését vezérlő [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) magát. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) négy tulajdonságot és a egy esemény határozza meg:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): A [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)hívásakor fogadni kívánt gyűjtemény maximális mérete. Ez a méret nem áll a minimális, maximális mérete. Ha kevesebb üzenetek fogadását, **ProcessEventsAsync** végrehajtja, több módon is elérhető.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Az alapul szolgáló AMQP-csatorna által az ügyfél által fogadott üzenetek felső határának meghatározására használt érték. Ennek az értéknek nagyobbnak vagy azzal egyenlőnek kell lennie. [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Ha a paraméter értéke **true (igaz**), a rendszer a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) hívja meg, ha az alapul szolgáló hívás egy partíción időtúllépést kap. Ez a módszer hasznos időalapú műveletek végrehajtása során a partícióra inaktív időszakaik.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Lehetővé teszi egy függvény mutatójának vagy lambda kifejezésének beállítását, amelyet a rendszer meghív a kezdeti eltolás megadására, amikor egy olvasó elkezd egy partíciót olvasni. Az eltolás megadása, nélkül az olvasó kezdődik, a legrégebbi esemény, ha az eltolás egy JSON-fájlt már megtörtént, a megadott tárfiókban a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktor. Ez a módszer akkor hasznos, ha meg szeretné változtatni az olvasó indítási viselkedését. Ez a metódus meghívásakor az objektumot a paraméter tartalmazza a Partícióazonosító, amelynek az olvasó indítása folyamatban van.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Lehetővé teszi, hogy értesítést kapjon a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-ben előforduló mögöttes kivételekről. Dolgok nem várt módon működik, ha ez az esemény remek hogy elkezdhessük megvizsgálni.

## <a name="epoch"></a>Alapidőszak

A fogadási korszak működése:

### <a name="with-epoch"></a>EPOCH
A EPOCH a szolgáltatás által használt egyedi azonosító (EPOCH érték) a partíció/bérlet tulajdonjogának érvényesítéséhez. Létre kell hoznia egy EPOCH-alapú fogadót a [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metódus használatával. Ez a metódus egy EPOCH-alapú fogadót hoz létre. A fogadó egy adott Event hub-partícióhoz jön létre a megadott fogyasztói csoportból.

A EPOCH szolgáltatás lehetővé teszi a felhasználók számára, hogy egy adott időpontban csak egy fogadót biztosítson a fogyasztói csoport számára a következő szabályokkal:

- Ha egy fogyasztói csoport nem rendelkezik meglévő fogadóval, a felhasználó bármilyen EPOCH értékű fogadót hozhat létre.
- Ha van egy, az E1 értékkel rendelkező fogadó, és az új fogadó egy olyan EPOCH értékkel jön létre, amelyben az E1 < = E2, a fogadó az E1-vel automatikusan le lesz választva, és az E2-vel rendelkező fogadó sikeresen létrejön.
- Ha van egy, az E1 értékkel rendelkező fogadó, és az új fogadó egy olyan EPOCH-értékkel jön létre, amelyben az E1 > E2, majd az E2 létrehozása a következő hibával meghiúsul: Már létezik egy olyan fogadó, amelyen már van ilyen.

### <a name="no-epoch"></a>Nincs EPOCH
Nem EPOCH-alapú fogadót hoz létre a [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metódus használatával. 

Az adatfolyam-feldolgozás bizonyos forgatókönyveket mutat be, ahol a felhasználók több fogadót szeretnének létrehozni egyetlen felhasználói csoporton. Az ilyen forgatókönyvek támogatása érdekében lehetőség van arra, hogy EPOCH nélkül hozzon létre egy fogadót, ebben az esetben pedig legfeljebb 5 egyidejű fogadót engedélyezünk a fogyasztói csoportban.

### <a name="mixed-mode"></a>Kevert mód
Nem javasoljuk, hogy az alkalmazások használatakor hozzon létre egy olyan fogadót, amelynél a EPOCH, majd a nem-EPOCH vagy fordítva értékre váltson ugyanarra a fogyasztói csoportra. Ha azonban ez a viselkedés tapasztalható, a szolgáltatás a következő szabályok alapján kezeli azt:

- Ha van egy olyan fogadó, amely a EPOCH E1-mel lett létrehozva, és aktívan fogad eseményeket, és az új fogadót alapkorszak nélkül hozták létre, az új fogadó létrehozása sikertelen lesz. Az EPOCH-vevőkészülékek mindig elsőbbséget élveznek a rendszeren.
- Ha már létrejött egy, a (z) és a (z), a (z) és a (z) rendszerű, és egy új MessagingFactory, az új fogadó létrehozása sikeres lesz. Itt van egy figyelmeztetés arról, hogy a rendszer a "fogadó leválasztását" körülbelül 10 perc múlva fogja felderíteni.
- Ha van egy vagy több olyan fogadó, amely alapértékkel lett létrehozva, és egy új fogadó jön létre a EPOCH E1-vel, az összes régi fogadó le lesz választva.


## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az Event Processor Host, talál további információt az Event Hubs az alábbi cikkeket:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs-példák a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples)
