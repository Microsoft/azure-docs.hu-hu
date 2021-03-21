---
title: Útmutató az Azure Monitor REST API-hoz
description: Kérelmek hitelesítése és a Azure Monitor REST API használata az elérhető metrika-definíciók és metrikai értékek lekéréséhez.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: has-adal-ref
ms.openlocfilehash: a7cd6ff7c0c3b5d4bee859ef288f16673ebe0835
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033078"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Útmutató az Azure Monitor REST API-hoz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan végezheti el a hitelesítést, hogy a kód használhassa a [Microsoft Azure Monitor REST API-referenciát](/rest/api/monitor/).

A Azure Monitor API lehetővé teszi az elérhető alapértelmezett metrikai definíciók, részletességi és metrikai értékek programozott beolvasását. Az adattárak külön adattárba menthetők, például Azure SQL Database, Azure Cosmos DB vagy Azure Data Lake. A további elemzések igény szerint elvégezhetők.

A különböző metrikai adatpontok használata mellett a figyelő API is lehetővé teszi a riasztási szabályok listázását, a tevékenységek naplóinak megtekintését és sok más további munkát. Az elérhető műveletek teljes listáját a [Microsoft Azure Monitor REST API-referenciája](/rest/api/monitor/)című témakörben tekintheti meg.

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor kérelmek hitelesítése

Az első lépés a kérelem hitelesítése.

