---
title: Az Azure arc-kompatibilis Kubernetes tervezése és üzembe helyezése
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Nagy mennyiségű fürt bevezetése az Azure arc-kompatibilis Kubernetes a konfiguráció felügyeletéhez
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315427"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Azure arc-kompatibilis Kubernetes tervezése és üzembe helyezése

Egy informatikai infrastruktúra-szolgáltatás vagy üzleti alkalmazás üzembe helyezése bármely vállalat számára kihívást jelenthet. A váratlan meglepetések vagy nem tervezett költségek elkerülése érdekében alaposan meg kell terveznie, hogy a lehető leghamarabb készen álljon. Egy ilyen tervnek azonosítania kell a feladatok végrehajtásához szükséges tervezési és központi telepítési feltételeket.

Ahhoz, hogy a telepítés zökkenőmentes maradjon, a tervnek világosan meg kell ismernie a következőket:

* Szerepkörök és felelősségek.
* Az összes Kubernetes-fürt leltára
* A hálózati követelmények teljesítése.
* Az üzembe helyezés és a folyamatos felügyelet sikeres végrehajtásához szükséges szaktudás és képzés.
* Elfogadási feltételek és a siker nyomon követése.
* Az üzemelő példányok automatizálására szolgáló eszközök és módszerek.
* A késések és a fennakadások elkerülése érdekében azonosított kockázatok és kockázatcsökkentő tervek.
* Az üzembe helyezés során felmerülő fennakadások elkerülése.
* Mi a eszkalációs útvonal jelentős probléma esetén?

Ennek a cikknek a célja annak biztosítása, hogy készen áll az Azure arc-kompatibilis Kubernetes sikeres üzembe helyezésére a környezet több üzemi fürtje között.

## <a name="prerequisites"></a>Előfeltételek

