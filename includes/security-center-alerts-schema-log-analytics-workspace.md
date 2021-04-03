---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "79538830"
---
### <a name="the-data-model-of-the-schema"></a>A séma adatmodellje

|Mező|Leírás|
|----|----|
|**AlertName**|Riasztás megjelenítendő neve|
|**AlertType**|egyedi riasztás azonosítója|
|**ConfidenceLevel**|Választható A riasztás megbízhatósági szintje (magas/alacsony)|
|**ConfidenceScore**|Választható A biztonsági riasztás numerikus megbízhatósági mutatója|
|**Leírás**|A riasztás leírásának szövege|
|**Megjelenítendő név**|A riasztás megjelenítendő neve|
|**EndTime**|A riasztás befejezési időpontja (a riasztás utolsó eseményének időpontja)|
|**Entitások**|A riasztáshoz kapcsolódó entitások listája. Ez a lista különböző típusú entitások keverékét képes tárolni|
|**ExtendedLinks**|Választható A riasztással kapcsolatos összes hivatkozáshoz tartozó táska. Ez a táska különböző típusú hivatkozások tárolására képes|
|**Extendedproperties példányt paraméterként**|A riasztáshoz kapcsolódó további mezők|
|**IsIncident**|Meghatározza, hogy a riasztás incidens vagy rendszeres riasztás-e. Az incidens olyan biztonsági riasztás, amely több riasztást összesít egy biztonsági incidensbe|
|**ProcessingEndTime**|A riasztás létrehozásának UTC-időbélyege|
|**ProductComponentName**|Választható A riasztást létrehozó terméken belüli összetevő neve.|
|**ProductName**|állandó ("Azure Security Center")|
|**ProviderName**|nem használt|
|**RemediationSteps**|A biztonsági fenyegetés kijavításához szükséges kézi műveleti elemek|
|**ResourceId**|Az érintett erőforrás teljes azonosítója|
|**Súlyosság**|A riasztás súlyossága (magas/közepes/alacsony/tájékoztató)|
|**SourceComputerId**|az érintett kiszolgáló egyedi GUID azonosítója (ha a riasztás a kiszolgálón jön létre)|
|**SourceSystem**|nem használt|
|**StartTime**|A riasztás indítási időpontja (az első eseménynek a riasztáshoz való hozzájárulásának időpontja)|
|**SystemAlertId**|A biztonsági riasztási példány egyedi azonosítója|
|**TenantId**|annak az előfizetésnek a szülő Azure Active Directory-bérlője, amely alatt a beolvasott erőforrás található|
|**TimeGenerated**|UTC-időbélyeg, amelyen az értékelés lezajlott (Security Center vizsgálat időpontja) (azonos a DiscoveredTimeUTC)|
|**Típus**|állandó ("SecurityAlert")|
|**Szállítónév**|A riasztást szolgáltató szállító neve (például "Microsoft")|
|**VendorOriginalId**|nem használt|
|**WorkspaceResourceGroup**|abban az esetben, ha a riasztás egy olyan virtuális gépen, kiszolgálón, virtuálisgép-méretezési csoporton vagy App Service-példányon jön létre, amely egy munkaterületre jelentést küld, tartalmazza a munkaterület erőforráscsoport-nevét.|
|**WorkspaceSubscriptionId**|abban az esetben, ha a riasztást egy olyan virtuális gépen, kiszolgálón, virtuálisgép-méretezési csoporton vagy App Service-példányon hozza létre, amely egy munkaterületre mutat, az a munkaterület subscriptionId tartalmazza.|
|||
