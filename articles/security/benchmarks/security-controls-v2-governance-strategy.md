---
title: Azure Security teljesítményteszt v2 – kormányzás és stratégia
description: Azure Security teljesítményteszt v2 – irányítás és stratégia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ae0930e0845e8e8bd6dc4571dc3e8e27491a7be6
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408792"
---
# <a name="security-control-v2-governance-and-strategy"></a>Security Control v2: irányítási és stratégiai

A szabályozás és a stratégia útmutatást nyújt egy koherens biztonsági stratégia és dokumentált irányítási megközelítés biztosításához a biztonsági garanciák kialakításához és fenntartásához, beleértve a szerepkörök és felelősségek létrehozását a Felhőbeli biztonsági funkciók, az egységes műszaki stratégia és a támogatási szabályzatok és szabványok tekintetében.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: eszközkezelés és adatvédelmi stratégia meghatározása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Gondoskodjon róla, hogy a rendszerek és az információk folyamatos monitorozásával és védelmével kapcsolatos egyértelmű stratégiát dokumentáljon és tájékoztasson. Az üzleti szempontból kritikus fontosságú adatmennyiségek és rendszerek felderítésének, értékelésének, védelmének és figyelésének rangsorolása. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- Az adatbesorolási szabvány az üzleti kockázatoknak megfelelően

- A biztonsági szervezet betekintést nyerhet a kockázatokra és az eszközök leltározására 

- Az Azure-szolgáltatások használatának biztonsági szervezet általi jóváhagyása 

- Az eszközök biztonsága életcikluson keresztül

- A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

- Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

- Adattitkosítási követelmények átvitel közbeni és nyugalmi használati esetekben

- Megfelelő titkosítási szabványok

További információkért tekintse meg a következő referenciákat:
- [Azure biztonsági architektúra – javaslat – tárolás, adatkezelés és titkosítás](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%252fsecurity%252fcompass%252fbreadcrumb%252ftoc.json&toc=%252fsecurity%252fcompass%252ftoc.json)

- [Az Azure biztonsági alapjai – Azure-adatbiztonság, titkosítás és tárolás](../fundamentals/encryption-overview.md)

- [Felhőalapú bevezetési keretrendszer – az Azure adatbiztonsági és-titkosítási ajánlott eljárásai](../fundamentals/data-encryption-best-practices.md?amp;bc=%252fazure%252fcloud-adoption-framework%252f_bread%252ftoc.json&toc=%252fazure%252fcloud-adoption-framework%252ftoc.json)

- [Azure biztonsági teljesítményteszt – eszközkezelés](security-controls-v2-asset-management.md)

- [Azure biztonsági teljesítményteszt – adatvédelem](security-controls-v2-data-protection.md)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: vállalati szegmentálási stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Hozzon létre egy nagyvállalati stratégiát, amely az eszközök hozzáférésének szegmentálására használatos az identitás, a hálózat, az alkalmazás, az előfizetés, a felügyeleti csoport és más vezérlők kombinációjával.

Körültekintően kell elkülöníteni a biztonsági elkülönítés szükségességét annak érdekében, hogy lehetővé váljon a rendszerek napi működésének engedélyezése, amelyeknek kommunikálnia kell egymással, és az adathozzáférésre van szükségük.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás-és hozzáférési modelleket, valamint az alkalmazás engedély/hozzáférési modelljeit, valamint az emberi folyamatok vezérlés

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás igazítása nagyvállalati szegmentálási stratégiával](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: a biztonsági testhelyzet kezelési stratégiájának meghatározása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Folyamatosan méri és csökkenti a kockázatokat az egyes eszközeivel és a bennük üzemeltetett környezettel kapcsolatban. Rangsorolja a nagy értékű eszközöket és a nagy teljesítményű támadási felületeket, például a közzétett alkalmazásokat, a hálózati bejövő és kimenő pontokat, a felhasználói és rendszergazdai végpontokat stb.

- [Azure biztonsági teljesítményteszt – a testtartás és a sebezhetőségek kezelése](security-controls-v2-posture-vulnerability-management.md)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: szervezeti szerepkörök, felelősségek és elszámoltathatóság igazítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-4 | N/A | PL. PM |

Győződjön meg arról, hogy dokumentálja és kommunikálja a biztonsági szervezet szerepköreinek és feladatainak egyértelmű stratégiáját. Rangsorolja a biztonsági döntések egyértelmű elszámoltathatóságát, mindenki számára elérhetővé teszi a megosztott felelősségi modellt, és megtanítja a technikai csapatokat a felhő védelméhez.

- [Azure Security – ajánlott eljárás 1 – emberek: a csapatok képzése a Felhőbeli biztonsági úton](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security – ajánlott eljárás 2 – emberek: a csapatok képzése a Felhőbeli biztonsági technológiában](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security – ajánlott eljárás 3 – folyamat: elszámoltathatóság kiosztása a Felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: hálózati biztonsági stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Hozzon létre egy Azure-alapú hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- Központosított Hálózatkezelés és biztonsági felelősség

- A virtuális hálózat szegmentálási modellje a nagyvállalati szegmentálási stratégiához igazodik

- Szervizelési stratégia a különböző veszélyforrások és támadási helyzetekben

- Az Internet Edge és a bejövő forgalom és a kimenő forgalom stratégiája

- Hibrid felhő és helyszíni összekapcsolási stratégia

- Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hivatkozási hálózati architektúra)

További információkért tekintse meg a következő referenciákat:

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure biztonsági teljesítményteszt – hálózati biztonság](security-controls-v2-network-security.md)

