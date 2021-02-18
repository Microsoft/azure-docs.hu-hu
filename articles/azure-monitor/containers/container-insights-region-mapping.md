---
title: Azure Monitor a tárolók régiójának leképezéséhez
description: A Azure Monitor a tárolók, a Log Analytics munkaterület és az egyéni metrikák között támogatott régió-hozzárendeléseket ismerteti.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9b77242de3e7845a97874b663266103bf00a8e66
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619418"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>A tárolók Azure Monitor által támogatott régió-hozzárendelések

 A tárolók Azure Monitorának engedélyezésekor csak bizonyos régiók támogatottak egy Log Analytics-munkaterület és egy AK-fürt összekapcsolásához, valamint az Azure Monitor elküldött egyéni metrikák összegyűjtéséhez.

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

Az AK-fürt figyelésének megkezdéséhez tekintse át az [Azure monitor a tárolók számára című témakört](container-insights-onboard.md) , és Ismerje meg a figyelés engedélyezésének követelményeit és rendelkezésre álló módszereit.  
