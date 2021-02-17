---
title: Azure Security teljesítményteszt v2 – naplózás és fenyegetések észlelése
description: Azure Security teljesítményteszt v2 naplózás és fenyegetések észlelése
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2a0ce52c094739ffbbce2f93a41b2533222066b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576806"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Security Control v2: naplózás és fenyegetések észlelése

A naplózás és a fenyegetések észlelése kiterjed az Azure-beli fenyegetések észlelésére, valamint az Azure-szolgáltatások naplófájljainak engedélyezésére, gyűjtésére és tárolására. Ebbe beletartozik az észlelési, vizsgálati és szervizelési folyamatok engedélyezése a vezérlőkkel, hogy magas színvonalú riasztásokat készítsen a natív veszélyforrások észlelésével az Azure-szolgáltatásokban; magában foglalja a naplók gyűjtését is Azure Monitor, központosítva a biztonsági elemzést az Azure Sentinel, az időszinkronizálás és a naplózás megőrzése mellett. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Győződjön meg arról, hogy a különböző típusú Azure-eszközöket figyeli a lehetséges fenyegetések és rendellenességek tekintetében. A magas színvonalú riasztások beszerzésével csökkentheti a hamis pozitív értéket az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

Használja a Azure Security Center beépített veszélyforrások észlelésére szolgáló funkciót, amely az Azure-szolgáltatások telemetria figyelésén és a szolgáltatási naplók elemzésén alapul. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a rendszerből, és az adatokat a munkaterületre másolja az elemzéshez. 

Emellett az Azure Sentinel használatával elemzési szabályokat hozhat létre, amelyek a környezetében meghatározott feltételeknek megfelelő veszélyforrásokat vadásznak. A szabályok a feltételek teljesülése esetén eredményeznek incidenseket, így az egyes incidensek megvizsgálható. Az Azure Sentinel a fenyegetések észlelési képességének növelése érdekében importálhatja a harmadik féltől származó veszélyforrások felderítését is. 

- [Fenyegetésvédelem az Azure Security Centerben](../../security-center/azure-defender.md)

- [Azure Security Center biztonsági riasztások referenciája](../../security-center/alerts-reference.md)

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../../sentinel/tutorial-detect-threats-custom.md)

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel vagy más SIEM/monitoring eszközeivel a kifinomultabb monitorozási és elemzési felhasználási esetekben: 
-   Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

-   Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.

-   Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

-   Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletekről, valamint az előfizetésben elavult fiókokról. Az alapszintű biztonsági higiéniai monitorozás mellett Azure Security Center veszélyforrások elleni védelmi modulja több részletes biztonsági riasztást is gyűjthet az egyes Azure-beli számítási erőforrásokból (például a Virtual Machines, a containers, az App Service szolgáltatásból), az adatforrásokból (például az SQL-ADATBÁZISból és a tárolóból) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy az egyes erőforrásokon belül megtekintse a fiókok rendellenességeit.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Fenyegetésvédelem az Azure Security Centerben](../../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

A hálózati biztonsági csoport (NSG) erőforrás-naplói, NSG-naplók, Azure Firewall naplók és webalkalmazási tűzfal (WAF) naplófájljainak engedélyezése és gyűjtése a biztonsági elemzésekhez az incidensek vizsgálatának, a fenyegetések vadászatának és a biztonsági riasztások generálásának támogatásához. A folyamat naplóit elküldheti egy Azure Monitor Log Analytics munkaterületre, majd a Traffic Analytics használatával betekintést biztosíthat. Győződjön meg arról, hogy a DNS-lekérdezési naplók gyűjtése segítséget nyújt a más hálózati adatok korrelációs biztosításához.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Azure Firewall naplói és metrikái](../../firewall/logs-and-metrics.md)

- [A Traffic Analytics engedélyezése és használata](../../network-watcher/traffic-analytics.md)

- [Figyelés Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure hálózatkezelési figyelési megoldások Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [A DNS-infrastruktúrával kapcsolatos információk gyűjtése a DNS Analytics megoldással](../../azure-monitor/insights/dns-analytics.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Az Azure-erőforrások naplózásának engedélyezése a megfelelőség, a fenyegetések észlelése, a vadászat és az incidensek vizsgálatához szükséges követelmények teljesítése érdekében. 

Azure Security Center és Azure Policy használatával engedélyezheti az erőforrás-naplókat és naplózhatja az adatok gyűjtését az Azure-erőforrásokon a naplózási, biztonsági és erőforrás-naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek. 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../../azure-monitor/essentials/platform-logs-overview.md)

- [Az Azure Security Center adatgyűjtés ismertetése](../../security-center/security-center-enable-data-collection.md)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Infrastruktúra és végpontbiztonság 

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Központosítsa a naplózási tárolást és elemzést a korreláció engedélyezéséhez. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával. 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

A megfelelőségi, szabályozási és üzleti követelményeinek megfelelően konfigurálja a napló megőrzését. 

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

- [Az adatmegőrzési időszak módosítása Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center riasztások és javaslatok exportálása](../../security-center/continuous-export.md)

**Felelősség**: Ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Biztonsági műveletek](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: a jóváhagyott időszinkronizálási források használata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP 800-53 R4 azonosító (k) |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

A Microsoft a legtöbb Azure-beli és SaaS-szolgáltatáshoz időforrást tart fenn. A virtuális gépek esetében a Microsoft alapértelmezett NTP-kiszolgálóját használja az időszinkronizáláshoz, hacsak nincs konkrét követelménye.  Ha létre kell hoznia a saját Network Time Protocol (NTP) kiszolgálót, gondoskodjon az UDP-szolgáltatás 123-es portjának védelméről.

Az Azure-on belüli erőforrások által generált összes napló biztosítja az időbélyegeket az alapértelmezés szerint megadott időzónával.

- [Az időszinkronizálás konfigurálása az Azure Windows számítási erőforrásaihoz](../../virtual-machines/windows/time-sync.md)

- [Az időszinkronizálás konfigurálása az Azure Linux számítási erőforrásaihoz](../../virtual-machines/linux/time-sync.md)

- [Bejövő UDP letiltása az Azure-szolgáltatásokhoz](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Felelősség**: Megosztott

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Szabályzat és szabványok](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)