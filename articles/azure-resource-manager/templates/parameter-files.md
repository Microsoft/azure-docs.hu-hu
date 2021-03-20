---
title: Paraméterfájl létrehozása
description: Paraméter létrehozása az értékek átadásához egy Azure Resource Manager sablon üzembe helyezése során
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 2b6d942b21594fa608127bb8f403e72295671005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89276643"
---
# <a name="create-resource-manager-parameter-file"></a>Resource Manager-paraméter fájljának létrehozása

Ahelyett, hogy a paramétereket beágyazott értékekként adná át a szkriptben, egyszerűbbnek találhatja egy olyan JSON-fájl használatát, amely tartalmazza a paraméterértékeket. Ez a cikk bemutatja, hogyan hozhatja létre a paramétert tartalmazó fájlt.

## <a name="parameter-file"></a>Paraméter fájlja

A paraméter fájljának formátuma a következő:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Figyelje meg, hogy a paraméterek értékei egyszerű szövegként vannak tárolva a paraméter fájlban. Ez a megközelítés olyan értékekhez használható, amelyek nem érzékenyek, például egy erőforrás SKU-jának megadását. A bizalmas értékek, például a jelszavak esetében nem működik. Ha egy bizalmas értéket paraméterként kell átadnia, tárolja az értéket egy kulcstartóban, és hivatkozzon a kulcstárolóra a paraméter fájljában. A rendszer az üzembe helyezés során biztonságos módon kéri le a bizalmas értéket.

A következő paraméterérték tartalmaz egy egyszerű szöveges értéket és egy Key vaultban tárolt értéket.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

További információ a Key Vault értékeinek használatáról: a [Azure Key Vault használata a biztonságos paraméterek értékének](key-vault-parameter.md)átadására az üzembe helyezés során.

## <a name="define-parameter-values"></a>Paraméter értékeinek meghatározása

Ha szeretné megtudni, hogyan határozhatja meg a paraméterek értékeit, nyissa meg az üzembe helyezni kívánt sablont. Tekintse meg a sablon paraméterek szakaszát. A következő példa egy sablon paramétereit jeleníti meg.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

A figyelmeztetés első részlete az egyes paraméterek neve. A paraméterben szereplő értékeknek meg kell egyezniük a nevekkel.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Figyelje meg a paraméter típusát. A paraméterben szereplő értékeknek azonos típusúaknak kell lenniük. Ehhez a sablonhoz karakterláncként is megadhatja a paramétereket.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Ezután keresse meg az alapértelmezett értéket. Ha egy paraméter alapértelmezett értékkel rendelkezik, megadhat egy értéket, de nem szükséges.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Végül tekintse meg az engedélyezett értékeket és a korlátozásokat, például a maximális hosszt. Megadják a paraméterhez megadható értékek tartományát.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

A paraméter fájlja csak a sablonban definiált paraméterek értékeit tartalmazza. Ha a paraméter-fájl olyan további paramétereket tartalmaz, amelyek nem egyeznek a sablon paraméterei között, hibaüzenetet kap.

## <a name="parameter-type-formats"></a>Paramétertípusok formátumai

A következő példában a különböző típusú paraméterek formátuma látható.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="deploy-template-with-parameter-file"></a>Sablon üzembe helyezése paraméter-fájllal

Ha egy helyi paramétert szeretne átadni az Azure CLI-vel, használja a @ nevet és a paraméter fájljának nevét.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

További információ: [erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel](./deploy-cli.md#parameters).

Ha Azure PowerShell használatával szeretne átadni egy helyi paramétert, használja a `TemplateParameterFile` paramétert.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

További információ: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](./deploy-powershell.md#pass-parameter-values)

> [!NOTE]
> A portál egyéni sablon paneljén nem lehet paraméter-fájlt használni.

## <a name="file-name"></a>Fájlnév

A paraméter elnevezésének általános konvenciója a **. Parameters** hozzáadása a sablon nevéhez. Ha például a sablon neve **azuredeploy.json**, a paraméter fájljának neve **azuredeploy.parameters.js**. Ez az elnevezési konvenció a sablon és a paraméterek közötti kapcsolat megtekintését segíti.

Ha különböző környezetekben kíván üzembe helyezni, hozzon létre egynél több paramétert. A paraméterérték elnevezése esetén adjon hozzá egy módszert a használat azonosításához. Használja például a **azuredeploy.parameters-dev.json** és a **azuredeploy.parameters-prod.json**

## <a name="parameter-precedence"></a>Paraméter prioritása

A beágyazott paramétereket és a helyi paramétereket is használhatja ugyanabban a telepítési műveletben. Megadhat például néhány értéket a helyi paraméter fájljában, és az üzembe helyezés során további értékeket is hozzáadhat. Ha a paraméter értékét a helyi paraméter fájljában és a beágyazott mezőben is megadja, a beágyazott érték elsőbbséget élvez.

Külső paramétert is használhat a fájl URI azonosítójának megadásával. Ha külső paramétert használ, nem adhat át más értékeket beágyazott vagy helyi fájlból. Az összes beágyazott paraméter figyelmen kívül lesz hagyva. Adja meg az összes paraméter értékét a külső fájlban.

## <a name="parameter-name-conflicts"></a>Paraméter neve ütközés

Ha a sablon egy, a PowerShell-parancsban szereplő paraméterekkel megegyező nevű paramétert tartalmaz, a PowerShell a sablonban található paramétert a Postfix **FromTemplate** mutatja be. A sablonban található **ResourceGroupName** nevű paraméter például ütközik a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmag **ResourceGroupName** paraméterével. A rendszer megkéri, hogy adjon meg egy értéket a **ResourceGroupNameFromTemplate** számára. Ezt a zavart a telepítési parancsokhoz nem használt paraméterek neveinek használatával lehet elkerülni.


## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg a [Azure Resource Manager sablonokban található paramétereket](template-parameters.md).
- További információ a Key Vault értékeinek használatáról: a [Azure Key Vault használata a biztonságos paraméterek értékének](key-vault-parameter.md)átadására az üzembe helyezés során.
- További információ a paraméterekről: [Azure Resource Manager sablonokban található paraméterek](template-parameters.md).
