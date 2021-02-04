---
title: Fogalmak – a privát felhő frissítései és frissítései
description: Ismerje meg az Azure VMware-megoldás legfontosabb frissítési folyamatait és funkcióit.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 2d5f789304cc264f07aad1865b0bcb2ed4182d4b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536984"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware-megoldás saját Felhőbeli frissítései és frissítései

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

Most, hogy lefedette az Azure VMware-megoldás legfontosabb frissítési folyamatait és funkcióit, érdemes megismernie az alábbiakat:

- [Privát felhő létrehozása](tutorial-create-private-cloud.md)
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
