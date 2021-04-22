---
title: Alapfogalmak – Magánfelhők frissítései és frissítései
description: Ismerje meg a legfontosabb frissítési folyamatokat és szolgáltatásokat a Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ced5832a6d994f6cbc7e659d44ce4f6ac88681d0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876813"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware Solution és frissítései

A magánfelhők Azure VMware Solution egyik előnye, hogy a platformot fenntartják Ön számára. A karbantartás magában foglalja a VMware által ellenőrzött szoftvercsomagok automatikus frissítéseit, így biztosítva, hogy a magánfelhős Azure VMware Solution legújabb verzióját használja.

A magánfelhők Azure VMware Solution a következőket tartalmazzák:

- Dedikált, operációs rendszer nélküli kiszolgálói csomópontok üzembe VMware ESXi hipervizorral 
- vCenter-kiszolgáló AZ ESXi és a vSAN kezeléséhez 
- VMware NSX-T szoftveres hálózat a vSphere számítási feladatok virtuális gépei számára  
- VMware vSAN-adattár vSphere számítási feladatú virtuális gépekhez  
- VMware HCX a számítási feladatok mobilitása érdekében  

A Azure VMware Solution magánfelhő is tartalmazza a kapcsolathoz és a magánfelhő működtetéséhez szükséges Erőforrásokat az Azure-rétegben. Azure VMware Solution monitorozza az aláfedő és a VMware-összetevők állapotát is. Amikor Azure VMware Solution hibát észlel, a meghibásodott összetevők javításához szükséges lépéseket is el kell látnia. 

## <a name="what-components-get-updated"></a>Milyen összetevők frissülnek?   

Azure VMware Solution VMware következő összetevőit frissíti: 

- vCenter Server operációs rendszer nélküli kiszolgálócsomópontokon futó operációs rendszer nélküli kiszolgálók és ESXi-kiszolgálók 
- vSAN 
- NSX-T 

Azure VMware Solution az alsó rétegben lévő szoftvereket is frissíti, például az illesztőprogramokat, a hálózati kapcsolók szoftvereit és az operációs rendszer nélküli csomópontok belső vezérlőprogramját. 

## <a name="types-of-updates"></a>A frissítések típusai

Azure VMware Solution vMware-összetevőkhöz a következő típusú frissítéseket alkalmazza:

- Javítások: A VMware által kiadott biztonsági javítások és hibajavítások. 
- Frissítések: Egy vagy több VMware-összetevő alverzió-frissítései. 
- Frissítések: Egy vagy több VMware-összetevő főverzió-frissítései.

A javítások magánfelhőkre való alkalmazása előtt és után értesítést kap. Emellett egy karbantartási időszak ütemezését is segítjük, mielőtt frissítéseket vagy frissítéseket alkalmaz a magánfelhőre. 

## <a name="vmware-appliance-backup"></a>VMware-berendezés biztonsági mentése 

Azure VMware Solution VMware-összetevők konfigurációs biztonsági másolatát is készíti:

- vCenter Server 
- NSX-T Manager 

Hiba esetén a Azure VMware Solution visszaállíthatja ezeket az összetevőket a konfigurációs biztonsági másolatból. 

## <a name="vmware-software-versions"></a>VMware szoftververziók
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Következő lépések

Most, hogy már megismerte a legfontosabb frissítési folyamatokat és szolgáltatásokat a Azure VMware Solution, a következővel ismerkedhet meg:

- [Magánfelhő létrehozása](tutorial-create-private-cloud.md)
- [Az erőforrás Azure VMware Solution engedélyezése](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
