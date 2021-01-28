---
title: A tárolók Azure Monitor áttekintése | Microsoft Docs
description: Ez a cikk azokat a tárolókat ismerteti Azure Monitor, amelyek az AK-tárolók bevezetési megoldását figyelik, valamint a általa nyújtott értéket az AK-fürtök és az Azure-Container Instances állapotának figyelésével.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a21a0e506082b58473870baed014ede16665e837
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943796"
---
# <a name="azure-monitor-for-containers-overview"></a>Tárolókhoz készült Azure Monitor – áttekintés

A tárolók Azure Monitor a-ben üzembe helyezett tároló-munkaterhelések teljesítményének figyelésére szolgáló szolgáltatás:

- Az [Azure Kubernetes szolgáltatásban (ak)](../../aks/intro-kubernetes.md) üzemeltetett fürtök Managed Kubernetes
- Az Azure-ban üzemeltetett önfelügyelt Kubernetes-fürtök az [AK motor](https://github.com/Azure/aks-engine) használatával
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) vagy helyszíni környezetben üzemeltetett, önállóan felügyelt Kubernetes-fürtök
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure arc-kompatibilis Kubernetes](../../azure-arc/kubernetes/overview.md) (előzetes verzió)

A tárolók Azure Monitor támogatja a Linux és a Windows Server 2019 operációs rendszert futtató fürtöket. Az általa támogatott tároló-futtatókörnyezetek a Docker, a Moby és az olyan ICC-kompatibilis futtatókörnyezetek, mint például az ICC-O és a tárolók.

A tárolók monitorozása kritikus fontosságú, különösen akkor, ha éles fürtöt futtat, több alkalmazással.

A tárolók Azure Monitor a teljesítmény láthatóságát azáltal, hogy a mérőszámok API-n keresztül Kubernetes elérhető vezérlőket, csomópontokat és tárolókat gyűjt a memória és a processzor metrikáinak összegyűjtéséhez. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, a rendszer automatikusan összegyűjti a metrikákat és a naplókat a linuxos Log Analytics-ügynök egy tárolós verziójában. A metrikák a metrikák tárolójába íródnak, és a rendszer a napló adatait a [log Analytics](../log-query/log-query-overview.md) munkaterülethez társított naplók tárolójába írja.

![Azure Monitor a tárolók architektúrához](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-azure-monitor-for-containers-provide"></a>Mit nyújt a tárolók Azure Monitor?

A tárolók Azure Monitor a Azure Monitor különböző funkcióit használó átfogó figyelési élményt nyújt. Ezek a funkciók lehetővé teszik a Linux és a Windows Server 2019 operációs rendszert futtató Kubernetes-fürt teljesítményének és állapotának, valamint a tároló munkaterhelésének megismerését. A tárolókhoz készült Azure Monitor használatával megteheti a következőket:

* Azonosítsa a csomóponton futó AK-tárolókat, valamint a processzor és a memória átlagos kihasználtságát. Ez az információ segít azonosítani az erőforrás szűk keresztmetszeteit.
* Azonosítsa a tároló-csoportok és a Azure Container Instancesban üzemeltetett tárolók processzorát és memóriájának kihasználtságát.
* Annak meghatározása, hogy a tároló hol található egy vezérlőben vagy egy Pod-ban. Ez a tudás segítséget nyújt a vezérlő vagy a pod teljes teljesítményének megtekintéséhez.
* Tekintse át a gazdagépen futó számítási feladatok erőforrás-kihasználtságát, amelyek nem kapcsolódnak a pod-t támogató szabványos folyamatokhoz.
* Ismerje meg a fürt viselkedését az átlag és a legnagyobb terhelések alatt. Ez a tudás segíthet a kapacitási igények azonosításában és a fürt által fenntartott maximális terhelés meghatározásában.
* Riasztásokat állíthat be, amelyekkel proaktívan értesítheti Önt, vagy rögzítheti azt, ha a csomópontokon vagy tárolókban a CPU és a memória kihasználtsága túllépi a küszöbértékeket, vagy ha az infrastruktúra vagy a csomópontok állapota összesítéssel a fürt állapota megváltozik.
* A [Prometheus](https://prometheus.io/docs/introduction/overview/) -nal integrálva megtekintheti a csomópontokból és Kubernetes származó alkalmazások és munkaterhelések mérőszámait, és [lekérdezésekkel](container-insights-log-search.md) egyéni riasztásokat, irányítópultokat és részletes elemzéseket készíthet.
* Figyelheti a tároló munkaterheléseit [, amelyek](https://github.com/Azure/aks-engine) a helyszíni és az [AK](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)-os motoron üzemelnek Azure stack.
* [Az Azure Red Hat OpenShift üzembe helyezett tároló-](../../openshift/intro-openshift.md)munkaterhelések figyelése.

    >[!NOTE]
    >Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
    >

* [Az Azure arc-kompatibilis Kubernetes (előzetes verzió) üzembe helyezett](../../azure-arc/kubernetes/overview.md)tároló munkaterhelések figyelése.

A Windows Server-fürtök Linux-fürthöz képest történő figyelésének fő eltérései a következők:

- A Windows nem rendelkezik memóriabeli RSS-metrikával, ezért a Windows-csomópontok és-tárolók esetében nem érhető el. A [munkakészlet](/windows/win32/memory/working-set) metrikája elérhető.
- A lemezes tárolás kapacitására vonatkozó információk nem érhetők el Windows-csomópontok esetén.
- Csak a pod környezetek figyelhetők meg, nem pedig a Docker-környezetek.
- Az előzetes kiadásban legfeljebb 30 Windows Server-tároló támogatott. Ez a korlátozás nem vonatkozik a Linux-tárolók esetében.

Tekintse meg az alábbi videót, amely egy köztes szintű részletes bemutatót biztosít, amely segít megismerni az AK-fürt Azure Monitor for containers szolgáltatással való figyelését.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hogyan hozzáférni a szolgáltatáshoz?

A tárolók Azure Monitor kétféleképpen, Azure Monitor vagy közvetlenül a kiválasztott AK-fürtből érheti el. A Azure Monitor globális perspektívát biztosít az összes üzembe helyezett tárolóról, amelyeket figyel, és amelyek nem, így lehetővé teszi az előfizetések és erőforráscsoportok keresését és szűrését, majd a kiválasztott tárolóból származó tárolók Azure Monitorének részletezését.  Ellenkező esetben közvetlenül egy kiválasztott AK-tárolóból is elérheti a szolgáltatást az AK-ból származó lapról.

![A tárolók Azure Monitor elérési módszereinek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha az AK-n kívül futó Docker-és Windows-tároló gazdagépek figyelését és felügyeletét érdekli, tekintse meg a [tároló-figyelési megoldást](./containers.md).

## <a name="next-steps"></a>Következő lépések

A Kubernetes-fürt figyelésének megkezdéséhez tekintse át, [hogyan engedélyezheti a tárolók Azure monitor](container-insights-onboard.md) a figyelés engedélyezéséhez szükséges követelmények és a rendelkezésre álló módszerek megismerése érdekében.

