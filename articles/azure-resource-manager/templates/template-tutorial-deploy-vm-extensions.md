---
title: Virtuálisgép-bővítmények üzembe helyezése sablonnal
description: Útmutató virtuális gépi bővítmények üzembe helyezéséhez Azure Resource Manager-sablonokkal.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fb99babfd53b26874bed62183871d13ae0ae4baf
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "86120126"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Oktatóanyag: virtuálisgép-bővítmények üzembe helyezése ARM-sablonokkal

Ismerje meg, hogyan használhatja az [Azure-beli virtuális gépi bővítményeket](../../virtual-machines/extensions/features-windows.md) Azure-beli virtuális gépeken az üzembe helyezés utáni konfigurációs és automatizálási feladatokhoz. Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. Ebben az oktatóanyagban egy Azure Resource Manager (ARM) sablonból telepít egy egyéni parancsfájl-bővítményt egy PowerShell-parancsfájl futtatásához egy Windows rendszerű virtuális gépen.  A szkript telepíti a webkiszolgálót a virtuális gépen.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * PowerShell-szkript előkészítése
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Tekintse meg a rövid útmutató [: Azure Resource Manager sablonok létrehozása Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [oktatóanyag: Azure Key Vault integrálása az ARM-sablonok üzembe helyezésével](./template-tutorial-use-key-vault.md). Azt is javasoljuk, hogy három havonta frissítse a jelszavát.

## <a name="prepare-a-powershell-script"></a>PowerShell-szkript előkészítése

Használhat beágyazott PowerShell-parancsfájlt vagy parancsfájlt is.  Ez az oktatóanyag bemutatja, hogyan használható egy parancsfájl. A [githubon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)a következő tartalommal rendelkező PowerShell-szkriptek vannak megosztva:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha úgy dönt, hogy közzéteszi a fájlt a saját helyén, az `fileUri` oktatóanyag későbbi részében frissítse az elemet a sablonban.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure Gyorsindítás sablonjai az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **fájl**  >  **megnyitott**fájl elemet.
1. Illessze be a következő URL-címet a **fájlnév** mezőbe:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. A fájl megnyitásához válassza a **Megnyitás**lehetőséget.
    A sablon öt erőforrást határoz meg:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     A Testreszabás előtt hasznos lehet a sablon alapvető megismerése.

1. Mentse a fájl egy példányát a helyi számítógépre *azuredeploy.jsa* következő néven: a **fájl**  >  **Mentés másként lehetőség**kiválasztásával.

## <a name="edit-the-template"></a>A sablon szerkesztése

Adjon hozzá a meglévő sablonhoz egy virtuális gépi bővítmény erőforrást a következő tartalommal:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Az erőforrás-definícióval kapcsolatos további információkért tekintse meg a [bővítmények referenciáját](/azure/templates/microsoft.compute/virtualmachines/extensions). A következők a fontosabb elemek:

* **name**: Mivel a bővítményerőforrás a virtuális gép objektum gyermekerőforrása, a nevének tartalmaznia kell a virtuális gép nevének előtagját. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md).
* **dependsOn**: hozza létre a bővítmény erőforrást a virtuális gép létrehozása után.
* **fileUris**: a parancsfájlokat tároló hely. Ha úgy dönt, hogy nem használja a megadott helyet, frissítenie kell az értékeket.
* **commandToExecute**: Ez a parancs meghívja a parancsfájlt.

A beágyazott parancsfájl használatához távolítsa el a **fileUris**, és frissítse a **commandToExecute** a következőre:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Ez a beágyazott parancsfájl frissíti a iisstart.html tartalmát is.

Meg kell nyitnia a HTTP-portot is, hogy el tudja érni a webkiszolgálót.

1. Keresse meg a **securityRules** a sablonban.
1. Adja hozzá a következő szabályt az **alapértelmezett-Allow-3389**elem mellett.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A telepítési eljárással kapcsolatban tekintse meg az [oktatóanyag: ARM-sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)című témakör "a sablon telepítése" című szakaszát. Javasoljuk, hogy a virtuális gép rendszergazdai fiókjához generált jelszót használjon. Tekintse meg ezt a cikk [előfeltételeit](#prerequisites) ismertető szakaszt.

A Cloud Shell futtassa a következő parancsot a virtuális gép nyilvános IP-címének lekéréséhez:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Illessze be az IP-címet egy webböngészőbe. Megnyílik az alapértelmezett Internet Information Services (IIS) Kezdőlap:

![A Internet Information Services kezdőlapja](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az üzembe helyezett Azure-erőforrásokra, törölje azokat az erőforráscsoport törlésével.

1. A Azure Portal bal oldali ablaktábláján válassza az **erőforráscsoport**elemet.
2. A **szűrés név alapján** mezőbe írja be az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
    Az erőforráscsoporthoz hat erőforrás jelenik meg.
4. A felső menüben válassza az **erőforráscsoport törlése**elemet.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy virtuális gépet és egy virtuális gépi bővítményt. A bővítmény telepítette az IIS-webkiszolgálót a virtuális gépen. A következő témakörből megtudhatja, hogyan használhatja a Azure SQL Database bővítményt egy BACPAC-fájl importálásához:

> [!div class="nextstepaction"]
> [SQL-bővítmények üzembe helyezése](./template-tutorial-deploy-sql-extensions-bacpac.md)
