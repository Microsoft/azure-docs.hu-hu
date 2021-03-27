---
title: Bicep-fájlok létrehozása – Visual Studio Code
description: Az Azure-erőforrások üzembe helyezéséhez használja a Visual Studio Code és a bicep-bővítményt a bicep-fájlok számára
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 4d1064351ddfacdebfa67fd9b2f517f592de3a7c
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612889"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Rövid útmutató: bicep-fájlok létrehozása a Visual Studio Code-ban

A Visual Studio Code-hoz készült bicep bővítmény nyelvi támogatást és erőforrás-újratöltést biztosít. Ezek az eszközök segítenek létrehozni és érvényesíteni a [bicep](./bicep-overview.md) -fájlokat. Ebben a rövid útmutatóban a bővítmény használatával hozhat létre teljesen új bicep-fájlt. Ennek során a bővítmények képességeit, például az ellenőrzést és a befejezést is megtapasztalhatja.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

A rövid útmutató elvégzéséhez szüksége lesz a [Visual Studio Code](https://code.visualstudio.com/)-ra, amelyen telepítve van a [bicep bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) . Szüksége lesz a legújabb [Azure CLI](/cli/azure/) -re vagy a legújabb Azure PowerShell telepített és hitelesített [modulra](/powershell/azure/new-azureps-module-az) is.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-a-bicep-file"></a>Bicep-fájl létrehozása

Hozzon létre és nyisson meg egy *azuredeploy. bicep* nevű új fájlt a Visual Studio Code-ban.

## <a name="add-an-azure-resource"></a>Azure-erőforrás hozzáadása

Adjon hozzá egy alapszintű Storage-fiók erőforrást a bicep-fájlhoz.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

Az erőforrás deklarációja négy összetevőből áll:

- **erőforrás**: kulcsszó.
- **szimbolikus név** (STG): a szimbolikus név egy azonosító, amely az erőforrásra hivatkozik a bicep-fájlon belül. Az erőforrás neve nem az, hogy mikor lesz telepítve. Az erőforrás nevét a **Name (név** ) tulajdonság határozza meg.  Lásd a lista negyedik összetevőjét.
- **erőforrástípus** ( Microsoft.Storage/storageAccounts@2019-06-01 ): az erőforrás-szolgáltató (Microsoft. Storage), az erőforrás típusa (storageAccounts) és a apiVersion (2019-06-01) áll. Mindegyik erőforrás-szolgáltató közzéteszi a saját API-verzióit, így ez az érték a típusra jellemző. Több típust és apiVersions is találhat az [ARM-sablonok referenciájának](/azure/templates/)különböző Azure-erőforrásaihoz.
- **Tulajdonságok** (minden belül = {...}): adja meg az erőforrástípus tulajdonságait. Minden erőforrás rendelkezik egy `name` tulajdonsággal. A legtöbb erőforráshoz tartozik egy `location` tulajdonság is, amely azt a régiót állítja be, ahol az erőforrás telepítve van. A többi tulajdonság az erőforrás típusa és az API-verzió szerint változhat.

## <a name="completion-and-validation"></a>Befejezés és érvényesítés

A bővítmény egyik legerősebb funkciója az Azure-sémákkal való integráció. Az Azure-sémák az ellenőrzési és erőforrás-kompatibilis befejezési képességekkel biztosítják a bővítményt. Módosítsa a Storage-fiókot az érvényesítés és a Befejezés működés közbeni megtekintéséhez.

Először frissítse a Storage-fiók típusát egy érvénytelen értékre, például: `megaStorage` . Figyelje meg, hogy ez a művelet egy figyelmeztetést állít elő, amely `megaStorage` nem érvényes érték.

![Érvénytelen tárolási konfigurációt mutató kép](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

A befejezési képességek használatához távolítsa el `megaStorage` a kurzort az aposztrófok között, és nyomja meg a gombot `ctrl`  +  `space` . Ez a művelet az érvényes értékek befejezési listáját jeleníti meg.

![A bővítmény automatikus kiegészítését bemutató kép](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Paraméterek hozzáadása

Most hozzon létre és használjon egy paramétert a Storage-fiók nevének megadásához.

Adja hozzá a következő kódot a fájl elejéhez:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Az Azure Storage-fiókok neveinek legalább 3 karakterből kell lenniük, és legfeljebb 24 karakterből állhatnak. `minLength`A és `maxLength` a használatával adja meg a megfelelő értékeket.

Most a Storage erőforrásban frissítse a Name (név) tulajdonságot a paraméter használatára. Ehhez távolítsa el az aktuális tárolási erőforrás nevét, beleértve az aposztrófokat is. nyomja meg a gombot `ctrl`  +  `space` . Válassza ki a **storageAccountName** paramétert a listából. Figyelje meg, hogy a paraméterek közvetlenül is szerepelhetnek a bicep-beli nevük használatával. A JSON-sablonokhoz paraméter () függvény szükséges.

![A következő kép mutatja be az automatikus kiegészítést, ha paramétereket használ a bicep-erőforrásokban](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>A bicep-fájl üzembe helyezése

Nyissa meg az integrált Visual Studio Code Terminalt a `ctrl`  +  ```` ` ```` kulcs kombinációjának használatával, módosítsa az aktuális könyvtárat a bicep-fájl helyére, majd használja az Azure CLI vagy a Azure PowerShell modult a bicep-fájl üzembe helyezéséhez.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
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
> [Kezdő bicep-oktatóanyagok](./bicep-tutorial-create-first-bicep.md)
