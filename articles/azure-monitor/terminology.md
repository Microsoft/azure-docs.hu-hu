---
title: Azure Monitor terminológiai frissítések | Microsoft Docs
description: Ismerteti az Azure monitoring Services közelmúltbeli terminológiai módosításait.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 99592544d97793f82db36e1d2cb33ba8c6af1c9c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586957"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor elnevezési és terminológiai változások
Jelentős változások történtek a közelmúltban, és az Azure-ügyfelek figyelésének egyszerűsítése érdekében a különböző szolgáltatások összevonása is megtörténik a Azure Monitor. Ez a cikk a Azure Monitor dokumentációjának közelmúltbeli nevét és terminológiájának változásait ismerteti.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Október 2019 – diagnosztikai napló az erőforrás-naplóhoz
A "diagnosztikai naplók" módosultak az "erőforrás-naplók" értékre, hogy jobban illeszkedjenek a ténylegesen gyűjtött adatokhoz. A "diagnosztikai beállítások" kifejezés változatlan marad.  

## <a name="february-2019---log-analytics-terminology"></a>Február 2019 – Log Analytics terminológia
A Azure Monitor alatti különböző szolgáltatások összevonása után a következő lépés a dokumentáció terminológiájának módosítása a Azure Monitor szolgáltatás és annak különböző összetevőinek jobb leírásához. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor naplózási adatokat a rendszer továbbra is egy Log Analytics munkaterületen tárolja, és ugyanazokat a Log Analytics szolgáltatást gyűjti és elemzi, de a _log Analytics_ számos helyen módosítjuk _Azure monitor naplókra_. Ez a kifejezés jobban tükrözi a Azure Monitorban betöltött szerepkörét, és jobb következetességet biztosít a [Azure monitor metrikáinak](essentials/data-platform-metrics.md).

A _log Analytics_ kifejezés mostantól elsősorban a lekérdezés írásához és futtatásához, illetve a naplófájlok elemzéséhez használt Azure Portal oldalára vonatkozik. Ez a [metrikák Explorer](essentials/metrics-charts.md)funkcionális megfelelője, amely a metrikai adatok elemzéséhez használt Azure Portal lapja.

### <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek
Azok a [munkaterületek](logs/manage-access.md) , amelyeken a Azure monitor naplózási adatként vannak, log Analytics munkaterületeknek is nevezik. A Azure Portal **log Analytics** menüje át lett nevezve **log Analytics munkaterületekre** , és [új munkaterületeket hoz létre](logs/quick-create-workspace.md) , és konfigurálja az adatforrásokat. Elemezze a naplókat és az egyéb figyelési adatait **Azure monitor** és konfigurálja a munkaterületet **log Analytics munkaterületeken**.

### <a name="management-solutions"></a>Felügyeleti megoldások
Felügyeleti [megoldások](insights/solutions.md) lettek átnevezve a _figyelési megoldásokra_, ami jobban leírja a funkcióit.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Augusztus 2018 – a figyelési szolgáltatások összevonása a Azure Monitorba
A Log Analytics és Application Insights konszolidálva lettek a Azure Monitorba, így egyetlen integrált felhasználói élményt biztosítanak az Azure-erőforrások és a hibrid környezetek figyeléséhez. Ezekből a szolgáltatásokból semmilyen funkció nem lett eltávolítva, és a felhasználók ugyanazokat a forgatókönyveket hajthatják végre, amelyeket minden szolgáltatás elvesztése vagy sérülése nélkül végeztek el.

Az egyes szolgáltatások dokumentációját egyetlen, a Azure Monitorhoz tartozó tartalomba vontuk össze. Ez segíti az olvasót abban, hogy egy adott figyelési forgatókönyv összes tartalmát megkeresse egyetlen helyen, és ne kelljen több tartalomra hivatkozni. Az összevont szolgáltatás fejlődése során a tartalom integráltabb lesz.

Az Log Analytics, például az ügynökök és a nézetek részét képező egyéb szolgáltatások is Azure Monitor-funkciókként lettek áthelyezve. A funkcióik nem változtak meg a Azure Portalban rejlő tapasztalataik alapján.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Április 2018 – az Operations Management Suite-márka kivonása
Az Operations Management Suite (OMS) a következő Azure felügyeleti szolgáltatások árukapcsolását adta licencelési célokra:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Ezekhez a szolgáltatásokhoz új díjszabás lett bevezetve](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), és az OMS árukapcsolás már nem érhető el az új ügyfelek számára. A OMS részét képező szolgáltatások egyike sem módosult, a fent ismertetett Azure Monitori konszolidáció kivételével. 




## <a name="next-steps"></a>Következő lépések

- Tekintse át a különböző összetevőkkel és szolgáltatásokkal [kapcsolatos Azure monitor áttekintése című](overview.md) témakört.
- Ismerje meg a [OMS-portál áttérését](./logs/oms-portal-transition.md).
