---
title: Oktatóanyag – Azure szinapszis Spark alkalmazási szintű metrikáinak összekötése és figyelése
description: Oktatóanyag – megtudhatja, hogyan integrálhatja meglévő helyszíni Prometheus-kiszolgálóját az Azure szinapszis-munkaterülettel a közel valós idejű Azure Spark alkalmazás-metrikához a szinapszis Prometheus-összekötő használatával.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: d22975199eedae353f2dc12588671ae4b54c85ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109318"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Oktatóanyag: az Azure szinapszis Spark alkalmazási szintű metrikáinak összekötése és figyelése

## <a name="overview"></a>Áttekintés

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a meglévő helyszíni Prometheus-kiszolgálót az Azure szinapszis-munkaterülettel a közel valós idejű Azure Spark alkalmazás-metrikák használatával a szinapszis Prometheus-összekötővel. 

Ez az oktatóanyag az Azure szinapszis REST mérőszámok API-jait is bemutatja. A REST API-kon keresztül beolvashatja a Spark Application mérőszámait, hogy saját monitorozási és diagnosztikai eszközkészletet hozzon létre, vagy integrálja azokat a megfigyelési rendszerekkel.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Az Azure szinapszis Prometheus-összekötő használata a helyszíni Prometheus-kiszolgálókhoz

