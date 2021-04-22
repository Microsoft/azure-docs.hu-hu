---
title: Azure Policy megfelelőségi vezérlők a Azure Cognitive Search
description: Felsorolja Azure Policy megfelelőségi vezérlőket, amelyek a Azure Cognitive Search. Ezek a beépített szabályzatdefiníciók gyakori megközelítéseket biztosítanak az Azure-erőforrások megfelelőségének kezeléséhez.
ms.date: 04/21/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: a8f452703826a68fde8c1b931ab8be7cbeeca6f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861938"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy megfelelőségi vezérlők a Azure Cognitive Search

Ha a [](../governance/policy/overview.md) Azure Policy kényszeríti a javaslatokat az [Azure-biztonsági](../security/benchmarks/introduction.md)teljesítménytesztben, akkor valószínűleg már tudja, hogy létrehozhat szabályzatokat a nem megfelelő szolgáltatások azonosításához és kijavítása érdekében. Ezek a szabályzatok lehetnek egyéniek, vagy olyan beépített definíciókon alapulnak, amelyek megfelelőségi feltételeket és megfelelő megoldásokat biztosítanak a jól ismert ajánlott eljárásokhoz.

A Azure Cognitive Search jelenleg egy, az alábbiakban felsorolt beépített definíciót használhat egy szabályzat-hozzárendelésben. A beépített naplózási és monitorozási szolgáltatás. Ha ezt [a](../governance/policy/assign-policy-portal.md)beépített definíciót használja egy Ön által létrehozott szabályzatban, a rendszer megkeresi azokat a keresési szolgáltatásokat, amelyek nem futtatnak diagnosztikai naplózást, [](search-monitor-logs.md)majd ennek megfelelően engedélyezi azt.

[A megfelelőségi Azure Policy](../governance/policy/concepts/regulatory-compliance.md) a Microsoft által létrehozott és felügyelt kezdeményezési definíciókat (más néven  beépítetteket) biztosít a különböző megfelelőségi szabványokhoz kapcsolódó megfelelőségi tartományokhoz és biztonsági vezérlőkhöz.  Ezen az oldalon a felhasználók **megfelelőségi tartománya** **és** biztonsági vezérlői Azure Cognitive Search. A beépített biztonsági vezérlőket egyenként  is hozzárendelheti, hogy az Azure-erőforrások megfeleljenek az adott szabványnak.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Következő lépések

- További információ a [jogszabályi Azure Policy való megfelelésről.](../governance/policy/concepts/regulatory-compliance.md)
- A beépített elemek megtekintése az [Azure Policy GitHub-adattárában](https://github.com/Azure/azure-policy).
