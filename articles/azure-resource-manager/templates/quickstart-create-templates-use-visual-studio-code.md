---
title: Sablon létrehozása – Visual Studio Code
description: A Visual Studio Code és a Azure Resource Manager Tools bővítmény használatával dolgozhat Azure Resource Manager-sablonokban (ARM-sablonok).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 97f956c1b1c1ea18242dc656c533451eda29dc6d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179051"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Rövid útmutató: ARM-sablonok létrehozása a Visual Studio Code-ban

A Visual Studio Code-hoz készült Azure Resource Manager Tools biztosítja a nyelvi támogatást, az erőforrás-kódrészleteket és az erőforrás-kiegészítést. Ezek az eszközök segítenek Azure Resource Manager-sablonok (ARM-sablonok) létrehozásában és ellenőrzésében. Ebben a rövid útmutatóban a bővítmény használatával hozzon létre egy ARM-sablont a semmiből. Ennek során a bővítmények funkcióit, például az ARM-sablonok részleteit, az érvényesítést, a befejezést és a paraméteres fájlok támogatását tapasztalhatja.

A rövid útmutató elvégzéséhez szüksége lesz a [Visual Studio Code](https://code.visualstudio.com/)-ra, amelyen telepítve van a [Azure Resource Manager Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) . Az [Azure CLI](/cli/azure/) -t vagy az Azure PowerShell- [modult](/powershell/azure/new-azureps-module-az?view=azps-3.7.0) is telepítenie és hitelesítenie kell.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-arm-template"></a>ARM-sablon létrehozása

Hozzon létre és nyisson meg egy új, *azuredeploy.js* nevű fájlt a Visual Studio Code-ban. Adja meg `arm` a Kódszerkesztő alkalmazást, amely az ARM-sablonokhoz Azure Resource Manager kódrészleteket indít el.

Válassza ki `arm!` Az Azure-erőforráscsoport üzembe helyezéséhez hatókörrel rendelkező sablon létrehozásához.

![A kép Azure Resource Manager állványok töredékeit mutatja](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Ez a kódrészlet létrehoz egy ARM-sablon alapszintű építőelemeit.

![Egy teljesen összeszerelő ARM-sablont ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Figyelje meg, hogy a Visual Studio Code nyelvi módja *JSON* -ról *Azure Resource Manager sablonra* módosult. A bővítmény az ARM-sablonokra jellemző nyelvi kiszolgálót tartalmaz, amely ARM sablon-specifikus érvényesítési, befejezési és egyéb nyelvi szolgáltatásokat biztosít.

![Kép, amely a Visual Studio Code nyelvi módjában Azure Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Azure-erőforrás hozzáadása

A bővítmény számos Azure-erőforráshoz tartalmaz kódrészleteket. Ezek a kódrészletek az erőforrások egyszerű hozzáadására használhatók a sablon üzembe helyezéséhez.

Helyezze a kurzort a sablon **erőforrásainak** blokkba, írja be a következőt `storage` , és válassza ki a *ARM-Storage* kódrészletet.

![Az ARM-sablonba felvenni kívánt erőforrást ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Ez a művelet hozzáadja a tárolási erőforrást a sablonhoz.

![Az ARM-sablonban egy Azure Storage-erőforrást ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/5.png)

A **Tab** billentyűt a Storage-fiók konfigurálható tulajdonságai lapon lehet használni.

![Kép, amely bemutatja, hogyan használható a TAB billentyű az erőforrás-konfigurációban való böngészéshez](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Befejezés és érvényesítés

A bővítmény egyik legerősebb funkciója az Azure-sémákkal való integráció. Az Azure-sémák az ellenőrzési és erőforrás-kompatibilis befejezési képességekkel biztosítják a bővítményt. Módosítsa a Storage-fiókot az érvényesítés és a Befejezés működés közbeni megtekintéséhez.

Először frissítse a Storage-fiók típusát egy érvénytelen értékre, például: `megaStorage` . Figyelje meg, hogy ez a művelet egy figyelmeztetést állít elő, amely `megaStorage` nem érvényes érték.

![Érvénytelen tárolási konfigurációt mutató kép](./media/quickstart-create-templates-use-visual-studio-code/7.png)

A befejezési képességek használatához távolítsa el `megaStorage` a kurzort a idézőjelek között, és nyomja meg a gombot `ctrl`  +  `space` . Ez a művelet az érvényes értékek befejezési listáját jeleníti meg.

![A bővítmény automatikus kiegészítését bemutató kép](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Sablon paramétereinek hozzáadása

Most hozzon létre és használjon egy paramétert a Storage-fiók nevének megadásához.

Vigye a kurzort a paraméterek blokkba, adjon hozzá egy kocsivissza értéket, írja be a karaktert, `"` majd válassza ki a `new-parameter` kódrészletet. Ezzel a művelettel egy általános paramétert adhat hozzá a sablonhoz.

![Az ARM-sablonba felvenni kívánt paramétert ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Frissítse a paraméter nevét `storageAccountName` és a leírását a következőre: `Storage Account Name` .

![Az ARM-sablon befejezett paraméterét ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Az Azure Storage-fiókok neveinek legalább 3 karakterből kell lenniük, és legfeljebb 24 karakterből állhatnak. Adja hozzá `minLength` mind `maxLength` a, mind a paramétert, és adja meg a megfelelő értékeket.

![A minLength és a maxLength egy ARM-sablon paraméterbe való felvételét bemutató kép](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Most a Storage erőforrásban frissítse a Name (név) tulajdonságot a paraméter használatára. Ehhez távolítsa el az aktuális nevet. Írjon be egy dupla idézőjelet és egy nyitó szögletes zárójelet `[` , amely az ARM-sablon funkcióinak listáját állítja elő. Válassza ki a *paramétereket* a listából.

![Az ARM-sablon erőforrásaiban paraméterek használatakor automatikus kiegészítést ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/12.png)

`'`Ha a kör alakú zárójelben egyetlen idézőjelet ad meg, akkor a sablonban definiált összes paraméter (ebben az esetben a *storageAccountName*) listája megjelenik. Válassza ki a paramétert.

![Az ARM-sablon erőforrásának befejezett paraméterét bemutató kép](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Paraméterérték létrehozása

Az ARM-sablon paraméterének fájlja lehetővé teszi a környezet-specifikus paraméterek tárolását, és a központi telepítés időpontjában csoportosítva továbbítja ezeket az értékeket. Előfordulhat például, hogy egy olyan paraméter-fájllal rendelkezik, amely egy tesztkörnyezetben, egy másik pedig az éles környezethez tartozó értékeket tartalmaz.

A bővítmény használatával egyszerűen létrehozhat egy paramétert a meglévő sablonokból. Ehhez kattintson a jobb gombbal a sablonra a Kódszerkesztő alkalmazásban, és válassza a lehetőséget `Select/Create Parameter File` .

![Kép: az ARM-sablonból származó paraméterérték létrehozásához szükséges jobb kattintásos folyamat](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Válassza ki `New`  >  `All Parameters` > válassza ki a paraméter fájljának nevét és helyét.

![Kép: a fájl neve és mentése párbeszédpanel, amikor egy ARM-sablonból hoz létre paramétereket.](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Ez a művelet létrehoz egy új paraméter-fájlt, és leképezi azt a sablonnal, amelyből létrehozták. A sablon kiválasztása után megtekintheti és módosíthatja az aktuális sablon/paraméter fájl-hozzárendelést a Visual Studio Code állapotjelző sávjában.

![A Visual Studio Code állapotjelző sávján a sablon/paraméter fájl leképezését ábrázoló kép.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Most, hogy a paraméter fájlja le lett képezve a sablonra, a bővítmény a sablon és a paraméter fájlját is ellenőrzi. Ha szeretné megtekinteni ezt az ellenőrzést a gyakorlatban, adjon hozzá egy kétkarakteres értéket a paraméterhez `storageAccountName` a paraméter fájlban, és mentse a fájlt.

![A kép paraméterének hibája miatt egy érvénytelenített sablon látható](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Térjen vissza az ARM-sablonhoz, és figyelje meg, hogy hiba történt, ami azt jelzi, hogy az érték nem felel meg a paraméter feltételeinek.

![Egy érvényes ARM-sablont ábrázoló kép](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Frissítse az értéket a megfelelő értékre, mentse a fájlt, és térjen vissza a sablonhoz. Figyelje meg, hogy a paraméter hibája megoldódott.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Nyissa meg az integrált Visual Studio Code-terminált a `ctrl`  +  ```` ` ```` kulcs kombinációjának használatával, és használja az Azure CLI vagy a Azure PowerShell modult a sablon üzembe helyezéséhez.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az Azure-erőforrások már nem szükségesek, az Azure CLI vagy Azure PowerShell modullal törölheti a gyors üzembe helyezési erőforráscsoportot.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)
