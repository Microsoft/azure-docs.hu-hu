---
title: Az Azure Container Instances és tárolóvezénylés
description: Ismerje meg, hogyan az Azure container instances használatához tárolóvezénylőt az.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: df9c3ecbec6dccd9ba8db2b375cfab3276005098
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072991"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Az Azure Container Instances és tárolóvezénylőt

Kis méret és alkalmazás tájolást tárolók kifejezetten a mikroszolgáltatás-alapú architektúrák és Agilis kézbesítési környezetben. A feladat automatizált és felügyelt tárolókat és azok működését nagy számú, más néven *vezénylési*. Népszerű tárolóvezénylőt közé tartozik a Kubernetes, DC/OS és Docker Swarm.

Az Azure Container Instances néhány vezénylési platformokra alapszintű ütemezési funkciókat biztosít. És nem terjed ki a magasabb értékű szolgáltatások platformokhoz biztosító, amíg az Azure Container Instances kiegészíti azokat. Ez a cikk ismerteti az Azure Container Instances kezeli, és teljes tárolóvezénylőt előfordulhat, hogy mobiljelentésre annak hatókörén.

## <a name="traditional-orchestration"></a>Hagyományos vezénylési

Vezénylési standard definícióját a következő feladatokból áll:

- **Ütemezés**: Adja meg egy tárolórendszerképet, és a egy erőforrás-kérelemben, keresse meg egy megfelelő gép, amelyen a tároló futtatásához.
- **Kapcsolat/Anti-affinity**: Adja meg, hogy a tárolók kell a Futtatás közeli hasznos helyek egymással (a teljesítmény) vagy elég távolságra (a rendelkezésre állás érdekében).
- **Szolgáltatásállapot-figyelést**: Tekintse meg a tároló hibák, és automatikusan le őket.
- **Feladatátvétel**: Nyomon követheti az összes olyan számítógépen futó, és ütemezze újra a sikertelen gépekről tárolók a megfelelő állapotú csomópontokat.
- **Skálázás**: Adja hozzá, vagy távolítsa el a megfelelő igény szerint, manuálisan vagy automatikusan a container Instances szolgáltatásban.
- **Hálózatkezelés**: Adjon meg egy fölérétegzett hálózatot a gazdagép több gép közötti kommunikáció koordináló tárolók.
- **A szolgáltatásészlelés**: Tárolók keresse meg egymással automatikusan, még akkor is, mivel azok között gazdagépekre, és módosítsa az IP-címek engedélyezése.
- **Alkalmazásfrissítések koordinált**: Alkalmazás üzemszünet elkerülése érdekében a tároló frissítések kezelése, és a visszaállítás engedélyezése, ha valami probléma merül fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Vezénylési az Azure Container Instances szolgáltatással: A rétegelt megközelítést

Az Azure Container Instances lehetővé teszi a réteges megközelítésének szervezését, minden egyetlen tároló, miközben lehetővé teszi az orchestrator platform többtárolós feladatait megkönnyítése végrehajtásához szükséges ütemezés és a felügyeleti képességet biztosít.

Az Azure container Instances az alapul szolgáló infrastruktúra kezeli, mivel az orchestrator platform nem kell magát a keresés, egy megfelelő gazdagép egyetlen tároló futtatására vonatkoznak. A felhő rugalmasságát biztosítja, hogy egy mindig elérhető. Ehelyett az orchestrator összpontosíthat a feladatok, amelyek megkönnyítik a több tároló-architektúrák, beleértve a méretezés és összehangolt frissítése.

## <a name="scenarios"></a>Forgatókönyvek

Az orchestrator-integráció az Azure Container Instances használatával pedig továbbra is születőfélben tervezzük, hogy néhány különböző környezetek fog megjelenni:

### <a name="orchestration-of-container-instances-exclusively"></a>Vezénylési tároló kizárólag példányok

Mivel gyorsan elkezdheti a munkát, és másodpercalapú számlázás, kizárólag az Azure Container Instances alapján környezet kínál a leggyorsabban használatba, és nagymértékben változó számítási feladatok kezelésére.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Container Instances szolgáltatásban és a tárolókat a virtuális gépek együttes használata

A hosszan futó, stabil számítási feladatokhoz dedikált virtuális gépek egy fürtben lévő tárolók replikálásával segít a vállalatnak rendszerint olcsóbb futó ugyanazokat a tárolókat az Azure Container Instances használatával. Ugyanakkor a tárolópéldányok biztosít egy remek megoldást gyorsan és csökkenthetjük a teljes kapacitás használati váratlan vagy rövid ideig tartó kiugrások kezelésére.

Ahelyett, hogy a horizontális felskálázás a fürtben lévő virtuális gépek számát, akkor ezeken a gépeken alakzatot további tárolók üzembe helyezése, az orchestrator is egyszerűen ütemezheti a további tárolókat az Azure Container Instances szolgáltatásban, és törölje őket, ha azok már nem szükséges.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Példa: virtuális csomópontok számára az Azure Kubernetes Service (AKS)

Az alkalmazások és szolgáltatások gyors méretezése egy [Azure Kubernetes Service](../aks/intro-kubernetes.md) -(AKS)-fürt, használhatja *virtuális csomópontok* dinamikusan létrehozott, az Azure Container Instances szolgáltatásban. Virtuális csomópontok ACI futtató podok és az AKS-fürt közötti hálózati kommunikáció engedélyezéséhez. 

Virtuális csomópontok jelenleg támogatja a Linux container Instances szolgáltatásban. Virtuális csomópontok használatával – első lépések a [Azure CLI-vel](https://go.microsoft.com/fwlink/?linkid=2047538) vagy [az Azure portal](https://go.microsoft.com/fwlink/?linkid=2047545).

Virtuális csomópontok használja a nyílt forráskódú [Virtual Kubelet] [ aci-connector-k8s] referenciaszámítógépnek a Kubernetes [kubelet] [ kubelet-doc] oly módon, a korlátlan számú csomópont a kapacitás. A Virtual Kubelet továbbítja a létrehozásának [podok] [ pod-doc] , tárolócsoportok az Azure Container Instances szolgáltatásban.

Tekintse meg a [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) projekt kapcsolatos további példák a Kubernetes API-t kiterjesztése a kiszolgáló nélküli tárolóplatformokhoz.

## <a name="next-steps"></a>További lépések

Az első tároló létrehozása az Azure Container Instances használatával a [a rövid útmutató](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/