---
title: Diagnosztikai naplók megtekintése a Azure Data Lake Storage Gen1hoz | Microsoft Docs
description: 'Ismerje meg, hogyan lehet beállítani és elérni a diagnosztikai naplókat a Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 479b227a9144604d3bd0116a60de751189376b5f
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85511457"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Diagnosztikai naplók elérése Azure Data Lake Storage Gen1hoz
Megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást a Azure Data Lake Storage Gen1-fiókjához, és hogyan tekintheti meg a fiókjához gyűjtött naplókat.

A szervezetek a Azure Data Lake Storage Gen1-fiókjuk számára lehetővé tehetik a diagnosztikai naplózást, hogy adathozzáférési naplókat gyűjtsenek, amelyek olyan információkat biztosítanak, mint például az adatokhoz hozzáférő felhasználók listája, az adatok elérésének gyakorisága, a fiókban tárolt adatok mennyiségét stb. Ha ez a beállítás engedélyezve van, a diagnosztika és/vagy a kérések a lehető leghatékonyabban vannak naplózva. A kérelmek és a diagnosztikai naplóbejegyzések is csak akkor jönnek létre, ha a szolgáltatás végpontján kérések történnek.

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage Gen1 fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait.

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Diagnosztikai naplózás engedélyezése a Data Lake Storage Gen1-fiókhoz
1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Nyissa meg Data Lake Storage Gen1-fiókját, és a Data Lake Storage Gen1-fiók paneljén kattintson a **diagnosztikai beállítások**elemre.
3. A **diagnosztikai beállítások** panelen kattintson a **diagnosztika bekapcsolása**elemre.

    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Diagnosztikai naplók engedélyezése")

