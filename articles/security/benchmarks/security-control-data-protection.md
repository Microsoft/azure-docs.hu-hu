---
title: Azure Security Control – adatvédelem
description: Azure biztonsági vezérlés – adatvédelem
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d36ed6a795c5fa2241ee71751053a4cb5986aaf0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576830"
---
# <a name="security-control-data-protection"></a>Biztonság-ellenőrzés: Adatvédelem

Az adatvédelmi javaslatok a titkosítással, a hozzáférés-vezérlési listával, az identitás-alapú hozzáférés-vezérléssel és az adathozzáférés naplózási naplózásával kapcsolatos problémák kezelésére összpontosítanak.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.1 | 13,1 | Ügyfél |

A címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.2 | 13,2, 2,10 | Ügyfél |

Különálló előfizetések és felügyeleti csoportok használatával különítse el az elkülönítést az egyes biztonsági tartományok, például a környezeti típus és az adatérzékenység szintjén. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével szabályozhatja. 

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.3 | 13,3 | Megosztott |

Egy külső gyártótól származó megoldást használhat fel az Azure Marketplace-ről olyan hálózati területekre, amelyek figyelik a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küldenek az informatikai szakemberek számára.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.4 | 14,4 | Megosztott |

Titkosítsa az összes bizalmas információt az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [A titkosítás ismertetése az Azure-ban](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4,5 | 14,5 | Megosztott |

Ha az Azure-ban egyetlen szolgáltatás sem érhető el, egy külső gyártótól származó aktív felderítési eszköz használatával azonosíthatja a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információt, beleértve a helyszínen vagy egy távoli szolgáltatón keresztül, valamint a szervezet bizalmas információinak leltározását.

A bizalmas adatok Microsoft 365 dokumentumokon belüli azonosításához használja a Azure Information Protection.

Az Azure SQL Information Protection használatával segítheti a Azure SQL Database tárolt információk besorolását és címkézését.

- [Az Azure SQL-adatfelderítés implementálása](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [A Azure Information Protection megvalósítása](/azure/information-protection/deployment-roadmap)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4,6 | 14,6 | Ügyfél |

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja az adat-és erőforrásokhoz való hozzáférést, máskülönben a szolgáltatás-specifikus hozzáférés-vezérlési módszereket is használhatja.

- [Az Azure RBAC konfigurálása](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.7 | 14,7 | Megosztott |

Ha a megfelelőség szükséges a számítási erőforrásokon, hozzon létre egy harmadik féltől származó eszközt, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldást, hogy az adatokra akkor is kényszerítse a hozzáférés-vezérlést, ha az adatok egy rendszerből lettek átmásolva.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.8 | 14,8 | Ügyfél |

Az összes Azure-erőforráson használja a titkosítást. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban lehetőség van arra, hogy a saját kulcsait bizonyos példányokban kezelje. 

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../fundamentals/encryption-atrest.md)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.9 | 14,9 | Ügyfél |

A Azure Monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amikor a módosítások a kritikus Azure-erőforrásokra vonatkoznak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../../azure-monitor/alerts/alerts-activity-log.md)


## <a name="next-steps"></a>Következő lépések

- Tekintse meg a következő biztonsági szabályozást:  [sebezhetőségek kezelése](security-control-vulnerability-management.md)