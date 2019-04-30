---
title: Központi telepítési sorrendet, az Azure-erőforrások |} A Microsoft Docs
description: Ismerteti, hogyan állíthatja be egy erőforrást egy másik erőforrástól függ-e, győződjön meg arról, hogy üzembe helyezett erőforrások a megfelelő sorrendben történő üzembe helyezés során.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 91325b7884eae4c6f4c85c142b1e81cf2121c039
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103798"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>A rendelés üzembe erőforrásokat az Azure Resource Manager-sablonok meghatározása
Egy adott erőforráshoz lehet más erőforrások, amelyek már léteznie kell az erőforrás üzembe van helyezve. Például egy SQL server léteznie kell az SQL-adatbázis üzembe helyezése előtt. Ezt a kapcsolatot definiálhat egy erőforrást, mint a többi erőforrástól függ-e megjelölésével. A függőséget határoz meg a **dependsOn** elem, vagy a **referencia** függvény. 

A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. Csak kell határozhat meg telepített erőforrások függőségeinek ugyanazt a sablont. 

Foglalkozó oktatóanyagért lásd: [oktatóanyag: Azure Resource Manager-sablonok létrehozása a tőle függő erőforrások](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
A sablonon belül a dependsOn elem lehetővé teszi egy erőforrás egy függő egy vagy több erőforrást kell definiálni. Az érték lehet egy kívánt erőforrás nevét tartalmazó vesszővel tagolt lista. 

Az alábbi példa bemutatja egy virtuális gép méretezési csoportot, amely egy terheléselosztót, virtuális hálózat és több tárfiókot hoz létre, hurkot függ. A többi erőforrás nem jelennek meg az alábbi példában, de lenne szükségük a sablon megtalálható.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

Az előző példában egy függőségi szerepel-e az erőforrásokat, a másolási ciklust nevű keresztül létrehozott **storageLoop**. Egy vonatkozó példáért lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).

Függőségek definiálása, amikor az erőforrás-szolgáltató névtere és a félreérthetőség elkerülése érdekében erőforrástípus is megadhat. Például azzal a pontosítással, egy terheléselosztó és a virtuális hálózatot, amely előfordulhat, hogy ugyanazokat a neveket, mint más erőforrások, használja a következő formátumot:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Bár előfordulhat, hogy kódolni, dependsOn segítségével az erőforrások közötti kapcsolatok leképezése, fontos tudni, miért végez. Dokumentálása, hogyan vannak összekapcsolva erőforrások, például a dependsOn nem a megfelelő módszer. Nem lehet lekérdezni, mely erőforrásokat üzembe helyezés után a dependsOn elemben meghatározott. DependsOn használatával, potenciálisan hatással üzembe helyezési időt, mert a párhuzamos két erőforrások, amelyek nem üzembe helyezni a Resource Manager. 

## <a name="child-resources"></a>Gyermek-erőforrás
Az erőforrás-tulajdonság az erőforrás definiált kapcsolódó gyermekerőforrásait megadását teszi lehetővé. Gyermekerőforrásait meghatározott öt szintnél mélyebb csak lehet. Fontos megjegyezni, hogy a nem egy gyermek-erőforrás és a szülő erőforrás között jön létre az implicit telepítési függ. A gyermek-erőforrás után a szülő erőforrás üzembe helyezni van szüksége, a dependsOn tulajdonság függőséget explicit módon meg kell adni. 

Minden egyes szülőerőforrás csak bizonyos erőforrástípusok gyermek erőforrásként fogad el. Az elfogadott erőforrástípusok vannak megadva a [sablonséma](https://github.com/Azure/azure-resource-manager-schemas) a szülő erőforrás. A gyermek erőforrástípus neve szerepel a szülő erőforrástípus neve például **Microsoft.Web/sites/config** és **Microsoft.Web/sites/extensions** forrásanyag mindkét a gyermek**Microsoft.Web/sites**.

Az alábbi példa bemutatja egy SQL server és SQL database. Figyelje meg, hogy az explicit függ az SQL database és SQL server között van definiálva, annak ellenére, hogy az adatbázis egy alárendelt kiszolgáló.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>referencia- és funkciók
A [függvényre](resource-group-template-functions-resource.md#reference) lehetővé teszi, hogy egy kifejezés az értékét más JSON név-érték párokból vagy a futásidejű erőforrások származtassa. A [lista * funkciók](resource-group-template-functions-resource.md#list) erőforrás értékek visszaadásához a list művelet.  Referencia- és kifejezések implicit módon deklarálja, hogy egy erőforrás függ egy másik, vagy ha a hivatkozott erőforrás üzembe van helyezve, az ugyanazt a sablont, és a nevét (nem erőforrás-azonosító) által hivatkozott. Ha a referencia- vagy függvényeket az erőforrás-azonosító, az egy implicit hivatkozás nem jön létre.

A referencia-függvény általános formátuma:

```json
reference('resourceName').propertyPath
```

A listkeys műveletének függvény általános formátuma:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

A következő példában a CDN-végpont explicit módon függ, hogy a CDN-profil, és implicit módon függ a webes alkalmazás.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Ez az elem vagy a dependsOn elem is használható függőségeinek megadása, de nem szeretné használni mindkét függő ugyanarra az erőforrásra vonatkozó. Amikor csak lehetséges, kerülje a szükségtelen függőség hozzáadása egy implicit hivatkozás használatával.

További tudnivalókért lásd: [függvényre](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Körkörös függőségi viszony

Erőforrás-kezelő körkörös függőségi azonosítja a sablon érvényesítése során. Ha hibaüzenet jelenik meg, amely megállapítja, hogy létezik-e körkörös függőséget, értékelje ki a sablont, hogy függőségek nem szükségesek, és el kell távolítani. Ha eltávolítja a függőségek nem működik, elkerülheti a körkörös függőségi szerint bizonyos üzembe helyezési műveletek helyezi át az erőforrásokat, a rendszer körkörös függőséget után üzembe helyezett gyermekerőforrásait. Tegyük fel például, hogy két virtuális gépet telepít, de a tulajdonságai az egyes hivatkozni, a másik be kell. A következő sorrendben telepíthetők:

1. vm1
2. vm2
3. A vm1 futtatására szolgáló bővítmény a vm1, vm2 és függ. A bővítmény a vm1, vm2 olvas, adja meg az értékeket.
4. A vm2 bővítmény a vm1, vm2 és függ. A bővítmény lekéri a vm1, vm2 értékek beállítása.

További információ a értékelése a telepítési sorrendet, és a függőségi hibák megoldása: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>További lépések

* Go-oktatóanyagot, tekintse meg [oktatóanyag: Azure Resource Manager-sablonok létrehozása a tőle függő erőforrások](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Javaslatok függőségek beállításakor, lásd: [gyakorlati tanácsok az Azure Resource Manager-sablon](template-best-practices.md).
* Üzembe helyezés során függőségek hibaelhárítással kapcsolatos tudnivalókért lásd: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Az Azure Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [sablonok készítése](resource-group-authoring-templates.md). 
* A sablonban rendelkezésre álló függvények listája: [sablonfüggvények](resource-group-template-functions.md).

