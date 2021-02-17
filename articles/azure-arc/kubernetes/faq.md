---
title: Azure arc-kompatibilis Kubernetes – gyakori kérdések
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ez a cikk az Azure arc-kompatibilis Kubernetes kapcsolatos gyakori kérdések listáját tartalmazza
keywords: Kubernetes, arc, Azure, tárolók, konfiguráció, GitOps, GYIK
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561739"
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

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Hogyan lehet kezelni a lejárt Azure arc-kompatibilis Kubernetes-erőforrásokat?

Az Azure arc-kompatibilis Kuberenetes tartozó felügyelt szolgáltatás-identitás (MSI) tanúsítványának érvényességi időszaka 90 nap. A tanúsítvány lejárata után az erőforrást figyelembe kell venni, `Expired` és az összes funkció, például a konfiguráció, a figyelés és a házirend nem működik ezen a fürtön. Kövesse az alábbi lépéseket, hogy a Kubernetes-fürt ismét működjön az Azure arc használatával:

1. Azure arc-kompatibilis Kubernetes-erőforrás és-ügynökök törlése a fürtön 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Hozza létre újra az Azure arc-kompatibilis Kubernetes-erőforrást úgy, hogy ügynököket telepít újra a fürtön.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` a a fürt tetején lévő konfigurációkat is törli. A Futtatás után `az connectedk8s connect` hozza létre újra a konfigurációkat a fürtön manuálisan vagy a Azure Policy használatával.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Ha már használom a CI/CD-folyamatokat, továbbra is használhatom az Azure arc-kompatibilis Kubernetes és-konfigurációkat?

Igen, továbbra is használhat konfigurációkat egy olyan fürtön, amely a központi telepítést egy CI/CD-folyamaton keresztül fogadja. A hagyományos CI/CD-folyamatokhoz képest a konfigurációk két további előnyt kínálnak:

**Drift egyeztetés**

A CI/CD-folyamat csak egyszer alkalmazza a módosításokat a folyamat futtatásakor. A fürt GitOps operátora azonban folyamatosan lekérdezi a git-tárházat, hogy beolvassa a Kubernetes-erőforrások kívánt állapotát a fürtön. Ha a GitOps operátor megállapítja, hogy az erőforrások kívánt állapota eltér a fürt erőforrásainak tényleges állapotától, ez a drift egyeztetésre kerül.

**GitOps alkalmazása skálán**

A CI/CD-folyamatok kiválóan alkalmasak az eseményvezérelt üzemelő példányok számára a Kubernetes-fürtön, ahol az esemény lehet egy git-tárházba való leküldés. Ugyanakkor az összes Kubernetes-fürthöz azonos konfiguráció telepítése megköveteli, hogy a CI/CD-folyamat manuálisan legyen konfigurálva az egyes Kubernetes-fürtök hitelesítő adataival. Ha azonban az Azure arc-kompatibilis Kubernetes esetében Azure Resource Manager kezeli a konfigurációkat, akkor a Azure Policy segítségével automatizálhatja a kívánt konfiguráció alkalmazását az előfizetés vagy az erőforráscsoport hatóköre alá tartozó összes Kubernetes-fürtön egy ugrással. Ez a képesség még a szabályzat-hozzárendelés után létrehozott Azure arc-kompatibilis Kubernetes-erőforrásokra is érvényes.

A konfigurációk funkció az alapkonfigurációk, például a hálózati házirendek, a szerepkör-kötések és a pod biztonsági házirendek alkalmazására használható a Kubernetes-fürtök teljes leltárában a megfelelőségi és irányítási követelmények teljesítése érdekében.

## <a name="next-steps"></a>Következő lépések

* [Fürt összekötése az Azure-ív használatával](./connect-cluster.md)
* [Konfigurációk létrehozása az ív használatára képes Kubernetes-fürtön](./use-gitops-connected-cluster.md)
* [A Azure Policy használata a konfigurációk nagy méretekben való alkalmazásához](./use-azure-policy.md)
