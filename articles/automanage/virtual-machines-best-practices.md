---
title: Azure-beli automanage Virtual Machines ajánlott eljárások
description: Ismerje meg az Azure automatikus felügyeletét a Virtual Machines szolgáltatáshoz, amely az automatikusan előkészített és konfigurált szolgáltatások esetében ajánlott eljárások.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 5cdc0080c0d8b06bd0b7e19566b54b4f105648e5
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122102"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Azure-felügyelet a Virtual Machines szolgáltatásban – ajánlott eljárások


Ezeket az Azure-szolgáltatásokat automatikusan előkészíti a rendszer, ha a virtuális gépek automatikus felügyeletét használja. Alapvető fontosságúak az ajánlott eljárásokról szóló tanulmány, amelyet a [felhőalapú bevezetési keretrendszerben](/azure/cloud-adoption-framework/manage/azure-server-management)talál.

Az összes szolgáltatás esetében automatikus bevezetést, automatikus konfigurálást, a drift figyelését, valamint a drift észlelését követően közvetítjük. A folyamattal kapcsolatos további információkért lásd: [Az Azure-beli automanage szolgáltatás virtuális gépeken](automanage-virtual-machines.md).


## <a name="participating-services"></a>Résztvevő szolgáltatások

|Szolgáltatás    |Leírás    |Támogatott profilok<sup>1</sup>    |Támogatott beállítások<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|A VM-alapú adatvizsgálatok figyelése    |Azure Monitor for VMs figyeli a virtuális gépek teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásokra vonatkozó függőségeket. [További](../azure-monitor/vm/vminsights-overview.md)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – éles környezet    |Nem    |
|Backup    |Az Azure Backup független és elkülönített biztonsági másolatokat biztosít, ezzel védelmet nyújtva a virtuális gépeken lévő adatok nem szándékos megsemmisítésével szemben. [További](../backup/backup-azure-vms-introduction.md)információ. A díjak a védett virtuális gépek számán és méretén alapulnak. [További](https://azure.microsoft.com/pricing/details/backup/)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – éles környezet    |Igen    |
|Azure Security Center    |Azure Security Center egy egységes infrastruktúra-alapú biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben futó hibrid számítási feladatokhoz. [További](../security-center/security-center-introduction.md)információ.  Az automanage Beállítja azt az előfizetést, amelyben a virtuális gép a Azure Security Center ingyenes szintű ajánlatában található. Ha az előfizetése már be van Azure Security Centerba, az automatikusan felügyelt nem konfigurálja újra.    |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Nem    |
|Microsoft Antimalware    |Az Azure-hoz készült Microsoft antimalware egy ingyenes valós idejű védelem, amely segít a vírusok, kémprogramok és más kártevő szoftverek azonosításában és eltávolításában. Riasztásokat állít elő, amikor az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken. [További](../security/fundamentals/antimalware.md)információ. |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Igen    |
|Frissítéskezelés    |A virtuális gépek operációs rendszerének frissítéseinek kezeléséhez Azure Automation Update Management is használhatja. Gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítéseinek telepítésének folyamatát. [További](../automation/update-management/overview.md)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Nem    |
|Change Tracking & leltár    |A Change Tracking és a leltár ötvözi a Change Tracking and Inventory függvényeket, így nyomon követheti a virtuális gépek és a kiszolgálói infrastruktúra változásait. A szolgáltatás támogatja a változások nyomon követését a környezetekben, a Daemon-szoftvereket, a beállításjegyzéket és az adott környezetben található fájlokat, így segít a nemkívánatos módosítások diagnosztizálásában és a riasztások felemelésében. A leltár-támogatás lehetővé teszi a vendég erőforrásainak lekérdezését a telepített alkalmazások és egyéb konfigurációs elemek láthatóságának érdekében.  [További](../automation/change-tracking/overview.md)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Nem    |
|Azure Guest Configuration    | A vendég konfigurációs szabályzattal figyelhető a konfiguráció és a jelentés a gép megfelelőségéről. Az automatikus kezelés szolgáltatás a [Windows biztonsági](/windows/security/threat-protection/windows-security-baselines) alapkonfigurációit a vendég konfigurációs bővítménnyel fogja telepíteni. [További](../governance/policy/concepts/guest-configuration.md)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Nem    |
|Azure Automation-fiók    |Azure Automation támogatja a felügyeletet az infrastruktúra és az alkalmazások életciklusa során. [További](../automation/automation-intro.md)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Nem    |
|Log Analytics-munkaterület    |A Azure Monitor egy Log Analytics-munkaterületen tárolja a napló adatokat, amely egy Azure-erőforrás, valamint egy olyan tároló, amelybe az adatok gyűjtése, összesítése és felügyeleti határként szolgál. [További](../azure-monitor/logs/design-logs-deployment.md)információ.    |Azure-beli virtuális gépek – ajánlott eljárások – üzemi, Azure-beli virtuális gépek – ajánlott eljárások – fejlesztés/tesztelés    |Nem    |


<sup>1</sup> a konfigurációs profilok akkor érhetők el, ha engedélyezi az automatikus felügyeletet. [További](automanage-virtual-machines.md)információ. A konfigurációs profil alapértelmezett beállításait is módosíthatja, és saját beállításokat is beállíthat az ajánlott eljárások korlátain belül.


## <a name="next-steps"></a>Következő lépések

Próbálja meg engedélyezni a Azure Portalban található virtuális gépek autofelügyeletét.

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)