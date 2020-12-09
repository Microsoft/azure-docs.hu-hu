---
title: Sablonok csatolása az üzembe helyezéshez
description: Azt ismerteti, hogyan használhatók a Azure Resource Manager sablonban található csatolt sablonok a moduláris sablonok megoldásához. Bemutatja, hogyan adhatók át a paraméterek értékei, meghatározhatók egy paraméterérték és dinamikusan létrehozott URL-címek.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 1e2ccc57b42f8072c9aa28612d534507b9a674ed
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852098"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Kapcsolt és beágyazott sablonok használata Azure-erőforrások üzembe helyezésekor

Összetett megoldások üzembe helyezéséhez számos kapcsolódó sablonban megszakíthatja a sablont, majd egy fő sablonon keresztül is üzembe helyezheti azokat. A kapcsolódó sablonok a fő sablonba ágyazott fájlok vagy sablon-szintaxisok lehetnek. Ez a cikk a **társított sablon** kifejezését használja egy külön sablonfájl használatára, amelyre a fő sablon hivatkozásán keresztül hivatkozik. A **beágyazott sablon kifejezést használja** a beágyazott sablon szintaxisára a fő sablonon belül.

Kis és közepes mérető megoldások esetében könnyebb egyetlen sablont megérteni és karbantartani. Így az összes erőforrás és értéke egyetlen fájlban látható. Speciális felhasználási helyzetekben a megoldás csatolt sablonokkal bontható fel a kívánt összetevőkre. Az ilyen sablonok egyszerűen felhasználhatók újra más helyzetekben.

Oktatóanyagért lásd [: csatolt Azure Resource Manager sablonok létrehozása](./deployment-tutorial-linked-template.md).

> [!NOTE]
> Csatolt vagy beágyazott sablonok esetén a telepítési módot csak [növekményes](deployment-modes.md)értékre állíthatja. A fő sablon azonban teljes módban is üzembe helyezhető. Ha a fősablont a teljes módban telepíti, és a csatolt vagy beágyazott sablon ugyanazt az erőforráscsoportot célozza meg, a csatolt vagy beágyazott sablonban üzembe helyezett erőforrások a teljes telepítési mód kiértékelése részét képezik. A fő sablonban és a csatolt vagy beágyazott sablonokban üzembe helyezett erőforrások összevont gyűjteménye össze van hasonlítva az erőforráscsoport meglévő erőforrásaival. Az egyesített gyűjteményben nem szereplő erőforrások törlődnek.
>
> Ha a csatolt vagy beágyazott sablon más erőforráscsoportot céloz, a központi telepítés növekményes módot használ.
>

## <a name="nested-template"></a>Beágyazott sablon

Sablon beágyazásához vegyen fel egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonba. A **sablon** tulajdonságban határozza meg a sablon szintaxisát.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

A következő példa egy Storage-fiókot helyez üzembe egy beágyazott sablonon keresztül.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Kifejezés kiértékelési hatóköre beágyazott sablonokban

