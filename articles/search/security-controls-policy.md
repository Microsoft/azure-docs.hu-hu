---
title: Azure Policy megfelelőségi vezérlők a Azure Cognitive Search
description: A Azure Policy megfelelőségi vezérlőket felsoroló Azure Cognitive Search. Ezek a beépített szabályzatdefiníciók gyakori megközelítéseket biztosítanak az Azure-erőforrások megfelelőségének kezeléséhez.
ms.date: 04/14/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 807201d5626bcdbc421d182f06b888c5775dcc48
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497412"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy megfelelőségi vezérlők a Azure Cognitive Search

Ha a [](../governance/policy/overview.md) Azure Policy kényszeríti a javaslatokat az [Azure-biztonsági](../security/benchmarks/introduction.md)teljesítménytesztben, akkor valószínűleg tudja, hogy létrehozhat szabályzatokat a nem megfelelő szolgáltatások azonosításához és kijavítása érdekében. Ezek a szabályzatok lehetnek egyéniek, vagy olyan beépített definíciókon alapulnak, amelyek megfelelőségi feltételeket és megfelelő megoldásokat biztosítanak a jól ismert ajánlott eljárásokhoz.

A Azure Cognitive Search az alábbiakban egy beépített definíció található, amely használható szabályzat-hozzárendelésben. A beépített naplózási és monitorozási szolgáltatás. Ha ezt [a](../governance/policy/assign-policy-portal.md)beépített definíciót használja egy ön által létrehozott szabályzatban, a rendszer megkeresi azokat a keresési szolgáltatásokat, amelyek nem futtatnak diagnosztikai [naplózást,](search-monitor-logs.md)majd ennek megfelelően engedélyezi azt.

[A megfelelőségi Azure Policy](../governance/policy/concepts/regulatory-compliance.md) a Microsoft által létrehozott és felügyelt kezdeményezési definíciókat (más néven  beépítetteket) biztosít a különböző megfelelőségi szabványokhoz kapcsolódó megfelelőségi tartományokhoz és biztonsági vezérlőkhöz.  Ezen az oldalon a megfelelőségi **tartományok** és a biztonsági **vezérlők** Azure Cognitive Search. A beépített biztonsági vezérlőket egyenként  is hozzárendelheti, hogy az Azure-erőforrások megfeleljenek az adott szabványnak.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Következő lépések

- További információ a jogszabályi [Azure Policy való megfelelésről.](../governance/policy/concepts/regulatory-compliance.md)
- A beépített elemek megtekintése az [Azure Policy GitHub-adattárában](https://github.com/Azure/azure-policy).