- [Az Azure hálózati biztonság áttekintése](../fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identitás-és privilegizált hozzáférési stratégia meghatározása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Hozzon létre egy Azure-identitást és egy emelt szintű hozzáférési megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- Központi identitás-és hitelesítési rendszer, valamint az egyéb belső és külső identitási rendszerekkel való kapcsolat

- Erős hitelesítési módszerek különböző használati esetekben és kikötésekben

- Magas jogosultsági szintű felhasználók védelme

- Felhasználói tevékenységek rendellenességének figyelése és feldolgozása  

- Felhasználói identitás és hozzáférés felülvizsgálata és egyeztetési folyamata

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – Identitáskezelés](security-controls-v2-identity-management.md)

- [Azure biztonsági teljesítményteszt – emelt szintű hozzáférés](security-controls-v2-privileged-access.md)

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure Identity Management biztonsági áttekintése](../fundamentals/identity-management-overview.md)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: a naplózási és a veszélyforrások reagálási stratégiájának meghatározása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Hozzon létre egy naplózási és veszélyforrás-reagálási stratégiát a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Rangsorolja az elemzőket kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai 

- Egy jól definiált incidens-reagálási folyamat NIST vagy más iparági keretrendszerrel való összehangolása 

- A fenyegetések észlelését, az incidensek megválaszolását és a megfelelőségi igényeket támogató napló rögzítése és megőrzése

- A fenyegetésekkel kapcsolatos központosított láthatóság és korrelációs információk a SIEM, a natív Azure-képességek és más források használatával 

- Kommunikációs és értesítési terv ügyfeleivel, szállítókkal és az érdekelt felekkel

- Az Azure Native és a harmadik féltől származó platformok használata az incidensek kezelésére, mint például a naplózás és a veszélyforrások észlelése, a kriminalisztika, valamint a támadási problémák elhárítása és felszámolása

- Az incidensek és az incidens utáni tevékenységek kezelésének folyamatai, például a tanulságok és a bizonyítékok megőrzése

További információkért tekintse meg a következő referenciákat:
- [Azure biztonsági teljesítményteszt – naplózás és fenyegetések észlelése](security-controls-v2-logging-threat-detection.md)

- [Azure biztonsági teljesítményteszt – incidens válasza](security-controls-v2-incident-response.md)

- [Azure Security – ajánlott eljárás 4 – folyamat. Incidensek frissítési folyamatainak frissítése a felhőben](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Azure bevezetési keretrendszer, naplózás és jelentéskészítési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure nagyvállalati szintű skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: biztonsági mentési és helyreállítási stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-8 | 10 | CP |

Hozzon létre egy Azure biztonsági mentési és helyreállítási stratégiát a szervezet számára. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- A helyreállítási időre vonatkozó célkitűzés (RTO) és a helyreállítási pontok célkitűzése (RPO) meghatározása az üzleti rugalmassági célkitűzéseknek megfelelően

- Redundancia kialakítása az alkalmazások és az infrastruktúra beállításában

- A biztonsági mentés védelme a hozzáférés-vezérlés és az adattitkosítás használatával

További információkért tekintse meg a következő referenciákat:
- [Azure biztonsági teljesítményteszt – biztonsági mentés és helyreállítás](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework – biztonsági mentés és vész-helyreállítás Azure-alkalmazásokhoz](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure-beli bevezetési keretrendszer – Üzletmenet-folytonosság és vész-helyreállítás](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Felelősség** : ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ( [További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)