---
title: Azure Security Control – naplózás és figyelés
description: Az Azure Security Control naplózása és figyelése
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d30d8cdfe6ee93f54dfd560987ac8b6a78517fd8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718692"
---
# <a name="security-control-logging-and-monitoring"></a>Biztonsági ellenőrzés: naplózás és figyelés

A biztonsági naplózás és a monitorozás az Azure-szolgáltatások naplófájljainak engedélyezésével, beszerzésével és tárolásával kapcsolatos tevékenységekre koncentrál.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft az Azure-erőforrások időforrásait is fenntartja, azonban lehetősége van a számítási erőforrások időszinkronizálási beállításainak kezelésére.

- [Az időszinkronizálás konfigurálása az Azure Windows számítási erőforrásaihoz](../../virtual-machines/windows/time-sync.md)

- [Az időszinkronizálás konfigurálása az Azure Linux számítási erőforrásaihoz](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2,2 | 6,5, 6,6 | Ügyfél |

Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2.3 | 6,2, 6,3 | Ügyfél |

Az Azure-erőforrások diagnosztikai beállításainak engedélyezése a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2,4 | 6,2, 6,3 | Ügyfél |

Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a figyelése. Ha a számítási erőforrás a szervezet tulajdonában van, akkor az Ön felelőssége, hogy figyelje. A Azure Security Center az operációs rendszer figyelésére használható. Az operációs rendszer Security Center által összegyűjtött adatok közé tartozik az operációs rendszer típusa és verziója, az operációs rendszer (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is gyűjti.

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Az Azure Security Center adatgyűjtés ismertetése](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2.5 | 6.4 | Ügyfél |

Azure Monitor belül állítsa be a Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Az adatmegőrzési időszak módosítása Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2,6 | 6.7 | Ügyfél |

Elemezheti és figyelheti a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplókat. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek. 

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

- [Log Analytics munkaterület ismertetése](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2.7 | 6.8 | Ügyfél |

A biztonsági naplókban és eseményekben talált rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz használja a Azure Security Center Log Analytics munkaterülettel.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2,8 | 8,6 | Ügyfél |

Az Azure-Virtual Machines és Cloud Services a kártevők elleni események gyűjtésének engedélyezése.

- [A Microsoft antimalware konfigurálása a Virtual Machineshoz](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [A Microsoft antimalware konfigurálása a Cloud Serviceshoz](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [A Microsoft antimalware megismerése](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2.9 | 8.7 | Ügyfél |

Egy harmadik féltől származó megoldás implementálása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, amelyet a szervezeteknek szüksége van.  

## <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 2.10 | 8,8 | Ügyfél |

A folyamat-létrehozási esemény és a parancssori mező naplózásához használja a Microsoft monitoring agentet az összes támogatott Azure Windows-beli virtuális gépen.   A támogatott Azure Linux rendszerű virtuális gépek esetében a konzol naplózását manuálisan is konfigurálhatja a csomópontok alapján, és a syslog használatával tárolhatja az adattárakat.  Emellett a Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket hajthat végre az Azure Virtual Machines szolgáltatásból naplózott adatokon. 

- [Adatgyűjtés az Azure Security Centerben](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../../azure-monitor/logs/get-started-queries.md)

- [Rendszernapló-adatforrások az Azure Monitorban](../../azure-monitor/agents/data-sources-syslog.md)


## <a name="next-steps"></a>Következő lépések

- Tekintse meg a következő biztonsági vezérlőt: [identitás és Access Control](security-control-identity-access-control.md)