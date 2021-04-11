---
title: Azure-alapú autokezelés Linux rendszeren
description: Ismerje meg az Azure automatikus felügyeletét a Virtual Machines szolgáltatással kapcsolatos ajánlott eljárásokat a Linux rendszerű gépek számára automatikusan előkészített és konfigurált szolgáltatások esetében.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 24b72afb7f685e50ac9452889b404fa8bda29644
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278320"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure-beli automanage-alapú virtuális gépek – ajánlott eljárások – Linux

Ezek az Azure-szolgáltatások automatikusan bevezetésre kerülnek, amikor a virtuális gépekhez (VM-EK) automatikus felügyeletet használ egy linuxos virtuális GÉPEN. Alapvető fontosságúak az ajánlott eljárásokról szóló tanulmány, amelyet a [felhőalapú bevezetési keretrendszerben](/azure/cloud-adoption-framework/manage/azure-server-management)talál.

Az összes ilyen szolgáltatás esetében automatikus bevezetést, automatikus konfigurálást, figyelést és a drift észlelését követően szervizelést végezünk. A folyamattal kapcsolatos további információkért lásd: [Az Azure-beli automanage szolgáltatás virtuális gépeken](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Támogatott Linux-disztribúciók és-verziók

Az automanage a következő Linux-disztribúciókat és-verziókat támogatja:

- CentOS 7.3 +
- RHEL 7.4 +
- Ubuntu 16,04 és 18,04
- SLES 12 (csak SP3 – SP5)

## <a name="participating-services"></a>Résztvevő szolgáltatások

>[!NOTE]
> A Microsoft antimalware jelenleg nem támogatott Linux rendszerű virtuális gépeken.

|Szolgáltatás    |Leírás    |Támogatott környezetek<sup>1</sup>    |Támogatott beállítások<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[A VM-alapú adatvizsgálatok figyelése](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor for VMs figyeli a virtuális gépek teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásokra vonatkozó függőségeket. [További](../azure-monitor/vm/vminsights-overview.md)információ.    |Production    |Nem    |
|[Biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-overview)   |Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. [További](../backup/backup-azure-vms-introduction.md)információ. A díjak a védett virtuális gépek számán és méretén alapulnak. [További](https://azure.microsoft.com/pricing/details/backup/)információ.    |Production    |Igen    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center egy egységes infrastruktúra-alapú biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben futó hibrid számítási feladatokhoz. [További](../security-center/security-center-introduction.md)információ.  Az automanage Beállítja azt az előfizetést, amelyben a virtuális gép a Azure Security Center ingyenes szintű ajánlatában található. Ha az előfizetése már bekerült a Azure Security Centerba, az automanage nem konfigurálja újra.    |Éles üzem, fejlesztés/tesztelés    |Nem    |
|[Frissítéskezelés](https://docs.microsoft.com/azure/automation/update-management/overview)    |A virtuális gépek operációs rendszerének frissítéseinek kezeléséhez Azure Automation Update Management is használhatja. Gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítéseinek telepítésének folyamatát. [További](../automation/update-management/overview.md)információ.    |Éles üzem, fejlesztés/tesztelés    |Nem    |
|[Change Tracking & leltár](https://docs.microsoft.com/azure/automation/change-tracking/overview) |A Change Tracking és a leltár ötvözi a Change Tracking and Inventory függvényeket, így nyomon követheti a virtuális gépek és a kiszolgálói infrastruktúra változásait. A szolgáltatás támogatja a változások nyomon követését a környezetekben, a Daemon-szoftvereket, a beállításjegyzéket és az adott környezetben található fájlokat, így segít a nemkívánatos módosítások diagnosztizálásában és a riasztások felemelésében. A leltár-támogatás lehetővé teszi a vendég erőforrásainak lekérdezését a telepített alkalmazások és egyéb konfigurációs elemek láthatóságának érdekében.  [További](../automation/change-tracking/overview.md)információ.    |Éles üzem, fejlesztés/tesztelés    |Nem    |
|[Azure Guest Configuration](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | A vendég konfigurációs szabályzattal figyelhető a konfiguráció és a jelentés a gép megfelelőségéről. Az automatikus kezelés szolgáltatás az Azure Linux alapkonfigurációját a vendég konfigurációs bővítménnyel fogja telepíteni. A Linux rendszerű gépek esetében a vendég konfigurációs szolgáltatás csak naplózási módban telepíti az alaptervet. Láthatja, hogy a virtuális gép hol nem felel meg az alapkonfigurációnak, de a nem megfelelőséget nem lehet automatikusan szervizelni. [További](../governance/policy/concepts/guest-configuration.md)információ.    |Éles üzem, fejlesztés/tesztelés    |Nem    |
|[Rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | A rendszerindítási diagnosztika az Azure Virtual Machines (VM) hibakeresési funkciója, amely lehetővé teszi a virtuális gépek rendszerindítási hibáinak diagnosztizálását. A rendszerindítási diagnosztika lehetővé teszi a felhasználók számára, hogy betartsák a virtuális gép állapotát, mivel a soros napló információi és képernyőképek összegyűjtésével indulnak. |Éles üzem, fejlesztés/tesztelés    |Nem    |
|[Azure Automation-fiók](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation támogatja a felügyeletet az infrastruktúra és az alkalmazások életciklusa során. [További](../automation/automation-intro.md)információ.    |Éles üzem, fejlesztés/tesztelés    |Nem    |
|[Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |A Azure Monitor egy Log Analytics-munkaterületen tárolja a napló adatokat, amely egy Azure-erőforrás, valamint egy olyan tároló, amelybe az adatok gyűjtése, összesítése és felügyeleti határként szolgál. [További](../azure-monitor/logs/design-logs-deployment.md)információ.    |Éles üzem, fejlesztés/tesztelés    |Nem    |


<sup>1</sup> a környezet kiválasztása az automanage engedélyezésekor érhető el. [További](automanage-virtual-machines.md#environment-configuration)információ. Emellett módosíthatja a környezet alapértelmezett beállításait, és az ajánlott eljárások korlátain belül beállíthatja a saját beállításait is.


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)