Az [Azure szinapszis Prometheus-összekötő](https://github.com/microsoft/azure-synapse-spark-metrics) egy nyílt forráskódú projekt. A szinapszis Prometheus-összekötő egy file-alapú szolgáltatás-felderítési módszert használ, amely a következőket teszi lehetővé:
 - Hitelesítés a szinapszis munkaterületen egy HRE egyszerű szolgáltatásnév használatával.
 - Munkaterület Apache Spark alkalmazások listájának beolvasása. 
 - A Spark-alkalmazás metrikáinak lekérése a Prometheus-fájl alapú konfiguráción keresztül. 

### <a name="1-prerequisite"></a>1. előfeltétel

Rendelkeznie kell egy linuxos virtuális gépen üzembe helyezett Prometheus-kiszolgálóval.

### <a name="2-create-a-service-principal"></a>2. egyszerű szolgáltatásnév létrehozása

Ha az Azure szinapszis Prometheus-összekötőt szeretné használni a helyszíni Prometheus-kiszolgálón, kövesse az alábbi lépéseket egy egyszerű szolgáltatásnév létrehozásához.

#### <a name="21-create-a-service-principal"></a>2,1 egyszerű szolgáltatásnév létrehozása:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Jegyezze fel a appId, a jelszót és a bérlő AZONOSÍTÓját.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2 megfelelő engedélyeket adhat hozzá a fenti lépésben létrehozott egyszerű szolgáltatáshoz.

![képernyőfelvétel engedélyezési srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Jelentkezzen be az [Azure szinapszis Analytics-munkaterületre](https://web.azuresynapse.net/) a szinapszis-rendszergazdaként

2. A szinapszis Studióban a bal oldali ablaktáblán válassza a **kezelés > hozzáférés-vezérlés**

3. **Szerepkör-hozzárendelés hozzáadásához** kattintson a bal felső sarokban található Hozzáadás gombra.

4. A hatókör területen válassza a **munkaterület** lehetőséget.

5. A szerepkörhöz válassza a **szinapszis számítási operátor** elemet.

6. A felhasználó kiválasztása beállításnál adja meg a **<service_principal_name>** , majd kattintson a szolgáltatásra

7. Kattintson az **alkalmaz** gombra (várjon 3 percet, amíg az engedély érvénybe lép.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Töltse le az Azure szinapszis Prometheus-összekötőt

A parancsok segítségével telepítse az Azure szinapszis Prometheus-összekötőt.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. konfigurációs fájl létrehozása az Azure szinapszis-munkaterületekhez

Hozzon létre egy config. YAML fájlt a konfigurációs mappában, és töltse ki a következő mezőket: workspace_name, tenant_id, service_principal_name és service_principal_password.
A YAML konfigurációban több munkaterületet is hozzáadhat.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. frissítse a Prometheus-konfigurációt

Adja hozzá a következő konfigurációs szakaszt a Prometheus scrape_configban, és cserélje le a <your_workspace_name> a munkaterület nevére és a <path_to_synapse_connector> a klónozott szinapszis-Prometheus-Connector mappára

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Indítsa el az összekötőt a Prometheus-kiszolgáló virtuális gépén.

A következő módon indítson el egy összekötő kiszolgálót a Prometheus-kiszolgáló virtuális gépén.

```
python main.py
```

Várjon néhány másodpercig, és az összekötőnek működnie kell. És láthatja a "szinapszis-Prometheus-Connector" kifejezést a Prometheus Service Discovery oldalon.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Az Azure szinapszis Prometheus vagy a REST metrika API-k használata a metrikák adatainak gyűjtéséhez

### <a name="1-authentication"></a>1. hitelesítés
Hozzáférési token beszerzéséhez használhatja az ügyfél-hitelesítő adatok folyamatát. A metrikák API eléréséhez be kell szereznie egy Azure AD hozzáférési jogkivonatot az egyszerű szolgáltatásnév számára, amely megfelelő jogosultságokkal rendelkezik az API-k eléréséhez.

| Paraméter     | Kötelező | Leírás                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Igaz     | Az Azure egyszerű szolgáltatásnév (alkalmazás) bérlői azonosítója                                                          |
| grant_type    | Igaz     | Megadja a kért engedélyezési típust. Az ügyfél hitelesítő adatainak megadása esetén a client_credentials értéket kell megadni. |
| client_id     | Igaz     | Az Azure Portal vagy az Azure CLI-ben regisztrált alkalmazás alkalmazás (egyszerű szolgáltatásnév) azonosítója.        |
| client_secret | Igaz     | Az alkalmazáshoz generált titok (egyszerű szolgáltatásnév)                                                  |
| erőforrás      | Igaz     | A szinapszis erőforrás URI azonosítójának a következőnek kell lennie: " https://dev.azuresynapse.net "                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

A válasz a következőképpen néz ki:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. az Azure szinapszis munkaterületen futó alkalmazások listázása

A szinapszis-munkaterülethez tartozó Spark-alkalmazások listájának beszerzéséhez kövesse ezt a dokumentumot [figyelés – Spark-feladat beolvasása](/rest/api/synapse/data-plane/monitoring/getsparkjoblist)című témakört.


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. a Spark-alkalmazás metrikáinak gyűjtése a Prometheus vagy a REST API-kkal


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Spark-alkalmazás metrikáinak összegyűjtése a Prometheus API-val

A megadott Spark-alkalmazás legújabb metrikáinak beolvasása a Prometheus API-val

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Paraméter          | Kötelező | Leírás                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Igaz     | A munkaterület-fejlesztési végpont, például: https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Igaz     | A kérelem érvényes API-verziója. Jelenleg a 2019-11-01 – előzetes verzió                    |
| sparkPoolName      | Igaz     | A Spark-készlet neve.                                                                   |
| sessionId          | Igaz     | A munkamenet azonosítója.                                                               |
| sparkApplicationId | Igaz     | Spark-alkalmazás azonosítója                                                                      |

Példa a kérelemre: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Példa a válaszra:

Állapotkód: 200 válasz a következőképpen néz ki:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Spark-alkalmazás metrikáinak összegyűjtése a REST API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Paraméter          | Kötelező | Leírás                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Igaz     | A munkaterület-fejlesztési végpont, például: https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Igaz     | A kérelem érvényes API-verziója. Jelenleg a 2019-11-01 – előzetes verzió                    |
| sparkPoolName      | Igaz     | A Spark-készlet neve.                                                                   |
| sessionId          | Igaz     | A munkamenet azonosítója.                                                               |
| sparkApplicationId | Igaz     | Spark-alkalmazás azonosítója                                                                      |

Kérésminta

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Példa a válasz állapotkódot: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. saját diagnosztikai és figyelési eszközök létrehozása

A Prometheus API és a REST API-k részletes mérőszámokat biztosítanak a futó Spark-alkalmazásról. Az alkalmazással kapcsolatos metrikák adatait a Prometheus API-n és a REST API-kon keresztül is összegyűjtheti. Saját diagnosztikai és figyelési eszközöket hozhat létre, amelyek megfelelőbbek az igényeinek megfelelően.
