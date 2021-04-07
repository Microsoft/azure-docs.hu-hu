---
title: Erőforrások telepítési sorrendjének beállítása
description: Ismerteti, hogyan lehet egy Azure-erőforrást egy másik erőforrástól függőként beállítani az üzembe helyezés során. A függőségek biztosítják, hogy az erőforrások a megfelelő sorrendben legyenek telepítve.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934747"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Erőforrások üzembe helyezési sorrendjének definiálása ARM-sablonokban

Erőforrások üzembe helyezése esetén előfordulhat, hogy a többi erőforrás előtt meg kell győződnie arról, hogy bizonyos erőforrások léteznek. Egy adatbázis üzembe helyezése előtt például logikai SQL Serverre van szükség. Ezt a kapcsolatot úgy hozza létre, hogy egy erőforrást a másik erőforrástól függőként jelöl meg. `dependsOn`Explicit függőség definiálásához használja az elemet. Egy implicit függőség meghatározásához használja a **hivatkozás** vagy a **lista** függvényt.

Azure Resource Manager kiértékeli az erőforrások közötti függőségeket, és a függő sorrendbe helyezi őket. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. Csak az ugyanabban a sablonban üzembe helyezett erőforrások függőségeit kell meghatároznia.

## <a name="dependson"></a>dependsOn

A Azure Resource Manager sablonjában (ARM-sablon) belül az `dependsOn` elem lehetővé teszi, hogy egy erőforrást egy vagy több erőforrástól függőként definiáljon. Az érték a karakterláncok JavaScript Object Notation (JSON) tömbje, amelyek mindegyike egy erőforrás neve vagy azonosítója. A tömb tartalmazhat [feltételesen üzembe helyezett](conditional-resource-deployment.md)erőforrásokat is. Ha egy feltételes erőforrás nincs telepítve, Azure Resource Manager automatikusan eltávolítja a szükséges függőségekről.

Az alábbi példa egy olyan hálózati adaptert mutat be, amely a virtuális hálózattól, a hálózati biztonsági csoporttól és a nyilvános IP-címről függ. A teljes sablonhoz tekintse meg [a Linux rendszerű virtuális gép](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json)gyors üzembe helyezési sablonját.

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Előfordulhat, hogy az `dependsOn` erőforrások közötti kapcsolatok leképezésére is hajlamos lehet, ezért fontos megérteni, hogy miért csinálja. Ha például dokumentálni szeretné, hogy az erőforrások hogyan kapcsolódnak egymáshoz, `dependsOn` nem a megfelelő megközelítés. A telepítés után nem lehet lekérdezni, hogy mely erőforrások lettek meghatározva az `dependsOn` elemben. A szükségtelen függőségek beállítása lelassítja a telepítési időt, mivel a Resource Manager nem tudja párhuzamosan telepíteni ezeket az erőforrásokat.

## <a name="child-resources"></a>Gyermek erőforrások

Egy implicit központi telepítési függőség nem jön létre automatikusan egy [alárendelt erőforrás](child-resource-name-type.md) és a fölérendelt erőforrás között. Ha a alárendelt erőforrást a szülő erőforrás után kell központilag telepítenie, állítsa be a `dependsOn` tulajdonságot.

Az alábbi példa egy logikai SQL Servert és adatbázist mutat be. Figyelje meg, hogy az adatbázis és a kiszolgáló között explicit függőség van meghatározva annak ellenére, hogy az adatbázis a kiszolgáló gyermeke.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

