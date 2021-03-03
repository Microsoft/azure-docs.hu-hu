---
title: Tároló-áttekintési régió leképezései
description: Ismerteti a tárolók beolvasása, Log Analytics munkaterület és az egyéni metrikák között támogatott régió-leképezéseket.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728875"
---
# <a name="region-mappings-supported-by-container-insights"></a>A Container-bepillantást támogató régió-hozzárendelések

 A tárolók megállapításának engedélyezésekor csak bizonyos régiók támogatottak egy Log Analytics munkaterület és egy AK-fürt összekapcsolásához, valamint a Azure Monitor számára elküldött egyéni metrikák összegyűjtéséhez.

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics munkaterület által támogatott leképezések

A támogatott AK-régiók a [régiók alapján elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)listáján találhatók. A Log Analytics munkaterületnek ugyanabban a régióban kell lennie, kivéve az alábbi táblázatban felsorolt régiókat. A frissítésekért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .


|**AK-fürt régiója** | **Log Analytics munkaterület-régió** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Ausztrália** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brazília** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
|CanadaEast |CanadaCentral |
|**Európa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**India** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japán** | |
|JapanWest |JapanEast |
|**Dél-Korea** | |
|KoreaSouth |KoreaCentral |
|**USA** | |
|<sup>1</sup> . WestCentralUS|<sup>1</sup> . EastUS|


<sup>1</sup> a kapacitás korlátozásai miatt a régió nem érhető el új erőforrások létrehozásakor. Ez Log Analytics munkaterületet tartalmaz. A régióban azonban a már meglévő társított erőforrások továbbra is működőképesek maradnak.

## <a name="custom-metrics-supported-regions"></a>Egyéni metrikák támogatott régiói

Az Azure Kubernetes Services (ak) fürtök csomópontjaiból és hüvelyből származó metrikák összegyűjtése csak az alábbi [Azure-régiókban](../essentials/metrics-custom-overview.md#supported-regions)támogatott egyéni metrikák való közzétételre.

## <a name="next-steps"></a>Következő lépések

Az AK-fürt figyelésének megkezdéséhez tekintse át, [hogyan engedélyezheti a tároló-bepillantást](container-insights-onboard.md) a követelmények és az elérhető módszerek megismeréséhez a figyelés engedélyezéséhez.  
