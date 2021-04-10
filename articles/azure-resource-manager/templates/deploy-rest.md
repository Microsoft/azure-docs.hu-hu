---
title: Erőforrások üzembe helyezése REST API és sablonnal
description: Erőforrások üzembe helyezése az Azure-ban Azure Resource Manager és Resource Manager REST API használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 90e50598176ddc0327a81df105740f58afd930bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732567"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API

Ez a cikk azt ismerteti, hogyan használhatók az erőforrások az Azure-ban való üzembe helyezéséhez Azure Resource Manager sablonokkal (ARM-sablonokkal) a Azure Resource Manager REST API.

Megadhatja a sablont a kérelem törzsében, vagy csatolhat egy fájlhoz. Fájl használata esetén helyi fájl vagy egy URI-n keresztül elérhető külső fájl lehet. Ha a sablon egy Storage-fiókban található, korlátozhatja a hozzáférést a sablonhoz, és megadhat egy közös hozzáférési aláírási (SAS-) tokent az üzembe helyezés során.

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy erőforráscsoport, egy Azure-előfizetés, egy felügyeleti csoport vagy egy bérlő számára is megcélozhatja. A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

- Egy **erőforráscsoporthoz** való üzembe helyezéshez használja a [központi telepítések – létrehozás](/rest/api/resources/resources/deployments/createorupdate)lehetőséget. A kérelmet a következő címre küldi a rendszer:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

- Az **előfizetéshez** való üzembe helyezéshez használja a [központi telepítések – létrehozás az előfizetések hatókörében](/rest/api/resources/resources/deployments/createorupdateatsubscriptionscope)lehetőséget. A kérelmet a következő címre küldi a rendszer:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

- Egy **felügyeleti csoportba** történő központi telepítéshez használja a [központi telepítések – létrehozás felügyeleti csoport hatókörét](/rest/api/resources/resources/deployments/createorupdateatmanagementgroupscope). A kérelmet a következő címre küldi a rendszer:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

- Az üzembe helyezéshez **a** bérlői [hatókörben használjon központi telepítéseket – létrehozás vagy frissítés](/rest/api/resources/resources/deployments/createorupdateattenantscope). A kérelmet a következő címre küldi a rendszer:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  A bérlői szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforrások létrehozása a bérlő szintjén](deploy-to-tenant.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják.

## <a name="deploy-with-the-rest-api"></a>Üzembe helyezés a REST API-val

1. [Közös paraméterek és fejlécek](/rest/api/azure/)beállítása, beleértve a hitelesítési jogkivonatokat.

1. Ha nem létező erőforráscsoporthoz végez üzembe helyezést, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés-AZONOSÍTÓját, az új erőforráscsoport nevét és a megoldáshoz szükséges helyet. További információ: Create an [Resource Group (erőforráscsoport létrehozása](/rest/api/resources/resources/resourcegroups/createorupdate)).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
   ```

   A kérelem törzse például:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. A sablon üzembe helyezése előtt megtekintheti, hogy a sablon milyen módosításokat hajt végre a környezetben. A [mi-if művelettel](template-deploy-what-if.md) ellenőrizheti, hogy a sablon elvégzi-e a várt módosításokat. Mi a teendő, ha a hibát is ellenőrzi a sablonban.

1. Sablon üzembe helyezéséhez adja meg az előfizetés AZONOSÍTÓját, az erőforráscsoport nevét, a központi telepítés nevét a kérelem URI-ja alapján.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2020-10-01
   ```

   A kérelem törzsében adjon meg egy hivatkozást a sablonra és a paraméter fájlra. A paraméterfájlról a [Resource Manager-paraméterfájl létrehozása](parameter-files.md) című cikk nyújt további információkat.

   Figyelje `mode` meg, hogy a értéke **növekményes**. Teljes telepítés futtatásához állítsa be a következőt: `mode` **Befejezés**. Ügyeljen arra, hogy a teljes módot használja, mert a sablonban nem szereplő erőforrásokat véletlenül nem lehet törölni.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Ha a válasz tartalmát, a kérés tartalmát vagy mindkettőt szeretné naplózni, vegye `debugSetting` fel a kérelmet a kérelembe.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Beállíthatja, hogy a Storage-fiók közös hozzáférésű aláírás (SAS) jogkivonatot használjon. További információ: [hozzáférés delegálása közös hozzáférési aláírással](/rest/api/storageservices/delegate-access-with-shared-access-signature).

    Ha egy paraméterhez (például jelszóhoz) bizalmas értéket kell megadnia, adja hozzá ezt az értéket egy kulcstartóhoz. Kérje le a Key vaultot az üzembe helyezés során, ahogy az az előző példában is látható. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](key-vault-parameter.md)átadására az üzembe helyezés során.

