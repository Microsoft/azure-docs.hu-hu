---
title: Azure Advisor riasztások létrehozása az új javaslatokhoz a Azure Portal használatával
description: Új javaslat Azure Advisor riasztások létrehozása
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 3c51479821914ef34edcd13d8708344169f17aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590116"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Azure Advisor riasztások létrehozása az új javaslatokhoz a Azure Portal használatával 

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat a Azure Advisor új ajánlásaihoz a Azure Portal használatával. 

Ha Azure Advisor észleli az egyik erőforrásra vonatkozó új javaslatot, egy eseményt az [Azure-tevékenység naplójában](../azure-monitor/essentials/platform-logs-overview.md)tárol a rendszer. Az eseményekre vonatkozó riasztásokat a Azure Advisor-specifikus riasztások létrehozási felületének használatával állíthatja be. Kiválaszthat egy előfizetést, és opcionálisan egy erőforráscsoportot is megadhatja azokat az erőforrásokat, amelyekről riasztást szeretne kapni. 

A következő tulajdonságokkal is meghatározhatja a javaslatok típusait:

* Kategória
* Hatás szintje
* Javaslat típusa

Azt is beállíthatja, hogy milyen műveletet hajtson végre a rendszer a riasztás indításakor:  

* Meglévő műveleti csoport kiválasztása
* Új műveleti csoport létrehozása

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

> [!NOTE] 
> Az Advisor-riasztások jelenleg csak a magas rendelkezésre állás, a teljesítmény és a Cost-javaslatok esetében érhetők el. A biztonsági javaslatok nem támogatottak. 

## <a name="create-alert-rule"></a>Riasztási szabály létrehozása
1. A **portálon** válassza a **Azure Advisor** lehetőséget.

    ![Azure Advisor a portálon](./media/advisor-alerts/create1.png)

2. A bal oldali menü **figyelés** területén válassza a **riasztások** lehetőséget. 

    ![Riasztások az Advisorban](./media/advisor-alerts/create2.png)

3. Válassza az **új Advisor-riasztás** lehetőséget.

    ![Új Advisor-riasztás](./media/advisor-alerts/create3.png)

4. A **hatókör** szakaszban válassza ki az előfizetést és opcionálisan azt az erőforráscsoportot, amelyet a riasztásra szeretne kapni. 

    ![Advisor riasztási hatóköre](./media/advisor-alerts/create4.png)

5. A **feltétel** szakaszban válassza ki a riasztás konfigurálásához használni kívánt módszert. Ha szeretne riasztást kapni egy adott kategória és/vagy hatás szintjére vonatkozó javaslatokról, válassza a **Kategória és a hatás szintjét**. Ha egy bizonyos típusra vonatkozó javaslatra vonatkozóan szeretne riasztást kapni, válassza a **javaslat típusa** lehetőséget.

    ![Azure Advisor riasztási feltétel](./media/advisor-alerts/create5.png)

6. A kiválasztott beállítástól függően megadhatja a feltételt. Ha minden javaslatot szeretne, hagyja üresen a fennmaradó mezőket. 

    ![Advisor riasztási műveleti csoport](./media/advisor-alerts/create6.png)

7. A **műveleti csoportok** szakaszban válassza a **meglévő hozzáadása** elemet a már létrehozott műveleti csoport használatához, vagy válassza az **új létrehozása** lehetőséget egy új [műveleti csoport](../azure-monitor/alerts/action-groups.md)beállításához. 

    ![Advisor-riasztás meglévő hozzáadása](./media/advisor-alerts/create7.png)

8. A riasztás részletei szakaszban adja meg a riasztás nevét és rövid leírását. Ha azt szeretné, hogy a riasztás engedélyezve legyen, hagyja a **szabály engedélyezése** beállítást az **Igen** értékre a létrehozás kiválasztása után. Ezután válassza ki azt az erőforráscsoportot, amelybe menteni szeretné a riasztást. Ez nem befolyásolja a javaslat célcsoport-hatókörét. 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="A riasztás részletei szakasz képernyőképe.":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Ajánlási riasztások konfigurálása webhook használatára
Ebből a szakaszból megtudhatja, hogyan konfigurálhat Azure Advisor riasztásokat arra, hogy a meglévő rendszereken webhookokon keresztül küldje el a javaslati adatait. 

Beállíthat riasztásokat, amelyekről értesítést kaphat, ha új tanácsadói javaslattal rendelkezik az egyik erőforráson. Ezek a riasztások e-mailben vagy SMS-ben értesítik Önt, de a meglévő rendszerekkel való integrálására is használhatók webhook használatával. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Az Advisor javaslat riasztási hasznos adatai
Ha az Advisor-riasztásokat webhook használatával szeretné integrálni a saját rendszereibe, meg kell elemezni az értesítésből küldött JSON-adattartalmat. 

A riasztáshoz tartozó műveleti csoport beállításakor kiválaszthatja, hogy szeretné-e használni a Common Alert sémát. Ha a gyakori riasztási sémát választja, az adattartalom a következőképpen fog kinézni: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Ha nem használja a közös sémát, az adattartalom a következőhöz hasonlóan néz ki: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

Bármelyik sémában azonosíthatja az Advisor-javaslatok eseményeit, ha **eventSource** keres, `Recommendation` és a **operationName** `Microsoft.Advisor/recommendations/available/action` .

Néhány további fontos mező, amelyet érdemes használni: 

* *alertTargetIDs* (a közös sémában) vagy *resourceId* (örökölt séma)
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>A riasztások kezelése 

A Azure Advisorban szerkesztheti, törölheti vagy letilthatja és engedélyezheti a javaslatok riasztásait. 

1. A **portálon** válassza a **Azure Advisor** lehetőséget.

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="A Azure Advisor kijelölve látható Azure Portal menü képernyőképe.":::

2. A bal oldali menü **figyelés** területén válassza a **riasztások** lehetőséget.

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Képernyőkép a Azure Portal menüjéről, amelyen a riasztások ki vannak választva.":::

3. A riasztások szerkesztéséhez kattintson a riasztás nevére a riasztás megnyitásához, és szerkessze a szerkeszteni kívánt mezőket.

4. A riasztások törléséhez, engedélyezéséhez vagy letiltásához kattintson a sor végén található ellipszisre, majd válassza ki a végrehajtani kívánt műveletet.
 

## <a name="next-steps"></a>Következő lépések
- [Tekintse át a tevékenységek naplójának riasztásait](../azure-monitor/alerts/alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.
- További információ a [műveleti csoportokról](../azure-monitor/alerts/action-groups.md).
