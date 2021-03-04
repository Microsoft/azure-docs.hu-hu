---
title: Azure Security teljesítményteszt v2 – Asset Management
description: Azure Security teljesítményteszt v2 – eszközkezelés
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbeb88b6aa542666481458fde97d7c63f467fa30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051531"
---
# <a name="security-control-v2-asset-management"></a>Biztonsági vezérlő v2: Asset Management

Az eszközkezelés kiterjed az Azure-erőforrások biztonságának és irányításának biztosítására szolgáló vezérlőkre. Ide tartoznak a biztonsági személyzet engedélyeivel kapcsolatos javaslatok, az eszközök leltározásának biztonsági hozzáférése, valamint a szolgáltatások és erőforrások jóváhagyásának kezelése (leltár, nyomon követés és helyes).

A megfelelő beépített Azure Policy megjelenítéséhez tekintse meg [Az Azure biztonsági teljesítményteszt szabályozási megfelelőségének beépített kezdeményezésének részleteit: hálózati biztonság](../../governance/policy/samples/azure-security-benchmark.md#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Az összetevőket érintő kockázatok biztonsági csapat általi átláthatóságának biztosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Győződjön meg arról, hogy a biztonsági csapatoknak biztonsági olvasói engedélyekkel kell rendelkezniük az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával felügyeljék

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: A számítási feladatok és a szolgáltatások átláthatóvá tételéhez további engedélyek lehetnek szükségesek.

- [A biztonsági olvasó szerepkör áttekintése](../../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../../governance/management-groups/overview.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Győződjön meg arról, hogy a biztonsági csapatok hozzáférhetnek az Azure-beli adategységek folyamatosan frissített leltározásához. A biztonsági csapatoknak gyakran van szükségük erre a leltárra a vállalat új kockázatokkal szembeni kitettségének felméréséhez, és a folyamatos biztonsági fejlesztéséket szolgáló információként. 

A Azure Security Center Inventory szolgáltatás és az Azure Resource Graph az előfizetések összes erőforrását lekérdezheti és felderítheti, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat is.

Az eszközöket logikailag rendezheti a szervezet besorolása alapján a címkék használatával, valamint az Azure-ban található egyéb metaadatokkal (név, leírás és kategória).

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM-7, CM-8 |

Az Azure Policy használatával ellenőrizheti és korlátozhatja a felhasználók által a környezetben kiépíthető szolgáltatások körét. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Az Azure Monitort is használhatja olyan szabályok létrehozásához, amelyek riasztást aktiválnak nem jóváhagyott szolgáltatás észlelésekor.

- [Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../../governance/policy/samples/index.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Hozzon létre vagy frissítsen olyan biztonsági házirendeket, amelyek az eszköz életciklus-kezelési folyamataiban potenciálisan nagy hatású módosításokat végeznek. Ezek a módosítások a következők módosításait tartalmazzák: identitásszolgáltatók és hozzáférés, adatbizalmasság, hálózati konfiguráció, adminisztratív jogosultságok hozzárendelése.

Távolítsa el az Azure-erőforrásokat, ha már nincs rájuk szükség.

- [Azure-erőforráscsoport és -erőforrás törlése](../../azure-resource-manager/management/delete-resource-group.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Az Azure AD feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: csak jóváhagyott alkalmazások használata számítási erőforrásokban

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Győződjön meg arról, hogy csak a hitelesített szoftverek futnak, és az összes jogosulatlan szoftver le van tiltva az Azure Virtual Machines futtatásához.

Az alkalmazások engedélyezési listájának észleléséhez és létrehozásához használjon Azure Security Center (ASC) adaptív alkalmazás-vezérlőket. Az ASC adaptív alkalmazás-vezérlők használatával biztosíthatja, hogy csak a hitelesített szoftverek legyenek végrehajtva, és minden jogosulatlan szoftver le legyen tiltva az Azure Virtual Machines.

A Windows-és Linux-alapú virtuális gépekről származó leltározási adatok gyűjtésének automatizálásához használja Azure Automation Change Tracking és a leltárt. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A szoftver telepítési dátumának és egyéb információinak beszerzéséhez engedélyezze a vendég szintű diagnosztikát, és irányítsa a Windows-eseménynaplókat Log Analytics munkaterületre.

A parancsfájlok típusától függően az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások segítségével korlátozható, hogy a felhasználók képesek legyenek parancsfájlokat végrehajtani az Azure számítási erőforrásokban.

Harmadik féltől származó megoldást is használhat a nem jóváhagyott szoftverek felderítésére és azonosítására.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../../security-center/security-center-adaptive-application.md)

- [A Azure Automation Change Tracking és a leltár ismertetése](../../automation/change-tracking/overview.md)

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
