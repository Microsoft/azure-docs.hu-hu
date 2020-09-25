---
title: Fogalmak – a privát felhő frissítései és frissítései
description: Ismerje meg az Azure VMware-megoldás legfontosabb frissítési folyamatait és funkcióit.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 380e97eae559145a9ef5ed7b6e7bf14f18039eed
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316800"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware-megoldás saját Felhőbeli frissítései és frissítései

## <a name="overview"></a>Áttekintés

Az Azure VMware-megoldás privát felhők egyik legfőbb előnye, hogy a platform karbantartás alatt áll. A platform karbantartása magában foglalja a VMware ellenőrzött szoftvercsomag automatizált frissítéseit, így biztosítva, hogy az ellenőrzött Azure VMware-megoldás saját felhőalapú szoftverének legújabb verzióját használja.

Pontosabban, az Azure VMware-megoldás saját felhője a következőket tartalmazza:

- VMware ESXi hypervisorsal kiépített dedikált operációs rendszer nélküli kiszolgálói csomópontok 
- vCenter-kiszolgáló az ESXi és a vSAN kezeléséhez 
- VMware NSX-T szoftver által definiált hálózatkezelés vSphere számítási feladatokhoz virtuális gépek számára  
- VMware vSAN adattár vSphere számítási feladatokhoz virtuális gépekhez  
- VMware HCX a számítási feladatok mobilitásához  

Ezen összetevőkön kívül az Azure VMware megoldás saját felhője az Azure alátétben lévő, a kapcsolathoz és a privát felhő üzemeltetéséhez szükséges erőforrásokat is tartalmazza. Az Azure VMware megoldás folyamatosan figyeli az alátét és a VMware-összetevők állapotát. Ha az Azure VMware-megoldás hibát észlel, végrehajtja a sikertelen összetevők kijavítását. 

## <a name="what-components-get-updated"></a>Milyen összetevőket kell frissíteni?   

Az Azure VMware-megoldás a következő VMware-összetevőket frissíti: 

- az operációs rendszer nélküli kiszolgálók csomópontjain futó vCenter Server és ESXi 
- vSAN 
- NSX – T 

Az Azure VMware megoldás az alátéten lévő szoftvereket is frissíti, például az illesztőprogramokat, a hálózati kapcsolókon lévő szoftvereket és az operációs rendszer nélküli csomópontokon található belső vezérlőprogramot. 

## <a name="types-of-updates"></a>A frissítések típusai

Az Azure VMware-megoldás a következő típusú frissítéseket alkalmazza a VMware-összetevőkhöz:

- Javítások: a VMware által kiadott biztonsági javítások és hibajavítások. 
- Frissítések: egy vagy több VMware-összetevő másodlagos verziója. 
- Frissítések: egy vagy több VMware-összetevő főverziójának frissítései.

A rendszer a javítások előtt és után is értesíti a privát felhőkre. Emellett a karbantartási időszakot is ütemezhetjük a frissítések vagy a saját felhőre történő verziófrissítések alkalmazása előtt. 

## <a name="vmware-appliance-backup"></a>VMware készülék biztonsági mentése 

A frissítések mellett az Azure VMware-megoldás a következő VMware-összetevők konfigurációs biztonsági másolatát is végrehajtja:

- vCenter Server 
- NSX – T Manager 

Meghibásodás esetén az Azure VMware-megoldás visszaállíthatja ezeket a konfiguráció biztonsági másolatából. 

A VMware-szoftverekkel kapcsolatos további információkért lásd a [privát felhők és fürtök fogalmát ismertető cikket](concepts-private-clouds-clusters.md) és a [gyakori kérdéseket](faq.md).

## <a name="next-steps"></a>Következő lépések

A következő lépés [egy privát felhő létrehozása](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
