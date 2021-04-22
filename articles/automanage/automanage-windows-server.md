---
title: Azure Automanage Windows Serverhez
description: Ismerje meg a Azure Automanage virtuális gépekre vonatkozó ajánlott eljárásokat a Windows Server rendszerű gépekhez automatikusan beállított szolgáltatásokhoz.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 98d91356b55be015b2c1b73787dad0bb7d8fd424
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863010"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure Automanage virtuális gépekre vonatkozó ajánlott eljárások – Windows Server

Ezeket az Azure-szolgáltatásokat a rendszer automatikusan beveszi, ha Windows Server rendszerű virtuális gépeken használja az Automatikus szolgáltatásokat. Ezek elengedhetetlenek az ajánlott eljárásokhoz, amelyet a dokumentumunkban [talál felhőadaptálási keretrendszer.](/azure/cloud-adoption-framework/manage/azure-server-management)

Mindezekhez a szolgáltatásokhoz automatikusan be lesz állítva, automatikusan konfiguráljuk, figyeljük a sodródásokat, és orvosoljuk a sodródás észlelése esetén. További információ erről a folyamatról: Azure Automanage [virtuális gépekhez.](automanage-virtual-machines.md)

## <a name="supported-windows-server-versions"></a>Támogatott Windows Server-verziók

Az automatikus automatizálás a következő Windows Server-verziókat támogatja:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Résztvevő szolgáltatások

|Szolgáltatás    |Leírás    |Támogatott környezetek<sup>1</sup>    |Támogatott beállítások<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[VM-elemzések monitorozása](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor for VMs figyeli a virtuális gépek teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásoktól való függőségeiket. További [információ](../azure-monitor/vm/vminsights-overview.md).    |Production    |No    |
|[Biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-overview)    |Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. További [információ](../backup/backup-azure-vms-introduction.md). A díjak a védett virtuális gépek számán és méretétől függnek. További [információ](https://azure.microsoft.com/pricing/details/backup/).    |Production    |Yes    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center egy egységes infrastruktúrabiztonsági felügyeleti rendszer, amely megerősíti az adatközpontok biztonsági biztonságát, és fejlett fenyegetésvédelmet biztosít a hibrid felhőbeli számítási feladatokhoz. További [információ](../security-center/security-center-introduction.md).  Az automatikus automatizálás konfigurálja azt az előfizetést, amelyben a virtuális gép található a virtuális gép ingyenes szintű Azure Security Center. Ha az előfizetése már elő van automatizálva Azure Security Center, akkor az automatikus automatizálás nem konfigurálja újra.    |Éles, fejlesztési/tesztelési    |No    |
|[Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Microsoft Antimalware azure-hoz való használata ingyenes, valós idejű védelem, amely segít a vírusok, kémprogramok és más kártevő szoftverek azonosításában és eltávolításában. Riasztásokat hoz létre, amikor egy ismert kártevő vagy nemkívánatos szoftver megpróbálja telepíteni magát vagy futtatni az Azure-rendszereken. További [információ](../security/fundamentals/antimalware.md). |Éles, fejlesztési/tesztelési    |Yes    |
|[Frissítéskezelés](https://docs.microsoft.com/azure/automation/update-management/overview)    |A virtuális Update Management operációs Azure Automation a virtuális gépek operációsrendszer-frissítéseit. Az összes ügynökgépen gyorsan felmérheti az elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítésének telepítésének folyamatát. További [információ](../automation/update-management/overview.md).    |Éles, fejlesztési/tesztelési    |No    |
|[Change Tracking & Inventory](https://docs.microsoft.com/azure/automation/change-tracking/overview) |változáskövetés és leltározás változáskövetési és leltárfunkciók kombinálása lehetővé teszi a virtuális gép és a kiszolgáló infrastruktúrájának változásainak nyomon követését. A szolgáltatás támogatja a változáskövetést a környezetben található szolgáltatások, démonszoftverek, beállításjegyzék és fájlok között, így segít diagnosztizálni a nem kívánt módosításokat és riasztásokat kiadni. A leltártámogatás lehetővé teszi a vendégen lévő erőforrások lekérdezését a telepített alkalmazások és egyéb konfigurációelemek láthatóságának érdekében.  További [információ](../automation/change-tracking/overview.md).    |Éles, fejlesztési/tesztelési    |No    |
|[Azure-vendégkonfiguráció](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | A vendégkonfigurációs szabályzat a konfiguráció figyelére és a gép megfelelőségére vonatkozó jelentésre használható. Az Automatikus kezelés szolgáltatás a Vendégkonfiguráció bővítmény használatával telepíti a [Windows](/windows/security/threat-protection/windows-security-baselines) biztonsági alapkonfigurációit. Windows rendszerű gépek esetén a vendégkonfigurációs szolgáltatás automatikusan újraalkotja az alapbeállításokat, ha azok nem megfelelőek. További [információ](../governance/policy/concepts/guest-configuration.md).    |Éles, fejlesztési/tesztelési    |No    |
|[Rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | A rendszerindítási diagnosztika az Azure-beli virtuális gépek (VM) hibakeresési funkciója, amely lehetővé teszi a virtuális gépek rendszerindítási meghibásodásának diagnosztizálást. A rendszerindítási diagnosztika lehetővé teszi, hogy a felhasználó megfigyelje a virtuális gép rendszerindítási állapotát a soros naplóinformációk és képernyőképek gyűjtésével. Ez csak felügyelt lemezeket használó gépeken lesz engedélyezve. |Éles, fejlesztési és tesztelési    |No    |
|[Azure Automation-fiók](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation az infrastruktúra és az alkalmazások teljes életciklusa alatt támogatja a felügyeletet. További [információ](../automation/automation-intro.md).    |Éles, fejlesztési és tesztelési    |No    |
|[Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor naplóadatokat tárol egy Log Analytics-munkaterületen, amely egy Azure-erőforrás és egy tároló, ahol az adatok gyűjtése, összesítése és felügyeleti határként szolgálnak. További [információ](../azure-monitor/logs/design-logs-deployment.md).    |Éles, fejlesztési és tesztelési    |No    |


<sup>1</sup> A környezet kiválasztása az automatikus szolgáltatás engedélyezésekor érhető el. További [információ](automanage-virtual-machines.md#environment-configuration). A környezet alapértelmezett beállításait is módosíthatja, és saját beállításokat állíthat be az ajánlott eljárások korlátozásai között.


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a virtuális gépek automatikus Azure Portal.

> [!div class="nextstepaction"]
> [A virtuális gépek automatikus Azure Portal](quick-create-virtual-machines-portal.md)