1. A sablonhoz és paraméterekhez kapcsolódó fájlokhoz való csatolás helyett a kérés törzsében is felveheti őket. A következő példa a kérelem törzsét mutatja be a sablonnal és paraméterrel:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. A sablon üzembe helyezési állapotának lekéréséhez használja a [Get](/rest/api/resources/resources/deployments/get)lehetőséget.

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
   ```

## <a name="deployment-name"></a>Központi telepítés neve

Megadhatja az üzemelő példány nevét, például: `ExampleDeployment` .

Minden alkalommal, amikor futtat egy központi telepítést, a rendszer egy bejegyzést ad hozzá az erőforráscsoport telepítési előzményeihez a központi telepítési névvel. Ha egy másik központi telepítést futtat, és ugyanazt a nevet adja, a korábbi bejegyzést a rendszer a jelenlegi telepítéssel helyettesíti. Ha az üzembe helyezési előzményekben egyedi bejegyzéseket kíván fenntartani, adjon meg minden egyes központi telepítést egyedi nevet.

Egyedi név létrehozásához véletlenszerű számot rendelhet hozzá. Vagy adjon hozzá egy dátumérték értéket.

Ha egyazon erőforráscsoporthoz futtat egyidejű központi telepítéseket ugyanazzal a központi telepítési névvel, akkor a rendszer csak az utolsó telepítést hajtja végre. A rendszer a legutóbbi telepítés helyett minden olyan központi telepítést lecserél, amelynek a neve nem fejeződött be. Ha például egy nevű központi telepítést futtat, amely egy nevű `newStorage` Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage2` , csak egy Storage-fiókot telepít. Az eredményül kapott Storage-fiók neve `storage2` .

Ha azonban egy nevű központi telepítést futtat `newStorage` , amely egy nevű Storage-fiókot helyez üzembe `storage1` , és közvetlenül a befejezése után futtat egy másik nevű központi telepítést `newStorage` , amely telepíti a nevű Storage `storage2` -fiókot, akkor két Storage-fiókkal rendelkezik. Az egyik neve `storage1` , a másik pedig a neve `storage2` . Azonban csak egy bejegyzés szerepel az üzembe helyezési előzményekben.

Amikor egyedi nevet ad meg az egyes központi telepítésekhez, ütközés nélkül is futtathatja őket. Ha olyan nevű központi telepítést futtat `newStorage1` , amely egy nevű Storage-fiókot telepít `storage1` , és egy másik nevű központi telepítési példányt futtat `newStorage2` , amely egy nevű Storage-fiókot telepít `storage2` , akkor két Storage-fiókkal és két bejegyzéssel rendelkezik az üzembe helyezési előzményekben.

Az egyidejű központi telepítésekkel való ütközések elkerülése érdekében, valamint a telepítési előzmények egyedi bejegyzéseinek biztosításához adjon egyedi nevet a központi telepítésnek.

## <a name="next-steps"></a>Következő lépések

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- További információ az aszinkron REST-műveletek kezeléséről: az [aszinkron Azure-műveletek nyomon követése](../management/async-operations.md).
- A sablonokkal kapcsolatos további tudnivalókért tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
