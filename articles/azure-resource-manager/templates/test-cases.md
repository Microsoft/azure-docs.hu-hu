---
title: Tesztelési eszközkészlet tesztelése
description: Az ARM template test Toolkit által futtatott tesztek leírása.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 451323058ad743d6e26fc8bcea27d1b44c76f543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674042"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Az ARM-sablon tesztelési eszközkészletének alapértelmezett tesztelési esetei

Ez a cikk a [template test Toolkit használatával](test-toolkit.md)futtatott alapértelmezett teszteket ismerteti. Olyan példákat tartalmaz, amelyek átadják vagy elmulasztják a tesztet. Tartalmazza az egyes tesztek nevét.

## <a name="use-correct-schema"></a>Helyes séma használata

Teszt neve: **a DeploymentTemplate séma helyes**

A sablonban érvényes séma-értéket kell megadnia.

A következő példa **átadja** ezt a tesztet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

A sablon Schema tulajdonságát az alábbi sémák egyikére kell beállítani:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>A paramétereknek léteznie kell

Teszt neve: a **Parameters tulajdonságnak léteznie kell**

A sablonnak tartalmaznia kell egy Parameters elemet. A paraméterek elengedhetetlenek ahhoz, hogy a sablonok újrafelhasználhatók legyenek a különböző környezetekben. Adjon hozzá paramétereket a sablonhoz olyan értékekhez, amelyek a különböző környezetekben való üzembe helyezéskor változnak.

A következő példa **átadja** ezt a tesztet:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Deklarált paramétereket kell használni

Teszt neve: **a paramétereket hivatkozni** kell

A sablonban felmerülő félreértések csökkentése érdekében törölje a definiált, de nem használt paramétereket. Ez a teszt megkeresi azokat a paramétereket, amelyeket a sablon sehol nem használ. A használaton kívüli paraméterek kiiktatásával megkönnyíti a sablon üzembe helyezését, mert nem kell szükségtelen értékeket megadnia.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>A biztonságos paraméterek nem rendelkezhetnek hardcoded alapértelmezett értékkel

Teszt neve: **a Secure string paraméterek nem rendelkezhetnek alapértelmezett értékkel**

A sablonban ne adjon meg rögzített alapértelmezett értéket a biztonságos paraméterek számára. Az alapértelmezett értékhez egy üres karakterláncot kell megadni.

A **SecureString** vagy a **SecureObject** típust kell használnia a bizalmas értékeket, például jelszavakat tartalmazó paramétereknél. Ha egy paraméter biztonságos típust használ, a paraméter értéke nem kerül naplózásra, illetve nem tárolódik az üzembe helyezési előzmények között. Ez a művelet megakadályozza, hogy a rosszindulatú felhasználók felfedezzék a bizalmas értéket.

Ha azonban alapértelmezett értéket ad meg, ez az érték a sablonhoz vagy az üzembe helyezési előzményekhez hozzáférő bárki számára is felderíthető.

A következő példa **nem tudja végrehajtani** ezt a tesztet:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

A következő példa **átadja** ezt a tesztet:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>A környezeti URL-címek nem lehetnek hardcoded

Teszt neve: a **DeploymentTemplate nem tartalmazhat hardcoded URI** -t

