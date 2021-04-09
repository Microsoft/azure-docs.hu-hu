---
title: Biztonságos üzembe helyezés régiók között – Azure Deployment Manager
description: Megtudhatja, hogyan helyezhet üzembe egy szolgáltatást számos régióban az Azure Deployment Manager és a biztonságos üzembe helyezési gyakorlatokkal kapcsolatban.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 63553b0bbca031faa44e0d88480fcc08950a3e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627499"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Biztonságos üzembe helyezési eljárások engedélyezése az Azure Deployment Manager (nyilvános előzetes verzió)

Ha a szolgáltatást több régióban szeretné üzembe helyezni, és győződjön meg arról, hogy az a vártnak megfelelően fut az egyes régiókban, az Azure Deployment Manager használatával koordinálhatja a szolgáltatás fokozatos bevezetését. Ugyanúgy, ahogy bármely Azure-telepítés esetében, a [Resource Manager-sablonokban](template-syntax.md)definiálhatja a szolgáltatás erőforrásait. A sablonok létrehozása után a Deployment Manager segítségével leírja a szolgáltatás topológiáját, és azt, hogy a rendszer hogyan legyen kivezetve.

Deployment Manager a Resource Manager egyik funkciója. Az üzembe helyezés során kibővíti a képességeit. Ha olyan összetett szolgáltatást használ, amelyet több régióban kell üzembe helyezni, használja Deployment Manager. A szolgáltatás kibocsátásának előkészítésével a régiókban történő üzembe helyezés előtt azonosíthat potenciális problémákat. Ha nincs szüksége a szakaszos bevezetéshez szükséges további óvintézkedésekre, használja a Resource Manager szabványos [központi telepítési lehetőségeit](deploy-portal.md) . A Deployment Manager zökkenőmentesen integrálható minden olyan, a Resource Manager-alapú üzemelő példányokat támogató, harmadik féltől származó eszközzel, mint a folyamatos integráció és a folyamatos teljesítés (CI/CD) ajánlatok.

