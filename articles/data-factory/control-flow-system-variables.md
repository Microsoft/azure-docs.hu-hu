---
title: Rendszerváltozók a Azure Data Factoryban
description: Ez a cikk a Azure Data Factory által támogatott rendszerváltozókat ismerteti. Ezeket a változókat kifejezésekben használhatja Data Factory entitások definiálásához.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: fc6b2e4c944394d811abc19f70aeb34a0ae3c9a4
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127668"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Azure Data Factory által támogatott rendszerváltozók
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory által támogatott rendszerváltozókat ismerteti. Ezeket a változókat kifejezésekben használhatja Data Factory entitások definiálásához.

## <a name="pipeline-scope"></a>Folyamat hatóköre
Ezeket a rendszerváltozókat a folyamat JSON-ban bárhol lehet hivatkozni.

| Változó neve | Leírás |
| --- | --- |
| @pipeline(). DataFactory |Annak az adatelőállítónak a neve, amelyen a folyamat fut |
| @pipeline(). Folyamat |A folyamat neve |
| @pipeline(). RunId |Az adott folyamat futtatásának azonosítója |
| @pipeline(). TriggerType |Az adatfolyamatot meghívó eseményindító típusa (például `ScheduleTrigger` , `BlobEventsTrigger` ). A támogatott eseményindító-típusok listáját lásd: [folyamat-végrehajtás és eseményindítók Azure Data Factoryban](concepts-pipeline-execution-triggers.md). Az trigger típusa `Manual` azt jelzi, hogy a folyamat manuálisan lett aktiválva. |
| @pipeline(). TriggerId|A folyamatot meghívó eseményindító azonosítója |
| @pipeline(). TriggerName|A folyamatot meghívó Eseményindító neve |
| @pipeline(). TriggerTime|Az eseményindító futtatásának ideje, amely meghívja a folyamatot. Ez az az idő, amikor az eseményindító **ténylegesen** elindította a folyamat futását, és az eseményindító ütemezett idejétől némileg eltérő lehet.  |

>[!NOTE]
>Az triggerrel kapcsolatos dátum/idő rendszerváltozók (a folyamat és az aktiválási hatókörök esetében) az UTC dátumokat ISO 8601 formátumban adják vissza, például: `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Trigger hatókörének ütemterve
Ezeket a rendszerváltozókat az eseményindító JSON-ban bárhol lehet hivatkozni [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger)típusú eseményindítók esetén.

| Változó neve | Leírás |
| --- | --- |
| @trigger().scheduledTime |Az az idő, amikor az eseményindító ütemezve lett a folyamat futásának meghívására. |
| @trigger(). kezdő időpont |Az az idő, amikor az eseményindító **ténylegesen** elindította a folyamat futtatását. Ez kis mértékben eltérhet a trigger ütemezett idejétől. |

## <a name="tumbling-window-trigger-scope"></a>Kiesési ablak triggerének hatóköre
Ezeket a rendszerváltozókat az eseményindító JSON-ban bárhol lehet hivatkozni [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger)típusú eseményindítók esetén.

| Változó neve | Leírás |
| --- | --- |
| @trigger(). outputs. windowStartTime |Az trigger futtatásához társított ablak kezdete. |
| @trigger(). outputs. windowEndTime |Az trigger futtatásához társított ablak vége. |
| @trigger().scheduledTime |Az az idő, amikor az eseményindító ütemezve lett a folyamat futásának meghívására. |
| @trigger(). kezdő időpont |Az az idő, amikor az eseményindító **ténylegesen** elindította a folyamat futtatását. Ez kis mértékben eltérhet a trigger ütemezett idejétől. |

## <a name="event-based-trigger-scope"></a>Eseményvezérelt eseményindító hatóköre
Ezeket a rendszerváltozókat az eseményindító JSON-ban bárhol lehet hivatkozni [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger)típusú eseményindítók esetén.

| Változó neve | Leírás |
| --- | --- |
| @triggerBody(). fájlnév  |Annak a fájlnak a neve, amelynek létrehozása vagy törlése miatt a trigger tüzet okozott.   |
| @triggerBody(). mappanév  |A által megadott fájlt tartalmazó mappa elérési útja `@triggerBody().fileName` . A mappa elérési útjának első szegmense az Azure Blob Storage tároló neve.  |
| @trigger(). kezdő időpont |Az az idő, amikor a trigger kezdeményezte a folyamat futtatását. |

## <a name="next-steps"></a>Következő lépések
További információ a változók kifejezésekben való használatáról: [Expression language & functions](control-flow-expression-language-functions.md).
