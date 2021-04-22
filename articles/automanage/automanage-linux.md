---
title: Azure Automanage Linuxhoz
description: Ismerje meg a Azure Automanage virtuális gépekre vonatkozó ajánlott eljárásokat a Linux rendszerű gépekhez automatikusan bevetett és konfigurált szolgáltatásokhoz.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0d35963d96ead68c35ca44467119b3c03d0b16c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863064"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure Automanage virtuális gépekre vonatkozó ajánlott eljárások – Linux

Ezeket az Azure-szolgáltatásokat a rendszer automatikusan beveszi az Ön számára, amikor a Linux rendszerű virtuális gépeken az Automatikus gép-szolgáltatásokat használja. Ezek elengedhetetlenek az ajánlott eljárásokhoz. Ezt a dokumentumban találja [felhőadaptálási keretrendszer.](/azure/cloud-adoption-framework/manage/azure-server-management)

Mindezekhez a szolgáltatásokhoz automatikusan be lesz állítva, automatikusan konfiguráljuk, figyeljük a sodródásokat, és orvosoljuk a sodródás észlelése esetén. További információ erről a folyamatról: Azure Automanage [virtuális gépekhez.](automanage-virtual-machines.md)

## <a name="supported-linux-distributions-and-versions"></a>Támogatott Linux-disztribúciók és -verziók

Az automanage a következő Linux-disztribúciókat és -verziókat támogatja:

- CentOS 7.3+
- RHEL 7.4+
- Ubuntu 16.04 és 18.04
- SLES 12 (csak SP3-SP5 esetén)

## <a name="participating-services"></a>Résztvevő szolgáltatások

>[!NOTE]
> Microsoft Antimalware linuxos virtuális gépek jelenleg nem támogatottak.

|Szolgáltatás    |Leírás    |Támogatott környezetek<sup>1</sup>    |Támogatott beállítások<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[VM-elemzések monitorozása](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor for VMs figyeli a virtuális gépek teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásoktól való függőségeiket. További [információ](../azure-monitor/vm/vminsights-overview.md).    |Production    |No    |
|[Biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-overview)   |Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. További [információ](../backup/backup-azure-vms-introduction.md). A díjak a védett virtuális gépek számán és méretétől függnek. További [információ](https://azure.microsoft.com/pricing/details/backup/).    |Production    |Yes    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center egy egységes infrastruktúrabiztonsági felügyeleti rendszer, amely megerősíti az adatközpontok biztonsági biztonságát, és fejlett fenyegetésvédelmet biztosít a hibrid felhőbeli számítási feladatokhoz. További [információ](../security-center/security-center-introduction.md).  Az automatikus automatizálás konfigurálja azt az előfizetést, amelyben a virtuális gép található, az ingyenes szintű előfizetési Azure Security Center. Ha az előfizetése már elő van automatizálva Azure Security Center, akkor az automatikus automatizálás nem konfigurálja újra.    |Éles, fejlesztési és tesztelési    |No    |
|[Frissítéskezelés](https://docs.microsoft.com/azure/automation/update-management/overview)    |A virtuális gépek Update Management a Azure Automation operációsrendszer-frissítések kezeléséhez használhatja a virtuális gépeken. Az összes ügynökgépen gyorsan felmérheti az elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítésének telepítésének folyamatát. További [információ](../automation/update-management/overview.md).    |Éles, fejlesztési és tesztelési    |No    |
|[Change Tracking & Inventory](https://docs.microsoft.com/azure/automation/change-tracking/overview) |változáskövetés és leltározás változáskövetési és leltárfunkciók kombinálása révén nyomon követheti a virtuális gép és a kiszolgáló infrastruktúrájának változásait. A szolgáltatás támogatja a környezetben található szolgáltatások, démonszoftverek, beállításjegyzék és fájlok változáskövetését, így segít diagnosztizálni a nemkívánatos módosításokat és riasztásokat kiadni. A leltártámogatás lehetővé teszi a vendégen lévő erőforrások lekérdezését a telepített alkalmazások és egyéb konfigurációelemek láthatóságának érdekében.  További [információ](../automation/change-tracking/overview.md).    |Éles, fejlesztési és tesztelési    |No    |
|[Azure-vendégkonfiguráció](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | A vendégkonfigurációs szabályzat a konfiguráció figyelése és a gép megfelelőségét figyelő jelentés. Az Automanage szolgáltatás a Vendégkonfiguráció bővítmény használatával telepíti az Azure Linux alapkonfigurációt. Linux rendszerű gépek esetén a vendégkonfigurációs szolgáltatás csak naplózási módban telepíti az alapkonfigurációt. Látni fogja, hogy a virtuális gép hol nem felel meg az alapkonfigurációnak, de a meg nem felelés automatikusan nem lesz orvosolva. További [információ](../governance/policy/concepts/guest-configuration.md).    |Éles, fejlesztési és tesztelési    |No    |
|[Rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | A rendszerindítási diagnosztika az Azure-beli virtuális gépek (VM) hibakeresési funkciója, amely lehetővé teszi a virtuális gépek rendszerindítási meghibásodásának diagnosztizálást. A rendszerindítási diagnosztika lehetővé teszi, hogy a felhasználó megfigyelje a virtuális gép rendszerindítási állapotát a soros naplóinformációk és képernyőképek gyűjtésével. Ez csak felügyelt lemezeket használó gépeken lesz engedélyezve. |Éles, fejlesztési/tesztelési    |No    |
|[Azure Automation-fiók](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation infrastruktúra és alkalmazások teljes életciklusa alatt támogatja a felügyeletet. További [információ](../automation/automation-intro.md).    |Éles, fejlesztési/tesztelési    |No    |
|[Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor log analytics-munkaterületen tárolja, amely egy Azure-erőforrás és egy tároló, ahol az adatok gyűjtése, összesítése és felügyeleti határként való használata szolgál. További [információ](../azure-monitor/logs/design-logs-deployment.md).    |Éles, fejlesztési/tesztelési    |No    |


<sup>1</sup> A környezet kiválasztása az Automanage engedélyezésekor érhető el. További [információ](automanage-virtual-machines.md#environment-configuration). A környezet alapértelmezett beállításait is módosíthatja, és saját beállításokat állíthat be az ajánlott eljárásokra vonatkozó korlátozásokon belül.


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a virtuális gépek automatikus Azure Portal.

> [!div class="nextstepaction"]
> [A virtuális gépek automatikus automatizálásának engedélyezése a Azure Portal](quick-create-virtual-machines-portal.md)