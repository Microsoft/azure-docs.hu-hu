---
title: A REST használata az ML-erőforrások kezeléséhez
titleSuffix: Azure Machine Learning
description: A REST API-k használata Azure Machine Learning erőforrások, például munkaterületek létrehozására, futtatására és törlésére, valamint modellek regisztrálására.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b61050db29ff960b7923f2211b2f09649608b37e
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520319"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Azure ML-erőforrások létrehozása, futtatása és törlése a REST használatával



Az Azure ML-erőforrások több módon is kezelhetők. Használhatja a [portált](https://portal.azure.com/), a [parancssori felületet](/cli/azure)vagy a [Python SDK](/python/api/overview/azure/ml/intro)-t. Vagy a REST API is kiválaszthatja. A REST API a HTTP-műveleteket szabványos módon használja az erőforrások létrehozásához, lekéréséhez, frissítéséhez és törléséhez. A REST API bármely olyan nyelven vagy eszközzel működik, amely HTTP-kéréseket tesz elérhetővé. A REST egyszerű szerkezete sokszor jó választás a parancsfájlkezelési környezetekben és a MLOps-automatizálásban. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Engedélyezési jogkivonat beolvasása
> * Megfelelően formázott REST-kérelem létrehozása az egyszerű szolgáltatás hitelesítésének használatával
> * Az Azure ML hierarchikus erőforrásaival kapcsolatos információk lekérése a GET kérelmek használatával
> * Erőforrások létrehozására és módosítására szolgáló PUT és POST kérelmek használata
> * TÖRLÉSi kérések használata az erőforrások tisztításához 
> * Kulcs alapú hitelesítés használata az üzembe helyezett modellek pontozásához

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** , amelyhez rendszergazdai jogosultságokkal rendelkezik. Ha nem rendelkezik ilyen előfizetéssel, próbálja ki az [ingyenes vagy fizetős személyes előfizetést](https://aka.ms/AMLFree)
- Egy [Azure Machine learning-munkaterület](./how-to-manage-workspace.md)
- A rendszergazdai REST-kérelmek az egyszerű szolgáltatásnév hitelesítését használják. Az egyszerű szolgáltatás munkaterületen való létrehozásához kövesse a [hitelesítés beállítása Azure Machine learning erőforrásokhoz és munkafolyamatokhoz](./how-to-setup-authentication.md#service-principal-authentication) című témakör lépéseit.
- A **curl** segédprogram. A **curl** program a [Windows alrendszerben Linux](/windows/wsl/install-win10) vagy bármely Unix-disztribúció számára elérhető. A PowerShellben a **curl** a **meghívó-webkérések** aliasa, amely a következő `curl -d "key=val" -X POST uri` lesz: `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Egyszerű szolgáltatásnév hitelesítési jogkivonatának beolvasása

A rendszergazdai REST-kérelmek hitelesítése OAuth2 implicit folyamattal történik. Ez a hitelesítési folyamat az előfizetés egyszerű szolgáltatásnév által biztosított jogkivonatot használja. A jogkivonat lekéréséhez a következőkre lesz szüksége:

- A bérlő azonosítója (annak a szervezetnek az azonosítása, amelyhez az előfizetés tartozik)
- Az ügyfél-azonosító (amely a létrehozott jogkivonathoz lesz társítva)
- Az ügyfél titkos kulcsa (amelyet védenie kell)

Ezeket az értékeket az egyszerű szolgáltatásnév létrehozási válasza alapján kell megkapnia. Ezeket az értékeket a [Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](./how-to-setup-authentication.md#service-principal-authentication)című szakaszban tárgyaljuk. Ha céges előfizetését használja, lehet, hogy nincs engedélye egy egyszerű szolgáltatásnév létrehozásához. Ebben az esetben [ingyenes vagy fizetős személyes előfizetést](https://aka.ms/AMLFree)kell használnia.

Token lekérése:

1. Nyisson meg egy terminálablakot.
1. Adja meg a következő kódot a parancssorban
1. Helyettesítse be a, a és a saját értékeit `{your-tenant-id}` `{your-client-id}` `{your-client-secret}` . Ebben a cikkben a kapcsos zárójelek által körülvett sztringek a saját megfelelő értékeire cserélt változók.
1. Futtassa a

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A válasznak egy órára jó hozzáférési jogkivonatot kell biztosítania:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Jegyezze fel a jogkivonatot, mivel az összes további rendszergazdai kérelem hitelesítéséhez használni fogja. Ezt úgy teheti meg, hogy az összes kérelemben beállítja az engedélyezési fejlécet:

```bash
curl -h "Authorization:Bearer {your-access-token}" ...more args...
```

Vegye figyelembe, hogy az érték a "tulajdonos" karakterlánccal kezdődik, beleértve a token hozzáadása előtt egy szóközzel.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Az előfizetéshez társított erőforráscsoportok listájának beolvasása

Az előfizetéshez társított erőforráscsoportok listájának lekéréséhez futtassa a következőt:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Az Azure-ban számos REST API van közzétéve. Minden szolgáltató frissíti az API-t a saját ritmusán, de a meglévő programok megszakítása nélkül. A szolgáltató az `api-version` argumentumot használja a kompatibilitás biztosításához. Az `api-version` argumentum a szolgáltatásról a szolgáltatásra változik. A Machine Learning szolgáltatás esetében például az aktuális API-verzió a következő: `2019-11-01` . A Storage-fiókok esetében ez a következő: `2019-06-01` . Kulcstartók esetében ez a következő: `2019-09-01` . Az összes REST-hívásnak a `api-version` várt értékre kell állítania az argumentumot. A megadott verzió szintaxisát és szemantikai lehetőségeit is használhatja, még akkor is, ha az API továbbra is fejlődik. Ha az argumentum nélkül küld egy szolgáltatónak egy kérést `api-version` , a válasz a támogatott értékek egy ember által olvasható listáját fogja tartalmazni. 

A fenti hívás az űrlap tömörített JSON-válaszát eredményezi: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Munkaterületek és erőforrásaik részletezése

Egy erőforráscsoport munkaterületének lekéréséhez futtassa a következőt, és cserélje le a és a parancsot `{your-subscription-id}` `{your-resource-group}` `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Ekkor megjelenik egy JSON-lista, amely egy listát tartalmaz, amely egy adott munkaterületet részletez:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

A munkaterületen belüli erőforrásokkal való munkavégzéshez az általános **Management.Azure.com** -kiszolgálóról a munkaterület helyére jellemző REST API kiszolgálóra kell váltania. Jegyezze fel a kulcs értékét a `discoveryUrl` fenti JSON-válaszban. Ha ezt az URL-címet kapja, a következőhöz hasonló választ kap:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

A válasz értéke annak a `api` kiszolgálónak az URL-címe, amelyet a további kérésekhez fog használni. A kísérletek listázásához például küldje el a következő parancsot. Cserélje le a `regional-api-server` értéket a válasz értékére `api` (például: `centralus.api.azureml.ms` ). Továbbá cserélje le a,, `your-subscription-id` `your-resource-group` `your-workspace-name` , és `your-access-token` a szokásos módon:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Hasonlóképpen, ha a munkaterületen regisztrált modelleket szeretne beolvasni, küldje el a következőt:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Figyelje meg, hogy az elérési úton megjelenő kísérletek listája a `history/v1.0` modellek listázása mellett kezdődik `modelmanagement/v1.0` . A REST API több operatív csoportba vannak osztva, amelyek mindegyike külön elérési úttal rendelkezik. 

|Terület|Elérési út|
|-|-|
|Artifacts|/rest/api/azureml|
|Adattárolók|/azure/machine-learning/how-to-access-data|
|Hiperparaméterek finomhangolása|HyperDrive/v 1.0/|
|Modellek|modelmanagement/v 1.0/|
|Előzmények|végrehajtás/v 1.0/és előzmények/v 1.0/|

A REST API a következő általános mintázattal derítheti fel:

|URL-összetevő|Példa|
|-|-|
| https://| |
| regionális-API-kiszolgáló/ | centralus.api.azureml.ms/ |
| műveletek – elérési út/ | előzmények/v 1.0/ |
| előfizetések/{saját előfizetés-azonosító}/ | előfizetések/abcde123-ABAB-ABAB-1234-0123456789abc/ |
| resourceGroups/{erőforrás-csoport}/ | resourceGroups/MyResourceGroup/ |
| szolgáltatók/művelet – szolgáltató/ | szolgáltatók/Microsoft. MachineLearningServices/ |
| Szolgáltató – erőforrás-útvonal/ | munkaterület/MLWorkspace/Sajátmunkaterület/FirstExperiment/Run/1/ |
| műveletek – végpont/ | összetevők/metaadatok/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Erőforrások létrehozása és módosítása PUT és POST kérelmek használatával

A GET művelet erőforrás-lekérése mellett a REST API támogatja az összes olyan erőforrás létrehozását, amely a ML-megoldások betanításához, üzembe helyezéséhez és figyeléséhez szükséges. 

Az ML-modellek betanítása és futtatása számítási erőforrásokat igényel. A munkaterületek számítási erőforrásait az alábbiak szerint listázhatja: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Elnevezett számítási erőforrás létrehozásához vagy felülírásához PUT-kérést kell használni. A következő, a, a, a, a, a, a, a, a, a, a, a, a, a és a értékek már ismerős helyettesítései mellett `your-subscription-id` `your-resource-group` `your-workspace-name` `your-access-token` `your-compute-name` `location` `vmSize` `vmPriority` `scaleSettings` `adminUserName` `adminUserPassword` . A (z) [Machine learning Compute – SDK létrehozása vagy frissítése](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)című hivatkozásban megadott módon a következő parancs létrehoz egy dedikált, egycsomópontos Standard_D1 (egy alapszintű CPU-számítási erőforrást), amely 30 perc után le lesz méretezve:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> A Windows-terminálokon a JSON-adatok küldésekor előfordulhat, hogy el kell menekülnie a dupla idézőjeles szimbólumokkal. Ez a szöveg, például a `"location"` válik `\"location\"` . 

Egy sikeres kérelem kap egy `201 Created` választ, de a válasz egyszerűen azt jelenti, hogy a kiépítési folyamat megkezdődött. A sikeres Befejezés megerősítéséhez le kell kérdezni (vagy a portált kell használnia).

### <a name="create-an-experimental-run"></a>Kísérleti Futtatás létrehozása

Egy kísérleten belüli Futtatás indításához szüksége van egy zip-mappára, amely tartalmazza a betanítási parancsfájlt és a kapcsolódó fájlokat, valamint egy Run definition JSON-fájlt. A zip-mappának tartalmaznia kell a Python-bejegyzés fájlját a gyökérkönyvtárában. Például egy triviális Python-programot, például a következőt egy **train.zip** nevű mappába.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Mentse ezt a következő kódrészletet **definition.jsként**. Ellenőrizze, hogy a "script" érték megegyezik-e a csak a tömörített Python-fájl nevével. Ellenőrizze, hogy a "Target" érték megegyezik-e egy elérhető számítási erőforrás nevével. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Tegye közzé ezeket a fájlokat a kiszolgálóra a `multipart/form-data` tartalom használatával:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

A sikeres POST kérelem `200 OK` állapota a létrehozott Futtatás azonosítóját tartalmazó válasz törzsével fog létrejönni:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

A futtatást a REST-ful mintázattal figyelheti, amely most már ismerős lehet:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>A már nem szükséges erőforrások törlése

Néhány, de nem minden erőforrás támogatja a TÖRLÉSi műveletet. Győződjön meg arról, hogy az [API-hivatkozás](/rest/api/azureml/) a törléshez szükséges REST API véglegesítése előtt. Egy modell törléséhez például a következőt használhatja:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>A REST használata egy üzembe helyezett modell kiértékeléséhez

Habár lehetséges a modell üzembe helyezése úgy, hogy az egy egyszerű szolgáltatással legyen hitelesítve, a legtöbb ügyfélre irányuló központi telepítés a kulcs alapú hitelesítést használja. A megfelelő kulcsot az üzembe helyezés lapján találja a Studio **endpoints (végpontok** ) lapján. Ugyanazon a helyen jelenik meg a végpont pontozási URI-ja. A modell bemeneteit egy nevű JSON-tömbként kell modellezni `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Munkaterület létrehozása REST használatával 

Minden Azure ML-munkaterület négy további Azure-erőforrástól függ: egy, a felügyeletet engedélyező tároló-beállításjegyzékkel, egy kulcstartóval, egy Application Insights erőforrással és egy Storage-fiókkal. Nem hozhat létre munkaterületet, amíg ezek az erőforrások nem léteznek. Az egyes erőforrások létrehozásának részleteiért lásd a REST API referenciát.

Munkaterület létrehozásához a következőhöz hasonló hívást HELYEZZEN el `management.azure.com` . Habár a híváshoz nagy számú változót kell beállítania, szerkezetileg megegyeznek a cikk által tárgyalt más hívásokkal. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Meg kell kapnia egy `202 Accepted` választ, és a visszaadott fejlécekben egy URI-t `Location` . Ezt az URI-t beolvashatja a központi telepítéssel kapcsolatos információkért, beleértve a hasznos hibakeresési információkat is, ha probléma merül fel az egyik függő erőforrással kapcsolatban (például ha elfelejtette a rendszergazdai hozzáférés engedélyezését a tároló beállításjegyzékében). 

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

### <a name="deleting-the-azure-container-registry"></a>A Azure Container Registry törlése

A Azure Machine Learning munkaterület egyes műveletekhez Azure Container Registry (ACR) használ. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége lesz rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a teljes [AzureML REST API-referenciával](/rest/api/azureml/).
- Megtudhatja, hogyan használhatja a tervezőt az [autó árának előrejelzésére a tervezővel](./tutorial-designer-automobile-price-train-score.md).
- Ismerkedjen meg [Azure Machine learning Jupyter notebookokkal](..//machine-learning/samples-notebooks.md).