Ne parancsmagba a környezet URL-címeit a sablonban. Ehelyett használja a [környezeti funkciót](template-functions-deployment.md#environment) , hogy dinamikusan beolvassa ezeket az URL-címeket az üzembe helyezés során. A blokkolt URL-gazdagépek listáját a [tesztelési eset](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)című részben tekintheti meg.

Az alábbi példa ezt a tesztet **nem tudja végrehajtani** , mert az URL-cím hardcoded.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

A teszt a [Összefűzés](template-functions-string.md#concat) vagy az [URI](template-functions-string.md#uri)használata esetén is **meghiúsul** .

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

A következő példa **átadja** ezt a tesztet.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>A hely paramétert használ

Teszt neve: **a hely nem lehet hardcoded**

A sablonoknak egy Location nevű paraméterrel kell rendelkezniük. Ezzel a paraméterrel állíthatja be az erőforrások helyét a sablonban. A fősablonban (azuredeploy.json vagy mainTemplate.js) Ez a paraméter alapértelmezés szerint az erőforráscsoport helyére kerül. A csatolt vagy beágyazott sablonokban a Location paraméternek nem lehet alapértelmezett helye.

Előfordulhat, hogy a sablon felhasználói korlátozott számú régióval rendelkeznek. Az erőforrás helyének megadását követően előfordulhat, hogy a felhasználók nem tudnak erőforrásokat létrehozni az adott régióban. A felhasználók akkor is blokkolva lehetnek, ha az erőforrás helyét az értékre állítja `"[resourceGroup().location]"` . Előfordulhat, hogy az erőforráscsoport olyan régióban lett létrehozva, amelyhez más felhasználók nem férhetnek hozzá. Ezek a felhasználók le vannak tiltva a sablon használatával.

Egy olyan Location paraméter megadásával, amely alapértelmezés szerint az erőforráscsoport helyét használja, a felhasználók az alapértelmezett értéket használhatják, ha kényelmesek, de más helyet is megadhatnak.

A következő példa **nem tudja végrehajtani** ezt a tesztet, mert az erőforráshoz tartozó hely a értékre van állítva `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

A következő példa egy Location paramétert használ, de ez a teszt **sikertelen** , mert a Location paraméter alapértelmezett értéke egy hardcoded-hely.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Ehelyett hozzon létre egy olyan paramétert, amely Alapértelmezésben az erőforráscsoport helyére kerül, de lehetővé teszi a felhasználók számára, hogy más értéket adjanak meg. A következő példa **átadja** ezt a tesztet, ha a sablon a fő sablonként van használatban.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Ha azonban az előző példában csatolt sablonként van használatban, a teszt **sikertelen lesz**. Csatolt sablonként való használata esetén távolítsa el az alapértelmezett értéket.

## <a name="resources-should-have-location"></a>Az erőforrásoknak helyet kell tartalmaznia

Teszt neve: **az erőforrásoknak helyet kell tartalmaznia**

Az erőforrás helyét a [sablon kifejezésére](template-expressions.md) vagy a (z) értékre kell beállítani `global` . A sablon kifejezés általában az előző tesztben leírt Location paramétert fogja használni.

A következő példa **nem teljesíti** ezt a tesztet, mert a hely nem kifejezés vagy `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

A következő példa **átadja** ezt a tesztet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

A következő példa is **átadja** ezt a tesztet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>A virtuális gép mérete paramétert használ

Teszt neve: **a virtuális gép méretének paraméternek kell lennie**

Ne parancsmagba a virtuális gép méretét. Adjon meg egy paramétert, hogy a sablon felhasználói módosíthassák a telepített virtuális gép méretét.

A következő példa **nem tudja végrehajtani** ezt a tesztet.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Ehelyett adjon meg egy paramétert.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Ezután állítsa a virtuális gép méretét erre a paraméterre.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>A minimális és a maximális érték számok

Teszt neve: **a minimális és a maximális érték számok**

Ha a paraméterhez a min és a Max értéket adja meg, akkor adja meg őket számként.

A következő példa **nem tudja végrehajtani** ezt a tesztet:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Ehelyett számokként adja meg az értékeket. A következő példa **átadja** ezt a tesztet:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Akkor is ezt a figyelmeztetést kapja, ha a min vagy a maximális értéket adja meg, a másikat azonban nem.

## <a name="artifacts-parameter-defined-correctly"></a>Az összetevők paramétere helyesen van megadva

Teszt neve: összetevők **paraméter**

Ha `_artifactsLocation` a és a paramétereket is tartalmazza `_artifactsLocationSasToken` , használja a megfelelő alapértelmezett értékeket és típusokat. A teszt továbbításához a következő feltételeknek kell teljesülniük:

* Ha egy paramétert ad meg, meg kell adnia a másikat.
* `_artifactsLocation`**karakterláncnak** kell lennie
* `_artifactsLocation` a fő sablonban alapértelmezett értékkel kell rendelkeznie
* `_artifactsLocation` nem lehet alapértelmezett érték egy beágyazott sablonban. 
* `_artifactsLocation` az `"[deployment().properties.templateLink.uri]"` alapértelmezett értékének vagy a nyers tárház URL-címének kell lennie
* `_artifactsLocationSasToken`**secureString** kell lennie
* `_artifactsLocationSasToken` az alapértelmezett értékének csak üres karakterlánca lehet
* `_artifactsLocationSasToken` nem lehet alapértelmezett érték egy beágyazott sablonban. 

## <a name="declared-variables-must-be-used"></a>Deklarált változókat kell használni

Teszt neve: **a változókat hivatkozni** kell

A sablonban felmerülő félreértések csökkentése érdekében törölje a definiált, de nem használt változókat. Ez a teszt megkeresi a sablonban nem használt változókat.

## <a name="dynamic-variable-should-not-use-concat"></a>A dinamikus változó nem használhatja a concat

Teszt neve: a **dinamikus változók hivatkozásai nem használhatják a concat**

Esetenként dinamikusan kell létrehoznia egy változót egy másik változó vagy paraméter értéke alapján. Az érték beállításakor ne használja a [concat](template-functions-string.md#concat) függvényt. Ehelyett használjon olyan objektumot, amely tartalmazza az elérhető beállításokat, és dinamikusan beolvassa az objektum egyik tulajdonságát az üzembe helyezés során.

A következő példa **átadja** ezt a tesztet. Az üzembe helyezés során a rendszer dinamikusan beállítja a **currentImage** változót.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>A legutóbbi API-verzió használata

Teszt neve: **a apiVersions legyen közelmúltbeli**

Az egyes erőforrásokhoz tartozó API-verziónak egy közelmúltbeli verziót kell használnia. A teszt kiértékeli az adott erőforrás-típushoz elérhető verziókon használt verziót.

## <a name="use-hardcoded-api-version"></a>Hardcoded API-verzió használata

Teszt neve: **a szolgáltatók ApiVersions nem engedélyezett**

Az erőforrástípus API-verziója határozza meg, hogy mely tulajdonságok érhetők el. A sablonban adjon meg egy nehezen kódolt API-verziót. Ne kérjen le egy, az üzembe helyezés során meghatározott API-verziót. Nem fogja tudni, hogy mely tulajdonságok érhetők el.

A következő példa **nem tudja végrehajtani** ezt a tesztet.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

A következő példa **átadja** ezt a tesztet.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>A tulajdonságok nem lehetnek üresek

Teszt neve: a **sablon nem tartalmazhat üres értékeket**

Ne parancsmagba a tulajdonságokat üres értékre. Az üres értékek null és üres karakterláncokat, objektumokat vagy tömböket tartalmaznak. Ha egy tulajdonságot üres értékre állított be, távolítsa el a tulajdonságot a sablonból. Az üzembe helyezés során azonban rendben van egy tulajdonság beállítása üres értékre, például egy paraméteren keresztül.

## <a name="use-resource-id-functions"></a>Erőforrás-azonosító függvények használata

Teszt neve: **az azonosítókat a ResourceId:-ből kell származtatni** .

Erőforrás-azonosító megadásakor használja az erőforrás-azonosító függvények egyikét. Az engedélyezett függvények a következők:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Ne használja a concat függvényt erőforrás-azonosító létrehozásához. A következő példa **nem tudja végrehajtani** ezt a tesztet.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

A következő példa **átadja** ezt a tesztet.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>A ResourceId függvény megfelelő paramétereket tartalmaz

Teszt neve: a **ResourceId: nem tartalmazhatnak**

Erőforrás-azonosítók létrehozásakor ne használjon felesleges függvényeket a választható paraméterekhez. Alapértelmezés szerint a [resourceId](template-functions-resource.md#resourceid) függvény az aktuális előfizetést és erőforráscsoportot használja. Ezeket az értékeket nem kell megadnia.  

A következő példa **nem** teszi lehetővé ezt a tesztet, mert nem kell megadnia az aktuális előfizetés-azonosítót és az erőforráscsoport nevét.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

A következő példa **átadja** ezt a tesztet.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Ez a teszt a következőkre vonatkozik:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [referencia](template-functions-resource.md#reference)
* [listáját](template-functions-resource.md#list)

A `reference` és a esetében `list*` a teszt **sikertelen lesz** , ha `concat` az erőforrás-azonosító létrehozásához használja.

## <a name="dependson-best-practices"></a>dependsOn ajánlott eljárások

Teszt neve: **DependsOn ajánlott eljárások**

A központi telepítési függőségek beállításakor ne használja az [IF](template-functions-logical.md#if) függvényt a feltétel teszteléséhez. Ha egy erőforrás a [feltételesen üzembe helyezett](conditional-resource-deployment.md)erőforrástól függ, állítsa be a függőséget úgy, hogy bármilyen erőforrással lenne. Ha egy feltételes erőforrás nincs telepítve, Azure Resource Manager automatikusan eltávolítja a szükséges függőségekről.

A következő példa **nem tudja végrehajtani** ezt a tesztet.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

A következő példa **átadja** ezt a tesztet.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>A beágyazott vagy csatolt központi telepítések nem használhatják a hibakeresést

Teszt neve: **a telepítési erőforrások nem lehetnek hibakeresési** műveletek

Ha egy [beágyazott vagy csatolt sablont](linked-templates.md) definiál a **Microsoft. Resources/Deployments** erőforrástípus használatával, engedélyezheti a hibakeresést az adott sablonhoz. A hibakeresés rendben van, ha meg kell vizsgálnia a sablont, de a sablon éles környezetben való használatra kész állapotban kell lennie.

## <a name="admin-user-names-cant-be-literal-value"></a>A rendszergazdai felhasználónevek nem lehetnek literális értékek

Teszt neve: **a AdminUsername nem lehet literál**

Rendszergazdai Felhasználónév beállításakor ne használjon literál értéket.

A következő példa **nem tudja végrehajtani** ezt a tesztet:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Ehelyett használjon paramétert. A következő példa **átadja** ezt a tesztet:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>A legújabb virtuálisgép-lemezkép használata

Teszt neve: a virtuálisgép- **lemezképeknek a legújabb verziót kell használniuk**

Ha a sablon tartalmaz egy rendszerképpel rendelkező virtuális gépet, győződjön meg róla, hogy a lemezkép legújabb verzióját használja.

## <a name="use-stable-vm-images"></a>Stabil virtuálisgép-rendszerképek használata

Teszt neve: **Virtual Machines nem lehet előzetes** verzió

A virtuális gépek nem használhatják az előnézeti lemezképeket.

A következő példa **nem tudja végrehajtani** ezt a tesztet.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

A következő példa **átadja** ezt a tesztet.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Ne használja a ManagedIdentity-bővítményt

Teszt neve: **a ManagedIdentityExtension nem használható**

Ne alkalmazza a ManagedIdentity-bővítményt egy virtuális gépre. További információ: [a virtuális gépek felügyelt identitások bővítmény használatának leállítása és az Azure instance metadata Service használatának megkezdése](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>A kimenetek nem tartalmazhatnak titkos kódokat

Teszt neve: a **kimenetek nem tartalmazhatnak titkos** kódokat

Ne adjon meg olyan értékeket a kimenetek szakaszban, amelyek esetleg titkos titkokat tesznek elérhetővé. A sablon kimenetét a rendszer az üzembe helyezési előzményekben tárolja, így a rosszindulatú felhasználók megtalálják ezt az információt.

**A következő példa** leállítja a tesztet, mert egy biztonságos paramétert tartalmaz egy kimeneti értékben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

A következő példa **meghiúsul** , mert a kimenetben egy [List *](template-functions-resource.md#list) függvényt használ.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A tesztelési eszközkészlet futtatásáról további információt a [ARM-sablon tesztelési eszközkészletének használata](test-toolkit.md)című témakörben talál.
- A tesztelési eszközkészlettel foglalkozó Microsoft Learn modul esetében tekintse meg az [előzetes verzió módosításait és az Azure-erőforrások érvényesítését a mi és az ARM template test Toolkit használatával](/learn/modules/arm-template-test/).