Beágyazott sablon használatakor megadhatja, hogy a sablonkifejezések a szülősablon vagy a beágyazott sablon hatókörében legyenek kiértékelve. A hatókör határozza meg, Hogyan oldhatók meg a paraméterek, a változók és a függvények, például a [resourceGroup](template-functions-resource.md#resourcegroup) és az [előfizetés](template-functions-resource.md#subscription) .

A hatókört a tulajdonságon keresztül állíthatja be `expressionEvaluationOptions` . Alapértelmezés szerint a tulajdonság értékre `expressionEvaluationOptions` van állítva `outer` , ami azt jelenti, hogy a szülő sablon hatókörét használja. Állítsa be úgy a értéket, hogy a `inner` kifejezések kiértékelése a beágyazott sablon hatókörén belül történjen.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

A következő sablon azt mutatja be, Hogyan oldhatók fel a sablon kifejezései a hatókörnek megfelelően. Tartalmaz egy nevű változót, `exampleVar` amely a fölérendelt sablonban és a beágyazott sablonban is definiálva van. A változó értékét adja vissza.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

A (z `exampleVar` ) tulajdonságának értékétől függően változik a változás értéke `scope` `expressionEvaluationOptions` . Az alábbi táblázat mindkét hatókör eredményét tartalmazza.

| `expressionEvaluationOptions` hatókör | Kimenet |
| ----- | ------ |
| belső | beágyazott sablonból |
| külső (vagy alapértelmezett) | fölérendelt sablonból |

Az alábbi példa egy SQL Servert telepít, és lekéri a jelszóhoz használandó Key Vault titkos kulcsot. A hatókör úgy van beállítva, hogy `inner` dinamikusan létrehozza a Key Vault-azonosítót (lásd `adminPassword.reference.keyVault` a külső sablonokban `parameters` ), és paraméterként adja át a beágyazott sablonnak.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> Ha a hatókör értékre van állítva `outer` , nem használhat `reference` beágyazott sablon kimenetek szakaszában lévő függvényt a beágyazott sablonban üzembe helyezett erőforráshoz. Egy beágyazott sablonban lévő üzembe helyezett erőforrás értékeinek visszaküldéséhez használja a `inner` hatókört, vagy alakítsa át a beágyazott sablont egy csatolt sablonra.

## <a name="linked-template"></a>Csatolt sablon

Sablon csatolásához vegyen fel egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonba. A **templateLink** tulajdonságban adja meg a felvenni kívánt sablon URI-ját. Az alábbi példa egy, a Storage-fiókban található sablonra mutató hivatkozásokat tartalmaz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Csatolt sablonra való hivatkozás esetén a `uri` nem lehet helyi fájl vagy olyan fájl, amely csak a helyi hálózaton érhető el. A Azure Resource Managernak képesnek kell lennie a sablon elérésére. Olyan URI-értéket adjon meg, amely **http** -vagy **https**-fájlként letölthető. 

A sablonokat a **http** vagy **HTTPS protokollt** tartalmazó paraméterekkel lehet hivatkozni. Például egy általános minta a `_artifactsLocation` paraméter használata. A csatolt sablon a következőhöz hasonló kifejezéssel állítható be:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Ha a GitHub-sablonhoz kapcsolódik, használja a nyers URL-címet. A hivatkozás formátuma a (z `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` ):. A nyers hivatkozás beszerzéséhez válassza a **RAW** elemet.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Nyers URL-cím kiválasztása":::

### <a name="parameters-for-linked-template"></a>Csatolt sablon paraméterei

A csatolt sablon paramétereit külső fájlban vagy beágyazottan is megadhatja. Külső paraméterérték megadásakor használja a **parametersLink** tulajdonságot:

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
      "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

A paraméterek értékének megadásához használja a **Parameters** tulajdonságot.

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2019-10-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "storageAccountName":{"value": "[parameters('storageAccountName')]"}
    }
   }
  }
]
```

Nem használhat egyszerre beágyazott paramétereket és egy paraméterre mutató hivatkozást. Az üzemelő példány hibát jelez, ha mindkettő `parametersLink` és `parameters` a meg van adva.

## <a name="template-specs"></a>Sablonspecifikációk

A csatolt sablonok egy elérhető végponton való fenntartása helyett létrehozhat egy olyan [sablont](template-specs.md) , amely a fő sablont és a hozzá tartozó sablonokat egyetlen olyan entitásba csomagolja, amelyet üzembe helyezhet. A sablon spec az Azure-előfizetés egyik erőforrása. Megkönnyíti a sablon biztonságos megosztását a szervezetben lévő felhasználókkal. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával hozzáférést biztosíthat a sablon specifikációjának. Ez a funkció jelenleg előzetes verzióban érhető el.

További információ:

- [Oktatóanyag: spec sablon létrehozása csatolt sablonokkal](./template-specs-create-linked.md).
- [Oktatóanyag: a sablon specifikációjának központi telepítése csatolt sablonként](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Függőségek

A többi erőforrástípus hasonlóan a társított sablonok közötti függőségek is megadhatók. Ha az egyik csatolt sablonban lévő erőforrásokat a második csatolt sablon erőforrásai előtt kell telepíteni, állítsa be a második sablont az elsőtől függően.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

Nem kell megadnia a `contentVersion` tulajdonságot a `templateLink` vagy `parametersLink` tulajdonsághoz. Ha nem ad meg a `contentVersion` -t, a rendszer a sablon aktuális verzióját telepíti. Ha értéket ad meg a tartalom verziójához, akkor annak meg kell egyeznie a csatolt sablonban található verzióval. Ellenkező esetben a telepítés hibát jelez.

## <a name="using-variables-to-link-templates"></a>Változók használata a sablonok összekapcsolásához

Az előző példák a sablon hivatkozásainak rögzített URL-értékeit mutatták. Ez a megközelítés egy egyszerű sablon esetében működhet, de a moduláris sablonok nagy készlete esetében nem működik megfelelően. Ehelyett létrehozhat egy statikus változót, amely alapszintű URL-címet tárol a fő sablon számára, majd dinamikusan létrehozza a csatolt sablonok URL-címeit az alap URL-címről. Ennek a megközelítésnek az az előnye, hogy könnyedén áthelyezheti vagy leképezheti a sablont, mert csak a fősablonban lévő statikus változót kell módosítania. A fő sablon a kibontott sablonban a megfelelő URI-ket adja át.

Az alábbi példa bemutatja, hogyan használható egy alap URL-cím két URL-cím létrehozásához csatolt sablonokhoz (**sharedTemplateUrl** és **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Az [üzembe helyezés ()](template-functions-deployment.md#deployment) használatával lekérheti az aktuális sablon alap URL-címét, és ezzel az adott helyen található más sablonok URL-címét is lekérheti. Ez a megközelítés akkor lehet hasznos, ha a sablon helye megváltozik, vagy ha el szeretné kerülni a sablonban rögzített URL-címeket. A templateLink tulajdonságot csak akkor adja vissza a rendszer, ha egy távoli sablonhoz URL-címmel kapcsolódik. Ha helyi sablont használ, akkor ez a tulajdonság nem érhető el.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Végső soron a változót `uri` egy tulajdonság tulajdonságában fogja használni `templateLink` .

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Másolás használata

Ha egy erőforrás több példányát szeretné létrehozni beágyazott sablonnal, adja hozzá a másolás elemet a **Microsoft. Resources/Deployments** erőforrás szintjén. Ha a hatókör belső, a másolást a beágyazott sablonon belül is hozzáadhatja.

Az alábbi példa bemutatja, hogyan használható a másolás beágyazott sablonnal.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="get-values-from-linked-template"></a>Értékek lekérése összekapcsolt sablonból

Egy csatolt sablon kimeneti értékének lekéréséhez a tulajdonság értékét a következő szintaxissal kell lekérni, például: `"[reference('deploymentName').outputs.propertyName.value]"` .

Ha egy kimeneti tulajdonságot egy csatolt sablonból olvas be, a tulajdonság neve nem tartalmazhat kötőjelet.

Az alábbi példák bemutatják, hogyan hivatkozhat egy csatolt sablonra, és hogyan kérhet le egy kimeneti értéket. A csatolt sablon egy egyszerű üzenetet ad vissza.  Először a csatolt sablon:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

A fő sablon telepíti a csatolt sablont, és lekéri a visszaadott értéket. Figyelje meg, hogy név szerint hivatkozik a központi telepítési erőforrásra, és a társított sablon által visszaadott tulajdonság nevét használja.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

A következő példa egy olyan sablont mutat be, amely nyilvános IP-címet telepít, és az adott nyilvános IP-címhez tartozó Azure-erőforrás erőforrás-AZONOSÍTÓját adja vissza:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Ha az előző sablonból a terheléselosztó telepítésekor a nyilvános IP-címet szeretné használni, csatolja a sablont, és állapítsa meg az erőforrás függőségét `Microsoft.Resources/deployments` . A terheléselosztó nyilvános IP-címe a kimeneti értékre van állítva a társított sablonban.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Üzembe helyezési előzmények

A Resource Manager az egyes sablonokat különálló központi telepítésként dolgozza fel az üzembe helyezési előzményekben. Három csatolt vagy beágyazott sablonnal rendelkező fő sablon az üzembe helyezési előzményekben a következőképpen jelenik meg:

![Üzembe helyezési előzmények](./media/linked-templates/deployment-history.png)

Ezeket a különálló bejegyzéseket használhatja az előzményekben a kimeneti értékek lekéréséhez a telepítés után. A következő sablon létrehoz egy nyilvános IP-címet, és kiírja az IP-címet:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Az alábbi sablon az előző sablonra hivatkozik. Három nyilvános IP-címet hoz létre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Az üzembe helyezés után a következő PowerShell-szkripttel kérheti le a kimeneti értékeket:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Vagy Azure CLI-szkript egy bash-rendszerhéjban:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Külső sablon biztonságossá tétele

Bár a csatolt sablonnak külsőleg elérhetőnek kell lennie, nem kell általánosan elérhetőnek lennie a nyilvánosság számára. A sablont egy olyan privát Storage-fiókhoz is hozzáadhatja, amely csak a Storage-fiók tulajdonosa számára érhető el. Ezután létrehoz egy közös hozzáférés-aláírási (SAS-) tokent, amely engedélyezi a hozzáférést az üzembe helyezés során. Ezt az SAS-tokent hozzáadja a társított sablon URI-kódjához. Annak ellenére, hogy a tokent biztonságos karakterláncként adja át, a társított sablon URI-ja, beleértve az SAS-tokent is, a rendszer naplózza a telepítési műveletekben. A kitettség korlátozásához állítsa be a jogkivonat lejáratát.

A paraméter fájlja az SAS-tokenen keresztüli hozzáférésre is korlátozható.

Jelenleg nem lehet olyan sablonhoz csatolni, amely egy [Azure Storage-tűzfal](../../storage/common/storage-network-security.md)mögött található Storage-fiókban van.

> [!IMPORTANT]
> A csatolt sablon SAS-tokenhez való biztonságossá tétele helyett érdemes lehet létrehozni egy [sablon-specifikációt](template-specs.md). A sablon specifikációja biztonságosan tárolja a fő sablont és a hozzá tartozó sablonokat erőforrásként az Azure-előfizetésében. Az Azure RBAC használatával biztosíthatja a hozzáférést azokhoz a felhasználókhoz, akiknek szükségük van a sablon üzembe helyezésére.

Az alábbi példa bemutatja, hogyan lehet átadni egy SAS-tokent egy sablonhoz való csatoláskor:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
      "contentVersion": "1.0.0.0"
    }
    }
  }
  ],
  "outputs": {
  }
}
```

