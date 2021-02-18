---
title: Fogalmak – a privát felhő frissítései és frissítései
description: Ismerje meg az Azure VMware-megoldás legfontosabb frissítési folyamatait és funkcióit.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: d93453cbf6ad744844a04cd298cc18ad181cc0b0
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634992"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware-megoldás saját Felhőbeli frissítései és frissítései

Az Azure VMware-megoldás privát felhők egyik előnye, hogy a platform karbantartás alatt áll. A karbantartás magában foglalja a VMware ellenőrzött szoftvercsomag automatizált frissítéseit, így biztosítva, hogy az Azure VMware-megoldás saját felhőalapú szoftverének legújabb verzióját használja.

Pontosabban, az Azure VMware-megoldás saját felhője a következőket tartalmazza:

- VMware ESXi hypervisorsal kiépített dedikált operációs rendszer nélküli kiszolgálói csomópontok 
- vCenter-kiszolgáló az ESXi és a vSAN kezeléséhez 
- VMware NSX-T szoftver által definiált hálózatkezelés vSphere számítási feladatokhoz virtuális gépek számára  
- VMware vSAN adattár vSphere számítási feladatokhoz virtuális gépekhez  
- VMware HCX a számítási feladatok mobilitásához  

Az Azure VMware megoldás saját felhője a kapcsolódáshoz és a privát felhő üzemeltetéséhez szükséges Azure alátét erőforrásaival is rendelkezik. Az Azure VMware megoldás folyamatosan figyeli az alátét és a VMware-összetevők állapotát. Ha az Azure VMware-megoldás hibát észlel, végrehajtja a sikertelen összetevők kijavítását. 

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

A rendszer a javítások előtt és után is értesíti a privát felhőkre. Emellett a frissítések vagy a saját felhőre való frissítésük előtt is ütemezhet egy karbantartási időszakot. 

## <a name="vmware-appliance-backup"></a>VMware készülék biztonsági mentése 

Az Azure VMware megoldás a következő VMware-összetevők konfigurációs biztonsági mentését is végrehajtja:

- vCenter Server 
- NSX – T Manager 

Meghibásodás esetén az Azure VMware megoldás képes visszaállítani ezeket az összetevőket a konfiguráció biztonsági másolatából. 

A VMware-szoftverekkel kapcsolatos további információkért lásd a [privát felhők és fürtök fogalmát ismertető cikket](concepts-private-clouds-clusters.md) és a [gyakori kérdéseket](faq.yml).

## <a name="next-steps"></a>Következő lépések

Most, hogy lefedette az Azure VMware-megoldás legfontosabb frissítési folyamatait és funkcióit, érdemes megismernie az alábbiakat:

- [Privát felhő létrehozása](tutorial-create-private-cloud.md)
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
