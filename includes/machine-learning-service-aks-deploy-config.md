---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 31d20f4824b034230bc941858e8ecb20cc00b6b2
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348527"
---
A `deploymentconfig.json` dokumentumban szereplő bejegyzések a [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)paramétereit képezik. A következő táblázat ismerteti a JSON-dokumentum entitásai és a metódus paramétereinek megfeleltetését:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `computeType` | NA | A számítási cél. Az AK esetében az értéknek a `aks`következőnek kell lennie:. |
| `autoScaler` | NA | Az automatikus skálázás konfigurációs elemeit tartalmazza. Tekintse meg az autoskálázási táblázatot. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Azt határozza meg, hogy engedélyezi-e az automatikus skálázást a webszolgáltatáshoz. Ha `numReplicas` , = egyéb esetben ,`False`. `0` `True` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | A webszolgáltatás automatikus skálázásakor használandó tárolók minimális száma. Alapértelmezett érték:. `1` |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | A webszolgáltatás automatikus skálázásakor használandó tárolók maximális száma. Alapértelmezett érték:. `10` |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Az autoskálázás milyen gyakran próbálja méretezni ezt a webszolgáltatást. Alapértelmezett érték:. `1` |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | A cél kihasználtsága (az 100-as százalékban kifejezve), amelyet az autoskálázásnak meg kell próbálnia fenntartani a webszolgáltatás számára. Alapértelmezett érték:. `70` |
| `dataCollection` | NA | Az adatgyűjtés konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Azt határozza meg, hogy engedélyezi-e a modell adatgyűjtését a webszolgáltatáshoz. Alapértelmezett érték:. `False` |
| `authEnabled` | `auth_enabled` | Azt határozza meg, hogy engedélyezi-e a hitelesítést a webszolgáltatáshoz. Alapértelmezett érték:. `True` |
| `containerResourceRequirements` | NA | A processzor-és memória-entitások tárolója. |
| &emsp;&emsp;`cpu` | `cpu_cores` | A webszolgáltatás számára lefoglalható CPU-magok száma. Alapértelmezett`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | A webszolgáltatás számára lefoglalható memória mennyisége (GB-ban). Alapértelmezett`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Azt határozza meg, hogy engedélyezi-e Application Insights naplózást a webszolgáltatáshoz. Alapértelmezett érték:. `False` |
| `scoringTimeoutMs` | `scoring_timeout_ms` | A webszolgáltatásnak való pontozási hívások kényszerített időkorlátja. Alapértelmezett érték:. `60000` |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | A webszolgáltatáshoz tartozó csomópontok maximális egyidejű kérelmeinek száma. Alapértelmezett érték:. `1` |
| `maxQueueWaitMs` | `max_request_wait_time` | Az a maximális időtartam, ameddig egy kérelem a 503-es hiba visszaadása előtt a várólistában marad (ezredmásodpercben). Alapértelmezett érték:. `500` |
| `numReplicas` | `num_replicas` | A webszolgáltatás számára lefoglalható tárolók száma. Nincs alapértelmezett érték. Ha ez a paraméter nincs beállítva, az automéretező alapértelmezés szerint engedélyezve van. |
| `keys` | NA | A kulcsok konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`primaryKey` | `primary_key` | A webszolgáltatáshoz használandó elsődleges hitelesítési kulcs |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | A webszolgáltatáshoz használandó másodlagos hitelesítési kulcs |
| `gpuCores` | `gpu_cores` | A webszolgáltatás számára lefoglalható GPU-magok száma. Az alapértelmezett érték 1. Csak az egész szám értékeit támogatja. |
| `livenessProbeRequirements` | NA | Az élettartam mintavételi követelményeinek konfigurációs elemeit tartalmazza. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Milyen gyakran (másodpercben) kell végrehajtani az élettartam-mintavételt. Az alapértelmezett érték 10 másodperc. A minimális érték 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Azon másodpercek száma, ameddig a tároló el lett indítva az élő mintavételek elindítása előtt. Alapértelmezett értéke 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Azon másodpercek száma, amely után az élettartam mintavétele időtúllépést mutat. Az alapértelmezett érték 2 másodperc. A minimális érték 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Az élettartam-mintavétel minimális egymást követő sikerei sikeresnek tekintendők a sikertelenség után. Az alapértelmezett érték 1. A minimális érték 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Ha egy Pod elindul, és az élettartam-mintavétel meghiúsul, a Kubernetes a failureThreshold időt fogja kipróbálni. Az alapértelmezett érték 3. A minimális érték 1. |
| `namespace` | `namespace` | Az a Kubernetes-névtér, amelyre a webszolgáltatás telepítve van. Akár 63 kisbetűs alfanumerikus ("név elején-kisbetűket", "0"-"9") és kötőjel ("-") karakter. Az első és az utolsó karakter nem lehet kötőjel. |

A következő JSON a parancssori felülettel való használatra példaként használható telepítési konfiguráció:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
