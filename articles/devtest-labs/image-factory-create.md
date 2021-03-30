---
title: Rendszerkép-előállító létrehozása a Azure DevTest Labsban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan állíthat be egyéni rendszerkép-előállítót a git-tárházban (Azure DevTest Labs) elérhető minta-parancsfájlok használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85482071"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Egyéni rendszerkép-előállító létrehozása Azure DevTest Labs
Ez a cikk bemutatja, hogyan állíthat be egyéni rendszerkép-előállítót a [git-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)elérhető minta-parancsfájlok használatával.

## <a name="whats-an-image-factory"></a>Mi az a rendszerkép-előállító?
A rendszerkép-előállító egy olyan konfigurációs kód, amely az összes kívánt konfigurációval rendszeres időközönként automatikusan létrehozza és elosztja a lemezképeket. A rendszerkép-előállítóban található rendszerképek mindig naprakészek, és a folyamatos karbantartás csaknem nulla, ha a teljes folyamat automatizálva van. És mivel az összes szükséges konfiguráció már szerepel a képen, a rendszer manuálisan konfigurálja a rendszert, miután létrehozta a virtuális gépet az alap operációs rendszerrel.

A DevTest Labs szolgáltatásban az egyéni rendszerképeket használva a fejlesztői asztalt egy kész állapotba veheti fel a jelentős gyorssegéd. Az Egyéni rendszerképek hátránya az, hogy van valami további a laborban való karbantartáshoz. Például a termékek próbaverziójának időbeli lejárta (vagy) az újonnan kiadott biztonsági frissítések nem érvényesek, ami arra kényszeríti az Egyesült Államokat, hogy rendszeresen frissítse az egyéni rendszerképet. A rendszerkép-előállítóval a forráskód vezérlőelemben bejelölt lemezkép definíciója van, és automatizált folyamattal rendelkezik, hogy a definíció alapján hozzon létre egyéni lemezképeket.

A megoldás lehetővé teszi, hogy a virtuális gépeket egyéni lemezképek alapján hozza létre, és ezzel kiküszöbölje a további folyamatos karbantartási költségeket. Ezzel a megoldással automatikusan létrehozhat egyéni lemezképeket, terjesztheti azokat más DevTest Labs-be, és kivonja a régi lemezképeket. A következő videóban megismerheti a rendszerkép-előállítót, valamint azt, hogy miként implementálható a DevTest Labs szolgáltatással.  Az összes Azure PowerShell-szkript szabadon elérhető, és itt található:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>A megoldás magas szintű áttekintése
A megoldás lehetővé teszi, hogy a virtuális gépeket egyéni lemezképek alapján hozza létre, és ezzel kiküszöbölje a további folyamatos karbantartási költségeket. Ezzel a megoldással automatikusan létrehozhat egyéni lemezképeket, és terjesztheti azokat más DevTest Labs-be. A DevTest Labs összes műveletének automatizálásához használja az Azure DevOps (korábban Visual Studio Team Services).

![A megoldás magas szintű áttekintése](./media/create-image-factory/high-level-view-of-solution.png)

A [DevTest Labs vsts-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) lehetővé teszi, hogy végrehajtsa ezeket az egyes lépéseket:

- Egyéni kép létrehozása
- Virtuális gép létrehozása
- Virtuális gép törlése
- Környezet létrehozása
- Környezet törlése
- Környezet feltöltése

Az DevTest Labs bővítmény használatával egyszerűen megkezdheti az egyéni lemezképek automatikus létrehozását a DevTest Labs szolgáltatásban.

A PowerShell-szkriptek egy másik implementációt használnak összetettebb forgatókönyvhöz. A PowerShell használatával teljes mértékben automatizálható egy DevTest Labs-alapú rendszerkép-előállító, amely a folyamatos integrációs és folyamatos teljesítési (CI/CD) toolchain használható. A másodlagos megoldásban követett alapelvek a következők:

- A gyakori frissítésekhez nem szükséges módosítani a rendszerkép-előállítót. (például új típusú egyéni rendszerkép hozzáadásával, a régi rendszerképek automatikus kivonásával, új "Endpoint" DevTest Labs hozzáadásával egyéni lemezképek fogadásához stb.)
- A gyakori módosításokat a forráskód-ellenőrzés (az infrastruktúra mint kód) támogatja.
- Előfordulhat, hogy az egyéni lemezképeket fogadó DevTest Labs nem ugyanabban az Azure-előfizetésben van (Labs span előfizetések).
- A PowerShell-szkripteket újra fel kell használni, hogy szükség esetén további gyárakat is felhozzunk

## <a name="next-steps"></a>Következő lépések
Lépjen be a szakasz következő cikkére: a [rendszerkép-előállító futtatása az Azure DevOps](image-factory-set-up-devops-lab.md)
