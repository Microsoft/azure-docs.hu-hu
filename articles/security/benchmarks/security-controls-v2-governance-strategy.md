---
title: Azure Security teljesítményteszt v2 – kormányzás és stratégia
description: Azure Security teljesítményteszt v2 – irányítás és stratégia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727022"
---
# <a name="security-control-v2-governance-and-strategy"></a>Security Control v2: irányítási és stratégiai

A szabályozás és a stratégia útmutatást nyújt egy koherens biztonsági stratégia és dokumentált irányítási megközelítés biztosításához a biztonsági garanciák kialakításához és fenntartásához, beleértve a szerepkörök és felelősségek létrehozását a Felhőbeli biztonsági funkciók, az egységes műszaki stratégia és a támogatási szabályzatok és szabványok tekintetében.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Eszközkezelési és adatvédelmi stratégia

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Gondoskodjon róla, hogy a rendszerek és az információk folyamatos monitorozásával és védelmével kapcsolatos egyértelmű stratégiát dokumentáljon és tájékoztasson. Állítsa fel az üzletileg kritikus adatok és rendszerek felmérésének, védelmének és monitorozásának fontossági sorrendjét.

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

- Az üzleti kockázattal összhangban lévő adatbesorolási szabványok

- A kockázatok és az eszközleltár biztonsági szervezet általi láthatósága

- A használandó Azure-szolgáltatások biztonsági szervezet általi jóváhagyása

- Az biztonsága azok teljes életciklusában

- A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

- Az Azure natív védelmi funkcióinak és külső védelmi funkciók használata

- Adattitkosítási követelmények a használatban lévő és a jelenleg nem használt használati esetekhez

- A megfelelő titkosítási szabványok

További információkat az alábbi hivatkozásokon találhat:
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../fundamentals/encryption-overview.md)

- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Eszközkezelés](security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Adatvédelem](security-controls-v2-data-protection.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Nagyvállalati szegmentálási stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Hozzon létre egy nagyvállalati stratégiát, amely az eszközök hozzáférésének szegmentálására használatos az identitás, a hálózat, az alkalmazás, az előfizetés, a felügyeleti csoport és más vezérlők kombinációjával.

Gondosan egyensúlyozza ki a biztonsági elkülönítés igényét azoknak a rendszereknek a mindennapos működésével, amelyeknek kommunikálniuk kell egymással, és hozzá kell férniük az adatokhoz.

Gondoskodjon róla, hogy a szegmentálási stratégia következetesen meg legyen valósítva az olyan vezérlőtípusokon, mint a hálózati biztonság, az identitás- és hozzáférés-modellek, az alkalmazások jogosultsági és hozzáférési modelljei és az emberi folyamatokra vonatkozó vezérlők.

- [Útmutató Azure-beli szegmentálási stratégiához (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató Azure-beli szegmentálási stratégiához (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás vállalati szegmentálási stratégiához igazítása](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági állapotot felügyelő stratégiája definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Folyamatosan méri és csökkenti a kockázatokat az egyes eszközeivel és a bennük üzemeltetett környezettel kapcsolatban. Kezelje kiemelten az olyan nagy értékű összetevőket és leginkább elérhetővé tett támadási felületeket, mint a közzétett alkalmazások, a hálózat be- és kilépési pontjai, a felhasználói és rendszergazdai végpontok stb.

- [Azure Security Benchmark - Biztonsági állapot és biztonsági rések felmérése](security-controls-v2-posture-vulnerability-management.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: A vállalati szerepkörök, a felelősség és az elszámoltathatóság összehangolása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-4 | N/A | PL. PM |

Győződjön meg arról, hogy dokumentálja és kommunikálja a biztonsági szervezet szerepköreinek és feladatainak egyértelmű stratégiáját. Kezelje kiemelten a biztonsági döntések egyértelmű elszámoltathatóságát, a megosztott felelősségi modell oktatását mindenki számára, és a technikai csapatok oktatását a felhőbeli biztonsági technikákra.

- [Ajánlott Azure-biztonsági eljárások 1 – Személyek: Csapatok oktatása a felhőbeli biztonság kialakítására](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Ajánlott Azure-biztonsági eljárások 2 – Személyek: Csapatok oktatása a felhőbeli biztonsági technológiákra](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Ajánlott Azure-biztonsági eljárások 3 – Folyamat: Elszámoltathatóság hozzárendelése felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Hozzon létre egy Azure-alapú hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait:

- Központosított hálózatkezelési és biztonsági felelősség

- A vállalati szegmentálási stratégiának megfelelő virtuális hálózati szegmentálási modell

- Szervizelési stratégia különböző fenyegetési és támadási helyzetekre

- Internetes peremhálózati, bejövő és kimenő forgalomra vonatkozó stratégia

- Hibrid felhős és helyszíni kapcsolódási stratégia

- Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hivatkozási hálózati architektúra)

További információkat az alábbi hivatkozásokon találhat:

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Hálózati biztonság](security-controls-v2-network-security.md)

- [A nagyvállalati hálózati biztonság áttekintése](../fundamentals/network-overview.md)

- [Nagyvállalati hálózati architektúrára vonatkozó stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitáskezelési és emelt jogosultságú hozzáférési stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Hozzon létre egy Azure-identitást és egy emelt szintű hozzáférési megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait:

- Központosított identitáskezelési és hitelesítési rendszer és annak kapcsolatai más belső és külső identitásrendszerekkel

- Erős hitelesítési módszerek különböző használati helyzetek és feltételek esetén

- Emelt jogosultságú felhasználók védelme

- Rendellenes felhasználói tevékenységek monitorozása és kezelése

- Felhasználói identitások és hozzáférések felülvizsgálati és egyeztetési folyamata

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Identitáskezelés](security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](security-controls-v2-privileged-access.md)

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure identitáskezelésének biztonsági áttekintése](../fundamentals/identity-management-overview.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási és veszélyforrás-kezelési stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Hozzon létre egy naplózási és veszélyforrás-reagálási stratégiát a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Biztosítson jó minőségű riasztásokat és zökkenőmentes élményt az elemzőknek, így a fenyegetésekre összpontosíthatnak az integráció és a manuális lépések helyett. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

- A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai 

- Jól definiált incidenskezelési folyamat az NIST-vel vagy más iparági keretrendszerrel összhangban 

- Naplórögzítés és -megőrzés a veszélyforrások észlelése, az incidensek kezelése és a megfelelőségi igények támogatására

- A fenyegetésekkel kapcsolatos információk központosított láthatósága és összevetése SIEM, natív Azure-képességek és más források használatával 

- Kommunikációs és értesítési terv az ügyfelek, szállítók és külső érdekelt felek számára

- Natív Azure-beli és külső platformok használata incidensek kezelésére, például naplózásra és veszélyforrások észlelésére, kivizsgálásra és a támadások megfékezésére és megszüntetésére

- Folyamatok az olyan incidenskezelési és incidens utáni tevékenységekhez, mint a tanulságok levonása és a nyomok megőrzése

További információkat az alábbi hivatkozásokon találhat:
- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Incidenskezelés](security-controls-v2-incident-response.md)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure-beli nagyvállalati szintű skálázás, felügyelet és monitorozás](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: biztonsági mentési és helyreállítási stratégia definiálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| GS-8 | 10 | CP |

Hozzon létre egy Azure biztonsági mentési és helyreállítási stratégiát a szervezet számára. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

- A helyreállítási időre vonatkozó célkitűzés (RTO) és a helyreállítási pontok célkitűzése (RPO) meghatározása az üzleti rugalmassági célkitűzéseknek megfelelően

- Redundancia kialakítása az alkalmazások és az infrastruktúra beállításában

- A biztonsági mentés védelme a hozzáférés-vezérlés és az adattitkosítás használatával

További információkat az alábbi hivatkozásokon találhat:
- [Azure biztonsági teljesítményteszt – biztonsági mentés és helyreállítás](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework – biztonsági mentés és vész-helyreállítás Azure-alkalmazásokhoz](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure-beli bevezetési keretrendszer – Üzletmenet-folytonosság és vész-helyreállítás](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Minden érintett](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)