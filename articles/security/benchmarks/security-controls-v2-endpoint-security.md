---
title: Azure Security teljesítményteszt v2 – Endpoint Security
description: Azure Security teljesítményteszt v2 Endpoint Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7672f4eb4530dbfb5d039b066fe7cf6eaf79e5a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718641"
---
# <a name="security-control-v2-endpoint-security"></a>Biztonsági vezérlő v2: Endpoint Security

A Endpoint Security a végpontok észlelésével és válaszával kapcsolatos vezérlőket fedi le. Ez magában foglalja a végpontok észlelését és válaszát (EDR) és a kártevők elleni szolgáltatást az Azure-környezetekben található végpontokhoz.

A megfelelő beépített Azure Policy megjelenítéséhez tekintse meg [Az Azure biztonsági teljesítményteszt szabályozási megfelelőségének beépített kezdeményezésének részleteit: Endpoint Security](../../governance/policy/samples/azure-security-benchmark#endpoint-security)

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

A végpont-észlelési és-reagálási (EDR) képességek engedélyezése a kiszolgálók és az ügyfelek számára, valamint az SIEM-és biztonsági műveletek folyamatainak integrálására.

A Microsoft Defender for Endpoint a nagyvállalati végpontok biztonsági platformjának részeként egy EDR képességet biztosít a speciális fenyegetések megelőzésére, észlelésére, kivizsgálására és reagálására.

- [A Microsoft Defender for Endpoint áttekintése](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender a Windows Server rendszerhez készült végponthoz](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender a nem Windows rendszerű kiszolgálók végpontja számára](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: központilag felügyelt, modern kártevő szoftverek használata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| ES – 2 | 8.1 | SI-2, SI-3, SC-3 |

A valós idejű és rendszeres vizsgálatra alkalmas, központilag felügyelt végpontok elleni kártevő megoldás használata

A Azure Security Center automatikusan képes azonosítani a virtuális gépek számos népszerű kártevő szoftveres megoldását, és jelentést készít az Endpoint Protection futási állapotáról, és javaslatokat tesz. 

A Microsoft antimalware for Azure Cloud Services a Windows rendszerű virtuális gépek (VM) alapértelmezett kártevő szoftvere. Linux rendszerű virtuális gépek esetén használjon harmadik féltől származó antimalware-megoldást. Az Azure Storage-fiókokba feltöltött kártevők észleléséhez használhatja a Azure Security Center veszélyforrások észlelését is az adatszolgáltatások számára. 

- [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../fundamentals/antimalware.md)

- [Támogatott Endpoint Protection-megoldások](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| ES – 3 | 8.2 | SI-2, SI-3 |

Győződjön meg arról, hogy a kártevők elleni aláírások gyorsan és következetesen frissülnek.

Kövesse az Azure Security Center: "számítás & alkalmazások" című témakörben található javaslatokat, hogy az összes végpont naprakész legyen a legújabb aláírásokkal. A Microsoft antimalware alapértelmezés szerint automatikusan telepíti a legújabb aláírásokat és a motor-frissítéseket. Linux esetén győződjön meg arról, hogy az aláírások frissülnek a harmadik féltől származó antimalware megoldásban.

- [A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines](../fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security)

- [Fenyegetések felderítése](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Endpoint Protection-Értékelés és javaslatok a Azure Security Center](../../security-center/security-center-endpoint-protection.md)