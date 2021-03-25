---
title: Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search
description: Felsorolja Azure Policy az Azure Cognitive Search számára elérhető megfelelőségi szabályozásokat. Ezek a beépített szabályzat-definíciók közös megközelítéseket biztosítanak az Azure-erőforrások megfelelőségének kezeléséhez.
ms.date: 03/24/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: af387d44caec333c2a320516d5c5714c803f0caa
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033729"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search

Ha [Azure Policy](../governance/policy/overview.md) használja az [Azure biztonsági teljesítménytesztben](../security/benchmarks/introduction.md)szereplő javaslatok betartatására, akkor valószínűleg már tudja, hogy szabályzatokat hozhat létre a nem megfelelő szolgáltatások azonosításához és kijavításához. Ezek a szabályzatok lehetnek egyéniek, vagy a beépített definíciók alapján, amelyek megfelelőségi feltételeket és megfelelő megoldásokat biztosítanak a jól ismert ajánlott eljárásokhoz.

Az Azure Cognitive Search esetében jelenleg egy beépített definíció található, amely a szabályzat-hozzárendelésben használható. A beépített naplózási és figyelési funkciók. Ha ezt a beépített definíciót használja egy [Ön által létrehozott szabályzatban](../governance/policy/assign-policy-portal.md), a rendszer megvizsgálja a [diagnosztikai naplózást](search-monitor-logs.md)nem tartalmazó keresési szolgáltatásokat, majd ennek megfelelően engedélyezi azt.

A [Azure Policy szabályozási megfelelősége](../governance/policy/concepts/regulatory-compliance.md) biztosítja a Microsoft által létrehozott és felügyelt kezdeményezési _definíciókat, amelyek_ a különböző megfelelőségi szabványokhoz kapcsolódó **megfelelőségi tartományokhoz** és **biztonsági ellenőrzésekhez** használhatók. Ez az oldal az Azure Cognitive Search **megfelelőségi tartományait** és **biztonsági vezérlőit** sorolja fel. A **biztonsági vezérlők** beépített moduljait egyenként is hozzárendelheti ahhoz, hogy az Azure-erőforrások megfeleljenek az adott szabványnak.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Policy szabályozási megfelelőségről](../governance/policy/concepts/regulatory-compliance.md).
- A beépített elemek megtekintése az [Azure Policy GitHub-adattárában](https://github.com/Azure/azure-policy).
