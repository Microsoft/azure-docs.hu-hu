---
title: Események fogadása az Event Processor Host használatával – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs Event Processor Hostját ismerteti, amely leegyszerűsíti az ellenőrzőpontok, a bérletek és az olvasási események ion párhuzamos kezelését.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: de5d8f0f8bf9f64a473b18a50434cac83e8e38c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622062"
---
# <a name="event-processor-host"></a>Event Processor Host
> [!NOTE]
> Ez a cikk az Azure Event Hubs SDK régi verziójára vonatkozik. Az SDK aktuális verziójával kapcsolatban lásd: a [partíciók terhelésének elosztása az alkalmazás több példánya között](event-processor-balance-partition-load.md). Az alábbi áttelepítési útmutatókból megtudhatja, hogyan telepítheti át a programkódot az SDK újabb verziójára. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java-parancsfájl](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Az Azure Event Hubs egy hatékony telemetria-betöltési szolgáltatás, amellyel akár több millió eseményt is továbbíthat alacsony áron. Ez a cikk azt ismerteti, hogyan lehet a betöltött eseményeket az *Event Processor Host* (EF) használatával használni. intelligens fogyasztói ügynök, amely leegyszerűsíti az ellenőrzőpontok, a lízingek és a párhuzamos események kezelését.  

A Event Hubs méretezésének kulcsa a particionált fogyasztók ötlete. A [versengő fogyasztók](/previous-versions/msp-n-p/dn568101(v=pandp.10)) mintával ellentétben a particionált fogyasztói minta nagy méretekben teszi lehetővé a szűk keresztmetszetet, és megkönnyíti a végpontok közötti párhuzamosságot.

## <a name="home-security-scenario"></a>Otthoni biztonsági forgatókönyv

Példaként vegyünk egy olyan otthoni biztonsági vállalatot, amely az 100 000-es otthonok figyelését végzi. Minden percben különböző érzékelőkből származó adatok olvashatók be, például egy mozgásérzékelő, egy ajtó/ablak nyitott érzékelő, az üveg-megszakító kimutatása stb., amely az egyes otthonokban van telepítve. A vállalat olyan webhelyet biztosít a lakosok számára, amely közel valós időben figyeli a saját otthonuk tevékenységeit.

Minden érzékelő egy Event hub-ba küldi az adatküldést. Az Event hub 16 partícióval van konfigurálva. A használat végén olyan mechanizmusra van szüksége, amely képes beolvasni ezeket az eseményeket, összevonja őket (szűrő, összesítés stb.), és kiírja az összesítést egy Storage-blobba, amelyet aztán egy felhasználóbarát weboldalra terveztek.

## <a name="write-the-consumer-application"></a>A fogyasztói alkalmazás írása

A fogyasztó elosztott környezetben történő tervezésekor a forgatókönyvnek a következő követelményeket kell kezelnie:

1. **Skála:** Több fogyasztót is létrehozhat, és minden fogyasztónak több Event Hubs partícióból kell elolvasnia a tulajdonjogát.
2. **Terheléselosztás:** Növelje vagy csökkentse dinamikusan a fogyasztókat. Ha például egy új érzékelő típusa (például egy szén-monoxid-detektor) hozzá van adva az egyes kezdőlapokhoz, az események száma nő. Ebben az esetben az operátor (emberi) növeli a fogyasztói példányok számát. Ezután a felhasználók készlete megoszthatja a saját maga által birtokolt partíciók számát, hogy megosszák a terhelést az újonnan hozzáadott fogyasztókkal.
3. **Zökkenőmentes folytatás a hibáknál:** Ha a fogyasztó (**a fogyasztó**) nem sikerül (például a fogyasztót futtató virtuális gép hirtelen összeomlik), akkor a többi fogyasztónak képesnek kell lennie az **a fogyasztó** által birtokolt és folytatott partíciók felvételére. Emellett a folytatási pontnak, amely *ellenőrzőpontnak* vagy *eltolásnak* nevezhető, pontosan olyan ponton kell lennie, ahol a **fogyasztó** sikertelen volt, vagy valamivel azelőtt.
4. **Események felhasználása:** Míg az előző három pont a fogyasztó felügyeletével foglalkozik, az események felhasználásához programkódot kell használni, és ehhez hasznosnak kell lennie. például összesítheti és feltöltheti a blob Storage-ba.

Ahelyett, hogy kiépíti a saját megoldását, Event Hubs biztosítja ezt a funkciót a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületen és a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) osztályban.

