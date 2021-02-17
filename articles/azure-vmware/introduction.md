---
title: Bevezetés
description: Ismerje meg az Azure VMware-megoldás funkcióit és előnyeit a VMware-alapú számítási feladatok Azure-beli üzembe helyezéséhez és kezeléséhez.
ms.topic: overview
ms.date: 11/11/2020
ms.openlocfilehash: 255d3599385c60d3b13f4769796ced41a1177311
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579287"
---
# <a name="what-is-azure-vmware-solution"></a>Mi az az Azure VMware Solution?

Az Azure VMware megoldás olyan privát felhőket biztosít, amelyek a dedikált operációs rendszer nélküli Azure-infrastruktúrából készült vSphere-fürtöket tartalmaznak. A minimális kezdeti üzembe helyezés három gazdagép, de egyszerre további gazdagépek is hozzáadhatók, legfeljebb 16 gazdagépre.  Az összes kiosztott privát felhők vCenter Server, vSAN, vSphere és NSX-T. Áttelepítheti a munkaterheléseket a helyszíni környezetekről, üzembe helyezhet új virtuális gépeket (VM-ket), és az Azure-szolgáltatásokat saját felhőkből is felhasználhatja.

Az Azure VMware-megoldás egy VMware-alapú ellenőrzött megoldás, amely a fejlesztés és a frissítés folyamatos ellenőrzésével és tesztelésével foglalkozik. A Microsoft saját felhőalapú infrastruktúrát és szoftvereket kezel és tart fenn. Lehetővé teszi a munkaterhelések kiépítését és futtatását a saját felhőkben. 

Az ábrán látható a privát felhők és a virtuális hálózatok közötti érintkezés az Azure-ban, az Azure-szolgáltatások és a helyszíni környezetek között. A privát felhőktől az Azure-szolgáltatásokhoz vagy virtuális hálózatok való hálózati hozzáférés az Azure szolgáltatási végpontok SLA-alapú integrálását teszi lehetővé. A ExpressRoute Global Reach összekapcsolja a helyszíni környezetet az Azure VMware-megoldás saját felhővel. 

![Az Azure VMware-megoldás saját felhővel való érintkezésének képe az Azure-ba és a helyszínen](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Gazdagépek, fürtök és privát felhők

Az Azure VMware-megoldás privát felhők és fürtök egy operációs rendszer nélküli, Hyper-konvergens Azure-infrastruktúra-gazdagépről készültek. A magas szintű gazdagépek 576 GB RAM-mal és kettős Intel 18 mag, 2,3 GHz-es processzorokkal rendelkeznek. A vSAN két, 15,36 TB-os (SSD) vSAN és egy 3,2 TB (NVMe) vSAN gyorsítótár-réteggel rendelkező diskgroups rendelkezik.

Az új privát felhők üzembe helyezése a Azure Portal vagy az Azure CLI használatával történik.

## <a name="networking"></a>Hálózatkezelés

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

További információ: [hálózatkezelési fogalmak](concepts-networking.md).

## <a name="access-and-security"></a>Hozzáférés és biztonság

Az Azure VMware-megoldás privát felhői vSphere szerepköralapú hozzáférés-vezérlést használnak a fokozott biztonsághoz. A vSphere SSO LDAP-funkciókat integrálhatja Azure Active Directoryokkal. További információkért tekintse meg a [hozzáférési és identitási fogalmakat](concepts-identity.md).  

a vSAN inaktív adatok titkosítása alapértelmezés szerint engedélyezve van, és a vSAN adattár biztonságának biztosítására szolgál. További információ: [tárolási fogalmak](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>A gazdagép és a szoftver életciklusának karbantartása

Az Azure VMware-megoldás saját felhő-és VMware-szoftverének rendszeres frissítése biztosítja, hogy a legújabb biztonsági, stabilitási és szolgáltatási készletek a privát felhőkben fussanak. További információ: [Private Cloud Updates and Upgrades](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Saját felhő figyelése

Miután telepítette az Azure VMware-megoldást az előfizetésbe, [Azure monitor naplók](../azure-monitor/overview.md) automatikusan létrejönnek. 

A saját felhőben a következőket teheti:
- Gyűjtsön naplókat az egyes virtuális gépeken.
- [Töltse le és telepítse az MMA-ügynököt](../azure-monitor/agents/log-analytics-agent.md#installation-options) Linux és Windows rendszerű virtuális gépekre.
- Engedélyezze az [Azure Diagnostics bővítményt](../azure-monitor/agents/diagnostics-extension-overview.md).
- [Hozzon létre és futtasson új lekérdezéseket](../azure-monitor/logs/data-platform-logs.md#log-queries).
- Futtassa ugyanazokat a lekérdezéseket, amelyeket általában futtat a virtuális gépeken.

Az Azure VMware-megoldáson belüli figyelési minták hasonlóak az Azure-beli virtuális gépekhez a IaaS platformon belül. További információk és útmutatók: Azure-beli [virtuális gépek figyelése Azure monitorokkal](../azure-monitor/vm/monitor-vm-azure.md).

## <a name="next-steps"></a>Következő lépések

A következő lépés a [saját felhő és a fürtök legfontosabb alapfogalmai](concepts-private-clouds-clusters.md)megismerése.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
