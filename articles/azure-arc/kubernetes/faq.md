---
title: Azure arc-kompatibilis Kubernetes – gyakori kérdések
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes kapcsolatos gyakori kérdések listáját tartalmazza
keywords: Kubernetes, arc, Azure, tárolók, konfiguráció, GitOps, GYIK
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025849"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Gyakori kérdések – Azure arc-kompatibilis Kubernetes

Ez a cikk az Azure arc-kompatibilis Kubernetes kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Mi a különbség az Azure arc-kompatibilis Kubernetes és az Azure Kubernetes Service (ak) között?

Az AK a felügyelt Kubernetes-ajánlat az Azure-ban. Az AK leegyszerűsíti a felügyelt Kubernetes-fürtök üzembe helyezését az Azure-ban azáltal, hogy az Azure-ban sok összetettséget és működési terhelést kiszervez. Mivel a Kubernetes-főkiszolgálókat az Azure felügyeli, csak az ügynök csomópontjait kell felügyelni és karbantartani.

Az Azure arc-kompatibilis Kubernetes lehetővé teszi az Azure felügyeleti képességeinek (például Azure Monitor és Azure Policy) kiterjesztését a Kubernetes-fürtök Azure-hoz való csatlakoztatásával. A mögöttes Kubernetes-fürtöt is karbantartja.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Szükségem van az Azure-beli Azure-beli arc-alapú fürtök összekapcsolására?

Nem. Az összes Azure arc-kompatibilis Kubernetes funkció, beleértve a Azure Monitor és a Azure Policy (forgalomirányító) is, az AK-ban érhető el (a Azure Resource Manager natív erőforrása).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Csatlakoztatni kell az AK-HCI-fürtöt és a Kubernetes-fürtöket Azure Stack hub-on, és Azure Stack Edge-t az Azure arc-hoz?

Igen, ha az AK-HCI-fürtöt vagy a Kubernetes-fürtöket Azure Stack Edge-ben vagy Azure Stack hub-on az Azure arc-hoz csatlakoztatja, az Azure Resource Manager-ban erőforrás-ábrázolással Ez az erőforrás-ábrázolás olyan funkciókat bővít ki, mint a fürtkonfiguráció, a Azure Monitor és a Azure Policy (forgalomirányító) a csatlakoztatott Kubernetes-fürtökhöz.

Ha az Azure arc-kompatibilis Kubernetes-fürt Azure Stack Edge, AK Azure Stack HCI (>= április 2021 Update), vagy ak a Windows Server 2019 Datacenter (>= április 2021 Update), akkor a Kubernetes-konfiguráció díjmentesen szerepel.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Hogyan lehet kezelni a lejárt Azure arc-kompatibilis Kubernetes-erőforrásokat?

Az Azure arc-kompatibilis Kubernetes társított Managed Service Identity (MSI) tanúsítvány érvényességi ideje 90 nap. A tanúsítvány lejárata után az erőforrást figyelembe kell venni, `Expired` és az összes funkció (például a konfiguráció, a figyelés és a házirend) nem működik ezen a fürtön. A Kubernetes-fürt újbóli használatának megkezdése az Azure arc használatával:

1. Az Azure arc-kompatibilis Kubernetes-erőforrás és-ügynökök törlése a fürtön. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Hozza létre újra az Azure arc-kompatibilis Kubernetes-erőforrást az ügynökök fürtön való üzembe helyezésével.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` a a fürt tetején lévő konfigurációkat is törli. A futtatása után `az connectedk8s connect` hozza létre újra a konfigurációkat a fürtön manuálisan vagy a Azure Policy használatával.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Ha már használom a CI/CD-folyamatokat, továbbra is használhatom az Azure arc-kompatibilis Kubernetes és-konfigurációkat?

Igen, továbbra is használhat konfigurációkat egy olyan fürtön, amely a központi telepítést egy CI/CD-folyamaton keresztül fogadja. A hagyományos CI/CD-folyamatokhoz képest a konfigurációk két további előnyt kínálnak:

**Drift egyeztetés**

A CI/CD-folyamat csak egyszer alkalmazza a módosításokat a folyamat futtatásakor. A fürt GitOps operátora azonban folyamatosan lekérdezi a git-tárházat, hogy beolvassa a Kubernetes-erőforrások kívánt állapotát a fürtön. Ha a GitOps operátor megállapítja, hogy az erőforrások kívánt állapota eltér a fürt erőforrásainak tényleges állapotától, ez a drift egyeztetésre kerül.

**GitOps alkalmazása skálán**

A CI/CD-folyamatok a Kubernetes-fürt eseményvezérelt üzembe helyezéséhez hasznosak (például egy git-tárházba való leküldés). Ha azonban ugyanazt a konfigurációt szeretné telepíteni az összes Kubernetes-fürtön, akkor manuálisan kell konfigurálnia az egyes Kubernetes-fürtök hitelesítő adatait a CI/CD-folyamathoz. 

Az Azure arc-kompatibilis Kubernetes esetében, mivel a Azure Resource Manager kezeli a konfigurációkat, a Azure Policy használatával automatizálhatja ugyanazt a konfigurációt az összes Azure arc-kompatibilis Kubernetes-erőforráshoz az előfizetés vagy az erőforráscsoport hatókörén belül. Ez a képesség még a szabályzat-hozzárendelés után létrehozott Azure arc-kompatibilis Kubernetes-erőforrásokra is érvényes.

Ez a szolgáltatás alapkonfigurációkat (például hálózati házirendeket, szerepkör-kötéseket és Pod biztonsági házirendeket) alkalmaz a teljes Kubernetes-fürt leltárában a megfelelőségi és irányítási követelmények teljesítése érdekében.

## <a name="next-steps"></a>Következő lépések

* Útmutató a [Kubernetes-fürtök Azure arc-hoz való összekapcsolásához](./quickstart-connect-cluster.md).
* Már van csatlakoztatva Kubernetes-fürt az Azure arc? [Konfigurációk létrehozása az ív használatára képes Kubernetes-fürtön](./tutorial-use-gitops-connected-cluster.md).
* Megtudhatja, hogyan [használhatók a Azure Policy a konfigurációk méretezésére való alkalmazásához](./use-azure-policy.md).