A PowerShellben kap egy jogkivonatot a tárolóhoz, és telepíti a sablonokat az alábbi parancsokkal. Figyelje meg, hogy a **containerSasToken** paraméter a sablonban van definiálva. Nem paraméter a **New-AzResourceGroupDeployment** parancsban.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Az Azure CLI-hez egy bash-rendszerhéjban kap egy tokent a tárolóhoz, és telepíti a sablonokat a következő kóddal:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példák a csatolt sablonok gyakori használatát mutatják be.

|Fő sablon  |Csatolt sablon |Leírás  |
|---------|---------| ---------|
|[„Helló világ!” alkalmazás](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Karakterláncot ad vissza csatolt sablonból. |
|[Load Balancer nyilvános IP-címmel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |A társított sablonból származó nyilvános IP-címet adja vissza, és beállítja a terheléselosztó értékét. |
|[Több IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Több nyilvános IP-címet hoz létre a társított sablonban.  |

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyagot a következő témakörben tekintheti meg [: oktatóanyag: csatolt Azure Resource Manager-sablonok létrehozása](./deployment-tutorial-linked-template.md).
* Az erőforrások telepítési sorrendjének definiálásával kapcsolatos további információkért lásd: [függőségek meghatározása Azure Resource Manager sablonokban](define-resource-dependency.md).
* Ha meg szeretné tudni, hogyan határozhat meg egy erőforrást, de több példányt is létrehozhat, tekintse meg az [erőforrások több példányának létrehozása Azure Resource Manager](copy-resources.md).
* A sablonok Storage-fiókban való beállításával és az SAS-token létrehozásával kapcsolatos lépésekért lásd: [erőforrások üzembe helyezése Resource manager Azure PowerShell-sablonokkal és](deploy-powershell.md) [az erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](deploy-cli.md).
