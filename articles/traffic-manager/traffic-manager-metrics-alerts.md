---
title: Metrikák és riasztások az Azure Traffic Manager
description: Ez a cikk az Azure-ban Traffic Manager elérhető metrikákat és riasztásokat ismerteti.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: b18e0329aeb4e95e021c3326b6b428c10edc0c6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586422"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Metrikák és riasztások Traffic Manager

A Traffic Manager DNS-alapú terheléselosztást biztosít, amely több útválasztási módszert és végpont-figyelési lehetőséget is tartalmaz. Ez a cikk az ügyfelek számára elérhető metrikákat és kapcsolódó riasztásokat ismerteti. 

## <a name="metrics-available-in-traffic-manager"></a>Traffic Manager elérhető metrikák 

Traffic Manager a következő mérőszámokat biztosítja a profilok alapján, hogy az ügyfelek megértsék a Traffic Manager használatát és az adott profilhoz tartozó végpontok állapotát.  

### <a name="queries-by-endpoint-returned"></a>Visszaadott végponti lekérdezések
[Ezzel a metrikával](../azure-monitor/essentials/metrics-supported.md) megtekintheti, hogy hány lekérdezés van egy Traffic Manager-profillal egy adott időszakban. A végpontok részletességi szintjén is megtekintheti ugyanezeket az információkat, amelyek segítségével megtudhatja, hogy a rendszer hányszor adott vissza egy végpontot a Traffic Manager által küldött lekérdezési válaszokban.

Az alábbi példában az 1. ábra az Traffic Manager-profil által visszaadott összes lekérdezési választ megjeleníti. 

  
![Az összes lekérdezés összesített nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*1. ábra: összesítő nézet az összes lekérdezéssel*
  
A 2. ábrán ugyanazokat az adatokat jeleníti meg, azonban a végpontok alapján oszlik meg. Ennek eredményeképpen láthatja a lekérdezési válaszok mennyiségét, amelyben egy adott végpontot adott vissza.

![Traffic Manager mérőszámok – a lekérdezési kötet felosztási nézete végponton](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*2. ábra: osztott nézet a visszaadott végponton megjelenített lekérdezési kötettel*

## <a name="endpoint-status-by-endpoint"></a>Végponti állapot végpont szerint
[Ez a metrika](../azure-monitor/essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) a profilban található végpontok állapotának megismerésére használható. Két értéket vesz igénybe:
 - Ha a végpont fel van használva, használja az **1** .
 - Ha a végpont nem érhető el, használja a **0 értéket** .

Ez a metrika az összes metrika (3. ábra) állapotát jelképező összesített értékként jeleníthető meg, vagy felosztható (lásd a 4. ábrát) az adott végpontok állapotának megjelenítéséhez. Ha a korábbi, ha az összesítési szint az **AVG** értékre van kiválasztva, akkor a metrika értéke az összes végpont állapotának számtani átlaga. Ha például egy profil két végponttal rendelkezik, és csak egy kifogástalan állapotú, akkor ez a metrika **0,50** értékű, ahogy az a 3. ábrán látható. 


![Traffic Manager mérőszámok – a végpont állapotának összetett nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*3. ábra: végponti állapot mérőszámának összetett nézete – "AVG" összesítés kiválasztva*


![Traffic Manager mérőszámok – a végpont állapotának felosztott nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*4. ábra: végponti állapot metrikáinak felosztott nézete*

Ezeket a metrikákat [Azure monitor szolgáltatás](../azure-monitor/essentials/metrics-supported.md)portálján, [Rest APIon](/rest/api/monitor/), az [Azure CLI](/cli/azure/monitor)-n és a [Azure PowerShellon](/powershell/module/az.applicationinsights)keresztül, vagy a Traffic Manager portálján található mérőszámok szakaszán keresztül használhatja fel.

## <a name="alerts-on-traffic-manager-metrics"></a>Traffic Manager metrikákkal kapcsolatos riasztások
A Traffic Manager metrikáinak feldolgozásán és megjelenítésén kívül Azure Monitor lehetővé teszi, hogy az ügyfelek a metrikákkal kapcsolatos riasztásokat konfiguráljanak és fogadnak. Kiválaszthatja, hogy milyen feltételeket kell teljesíteni ezekben a mérőszámokban, hogy riasztást kapjon, milyen gyakran kell figyelni ezeket a feltételeket, és hogy a riasztásokat hogyan kell elküldeni Önnek. További információ: [Azure monitor riasztások dokumentációja](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure monitor szolgáltatásról](../azure-monitor/essentials/metrics-supported.md)
- Megtudhatja, hogyan [hozhat létre diagramot a Azure monitor használatával](../azure-monitor/essentials/metrics-getting-started.md#create-your-first-metric-chart)