A Azure Monitor API-val végrehajtott összes feladat a Azure Resource Manager hitelesítési modellt használja. Ezért az összes kérelmet hitelesíteni kell Azure Active Directory (Azure AD). Az ügyfélalkalmazás hitelesítésének egyik módja az, ha létrehoz egy Azure AD-szolgáltatásnevet, és lekéri a hitelesítési (JWT) jogkivonatot. Az alábbi példa azt mutatja be, hogyan hozható létre egy Azure AD-szolgáltatásnév a PowerShell használatával. Részletesebb útmutatásért tekintse meg a dokumentációt, amely a [Azure PowerShell használatával hoz létre egy egyszerű szolgáltatásnevet az erőforrásokhoz való hozzáféréshez](/powershell/azure/create-azure-service-principal-azureps). [A Azure Portal használatával is létrehozhat egyszerű szolgáltatásnevet](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

A Azure Monitor API lekérdezéséhez az ügyfélalkalmazás a korábban létrehozott egyszerű szolgáltatást használja a hitelesítéshez. Az alábbi PowerShell-szkript egy megközelítést mutat be, amely a [Active Directory-hitelesítési tár](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) használatával szerzi be a JWT hitelesítési tokent. Az JWT token egy HTTP-engedélyezési paraméter részeként kerül átadásra a Azure Monitor REST API kérelmekben.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

A hitelesítés után a lekérdezések végrehajthatók a Azure Monitor REST API. Két hasznos lekérdezés létezik:

1. Erőforrás metrika-definícióinak listázása
2. Metrika értékeinek beolvasása

> [!NOTE]
> Az Azure REST API való hitelesítéssel kapcsolatos további információkért tekintse meg az [azure REST API-referenciát](/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Metrika-definíciók beolvasása (többdimenziós API)

A szolgáltatáshoz elérhető metrikák listájának eléréséhez használja a [Azure monitor metrikai definíciókat REST API](/rest/api/monitor/metricdefinitions) .

**Metódus**: Get

**Kérelem URI-ja**: https: \/ \/ Management.Azure.com/Subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{resourceType}* / *{resourcename}*/Providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Ha például egy Azure Storage-fiók metrika-definícióit szeretné lekérni, a kérelem a következőképpen fog megjelenni:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Ha a többdimenziós Azure Monitor REST API metrikák használatával szeretné lekérni a metrika-definíciókat, használja az "2018-01-01" API-verziót.
>
>

Az eredményül kapott JSON-válasz a következő példához hasonló lenne: (vegye figyelembe, hogy a második metrika dimenziókkal rendelkezik)

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Dimenzió értékének beolvasása (többdimenziós API)

Ha a rendelkezésre álló metrika-definíciók ismertek, lehet, hogy vannak olyan mérőszámok, amelyek dimenziókkal rendelkeznek. A metrika lekérdezése előtt érdemes lehet felderíteni, hogy mi az értékek tartománya a dimenzióban. Ezek alapján a dimenzióértékeket kiválasztva szűrheti vagy szegmentálhatja a mérőszámokat a dimenzió értékei alapján a metrikák lekérdezése során.  Ennek eléréséhez használja a [Azure monitor metrikák REST API](/rest/api/monitor/metrics) .

A metrika neve "value" (nem a "localizedValue") az összes szűrési kérelemhez. Ha nincsenek megadva szűrők, a rendszer az alapértelmezett metrikát adja vissza. Ennek az API-nak a használata lehetővé teszi, hogy az egyik dimenzióban legyen helyettesítő szűrő.

> [!NOTE]
> A dimenzió értékének a Azure Monitor REST API használatával történő beolvasásához használja az "2018-01-01" értéket API-verzióként.
>
>

**Metódus**: Get

**Kérelem URI-ja**: https \: //Management.Azure.com/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Resource-Group-Name}*/Providers/*{erőforrás-szolgáltató-névtér}* / *{erőforrás-típus*} / *{Resource-Name}*/Providers/Microsoft.Insights/Metrics? metricnames =*{metrikus}*&TimeSpan =*{kezdő időpont/Befejezés}*&$Filter =*{Filter}*&resultType = metaadatok&API-Version =*{apiVersion}*

Ha például le szeretné kérdezni a "Transactions" metrika "API-név dimenziója" számára kibocsátott dimenzióértékeket, ahol a GeoType dimenzió = "elsődleges" érték szerepel a megadott időtartományban, a kérelem a következő lesz:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lenne:

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Metrikai értékek beolvasása (többdimenziós API)

Ha a rendelkezésre álló metrika-definíciók és a lehetséges dimenzió értékek ismertek, akkor lehetséges, hogy beolvassa a kapcsolódó metrikai értékeket.  Ennek eléréséhez használja a [Azure monitor metrikák REST API](/rest/api/monitor/metrics) .

A metrika neve "value" (nem a "localizedValue") az összes szűrési kérelemhez. Ha nincsenek megadva dimenzió szűrők, a rendszer az összegzett összesített metrikát adja vissza. Ha egy metrika-lekérdezés több idősor ad vissza, akkor a "Top" és a "OrderBy" lekérdezési paraméterekkel lehet visszaadni a idősor korlátozott sorrendű listáját.

> [!NOTE]
> A többdimenziós metrika értékének a Azure Monitor REST API használatával történő lekéréséhez használja az "2018-01-01" értéket API-verzióként.
>
>

**Metódus**: Get

**Kérelem URI-ja**: https: \/ /Management.Azure.com/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Resource-Group-Name}*/Providers/*{erőforrás-szolgáltató-névtér}* / *{erőforrás-típus}* / *{Resource-Name}*/Providers/Microsoft.Insights/Metrics? metricnames =*{metrika}*&TimeSpan =*{kezdő időpont/Befejezés}*&$Filter =*{Filter}*&intervallum =*{timeGrain}*&aggregációs =*{aggreation}*&API-Version =*{apiVersion}*

Ha például az első 3 API-t csökkenő értékben szeretné lekérdezni, a "tranzakciók" száma egy 5 perces tartományban, ahol a GeotType "elsődleges" volt, a kérelem a következő lesz:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lenne:

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Metrika-definíciók beolvasása

A szolgáltatáshoz elérhető metrikák listájának eléréséhez használja a [Azure monitor metrikai definíciókat REST API](/rest/api/monitor/metricdefinitions) .

**Metódus**: Get

**Kérelem URI-ja**: https: \/ \/ Management.Azure.com/Subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/Providers/*{resourceProviderNamespace}* / *{resourceType}* / *{resourcename}*/Providers/Microsoft.Insights/metricDefinitions? API-Version =*{apiVersion}*

Ha például egy Azure logikai alkalmazás metrika-definícióit szeretné lekérni, a kérelem a következőképpen fog megjelenni:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> A metrikai definíciók a Azure Monitor REST API használatával történő beolvasásához használja az "2016-03-01" API-verziót.
>
>

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lenne:

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

További információkért tekintse meg az [erőforrás metrikai definícióinak listáját Azure Monitor REST API](/rest/api/monitor/metricdefinitions) dokumentációban.

## <a name="retrieve-metric-values"></a>Metrikai értékek beolvasása

Ha a rendelkezésre álló metrika-definíciók ismertek, akkor lehetséges, hogy beolvassa a kapcsolódó metrikai értékeket. Használja a metrika "value" (nem a "localizedValue") nevét a szűrési kérelmekhez (például a "CpuTime" és a "requests" metrikai adatpontok lekérése). Ha nincsenek megadva szűrők, a rendszer az alapértelmezett metrikát adja vissza.

> [!NOTE]
> A metrikai értékek a Azure Monitor REST API használatával történő beolvasásához használja az "2016-09-01" értéket API-verzióként.
>
>

**Metódus**: `GET`

**Kérelem URI-ja**: `https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}/providers/microsoft.insights/metrics?$filter={filter}&api-version={apiVersion}`

Ha például a megadott időtartományhoz tartozó RunsSucceeded metrikai adatpontokat szeretné lekérni, és egy órán belül egy órát, akkor a kérelem a következő lesz:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lenne:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Több adat vagy összesítési pont lekéréséhez adja hozzá a metrika-definíciók nevét és az összesítési típusokat a szűrőhöz, az alábbi példában látható módon:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Az eredményül kapott JSON-válasz törzse a következő példához hasonló lenne:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>ARMClient használata

Egy további módszer a [ARMClient](https://github.com/projectkudu/armclient) használata a Windows rendszerű gépen. A ARMClient automatikusan kezeli az Azure AD-hitelesítést (és a létrejövő JWT-jogkivonatot). A következő lépések körvonalazzák a ARMClient használatát a metrikus adatok beolvasásához:

1. A [chocolatey](https://chocolatey.org/) és a [ARMClient](https://github.com/projectkudu/armclient)telepítése.
2. A terminál ablakban írja be a *armclient.exe login nevet*. Ezzel megkéri, hogy jelentkezzen be az Azure-ba.
3. Írja be a következőt: *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.Insights/metricdefinitions? API-Version = 2016-03-01*
4. Írja be a következőt: *ARMCLIENT Get [your_resource_id]/Providers/Microsoft.Insights/Metrics? API-Version = 2016-09-01*

Ha például egy adott logikai alkalmazás metrika-definícióit szeretné lekérni, adja ki a következő parancsot:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Erőforrás-azonosító lekérése

A REST API használatával valóban megismerheti az elérhető metrikák definícióit, részletességét és kapcsolódó értékeit. Ez az információ az [Azure felügyeleti könyvtárának](/previous-versions/azure/reference/mt417623(v=azure.100))használatakor hasznos.

Az előző kód esetében a használandó erőforrás-azonosító a kívánt Azure-erőforrás teljes elérési útja. Ha például egy Azure-webalkalmazásra szeretne lekérdezni, az erőforrás-azonosító a következő lesz:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Az alábbi lista néhány példát tartalmaz a különböző Azure-erőforrások erőforrás-azonosító formátumára:

* **IoT hub** -/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Erőforrás-csoport-név}*/Providers/Microsoft.Devices/IotHubs/*{IoT-hub-Name}*
* **Rugalmas SQL-készlet** –*/Subscriptions/{előfizetés-azonosító}*/resourceGroups/*{Erőforrás-csoport-név}*/Providers/Microsoft.SQL/Servers/*{Pool-db}*/elasticpools/*{SQL-Pool-Name}*
* **SQL Database (V12)** –/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Resource-Group-Name}*/Providers/Microsoft.SQL/Servers/*{kiszolgálónév}*/Databases/*{adatbázis neve}*
* **Service Bus** -/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Erőforrás-csoport-név}*/Providers/Microsoft.ServiceBus/*{névtér}* / *{ServiceBus-Name}*
* **Virtuálisgép-méretezési** csoportok –/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Erőforrás-csoport-név}*/Providers/Microsoft.Compute/virtualMachineScaleSets/*{VM-Name}*
* **Virtuális gépek** –/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Erőforrás-csoport-név}*/Providers/Microsoft.Compute/virtualMachines/*{VM-Name}*
* **Event Hubs** -/Subscriptions/*{előfizetés-azonosító}*/resourceGroups/*{Erőforrás-csoport-név}*/Providers/Microsoft.EventHub/Namespaces/*{EventHub-Namespace}*

Az erőforrás-azonosító lekérésének alternatív módszerei vannak, beleértve a Azure Erőforrás-kezelő használatát, a kívánt erőforrás megtekintését a Azure Portal, a PowerShell vagy az Azure CLI segítségével.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

A kívánt erőforrás erőforrás-AZONOSÍTÓjának megkereséséhez az egyik hasznos módszer az [Azure erőforrás-kezelő](https://resources.azure.com) eszköz használata. Navigáljon a kívánt erőforráshoz, és tekintse meg a megjelenő azonosítót, ahogy az alábbi képernyőképen is látható:

![ALT "Azure Erőforrás-kezelő"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Az erőforrás-azonosító a Azure Portal is beszerezhető. Ehhez keresse meg a kívánt erőforrást, majd válassza a tulajdonságok lehetőséget. Az erőforrás-azonosító a tulajdonságok szakaszban jelenik meg, ahogy az alábbi képernyőképen látható:

![ALT "erőforrás-azonosító megjelenik a Azure Portal tulajdonságok paneljén"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Az erőforrás-azonosító Azure PowerShell parancsmagok használatával is lekérhető. Ha például egy Azure logikai alkalmazás erőforrás-AZONOSÍTÓját szeretné beolvasni, hajtsa végre az Get-AzureLogicApp parancsmagot, ahogy az alábbi példában látható:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Az eredménynek az alábbi példához hasonlónak kell lennie:

```output
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretné lekérni az Azure Storage-fiók erőforrás-AZONOSÍTÓját, hajtsa végre a `az storage account show` parancsot az alábbi példában látható módon:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Az eredménynek az alábbi példához hasonlónak kell lennie:

```json
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps még nem érhető el az Azure CLI-n keresztül, így az előző példában egy Azure Storage-fiók jelenik meg.
>
>

## <a name="retrieve-activity-log-data"></a>Műveletnapló-adatlekérdezés

A mérőszám-definíciók és a kapcsolódó értékek mellett a Azure Monitor REST API is használható az Azure-erőforrásokkal kapcsolatos további érdekes információk lekéréséhez. Példaként lehetséges a [tevékenységi naplóban](/rest/api/monitor/activitylogs) tárolt adatlekérdezés. A következő példák a Azure Monitor REST API használják a műveletnapló lekérdezéséhez.

Tevékenységek naplóinak beolvasása szűrővel:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Tevékenységek naplóinak beolvasása szűrővel és kijelölés:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenységek naplóinak beolvasása a Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Tevékenységek naplóinak beolvasása szűrés vagy kiválasztás nélkül:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [figyelés áttekintését](../overview.md).
* A [támogatott metrikák megtekintése Azure monitor](./metrics-supported.md).
* Tekintse át a [Microsoft Azure figyelő REST API referenciát](/rest/api/monitor/).
* Tekintse át az [Azure felügyeleti könyvtárát](/previous-versions/azure/reference/mt417623(v=azure.100)).