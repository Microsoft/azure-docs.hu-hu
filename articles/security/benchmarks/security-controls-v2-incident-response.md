---
title: Azure Security teljesítményteszt v2 – incidens válasza
description: Azure Security benchmark v2 – incidens válasza
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 002/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 40a88349040482b3325e63d7035dea35df444cdb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698586"
---
# <a name="security-control-v2-incident-response"></a>Security Control v2: incidens válasza

Az incidensekre adott válasz az incidensek életciklusának előkészítését, észlelését és elemzését, a tárolást és az incidens utáni tevékenységeket fedi le. Ez magában foglalja az Azure-szolgáltatások, például a Azure Security Center és a Sentinel használatát az incidensek reagálási folyamatának automatizálásához.

A megfelelő beépített Azure Policy megtekintéséhez tekintse meg [Az Azure biztonsági teljesítményteszt szabályozási megfelelőségének beépített kezdeményezésének részleteit: incidens válasza](../../governance/policy/samples/azure-security-benchmark#incident-response)

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensválasz-folyamat frissítése az Azure-hoz

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Győződjön meg arról, hogy a szervezetnek vannak folyamatai a biztonsági incidensekre való reagálásra, frissítette ezeket a folyamatokat az Azure-ban, és rendszeresen gyakorolja őket a készültség biztosítására.

- [Biztonság implementálása a teljes nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Incidensválasz referencia-útmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensről szóló értesítés beállítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Biztonsági incidensek elérhetőségi adatainak beállítása a Azure Security Centerban. A Microsoft ezen kapcsolattartási adatok használatával keresi meg Önt, ha a Microsoft Security Response Center (MSCR) felfedezi, hogy az adataihoz törvénytelen vagy jogosulatlan módon fértek hozzá. Lehetősége van az incidensekkel kapcsolatos riasztások és értesítések testreszabására a különböző Azure-szolgáltatásokban az incidensválasz-igények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása jó minőségű riasztások alapján

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ez lehetővé teszi, hogy megismerje a múltbeli incidensek tanulságait, és rangsorolja a riasztásokat az elemzők számára, hogy ne pazarolja az időt a hamis pozitív értékekre. 

Kiváló minőségű riasztások a korábbi incidensek tapasztalatai és ellenőrzött közösségi források alapján, valamint a riasztások létrehozásához és tisztításához tervezett eszközökkel hozhatók létre a különböző jelforrások egyesítésével és összehasonlításával. 

Az Azure Security Center számos Azure-objektum esetében kiváló minőségű riasztásokat biztosít. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidensek kivizsgálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Győződjön meg arról, hogy az elemzők különböző adatforrásokat tudnak lekérdezni és használni a lehetséges incidensek kivizsgálásakor, így teljes körű képet kaphat arról, hogy mi történt. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon. A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../../sentinel/tutorial-investigate-cases.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Adja meg a kontextust az elemzők számára, amelyeken a riasztás súlyossága és az eszközök érzékenysége alapján az elsőre kell összpontosítani. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy a Security Center mennyire biztos a találatban vagy a riasztás kibocsátásához használt elemzésben, valamint abban, hogy a riasztáshoz vezető tevékenység rosszindulatú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elszigetelés, kiiktatás és helyreállítás – az incidenskezelés automatizálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatizálja a manuális ismétlődő feladatokat a válaszidő felgyorsításához és az elemzők terhének csökkentéséhez. A manuális tevékenységek végrehajtása több időt vesz igénybe, emiatt az elemzők kevesebb incidenst képesek kezelni. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../../sentinel/tutorial-respond-threats-playbook.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidens előkészítése](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)