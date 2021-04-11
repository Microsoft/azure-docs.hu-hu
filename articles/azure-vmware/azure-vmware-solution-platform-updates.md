---
title: Platform frissítései Azure VMware-megoldáshoz
description: Ismerje meg az Azure VMware-megoldás platformjának frissítéseit.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045212"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Platform frissítései Azure VMware-megoldáshoz

Az Azure VMware-megoldás a 2021. márciustól kezdődően fontos frissítéseket fog alkalmazni. Értesítést kap a Azure Service Healthon keresztül, amely tartalmazza a karbantartás ütemezését. További információ: [Azure VMware-megoldás saját Felhőbeli frissítései és frissítései](concepts-upgrades.md).

## <a name="march-24-2021"></a>Március 24., 2021
Minden új Azure VMware-megoldás privát felhők üzembe helyezése a VMware vCenter 6,7 U3l és a NSX-T 3.1.1-es verziójával történik. A fent említett kiadásokra a meglévő privát felhők frissülnek, és **június 2021** -én frissítve lesznek.

A tervezett karbantartási dátummal és időponttal ellátott e-mailt fog kapni. Újraütemezhet egy frissítést. Az e-mail emellett részletesen ismerteti a frissített összetevőt, annak hatását a számítási feladatokra, a privát felhő-hozzáférésre és más Azure-szolgáltatásokra.  Egy órával a frissítés előtt értesítést kap, majd újra befejeződik.

## <a name="march-15-2021"></a>Március 15., 2021 

- Az Azure VMware megoldás szolgáltatás a 2021. **március 19-** én végzi a karbantartási munkálatokat a vCenter-kiszolgáló frissítéséhez a saját felhőben, hogy vCenter Server 6,7 a 3l-es verzió frissítését.

- A VMware vCenter ebben az időszakban nem lesz elérhető.  Így nem fogja tudni felügyelni a virtuális gépeket (leállítás, indítás, létrehozás, törlés) vagy a saját felhőalapú méretezését (kiszolgálók és fürtök hozzáadása/eltávolítása). Azonban a VMware magas rendelkezésre állása (HA) továbbra is működni fog a meglévő virtuális gépekkel szembeni védelem érdekében. 
 
További információ erről a vCenter-verzióról: [VMware vCenter Server 6,7-es frissítés a 3L kiadási megjegyzéseit](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>2021. március 4.

- Az Azure VMware-megoldás a [VMware ESXi 6,7-es verzióra, a ESXi670 202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) a meglévő privát gépekre **2021. március 15**-én fogja alkalmazni a javítást.

- A vSphere [-verem VMSA-2021-0002-](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)es számú, a 2021-es **március 15**-én alkalmazott dokumentált megkerülő megoldásait is alkalmazza a rendszer.

>[!NOTE]
>Ez nem zavaró, és nem befolyásolhatja az Azure VMware-szolgáltatásokat vagy munkaterheléseket. A karbantartás során különböző VMware-riasztások, például a _DVPorts hálózati kapcsolata_ és a _DVPorts elveszett kimenő redundancia a_ vCenter-ben jelenik meg, és a karbantartás előrehaladtával automatikusan törlődik.

## <a name="post-update"></a>Frissítés utáni
Ha elkészült, a VMware-összetevők újabb verziói jelennek meg. Ha bármilyen problémát észlel, vagy kérdése van, forduljon a támogatási csapathoz egy támogatási jegy megnyitásával.