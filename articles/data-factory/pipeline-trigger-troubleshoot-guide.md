---
title: A folyamat-összehangolás és az eseményindítók hibáinak megoldása Azure Data Factory
description: Különböző módszerekkel végezheti el a folyamat-triggerekkel kapcsolatos hibák elhárítását Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 03/13/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 72f2a5eec25b9acc2aedd7b006fe3380141781c8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563412"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>A folyamat-összehangolás és az eseményindítók hibáinak megoldása Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A folyamatfuttatás egy folyamat-végrehajtás egy példányát határozza meg az Azure Data Factoryben. Tegyük fel például, hogy rendelkezik egy olyan folyamattal, amely a következő időpontban fut: 8:00, 9:00 AM és 10:00. Ebben az esetben három külön folyamat fut. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. A futtatási azonosító egy globálisan egyedi azonosító (GUID), amely meghatározza, hogy az adott folyamat fusson.

A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatban meghatározott paraméterekhez történő továbbításával történik. A folyamatokat manuálisan vagy trigger használatával is futtathatja. Részletekért lásd: [folyamat-végrehajtás és eseményindítók Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Gyakori problémák, okok és megoldások

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Egy Azure Functions alkalmazási folyamat hibát jelez a privát végponti kapcsolattal
 
Egy privát végponton Data Factory és egy Azure Function-alkalmazás fut. Olyan folyamatot próbál futtatni, amely együttműködik a Function alkalmazással. Három különböző metódust próbált meg használni, de az egyik a "hibás kérelem" hibaüzenetet adja vissza, a másik két metódus pedig "103 hibát tiltott".

**Ok**

A Data Factory jelenleg nem támogatja a Function apps-hez készült privát végpont-összekötőt. Azure Functions elutasítja a hívásokat, mert úgy van konfigurálva, hogy csak privát kapcsolaton keresztül engedélyezze a kapcsolatokat.

**Resolution** (Osztás)

Hozzon létre egy **PrivateLinkService** -végpontot, és adja meg a függvény alkalmazásának DNS-ét.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>A folyamat futása megszakadt, de a figyelő továbbra is megjeleníti a folyamat állapotát.

**Ok**

A folyamat futásának megszakításakor a folyamat figyelése gyakran továbbra is a folyamat állapotát mutatja. Ez egy böngésző gyorsítótárának hibája miatt fordul elő. Előfordulhat, hogy nem rendelkezik a megfelelő figyelési szűrőkkel.

**Resolution** (Osztás)

Frissítse a böngészőt, és alkalmazza a megfelelő figyelési szűrőket.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>A folyamat másolásakor "DelimitedTextMoreColumnsThanDefined" hibaüzenet jelenik meg
 
 **Ok**
 
Ha a másolt mappa különböző sémákkal rendelkező fájlokat tartalmaz, például az oszlopok változó számát, a különböző elválasztó karaktereket, az idézőjelek karakteres beállításait vagy valamilyen adatproblémát, akkor a Data Factory folyamat ezt a hibát okozhatja:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Resolution** (Osztás)

A másolási tevékenység létrehozásakor válassza a **bináris másolás** lehetőséget. Így az adatok tömeges másolása vagy áttelepítése az egyik adattóból egy másikba, Data Factory nem fogja megnyitni a fájlokat a séma olvasásához. Ehelyett Data Factory az egyes fájlokat bináris fájlként fogja kezelni, és a másik helyre másolja.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Egy folyamat futtatása meghiúsul, ha eléri az adatfolyam-integrációs modul kapacitásának korlátját

**Kérdés**

Hibaüzenet:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Ok**

Elérte az Integration Runtime kapacitási korlátját. Lehet, hogy nagy mennyiségű adatfolyamot futtat ugyanazon integrációs modul egyidejű használatával. Részletekért lásd az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Resolution** (Osztás)
 
- A folyamatokat különböző indító időpontokban futtathatja.
- Hozzon létre egy új integrációs modult, és ossza szét a folyamatokat több integrációs modul között.

### <a name="how-to-perform-activity-level-errors-and-failures-in-pipelines"></a>Tevékenység szintű hibák és hibák végrehajtása a folyamatokban

**Ok**

Azure Data Factory az előkészítés lehetővé teszi a feltételes logikát, és lehetővé teszi, hogy a felhasználók a korábbi tevékenységek eredményétől függően különböző útvonalakat használjanak. Négy feltételes elérési utat tesz lehetővé: **sikeres** (alapértelmezett pass), **hiba** esetén, **befejezéskor** és **kihagyás után**. 

Azure Data Factory kiértékeli az összes levél szintű tevékenység eredményét. A folyamat eredményei csak akkor lesznek sikeresek, ha az összes művelet sikeresen befejeződött. Ha a levél tevékenység ki lett hagyva, a fölérendelt tevékenységet kiértékeljük. 

**Resolution** (Osztás)

* Tevékenység szintű ellenőrzések végrehajtása a [folyamat hibáinak és hibáinak kezelésével](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
* A Azure Logic Apps használatával rendszeres időközönként figyelheti a folyamatokat a [gyár lekérdezését](/rest/api/datafactory/pipelineruns/querybyfactory)követően.
* [Folyamat vizuális monitorozása](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Folyamat-meghibásodások figyelése rendszeres időközönként

**Ok**

Előfordulhat, hogy a sikertelen Data Factory folyamatokat nem kell figyelnie, például 5 percet. A folyamatot a végpont használatával kérdezheti le és szűrheti a folyamat futtatását egy adatok gyárában. 

**Resolution** (Osztás)
* Beállíthat egy Azure logikai alkalmazást, amely 5 percenként lekérdezi az összes meghiúsult folyamatot a következő témakörben leírtak szerint: [query by Factory](/rest/api/datafactory/pipelineruns/querybyfactory). Ezt követően az incidenseket bejelenthetjük a jegyrendszer rendszerébe.
* [Folyamat vizuális monitorozása](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>A párhuzamossági fok növekedése nem eredményez nagyobb átviteli sebességet

**Ok** 

A *foreach* párhuzamossági foka valójában a maximális párhuzamossági fok. Egy adott időpontban nem garantálunk bizonyos számú végrehajtást, de ez a paraméter garantálja, hogy soha nem haladnak a beállított érték fölé. Ezt korlátként kell látnia, hogy kihasználja a forrásokhoz és a mosdóhoz való egyidejű hozzáférés szabályozásakor.

*Foreach* kapcsolatos ismert tények
 * A foreach rendelkezik egy batchs count (n) nevű tulajdonsággal, ahol az alapértelmezett érték 20, a Max pedig 50.
 * A kötegek száma n, az n várólisták létrehozásához használatos. Később megbeszéljük a várólisták kiépítésének részleteit.
 * Minden üzenetsor egymás után fut, de több várólista is futtatható párhuzamosan.
 * A várólisták előre lettek létrehozva. Ez azt jelenti, hogy a futásidejű modulban nincs szükség a várólisták kiegyensúlyozására.
 * Egyszerre legfeljebb egy, várólistára feldolgozható elemnek kell lennie. Ez azt jelenti, hogy a legtöbb n elem feldolgozása egy adott időpontban történik.
 * A foreach teljes feldolgozási ideje megegyezik a leghosszabb várólista feldolgozási idejével. Ez azt jelenti, hogy a foreach tevékenység a várólisták kiépítésének módjától függ.
 
**Resolution** (Osztás)

 * A *SetVariable* tevékenységet ne használja a párhuzamosan futó *rendszerekben* .
 * A várólisták létrehozási módjának figyelembevételével az ügyfél növelheti a foreach teljesítményét úgy, hogy több *foreaches* állít be, ahol minden foreach hasonló feldolgozási idővel rendelkező elemek lesznek. Ez biztosítja, hogy a hosszú távú feldolgozásokat párhuzamosan dolgozza fel a rendszer.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>A folyamat állapota várólistára van állítva, vagy hosszú ideig ragadt
 
 **Ok**
 
 Ez különböző okok miatt fordulhat elő, például a párhuzamossági korlátok, a szolgáltatási kimaradások, a hálózati hibák és így tovább.
 
 **Resolution** (Osztás)
 
* Egyidejűségi korlát: Ha a folyamat egy egyidejűségi házirenddel rendelkezik, ellenőrizze, hogy nem fut-e folyamatban a régi folyamat. A Azure Data Factoryban engedélyezett maximális feldolgozási folyamat 10 folyamat. 
* Figyelési korlátok: lépjen az ADF authoring vászonra, válassza ki a folyamatot, és állapítsa meg, hogy van-e társítva Egyidejűség tulajdonsága. Ha igen, ugorjon a figyelés nézetre, és győződjön meg arról, hogy az elmúlt 45 napban nincs folyamatban. Ha van valamilyen folyamatban lévő művelet, megszakíthatja, és az új folyamat futtatásának kell kezdődnie.
* Átmeneti problémák: lehetséges, hogy a futtatását egy átmeneti hálózati probléma, a hitelesítő adatok meghibásodása, a szolgáltatások leállása vagy a leállás befolyásolta.  Ha ez történik, Azure Data Factory belső helyreállítási folyamattal rendelkezik, amely figyeli az összes futtatást, és elindítja őket, amikor észreveszi, hogy hiba történt. Ez a folyamat óránként történik, így ha a Futtatás több mint egy óráig elakad, hozzon létre egy támogatási esetet.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Az ADF-másolási és-adatfolyam-műveletekhez tartozó tevékenységek hosszú ideje

**Ok**

Ez akkor fordulhat elő, ha az adatforgalom vagy az optimalizált, ha nem alkalmazta az élő szolgáltatást.

**Resolution** (Osztás)

* Ha az egyes másolási tevékenységek elindítása akár 2 percet is igénybe vehet, és a probléma elsősorban egy virtuális hálózat csatlakozásánál fordul elő (szemben az Azure IR esetével), ez a másolási teljesítménnyel kapcsolatos hiba lehet. A hibaelhárítási lépések áttekintéséhez lépjen a [teljesítmény javítása](./copy-activity-performance-troubleshooting.md) elemre.
* Az élő szolgáltatással időt is igénybe vehet, hogy csökkentse a fürt indítási idejét az adatfolyam-tevékenységek esetében. Tekintse át [az Adatáramlási Integration Runtime.](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Megtalálhatja a kapacitással kapcsolatos problémákat a (saját üzemeltetésű Integration Runtime)
 
 **Ok**
 
Ez akkor fordulhat elő, ha a számítási feladatnak megfelelően nem méretezett fel.

**Resolution** (Osztás)

* Ha a rendszer kapacitással kapcsolatos problémát tapasztal a-ről, frissítse a virtuális gépet, és növelje a csomópontot a tevékenységek kiegyensúlyozásához. Ha egy saját üzemeltetésű IR általános hibával vagy hibával kapcsolatos hibaüzenet jelenik meg, a saját üzemeltetésű IR-frissítés vagy a saját üzemeltetésű IR-kapcsolat problémái, amelyek hosszú üzenetsor létrehozását eredményezik, ugorjon a saját üzemeltetésű [integrációs modul hibaelhárítása](./self-hosted-integration-runtime-troubleshoot-guide.md) című lépésre.

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Hibaüzenetek a hosszú várólisták miatt az ADF másolásához és az adatfolyamhoz

**Ok**

A hosszú várólistával kapcsolatos hibaüzenetek számos okból megjelenhetnek. 

**Resolution** (Osztás)
* Ha az összekötőn keresztül hibaüzenetet kap bármilyen forrástól vagy célhelytől, amely hosszú üzenetsor létrehozását eredményezheti, ugorjon az [összekötő hibaelhárítási útmutatója](./connector-troubleshoot-guide.md) elemre.
* Ha hibaüzenet jelenik meg a leképezési folyamatról, amely hosszú várólistát tud előállítani, ugorjon az [adatforgalom hibaelhárítási útmutatója](./data-flow-troubleshoot-guide.md) elemre.
* Ha hibaüzenetet kap más tevékenységekről, például a Databricks, az egyéni tevékenységekről vagy a HDI-ről, amely hosszú üzenetsor létrehozását eredményezheti, lépjen a [tevékenység hibaelhárítási útmutatója](./data-factory-troubleshoot-guide.md) elemre.
* Ha a SSIS-csomagok futtatásával kapcsolatos hibaüzenet jelenik meg, amely hosszú várólistát tud előállítani, ugorjon az [Azure-SSIS-csomag végrehajtásával kapcsolatos hibaelhárítási útmutatóra](./ssis-integration-runtime-ssis-activity-faq.md) , és [Integration Runtime felügyeleti hibaelhárítási útmutatót.](./ssis-integration-runtime-management-troubleshoot.md)


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)