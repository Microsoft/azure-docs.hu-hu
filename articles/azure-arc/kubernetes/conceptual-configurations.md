---
title: Konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes GitOps és konfigurációjának fogalmi áttekintését tartalmazza.
keywords: Kubernetes, arc, Azure, tárolók, konfiguráció, GitOps
ms.openlocfilehash: 88a30876b25730e4cb0b4b1e19fac94b9e556adc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121796"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurációk és GitOps az Azure arc-kompatibilis Kubernetes

A Kubernetes kapcsolatban a GitOps a Kubernetes-fürt konfigurációjának (központi telepítések, névterek stb.) kívánt állapotának deklarálása a git-tárházban. Ezt a deklarációt a fürt konfigurációjának egy operátor használatával történő lekérdezésével és lekéréses telepítésével követheti. A git-tárház a következőket tartalmazhatja:
* YAML – bármilyen érvényes Kubernetes-erőforrást (például névtereket, ConfigMaps, központi telepítéseket, DaemonSets stb.) leíró jegyzékfájlok
* Helm-diagramok alkalmazások telepítéséhez.

A [GitOps-terület népszerű, nyílt](https://docs.fluxcd.io/)forráskódú eszközét a Kubernetes-fürtön helyezheti üzembe, így a git-tárházból egy Kubernetes-fürtön is könnyedén elvégezheti a konfigurációk folyamatát. A Flux a fürt és a névtér hatókörében is támogatja az operátor üzembe helyezését. A névtér hatókörével üzembe helyezett Flux-kezelők csak az adott névtéren belül telepíthetnek Kubernetes objektumokat. A fürt vagy a névtér hatókörének kiválasztásával több-bérlős üzembe helyezési mintákat érhet el ugyanazon a Kubernetes-fürtön.

## <a name="configurations"></a>Konfigurációk

[![Konfigurációk architektúrája ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

A fürt és a git-tárház közötti kapcsolat egy konfigurációs erőforrásként () jön létre `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Az Azure arc-kompatibilis Kubernetes-erőforrás (amelyet a jelképez `Microsoft.Kubernetes/connectedClusters` ) a Azure Resource Managerban. 

A konfigurációs erőforrás tulajdonságainak használatával a rendszer a megfelelő paraméterekkel telepíti a Fluxus-kezelőt a fürtön, például a git-tárházat, amelyből lekéri a jegyzékfájlokat, valamint azt a lekérdezési időközt, amelyen a lekérést kéri. A konfigurációs erőforrásadatokat a rendszer a Azure Cosmos DB adatbázisában tárolja titkosított állapotban, hogy biztosítsa az adatok titkosságát.

A `config-agent` fürtön futó operációs rendszer feladatai a következők:
* Az Azure arc-kompatibilis Kubernetes-erőforrás új vagy frissített konfigurációs erőforrásainak nyomon követése.
* A Flux-operátor üzembe helyezésével megtekintheti az egyes konfigurációs erőforrások git-tárházát.
* Bármilyen konfigurációs erőforráson végrehajtott frissítés alkalmazása. 

Több névtérre kiterjedő konfigurációs erőforrást is létrehozhat ugyanarra az Azure arc-kompatibilis Kubernetes-fürtre a több-bérlős szolgáltatás eléréséhez.

> [!NOTE]
> * `config-agent` az arc-kompatibilis Kubernetes erőforráson elérhető új vagy frissített konfigurációs erőforrások figyelése. Így az ügynököknek kapcsolódniuk kell a fürtre a kívánt állapothoz. Ha az ügynökök nem tudnak csatlakozni az Azure-hoz, a rendszer késéssel terjeszti a kívánt állapotot a fürtre.
> * Az Azure arc-kompatibilis Kubernetes-szolgáltatásokban a bizalmas felhasználói adatok, például a titkos kulcsok, az ismert gazdagépek tartalma, a HTTPS-Felhasználónév és a jogkivonat/jelszó több mint 48 óráig nem tárolódnak. Ha érzékeny bemeneteket használ a konfigurációkhoz, a fürtöket a lehető legrendszeresen online állapotba helyezheti.

## <a name="apply-configurations-at-scale"></a>Konfigurációk alkalmazása a skálán

Mivel a Azure Resource Manager kezeli a konfigurációkat, automatizálhatja ugyanazt a konfigurációt az összes Azure arc-kompatibilis Kubernetes-erőforráson az előfizetés vagy egy erőforráscsoport hatókörén belül Azure Policy használatával. 

Ez a méretezhető kényszerítés biztosítja a közös alapkonfigurációt (például a ClusterRoleBindings, a RoleBindings és a NetworkPolicy) az Azure arc-kompatibilis Kubernetes-fürtök teljes flottáján vagy leltározásán.

## <a name="next-steps"></a>Következő lépések

* Útmutató a [Kubernetes-fürtök Azure arc-hoz való összekapcsolásához](./connect-cluster.md).
* Már van csatlakoztatva Kubernetes-fürt az Azure arc? [Konfigurációk létrehozása az ív használatára képes Kubernetes-fürtön](./use-gitops-connected-cluster.md).
* Megtudhatja, hogyan [használhatók a Azure Policy a konfigurációk méretezésére való alkalmazásához](./use-azure-policy.md).