3. A **diagnosztika beállításai** panelen végezze el a következő módosításokat a diagnosztikai naplózás konfigurálásához.
   
    ![Diagnosztikai naplózás engedélyezése](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Diagnosztikai naplók engedélyezése")
   
   * A **név**mezőben adja meg a diagnosztikai napló konfigurációjának értékét.
   * Dönthet úgy, hogy különböző módokon tárolja/dolgozza fel az adatfeldolgozást.
     
        * Válassza ki a **Storage-fiókba való archiválási** lehetőséget a naplók Azure Storage-fiókba való tárolásához. Ezt a lehetőséget akkor kell használni, ha egy későbbi időpontban szeretné archiválni a kötegelt feldolgozásra kerülő adatok archiválását. Ha ezt a beállítást választja, meg kell adnia egy Azure Storage-fiókot, amelybe menteni szeretné a naplókat.
        
        * Válassza ki azt a lehetőséget, hogy az **Event hub** -ba továbbítsa a naplófájlokat az Azure Event hub felé. Ennek a lehetőségnek a használata valószínűleg akkor fog megjelenni, ha az alsóbb rétegbeli feldolgozási folyamattal valós időben szeretné elemezni a bejövő naplókat. Ha ezt a lehetőséget választja, meg kell adnia a használni kívánt Azure Event hub részleteit.

        * Válassza ki a **log Analytics küldésének** lehetőségét, hogy a Azure monitor szolgáltatás használatával elemezze a generált naplókat. Ha ezt a beállítást választja, meg kell adnia a Log Analytics munkaterület azon részleteit, amelyeket a naplózási elemzés végrehajtásakor használni fog. A Azure Monitor naplók használatával kapcsolatos részletekért tekintse meg a [Azure monitor naplókból összegyűjtött adatok megtekintése és elemzése](../azure-monitor/learn/tutorial-viewdata.md) című témakört.
     
   * Itt adhatja meg, hogy szeretné-e naplózni a naplókat vagy a kérelmeket, vagy mindkettőt.
   * Itt adhatja meg, hogy hány nap elteltével kell megőrizni az adatmennyiséget. A megőrzés csak akkor érvényes, ha Azure Storage-fiókot használ a naplófájlok archiválásához.
   * Kattintson a **Save** (Mentés) gombra.

Miután engedélyezte a diagnosztikai beállításokat, megtekintheti a naplókat a **diagnosztikai naplók** lapon.

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>A Data Lake Storage Gen1-fiókhoz tartozó diagnosztikai naplók megtekintése
A Data Lake Storage Gen1-fiók naplózási adatait kétféleképpen tekintheti meg.

* A Data Lake Storage Gen1 Fiókbeállítások nézetből
* Az Azure Storage-fiókból, ahol az adatok tárolódnak

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>A Data Lake Storage Gen1 beállítások nézet használata
1. A **Data Lake Storage Gen1 Fiókbeállítások panelen** kattintson a **diagnosztikai naplók**elemre.
   
    ![Diagnosztikai naplók megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Diagnosztikai naplók megtekintése") 
2. A **diagnosztikai naplók** panelen tekintse meg a **naplók és a** **naplók**szerint kategorizált naplókat.
   
   * A kérelmek naplói rögzítik a Data Lake Storage Gen1 fiókon végzett összes API-kérést.
   * A naplók hasonlóak a kérelmek naplóihoz, de a Data Lake Storage Gen1 fiókon végrehajtott műveletek sokkal részletesebb részletezését teszik lehetővé. Előfordulhat például, hogy egy feltöltési API-hívás a kérelmek naplófájljaiban több "Hozzáfűzés" műveletet eredményezhet a naplókban.
3. A naplók letöltéséhez kattintson a **Letöltés** hivatkozásra az egyes naplók bejegyzésein.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A napló adatait tartalmazó Azure Storage-fiókból
1. Nyissa meg a naplózáshoz Data Lake Storage Gen1 társított Azure Storage-fiók panelt, majd kattintson a Blobok elemre. A **blob Service** panelen két tároló látható.
   
    ![Diagnosztikai naplózás megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Diagnosztikai naplók megtekintése")
   
   * A Container- **elemzések – naplók – naplózás** tartalmazza a naplókat.
   * A Container-elemzések **– naplók – a kérelmek** a kérelmek naplóit tartalmazzák.
2. Ezeken a tárolókban a naplók tárolása a következő struktúrán belül történik.
   
    ![Diagnosztikai naplózás megtekintése](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Diagnosztikai naplók megtekintése")
   
    A napló teljes elérési útja például a következő lehet`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Hasonlóképpen lehetséges, hogy a kérelem naplójának teljes elérési útja`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>A naplózási adat szerkezetének megismerése
A naplózási és a kérési naplók JSON formátumúak. Ebben a szakaszban a JSON szerkezetét tekintjük át a kérelem és a naplók számára.

### <a name="request-logs"></a>Kérelmek naplói
Íme egy minta bejegyzés a JSON-formátumú kérelem naplójában. Mindegyik blob egyetlen **, a log** objektumokat tartalmazó tömböt tartalmazó főobjektummal rendelkezik.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Kérelem naplózási sémája
| Name | Típus | Description |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (UTC) |
| resourceId |Sztring |Annak az erőforrásnak az azonosítója, amelyre a műveletet végezték |
| category |Sztring |A napló kategóriája. Például: **kérelmek**. |
| operationName |Sztring |A naplózott művelet neve. Például: getfilestatus. |
| resultType |Sztring |A művelet állapota, például 200. |
| callerIpAddress |Sztring |A kérést készítő ügyfél IP-címe |
| correlationId |Sztring |A kapcsolódó naplóbejegyzések csoportba foglalásához használható napló azonosítója |
| identity |Objektum |A naplót létrehozó identitás |
| properties |JSON |Részletekért lásd alább |

#### <a name="request-log-properties-schema"></a>Kérelem naplójának tulajdonságai sémája
| Name | Típus | Description |
| --- | --- | --- |
| HttpMethod |Sztring |A művelethez használt HTTP-metódus. Például: GET. |
| Elérési út |Sztring |A művelet végrehajtásának elérési útja |
| RequestContentLength |int |A HTTP-kérelem tartalmának hossza |
| Ügyfélkérelem |Sztring |A kérelmet egyedileg azonosító azonosító |
| StartTime |Sztring |Az az idő, amikor a kiszolgáló megkapta a kérést |
| EndTime |Sztring |Az az idő, amikor a kiszolgáló választ küldött |

### <a name="audit-logs"></a>Naplók
Íme egy minta bejegyzés a JSON-formátumú naplóban. Mindegyik blob egyetlen **, a log** objektumokat tartalmazó tömböt tartalmazó root objektummal rendelkezik

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Auditnapló sémája
| Name | Típus | Description |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (UTC) |
| resourceId |Sztring |Annak az erőforrásnak az azonosítója, amelyre a műveletet végezték |
| category |Sztring |A napló kategóriája. Például: **naplózás**. |
| operationName |Sztring |A naplózott művelet neve. Például: getfilestatus. |
| resultType |Sztring |A művelet állapota, például 200. |
| resultSignature |Sztring |További részletek a műveletről. |
| correlationId |Sztring |A kapcsolódó naplóbejegyzések csoportba foglalásához használható napló azonosítója |
| identity |Objektum |A naplót létrehozó identitás |
| properties |JSON |Részletekért lásd alább |

#### <a name="audit-log-properties-schema"></a>Naplózási napló tulajdonságai sémája
| Name | Típus | Description |
| --- | --- | --- |
| StreamName |Sztring |A művelet végrehajtásának elérési útja |

## <a name="samples-to-process-the-log-data"></a>Minták a naplók feldolgozásához
A naplók Azure Data Lake Storage Gen1ról Azure Monitor naplókba való küldésekor (lásd: [Azure monitor naplókból gyűjtött adatok megtekintése vagy elemzése](../azure-monitor/learn/tutorial-viewdata.md) Azure monitor naplók használatával kapcsolatos részletekért), a következő lekérdezés egy olyan táblázatot ad vissza, amely tartalmazza a felhasználói megjelenítendő nevek listáját, az események időpontját, valamint az esemény időpontját a vizualizációs diagrammal együtt. Egyszerűen módosítható a felhasználói GUID azonosítók vagy egyéb attribútumok megjelenítéséhez:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 a naplófájlok feldolgozásának és elemzésének módját mutatja be. A minta a következő címen található: [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Lásd még
* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)

