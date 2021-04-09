---
title: Partíciók terhelésének elosztása több példány között – Azure Event Hubs | Microsoft Docs
description: Ismerteti, hogyan lehet terheléselosztást végezni az alkalmazás több példánya között egy eseményvezérelt processzor és az Azure Event Hubs SDK használatával.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025044"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>A partíciók terhelésének elosztása az alkalmazás több példánya között
Az Event Processing-alkalmazás méretezéséhez az alkalmazás több példánya is futtatható, és saját maguk is elérhetik a terhelést. A régebbi verziókban a [EventProcessorHost](event-hubs-event-processor-host.md) lehetővé tette a program több példánya közötti terhelés elosztását és az ellenőrzőpont-események fogadását. Az újabb verziókban (5,0-ig) a **EventProcessorClient** (.net és Java) vagy a **EventHubConsumerClient** (Python és JavaScript) is lehetővé teszi ugyanezt. A fejlesztési modellt az események használatával egyszerűbbé teszik. Előfizet az Önt érdeklő eseményekre egy eseménykezelő regisztrálásával. Ha az ügyféloldali kódtár régi verzióját használja, tekintse meg a következő áttelepítési útmutatókat: [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)és [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

Ez a cikk azt szemlélteti, hogyan lehet több példányt használni az Event hub eseményeinek beolvasásához, majd az Event Processor-ügyfél funkcióinak részletes ismertetését, amely lehetővé teszi, hogy egyszerre több partícióról fogadjon eseményeket, és a terheléselosztást más, ugyanazon az Event hub-t és a fogyasztói csoportot használó fogyasztókkal.

> [!NOTE]
> A Event Hubs méretezésének kulcsa a particionált fogyasztók ötlete. A [versengő fogyasztók](/previous-versions/msp-n-p/dn568101(v=pandp.10)) mintával ellentétben a particionált fogyasztói minta nagy méretekben teszi lehetővé a szűk keresztmetszetet, és megkönnyíti a végpontok közötti párhuzamosságot.

## <a name="example-scenario"></a>Példaforgatókönyv

Példaként vegyünk egy olyan otthoni biztonsági vállalatot, amely az 100 000-es otthonok figyelését végzi. Minden percben különböző érzékelőkből, például egy mozgásérzékelőből, egy ajtó/ablak nyitott érzékelőből, az üvegből történő kiderítés és így tovább. A vállalat olyan webhelyet biztosít a lakosok számára, amely közel valós időben figyeli a saját otthonuk tevékenységeit.

Minden érzékelő egy Event hub-ba küldi az adatküldést. Az Event hub 16 partícióval van konfigurálva. A felhasználás végén olyan mechanizmusra van szüksége, amely képes beolvasni ezeket az eseményeket, összevonja őket (szűrés, összesítés stb.), és kiírja az összesítést egy Storage-blobba, amelyet aztán egy felhasználóbarát weboldalra terveztek.

## <a name="write-the-consumer-application"></a>A fogyasztói alkalmazás írása

A fogyasztó elosztott környezetben történő tervezésekor a forgatókönyvnek a következő követelményeket kell kezelnie:

1. **Skála:** Több fogyasztót is létrehozhat, és minden fogyasztónak több Event Hubs partícióból kell elolvasnia a tulajdonjogát.
2. **Terheléselosztás:** Növelje vagy csökkentse dinamikusan a fogyasztókat. Ha például egy új érzékelő típusa (például egy szén-monoxid-detektor) hozzá van adva az egyes kezdőlapokhoz, az események száma nő. Ebben az esetben az operátor (emberi) növeli a fogyasztói példányok számát. Ezután a felhasználók készlete megoszthatja a saját maga által birtokolt partíciók számát, hogy megosszák a terhelést az újonnan hozzáadott fogyasztókkal.
3. **Zökkenőmentes folytatás a hibáknál:** Ha a fogyasztó (**a fogyasztó**) nem sikerül (például a fogyasztót futtató virtuális gép hirtelen összeomlik), akkor a többi fogyasztó az **a felhasználó** által birtokolt és folytatott partíciókat is felveheti. Emellett a folytatási pontnak, amely *ellenőrzőpontnak* vagy *eltolásnak* nevezhető, pontosan olyan ponton kell lennie, ahol a **fogyasztó** sikertelen volt, vagy valamivel azelőtt.
4. **Események felhasználása:** Míg az előző három pont a fogyasztó felügyeletével foglalkozik, az események felhasználásához programkódot kell használni, és ehhez hasznos lehet. Például összesítheti és feltöltheti a blob Storage-ba.

## <a name="event-processor-or-consumer-client"></a>Event processzor vagy fogyasztói ügyfél

A követelmények teljesítéséhez nem kell saját megoldást létrehoznia. Az Azure Event Hubs SDK-k biztosítják ezt a funkciót. .NET-vagy Java SDK-k esetén az EventHubConsumerClient-t használja a EventProcessorClient, a Pythonban és a JavaScript SDK-ban. Az SDK régi verziójában ez volt az a funkciókat támogató EventProcessorHost-gazda.

Az éles környezetek többsége számára azt javasoljuk, hogy az Event Processor-ügyfelet az események olvasására és feldolgozására használja. A processzor-ügyfél hatékony élményt biztosít az Event hub összes partícióján lévő események teljesítménybeli és hibatűrő módon történő feldolgozásához, miközben a folyamat előrehaladását is lehetővé teszi. Az esemény-feldolgozó ügyfelek az adott esemény központjának felhasználói csoportja kontextusában működhetnek. Az ügyfelek automatikusan kezelik a munka eloszlását és kiegyensúlyozását, mivel a példányok elérhetővé válnak, vagy nem lesznek elérhetők a csoport számára.

## <a name="partition-ownership-tracking"></a>Partíció tulajdonjogának nyomon követése

Egy esemény-feldolgozó példány általában egy vagy több partícióról származó eseményeket birtokol és dolgoz fel. A partíciók tulajdonjoga egyenletesen oszlik el az Event hub és a fogyasztói csoport kombinációhoz társított aktív Event Processor-példányok között. 

Minden eseményvezérelt processzor egyedi azonosítót kap, és a partíciók tulajdonjogát egy ellenőrzőpont-tárolóban lévő bejegyzés hozzáadásával vagy frissítésével állítja be. Az összes Event Processor-példány rendszeresen kommunikál a tárolóval, hogy frissítse a saját feldolgozási állapotát, valamint az egyéb aktív példányok megismerését. Ezt követően a rendszer az aktív processzorok közötti terhelés elosztására használja fel ezeket az adatkészleteket. Az új példányok a felskálázáshoz csatlakozhatnak a feldolgozó készlethez. Ha a példányok leállnak a meghibásodások vagy a leskálázás miatt, a partíciók tulajdonjoga szabályosan átkerül más aktív processzorokra.

Az ellenőrzőpont-tárolóban található partíciós tulajdonosi rekordok nyomon követhetik Event Hubs névteret, az Event hub nevét, a fogyasztói csoportot, az eseményvezérelt processzor azonosítóját (más néven tulajdonost), a partíció AZONOSÍTÓját és az utolsó módosítás időpontját.



| Event Hubs-névtér               | Eseményközpont neve | **Fogyasztói csoport** | Tulajdonos                                | Partícióazonosító | Utolsó módosítás időpontja  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020 – 01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020 – 01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020 – 01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020 – 01-15T01:22:00 |

Minden Event Processor-példány egy partíció tulajdonjogát szerzi be, és elindítja az utolsó ismert [ellenőrzőpontról](# Checkpointing)a partíció feldolgozását. Ha a processzor meghibásodik (a virtuális gép leáll), akkor a többi példány az utolsó módosítás időpontját vizsgálja. Más példányok megpróbálják megszerezni az inaktív példány által korábban birtokolt partíciók tulajdonjogát, és az ellenőrzőpont-tároló garantálja, hogy csak az egyik példány sikerül a partíció tulajdonjogának igénylésében. Tehát egy adott időpontban legfeljebb egy olyan processzor található, amely egy partícióról fogad eseményeket.

## <a name="receive-messages"></a>Üzenetek fogadása

Eseményvezérelt processzor létrehozásakor meg kell adnia azokat a függvényeket, amelyek feldolgozzák az eseményeket és a hibákat. Az eseményeket feldolgozó függvények minden hívása egyetlen eseményt biztosít egy adott partícióról. Ezt az eseményt az Ön felelőssége kezelni. Ha azt szeretné, hogy a fogyasztó minden üzenetet legalább egyszer feldolgozzon, újra kell írnia a saját kódját az újrapróbálkozási logikával. Legyen óvatos a mérgezett üzenetekkel kapcsolatban.

Javasoljuk, hogy a dolgokat viszonylag gyorsan végezze. Ez a lehető legkevesebb feldolgozás. Ha a tárhelyre kell írnia, és el kell végeznie az útválasztást, érdemes két fogyasztói csoportot használnia, és két eseménykezelővel kell rendelkeznie.

## <a name="checkpointing"></a>Ellenőrző pontok használata

Az *ellenőrzőpontok* olyan folyamat, amellyel egy adott esemény processzora megjelöli vagy véglegesíti az utolsó sikeresen feldolgozott esemény pozícióját egy partíción belül. Az ellenőrzőpontok megjelölése általában a függvényen belül történik, amely feldolgozza az eseményeket, és a felhasználói csoporton belül partíciós alapon történik. 

Ha egy esemény-feldolgozó egy partícióról bontja a kapcsolatot, egy másik példány folytathatja a partíció feldolgozását az ellenőrzőponton, amelyet korábban az adott felhasználói csoportban lévő partíció utolsó processzora véglegesített. Amikor a processzor csatlakozik, átadja az eltolást az Event hub-nak, hogy megadja azt a helyet, ahol az olvasást el szeretné indítani. Ily módon az ellenőrzőpontok segítségével megadhatja az eseményeket "befejezettként" az alárendelt alkalmazások számára, és biztosíthatja a rugalmasságot, ha az esemény processzora leáll. Vissza lehet térni a régebbi adatokhoz egy alacsonyabb eltolás megadásával az ellenőrzőpontok folyamata során. 

Ha az ellenőrzőpontot egy esemény feldolgozottként való megjelölésére hajtja végre, az ellenőrzőpont-tárolóban egy bejegyzés kerül hozzáadásra vagy frissítésre az esemény eltolásával és sorszámával. A felhasználóknak el kell dönteniük az ellenőrzőpont frissítésének gyakoriságát. Az egyes sikeres feldolgozott események utáni frissítés teljesítményre és költséghatékonyságra is hatással lehet, mivel írási műveletet indít el az alapul szolgáló ellenőrzőpont-tárolóba. Emellett az ellenőrzőpontok minden egyes eseménynél egy várólistán lévő üzenetküldési mintát jelezhetnek, amelyhez egy Service Bus üzenetsor jobb megoldás lehet, mint az Event hub. A Event Hubs mögötti ötlet az, hogy "legalább egyszer" kézbesítést kap nagy léptékben. Az alsóbb rétegbeli rendszerek idempotens egyszerűen helyreállítható a hibák vagy újraindítások, amelyek ugyanazt az eseményt többször kapják meg.

> [!NOTE]
> Ha Azure Blob Storaget használ az ellenőrzőpont-tárolóként olyan környezetben, amely támogatja a Storage blob SDK egy másik verzióját, mint az Azure-ban, akkor a Storage szolgáltatás API-verziójának az adott környezet által támogatott verzióra való módosításához programkódot kell használnia. Ha például [egy 2002-es Azure stack hub-os verzióban](/azure-stack/user/event-hubs-overview)futtatja az Event Hubs-t, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2017-11-09-es verzió. Ebben az esetben programkódot kell használnia a Storage szolgáltatás API-verziójának 2017-11-09-re való célzásához. Az adott tárolási API-verzió célzására vonatkozó példát a GitHubon található példákban talál: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) vagy  [írógéppel](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>A szál biztonsági és processzor-példányai

Alapértelmezés szerint az eseményeket feldolgozó függvényt a rendszer szekvenciálisan egy adott partícióra hívja. Az ezt a függvényt érintő további események és hívások ugyanabból a partíciós várólistából a háttérben, ahogy az esemény-szivattyú továbbra is fut a háttérben a többi szálon. A különböző partíciók eseményei egyszerre is feldolgozhatók, és a partíciók között elérhető megosztott állapotokat szinkronizálni kell.

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi rövid útmutatókat:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
