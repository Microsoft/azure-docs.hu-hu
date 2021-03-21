---
title: A Azure Policy használata a fürt konfigurációjának méretekben történő alkalmazásához
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: A Azure Policy használata a fürt konfigurációjának méretekben történő alkalmazásához
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121456"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>A Azure Policy használata a fürt konfigurációjának méretekben történő alkalmazásához

Az `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure arc-kompatibilis Kubernetes-fürtökön () lévő konfigurációk (erőforrástípus) méretezése az Azure Policy használatával végezhető el `Microsoft.Kubernetes/connectedclusters` .

A Azure Policy használatához válasszon ki egy meglévő szabályzat-definíciót, és hozzon létre egy szabályzat-hozzárendelést. A szabályzat-hozzárendelés létrehozásakor:
1. Adja meg a hozzárendelés hatókörét.
    * A hatókör egy Azure-erőforráscsoport vagy-előfizetés lesz. 
2. Adja meg a létrehozandó konfiguráció paramétereit. 

A hozzárendelés létrehozása után Azure Policy motor azonosítja a hatókörben található összes Azure arc-kompatibilis Kubernetes-fürtöt, és minden egyes fürtön alkalmazza a konfigurációt.

Több konfigurációt is létrehozhat, amelyek mindegyike egy másik git-tárházra mutat, és több házirend-hozzárendelést használ. Például egy adattár a központi IT-/fürt-kezelőhöz és más adattárakhoz az alkalmazás-munkacsoportok számára.

## <a name="prerequisite"></a>Előfeltétel

Ellenőrizze, hogy van- `Microsoft.Authorization/policyAssignments/write` e engedélye a hatókörre (előfizetés vagy erőforráscsoport), ahol létrehozza ezt a szabályzat-hozzárendelést.

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

A szabályzat-hozzárendelés létrehozása után a rendszer alkalmazza a konfigurációt a szabályzat-hozzárendelés hatókörében létrehozott új, Azure arc-kompatibilis Kubernetes-fürtökre.

A meglévő fürtök esetében manuálisan kell futtatnia egy szervizelési feladatot. Ez a feladat általában 10 – 20 percet vesz igénybe a szabályzat-hozzárendelés érvénybe léptetéséhez.

## <a name="verify-a-policy-assignment"></a>Szabályzat-hozzárendelés ellenőrzése

1. A Azure Portal navigáljon az egyik Azure arc-kompatibilis Kubernetes-fürthöz.
1. Az oldalsáv **Beállítások** szakaszában válassza a **szabályzatok** lehetőséget. 
    * A szabályzatok listájában meg kell jelennie a korábban létrehozott házirend-hozzárendelésnek, *amely megfelelt* a **megfelelőségi állapot** beállításának.
1. Az oldalsáv **Beállítások** szakaszában válassza a **konfigurációk** lehetőséget.
    * A konfigurációk listán látnia kell a szabályzat-hozzárendelés által létrehozott konfigurációt.
1. `kubectl`A paranccsal kérdezheti le a fürtöt. 
    * Ekkor meg kell jelennie a konfigurációk erőforrásai által létrehozott névtérnek és összetevőknek.
    * 5 percen belül (feltételezve, hogy a fürtnek van hálózati kapcsolata az Azure-nal), látnia kell a jegyzékfájlokban a git-tárházban leírt, a fürtön létrehozott objektumokat.

## <a name="next-steps"></a>Következő lépések

[Azure monitor beállítása a tárolók számára az Azure arc-kompatibilis Kubernetes-fürtökkel](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
