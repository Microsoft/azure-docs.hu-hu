---
title: Metrikák küldése a Azure Monitor metrikus adatbázisnak a REST API használatával
description: Egyéni metrikák küldése az Azure-erőforrásokhoz az Azure Monitor metrikai tárolóba egy REST API használatával
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 8acb3c31a41521eace46e065c06f04e69d97a2e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037217"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Egyéni metrikák küldése az Azure-erőforrásokhoz az Azure Monitor metrikai tárolóba egy REST API használatával

Ebből a cikkből megtudhatja, hogyan küldhet egyéni metrikákat az Azure-erőforrásokhoz az Azure Monitor metrikai tárolóba egy REST API használatával. A metrikák Azure Monitor a standard metrikákkal végezheti el az összes dolgot. Ilyenek például a diagramok, a riasztások és a többi külső eszközre történő útválasztás.  

>[!NOTE]  
>A REST API csak egyéni metrikák küldését engedélyezi az Azure-erőforrásokhoz. A különböző környezetekben vagy a helyszínen lévő erőforrások metrikáinak elküldéséhez használhatja a [Application Insights](../app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Egyszerű szolgáltatás létrehozása és engedélyezése mérőszámok kibocsátásához 

Hozzon létre egy egyszerű szolgáltatásnevet a Azure Active Directory-bérlőben az [egyszerű szolgáltatásnév létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md)című témakörben található utasítások segítségével. 

Vegye figyelembe a következőket a folyamat lépésein: 

- Megadhat bármilyen URL-címet a bejelentkezési URL-cím számára.  
- Hozzon létre egy új ügyfél-titkot ehhez az alkalmazáshoz.  
- Mentse a kulcsot és az ügyfél-azonosítót a későbbi lépésekben való használatra.  

Adja meg az alkalmazás számára az 1. lépés részeként létrehozott alkalmazást, a metrikák figyelését, a metrikák kiosztására jogosult erőforráshoz tartozó engedélyeket. Ha azt tervezi, hogy az alkalmazás használatával több erőforrásra vonatkozó egyéni metrikákat bocsát ki, ezeket az engedélyeket az erőforráscsoport vagy az előfizetés szintjén adhatja meg. 

## <a name="get-an-authorization-token"></a>Engedélyezési jogkivonat beszerzése
Nyisson meg egy parancssort, és futtassa a következő parancsot:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Mentse a hozzáférési jogkivonatot a válaszból.

![Hozzáférési jogkivonat](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>A metrika kibocsátása a REST API használatával 

1. Illessze be a következő JSON-fájlt egy fájlba, és mentse **custommetric.jsként** a helyi számítógépen. Frissítse az Time paramétert a JSON-fájlban: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. A parancssori ablakban tegye közzé a metrika adatait: 
   - **azureRegion**. Meg kell egyeznie annak az erőforrásnak a központi telepítési régiójával, amelynek mérőszámait ki szeretné próbálni. 
   - **resourceID**.  Annak az Azure-erőforrásnak az erőforrás-azonosítója, amellyel a metrikát nyomon követi.  
   - **AccessToken**. Illessze be a korábban beszerzett tokent.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Módosítsa a JSON-fájl időbélyegét és értékeit. 
1. Ismételje meg az előző két lépést néhányszor, így több percig is elvégezheti az adattárolást.

## <a name="troubleshooting"></a>Hibaelhárítás 
Ha a folyamat néhány részével hibaüzenetet kap, vegye figyelembe a következő hibaelhárítási információkat:

1. Azure-erőforrásként nem lehet mérőszámokat kiadni egy előfizetéshez vagy erőforráscsoporthoz. 
1. A 20 percnél régebbi tárolóba nem helyezhető metrika. A metrikai tároló a riasztások és a valós idejű diagramok esetében van optimalizálva. 
2. A dimenziók neveinek számának meg kell egyeznie az értékekkel, és fordítva. Keresse meg az értékeket. 
2. Lehet, hogy mérőszámokat bocsát ki egy olyan régióra, amely nem támogatja az egyéni metrikákat. Lásd: [támogatott régiók](./metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Metrikák megtekintése 

1. Jelentkezzen be az Azure portálra. 

1. A bal oldali menüben válassza a **figyelő** elemet. 

1. A **figyelés** lapon válassza a **metrikák** lehetőséget. 

   ![Metrikák kiválasztása](./media/metrics-store-custom-rest-api/metrics.png) 

1. Módosítsa az összesítési időszakot az **elmúlt 30 percre**.  

1. Az **erőforrás** legördülő menüben válassza ki azt az erőforrást, amellyel a metrikát kibocsátotta.  

1. A **névterek** legördülő menüben válassza a **QueueProcessing** lehetőséget. 

1. A **metrikák** legördülő menüben válassza a **QueueDepth** lehetőséget.  

 
## <a name="next-steps"></a>Következő lépések
- További információ az [Egyéni metrikákkal](./metrics-custom-overview.md)kapcsolatban.

