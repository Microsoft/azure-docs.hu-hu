---
title: Azure-beli biztonsági alapterv Azure Resource Graph-hoz
description: Az Azure Resource Graph biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad8968fdb6548da29a031f0e44bd3671f67b5553
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557717"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Azure-beli biztonsági alapterv Azure Resource Graph-hoz

Ez a biztonsági alapterv az [Azure Security Benchmark 1,0-es verziójáról](../../../security/benchmarks/overview-v1.md) az Azure Resource Graph-ra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure Resource Graph-ra vonatkozó kapcsolódó útmutatás. Az Azure Resource Graph-ra nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogy az Azure Resource Graph hogyan térképezi fel teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Resource Graph biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../../../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure Resource Graph az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján biztosít hozzáférést az erőforrás-típusokhoz és a tulajdonságokhoz. Rendszeresen naplózza és áttekintheti a rendszerbiztonsági tag (felhasználók, csoportok és szolgáltatásfiókok) számára biztosított hozzáférést, így biztosítva, hogy a lekérdezések a megfelelő erőforrások eredményét adják vissza.

- [Engedélyek az Azure Resource Graphban](../overview.md#permissions-in-azure-resource-graph)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC 

**Útmutató**: az Azure RBAC használatával szabályozhatja az adathozzáférést és az erőforrásokat. Az Azure Resource Graph használatához megfelelő hozzáféréssel kell rendelkeznie a lekérdezni kívánt erőforrásokhoz is. Ennek a hozzáférésnek csak olvashatónak kell lennie, és csak a szükséges személyzet számára adható meg.

- [Engedélyek az Azure Resource Graphban](../overview.md#permissions-in-azure-resource-graph)

- [Az Azure RBAC konfigurálása](../../../role-based-access-control/role-assignments-rest.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../../../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetések, a felügyeleti csoportok és a bérlők által támogatott összes erőforrást. Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel a bérlőben, és képes az összes Azure-előfizetés, valamint az előfizetésében lévő erőforrások számbavételére.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../first-query-portal.md)

- [Az Azure RBAC ismertetése](../../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően. Az Azure Resource Graph segítségével lekérdezheti a jóváhagyott Azure-erőforrásokat, és megtekintheti az előzményeket (előzetes verzió) a pillanatképek áttekintéséhez és a változásokról.

- [Azure-erőforrások lekérdezése az Azure Resource Graphtal](../first-query-portal.md)

- [Erőforrás-módosítások lekérése](../how-to/get-resource-changes.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetések, a felügyeleti csoportok és a bérlők erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Azure-erőforrások lekérdezése az Azure Resource Graphtal](../first-query-portal.md)

- [Minták: az Azure Resource Graph kezdő lekérdezései](../samples/starter.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../../../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../../../security/benchmarks/security-baselines-overview.md)