* Egy meglévő kubernetes-fürt. Ha még nem rendelkezik ilyennel, létrehozhat egy fürtöt a következő lehetőségek egyikének használatával:
    - [Kubernetes a Docker-ben (KIND)](https://kind.sigs.k8s.io/)
    - Kubernetes-fürt létrehozása a Docker használatával [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) vagy [Windows rendszerhez](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Önállóan felügyelt Kubernetes-fürt a [cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html) használatával

* A gépek közvetlenül vagy egy proxykiszolgálón keresztül kapcsolódnak a helyszíni hálózatról vagy más felhőalapú környezetről az Azure-beli erőforrásokhoz. További részleteket a [hálózati előfeltételek](quickstart-connect-cluster.md#meet-network-requirements)szakaszban találhat.

* Egy fájl, amely arra `kubeconfig` a fürtre mutat, amelyhez csatlakozni szeretne az Azure-ívet.
* A "READ" és a "Write" engedély a felhasználóhoz vagy az egyszerű szolgáltatáshoz, amely az Azure arc-kompatibilis Kubernetes erőforrás-típusát hozza létre `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Próbaüzem

Mielőtt az összes éles fürtbe telepítené az üzembe helyezést, kezdje a telepítési folyamat kiértékelésével, mielőtt széles körben befogadja a környezetét. Egy próba esetében azonosítson olyan fürtök reprezentatív mintavételezését, amelyek nem kritikus fontosságúak a vállalatok üzleti tevékenységének elvégzéséhez. Ügyeljen arra, hogy elegendő időt hagyjon a próbaüzem futtatásához és a hatás értékeléséhez: körülbelül 30 napot ajánlunk.

Hozzon létre egy formális tervet, amely leírja a pilóta hatókörét és részleteit. A következő mintavételi terv segítséget nyújt az első lépésekhez.

* **Célok** – ismerteti azokat az üzleti és technikai illesztőprogramokat, amelyek a próbaüzem szükségességét eredményezik.
* **Kiválasztási feltételek** – azokat a feltételeket adja meg, amelyekkel kiválaszthatja, hogy a megoldás mely szempontjait kell bemutatni a próbaüzem segítségével.
* **Hatókör** – a megoldás-összetevőkre, a várt ütemtervre, a pilóta időtartamára és a célként megadott fürtökre vonatkozik.
* **Sikerességi feltételek és mérőszámok** – határozza meg a próbaüzem sikerességi feltételeit és a siker szintjének meghatározására szolgáló konkrét mértékeket.
* **Képzési terv** – az Azure-ban és az IT-szolgáltatásokban új, a kísérleti időszakban a rendszermérnökök, a rendszergazdák stb. betanítási terve.
* **Áttérési terv** – a próbaüzem és az éles környezet közötti áttérés irányításához használt stratégia és feltételek leírása.
* **Visszaállítás** – a pilóta előzetes üzembe helyezési állapotba való visszaállításának eljárásait ismerteti.
* **Kockázatok** – felsorolja az összes azonosított kockázatot a próbaüzem végrehajtásához és az éles környezetben való üzembe helyezéshez.

## <a name="phase-1-build-a-foundation"></a>1. fázis: alaprendszer létrehozása

Ebben a fázisban a rendszermérnökök vagy a rendszergazdák olyan alapvető tevékenységeket hajtanak végre, mint például az erőforráscsoportok, a címkék és a szerepkör-hozzárendelések, így az Azure arc-kompatibilis Kubernetes-erőforrások létrehozhatók és működtethetők.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
| [Erőforráscsoport létrehozása](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Egy dedikált erőforráscsoport, amely csak az Azure arc-kompatibilis Kubernetes-erőforrásokat tartalmazza, és központosítja ezeknek az erőforrásoknak a felügyeletét és monitorozását. | Egy óra |
| [Címkék](../../azure-resource-manager/management/tag-resources.md) alkalmazása a gépek rendszerezéséhez. | Kiértékelheti és fejlesztheti az IT-igazítású [címkézési stratégiát](/azure/cloud-adoption-framework/decision-guides/resource-tagging/). Ezzel csökkentheti az Azure arc-alapú Kubernetes-erőforrások kezelésének összetettségét, és egyszerűbbé teheti a felügyeleti döntések meghozatalát. | Egy nap |
| GitOps- [konfigurációk](tutorial-use-gitops-connected-cluster.md) azonosítása | Azonosítsa az alkalmazást vagy az alapkonfigurációt `PodSecurityPolicy` , amelyet szeretne a `NetworkPolicy` fürtökre telepíteni. | Egy nap |
| [Azure Policy](../../governance/policy/overview.md) irányítási terv kidolgozása | Határozza meg, hogyan valósítja meg az Azure arc-kompatibilis Kubernetes-fürtök irányítását az előfizetés vagy az erőforráscsoport hatókörében Azure Policy. | Egy nap |
| [Szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md) konfigurálása (RBAC) | Olyan hozzáférési terv kidolgozása, amely meghatározza, hogy ki rendelkezik olvasási/írási/összes engedéllyel a fürtökön | Egy nap |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>2. fázis: az Azure arc-kompatibilis Kubernetes üzembe helyezése

Ebben a fázisban a Kubernetes-fürtöket az Azure-ba kapcsolja össze:

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
| [Az első Kubernetes-fürt összekötése az Azure-beli ív használatával](quickstart-connect-cluster.md) | Az első fürt Azure arc-hoz való csatlakoztatásának részeként állítsa be a bevezetési környezetet az összes szükséges eszközzel, például az Azure CLI, a Helm és `connectedk8s` Az Azure CLI bővítménnyel. | 15 perc |
| [Egyszerű szolgáltatás létrehozása](create-onboarding-service-principal.md) | Hozzon létre egy egyszerű szolgáltatást, amely nem interaktív módon csatlakozik a Kubernetes-fürtökhöz az Azure CLI vagy a PowerShell használatával. | Egy óra |


## <a name="phase-3-manage-and-operate"></a>3. fázis: kezelés és üzemeltetés

Ebben a fázisban alkalmazásokat és alapkonfigurációkat helyezünk üzembe a Kubernetes-fürtökön.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
|[Konfigurációk létrehozása](tutorial-use-gitops-connected-cluster.md) a fürtökön | Konfigurációk létrehozása az Azure arc-kompatibilis Kubernetes-erőforráson futó alkalmazások telepítéséhez. | 15 perc |
|[Azure Policy használata](use-azure-policy.md) a konfigurációk méretezésének kényszerítéséhez | Szabályzat-hozzárendelések létrehozásával automatizálhatja az alapkonfigurációk telepítését az összes fürtön az előfizetés vagy az erőforráscsoport hatóköre alatt. | 15 perc |
| [Azure arc-ügynökök frissítése](agent-upgrade.md) | Ha letiltotta az ügynökök automatikus frissítését a fürtökön, frissítse az ügynököket manuálisan a legújabb verzióra, és győződjön meg arról, hogy rendelkezik a legújabb biztonsági és hibajavítási javításokkal. | 15 perc |

## <a name="next-steps"></a>Következő lépések

* A rövid útmutató segítségével [Kubernetes-fürtöt létesíthet az Azure-ív](./quickstart-connect-cluster.md)használatával.
* [Konfigurációk létrehozása](./tutorial-use-gitops-connected-cluster.md) az Azure arc-kompatibilis Kubernetes-fürtön.
* [A konfigurációk méretezése Azure Policy használatával alkalmazható](./use-azure-policy.md).