Az Azure Deployment Manager előzetes verzióban érhető el. [Visszajelzések](https://aka.ms/admfeedback)megadásával segíthet a szolgáltatás tökéletesítésében.

A Deployment Manager használatához négy fájlt kell létrehoznia:

* Topológiai sablon.
* Bevezetési sablon.
* A topológia paraméterének fájlja.
* Paraméter-fájl a bevezetéshez.

A topológia sablonját a bevezetési sablon telepítése előtt kell telepíteni.

További források:

* Az [Azure Deployment Manager REST API referenciája](/rest/api/deploymentmanager/).
* [Oktatóanyag: az Azure Deployment Manager használata Resource Manager-sablonokkal](./deployment-manager-tutorial.md).
* [Oktatóanyag: az állapot-ellenőrzési szolgáltatás használata az Azure Deployment Managerban](./deployment-manager-tutorial-health-check.md).
* [Azure Deployment Manager minta](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identitás és hozzáférés

A Deployment Manager a [felhasználó által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) hajtja végre a telepítési műveleteket. Ezt az identitást a telepítés megkezdése előtt hozza létre. Hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelyen a szolgáltatást üzembe helyezi, és amely megfelelő jogosultsággal rendelkezik az üzembe helyezés befejezéséhez. A szerepkörökön keresztül kapott műveletekkel kapcsolatos információkért lásd: az [Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md).

Az identitásnak ugyanabban a helyen kell lennie, mint a bevezetés.

## <a name="topology-template"></a>Topológiai sablon

A topológiai sablon leírja a szolgáltatást alkotó Azure-erőforrásokat és a telepítésük helyét. Az alábbi képen egy példaként szolgáló szolgáltatás topológiája látható:

![Hierarchia a szolgáltatástól a szolgáltatástól a szolgáltatási egységig](./media/deployment-manager-overview/service-topology.png)

A topológia sablonja a következő erőforrásokat tartalmazza:

* Összetevő forrása – a Resource Manager-sablonok és-paraméterek tárolása.
* Szolgáltatási topológia – az összetevő forrására mutat.
  * Szolgáltatások – a hely és az Azure-előfizetés AZONOSÍTÓját adja meg.
    * Szolgáltatási egységek – az erőforráscsoport, a telepítési mód, valamint a sablon és a paraméter fájljainak elérési útjának megadása.

Ha szeretné megtudni, hogy mi történik minden szinten, hasznos lehet megtekinteni, hogy mely értékeket kell megadnia.

![Értékek az egyes szintekhez](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Összetevők forrása a sablonokhoz

A topológiai sablonban létre kell hoznia egy összetevő-forrást, amely tartalmazza a sablonokat és a paramétereket tartalmazó fájlokat. Az összetevő forrása a fájlok központi telepítéshez való lekérésének módja. A bináris fájlok egy másik összetevőjét láthatja a cikk későbbi részében.

Az alábbi példa az összetevő forrásának általános formátumát mutatja be.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

További információ: ArtifactSources- [sablon referenciája](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Szolgáltatástopológia

Az alábbi példa a szolgáltatás-topológiai erőforrás általános formátumát mutatja be. Adja meg annak az összetevő-forrásnak az erőforrás-AZONOSÍTÓját, amely tartalmazza a sablonokat és a paramétereket tartalmazó fájlokat. A szolgáltatás topológiája tartalmazza az összes szolgáltatás-erőforrást. Győződjön meg arról, hogy az összetevő forrása elérhető, mert a szolgáltatás topológiája függ tőle.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

További információ: ServiceTopologies- [sablon referenciája](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Szolgáltatások

Az alábbi példa a szolgáltatások erőforrás általános formátumát mutatja be. Az egyes szolgáltatásokban megadja a szolgáltatás üzembe helyezéséhez használt helyet és az Azure-előfizetés AZONOSÍTÓját. Több régióban való üzembe helyezéshez Definiáljon egy szolgáltatást az egyes régiókban. A szolgáltatás a szolgáltatási topológiától függ.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

További információ: [szolgáltatások sablonjának referenciája](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Szolgáltatási egység

Az alábbi példa a szolgáltatási egységek erőforrásának általános formátumát mutatja be. Minden egyes szolgáltatási egységben meg kell adnia az erőforráscsoportot, a központi telepítéshez használandó [telepítési módot](deployment-modes.md) , valamint a sablon és a paraméter fájljának elérési útját. Ha relatív elérési utat ad meg a sablonhoz és a paraméterekhez, a teljes elérési út az összetevők forrása gyökérkönyvtárában lesz létrehozva. Megadhatja a sablon és a paraméterek abszolút elérési útját, de elveszítheti a kiadások egyszerű verziójának lehetőségét. A szolgáltatási egység a szolgáltatástól függ.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Minden sablonnak tartalmaznia kell a kapcsolódó erőforrásokat, amelyeket egyetlen lépésben szeretne üzembe helyezni. Egy szolgáltatási egység rendelkezhet például olyan sablonnal, amely az összes erőforrást üzembe helyezi a szolgáltatás előtérén.

További információ: ServiceUnits- [sablon referenciája](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Bevezetési sablon

A bevezetési sablon ismerteti a szolgáltatás üzembe helyezéséhez szükséges lépéseket. Megadhatja a használni kívánt szolgáltatási topológiát, és megadhatja a szolgáltatási egységek központi telepítésének sorrendjét. Tartalmaz egy összetevő-forrást bináris fájlok tárolására a központi telepítéshez. A bevezetési sablonban a következő hierarchiát kell megadnia:

* Összetevő forrása.
* . Lépés.
* Bevezetés.
  * Step-csoportok.
    * Üzembe helyezési műveletek.

Az alábbi képen a bevezetési sablon hierarchiája látható:

![A kivezetéstől a lépésig elvégezhető hierarchia](./media/deployment-manager-overview/Rollout.png)

Az egyes bevezetések több lépésből álló csoportokba is tartozhatnak. Minden egyes lépési csoport egyetlen központi telepítési művelettel rendelkezik, amely egy szolgáltatási egységre mutat a szolgáltatási topológiában.

### <a name="artifact-source-for-binaries"></a>Bináris összetevők forrása

A bevezetési sablonban létre kell hoznia egy összetevő-forrást a szolgáltatáshoz telepítendő bináris fájlok számára. Az összetevő forrása hasonlít a [sablonokhoz tartozó összetevők forrásához](#artifact-source-for-templates), azzal a különbséggel, hogy a szolgáltatáshoz szükséges szkripteket, weblapokat, lefordított kódot vagy más fájlokat tartalmaz.

### <a name="steps"></a>Lépések

Megadhat egy lépést, amely a telepítési művelet előtt vagy után hajtható végre. Jelenleg csak a `wait` lépés és a `healthCheck` lépés érhető el.

A `wait` Folytatás előtt a lépés szünetelteti a telepítést. Lehetővé teszi, hogy a következő szolgáltatási egység telepítése előtt ellenőrizze, hogy a szolgáltatás a várt módon fut-e. Az alábbi példa egy lépés általános formátumát mutatja be `wait` .

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Az időtartam tulajdonság [ISO 8601 szabványt](https://en.wikipedia.org/wiki/ISO_8601#Durations)használ. Az előző példa egy egyperces várakozást határoz meg.

Az állapot-ellenőrzésekkel kapcsolatos további információkért lásd: az [állapot integrációjának bevezetése az azure Deployment Manager](./deployment-manager-health-check.md) és [oktatóanyag: az Azure Deployment Manager állapot-ellenőrzésének használata](./deployment-manager-tutorial-health-check.md).

További információkért lásd: a [Steps template referenciája](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Kibocsátások

Győződjön meg arról, hogy az összetevő forrása elérhető, mert a bevezetés ettől függ. A bevezetési szakasz az egyes központilag telepített szolgáltatási egységek esetében határozza meg a lépéseket. Megadhatja az üzembe helyezés előtt vagy után elvégzendő műveleteket. Megadhatja például a központi telepítést, amely a szolgáltatási egység üzembe helyezése után várakozik. Megadhatja a lépés csoportok sorrendjét.

Az Identity objektum meghatározza a [felhasználó által hozzárendelt felügyelt identitást](#identity-and-access) , amely végrehajtja a telepítési műveleteket.

Az alábbi példa a bevezetés általános formátumát mutatja be.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

További információ: bevezetési [sablon referenciája](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Paraméter fájlja

Két paraméter fájlját hozza létre. A szolgáltatás topológiájának telepítésekor egy paramétert használ a rendszer, a másik pedig a bevezetési telepítéshez használatos. Néhány értéknek meg kell győződnie arról, hogy a paraméterek mindkét fájljában azonosak.

## <a name="containerroot-variable"></a>containerRoot változó

A verzióval ellátott központi telepítések esetén az összetevők elérési útja minden új verziónál megváltozik. A központi telepítés első indításakor az elérési út lehet `https://<base-uri-blob-container>/binaries/1.0.0.0` . A második alkalommal, amikor lehetséges `https://<base-uri-blob-container>/binaries/1.0.0.1` . A Deployment Manager leegyszerűsíti az aktuális üzemelő példány helyes elérési útjának beolvasását a `$containerRoot` változó használatával. Ez az érték minden verzióra módosul, és nem ismert az üzembe helyezés előtt.

Használja az `$containerRoot` Azure-erőforrásokat üzembe helyező sablonhoz tartozó paraméterben található változót. A központi telepítés időpontjában ez a változó a bevezetésből származó tényleges értékekkel lesz lecserélve.

A bevezetés során például létre kell hoznia egy összetevő-forrást a bináris összetevőkhöz.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Figyelje meg a `artifactRoot` és a `sasUri` tulajdonságokat. Lehetséges, hogy az összetevő gyökeréhez hasonló érték van beállítva `binaries/1.0.0.0` . A SAS URI a Storage-tároló URI-ja, amely a hozzáféréshez SAS-tokent biztosít. Deployment Manager automatikusan létrehozza a `$containerRoot` változó értékét. Ezeket az értékeket a formátumban kombinálja `<container>/<artifactRoot>` .

A sablon és a paraméter fájljának ismernie kell a verziószámú bináris fájlok beolvasásának helyes elérési útját. Ha például egy webalkalmazás fájljait szeretné központilag telepíteni, hozza létre a következő paraméter-fájlt a `$containerRoot` változóval. Az elérési úthoz két fordított perjelet () kell használnia, `\\` mert az első egy escape-karakter.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Ezután használja a (z) paramétert a sablonban:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

A verzióval ellátott központi telepítéseket új mappák létrehozásával és a gyökér elérési útjának átadásával kezelheti a bevezetés során. Az elérési út az erőforrásokat központilag telepítő sablonra áramlik.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a Deployment Manager. A következő cikkből megtudhatja, hogyan helyezheti üzembe a Deployment Manager használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: az Azure Deployment Manager használata Resource Manager-sablonokkal](./deployment-manager-tutorial.md)
>
> [Rövid útmutató: az Azure Deployment Manager kipróbálása néhány perc alatt](https://github.com/Azure-Samples/adm-quickstart)