A teljes sablonhoz tekintse meg a Azure SQL Database gyors üzembe helyezési [sablonját](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>hivatkozás és lista függvények

A [Reference függvény](template-functions-resource.md#reference) lehetővé teszi, hogy egy kifejezés más JSON-nevekből és-érték párokból vagy futásidejű erőforrásokból származtatsa az értékét. A [lista * függvények](template-functions-resource.md#list) egy adott erőforráshoz adnak vissza értékeket egy list műveletből.

A hivatkozás és a lista kifejezése implicit módon deklarálja, hogy egy erőforrás egy másiktól függ. Ha lehetséges, használjon egy implicit hivatkozást, hogy elkerülje a szükségtelen függőségek hozzáadását.

Az implicit függőségek érvényesítéséhez tekintse meg az erőforrás név szerint, nem pedig az erőforrás-azonosító nevet. Ha az erőforrás-azonosítót a hivatkozás vagy a lista függvénybe adja át, az implicit hivatkozás nem jön létre.

A függvény általános formátuma `reference` :

```json
reference('resourceName').propertyPath
```

A függvény általános formátuma `listKeys` :

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

A következő példában egy CDN-végpont kifejezetten a CDN-profiltól függ, és implicit módon egy webalkalmazástól függ.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

További információért lásd a [Reference függvényt](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Egy hurok erőforrásaitól függ

A [másolási hurok](copy-resources.md)erőforrásaitól függő erőforrások üzembe helyezéséhez két lehetőség közül választhat. A hurokban vagy a teljes hurokban megadhat egy függőséget az egyes erőforrásokhoz.

> [!NOTE]
> A legtöbb esetben a másolási hurokban lévő egyes erőforrások függőségét kell beállítania. A következő erőforrás létrehozása előtt csak a teljes huroktól függ, ha a hurokban lévő összes erőforrásra szüksége van. A teljes hurok függőségének beállítása a függőségek gráf jelentős kibővítését eredményezi, különösen akkor, ha a hurkolt erőforrások más erőforrástól függenek. A kibővített függőségek megnehezítik a központi telepítés hatékonyságát.

Az alábbi példa bemutatja, hogyan helyezhet üzembe több virtuális gépet. A sablon ugyanazt a számú hálózati adaptert hozza létre. Az egyes virtuális gépek a teljes hurok helyett egy hálózati adaptertől függenek.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

Az alábbi példa bemutatja, hogyan helyezhet üzembe három Storage-fiókot a virtuális gép telepítése előtt. Figyelje meg, hogy az `copy` elem `name` értéke `storagecopy` és a `dependsOn` virtuális gép eleme is be van állítva `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Körkörös függőségek

A Resource Manager körkörös függőségeket azonosít a sablon érvényesítése során. Ha körkörös függőségi hibát kap, értékelje ki a sablont, és ellenőrizze, hogy el lehet-e távolítani a függőségeket. Ha a függőségek eltávolítása nem működik, elkerülheti a körkörös függőségeket úgy, hogy egyes telepítési műveleteket alárendelt erőforrásokra helyez el. A gyermek erőforrásokat a körkörös függőséggel rendelkező erőforrások után telepítse. Tegyük fel például, hogy két virtuális gépet telepít, de tulajdonságokat kell megadnia, amelyek a másikra hivatkoznak. Ezeket a következő sorrendben helyezheti üzembe:

1. vm1
2. VM2
3. A VM1 bővítmény a VM1 és a VM2 függvénytől függ. A bővítmény beállítja azokat az értékeket a VM1, amelyeket a VM2-ből kap.
4. A VM2 bővítmény a VM1 és a VM2 függvénytől függ. A bővítmény beállítja azokat az értékeket a VM2, amelyeket a VM1-ből kap.

További információ a telepítési sorrend kiértékeléséről és a függőségi hibák megoldásáról: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez lásd [: oktatóanyag: ARM-sablonok létrehozása függő erőforrásokkal](template-tutorial-create-templates-with-dependent-resources.md).
* Az erőforrás-függőségeket lefedi Microsoft Learn modul esetében lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablonok használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).
* A függőségek beállításával kapcsolatos javaslatokért lásd: [ARM-sablon – ajánlott eljárások](template-best-practices.md).
* Az üzembe helyezés során felmerülő hibák elhárításáról további információt [Az Azure telepítési hibáinak elhárítása a Azure Resource Manager](common-deployment-errors.md)használatával című témakörben talál.
* Azure Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A sablonban elérhető függvények listáját itt tekintheti meg: [ARM template functions](template-functions.md).
