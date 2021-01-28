---
title: A folyamat-összehangolás és az eseményindítók hibáinak megoldása Azure Data Factory
description: Különböző módszerekkel végezheti el a folyamat-triggerekkel kapcsolatos hibák elhárítását Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 1a5f665627da1b08ec57b04863a58f227c673af4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944901"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>A folyamat-összehangolás és az eseményindítók hibáinak megoldása Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A folyamatfuttatás egy folyamat-végrehajtás egy példányát határozza meg az Azure Data Factoryben. Tegyük fel például, hogy rendelkezik egy olyan folyamattal, amely a következő időpontban fut: 8:00, 9:00 AM és 10:00. Ebben az esetben három külön folyamat fut. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. A futtatási azonosító egy globálisan egyedi azonosító (GUID), amely meghatározza, hogy az adott folyamat fusson.

A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatban meghatározott paraméterekhez történő továbbításával történik. A folyamatokat manuálisan vagy trigger használatával is futtathatja. Részletekért lásd: [folyamat-végrehajtás és eseményindítók Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Gyakori problémák, okok és megoldások

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Egy Azure Functions alkalmazási folyamat hibát jelez a privát végponti kapcsolattal
 
Egy privát végponton Data Factory és egy Azure Function-alkalmazás fut. Olyan folyamatot próbál futtatni, amely együttműködik a Function alkalmazással. Három különböző metódust próbált meg használni, de az egyik a "hibás kérelem" hibaüzenetet adja vissza, a másik két metódus pedig "103 hibát tiltott".

**OK**: a Data Factory jelenleg nem támogatja a Function apps szolgáltatáshoz tartozó privát végpont-összekötőt. Azure Functions elutasítja a hívásokat, mert úgy van konfigurálva, hogy csak privát kapcsolaton keresztül engedélyezze a kapcsolatokat.

**Megoldás**: hozzon létre egy **PrivateLinkService** -végpontot, és adja meg a függvény alkalmazásának DNS-ét.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>A folyamat futása megszakadt, de a figyelő továbbra is megjeleníti a folyamat állapotát.

A folyamat futásának megszakításakor a folyamat figyelése gyakran továbbra is a folyamat állapotát mutatja. Ez egy böngésző gyorsítótárának hibája miatt fordul elő. Előfordulhat, hogy nem rendelkezik a megfelelő figyelési szűrőkkel.

**Megoldás**: frissítse a böngészőt, és alkalmazza a megfelelő figyelési szűrőket.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>A folyamat másolásakor "DelimitedTextMoreColumnsThanDefined" hibaüzenet jelenik meg
 
Ha a másolt mappa különböző sémákkal rendelkező fájlokat tartalmaz, például az oszlopok változó számát, a különböző elválasztó karaktereket, az idézőjelek karakteres beállításait vagy valamilyen adatproblémát, akkor a Data Factory folyamat ezt a hibát okozhatja:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Megoldás**: válassza a **bináris másolás** lehetőséget a másolási tevékenység létrehozásakor. Így az adatok tömeges másolása vagy áttelepítése az egyik adattóból egy másikba, Data Factory nem fogja megnyitni a fájlokat a séma olvasásához. Ehelyett Data Factory az egyes fájlokat bináris fájlként fogja kezelni, és a másik helyre másolja.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>Egy folyamat futtatása meghiúsul, ha eléri az integrációs modul kapacitásának korlátját

Hibaüzenet:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**OK**: elérte az Integration Runtime kapacitási korlátját. Lehet, hogy nagy mennyiségű adatfolyamot futtat ugyanazon integrációs modul egyidejű használatával. Részletekért lásd az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Megoldás**:
 
- A folyamatokat különböző indító időpontokban futtathatja.
- Hozzon létre egy új integrációs modult, és ossza szét a folyamatokat több integrációs modul között.

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>Tevékenység-szintű hibák és hibák vannak a folyamatokban

Azure Data Factory az előkészítés lehetővé teszi a feltételes logikát, és lehetővé teszi, hogy a felhasználók a korábbi tevékenységek eredményétől függően különböző útvonalakat használjanak. Négy feltételes elérési utat tesz lehetővé: **sikeres** (alapértelmezett pass), **hiba** esetén, **befejezéskor** és **kihagyás után**. 

Azure Data Factory kiértékeli az összes levél szintű tevékenység eredményét. A folyamat eredményei csak akkor lesznek sikeresek, ha az összes művelet sikeresen befejeződött. Ha a levél tevékenység ki lett hagyva, a fölérendelt tevékenységet kiértékeljük. 

**Resolution** (Osztás)

1. Tevékenység szintű ellenőrzések végrehajtása a [folyamat hibáinak és hibáinak kezelésével](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
1. A Azure Logic Apps használatával rendszeres időközönként figyelheti a folyamatokat a [gyár lekérdezését](/rest/api/datafactory/pipelineruns/querybyfactory)követően.

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>Folyamat-meghibásodások figyelése rendszeres időközönként

Előfordulhat, hogy a sikertelen Data Factory folyamatokat nem kell figyelnie, például 5 percet. A folyamatot a végpont használatával kérdezheti le és szűrheti a folyamat futtatását egy adatok gyárában. 

Állítson be egy Azure Logic app-alkalmazást, amely 5 percenként lekérdezi az összes meghiúsult folyamatot a következő témakörben leírtak szerint: [query by Factory](/rest/api/datafactory/pipelineruns/querybyfactory). Ezt követően az incidenseket bejelenthetjük a Ticketing rendszerbe.

További információ: [értesítések küldése Data Factoryről, 2. rész](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/).

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)