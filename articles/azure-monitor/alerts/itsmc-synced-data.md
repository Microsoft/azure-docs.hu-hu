---
title: A ITSM-termékről a LA Workspace-ba szinkronizált adatok
description: Ez a cikk áttekintést nyújt a ITSM-termékről a LA Workspace-ba szinkronizált adatokról.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 83994c1b6e150342a777a079d79d6a594d30c3ff
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041603"
---
# <a name="data-synced-from-your-itsm-product"></a>A ITSM-termékről szinkronizált adatok

Az incidensek és a módosítási kérelmek szinkronizálása a ITSM eszközről a Log Analytics munkaterületre a kapcsolódási konfiguráció alapján (az adatok szinkronizálása mező használatával):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Szinkronizált adathalmazok

Ez a szakasz néhány példát mutat be a ITSMC által összegyűjtött adatokra.

A **ServiceDesk_CL** mezői a log Analyticsba importált munkaelem típusától függően változnak. A következő lista felsorolja a két munkaelem típusának mezőit:

**Munkaelem:** **incidensek**  
ServiceDeskWorkItemType_s = "incidens"

**Mezők**

- ServiceDeskConnectionName
- Ügyfélszolgálat azonosítója
- Állam
- Sürgősség
- Hatás
- Prioritás
- Eszkalálás
- Created By (Létrehozó)
- Feloldotta:
- Lezárta
- Forrás
- A jog birtokosa
- Kategória
- Cím
- Leírás
- Létrehozás dátuma
- Lezárás dátuma
- Megoldás dátuma
- Utolsó módosítás dátuma
- Computer

**Munkaelem:** **módosítási kérelmek**

ServiceDeskWorkItemType_s = "módosítási kérelem"

**Mezők**
- ServiceDeskConnectionName
- Ügyfélszolgálat azonosítója
- Created By (Létrehozó)
- Lezárta
- Forrás
- A jog birtokosa
- Cím
- Típus
- Kategória
- Állam
- Eszkalálás
- Ütközési állapot
- Sürgősség
- Prioritás
- Kockázat
- Hatás
- A jog birtokosa
- Létrehozás dátuma
- Lezárás dátuma
- Utolsó módosítás dátuma
- Kért dátum
- Tervezett kezdési dátum
- Tervezett befejezési dátum
- Munka kezdő dátuma
- Munka befejezési dátuma
- Leírás
- Computer

## <a name="servicenow-example"></a>ServiceNow példa 
### <a name="output-data-for-a-servicenow-incident"></a>ServiceNow incidens kimeneti adatkészlete

| Log Analytics mező | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állam |
| Urgency_s |Sürgősség |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Megnyitva |
| ResolvedBy_s | Feloldotta:|
| ClosedBy_s  | Lezárta |
| Source_s| Kapcsolattartó típusa |
| AssignedTo_s | Felelős  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Jegyzetek |
| CreatedDate_t|  Megnyitott |
| ClosedDate_t| lezárva|
| ResolvedDate_t|Feloldva|
| Computer  | Konfigurációelem |

### <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow-módosítási kérelem kimeneti adatkészlete

| Log Analytics | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Kérelmező |
| ClosedBy_s | Lezárta |
| AssignedTo_s | Felelős  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állam|
| Urgency_s|  Sürgősség |
| Priority_s| Prioritás|
| Risk_s| Kockázat|
| Impact_s| Hatás|
| RequestedDate_t  | Kérelem dátuma szerint |
| ClosedDate_t | Lezárt dátum |
| PlannedStartDate_t  | Tervezett kezdési dátum |
| PlannedEndDate_t  | Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátum |
| WorkEndDate_t | Tényleges befejezési dátum|
| Description_s | Leírás |
| Computer  | Konfigurációs tétel |

## <a name="next-steps"></a>Következő lépések

* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)
