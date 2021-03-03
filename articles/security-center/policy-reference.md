---
title: A Azure Security Center beépített szabályzat-definíciói
description: A Azure Security Center Azure Policy beépített szabályzat-definícióit sorolja fel. Ezek a beépített szabályzat-definíciók közös megközelítéseket biztosítanak az Azure-erőforrások kezeléséhez.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 3b5860cc4ada88e2e7c7813e3441db3ec89f31af
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729920"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Beépített Azure Policy-definíciók az Azure Security Centerben

Ez az oldal [Azure Policy](../governance/policy/overview.md) Azure Security Centerhoz kapcsolódó beépített szabályzat-definíciók indexe. A következő csoportházirend-definíciók állnak rendelkezésre:

- A [kezdeményezések](#azure-security-center-initiatives) csoport felsorolja a "Security Center" kategóriában Azure Policy kezdeményezési definíciókat.
- Az [alapértelmezett kezdeményezési](#azure-security-center-initiatives) csoport felsorolja a Security Center alapértelmezett kezdeményezésének részét képező összes Azure Policy-definíciót, az [Azure biztonsági teljesítménytesztét](../security/benchmarks/introduction.md). Ez a Microsoft által készített, széles körben elismert teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST) technológiára](https://www.nist.gov/) épül, amelynek középpontjában a felhő-központú biztonság.
- A [Kategória](#azure-security-center-category) csoport felsorolja az összes Azure Policy definíciót a "Security Center" kategóriában.

További információ a biztonsági házirendekről: [a biztonsági házirendek használata](./tutorial-security-policy.md). További Azure Policy a más szolgáltatásokhoz készült beépített [definíciók: Azure Policy beépített definíciók](../governance/policy/samples/built-in-policies.md).

Az egyes beépített szabályzat-definíciók neve a Azure Portal házirend-definíciójának nevére hivatkozik. A **verzió** oszlopban található hivatkozás használatával megtekintheti a forrást a [Azure Policy GitHub](https://github.com/Azure/azure-policy)-tárházban.

## <a name="azure-security-center-initiatives"></a>Azure Security Center kezdeményezések

Az Security Center által figyelt beépített kezdeményezésekről a következő táblázatban talál további információt:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center alapértelmezett kezdeményezése (Azure biztonsági teljesítményteszt)

Ha többet szeretne megtudni a Security Center által figyelt beépített szabályzatokról, tekintse meg a következő táblázatot:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center kategória

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan Azure Policy biztonsági házirend-definíciókat Security Center. További információt a következő cikkekben talál.

- A beépített elemek megtekintése az [Azure Policy GitHub-adattárában](https://github.com/Azure/azure-policy).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../governance/policy/concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../governance/policy/concepts/effects.md).
- [Azure Security Center tervezési és üzemeltetési útmutató](./security-center-planning-and-operations-guide.md): megtudhatja, hogyan tervezhet és megismerheti a tervezési szempontokat Azure Security Centerokban.
- [Biztonsági állapotmonitorozás az Azure Security Centerben](./security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](./security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
- [Partneri megoldások monitorozása az Azure Security Centerrel](./security-center-partner-integration.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Policy](../governance/policy/overview.md): megismerheti az Azure-erőforrások naplózását és szabályozását.
