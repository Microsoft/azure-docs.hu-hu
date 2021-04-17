---
title: Paraméterfájl létrehozása
description: Paraméterfájl létrehozása az értékek átadáshoz a sablon üzembe helyezése Azure Resource Manager során
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531213"
---
# <a name="create-resource-manager-parameter-file"></a>Paraméterfájl Resource Manager létrehozása

Ahelyett, hogy beágyazott értékként ad át paramétereket a szkriptben, használhat egy JSON-fájlt, amely tartalmazza a paraméterértékeket. Ez a cikk bemutatja, hogyan hozhat létre egy JSON-sablonnal vagy Bicep-fájllal használt paraméterfájlt.

## <a name="parameter-file"></a>Paraméterfájl

A paraméterfájl a következő formátumot használja:

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

Figyelje meg, hogy a paraméterfájl egyszerű szövegként tárolja a paraméterértékeket. Ez a módszer nem bizalmas értékekhez, például erőforrás-termékváltozathoz működik. Az egyszerű szöveg bizalmas értékek, például jelszavak esetén nem működik. Ha bizalmas értéket tartalmazó paramétert kell átadnia, tárolja az értéket egy kulcstartóban. Ezután hivatkozhat a kulcstartóra a paraméterfájlban. A rendszer az üzembe helyezés során biztonságosan lekéri a bizalmas értéket.

A következő paraméterfájl egy egyszerű szöveges értéket és egy kulcstartóban tárolt bizalmas értéket tartalmaz.

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

A kulcstartók értékeinek használatával kapcsolatos további információkért lásd: Use Azure Key Vault to pass secure parameter value during deployment (Biztonságos Azure Key Vault paraméterértékek megadása [az üzembe helyezés során).](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Paraméterértékek definiálása

A paraméterek nevének és értékeinek meghatározásához nyissa meg a JSON- vagy Bicep-sablont. Nézze meg a sablon parameters szakaszát. Az alábbi példák a JSON- és Bicep-sablonok paramétereit mutatják be.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

A paraméterfájlban az első részlet, amit fel kell venni, az egyes paraméterek neve. A paraméterfájlban szereplő paraméterneveknek egyezniük kell a sablonban szereplő paraméternevekkel.

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

Figyelje meg a paraméter típusát. A paraméterfájlban található paramétertípusoknak a sablonnal azonos típusokat kell használniuk. Ebben a példában mindkét paramétertípus sztring.

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

Ellenőrizze, hogy a sablonban vannak-e alapértelmezett értékkel megadott paraméterek. Ha egy paraméter alapértelmezett értékkel rendelkezik, meg lehet adni egy értéket a paraméterfájlban, de ez nem kötelező. A paraméterfájl értéke felülírja a sablon alapértelmezett értékét.

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

Ellenőrizze a sablon megengedett értékeit és korlátozásait, például a maximális hosszt. Ezek az értékek határozzák meg a paraméterhez megadható értéktartományt. Ebben a példában a legfeljebb 11 karakterből állhat, és `storagePrefix` meg kell adnia egy engedélyezett `storageAccountType` értéket.

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

> [!NOTE]
> A paraméterfájl csak a sablonban definiált paraméterek értékeit tartalmazhatja. Ha a paraméterfájl olyan további paramétereket tartalmaz, amelyek nem egyeznek a sablon paramétereivel, hibaüzenetet kap.

## <a name="parameter-type-formats"></a>Paramétertípusok formátumai

Az alábbi példa a különböző paramétertípusok formátumait mutatja be: sztring, egész szám, logikai érték, tömb és objektum.

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

## <a name="deploy-template-with-parameter-file"></a>Sablon üzembe helyezése paraméterfájllal

Az Azure CLI-ről átad egy helyi paraméterfájlt a `@` használatával, és a paraméterfájl nevét. Például: `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

További információ: Erőforrások üzembe [helyezése ARM-sablonokkal és az Azure CLI-val.](./deploy-cli.md#parameters) A _.bicep-fájlok üzembe_ helyezéséhez az Azure CLI 2.20-as vagy újabb verziójára van szükség.

A Azure PowerShell helyi paraméterfájlt a paraméter használatával `TemplateParameterFile` ad át.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

További információ: Erőforrások üzembe [helyezése ARM-sablonokkal és Azure PowerShell.](./deploy-powershell.md#pass-parameter-values) A _.bicep-fájlok telepítéséhez_ 5.6.0 Azure PowerShell vagy újabb verzió szükséges.

> [!NOTE]
> Paraméterfájl nem használható a portál egyéni sablon panelével.

> [!TIP]
> Ha az Azure-erőforráscsoport projektet használja a [Visual Studio,](create-visual-studio-deployment-project.md)győződjön meg arról, hogy a paraméterfájl **Build Action** (Összeállítási művelet) értéke **Content (Tartalom).**

## <a name="file-name"></a>Fájlnév

A paraméterfájl általános elnevezési konvenciója, hogy _paramétereket tartalmaz a_ sablonnévben. Ha például a sablon neve _azuredeploy.json_, a paraméterfájl neveazuredeploy.parameters.js _a következőn:_. Ez az elnevezési konvenció segít megérteni a sablon és a paraméterek közötti kapcsolatot.

A különböző környezetekben való üzembe helyezéshez több paraméterfájlt is létre kell hoznia. A paraméterfájlok elnevezésekor azonosítsa a használatukat, például a fejlesztést és az éles üzemet. Erőforrások üzembe _helyezéséhez_ azuredeploy.parameters-dev.jsés _azuredeploy.parameters-prod.jsbe._

## <a name="parameter-precedence"></a>Paraméter precedenciása

Ugyanabban az üzembe helyezési műveletben beágyazott paramétereket és helyi paraméterfájlt is használhat. Megadhat például néhány értéket a helyi paraméterfájlban, és további értékeket adhat hozzá beágyazottan az üzembe helyezés során. Ha a helyi paraméterfájlban és a beágyazottban is ad meg értékeket egy paraméterhez, a beágyazott érték élvez elsőbbséget.

Külső paraméterfájl is használható a fájl URI-ját megadva. Külső paraméterfájl használata esetén más értékeket sem beágyazott, sem helyi fájlból nem lehet átadni. A rendszer minden beágyazott paramétert figyelmen kívül hagy. Adja meg a külső fájl összes paraméterértékét.

## <a name="parameter-name-conflicts"></a>Paraméternév-ütközések

Ha a sablon a PowerShell-parancs egyik paraméterével azonos nevű paramétert tartalmaz, a PowerShell a paramétert a(z) utótaggal mutatja be a `FromTemplate` sablonból. Például egy nevű paraméter ütközik a `ResourceGroupName` `ResourceGroupName` [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmag paraméterével. A rendszer kéri, hogy adja meg a `ResourceGroupNameFromTemplate` értékét. A félreértések elkerülése érdekében használjon olyan paraméterneveket, amelyek nem használatosak az üzembe helyezési parancsokhoz.

## <a name="next-steps"></a>Következő lépések

- További információ a paraméterek sablonban való definiálásról: [Paraméterek az ARM-sablonokban.](template-parameters.md)
- A kulcstartók értékeinek használatával kapcsolatos további információkért lásd: Use Azure Key Vault to pass secure parameter value during deployment (Biztonságos Azure Key Vault paraméterértékek megadása [az üzembe helyezés során).](key-vault-parameter.md)
