---
title: Frissítések és karbantartás – áttekintés
description: Ismerje meg az Azure-beli Virtual Machines szolgáltatásban elérhető frissítéseket és karbantartási lehetőségeket
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022265"
---
# <a name="updates-and-maintenance-overview"></a>Frissítések és karbantartás – áttekintés
Ez a cikk áttekintést nyújt az Azure Virtual Machines (VM) különböző frissítési és karbantartási lehetőségeiről.

## <a name="automatic-os-image-upgrade"></a>Operációs rendszer rendszerképének automatikus frissítése

Az [operációsrendszer-lemezképek automatikus verziófrissítésének](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) engedélyezése a méretezési csoporton belül a méretezési csoport összes példányához tartozó operációsrendszer-lemez biztonságos és automatikus frissítésével megkönnyíti a frissítés kezelését.

Az [operációs rendszer automatikus frissítése](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) a következő jellemzőkkel rendelkezik:

- A konfigurálást követően a lemezkép-közzétevők által közzétett legújabb operációsrendszer-lemezképet a rendszer automatikusan alkalmazza a méretezési csoportra felhasználói beavatkozás nélkül.
- A példányok kötegeit folyamatosan, a kiadó által közzétett új rendszerkép közzétételekor frissíti.
- Integrálható az Application Health-szondákkal és az [alkalmazás-állapot bővítménnyel](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Minden virtuálisgép-mérethez, valamint Windows-és Linux-rendszerképekhez is használható.
- Bármikor letilthatja az automatikus frissítést (az operációs rendszer frissítései manuálisan is elindíthatók).
- A virtuális gép operációsrendszer-lemezét a rendszer a legújabb rendszerkép-verzióval létrehozott új operációsrendszer-lemezzel helyettesíti. A konfigurált bővítmények és az egyéni adatszkriptek futnak, míg a megőrzött adatlemezek megmaradnak.
- A [bővítmények sorrendbe rendezése](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) támogatott.
- Az operációs rendszer rendszerképének automatikus frissítése bármilyen méretű méretezési csoporton engedélyezhető.


## <a name="automatic-vm-guest-patching-preview"></a>Automatikus VM vendég javítás (előzetes verzió)

Az Azure-beli virtuális gépekhez készült automatikus virtuálisgép- [javítás](automatic-vm-guest-patching.md) engedélyezésével a biztonsági megfelelőség fenntartása érdekében biztonságosan és automatikusan javíthatja a virtuális gépeket.

Az [automatikus VM-vendég javításának](automatic-vm-guest-patching.md) jellemzői a következők:
- A *kritikus* vagy *biztonsági* besorolású javítások automatikusan letöltődnek és alkalmazhatók a virtuális gépen.
- A javítások a virtuális gép időzónáján kívüli órákon belül lesznek alkalmazva.
- A javítás-előkészítést az Azure felügyeli, és a javítások a [rendelkezésre állási első alapelveket](automatic-vm-guest-patching.md#availability-first-patching)követik.
- A platform állapotára vonatkozó jelek alapján meghatározott virtuálisgép-állapotot a rendszer figyeli a hibajavítások észlelése érdekében.
- Minden virtuálisgép-mérettel működik.


## <a name="automatic-extension-upgrade-preview"></a>Automatikus bővítmény frissítése (előzetes verzió)

Az [automatikus bővítmény frissítése](automatic-extension-upgrade.md) az Azure-beli virtuális gépek és az Azure Virtual Machine Scale sets előzetes verziójában érhető el. Ha engedélyezve van az automatikus bővítmény frissítése a virtuális gépen vagy a méretezési csoporton, a bővítmény automatikusan frissül, amikor a bővítmény közzétevője új verziót szabadít fel az adott bővítmény számára.

 Az automatikus bővítmény frissítése a következő funkciókkal rendelkezik:
- Azure-beli virtuális gépek és Azure-Virtual Machine Scale Sets esetén támogatott. A Service Fabric Virtual Machine Scale Sets jelenleg nem támogatottak.
- A frissítések a rendelkezésre állási első üzembe helyezési modellben lesznek alkalmazva.
- Virtuálisgép-méretezési csoport esetén a méretezési csoport virtuális gépei nem több mint 20%-a lesz frissítve egyetlen kötegben. A köteg minimális mérete egy virtuális gép.
- Minden virtuálisgép-mérettel működik, valamint Windows-és Linux-bővítmények esetén is.
- Bármikor letilthatja az automatikus frissítést.
- Az automatikus bővítmények frissítése bármilyen méretű Virtual Machine Scale Sets engedélyezhető.
- Minden támogatott bővítmény regisztrálása külön történik, és kiválaszthatja, hogy mely bővítmények legyenek automatikusan frissítve.
- Minden nyilvános felhőalapú régióban támogatott.

## <a name="hotpatch"></a>HotPatching melletti telepítéséről 

A [javítás](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) új módszer a frissítések telepítéséhez olyan új Windows Server Azure Edition rendszerű virtuális gépeken (VM), amelyeken a telepítés után nem szükséges újraindítás. A Windows Server Azure Edition rendszerű virtuális gépek HotPatching melletti telepítéséről a következő előnyökkel jár:

- Alacsonyabb munkaterhelés-hatás kevesebb újraindítással
- A frissítések gyorsabb üzembe helyezése, mivel a csomagok kisebbek, gyorsabbak, és egyszerűbben javítható az Azure Update Manager javítása
- Jobb védelem, mivel a HotPatching melletti telepítéséről frissítési csomagjai a Windows biztonsági frissítéseire vannak korlátozva, amelyek az újraindítás nélkül gyorsabban települnek


## <a name="azure-update-management"></a>Azure Update-kezelés

Az Azure-ban, a helyszíni környezetekben és más felhőalapú környezetekben lévő Windows-és Linux-alapú virtuális gépek operációsrendszer-frissítéseinek kezeléséhez [Azure Automation Update Management](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) is használhatja. Gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítéseinek telepítésének folyamatát.

## <a name="maintenance-control"></a>Karbantartásszabályozás

A [karbantartási vezérlő](maintenance-control.md)használatával olyan platform-frissítéseket kezelhet, amelyek nem igényelnek újraindítást. Az Azure gyakran frissíti az infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindításának javítása érdekében. A legtöbb frissítés átlátható a felhasználók számára. Bizonyos érzékeny munkaterhelések, például a játékok, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók nem tolerálják a virtuális gépek lefagyasztásának vagy leválasztásának néhány másodpercét. A karbantartási ellenőrzés lehetőséget biztosít a platform frissítéseinek megvárni, és egy 35 napos időszakon belül alkalmazza őket. 

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre.

A [karbantartási ellenőrzéssel](maintenance-control.md)a következőket teheti:
- A Batch frissítése egyetlen frissítési csomagba.
- Várjon akár 35 napra a frissítések alkalmazásához. 
- A platform frissítéseinek automatizálása karbantartási ütemterv konfigurálásával vagy [Azure functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)használatával.
- A karbantartási konfigurációk az előfizetések és az erőforráscsoportok között működnek. 


## <a name="scheduled-events"></a>Ütemezett események

Scheduled Events egy Azure-Metadata Service, amely lehetővé teszi az alkalmazási idő előkészítését a virtuális gép (VM) karbantartásához. Információt nyújt a közelgő karbantartási eseményekről (például újraindítás), hogy az alkalmazás előkészítse őket, és korlátozza a megszakadást. Minden Azure Virtual Machines típushoz elérhető, beleértve a Pásti és a IaaS-t is Windows és Linux rendszeren. 

További információ a Scheduled Eventsről: [Scheduled Events a Windows rendszerű virtuális gépekhez](./windows/scheduled-events.md) és a [Linux-Scheduled Eventshoz](./linux/scheduled-events.md)

## <a name="next-steps"></a>Következő lépések

Tekintse át a [rendelkezésre állási és méretezési](availability.md) dokumentációt, amely további lehetőségeket kínál az alkalmazások és szolgáltatások üzemidőének növelésére. 