## <a name="ieventprocessor-interface"></a>IEventProcessor felület

Első lépésként az alkalmazások az  [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületet implementálják, amely négy módszerrel rendelkezik: [OpenAsync, CloseAsync, ProcessErrorAsync és ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Ez az illesztőfelület tartalmazza a tényleges kódot, amely az Event Hubs által küldött eseményeket használja fel. A következő kód egy egyszerű implementációt mutat be:

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

Ezután hozza létre az [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -példányt. A [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) példánynak a konstruktorban történő létrehozásakor a túlterhelés függvényében a következő paramétereket kell használni:

- **állomásnév:** az egyes fogyasztói példányok neve. A **EventProcessorHost** minden példányának egyedi értékkel kell rendelkeznie ehhez a változóhoz egy fogyasztói csoporton belül, ezért ne jegyezze fel ezt az értéket.
- **eventHubPath:** Az Event hub neve.
- **consumerGroupName:** A Event Hubs a **$default** használja az alapértelmezett fogyasztói csoport neveként, de célszerű létrehozni egy fogyasztói csoportot a feldolgozás konkrét aspektusához.
- **eventHubConnectionString:** Az Event hub kapcsolati karakterlánca, amely a Azure Portalból kérhető le. A kapcsolati sztringnek **figyelnie** kell az Event hub figyelő engedélyeit.
- **storageConnectionString:** A belső erőforrás-kezeléshez használt Storage-fiók.

Végezetül a felhasználók regisztrálják a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -példányt a Event Hubs szolgáltatással. Az Event Processor osztály EventProcessorHost-példányával való regisztrálása elindítja az események feldolgozását. A regisztrálás arra utasítja a Event Hubs szolgáltatást, hogy a fogyasztói alkalmazás egy adott partícióból származó eseményeket fogyasszon el, és meghívja a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációs kódot, amikor az eseményeket leküldi a felhasználásra. 

> [!NOTE]
> A consumerGroupName megkülönbözteti a kis-és nagybetűket.  A consumerGroupName módosításai az összes partíció olvasását eredményezheti a stream elejéről.

### <a name="example"></a>Példa

Tegyük fel például, hogy az események felhasználására szolgáló 5 virtuális gép (VM) és egy egyszerű konzol-alkalmazás minden virtuális gépen, amely a tényleges fogyasztást végzi. Ezután mindegyik konzolszoftver létrehoz egy [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -példányt, és regisztrálja azt a Event Hubs szolgáltatásban.

Ebben a példában tegyük fel, hogy 16 partíció van lefoglalva az 5 **EventProcessorHost** -példányhoz. Egyes **EventProcessorHost** -példányok a többinél több partíciót is tartalmazhatnak. Minden olyan partícióhoz, amely egy **EventProcessorHost** -példány tulajdonosa, létrehozza a osztály egy példányát `SimpleEventProcessor` . Ezért az `SimpleEventProcessor` egyes partíciók esetében legalább 16 példány létezik.

A következő lista összefoglalja a példát:

- 16 Event Hubs partíció.
- 5 virtuális gép, 1 fogyasztói alkalmazás (például Consumer.exe) minden egyes virtuális gépen.
- 5 EF-példány regisztrálva, 1 az egyes virtuális gépeken Consumer.exe alapján.
- `SimpleEventProcessor`az 5 EF-példány által létrehozott 16 objektum.
- 1 Consumer.exe alkalmazás 4 objektumot tartalmazhat `SimpleEventProcessor` , mivel az 1 EF-példány 4 partícióval rendelkezhet.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjogának nyomon követése

A partíciók EF-példányra (vagy fogyasztóra) való tulajdonjogát nyomon követjük a nyomon követéshez biztosított Azure Storage-fiókon keresztül. A nyomon követést egyszerű táblázatként jelenítheti meg, az alábbiak szerint. A tényleges megvalósítást úgy tekintheti meg, ha megvizsgálja a blobokat a megadott Storage-fiókban:

| **Fogyasztói csoport neve** | **Partícióazonosító** | **Állomásnév (tulajdonos)** | **Bérlet (vagy tulajdonos) beszerzett ideje** | **Eltolás a partícióban (ellenőrzőpont)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Fogyasztói \_ VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Fogyasztói \_ VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Fogyasztói \_ VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Fogyasztói \_ VM3 | 2018-04-15T01:22:56 | 976 |

Itt minden gazdagép egy adott időtartamra (a címbérlet időtartamára) egy partíció tulajdonjogát szerzi be. Ha egy gazdagép meghibásodik (a virtuális gép leáll), a bérlet lejár. Más gazdagépek megpróbálják beolvasni a partíció tulajdonjogát, és az egyik gazdagép sikeres. Ez a folyamat visszaállítja a partíción lévő bérletet egy új tulajdonossal. Így egyszerre csak egyetlen olvasó tud olvasni egy felhasználói csoporton belüli adott partícióról.

## <a name="receive-messages"></a>Üzenetek fogadása

A [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) minden egyes hívása események gyűjteményét biztosítja. Ezek az események az Ön felelőssége, hogy kezelje ezeket az eseményeket. Ha azt szeretné, hogy a feldolgozó gazdagépe legalább egyszer dolgozza fel az összes üzenetet, meg kell írnia a saját Keep rewrite kódját. Legyen óvatos a mérgezett üzenetekkel kapcsolatban.

Azt javasoljuk, hogy viszonylag gyorsan hajtsa végre a dolgokat; Ez a lehető legkevesebb feldolgozás. Ehelyett használjon fogyasztói csoportokat. Ha a tárhelyre kell írnia, és el kell végeznie néhány útválasztást, érdemes két fogyasztói csoportot használnia, és két [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementációja van, amelyek külön futnak.

Előfordulhat, hogy a feldolgozás során egy ponton nyomon szeretné követni az elolvasott és befejezett tartalmakat. A nyomon követés kritikus fontosságú, ha újra kell indítani az olvasást, így nem térhet vissza az adatfolyam elejéhez. A [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) *ellenőrzőpontok* használatával egyszerűsíti ezt a nyomkövetést. Az ellenőrzőpont egy adott partíció helye vagy eltolása egy adott felhasználói csoporton belül, amikor meggyőződött arról, hogy feldolgozta az üzeneteket. Az ellenőrzőpontok **EventProcessorHost** való megjelölése a [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objektum [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódusának meghívásával valósítható meg. Ez a művelet a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metóduson belül történik, de a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)is végezhető el.

## <a name="checkpointing"></a>Ellenőrző pontok használata

A [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metódus két túlterheléssel rendelkezik: az első, paraméterek nélkül, a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)által visszaadott gyűjtemény legmagasabb eseményének eltolására vonatkozó ellenőrzőpontok. Ez az eltolás egy "magas víz" jelölés; feltételezi, hogy az összes legutóbbi eseményt feldolgozta a hívásakor. Ha ezt a módszert használja, vegye figyelembe, hogy a másik esemény-feldolgozási kód visszaadását követően meg kell hívnia azt. A második túlterhelés lehetővé teszi egy [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) -példány megadását az ellenőrzőponthoz. Ez a módszer lehetővé teszi, hogy más típusú vízjel használatával ellenőrzőpontot használjon. Ezzel a vízjelekkel a "kis víz" jelölést lehet megvalósítani: az Ön által meghatározott legalacsonyabb sorszámú eseményt feldolgozták. Ez a túlterhelés biztosítja a rugalmasságot az eltolási felügyeletben.

Az ellenőrzőpont elvégzése után a rendszer a partícióra vonatkozó adatokat tartalmazó JSON-fájlt (pontosabban az eltolást) a konstruktorban a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)megadott Storage-fiókba írja. A fájl folyamatosan frissül. Fontos, hogy az ellenőrzőpontokat a kontextusban érdemes figyelembe venni – az összes üzenet ellenőrzőpontja nem lenne bölcs. Az ellenőrzőpontokhoz használt Storage-fiók valószínűleg nem fogja kezelni ezt a terhelést, de az egyes eseményeknél fontosabb ellenőrzőpontok jelzik a várólistán lévő üzenetküldési mintát, amelyhez egy Service Bus üzenetsor jobb megoldás lehet, mint az Event hub. A Event Hubs mögötti ötlet az, hogy "legalább egyszer" kézbesítést kap nagy léptékben. Az alsóbb rétegbeli rendszerek idempotens egyszerűen helyreállítható a hibák vagy újraindítások, amelyek ugyanazt az eseményt többször kapják meg.

## <a name="thread-safety-and-processor-instances"></a>A szál biztonsági és processzor-példányai

Alapértelmezés szerint a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) a szál biztonságos, és szinkron módon viselkedik a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-példányra vonatkozóan. Amikor az események megérkeznek egy partícióra, a rendszer a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) hívja meg a partíció **IEventProcessor** -példányát, és letiltja a **ProcessEventsAsync** további hívásait a partícióhoz. A következő üzenetek és hívások a **ProcessEventsAsync** várólistára a háttérben, mivel az üzenet-szivattyú továbbra is fut a háttérben a többi szálon. Ez a szál biztonsága megszünteti a szál-biztonságos gyűjtemények szükségességét, és jelentősen növeli a teljesítményt.

## <a name="shut-down-gracefully"></a>Kikapcsolás szabályosan

Végezetül, a [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) lehetővé teszi az összes partíciós olvasó tiszta leállítását, és a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)egy példányának leállításakor mindig meg kell hívni. Ha ezt elmulasztja, akkor késések jelentkezhetnek a **EventProcessorHost** más példányainak a bérlet lejárati ideje és az EPOCH-ütközések miatt. Az időszakos felügyeletet részletesen ismertetjük a cikk [EPOCH (alapkorszak](#epoch) ) szakaszában. 

## <a name="lease-management"></a>Bérletek kezelése
Az Event Processor osztály EventProcessorHost-példányával való regisztrálása elindítja az események feldolgozását. A gazdagép-példány az Event hub egyes partícióinak bérleteit szerzi be, valószínűleg más gazdagép-példányok esetében is, amelyek a partíciók egyenletes eloszlását jelentik az összes gazdagép-példányon. Az egyes bérelt partíciók esetében a gazdagép-példány létrehozza a megadott Event Processor osztály egy példányát, majd a partícióból fogad eseményeket, és átadja azokat az Event Processor-példánynak. Ahogy egyre több példány lett hozzáadva, és több bérletet is megragadott, a EventProcessorHost végül kiegyensúlyozza a terhelést az összes fogyasztó között.

Ahogy azt korábban említettük, a követési táblázat nagy mértékben leegyszerűsíti az [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync)autoskálázási jellegét. A **EventProcessorHost** egy példánya elindítja a lehető legtöbb bérletet, és megkezdi az események olvasását. A bérletek lejárata közelében a **EventProcessorHost** a foglalások elhelyezésével megkísérli megújítani őket. Ha a bérlet megújítható, a processzor folytatja az olvasást, de ha nem, akkor az olvasó bezárult, és a [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) hívja meg. A **CloseAsync** jó alkalom arra, hogy elvégezze az adott partíció végleges törlését.

A **EventProcessorHost** tartalmaz egy [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) tulajdonságot. Ez a tulajdonság lehetővé teszi a bérletek felügyeletét. Ezeket a beállításokat a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -megvalósítás regisztrálása előtt állíthatja be.

## <a name="control-event-processor-host-options"></a>Az Event Processor Host beállításainak vezérlése

Emellett a [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) egyik túlterhelése egy [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objektumot használ paraméterként. Ezzel a paraméterrel szabályozhatja a [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) viselkedését. A [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) négy tulajdonságot és egy eseményt határoz meg:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)hívásakor fogadni kívánt gyűjtemény maximális mérete. Ez a méret nem a minimális, csak a maximális méret. Ha kevesebb üzenet érkezik, a **ProcessEventsAsync** a lehető legtöbb módon hajtja végre.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): a mögöttes AMQP-csatorna által használt érték, amely meghatározza, hogy az ügyfél hány üzenetet kapjon. Ennek az értéknek nagyobbnak vagy egyenlőnek kell lennie a [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Ha ez a paraméter **igaz**, akkor a rendszer akkor hívja meg a [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) , ha az alapul szolgáló hívás egy partíción időtúllépést kap. Ez a módszer hasznos lehet a partíción belüli inaktivitási időszakok idejének időalapú műveleteinek megkezdéséhez.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): lehetővé teszi egy függvény mutatójának vagy lambda kifejezésének beállítását, amelyet a rendszer meghív a kezdeti eltolás megadására, amikor egy olvasó elkezd egy partíciót olvasni. Az eltolás megadása nélkül az olvasó a legrégebbi eseményen indul el, kivéve, ha egy eltolású JSON-fájl már el lett mentve a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktorhoz megadott Storage-fiókban. Ez a módszer akkor hasznos, ha módosítani szeretné az olvasó indításának viselkedését. A metódus meghívásakor az Object paraméter tartalmazza azt a partíció-azonosítót, amelyhez az olvasó elindult.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): lehetővé teszi, hogy értesítést kapjon a [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-ben előforduló mögöttes kivételekről. Ha a dolgok nem a várt módon működnek, ez az esemény jó kiindulópont a kereséshez.

## <a name="epoch"></a>Alapidőszak

A fogadási korszak működése:

### <a name="with-epoch"></a>EPOCH
A EPOCH a szolgáltatás által használt egyedi azonosító (EPOCH érték) a partíció/bérlet tulajdonjogának érvényesítéséhez. Létre kell hoznia egy EPOCH-alapú fogadót a [CreateEpochReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) metódus használatával. Ez a metódus egy EPOCH-alapú fogadót hoz létre. A fogadó egy adott Event hub-partícióhoz jön létre a megadott fogyasztói csoportból.

A EPOCH szolgáltatás lehetővé teszi a felhasználók számára, hogy egy adott időpontban csak egy fogadót biztosítson a fogyasztói csoport számára a következő szabályokkal:

- Ha egy fogyasztói csoport nem rendelkezik meglévő fogadóval, a felhasználó bármilyen EPOCH értékű fogadót hozhat létre.
- Ha van egy, az E1 értékkel rendelkező fogadó, és az új fogadó egy olyan EPOCH értékkel jön létre, amelyben az E1 <= E2, a fogadó az E1-vel automatikusan le lesz választva, és az E2-vel rendelkező fogadó sikeresen létrejön.
- Ha van egy olyan fogadó, amely az E1 értékkel rendelkezik, és az új fogadó egy olyan EPOCH-értékkel jön létre, ahol az E1 > E2, majd az E2 létrehozása a következő hibával meghiúsult: egy olyan fogadó, amelynél a EPOCH E1 már létezik.

### <a name="no-epoch"></a>Nincs EPOCH
Nem EPOCH-alapú fogadót hoz létre a [CreateReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) metódus használatával. 

Az adatfolyam-feldolgozás bizonyos forgatókönyveket mutat be, ahol a felhasználók több fogadót szeretnének létrehozni egyetlen felhasználói csoporton. Az ilyen forgatókönyvek támogatása érdekében lehetőség van arra, hogy EPOCH nélkül hozzon létre egy fogadót, ebben az esetben pedig legfeljebb 5 egyidejű fogadót engedélyezünk a fogyasztói csoportban.

### <a name="mixed-mode"></a>Kevert mód
Nem javasoljuk, hogy az alkalmazások használatakor hozzon létre egy olyan fogadót, amelynél a EPOCH, majd a nem-EPOCH vagy fordítva értékre váltson ugyanarra a fogyasztói csoportra. Ha azonban ez a viselkedés tapasztalható, a szolgáltatás a következő szabályok alapján kezeli azt:

- Ha van egy olyan fogadó, amely a EPOCH E1-mel lett létrehozva, és aktívan fogad eseményeket, és az új fogadót alapkorszak nélkül hozták létre, az új fogadó létrehozása sikertelen lesz. Az EPOCH-vevőkészülékek mindig elsőbbséget élveznek a rendszeren.
- Ha már létrejött egy, a (z) és a (z), a (z) és a (z) rendszerű, és egy új MessagingFactory, az új fogadó létrehozása sikeres lesz. Itt van egy figyelmeztetés arról, hogy a rendszer a "fogadó leválasztását" körülbelül 10 perc múlva fogja felderíteni.
- Ha van egy vagy több olyan fogadó, amely alapértékkel lett létrehozva, és egy új fogadó jön létre a EPOCH E1-vel, az összes régi fogadó le lesz választva.


> [!NOTE]
> Azt javasoljuk, hogy különböző fogyasztói csoportokat alkalmazzon olyan alkalmazásokhoz, amelyek alapértékeket használnak, és amelyek nem használják a korokat a hibák elkerülésére. 


## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az Event Processor Hostt, tekintse meg a következő cikkeket, amelyekben további információt talál a Event Hubs:

- Bevezetés az Event Hubs használatába
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs minták a GitHubon](https://github.com/Azure/azure-event-hubs/tree/master/samples)
