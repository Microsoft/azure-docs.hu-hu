---
title: Az összetevők hibáinak diagnosztizálása egy Azure DevTest Labs virtuális gépen
description: A DevTest Labs olyan információkat biztosít, amelyek segítségével diagnosztizálhatja az összetevő meghibásodását. Ez a cikk bemutatja, hogyan lehet elhárítani az összetevők hibáit.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480813"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Az összetevők hibáinak diagnosztizálása a laborban 
Miután létrehozott egy összetevőt, megtekintheti, hogy sikeres vagy sikertelen volt-e. A Azure DevTest Labs található összetevők naplói olyan információkat tartalmaznak, amelyek segítségével diagnosztizálhatja az összetevő meghibásodását. A Windows rendszerű virtuális gépek esetében több lehetőség közül választhat:

* Az Azure Portalon
* A virtuális gépen

> [!NOTE]
> Annak érdekében, hogy a hibák azonosítása és magyarázata sikeres legyen, fontos, hogy az összetevő megfelelő struktúrával rendelkezik. Az összetevők megfelelő létrehozásával kapcsolatos információkért lásd: egyéni összetevők [létrehozása](devtest-lab-artifact-author.md). Ha egy megfelelően strukturált összetevőre vonatkozó példát szeretne látni, tekintse meg a [teszt paraméter típusait](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Az összetevők hibáinak elhárítása a Azure Portal használatával

1. A Azure Portal az erőforrások listájában válassza ki a labort.
2. Válassza ki azt a Windows rendszerű virtuális gépet, amely tartalmazza a vizsgálni kívánt összetevőt.
3. A bal oldali panelen az **általános** területen válassza az **összetevők elemet.** Megjelenik az adott virtuális géphez társított összetevők listája. Az összetevő neve és az összetevő állapota jelezve.

   ![Összetevő állapota](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Olyan összetevőt válasszon, amely egy **sikertelen** állapotot mutat. Megnyílik az összetevő. Megjelenik egy olyan bővítményi üzenet, amely tartalmazza az összetevő hibájának részleteit.

   ![Összetevő hibaüzenete](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Az összetevők hibáinak elhárítása a virtuális gépen

1. Jelentkezzen be a virtuális gépre, amely tartalmazza a diagnosztizálni kívánt összetevőt.
2. Nyissa meg a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension \\ *1,9*\Status, ahol a *1,9* az Azure egyéni szkript bővítmény verziószáma.

   ![Az állapot fájlja](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Nyissa meg az **állapot** fájlt.

A naplófájlok **Linux** rendszerű virtuális gépen való megtalálásával kapcsolatos utasításokért tekintse meg a következő cikket: [Az Azure egyéni szkriptek bővítményének használata a 2. verzióban a Linux Virtual Machines használatával](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Virtuális gép csatlakoztatása meglévő Active Directory tartományhoz egy Resource Manager-sablonnal a DevTest Labs szolgáltatásban](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [adhat hozzá git-tárházat a laborhoz](devtest-lab-add-artifact-repo.md).

