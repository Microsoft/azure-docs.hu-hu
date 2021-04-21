---
title: Bevezetés
description: Megismerheti a VMware-Azure VMware Solution számítási feladatok Azure-ban való üzembe helyezésének és kezelésének funkcióit és előnyeit. Azure VMware Solution SLA garantálja, hogy az Azure VMware felügyeleti eszközök (vCenter Server és NSX Manager) az idő legalább 99,9%-ában elérhetők lesznek.
ms.topic: overview
ms.date: 04/20/2021
ms.openlocfilehash: 9471aa2e427d28e0b8211dc8b25b2e61a4bfa8c4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752641"
---
# <a name="what-is-azure-vmware-solution"></a>Mi az az Azure VMware Solution?

Azure VMware Solution olyan magánfelhőket biztosít, amelyek dedikált, operációs rendszer nélküli Azure-infrastruktúrából készült vSphere-fürtökből épülnek fel. A minimális kezdeti üzembe helyezés három gazdagép, de egyszerre legfeljebb 16 gazdagépet lehet hozzáadni, fürtönként legfeljebb 16 gazdagépet.  Minden kiépített magánfelhő rendelkezik vCenter Server, vSAN, vSphere és NSX-T környezetekkel. A számítási feladatokat a helyszíni környezetből telepítheti át, új virtuális gépeket helyezhet üzembe, és azure-szolgáltatásokat használhat a magánfelhőkből.  Az Azure VMware felügyeleti eszközök (vCenter Server NSX Manager) az idő legalább 99,9%-ában elérhetők lesznek. További információkért lásd: Azure VMware Solution [SLA.](https://aka.ms/avs/sla)

Azure VMware Solution VMware által ellenőrzött megoldás, amely a fejlesztéseket és frissítéseket ellenőrzi és teszteli. A Microsoft felügyeli és tartja karban a magánfelhő-infrastruktúrát és -szoftvereket. Így a számítási feladatok magánfelhőkben való fejlesztésére és futtatására összpontosíthat. 

Az ábrán a magánfelhők és az Azure-beli virtuális hálózatok, az Azure-szolgáltatások és a helyszíni környezetek közötti kapcsolat látható. A magánfelhők azure-szolgáltatásokhoz vagy virtuális hálózatokhoz való hálózati hozzáférése az Azure-szolgáltatásvégpont SLA-alapú integrációját biztosítja. Az ExpressRoute Global Reach csatlakoztatja a helyszíni környezetet a Azure VMware Solution magánfelhőhöz. 

![Magánfelhő Azure VMware Solution Azure-ral és helyszíni környezetekkel való szomszédosságot](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és magánfelhők

Azure VMware Solution és fürtök operációs rendszer nélküli, hiperkonvergens Azure-infrastruktúragazdaból épülnek fel. A csúcshálózati gazdagépek 576 GB RAM-mal és kettős Intel 18 maggal, 2,3 GHz-es processzorral vannak felhozva. A HE-gazdagépek két vSAN-lemezcsoporttal rendelkeznek, amelyek 15,36 TB (SSD) nyers vSAN-kapacitásszinttel és 3,2 TB -os (NVMe) vSAN-gyorsítótárszinttel rendelkeznek.

Az új magánfelhők üzembe helyezése a Azure Portal Azure CLI használatával.

## <a name="networking"></a>Hálózatkezelés

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

További információ: [Hálózati fogalmak.](concepts-networking.md)

## <a name="access-and-security"></a>Hozzáférés és biztonság

Azure VMware Solution magánfelhők vSphere szerepköralapú hozzáférés-vezérlést használnak a fokozott biztonság érdekében. A vSphere SSO LDAP-képességeit integrálhatja a Azure Active Directory. További információkért lásd a [hozzáféréssel és identitással kapcsolatos fogalmakat.](concepts-identity.md)  

A vSAN adattár-biztonság érdekében alapértelmezés szerint engedélyezve van a vSAN-adattár titkosítása. További információ: [Storage concepts (Tárolási fogalmak).](concepts-storage.md)

## <a name="host-and-software-lifecycle-maintenance"></a>A gazdagép és a szoftver életciklusának karbantartása

A felhőalapú Azure VMware Solution VMware szoftver rendszeres frissítései biztosítják a legújabb biztonsági, stabilitási és szolgáltatáskészletek futtatását a magánfelhőkben. További információ: [Magánfelhők frissítései és frissítései.](concepts-upgrades.md)

## <a name="monitoring-your-private-cloud"></a>A magánfelhő monitorozása

A Azure VMware Solution üzembe helyezése után a rendszer automatikusan [Azure Monitor naplókat.](../azure-monitor/overview.md) 

A magánfelhőben a következőt használhatja:
- Gyűjtse össze az egyes virtuális gépek naplóit.
- [Töltse le és telepítse az MMA-ügynököt](../azure-monitor/agents/log-analytics-agent.md#installation-options) Linux és Windows rendszerű virtuális gépeken.
- Engedélyezze az [Azure Diagnostics bővítményt.](../azure-monitor/agents/diagnostics-extension-overview.md)
- [Új lekérdezések létrehozása és futtatása.](../azure-monitor/logs/data-platform-logs.md#log-queries)
- Futtassa a virtuális gépeken általában futtatott lekérdezéseket.

A monitorozási minták a Azure VMware Solution hasonlóak az IaaS-platformon belüli Azure-beli virtuális gépekhez. További információkért és további tudnivalókért lásd: Azure-beli virtuális gépek [monitorozása Azure Monitor.](../azure-monitor/vm/monitor-vm-azure.md)

## <a name="customer-communication"></a>Ügyfél-kommunikáció
[!INCLUDE [customer-communications](includes/customer-communications.md)]

## <a name="next-steps"></a>Következő lépések

A következő lépés a magánfelhővel és a fürtök alapvető [fogalmaival ismerkedik meg.](concepts-private-clouds-clusters.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md


