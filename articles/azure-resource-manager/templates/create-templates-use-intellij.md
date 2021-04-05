---
title: Sablon üzembe helyezése – IntelliJ IDEA
description: Megtudhatja, hogyan hozhatja létre az első Azure Resource Manager-sablont (ARM-sablon) a IntelliJ IDEA használatával, és hogyan helyezheti üzembe.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: f0e007fe8930b30cee1c95159d7e964e3792449f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905961"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>ARM-sablonok létrehozása és üzembe helyezése a IntelliJ IDEA használatával

Megtudhatja, hogyan helyezhet üzembe egy Azure Resource Manager-sablont (ARM-sablont) az Azure-ban a IntelliJ IDEA használatával, valamint a sablon szerkesztésének és frissítésének folyamatát közvetlenül az IDE-ből. Az ARM-sablonok JSON-fájlok, amelyek meghatározzák azokat az erőforrásokat, amelyeket telepítenie kell a megoldásához. Az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md)című témakört.

![Resource Manager-sablonok portáljának diagramja](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Az oktatóanyag befejezése után üzembe kell helyeznie egy Azure Storage-fiókot. Ugyanez a folyamat használható más Azure-erőforrások üzembe helyezésére is.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Telepített [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition vagy Community Edition
* Telepített [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053), további információért lásd az [IntelliJ beépülő moduljainak felügyeleti útmutatóját](https://www.jetbrains.com/help/idea/managing-plugins.html)
* [Legyen bejelentkezve](/azure/developer/java/toolkit-for-intellij/sign-in-instructions) az Azure-fiókjába az Azure Toolkit for IntelliJ eléréséhez

## <a name="deploy-a-quickstart-template"></a>Gyorsindítás sablon üzembe helyezése

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure Gyorsindítás sablonjai az ARM-sablonok tárháza. A cikkben használt sablon neve [standard Storage-fiók létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Egy Azure Storage-fiók erőforrását határozza meg.

1. Kattintson a jobb gombbal, és mentse a [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) és a [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) helyi számítógépre.

1. Ha az Azure-eszközkészlet megfelelően van telepítve és bejelentkezve, az Azure Explorer az IntelliJ IDEA oldalsávjában jelenik meg. Kattintson a jobb gombbal az **Erőforrás-kezelés** elemre, és válassza a **központi telepítés létrehozása** elemet.

    ![Resource Manager-sablon – kattintson a jobb gombbal az üzembe helyezés létrehozásához](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Adja meg a **központi telepítési nevet**, az **előfizetést**, az **erőforráscsoportot** és a **régiót**. Itt telepítjük a sablont egy új erőforráscsoporthoz `testRG` . Ezután válassza ki a letöltött **erőforrás-sablon** elérési útját `azuredeploy.json` és az **erőforrás-paramétereket** `azuredeploy.parameters.json` .

    ![Resource Manager-sablon a központi telepítés létrehozására szolgáló fájlok kiválasztása](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Miután rákattintott az OK gombra, a rendszer elindítja a központi telepítést. Az üzembe helyezés befejezéséig a IntelliJ ötlet alján található **állapotjelző sáv** előrehaladása látható.

    ![Resource Manager-sablon telepítési állapota](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Meglévő üzemelő példány tallózása

1. Az üzembe helyezés után megtekintheti az új erőforráscsoportot, `testRG` és létrehozhatja az új központi telepítést. Kattintson a jobb gombbal a központi telepítésre, és megtekintheti a lehetséges műveletek listáját. Most válassza a **Tulajdonságok megjelenítése** lehetőséget.

    ![Resource Manager-sablon – Tallózás központi telepítés](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Ekkor megnyílik egy lap nézet, amely néhány hasznos tulajdonságot, például a telepítési állapotot és a sablon szerkezetét jeleníti meg.

    ![Resource Manager-sablon a központi telepítési tulajdonságokat jeleníti meg](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Meglévő üzemelő példány szerkesztése és frissítése

1. Válassza a **központi telepítés szerkesztése** lehetőséget a jobb gombbal, vagy a Tulajdonságok megjelenítése nézetet előtt. Megnyílik egy másik lap nézet, amely megjeleníti az Azure-beli üzembe helyezés sablonját és paramétereit. Ha a fájlokat a helyi fájlba szeretné menteni, kattintson a **sablonfájl exportálása**  vagy a **paraméterérték exportálása** lehetőségre.

    ![Resource Manager-sablon – telepítés szerkesztése](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Ezen a lapon szerkesztheti a két fájlt, és üzembe helyezheti az Azure-ban történt módosításokat. Itt módosítjuk a **tárfióktípus** értékét a rendszerből a-ból `Standard_LRS` `Standard_GRS` . Ezután kattintson az alul lévő **központi telepítés frissítése** elemre, és erősítse meg a frissítést.

    ![A képernyőképen látható, hogy melyik Resource Manager-sablon jelenik meg a frissítés központi telepítésének megadásával.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. A frissítés telepítésének befejezése után ellenőrizheti a portálon, hogy a létrehozott Storage-fiók módosult-e `Standard_GRS` .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ezt elvégezheti Azure Portal vagy az Azure CLI-vel. Az Azure Explorerben a IntelliJ ÖTLETből kattintson a jobb gombbal a létrehozott **erőforráscsoport** elemre, és válassza a Törlés lehetőséget.

    ![Erőforráscsoport törlése az Azure Explorerben a IntelliJ IDEA-ből](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Figyelje meg, hogy a központi telepítés törlése nem törli az üzemelő példány által létrehozott erőforrásokat. Ha már nincs szüksége rájuk, törölje a megfelelő erőforráscsoportot vagy adott erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ennek a cikknek a fő témája a IntelliJ IDEA használata egy meglévő sablon üzembe helyezéséhez az Azure Gyorsindítás sablonjaiból. Azt is megtanulta, hogyan tekintheti meg és frissítheti a meglévő üzembe helyezést az Azure-ban. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. A sablonok fejlesztésével kapcsolatos további tudnivalókért tekintse meg az új kezdő oktatóanyag-sorozatot:

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [A Java megnyitása az Azure fejlesztői központban](/azure/java)
