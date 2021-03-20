---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91401085"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

|Mező|Adattípus|Leírás|
|----|----|----|
|**AlertDisplayName**|Sztring|A riasztás megjelenítendő neve.|
|**AlertType**|Sztring|A riasztás típusa. Az azonos típusú riasztásoknak ugyanazzal az értékkel kell rendelkezniük. Ez a mező egy olyan karakterlánc, amely a riasztás típusát jelöli, nem pedig a riasztási példányokat. Az azonos észlelési logikában/analitikusban található összes riasztási példánynak ugyanazzal az értékkel kell rendelkeznie a riasztás típusához.|
|**CompromisedEntity**|Sztring|A riasztáshoz leginkább kapcsolódó erőforrás megjelenítendő neve.|
|**Leírás**|Sztring|A riasztás leírása.|
|**EndTimeUtc**|DateTime|A riasztásban szereplő utolsó esemény vagy tevékenység időpontja.  A mezőnek olyan karakterláncnak kell lennie, amely megfelel a ISO8601 formátumának, beleértve az UTC időzónára vonatkozó információkat is.|
|**Entitások**|IEnumerable (IEntity)|A riasztáshoz kapcsolódó entitások listája. Ez a lista különböző típusú entitások keverékét képes tárolni. Az entitások típusa az entitások szakaszban meghatározott típusok bármelyike lehet. Az alábbi listán nem szereplő entitásokat is el lehet juttatni, azonban nem garantált, hogy a rendszer feldolgozza őket (a riasztás nem fog meghiúsulni az új típusú entitások érvényesítésével).|
|**Extendedproperties példányt paraméterként**|Szótár (karakterlánc, karakterlánc)|Előfordulhat, hogy a szolgáltatók (opcionálisan) egyéni mezőket tartalmaznak.|
|**Szándék**|Enumeráció|A riasztás mögötti, a kill lánchoz kapcsolódó szándék. A támogatott értékek listáját, valamint a Azure Security Center támogatott kill Chain-leképezésének magyarázatait lásd: [szándékok](../articles/security-center/alerts-reference.md#intentions).<br/>Ennek a mezőnek több értéke is lehet (vesszővel elválasztva).|
|**IsIncident**|Logikai|Ez a mező határozza meg, hogy a riasztás incidens-e (több riasztás összetett csoportosítása) vagy egyetlen riasztás. A mező alapértelmezett értéke "false" (azaz egyetlen riasztás).|
|**ProcessingEndTime**|DateTime|Az az idő, ameddig a riasztás elérhető volt a végfelhasználónak a riasztást birtokló eredeti termékben.|
|**ProductName**|Sztring|A riasztást közzétevő termék neve (Azure Security Center, Azure ATP, Microsoft Defender ATP, MCAS stb.).|
|**RemediationSteps**|Lista<String>|A riasztás kijavításához szükséges kézi műveleti elemek.|
|**ResourceIdentifiers**|Lista (erőforrás-azonosítók)|A riasztáshoz tartozó erőforrás-azonosítók, amelyek segítségével a riasztást a megfelelő termék-expozíciós csoportba (bérlő, munkaterület, előfizetés stb.) lehet irányítani. A riasztások több különböző típusú azonosítóval is rendelkezhetnek.|
|**Súlyosság**|Enumeráció|A szolgáltató által jelentett riasztás súlyossága. Lehetséges értékek: tájékoztató, alacsony, közepes és magas.|
|**StartTimeUtc**|DateTime|A riasztásban szereplő első esemény vagy tevékenység időpontja. A mezőnek olyan karakterláncnak kell lennie, amely megfelel a ISO8601 formátumának, beleértve az UTC időzónára vonatkozó információkat is.|
|**Állapot**|Enumeráció|A riasztás életciklusának állapota.<br/>A támogatott állapotok a következők: új, megoldott, elutasított, ismeretlen.<br/>A támogatott beállításoktól eltérő értéket megadó riasztás a "Unknown" állapothoz van rendelve.<br/>Egy olyan riasztás, amely nem ad meg értéket, a rendszer az "új" állapotot rendeli hozzá.|
|**SystemAlertId**|Sztring|A riasztás azonosítója.|
|**TimeGenerated**|DateTime|A riasztási szolgáltató által generált riasztás időpontja. Ha a belső riasztási szolgáltatók nem jelentettek jelentést, a termék kiválaszthatja, hogy a termék általi feldolgozáshoz milyen időt vett igénybe.  A mezőnek olyan karakterláncnak kell lennie, amely megfelel a ISO8601 formátumának, beleértve az UTC időzónára vonatkozó információkat is.|
|**Szállítónév**|Sztring|A riasztást kiváltó szállító neve.|
|||
