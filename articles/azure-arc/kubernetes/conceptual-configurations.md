---
title: Konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes GitOps és konfigurációjának fogalmi áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók, konfiguráció, GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652547"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurációk és GitOps az Azure arc-kompatibilis Kubernetes

A Kubernetes kapcsolatban a GitOps a Kubernetes-fürt konfigurációjának (központi telepítések, névterek stb.) kívánt állapotának deklarálása a git-tárházban. Ezt a deklarációt a fürt konfigurációjának egy operátor használatával történő lekérdezésével és lekéréses telepítésével követheti. A git-tárház a következőket tartalmazhatja:
* YAML – bármilyen érvényes Kubernetes-erőforrást (például névtereket, ConfigMaps, központi telepítéseket, DaemonSets stb.) leíró jegyzékfájlok
* Helm-diagramok alkalmazások telepítéséhez.

A [GitOps-terület népszerű, nyílt](https://docs.fluxcd.io/)forráskódú eszközét a Kubernetes-fürtön helyezheti üzembe, így a git-tárházból egy Kubernetes-fürtön is könnyedén elvégezheti a konfigurációk folyamatát. A Flux a fürt és a névtér hatókörében is támogatja az operátor üzembe helyezését. A névtér hatókörével üzembe helyezett Flux-kezelők csak az adott névtéren belül telepíthetnek Kubernetes objektumokat. A fürt vagy a névtér hatókörének kiválasztásával több-bérlős üzembe helyezési mintákat érhet el ugyanazon a Kubernetes-fürtön.

## <a name="configurations"></a>Konfigurációk

[![Konfigurációk architektúrája ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

A fürt és a git-tárház közötti kapcsolat a `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Resource Managerban az Azure arc-kompatibilis Kubernetes-erőforrás (képviselő:) felső részén található kiterjesztési erőforrásként jön létre `Microsoft.Kubernetes/connectedClusters` . 

Az `sourceControlConfiguration` Erőforrás-tulajdonságok használatával a rendszer a megfelelő paraméterekkel telepíti a Flux-kezelőt a fürtön, például azt a git-tárházat, amelyről lekéri a jegyzékfájlokat, és lekéri a lekérdezési időközt. Az adatok titkosítása a `sourceControlConfiguration` Azure Cosmos db adatbázisban titkosítva és tárolva történik az adatok titkosságának biztosítása érdekében.

A `config-agent` fürtön futó operációs rendszer feladatai a következők:
* Az új vagy frissített bővítmény-erőforrások nyomon követése `sourceControlConfiguration` Az Azure arc-kompatibilis Kubernetes-erőforráson.
* Egy Flux-operátor üzembe helyezésével megtekintheti a git-tárházat `sourceControlConfiguration` .
* Bármilyen frissítés alkalmazása `sourceControlConfiguration` . 

Több névtérre kiterjedő `sourceControlConfiguration` erőforrást is létrehozhat ugyanazon az Azure arc-kompatibilis Kubernetes-fürtön a több-bérlős szolgáltatás eléréséhez.

> [!NOTE]
> * `config-agent` folyamatosan figyeli az `sourceControlConfiguration` Azure arc-kompatibilis Kubernetes-erőforráson elérhető új vagy frissített bővítmény-erőforrásokat. Így az ügynököknek konzisztens kapcsolatra van szükségük a kívánt állapot tulajdonságainak a fürtre való lekéréséhez. Ha az ügynökök nem tudnak csatlakozni az Azure-hoz, a rendszer nem alkalmazza a kívánt állapotot a fürtön.
> * Az Azure arc-kompatibilis Kubernetes-szolgáltatásokban akár 48 óráig is tárolhatja a bizalmas felhasználói adatbevitelt, például a titkos kulcsot, az ismert tartalmakat, a HTTPS-felhasználóneveket, valamint a tokent vagy a jelszót. Ha érzékeny bemeneteket használ a konfigurációkhoz, a fürtöket a lehető legrendszeresen online állapotba helyezheti.

## <a name="apply-configurations-at-scale"></a>Konfigurációk alkalmazása a skálán

Mivel a Azure Resource Manager kezeli a konfigurációkat, automatizálhatja ugyanazt a konfigurációt az összes Azure arc-kompatibilis Kubernetes-erőforráson az előfizetés vagy egy erőforráscsoport hatókörén belül Azure Policy használatával. 

Ez a méretezhető kényszerítés biztosítja a közös alapkonfigurációt (például a ClusterRoleBindings, a RoleBindings és a NetworkPolicy) az Azure arc-kompatibilis Kubernetes-fürtök teljes flottáján vagy leltározásán.

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése az Azure-ív használatával](./connect-cluster.md)
* [Konfigurációk létrehozása az ív használatára képes Kubernetes-fürtön](./use-gitops-connected-cluster.md)
* [A Azure Policy használata a konfigurációk nagy méretekben való alkalmazásához](./use-azure-policy.md)