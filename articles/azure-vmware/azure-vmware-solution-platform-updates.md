---
title: Platform frissítései Azure VMware-megoldáshoz
description: Ismerje meg az Azure VMware-megoldás platformjának frissítéseit.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 73bd1d088f9055ebd80a28c6247ea9dfa6229093
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586233"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Platform frissítései Azure VMware-megoldáshoz

Az Azure VMware-megoldás fontos frissítései a 2021. márciusi naptól kezdődően lépnek életbe. Értesítést kap a Azure Service Healthon keresztül, amely tartalmazza a karbantartás ütemezését. Az Azure VMware megoldás legfontosabb frissítési folyamataival és funkcióival kapcsolatos további információkért lásd: [Azure VMware-megoldás saját Felhőbeli frissítései és](concepts-upgrades.md)frissítései.

## <a name="march-15-2021"></a>Március 15., 2021 

- Az Azure VMware Solution Service a 20201. március 19-én hajtja végre a karbantartási műveleteket, hogy frissítse a vCenter-kiszolgálót a saját felhőben, hogy vCenter Server 6,7-es frissítést a 3l verzióra.

- Ebben az időszakban a VMware vCenter nem lesz elérhető, és nem fogja tudni kezelni a virtuális gépeket (leállítás, indítás, létrehozás, törlés). A privát felhő skálázása (kiszolgálók és fürtök hozzáadása/eltávolítása) nem lesz elérhető. A VMware magas rendelkezésre állása (HA) továbbra is működni fog a meglévő virtuális gépek védelmének biztosítása érdekében. 
 
További információ erről a vCenter-verzióról: [VMware vCenter Server 6,7-es frissítés a 3L kiadási megjegyzéseit](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>2021. március 4.

- Az Azure VMware-megoldások a 2021. március 15-én, a meglévő privát VMware ESXi felhőkben, a [202011002 6,7-es verziójában, a patch Release ESXi670-](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- A vSphere [-verem VMSA-2021-0002-](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)es számú, a 2021-es március 15-én alkalmazott dokumentált megkerülő megoldásait is alkalmazza a rendszer.

>[!NOTE]
>Ez nem zavaró, és nem befolyásolhatja az Azure VMware-szolgáltatásokat vagy munkaterheléseket. A karbantartás során különböző VMware-riasztások, például a _DVPorts hálózati kapcsolata_ és a _DVPorts elveszett kimenő redundancia a_ vCenter-ben jelenik meg, és a karbantartás előrehaladtával automatikusan törlődik.

## <a name="post-update"></a>Frissítés utáni
Ha elkészült, a VMware-összetevők újabb verziói jelennek meg. Ha bármilyen problémát észlel, vagy kérdése van, forduljon a támogatási csapathoz egy támogatási jegy megnyitásával.





