---
title: Konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes GitOps és konfigurációjának fogalmi áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók, konfiguráció, GitOps
ms.openlocfilehash: 780c3c5c578c8a9b12eb7dda711070790477ac5f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561703"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurációk és GitOps az Azure arc-kompatibilis Kubernetes

A Kubernetes kapcsolatban a GitOps a Kubernetes-fürt konfigurációjának (központi telepítések, névterek stb.) kívánt állapotának deklarálása a git-tárházban. Ezt a deklarációt a fürt konfigurációjának egy operátor használatával történő lekérdezésével és lekéréses telepítésével követheti. A git-tárház a következőket tartalmazhatja:
* YAML – bármilyen érvényes Kubernetes-erőforrást (például névtereket, ConfigMaps, központi telepítéseket, DaemonSets stb.) leíró jegyzékfájlok
* Helm-diagramok alkalmazások telepítéséhez.

A [GitOps-terület népszerű, nyílt](https://docs.fluxcd.io/)forráskódú eszközét a Kubernetes-fürtön helyezheti üzembe, így a git-tárházban lévő konfigurációk egy Kubernetes-fürtön is könnyedén elhelyezhetők. A Flux a fürt és a névtér hatókörében is támogatja az operátor üzembe helyezését. A névtér hatókörével üzembe helyezett Flux-kezelők csak az adott névtéren belül telepíthetnek Kubernetes objektumokat. A fürt vagy a névtér hatókörének kiválasztásával több-bérlős üzembe helyezési mintákat érhet el ugyanazon a Kubernetes-fürtön.

## <a name="configurations"></a>Konfigurációk

[![Konfigurációk architektúrája ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

A fürt és a git-tárház közötti kapcsolat a `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Resource Managerban az Azure arc-kompatibilis Kubernetes-erőforrás (képviselő:) felső részén található kiterjesztési erőforrásként jön létre `Microsoft.Kubernetes/connectedClusters` . 

Az `sourceControlConfiguration` Erőforrás-tulajdonságok használatával a rendszer a megfelelő paraméterekkel telepíti a Flux-kezelőt a fürtön, például a git-tárházat, amelyből lekéri a jegyzékeket, és lekéri a lekérdezési időközt. Az adatok titkosítása a `sourceControlConfiguration` Azure Cosmos db adatbázisban található, inaktív állapotban tárolja az adatok titkosságának biztosítása érdekében.

A `config-agent` fürtön futó operációs rendszer feladatai a következők:
* Az új vagy frissített bővítmény-erőforrások nyomon követése `sourceControlConfiguration` Az Azure arc-kompatibilis Kubernetes-erőforráson.
* Egy Flux-operátor üzembe helyezésével megtekintheti a git-tárházat `sourceControlConfiguration` .
* Bármilyen frissítés alkalmazása `sourceControlConfiguration` . 

Több névtérre kiterjedő `sourceControlConfiguration` erőforrást is létrehozhat ugyanazon az Azure arc-kompatibilis Kubernetes-fürtön a több-bérlős szolgáltatás eléréséhez.

> [!NOTE]
> * Mivel az `config-agent` új vagy frissített bővítmény- `sourceControlConfiguration` erőforrások elérhetők az Azure arc-kompatibilis Kubernetes-erőforráson, az ügynököknek kapcsolódniuk kell a fürtre a kívánt állapothoz. Ha az ügynökök nem tudnak csatlakozni az Azure-hoz, a rendszer nem alkalmazza a kívánt állapotot az `sourceControlConfiguration` erőforráson Azure Resource Manager a fürtön.
> * Az Azure arc-kompatibilis Kubernetes-szolgáltatásokban a bizalmas felhasználói adatok, például a titkos kulcsok, az ismert gazdagépek tartalma, a HTTPS-Felhasználónév és a jogkivonat/jelszó több mint 48 óráig nem tárolódnak. Ha érzékeny bemeneteket használ a konfigurációkhoz, tanácsos a fürtöket a lehető legrendszeresen online állapotba hozni.

## <a name="apply-configurations-at-scale"></a>Konfigurációk alkalmazása a skálán

Mivel Azure Resource Manager kezeli a konfigurációkat, a Azure Policy segítségével automatizálhatja ugyanazt a konfigurációt az összes Azure arc-kompatibilis Kubernetes-erőforráson egy előfizetés vagy egy erőforráscsoport hatókörén belül. 

Ez a méretezhető kényszerítés biztosítja, hogy a közös alapkonfiguráció (például a ClusterRoleBindings, a RoleBindings és a NetworkPolicy) a teljes flotta vagy az Azure arc-kompatibilis Kubernetes-fürtök leltározása során alkalmazható legyen.

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése az Azure-ív használatával](./connect-cluster.md)
* [Konfigurációk létrehozása az ív használatára képes Kubernetes-fürtön](./use-gitops-connected-cluster.md)
* [A Azure Policy használata a konfigurációk nagy méretekben való alkalmazásához](./use-azure-policy.md)