---
title: Function app-erőforrás üzembe helyezésének automatizálása az Azure-ban
description: Megtudhatja, hogyan hozhat létre olyan Azure Resource Manager sablont, amely üzembe helyezi a Function alkalmazást.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9df4c62a65fd133c6ea8dc84e33d7c7b02d94cbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494039"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>A Function alkalmazás erőforrás-telepítésének automatizálása Azure Functions

Egy Azure Resource Manager sablon használatával üzembe helyezhet egy Function alkalmazást. Ez a cikk a szükséges erőforrásokat és paramétereket ismerteti. Előfordulhat, hogy további erőforrásokat kell telepítenie a Function alkalmazásban lévő [Eseményindítók és kötések](functions-triggers-bindings.md) függvényében.

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md) (Azure Resource Manager-sablonok készítése) című témakörben talál.

A példákat lásd:
- [Function alkalmazás a használati tervben]
- [Function alkalmazás Azure App Service csomagon]

## <a name="required-resources"></a>Szükséges erőforrások

Az Azure Functions üzemelő példányok általában az alábbi erőforrásokból állnak:

| Erőforrás                                                                           | Követelmény | Szintaxis és tulajdonságok – hivatkozás                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Function-alkalmazás                                                                     | Kötelező    | [Microsoft. Web/Sites](/azure/templates/microsoft.web/sites)                             |
| [Azure Storage](../storage/index.yml) -fiók                                   | Kötelező    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) összetevő | Választható    | [Microsoft. bepillantások/összetevők](/azure/templates/microsoft.insights/components)         |
| [Üzemeltetési csomag](./functions-scale.md)                                             | Opcionális<sup>1</sup>    | [Microsoft. Web/kiszolgálófarmok](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> A szolgáltatási csomagra csak akkor van szükség, ha úgy dönt, hogy prémium szintű [csomagon](./functions-premium-plan.md) vagy [app Service csomagon](../app-service/overview-hosting-plans.md)futtatja a Function alkalmazást.

> [!TIP]
> Habár nem kötelező, erősen ajánlott az alkalmazáshoz Application Insights konfigurálni.

<a name="storage"></a>
### <a name="storage-account"></a>A(z)

Egy Function alkalmazáshoz Azure Storage-fiók szükséges. Olyan általános célú fiókra van szüksége, amely támogatja a blobokat, a táblákat, a várólistákat és a fájlokat. További információ: [Azure functions Storage-fiókra vonatkozó követelmények](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Emellett a tulajdonságot `AzureWebJobsStorage` meg kell adni a hely konfigurációja beállításban. Ha a Function alkalmazás nem használja Application Insights a figyeléshez, azt is meg kell adnia az `AzureWebJobsDashboard` alkalmazás beállításaként.

A Azure Functions futtatókörnyezet a `AzureWebJobsStorage` kapcsolódási karakterlánc használatával hozza létre a belső várólistákat.  Ha a Application Insights nincs engedélyezve, a futtatókörnyezet a `AzureWebJobsDashboard` kapcsolódási karakterláncot használja az Azure Table Storage-ba való bejelentkezéshez és a portál **figyelés** lapjának bekapcsolásához.

Ezek a tulajdonságok az `appSettings` objektum gyűjteményében vannak megadva `siteConfig` :

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

A Application Insights a Function apps figyeléséhez ajánlott. A Application Insights erőforrás a **Microsoft. ininsights/Components** és a Kind **web** típussal van definiálva:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Emellett a kialakítási kulcsot is meg kell adni a Function alkalmazásnak az `APPINSIGHTS_INSTRUMENTATIONKEY` Alkalmazásbeállítások használatával. Ez a tulajdonság az `appSettings` objektum gyűjteményében van megadva `siteConfig` :

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Üzemeltetési csomag

A üzemeltetési csomag definíciója változó, és a következők egyike lehet:
* [Felhasználási terv](#consumption) (alapértelmezett)
* [Prémium szintű csomag](#premium)
* [App Service-csomag](#app-service-plan)

### <a name="function-app"></a>Függvényalkalmazás

A Function app erőforrás a **Microsoft. Web/Sites** és Kind **functionapp** típusú erőforrás használatával van definiálva:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Ha explicit módon definiál egy üzemeltetési csomagot, a dependsOn tömbben további elemre lenne szükség: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

A Function alkalmazásnak tartalmaznia kell ezeket az Alkalmazásbeállítások:

| Beállítás neve                 | Leírás                                                                               | Példaértékek                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Egy olyan Storage-fiókhoz tartozó kapcsolódási karakterlánc, amelyet a functions futtatókörnyezet a belső üzenetsor-kezelést használ | Lásd: [Storage-fiók](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Az Azure Functions futtatókörnyezet verziója                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Az alkalmazásban a függvényekhez használandó nyelvi verem                                   | `dotnet`,,, `node` `java` `python` vagy `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Csak a nyelvi verem használata esetén szükséges `node` , adja meg a használni kívánt verziót              | `10.14.1`                             |

Ezek a tulajdonságok a (z) `appSettings` tulajdonság gyűjteményében vannak megadva `siteConfig` :

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Üzembe helyezés a használati tervben

A használati terv automatikusan lefoglalja a számítási teljesítményt, amikor a kód fut, szükség szerint méretezi a terhelést, majd méretezi a értéket, ha a kód nem fut. A tétlen virtuális gépekért nem kell fizetnie, és nem kell előre lefoglalnia a kapacitást. További információ: [Azure functions skálázás és üzemeltetés](consumption-plan.md).

Minta Azure Resource Manager sablon esetében lásd: [Function app on felhasználási terv].

### <a name="create-a-consumption-plan"></a>Felhasználási terv létrehozása

Nem szükséges a használati terv meghatározása. A Function app-erőforrás létrehozásakor a rendszer automatikusan létrehozza vagy kiválasztja az egyiket a régió alapján.

A felhasználási terv egy speciális "kiszolgálófarm" típusú erőforrás. A Windows esetében a `Dynamic` és a tulajdonságok értékének megadásával adhatja meg a következőt `computeMode` `sku` :

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> A használati tervet nem lehet explicit módon definiálni a Linux rendszerhez. A rendszer automatikusan létrehozza.

Ha explicit módon határozza meg a használati tervet, be kell állítania az `serverFarmId` alkalmazás tulajdonságát, hogy az a csomag erőforrás-azonosítójára mutasson. Győződjön meg arról, hogy a Function alkalmazás rendelkezik a `dependsOn` terv beállításával is.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A használati tervben futó Function app által megkövetelt beállítások elhalasztása a Windows és a Linux között. 

#### <a name="windows"></a>Windows

Windows rendszeren a hely konfigurációjában egy felhasználási tervhez további beállítás szükséges: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Ez a tulajdonság konfigurálja azt a Storage-fiókot, ahol a Function app-kód és a konfiguráció tárolva van.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Ne állítsa be a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) beállítást, mert az a hely első létrehozásakor jön létre.  

#### <a name="linux"></a>Linux

Linux rendszeren a Function alkalmazásnak rendelkeznie kell a (z) értékkel `kind` `functionapp,linux` , és rendelkeznie kell a `reserved` tulajdonság beállításával `true` . 

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

A [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) és a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) beállítások Linux rendszeren nem támogatottak.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Üzembe helyezés prémium csomaggal

A Prémium csomag ugyanazt a skálázást kínálja, mint a használati terv, de dedikált erőforrásokat és további képességeket is tartalmaz. További információ: [Azure functions Premium csomag](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

A Prémium csomag egy speciális "kiszolgálófarm" típusú erőforrás. Megadhatja azt a vagy a `EP1` , `EP2` vagy a `EP3` `Name` tulajdonság értékének használatával a `sku` [Leírás objektumban](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A prémium szintű csomaghoz tartozó Function alkalmazásnak a `serverFarmId` korábban létrehozott csomag erőforrás-azonosítójára kell rendelkeznie. Emellett a prémium szintű csomaghoz további beállításra van szükség a hely konfigurációjában: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Ez a tulajdonság konfigurálja azt a Storage-fiókot, ahol a Function app-kód és a konfiguráció tárolva van.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Ne állítsa be a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) beállítást, mert az a hely első létrehozásakor jön létre.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Üzembe helyezés App Service tervben

A App Service tervben a Function app a webalkalmazásokhoz hasonló, az alapszintű, a standard és a prémium SKU-ra épülő dedikált virtuális gépeken fut. Az App Service-csomag működésével kapcsolatos részletekért tekintse [meg a Azure app Service tervek részletes áttekintését](../app-service/overview-hosting-plans.md).

Példa Azure Resource Manager sablonra: [Function app on Azure app Service Plan].

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az App Service-csomagot egy "kiszolgálófarm" erőforrás határozza meg.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Az alkalmazás Linux rendszeren való futtatásához a következőt is be kell állítania `kind` `Linux` :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Egy App Service tervben szereplő Function alkalmazásnak a `serverFarmId` korábban létrehozott csomag erőforrás-azonosítójára kell beállítania a tulajdonságot.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

A Linux-alkalmazásoknak tartalmaznia kell egy `linuxFxVersion` tulajdonságot is `siteConfig` . Ha csak a kód üzembe helyezését végzi, az értéket a kívánt futásidejű verem határozza meg a következő formátumban ```runtime|runtimeVersion``` :

| Verem            | Példaérték                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Ha [Egyéni tároló lemezképet helyez üzembe](./functions-create-function-linux-custom-image.md), meg kell adnia azt a-val, és tartalmaznia kell egy olyan `linuxFxVersion` konfigurációt, amely lehetővé teszi a lemezkép leválasztását a [Web App for containers](../app-service/index.yml). Emellett állítsa a (z) értékre, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false` mert az alkalmazás tartalma a tárolóban van megadva:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Központi telepítés testreszabása

A Function app számos alárendelt erőforrással rendelkezik, amelyek használhatók a központi telepítésben, beleértve az Alkalmazásbeállítások és a verziókövetés beállításait is. Dönthet úgy is, hogy eltávolítja a **sourcecontrols** -gyermek erőforrást, és egy másik [központi telepítési lehetőséget](functions-continuous-deployment.md) használ.

> [!IMPORTANT]
> Az alkalmazás Azure Resource Manager használatával történő sikeres üzembe helyezéséhez fontos megérteni, hogyan történik az erőforrások üzembe helyezése az Azure-ban. A következő példában a legfelső szintű konfigurációk a **siteConfig** használatával lesznek alkalmazva. Fontos, hogy legfelső szinten állítsa be ezeket a konfigurációkat, mert információt továbbítanak a functions futtatókörnyezet és a telepítési motor számára. A gyermek **sourcecontrols/webes** erőforrás alkalmazása előtt legfelső szintű információra van szükség. Habár ezeket a beállításokat a gyermek szintű **konfiguráció/appSettings** erőforrásban is konfigurálhatja, bizonyos esetekben a Function alkalmazást telepíteni kell a **config/appSettings** alkalmazása *előtt* . Ha például az [Logic apps](../logic-apps/index.yml)függvényt használja, a függvények egy másik erőforrástól függenek.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Ez a sablon a [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) app Settings (projekt alkalmazás beállításai) értéket használja, amely azt az alapkönyvtárat állítja be, amelyben a functions Deployment Engine (kudu) megkeresi a telepíthető kódot. A tárházban a függvények a **src** mappa almappájában találhatók. Tehát az előző példában az Alkalmazásbeállítások értékét a következőre állítja be: `src` . Ha a függvények a tárház gyökerében találhatók, vagy ha nem a verziókövetés alapján végzik el a telepítést, akkor eltávolíthatja ezt az Alkalmazásbeállítások értékét.

## <a name="deploy-your-template"></a>A sablon üzembe helyezése

A következő módokon végezheti el a sablon üzembe helyezését:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Üzembe helyezés az Azure-ban gomb

Cserélje le a ```<url-encoded-path-to-azuredeploy-json>``` fájlt a githubban található fájl nyers elérési útjának [URL-kódolású](https://www.bing.com/search?q=url+encode) verziójára `azuredeploy.json` .

Íme egy példa, amely az Markdown-t használja:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Az alábbi példa HTML-t használ:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

A következő PowerShell-parancsok létrehoznak egy erőforráscsoportot, és telepítenek egy olyan sablont, amely létrehozza a szükséges erőforrásokkal rendelkező Function alkalmazást. A helyi futtatáshoz [Azure PowerShell](/powershell/azure/install-az-ps) telepítve kell lennie. A [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) bejelentkezéshez futtassa a parancsot.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

A központi telepítés teszteléséhez használhat egy olyan [sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) , amely a Windowsban egy használati alkalmazást hoz létre egy felhasználási tervben. Cserélje le a `<function-app-name>` függvényt egy egyedi névre a Function alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions fejlesztéséről és konfigurálásáról.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Function app beállításainak konfigurálása](functions-how-to-use-azure-function-app-settings.md)
* [Az első Azure-függvény létrehozása](./functions-get-started.md)

<!-- LINKS -->

[Function alkalmazás a használati tervben]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Function alkalmazás Azure App Service csomagon]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
