---
title: Az Azure Policy használata fürtkonfigurációk tömeges alkalmazására (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 02/10/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: A Azure Policy használata a fürt konfigurációjának méretekben történő alkalmazásához
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.openlocfilehash: ce9ba75e200a02654cac4c50303cc90fd0c1a5fd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390912"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Az Azure Policy használata fürtkonfigurációk tömeges alkalmazására (előzetes verzió)

## <a name="overview"></a>Áttekintés

A Azure Policy segítségével kényszerítheti ki a következő erőforrások valamelyikét, hogy az adott `Microsoft.KubernetesConfiguration/sourceControlConfigurations` alkalmazásra kerüljön:
*  `Microsoft.Kubernetes/connectedclusters` erőforrás.
* GitOps-kompatibilis `Microsoft.ContainerService/managedClusters` erőforrás. 

A Azure Policy használatához válasszon ki egy meglévő szabályzat-definíciót, és hozzon létre egy szabályzat-hozzárendelést. A szabályzat-hozzárendelés létrehozásakor:
1. Adja meg a hozzárendelés hatókörét.
    * A hatókör egy Azure-erőforráscsoport vagy-előfizetés lesz. 
2. Állítsa be a létrehozandó paraméterek paramétereit `sourceControlConfiguration` . 

A hozzárendelés létrehozása után a Azure Policy motor azonosítja a `connectedCluster` `managedCluster` hatókörön belül található összes vagy erőforrást, és alkalmazza a `sourceControlConfiguration` -t a-re.

Több házirend-hozzárendelés használatával engedélyezheti a git-adattárakat, mint az igazság forrásait az egyes fürtökön. Minden szabályzat-hozzárendelés egy másik git-tárház használatára lesz konfigurálva; például egy adattár a központi IT-/fürt-kezelőhöz és más adattárakhoz.

## <a name="prerequisite"></a>Előfeltétel

Ellenőrizze, hogy van- `Microsoft.Authorization/policyAssignments/write` e engedélye a hatókörre (előfizetés vagy erőforráscsoport), ahol létre fogja hozni ezt a szabályzat-hozzárendelést.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

1. A Azure Portal navigáljon a **házirendhez**.
1. Az oldalsáv **authoring (szerzői műveletek** ) szakaszában válassza a **definíciók** lehetőséget.
1. A "Kubernetes" kategóriában válassza a "GitOps üzembe helyezése a Kubernetes-fürtön" beépített szabályzatot. 
1. Kattintson a **hozzárendelés** gombra.
1. Állítsa be a **hatókört** arra a felügyeleti csoportra, előfizetésre vagy erőforráscsoportre, amelyre a szabályzat-hozzárendelés vonatkozik majd.
    * Ha ki szeretné zárni a szabályzat hatókörében lévő erőforrásokat, állítsa be a **kizárásokat**.
1. Adja meg a szabályzat hozzárendelését egy könnyen azonosítható **névvel** és **leírással**.
1. Győződjön meg arról, hogy a **házirend-kényszerítés** **engedélyezve** értékre van állítva.
1. Kattintson a **Tovább** gombra.
1. Állítsa be a paraméter értékeit, amelyeket a hoz létre a létrehozásakor `sourceControlConfiguration` .
1. Kattintson a **Tovább** gombra.
1. **Szervizelési feladat létrehozásának** engedélyezése.
1. Ellenőrizze, hogy be van-e jelölve a **felügyelt identitás létrehozása** jelölőnégyzet, és hogy az identitás **közreműködői** jogosultságokkal rendelkezik-e. 
    * További információ: [szabályzat-hozzárendelés létrehozása](../../governance/policy/assign-policy-portal.md) rövid útmutató és a [nem megfelelő erőforrások szervizelése Azure Policy cikkel](../../governance/policy/how-to/remediate-resources.md).
1. Válassza az **Áttekintés + létrehozás** lehetőséget.

A szabályzat-hozzárendelés létrehozása után a `sourceControlConfiguration` rendszer a hozzárendelés hatókörében található alábbi erőforrások valamelyikére alkalmazza a-t:
* Új `connectedCluster` erőforrások.
* Új `managedCluster` erőforrások a telepített GitOps-ügynökökkel. 

Meglévő fürtök esetében manuálisan kell futtatnia egy szervizelési feladatot. Ez a feladat általában 10 – 20 percet vesz igénybe a szabályzat-hozzárendelés érvénybe léptetéséhez.

## <a name="verify-a-policy-assignment"></a>Szabályzat-hozzárendelés ellenőrzése

1. A Azure Portal navigáljon az egyik `connectedCluster` erőforráshoz.
1. Az oldalsáv **Beállítások** szakaszában válassza a **szabályzatok** lehetőséget. 
    * Az AK-fürt UX-je még nincs implementálva.
    * A szabályzatok listájában meg kell jelennie a korábban létrehozott házirend-hozzárendelésnek, *amely megfelelt* a **megfelelőségi állapot** beállításának.
1. Az oldalsáv **Beállítások** szakaszában válassza a **konfigurációk** lehetőséget.
    * A konfigurációk listán látnia kell, `sourceControlConfiguration` hogy a szabályzat-hozzárendelés létrejött.
1. `kubectl`A paranccsal kérdezheti le a fürtöt. 
    * Ekkor meg kell jelennie a által létrehozott névtérnek és összetevőknek `sourceControlConfiguration` .
    * 5 percen belül látnia kell a fürtben a beállított git-tárház jegyzékfájljában leírt összetevőket.

## <a name="next-steps"></a>Következő lépések

* [Azure Monitor beállítása az arc-kompatibilis Kubernetes-fürtökkel rendelkező tárolók számára](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
