---
title: Tesztelési esetek a tesztelési eszközkészlethez
description: Az ARM-sablonteszt eszközkészlet által futtatott teszteket ismerteti.
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7805d6dbdb8b93968a2792ed6dfaf2ac8fea9ae5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363393"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Az ARM-sablonteszt eszközkészlet alapértelmezett tesztelési esete

Ez a cikk a sablonteszt eszközkészlettel futtatott alapértelmezett teszteket ismerteti a Azure Resource Manager (ARM-sablonok) számára. [](test-toolkit.md) Olyan példákat tartalmaz, amelyek megesnek vagy sikertelenek a teszten. Ez tartalmazza az egyes tesztek nevét. Egy adott teszt futtatásához lásd: [Tesztparaméterek.](test-toolkit.md#test-parameters)

## <a name="use-correct-schema"></a>A megfelelő séma használata

Teszt neve: **DeploymentTemplate séma helyes**

A sablonban meg kell adnia egy érvényes sémaértéket.

Az alábbi példa megfelel **ennek a** tesztnek.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

A sablon sématulajdonságát a következő sémák egyikéhez kell beállítani:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Paramétereknek léteznie kell

Teszt neve: **A Parameters tulajdonságnak léteznie kell**

A sablonnak paraméterelemmel kell lennie. A paraméterek elengedhetetlenek ahhoz, hogy a sablonok különböző környezetekben is újrahasználhatók. Adjon hozzá paramétereket a sablonhoz a különböző környezetekben való üzembe helyezéskor megváltozó értékekhez.

A következő példa megfelel **ennek a** tesztnek:

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

Teszt neve: **A paraméterekre hivatkozni kell**

A sablonban a félreértések csökkentése érdekében törölje a definiált, de nem használt paramétereket. Ez a teszt megkeresi a sablonban sehol nem használt paramétereket. A nem használt paraméterek kiiktatása megkönnyíti a sablon üzembe helyezését is, mivel nem kell szükségtelen értékeket adnia.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>A biztonságos paraméterek alapértelmezett kódja nem lehet szoftveres

Teszt neve: **A biztonságos sztring paramétereinek nem lehet alapértelmezett beállítása**

Ne adjon meg nem kötelezően kódolt alapértelmezett értéket a sablonban egy biztonságos paraméterhez. Az üres sztring az alapértelmezett értékhez megfelelő.

A **SecureString** vagy **SecureObject** típust bizalmas értékeket, például jelszavakat tartalmazó paramétereken használhatja. Ha egy paraméter biztonságos típust használ, a paraméter értéke nincs naplózva vagy tárolva az üzembe helyezési előzményekben. Ez a művelet megakadályozza, hogy egy rosszindulatú felhasználó felfedezze a bizalmas értéket.

Ha azonban alapértelmezett értéket ad meg, ezt az értéket bárki felderítheti, aki hozzáfér a sablonhoz vagy az üzembe helyezési előzményekhez.

Az alábbi példa nem **felel meg a** tesztnek:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

A következő példa megfelel **ennek a** tesztnek:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>A környezeti URL-címek nem szoftveres kódolva

Teszt neve: **DeploymentTemplate nem tartalmazhat szoftveres URI-t**

Ne kódolja a környezet URL-címeit a sablonban. Ehelyett az environment függvény [használatával](template-functions-deployment.md#environment) dinamikusan lekérte ezeket az URL-címeket az üzembe helyezés során. A letiltott URL-gazdagépek listájáért tekintse meg a [tesztelési esetet.](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)

Az alábbi példa **sikertelen,** mert az URL-cím szoftveres.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

A teszt akkor **is sikertelen** lesz, ha [concat vagy](template-functions-string.md#concat) [URI-val használja.](template-functions-string.md#uri)

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Az alábbi példa megfelel **ennek a** tesztnek.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>A Hely paramétert használ

Teszt neve: **A hely nem lehet szoftveres kód**

A sablonoknak tartalmazni kell egy location nevű paramétert. Ezzel a paraméterrel használhatja az erőforrások helyének beállítását a sablonban. A fő sablonban (a neve _azuredeploy.json_ vagymainTemplate.js _),_ ez a paraméter alapértelmezés szerint az erőforráscsoport helyét használhatja. Csatolt vagy beágyazott sablonokban a location paraméternek nem lehet alapértelmezett helye.

Előfordulhat, hogy a sablon felhasználói számára korlátozott régiók érhetők el. Az erőforrás helyének kódba való bekódlása esetén előfordulhat, hogy a felhasználók nem hoznak létre erőforrást az adott régióban. A felhasználók akkor is letilthatóak, ha az erőforrás helyét a következőre van beállítva: `"[resourceGroup().location]"` . Előfordulhat, hogy az erőforráscsoport olyan régióban lett létrehozva, amelyhez más felhasználók nem férnek hozzá. Ezek a felhasználók nem használják a sablont.

Ha megad egy helyparamétert, amely alapértelmezés szerint az erőforráscsoport helyét adja meg, a felhasználók az alapértelmezett értéket használhatjak, ha kényelmesek, de megadhatnak egy másik helyet is.

A következő példában ez a **teszt** sikertelen, mert az erőforrás helye a következőre van beállítva: `resourceGroup().location` .

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

A következő példa egy  helyparamétert használ, de ez a teszt sikertelen, mert a location paraméter alapértelmezett értéke egy szoftveres hely.

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

Ehelyett hozzon létre egy paramétert, amely alapértelmezés szerint az erőforráscsoport helyét adja meg, de lehetővé teszi, hogy a felhasználók más értéket adjanak meg. Az alábbi példa **akkor ad** át egy tesztet, ha a sablont használja fő sablonként.

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

Ha azonban az előző példát csatolt sablonként használja, a teszt sikertelen **lesz.** Csatolt sablonként használva távolítsa el az alapértelmezett értéket.

## <a name="resources-should-have-location"></a>Az erőforrásoknak helynek kell lennie

Teszt neve: **Az erőforrásoknak helynek kell lennie**

Az erőforrás helyét egy vagy egy [sablonkifejezésre kell](template-expressions.md) `global` beállítani. A sablonkifejezés általában az előző tesztben leírt location paramétert használja.

A következő példa **nem felel** meg a tesztnek, mert a hely nem kifejezés vagy `global` .

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

Az alábbi példa megfelel **ennek a** tesztnek.

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

A következő példa is megfelel **ennek a** tesztnek.

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

Teszt neve: **A virtuális gép méretének paraméternek kell lennie**

Ne kódolvassa a virtuális gép méretét. Adjon meg egy paramétert, hogy a sablon felhasználói módosítsák az üzembe helyezett virtuális gép méretét.

Az alábbi példa nem **felel meg** a tesztnek.

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

## <a name="min-and-max-values-are-numbers"></a>A minimális és maximális értékek számok

Teszt neve: **a minimális és a maximális érték számok**

Ha minimális és maximális értékeket ad meg egy paraméterhez, adja meg őket számokként.

Az alábbi példa nem **felel meg a** tesztnek:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Ehelyett számokként adja meg az értékeket. A következő példa megfelel **ennek a** tesztnek:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Akkor is ez a figyelmeztetés jelenik meg, ha minimális vagy maximális értéket ad meg, de a másikat nem.

## <a name="artifacts-parameter-defined-correctly"></a>Az artifacts paraméter helyesen van definiálva

Teszt neve: **artifacts paraméter**

Ha paramétereket ad meg a és a `_artifactsLocation` `_artifactsLocationSasToken` számára, használja a megfelelő alapértelmezett értékeket és típusokat. A tesztnek a következő feltételeknek kell teljesülnie:

* Ha az egyik paramétert adja meg, meg kell adnia a másikat
* `_artifactsLocation` sztringnek kell **lennie**
* `_artifactsLocation` a fő sablonban egy alapértelmezett értéknek kell lennie
* `_artifactsLocation` beágyazott sablonban nem lehet alapértelmezett érték
* `_artifactsLocation` vagy a `"[deployment().properties.templateLink.uri]"` nyers kódtárak URL-címének kell lennie az alapértelmezett értékhez
* `_artifactsLocationSasToken`**secureStringnek kell lennie**
* `_artifactsLocationSasToken` az alapértelmezett értékhez csak üres sztringet lehet használni
* `_artifactsLocationSasToken` beágyazott sablonban nem lehet alapértelmezett érték

## <a name="declared-variables-must-be-used"></a>Deklarált változókat kell használni

Teszt neve: **Hivatkozni kell a változókra**

A sablon félreértésének csökkentése érdekében törölje a definiált, de nem használt változókat. Ez a teszt megkeresi a sablonban sehol nem használt változókat.

## <a name="dynamic-variable-should-not-use-concat"></a>A dinamikus változók ne használjanak concatot

Teszt neve: **A dinamikus változóhivatkozások nem használhatnak Concatot**

Néha dinamikusan kell változót összeépíteni egy másik változó vagy paraméter értéke alapján. Ne használja a [concat függvényt](template-functions-string.md#concat) az érték beállításakor. Ehelyett használjon olyan objektumot, amely tartalmazza az elérhető beállításokat, és az üzembe helyezés során dinamikusan le kell szereznie az objektum egyik tulajdonságát.

Az alábbi példa megfelel **ennek a** tesztnek. A **currentImage** változó dinamikusan van beállítva az üzembe helyezés során.

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

Teszt neve: **apiVersions Should Be Recent**

Az egyes erőforrások API-verziójának egy friss verziót kell használnia. A teszt kiértékeli a használt verziót az adott erőforrástípushoz elérhető verziók alapján.

## <a name="use-hardcoded-api-version"></a>Szoftveres API-verzió használata

Teszt neve: **Providers apiVersions Is Not Permitted**

Az erőforrástípus API-verziója határozza meg, hogy mely tulajdonságok érhetők el. Adjon meg egy nem szoftveres API-verziót a sablonban. Ne olvassa be az üzembe helyezés során meghatározott API-verziót. Nem fogja tudni, hogy mely tulajdonságok érhetők el.

Az alábbi példa nem **felel meg** a tesztnek.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Az alábbi példa megfelel **ennek a** tesztnek.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>A tulajdonságok nem lehet üresek

Teszt neve: **A sablon nem tartalmazhat üres értékeket**

Ne kódolvassa a tulajdonságokat üres értékre. Az üres értékek közé tartoznak a null és az üres sztringek, objektumok vagy tömbök. Ha egy tulajdonságot üres értékre beállított, távolítsa el a tulajdonságot a sablonból. Az üzembe helyezés során azonban egy tulajdonságot üres értékre is lehet állítani, például egy paraméterrel.

## <a name="use-resource-id-functions"></a>Erőforrás-azonosító függvények használata

Teszt neve: **Az értékeket resourceID-ről kell származtatni**

Erőforrás-azonosító megadásakor használja az egyik erőforrás-azonosító függvényt. Az engedélyezett függvények a következőek:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Ne használja a concat függvényt erőforrás-azonosító létrehozásához. Az alábbi példa nem **felel meg a** tesztnek.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

A következő példa megfelel **ennek a** tesztnek.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>A ResourceId függvény megfelelő paraméterekkel rendelkezik

Teszt neve: **A ResourceId-nek nem szabad tartalmaznia**

Az erőforrás-adatok generálásakor ne használjon felesleges függvényeket választható paraméterekhez. Alapértelmezés szerint a [resourceId](template-functions-resource.md#resourceid) függvény az aktuális előfizetést és erőforráscsoportot használja. Ezeket az értékeket nem kell meg adnia.

Az alábbi példa **sikertelen,** mert nem kell az aktuális előfizetés-azonosítót és erőforráscsoport-nevet meg adnia.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

A következő példa megfelel **ennek a** tesztnek.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Ez a teszt a következőkre vonatkozik:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId (bővítményforrás-azonosító)](template-functions-resource.md#extensionresourceid)
* [Hivatkozás](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

és `reference` `list*` esetén a teszt **meghiúsul,** ha az erőforrás-azonosítót a használatával `concat` hoz létre.

## <a name="dependson-best-practices"></a>dependsOn – ajánlott eljárások

Teszt neve: **DependsOn – ajánlott eljárások**

Az üzembe helyezési függőségek beállításakor ne használja az [if](template-functions-logical.md#if) függvényt egy feltétel teszteléséhez. Ha egy erőforrás egy feltételesen üzembe helyezett erőforrástól [függ,](conditional-resource-deployment.md)állítsa be a függőséget úgy, mint bármely erőforrás esetén. Ha egy feltételes erőforrás nincs telepítve, a Azure Resource Manager automatikusan eltávolítja azt a szükséges függőségekből.

Az alábbi példa nem **felel meg** a tesztnek.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

A következő példa megfelel **ennek a** tesztnek.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>A beágyazott vagy csatolt üzemelő példányok nem használhatnak hibakeresést

Teszt neve: **Az üzembe helyezési erőforrásokat nem lehet hibakereséssel végezni**

Ha beágyazott vagy [csatolt](linked-templates.md) sablont határoz meg a **Microsoft.Resources/deployments** erőforrástípussal, engedélyezheti a hibakeresést az adott sablonhoz. A hibakeresés akkor is megfelelő, ha tesztelni kell a sablont, de akkor érdemes bekapcsolni, amikor készen áll a sablon éles környezetben való használatára.

## <a name="admin-user-names-cant-be-literal-value"></a>A rendszergazdai felhasználónevek nem konstansértékek

Teszt neve: **adminUsername ne legyen konstans**

Rendszergazdai felhasználónév beállításakor ne használjon konstansértéket.

Az alábbi példa nem **felel meg a** tesztnek:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Ehelyett használjon egy paramétert. A következő példa megfelel **ennek a** tesztnek:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>A legújabb virtuálisgép-rendszerkép használata

Teszt neve: **A virtuálisgép-rendszerképeknek a legújabb verziót kell használniuk**

Ha a sablon tartalmaz egy rendszerképet tartalmazó virtuális gépet, győződjön meg arról, hogy a rendszerkép legújabb verzióját használja.

## <a name="use-stable-vm-images"></a>Stabil virtuálisgép-rendszerképek használata

Teszt neve: **Virtual Machines nem lehet előzetes verzió**

A virtuális gépek nem használhatnak előzetes verziójú rendszerképeket.

Az alábbi példa nem **felel meg** a tesztnek.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Az alábbi példa megfelel **ennek a** tesztnek.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Ne használja a ManagedIdentity bővítményt

Teszt neve: **A ManagedIdentityExtension nem használható**

Ne alkalmazza a ManagedIdentity bővítményt egy virtuális gépre. A bővítmény 2019-ben elavult, és már nem használható.

## <a name="outputs-cant-include-secrets"></a>A kimenetek nem tartalmazhatnak titkos okat

Teszt neve: **A kimenetek nem tartalmazhatnak titkos adatokat**

A kimenetek szakaszban ne szerepeljenek olyan értékek, amelyek esetleg titkos okat fednek fel. A sablon kimenete az üzembe helyezési előzményekben van tárolva, így egy rosszindulatú felhasználó megtalálja ezt az információt.

Az alábbi példa **meghiúsul a** teszten, mert egy biztonságos paramétert tartalmaz egy kimeneti értékben.

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

Az alábbi példa **meghiúsul,** mert [list*](template-functions-resource.md#list) függvényt használ a kimenetek között.

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

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>A protectedSettings használata a commandToExecute titkos kulcsokhoz

Teszt neve: **A CommandToExecute parancsnak ProtectedSettings parancsot kell használnia a titkos kulcsokhoz**

Egyéni szkriptbővítményben használja a titkosított tulajdonságot, ha titkos adatokat, például `protectedSettings` `commandToExecute` jelszót tartalmaz. Titkos adattípusok például a `secureString` , `secureObject` a `list()` függvények vagy a szkriptek.

További információ a virtuális gépek egyéni szkriptbővítményeiről: [Windows,](
/azure/virtual-machines/extensions/custom-script-windows) [Linux](/azure/virtual-machines/extensions/custom-script-linux), és [a Microsoft.Compute virtualMachines/extensions séma.](/azure/templates/microsoft.compute/virtualmachines/extensions)

Ebben a példában egy nevű és típusú paraméterrel megadott sablon megfelel a teszten, mert a titkosított tulajdonság tartalmazza `adminPassword` `secureString`  `protectedSettings` a `commandToExecute` et.

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

A teszt **meghiúsul,** ha a titkosítatlan tulajdonság tartalmazza `settings` a `commandToExecute` következőt: .

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>Következő lépések

* A tesztelési eszközkészlet futtatásával kapcsolatos további információkért lásd: [ARM-sablonteszt eszközkészlet használata.](test-toolkit.md)
* A teszteszközkészlet használatát bemutató Microsoft Learn lásd: Preview changes and validate Azure resources by using what-if and the ARM template test toolkit (Az Azure-erőforrások előzetes verziója a what-if és az [ARM-sablontesztelőeszközkészlet használatával).](/learn/modules/arm-template-test/)
