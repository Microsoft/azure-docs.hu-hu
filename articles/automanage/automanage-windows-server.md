---
title: Azure-felügyelet a Windows Serverhez
description: Ismerje meg az Azure automatikus felügyeletét a virtuális gépekhez – ajánlott eljárások a Windows Server rendszerű gépek számára automatikusan előkészített és konfigurált szolgáltatásokhoz.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: a3bb0a2877c71d19b05f424dc44e302c69577b2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662203"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure-beli automanage-alapú virtuális gépek – ajánlott eljárások – Windows Server

Ezek az Azure-szolgáltatások automatikusan bekerülnek az Ön számára, ha Windows Server rendszerű virtuális gépen használja az automatikus felügyeletet a virtuális gépeken. Alapvető fontosságúak az ajánlott eljárásokról szóló tanulmány, amelyet a [felhőalapú bevezetési keretrendszerben](/azure/cloud-adoption-framework/manage/azure-server-management)talál.

Az összes ilyen szolgáltatás esetében automatikus bevezetést, automatikus konfigurálást, figyelést és a drift észlelését követően szervizelést végezünk. A folyamattal kapcsolatos további információkért lásd: [Az Azure-beli automanage szolgáltatás virtuális gépeken](automanage-virtual-machines.md).

## <a name="supported-windows-server-versions"></a>Támogatott Windows Server-verziók

Az automanage a következő Windows Server-verziókat támogatja:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Résztvevő szolgáltatások

|Szolgáltatás    |Leírás    |Támogatott környezetek<sup>1</sup>    |Támogatott beállítások<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|A VM-alapú adatvizsgálatok figyelése    |Azure Monitor for VMs figyeli a virtuális gépek teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásokra vonatkozó függőségeket. [További](../azure-monitor/vm/vminsights-overview.md)információ.    |Production    |No    |
|Backup    |Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. [További](../backup/backup-azure-vms-introduction.md)információ. A díjak a védett virtuális gépek számán és méretén alapulnak. [További](https://azure.microsoft.com/pricing/details/backup/)információ.    |Production    |Yes    |
|Azure Security Center    |Azure Security Center egy egységes infrastruktúra-alapú biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben futó hibrid számítási feladatokhoz. [További](../security-center/security-center-introduction.md)információ.  Az automanage Beállítja azt az előfizetést, amelyben a virtuális gép a Azure Security Center ingyenes szintű ajánlatában található. Ha az előfizetése már bekerült a Azure Security Centerba, az automanage nem konfigurálja újra.    |Éles üzem, fejlesztés/tesztelés    |No    |
|Microsoft Antimalware    |Az Azure-hoz készült Microsoft antimalware egy ingyenes valós idejű védelem, amely segít a vírusok, kémprogramok és más kártevő szoftverek azonosításában és eltávolításában. Riasztásokat állít elő, amikor az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken. [További](../security/fundamentals/antimalware.md)információ. |Éles üzem, fejlesztés/tesztelés    |Yes    |
|Frissítéskezelés    |A virtuális gépek operációs rendszerének frissítéseinek kezeléséhez Azure Automation Update Management is használhatja. Gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítéseinek telepítésének folyamatát. [További](../automation/update-management/overview.md)információ.    |Éles üzem, fejlesztés/tesztelés    |No    |
|Change Tracking & leltár    |A Change Tracking és a leltár ötvözi a Change Tracking and Inventory függvényeket, így nyomon követheti a virtuális gépek és a kiszolgálói infrastruktúra változásait. A szolgáltatás támogatja a változások nyomon követését a környezetekben, a Daemon-szoftvereket, a beállításjegyzéket és az adott környezetben található fájlokat, így segít a nemkívánatos módosítások diagnosztizálásában és a riasztások felemelésében. A leltár-támogatás lehetővé teszi a vendég erőforrásainak lekérdezését a telepített alkalmazások és egyéb konfigurációs elemek láthatóságának érdekében.  [További](../automation/change-tracking/overview.md)információ.    |Éles üzem, fejlesztés/tesztelés    |No    |
|Azure Guest Configuration    | A vendég konfigurációs szabályzattal figyelhető a konfiguráció és a jelentés a gép megfelelőségéről. Az automatikus kezelés szolgáltatás a [Windows biztonsági](/windows/security/threat-protection/windows-security-baselines) alapkonfigurációit a vendég konfigurációs bővítménnyel fogja telepíteni. Windows rendszerű gépek esetén a vendég konfigurációs szolgáltatás automatikusan újraalkalmazza az alapkonfiguráció beállításait, ha azok nem felelnek meg a megfelelőségnek. [További](../governance/policy/concepts/guest-configuration.md)információ.    |Éles üzem, fejlesztés/tesztelés    |No    |
|Azure Automation-fiók    |Azure Automation támogatja a felügyeletet az infrastruktúra és az alkalmazások életciklusa során. [További](../automation/automation-intro.md)információ.    |Éles üzem, fejlesztés/tesztelés    |No    |
|Log Analytics-munkaterület    |A Azure Monitor egy Log Analytics-munkaterületen tárolja a napló adatokat, amely egy Azure-erőforrás, valamint egy olyan tároló, amelybe az adatok gyűjtése, összesítése és felügyeleti határként szolgál. [További](../azure-monitor/logs/design-logs-deployment.md)információ.    |Éles üzem, fejlesztés/tesztelés    |No    |


<sup>1</sup> a környezet kiválasztása az automanage engedélyezésekor érhető el. [További](automanage-virtual-machines.md#environment-configuration)információ. Emellett módosíthatja a környezet alapértelmezett beállításait, és az ajánlott eljárások korlátain belül beállíthatja a saját beállításait is.


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)