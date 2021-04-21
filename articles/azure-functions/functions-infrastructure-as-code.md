---
title: Függvényalkalmazás-erőforrások Azure-ban való üzembe helyezésének automatizálása
description: Megtudhatja, hogyan építhet Azure Resource Manager a függvényalkalmazást üzembe helyező sablont.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 4bbd3491c45b15d43ae0e94b37b916cd8091e655
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834209"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>A függvényalkalmazás erőforrás-üzembe helyezésének automatizálása a Azure Functions

Függvényalkalmazás üzembe helyezéséhez Azure Resource Manager sablont. Ez a cikk az ehhez szükséges erőforrásokat és paramétereket ismerteti. Előfordulhat, hogy további erőforrásokat kell [](functions-triggers-bindings.md) üzembe helyeznie a függvényalkalmazás eseményindítóitól és kötéseitől függően.

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md) (Azure Resource Manager-sablonok készítése) című témakörben talál.

Mintasablonok:
- [Használatban van egy függvényalkalmazás]
- [Függvényalkalmazás a Azure App Service tervben]

## <a name="required-resources"></a>Szükséges erőforrások

A Azure Functions üzemelő példány általában a következő erőforrásokból áll:

| Erőforrás                                                                           | Követelmény | Szintaxis és tulajdonságok referenciája                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Függvényalkalmazás                                                                     | Kötelező    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| [Egy Azure Storage-fiók](../storage/index.yml)                                   | Kötelező    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Egy [Application Insights](../azure-monitor/app/app-insights-overview.md) összetevő | Választható    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| Egy [üzemeltetési csomag](./functions-scale.md)                                             | <sup>1. nem kötelező</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Üzemeltetési csomagra csak akkor van szükség, ha [](./functions-premium-plan.md) a függvényalkalmazást prémium vagy App Service [futtatja.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Bár ez nem kötelező, erősen ajánlott az Application Insights konfigurálása.

<a name="storage"></a>
### <a name="storage-account"></a>Tárfiók

A függvényalkalmazáshoz Azure Storage-fiók szükséges. Szüksége van egy általános célú fiókra, amely támogatja a blobokat, táblákat, üzenetsorokat és fájlokat. További információkért lásd a Azure Functions [követelményeit.](storage-considerations.md#storage-account-requirements)

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

Emellett a tulajdonságot alkalmazásbeállításként kell megadni `AzureWebJobsStorage` a helykonfigurációban. Ha a függvényalkalmazás nem használ Application Insights a figyeléshez, alkalmazásbeállításként is meg kell `AzureWebJobsDashboard` adnia.

A Azure Functions futtatás a kapcsolati `AzureWebJobsStorage` sztringet használja belső üzenetsorok létrehozásához.  Ha Application Insights nincs engedélyezve, a futásidő a kapcsolati sztring használatával jelentkezik be az Azure Table Storage-ba, és bekapcsolja `AzureWebJobsDashboard` a **Monitor** lapot a portálon.

Ezek a tulajdonságok a objektum `appSettings` gyűjteményében vannak `siteConfig` megadva:

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

Application Insights ajánlott a függvényalkalmazások figyelése. A Application Insights erőforrás a **Microsoft.Insights/components** típussal és a web típussal van **definiálva:**

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

Emellett a rendszerindító kulcsot is meg kell adni a függvényalkalmazásnak az `APPINSIGHTS_INSTRUMENTATIONKEY` alkalmazásbeállítással. Ez a tulajdonság az objektum `appSettings` gyűjteményében van `siteConfig` megadva:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Üzemeltetési csomag

A szolgáltatáscsomag definíciója változó, és a következők egyike lehet:
* [Használat alapján csomag](#consumption) (alapértelmezett)
* [Prémium szintű csomag](#premium)
* [App Service-csomag](#app-service-plan)

### <a name="function-app"></a>Függvényalkalmazás

A függvényalkalmazás erőforrásának meghatározása **Microsoft.Web/sites** típusú erőforrás és a functionapp típusa **alapján történt:**

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
> Ha explicit módon definiál egy üzemeltetési tervet, egy további elemre lesz szükség a dependsOn tömbben: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

A függvényalkalmazásnak tartalmaznia kell az alábbi alkalmazásbeállításokat:

| Beállítás neve                 | Description                                                                               | Példaértékek                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Egy kapcsolati sztring egy tárfiókhoz, amely a Functions-futtatás által a belső üzenetsor-ozáshoz használható | Lásd: [Tárfiók](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A Azure Functions verziója                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Az alkalmazás függvényéhez használt nyelvi verem                                   | `dotnet`, `node` , `java` , `python` vagy `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Csak a nyelvi verem `node` használata esetén szükséges, a használni szükséges verziót adja meg              | `10.14.1`                             |

Ezek a tulajdonságok a tulajdonság `appSettings` gyűjteményében vannak `siteConfig` megadva:

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

## <a name="deploy-on-consumption-plan"></a>Üzembe helyezés használat alapján csomag

A használatban van csomag automatikusan lefoglalja a számítási teljesítményt, amikor a kód fut, szükség szerint horizontálisan felméretez a terhelés kezelésével, majd leméretez, ha a kód nem fut. Nem kell fizetnie a tétlen virtuális gépekért, és nem kell előre lefoglalni a kapacitást. További információ: Azure Functions [méretezése és üzemeltetése.](consumption-plan.md)

Mintasablonért Azure Resource Manager lásd: [Függvényalkalmazás használat alapján csomag.]

### <a name="create-a-consumption-plan"></a>Használat alapján létrehozott csomag létrehozása

A használatban van csomagokat nem kell meghatározni. A függvényalkalmazás-erőforrás létrehozásakor a rendszer automatikusan létrehoz vagy kiválaszt egyet régiónként.

A használaton keresztüli csomag a "serverfarm" erőforrás egy speciális típusa. Windows rendszeren a és a tulajdonság értékének használatával `Dynamic` `computeMode` adhatja `sku` meg:

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
> A használatban van csomag nem határozható meg explicit módon Linuxhoz. A rendszer automatikusan létrehoz egy újat.

Ha explicit módon határozza meg a használaton belüli csomagját, be kell állítania a tulajdonságot az alkalmazáson, hogy az a csomag erőforrás-azonosítójára `serverFarmId` mutatzon. Győződjön meg arról, hogy a függvényalkalmazás rendelkezik a csomag `dependsOn` beállításával is.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A Használat alapján csomag használata esetén dedukció Windows és Linux rendszeren futó függvényalkalmazás által megkövetelt beállítások. 

#### <a name="windows"></a>Windows

Windows rendszeren a használatban van egy további beállítás a helykonfigurációban: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Ez a tulajdonság a függvényalkalmazás kódját és konfigurációját tároló tárfiókot konfigurálja.

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
> Ne állítsa be a beállítást, mert az a webhely első létrehozásakor [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) jön létre.  

#### <a name="linux"></a>Linux

Linux rendszeren a függvényalkalmazásnak a következőre kell beállítania: , és a tulajdonságnak a következőnek kell `kind` `functionapp,linux` `reserved` lennie: `true` . 

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

A [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) és a beállítások Linux rendszeren nem [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) támogatottak.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Üzembe helyezés prémium szintű csomagon

A prémium szintű csomag ugyanazt a skálázást kínálja, mint a használat alapján, de dedikált erőforrásokat és további képességeket tartalmaz. További tudnivalókért lásd: [Prémium Azure Functions csomag.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Prémium szintű csomag létrehozása

A prémium szintű csomag a "serverfarm" erőforrás egy speciális típusa. A tulajdonságot a , vagy a tulajdonság értékével adhatja meg a `EP1` `EP2` description `EP3` `Name` `sku` [objektumban.](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)

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

A prémium szintű csomag függvényalkalmazásának a tulajdonságot a korábban létrehozott csomag `serverFarmId` erőforrás-azonosítójára kell beállítania. Emellett a prémium szintű csomaghoz egy további beállításra van szükség a helykonfigurációban: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Ez a tulajdonság a függvényalkalmazás kódját és konfigurációját tároló tárfiókot konfigurálja.

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
> Ne állítsa be a beállítást, mert az a webhely első létrehozásakor [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) jön létre.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Üzembe helyezés App Service tervben

A App Service a függvényalkalmazás az Alapszintű, Standard és Prémium termékkategóriákban dedikált virtuális gépeken fut, a webalkalmazásokéhoz hasonlóan. A terv App Service részletes áttekintésében Azure App Service [áttekintést.](../app-service/overview-hosting-plans.md)

Mintasablonért Azure Resource Manager lásd: [Függvényalkalmazás a Azure App Service tervben.]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

A App Service a "serverfarm" erőforrás határozza meg.

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

Az alkalmazás Linux rendszeren való futtatásához a következőt is be kell `kind` `Linux` állítania:

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

A App Service csomag függvényalkalmazásának a tulajdonságot a korábban létrehozott csomag `serverFarmId` erőforrás-azonosítójára kell beállítania.

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

A Linux-alkalmazásoknak tartalmazniuk kell egy tulajdonságot is a `linuxFxVersion` `siteConfig` alatt. Ha csak kódot helyez üzembe, ennek értékét a kívánt futásidejű verem határozza meg a következő ```runtime|runtimeVersion``` formátumban:

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

Ha egyéni [tároló-rendszerképet](./functions-create-function-linux-custom-image.md)helyez üzembe, meg kell adnia a használatával, és olyan konfigurációt kell megadnia, amely lehetővé teszi a rendszerkép `linuxFxVersion` [lekért](../app-service/index.yml)mint az alábbi Web App for Containers. Továbbá állítsa be `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a következőt: , mivel az alkalmazás tartalma magában a `false` tárolóban található:

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

## <a name="customizing-a-deployment"></a>Üzemelő példány testreszabása

Egy függvényalkalmazás számos gyermekerőforrással rendelkezik, amelyek az üzembe helyezés során használhatók, beleértve az alkalmazásbeállításokat és a forrásvezérlési beállításokat. Dönthet úgy is, hogy eltávolítja a **sourcecontrols** gyermekerőforrást, és helyette egy másik üzembe [helyezési lehetőséget használ.](functions-continuous-deployment.md)

> [!IMPORTANT]
> Ahhoz, hogy sikeresen üzembe tudja helyezni az alkalmazást Azure Resource Manager használatával, fontos tisztában lenni az erőforrások Azure-beli üzembe helyezési mikéntjéhez. A következő példában a legfelső szintű konfigurációk a **siteConfig** használatával alkalmazhatók. Fontos, hogy ezeket a konfigurációkat a legfelső szinten állítsa be, mert információt továbbít a Functions-futtatásnak és az üzembe helyezési motornak. A gyermek **forrásvezérlők/webes** erőforrás alkalmazása előtt legfelső szintű adatokra van szükség. Bár ezek a beállítások konfigurálhatóak a gyermekszintű **config/appSettings** erőforrásban, bizonyos esetekben  a függvényalkalmazást üzembe kell helyezni a **config/appSettings** alkalmazása előtt. Ha például függvényeket használ a [Logic Apps,](../logic-apps/index.yml)a függvények egy másik erőforrás függőségei.

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
> Ez a sablon a Project app settings [(Projektalkalmazás](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) beállításai) értéket használja, amely beállítja azt az alapkönyvtárat, amelyben a Functions üzembe helyezési motorja (Kudu) üzembe helyezhető kódot keres. Az adattárban a függvények az src mappa **almappában** találhatók. Az előző példában tehát az alkalmazásbeállítások értékét értékre állítva adhatja `src` meg. Ha a függvények az adattár gyökerében vannak, vagy ha nem a forrásvezérlőből telepíti, eltávolíthatja ezt az alkalmazásbeállítási értéket.

## <a name="deploy-your-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez az alábbi módszerek bármelyikét használhatja:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Üzembe helyezés az Azure-ban gomb

Cserélje le a helyére a githubon található fájl nyers elérési ```<url-encoded-path-to-azuredeploy-json>``` útjának [](https://www.bing.com/search?q=url+encode) `azuredeploy.json` URL-kódolású verzióját.

Az alábbi példa Markdown-jelölést használ:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Az alábbi példa HTML-t használ:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

Az alábbi PowerShell-parancsok létrehoznak egy erőforráscsoportot, és üzembe helyeznek egy sablont, amely létrehoz egy függvényalkalmazást a szükséges erőforrásokkal. A helyi futtatáshoz telepített [Azure PowerShell](/powershell/azure/install-az-ps) kell. A [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) bejelentkezéshez futtassa a következőt: .

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

Az üzembe helyezés teszteléséhez használhat ehhez [hasonló](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) sablont, amely egy függvényalkalmazást hoz létre a Windowson egy használat alapján. Cserélje `<function-app-name>` le a helyére a függvényalkalmazás egyedi nevét.

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztésről és a Azure Functions.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure-függvényalkalmazás beállításainak konfigurálása](functions-how-to-use-azure-function-app-settings.md)
* [Az első Azure-függvény létrehozása](./functions-get-started.md)

<!-- LINKS -->

[Függvényalkalmazás használaton belüli csomaghoz]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Függvényalkalmazás a Azure App Service tervben]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
