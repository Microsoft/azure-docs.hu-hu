---
title: Hozza el első API-hívását a kereskedelmi piactér Analytics-adataihoz való hozzáféréshez
description: Példák arra, hogy megtudja, hogyan használhatja az API-t a kereskedelmi piactér elemzési adatokhoz való hozzáféréshez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 65ee0847e6a59976eec223b68b1f3e0c464674e8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563803"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Hozza el első API-hívását a kereskedelmi piactér Analytics-adataihoz való hozzáféréshez

A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k listáját lásd: API-k a [kereskedelmi piactér elemzési adatokhoz való hozzáféréshez](analytics-available-apis.md). Az első API-hívás megkezdése előtt győződjön meg arról, hogy teljesítette az [előfeltételeket](analytics-prerequisites.md) , hogy programozott módon hozzáférhessenek a kereskedelmi piactér elemzési adataihoz.

## <a name="token-generation"></a>Jogkivonat létrehozása

A módszerek bármelyikének meghívása előtt be kell szereznie egy Azure Active Directory (Azure AD) hozzáférési jogkivonatot. Az Azure AD hozzáférési jogkivonatot az API minden metódusának engedélyezési fejlécébe kell átadnia. A hozzáférési token beszerzése után 60 percet is igénybe vehet, mielőtt lejár. A jogkivonat lejárta után frissítheti a tokent, és továbbra is használhatja az API további meghívásához.

A jogkivonat létrehozásához tekintse meg az alábbi minta-kérelmet. A jogkivonat létrehozásához szükséges három érték:, `clientId` `clientSecret` és `tenantId` . A `resource` paramétert a következőre kell beállítani: `https://graph.windows.net` .

***Példa a kérelemre***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Válasz példa***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

További információ az Azure AD-token alkalmazáshoz való beszerzéséről: az [elemzési adatok elérése az áruházi szolgáltatásokkal](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Programozott API-hívás

Miután megszerezte az Azure AD-tokent az előző szakaszban leírtak szerint, kövesse az alábbi lépéseket az első programozott hozzáférési jelentés létrehozásához.

Az adatok a következő adatkészletekről tölthetők le (Datasetname tulajdonságának értéke):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

A következő részekben példákat láthatunk arra, hogyan lehet programozott módon hozzáférni `OrderId` a ISVOrder adatkészletből.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>1. lépés: REST-hívás létrehozása az adatkészletek beolvasása API használatával

Az API-válasz megadja az adatkészlet nevét, ahonnan letöltheti a jelentést. Az adott adatkészlet esetében az API-válasz az egyéni jelentés sablonjában használható választható oszlopok listáját is megjeleníti. A következő táblázatokat is megtekintheti az oszlopok nevének beolvasásához:

- [Megrendelések részletei tábla](orders-dashboard.md#orders-details-table)
- [Használat részletei tábla](usage-dashboard.md#usage-details-table)
- [Ügyfél részletei tábla](customer-dashboard.md#customer-details-table)
- [Marketplace-információk részleteinek táblázata](insights-dashboard.md#marketplace-insights-details-table)

***Példa a kérelemre***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Válasz példa***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>2. lépés: az egyéni lekérdezés létrehozása

Ebben a lépésben a orders (megrendelések) jelentésben szereplő Order ID-t használjuk a kívánt jelentéshez tartozó egyéni lekérdezés létrehozásához. Az alapértelmezett érték `timespan` , ha nincs megadva a lekérdezésben, hat hónap.

***Példa a kérelemre***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Válasz példa***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

A lekérdezés sikeres végrehajtásakor a `queryId` rendszer létrehoz egy jelentést, amelyet a jelentés létrehozásához kell használnia.

### <a name="step-3-execute-test-query-api"></a>3. lépés: a teszt lekérdezési API végrehajtása

Ebben a lépésben a test Query API-t fogjuk használni a létrehozott lekérdezés első 100 sorához.

***Példa a kérelemre***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Válasz példa***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>4. lépés: a jelentés létrehozása

Ebben a lépésben a korábban létrehozott lehetőséget fogjuk használni a `QueryId` jelentés létrehozásához.

***Példa a kérelemre***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**1. táblázat: példa a kérelemben használt paraméterek leírására**_

| Paraméter | Leírás |
| ------------ | ------------- |
| `Description` | Adja meg az éppen létrejött jelentés rövid leírását. |
| `QueryId` | Ez az az érték, amelyet a rendszer a lekérdezés létrehozásakor `queryId` generált a 2. lépés: egyéni lekérdezés létrehozása című témakörben. |
| `StartTime` | A jelentés első végrehajtásának ideje elindítva. |
| `RecurrenceInterval` | A jelentés létrehozásakor megadott ismétlődési időköz. |
| `RecurrenceCount` | A jelentés létrehozásakor megadott ismétlődések száma. |
| `Format` | A CSV-és TSV-fájlformátumok támogatottak. |
|||

***Válasz példa***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Sikeres végrehajtás esetén a `reportId` rendszer létrehozza a jelentést, amelyet a jelentés letöltésének megadásához kell használnia.

### <a name="step-5-execute-report-executions-api"></a>5. lépés: a jelentések végrehajtásának végrehajtása API

A jelentés biztonságos helyének (URL-címének) beszerzéséhez most végre kell hajtani a jelentés végrehajtásának API-ját.

***Példa a kérelemre***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Válasz példa***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Következő lépések

- Az API-kat kipróbálhatja a [hencegő API URL-címén](https://partneranalytics-api.azure-api.net/analytics/cmp/swagger/index.html) keresztül
- [Programozott hozzáférés paradigma](analytics-programmatic-access.md)