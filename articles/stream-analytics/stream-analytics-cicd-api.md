---
title: A CI/CD használata a REST API-kkal IoT Edge-eszközökön futó Azure Stream Analytics
description: Ismerje meg, hogyan valósítható meg a folyamatos integrációs és üzembe helyezési folyamat a REST API-k használatával történő Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c3f776ad0996fa0b7422f0fca2d899a35e853d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016133"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>CI/CD implementálása IoT Edge Stream Analyticshoz API-k használatával

A REST API-k használatával lehetővé teheti a folyamatos integrációt és üzembe helyezést Azure Stream Analytics feladatokhoz. Ez a cikk példákat tartalmaz arra vonatkozóan, hogy mely API-k használhatók és hogyan használhatók. A REST API-k nem támogatottak Azure Cloud Shellon.

## <a name="call-apis-from-different-environments"></a>API-k hívása különböző környezetekben

A REST API-k Linux és Windows rendszerből is hívhatók. Az alábbi parancsok az API-hívás megfelelő szintaxisát szemléltetik. A jelen cikk későbbi szakaszaiban megadott API-használatot a rendszer ismerteti.

### <a name="linux"></a>Linux

Linux esetén a következő parancsokat használhatja `Curl` `Wget` :

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Windows esetén használja a PowerShellt: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>ASA-feladatok létrehozása az Edge-ben 
 
Stream Analytics-feladatok létrehozásához hívja meg a PUT metódust a Stream Analytics API használatával.

|Metódus|URL-cím kérése|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Példa a **curl** használatával történő parancsra:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Példa a JSON-beli kérelem törzsére:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
További információ: [API-dokumentáció](/rest/api/streamanalytics/).  
 
## <a name="publish-edge-package"></a>Edge-csomag közzététele 
 
Ha Stream Analytics feladatot szeretne közzétenni IoT Edge, hívja meg a POST metódust a Edge Package publish API használatával.

|Metódus|URL-cím kérése|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Ez az aszinkron művelet a 202-es állapotot adja vissza, amíg a feladatot nem sikerült közzétenni. A hely válaszának fejléce tartalmazza a folyamat állapotának lekéréséhez használt URI-t. A folyamat futása közben a Location fejlécben lévő URI-hívás a 202-es állapotot adja vissza. A folyamat befejeződése után a Location fejlécben lévő URI a 200-es állapotot adja vissza. 

Példa az Edge-csomag közzétételi hívására a **curl** használatával: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
A POST hívása után egy üres törzstel rendelkező választ kell várnia. Keresse meg a válasz élén található URL-címet, és jegyezze fel további használatra.
 
Példa a válasz vezetője URL-címére:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Várjon egy-két percet, mielőtt futtatja a következő parancsot egy API-hívás létrehozásához a válasz élén található URL-címmel. Ha nem kap 200 választ, próbálja megismételni a parancsot.
 
Példa arra, hogy API-hívást adjon vissza a visszaadott URL-címmel a **curl** használatával:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A válasz tartalmazza azokat az információkat, amelyeket hozzá kell adni az Edge üzembehelyezési parancsfájlhoz. Az alábbi példák azt mutatják be, hogy milyen információkat kell összegyűjtenie, és hova kell hozzáadni a telepítési jegyzékben.
 
Példa a válasz törzsére a sikeres közzététel után:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Telepítési jegyzékfájl mintája: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Az üzembe helyezési jegyzék konfigurálását követően tekintse át a [Azure IoT Edge-modulok üzembe helyezése az Azure CLI-vel üzembe helyezését ismertető témakört](../iot-edge/how-to-deploy-modules-cli.md) .


## <a name="next-steps"></a>Következő lépések 
 
* [IoT Edge-eszközökön futó Azure Stream Analytics](stream-analytics-edge.md)
* [ASA IoT Edge oktatóanyag](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Stream Analytics Edge-feladatok fejlesztése a Visual Studio Tools használatával](stream-analytics-tools-for-visual-studio-edge-jobs.md)