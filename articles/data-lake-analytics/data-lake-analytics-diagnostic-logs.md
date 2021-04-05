---
title: Diagnosztikai naplók engedélyezése és megtekintése Azure Data Lake Analytics
description: Ismerje meg, hogyan állíthatja be és érheti el a diagnosztikai naplókat a Azure Data Lake Analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: cd339729f2300ff7e13e7422bf73373b4ce4658e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221009"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics diagnosztikai naplóinak elérése

A diagnosztikai naplózás lehetővé teszi az adathozzáférés-naplózási nyomvonalak gyűjtését. Ezek a naplók olyan információkat tartalmaznak, mint például a következők:

* Azon felhasználók listája, akik hozzáfértek az adatszolgáltatáshoz.
* Az adathozzáférés gyakorisága.
* A fiókban tárolt adatmennyiség mennyisége.

## <a name="enable-logging"></a>Naplózás engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg Data Lake Analytics-fiókját, és válassza a **diagnosztikai naplók** lehetőséget a __figyelő__ szakaszban. Ezután válassza __a diagnosztika bekapcsolása__ elemet.

    ![Képernyőfelvétel: a "diagnosztikai naplók" művelet be van jelölve, és "a diagnosztika bekapcsolása a következő naplók összegyűjtéséhez" kiemelve.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. A __diagnosztikai beállítások__ területen adja meg a naplózási konfiguráció __nevét__ , majd válassza a naplózási beállítások lehetőséget.

    ![Diagnosztika bekapcsolása a naplózási és a kérési naplók összegyűjtéséhez](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Diagnosztikai naplók engedélyezése")

   * A három különböző módon tárolhatja/feldolgozhatja az adatfeldolgozást.

     * A naplók Azure Storage-fiókban való tárolásához válassza az __archiválás egy Storage-fiókba__ lehetőséget. Akkor használja ezt a lehetőséget, ha az adatok archiválását szeretné. Ha ezt a beállítást választja, meg kell adnia egy Azure Storage-fiókot, amelybe menteni szeretné a naplókat.

     * Válassza az adatfolyam küldése az **Event hub** -hoz lehetőséget, hogy továbbítsa az adatnaplót az Azure Event hub szolgáltatásba. Akkor használja ezt a beállítást, ha olyan alsóbb rétegbeli feldolgozási folyamattal rendelkezik, amely valós időben elemzi a bejövő naplókat. Ha ezt a lehetőséget választja, meg kell adnia a használni kívánt Azure Event hub részleteit.

     * Kattintson a __küldés log Analytics__ gombra az adatAzure monitor szolgáltatásba való küldéséhez. Akkor használja ezt a beállítást, ha Azure Monitor naplókat kíván használni a naplók összegyűjtéséhez és elemzéséhez.
   * Itt adhatja meg, hogy szeretné-e naplózni a naplókat vagy a kérelmeket, vagy mindkettőt.  A kérelmek naplója minden API-kérést rögzít. A napló az adott API-kérelem által aktivált összes műveletet rögzíti.

   * A __Storage-fiókba való archiváláshoz__ határozza meg, hogy hány nap elteltével szeretné megőrizni az adatok mennyiségét.

   * Kattintson a __Mentés__ gombra.

        > [!NOTE]
        > A __Save (Mentés__ ) gombra való kattintás előtt ki kell választania a __Storage-fiókba való archiválást__, a __streamet egy Event hub__ -ba, vagy __elküldeni log Analytics__ .

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>A naplózási adatkészletet tartalmazó Azure Storage-fiók használata

1. A naplózási adattárolást tartalmazó blob-tárolók megjelenítéséhez nyissa meg a naplózáshoz Data Lake Analytics használt Azure Storage-fiókot, majd kattintson a __Blobok__ elemre.

   * A Container- **elemzések – naplók – naplózás** tartalmazza a naplókat.
   * A Container-elemzések **– naplók – a kérelmek** a kérelmek naplóit tartalmazzák.

2. A tárolókban a naplófájlok a következő fájl struktúrában tárolódnak:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > Az `##` elérési úthoz tartozó bejegyzések a napló létrehozásának évét, hónapját, napját és óráját tartalmazzák. Data Lake Analytics óránként egy fájlt hoz létre, ezért `m=` mindig tartalmazza a értékét `00` .

    A napló teljes elérési útja például a következő lehet:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Hasonlóképpen a kérelmek naplójának teljes elérési útja a következő lehet:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Naplózási struktúra

A naplózási és a kérési naplók strukturált JSON formátumúak.

### <a name="request-logs"></a>Kérelmek naplói

Íme egy minta bejegyzés a JSON-formátumú kérelem naplójában. Mindegyik blob egyetlen **, a log** objektumokat tartalmazó tömböt tartalmazó főobjektummal rendelkezik.

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Kérelem naplózási sémája

| Név | Típus | Leírás |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (UTC) |
| resourceId |Sztring |Annak az erőforrásnak az azonosítója, amelyre a művelet került |
| category |Sztring |A napló kategóriája. Például: **kérelmek**. |
| operationName |Sztring |A naplózott művelet neve. Például: GetAggregatedJobHistory. |
| resultType |Sztring |A művelet állapota, például 200. |
| callerIpAddress |Sztring |A kérést készítő ügyfél IP-címe |
| correlationId |Sztring |A napló azonosítója. Ez az érték a kapcsolódó naplóbejegyzések csoportjának csoportosítására használható. |
| identity |Objektum |A naplót létrehozó identitás |
| properties |JSON |A részletekért tekintse meg a következő szakaszt (a kérelem naplójának tulajdonságai sémája). |

#### <a name="request-log-properties-schema"></a>Kérelem naplójának tulajdonságai sémája

| Név | Típus | Leírás |
| --- | --- | --- |
| HttpMethod |Sztring |A művelethez használt HTTP-metódus. Például: GET. |
| Elérési út |Sztring |A művelet végrehajtásának elérési útja |
| RequestContentLength |int |A HTTP-kérelem tartalmának hossza |
| Ügyfélkérelem |Sztring |A kérést egyedileg azonosító azonosító |
| StartTime |Sztring |Az az idő, amikor a kiszolgáló megkapta a kérést |
| EndTime |Sztring |Az az idő, amikor a kiszolgáló választ küldött |

### <a name="audit-logs"></a>Naplók

Íme egy minta bejegyzés a JSON-formátumú naplóban. Mindegyik blob egyetlen **, a log** objektumokat tartalmazó tömböt tartalmazó főobjektummal rendelkezik.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Auditnapló sémája

| Név | Típus | Leírás |
| --- | --- | --- |
| time |Sztring |A napló időbélyegzője (UTC) |
| resourceId |Sztring |Annak az erőforrásnak az azonosítója, amelyre a művelet került |
| category |Sztring |A napló kategóriája. Például: **naplózás**. |
| operationName |Sztring |A naplózott művelet neve. Például: JobSubmitted. |
| resultType |Sztring |A feladatok állapotának (operationName) alállapota. |
| resultSignature |Sztring |További részletek a feladatok állapotáról (operationName). |
| identity |Sztring |A műveletet kérő felhasználó. Például: susan@contoso.com. |
| properties |JSON |A részletekért tekintse meg a következő szakaszt (naplózási napló tulajdonságai sémája). |

> [!NOTE]
> a **resultType** és a **resultSignature** adatokat biztosítanak egy művelet eredményéről, és csak akkor tartalmaznak értéket, ha egy művelet befejeződött. Például csak akkor tartalmaznak értéket, ha a **OperationName** **JobStarted** vagy **JobEnded** értéket tartalmaz.
>
>

#### <a name="audit-log-properties-schema"></a>Naplózási napló tulajdonságai sémája

| Név | Típus | Leírás |
| --- | --- | --- |
| JobId |Sztring |A feladathoz rendelt azonosító |
| JobName |Sztring |A feladatokhoz megadott név |
| JobRunTime |Sztring |A feladatok feldolgozásához használt futtatókörnyezet |
| SubmitTime |Sztring |A feladatok elküldésének időpontja (UTC) |
| StartTime |Sztring |A művelet elindításának időpontja a beküldést követően (UTC) |
| EndTime |Sztring |A feladatok befejezésének időpontja |
| Párhuzamosság |Sztring |A feladatokhoz a küldés során kért Data Lake Analytics egységek száma |

> [!NOTE]
> A **SubmitTime**, a **kezdő időpont**, a **Befejezés** és a **párhuzamosság** a művelettel kapcsolatos információkat biztosít. Ezek a bejegyzések csak akkor tartalmaznak értéket, ha a művelet elindult vagy befejeződött. Például a **SubmitTime** csak akkor tartalmaz értéket, ha a **operationName** értéke **JobSubmitted**.

## <a name="process-the-log-data"></a>A napló adatfeldolgozása

Azure Data Lake Analytics a naplófájlok feldolgozásának és elemzésének módját mutatja be. A minta a következő címen található: [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Következő lépések

[Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
