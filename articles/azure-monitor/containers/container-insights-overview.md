---
title: A Container betekintésének áttekintése | Microsoft Docs
description: Ez a cikk azokat a tároló-megállapításokat ismerteti, amelyek az AK Container bepillantást és a általa nyújtott értéket figyelik az AK-fürtök és az Azure-Container Instances állapotának monitorozásával.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 1055e2228c6625ae24e6bf388cf297e3e3363666
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723384"
---
# <a name="container-insights-overview"></a>A Container-információk áttekintése

A Container bepillantást a szolgáltatás a következőre telepített tároló-munkaterhelések teljesítményének figyelésére szolgál:

- Az [Azure Kubernetes szolgáltatásban (ak)](../../aks/intro-kubernetes.md) üzemeltetett fürtök Managed Kubernetes
- Az Azure-ban üzemeltetett önfelügyelt Kubernetes-fürtök az [AK motor](https://github.com/Azure/aks-engine) használatával
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) vagy helyszíni környezetben üzemeltetett, önállóan felügyelt Kubernetes-fürtök
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure arc-kompatibilis Kubernetes](../../azure-arc/kubernetes/overview.md) (előzetes verzió)

A tároló-felismerések támogatják a Linux és a Windows Server 2019 operációs rendszert futtató fürtöket. Az általa támogatott tároló-futtatókörnyezetek a Docker, a Moby és az olyan ICC-kompatibilis futtatókörnyezetek, mint például az ICC-O és a tárolók.

A tárolók monitorozása kritikus fontosságú, különösen akkor, ha éles fürtöt futtat, több alkalmazással.

A tároló-felismerések teljesítménybeli láthatóságot biztosítanak azáltal, hogy a mérőszámok API-n keresztül Kubernetes elérhető vezérlőket, csomópontokat és tárolókat gyűjtenek a memória és a processzor metrikáinak összegyűjtésével. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, a rendszer automatikusan összegyűjti a metrikákat és a naplókat a linuxos Log Analytics-ügynök egy tárolós verziójában. A metrikák a metrikák tárolójába íródnak, és a rendszer a napló adatait a [log Analytics](../logs/log-query-overview.md) munkaterülethez társított naplók tárolójába írja.

![Tároló-felismerési architektúra](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Mit biztosít a tárolók betekintése?

A Container-elemzések átfogó figyelési élményt biztosítanak a Azure Monitor különböző szolgáltatásainak használatával. Ezek a funkciók lehetővé teszik a Linux és a Windows Server 2019 operációs rendszert futtató Kubernetes-fürt teljesítményének és állapotának, valamint a tároló munkaterhelésének megismerését. A Container bepillantást a következőket teheti:

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

Tekintse meg az alábbi videót, amely egy köztes szintű részletes bemutatót biztosít, amely segít megismerni az AK-fürtöket a tároló-megállapításokkal.

> [!VIDEO https://youtu.be/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>Hogyan hozzáférni a szolgáltatáshoz?

A Container bepillantást kétféleképpen érheti el, Azure Monitor vagy közvetlenül a kiválasztott AK-fürtből. A Azure Monitor globális perspektívát biztosít az összes üzembe helyezett tárolóról, amelyek figyelése és nem, így lehetővé teszi az előfizetések és az erőforráscsoportok keresését és szűrését, majd a kiválasztott tárolóból való betekintést nyújt a tárolóba.  Ellenkező esetben közvetlenül egy kiválasztott AK-tárolóból is elérheti a szolgáltatást az AK-ból származó lapról.

![A tároló-információk elérésére szolgáló módszerek áttekintése](./media/container-insights-overview/azmon-containers-experience.png)

Ha az AK-n kívül futó Docker-és Windows-tároló gazdagépek figyelését és felügyeletét érdekli, tekintse meg a [tároló-figyelési megoldást](./containers.md).

## <a name="next-steps"></a>Következő lépések

A Kubernetes-fürt figyelésének megkezdéséhez tekintse át, [hogyan engedélyezheti a tárolók bepillantást](container-insights-onboard.md) a követelmények és az elérhető módszerek megismeréséhez a figyelés engedélyezéséhez.
