---
title: Naplók lekérdezése Azure Automation Start/Stop VMs during off-hours
description: Ez a cikk azt ismerteti, hogyan használható a Azure Monitor a Start/Stop VMs during off-hours által generált naplófájlok lekérdezéséhez.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3e9e924d6626d9f0dcd2db8a5e8b8f90a0aa01ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593853"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Virtuális gépek munkaidőn kívüli indításának és leállításának lekérdezési naplói

Azure Automation két típusú rekordot továbbít a csatolt Log Analytics munkaterületre: a feladatok naplóit és a feladatok folyamait. Ez a cikk a Azure Monitor [lekérdezéséhez](../azure-monitor/logs/log-query-overview.md) rendelkezésre álló adatelemzéseket tekinti át.

## <a name="job-logs"></a>Feladatnaplók

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobLogs.|
|CorrelationId | GUID, amely a runbook-feladatokhoz tartozó korrelációs azonosító.|
|JobId | A runbook-feladatokhoz tartozó GUID azonosító.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job.|
|resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat állapota. Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
|resultDescription | Ismerteti a runbook-feladat eredményállapotát. Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
|RunbookName | Megadja a runbook-feladat nevét.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték a OpsManager|
|StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
|SubscriptionId | Megadja a feladat előfizetési azonosítóját.
|Idő | A runbook-feladat végrehajtásának dátuma és időpontja.|

## <a name="job-streams"></a>Feladatstreamek

|Tulajdonság | Leírás|
|----------|----------|
|Hívó |  A művelet kezdeményezője. Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
|Kategória | Az adattípus besorolása. Az Automation esetében az érték JobStreams.|
|JobId | A runbook-feladatokhoz tartozó GUID azonosító.|
|operationName | Meghatározza az Azure-ban végrehajtott művelet típusát. Az Automation esetében az érték a job.|
|ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
|resourceId | Megadja az erőforrás-azonosítót az Azure-ban. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat. Az Automation esetében az érték Azure Automation.|
|ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát. Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
|resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. Lehetséges érték:<br>- Folyamatban|
|resultDescription | A runbook kimeneti streamjét tartalmazza.|
|RunbookName | A runbook neve.|
|SourceSystem | Megadja az elküldött adatok forrásrendszerét. Az Automation esetében az érték a OpsManager.|
|StreamType | A feladatstream típusa. Lehetséges értékek:<br>– Folyamat<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
|Idő | A runbook-feladat végrehajtásának dátuma és időpontja.|

Ha olyan naplóbeli keresést hajt végre, amely a **JobLogs** vagy a **JobStreams** kategóriába tartozó rekordokat ad vissza, akkor kiválaszthatja a **JobLogs** vagy a **JobStreams** nézetet, amely a keresés által visszaadott frissítések összegzését jeleníti meg.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a Start/Stop VMs during off-hours által összegyűjtött feladatokhoz tartozó rekordokat tartalmazza.

|Lekérdezés | Description|
|----------|----------|
|A sikeresen befejezett runbook ScheduledStartStop_Parent feladatok keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Azon runbook ScheduledStartStop_Parent feladatok keresése, amelyek nem fejeződött be sikeresen | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|A sikeresen befejezett runbook SequencedStartStop_Parent feladatok keresése | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Azon runbook SequencedStartStop_Parent feladatok keresése, amelyek nem fejeződött be sikeresen | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Következő lépések

* A szolgáltatás beállításához tekintse meg a [virtuális gépek leállítása/elindítása munkaidőn kívüli konfigurálását](automation-solution-vm-management-config.md)ismertető témakört.
* A szolgáltatás központi telepítésével kapcsolatos riasztásokról további információt a következő témakörben talál: [log riasztások létrehozása a Azure monitor](../azure-monitor/alerts/alerts-log.md).
* A szolgáltatási hibák megoldásához tekintse meg a [Start/Stop VMS During off-hours problémák elhárítása](troubleshoot/start-stop-vm.md)című témakört.