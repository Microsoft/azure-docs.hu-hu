---
title: Migrálás az Enterprise Reporting API-ból az Azure Resource Manager API-kba
description: Ez a cikk segít megismerni a Reporting API-k és az Azure Resource Manager API-k közötti különbséget. Emellett bemutatja, hogy mire számítson az Azure Resource Manager API-kba történő migráláskor, illetve az új Azure Resource Manager API-kban elérhető új funkciókat is.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 24fbf52c8fd0338537862a54e15e8a249541a701
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635790"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Migrálás az Enterprise Reporting API-ból az Azure Resource Manager API-kba

Ez a cikk segítséget nyújt azon fejlesztők számára, akik az [Enterprise-ügyfeleknek elérhető Azure Reporting API-k](../manage/enterprise-api.md) használatával hoztak létre olyan egyéni megoldásokat, amelyek az Azure Resource Manager API-kba történő migrálást teszik lehetővé a Cost Managementhez. Az újabb Azure Resource Manager API-khoz mostantól általánosan elérhető a szolgáltatásnevek támogatása. Az Azure Resource Manager API-k jelenleg fejlesztés alatt állnak. Fontolja meg az ezekre való migrálást az Enterprise-ügyfeleknek elérhető régebbi Azure Reporting API-k helyett. A régebbi API-k elavultak. Ez a cikk segít megismerni a Reporting API-k és az Azure Resource Manager API-k közötti különbséget. Emellett bemutatja, hogy mire számítson az Azure Resource Manager API-kba történő migráláskor, illetve az új Azure Resource Manager API-kban elérhető új funkciókat is.

## <a name="api-differences"></a>Az API-k eltérései

Az alábbi információk az Enterprise-ügyfeleknek elérhető régebbi Reporting API-k és az újabb Resource Manager API-k közötti eltéréseket ismertetik.

| **Használat** | **Enterprise Agreement API-k** | **Azure Resource Manager API-k** |
| --- | --- | --- |
| Hitelesítés | A Nagyvállalati Szerződés (EA) portálján kiosztott API-kulcs | Azure Active Directory- (Azure AD-) hitelesítés felhasználói jogkivonatok vagy szolgáltatásnevek használatával. Az API-kulcsok helyét szolgáltatásnevek veszik át. |
| Hatókörök és engedélyek | Minden kérés a regisztráció hatókörében található. Az API-kulcs engedély-hozzárendelései határozzák meg, hogy a rendszer az egész regisztrációra, egy részlegre vagy egy adott fiókra vonatkozó adatokat ad vissza. Nincs felhasználói hitelesítés. | A felhasználók vagy a szolgáltatásnevek hozzárendelt hozzáféréssel rendelkeznek a regisztráció, a részleg vagy a fiók hatóköréhez. |
| URI-végpont | https://consumption.azure.com | https://management.azure.com |
| Fejlesztés állapota | Karbantartási módban. Folyamatban van az elavulása. | Aktív fejlesztés alatt |
| Elérhető API-k | Korlátozva a jelenleg elérhetőre | Egyenértékű API-k érthetők el az egyes EA API-k lecseréléséhez. <p> További [Cost Management API-k](/rest/api/cost-management/) is elérhetők, például a következők: <p> <ul><li>Költségvetések</li><li>Riasztások<li>Exportálások</li></ul> |

## <a name="migration-checklist"></a>Migrálási ellenőrzőlista

- Ismerkedjen meg az [Azure Resource Manager REST API-kkal](/rest/api/azure).
- Állapítsa meg, hogy melyik EA API-kat használja, és az [EA API-k új Azure Resource Manager API-kra való leképezését](#ea-api-mapping-to-new-azure-resource-manager-apis) ismertető cikkben tekintse meg, hogy melyik Azure Resource Manager API-kba helyezheti át őket.
- Szolgáltatások engedélyezésének és hitelesítésének konfigurálása az Azure Resource Manager API-khoz
  - Ha még nem használ Azure Resource Manager API-kat, [regisztrálja az ügyfélalkalmazását az Azure AD-vel](/rest/api/azure/#register-your-client-application-with-azure-ad). A regisztráció létrehoz egy szolgáltatásnevet, amelyet az API-k hívásához használhat.
  - Adjon hozzáférést a szolgáltatásnévnek a szükséges hatókörökhöz a lent leírtak alapján.
  - Frissítsen minden programkódot, hogy az [Azure AD-hitelesítést](/rest/api/azure/#create-the-request) használják a szolgáltatásnévhez.
- Tesztelje az API-kat, majd frissítsen minden programkódot úgy, hogy az EA API-hívásokat Azure Resource Manager API-hívásokra cseréli.
- Frissítse a hibakezelést az új hibakódok használatára. Vegye figyelembe a következőket:
  - Az Azure Resource Manager API-k időkorlátja 60 másodperc.
  - Az Azure Resource Manager API-k sebességkorlátozással rendelkeznek. Ez 429-es szabályozási hibát eredményez a sebességet túllépése esetén. Hozza létre saját megoldásait, hogy ne indítson túl sok API-hívást rövid idő alatt.
- Tekintse át az Azure Resource Managerben elérhető többi Cost Management API-t, és mérje fel őket a későbbi használathoz. További információ: [További Azure Cost Management API-k használata](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Szolgáltatásnév-hozzáférés hozzárendelése az Azure Resource Manager API-khoz

Miután létrehoz egy szolgáltatásnevet az Azure Resource Manager API-k programozott módon történő meghívásához, hozzá kell rendelnie a megfelelő engedélyeket, hogy engedélyezze az Azure Resource Manageren, és hogy kéréseket hajthasson végre az Azure Resource Managerben. A különböző forgatókönyvekhez két engedély-keretrendszer érhető el.

### <a name="azure-billing-hierarchy-access"></a>Azure Billing hierarchia-hozzáférés

Ha egyszerű szolgáltatásnév-jogosultságokat szeretne hozzárendelni a vállalati számlázási fiókhoz, részlegekhez vagy a beléptetési fiókok hatóköréhez, tekintse meg a [szerepkörök hozzárendelésének engedélyezése az Azure-ban nagyvállalati szerződés egyszerű szolgáltatásnév](../manage/assign-roles-azure-service-principals.md)

### <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

A szolgáltatásnevek új támogatása kiterjed az Azure-specifikus hatókörökre, például a felügyeleti csoportokra, előfizetésekre és erőforráscsoportokra. Ezekhez a hatókörökhöz közvetlenül az [Azure Portalon](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) vagy az [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role) használatával rendelhet hozzá szolgáltatásnév-engedélyeket.

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>EA API leképezése új Azure Resource Manager API-kra

Az alábbi táblázattal azonosíthatja a jelenleg használt EA API-kat és a helyettük használható helyettesítő Azure Resource Manager API-kat.

| **Forgatókönyv** | **EA API-k** | **Azure Resource Manager API-k** |
| --- | --- | --- |
| Egyenleg összegzése | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Árlista | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – a megegyezés szerinti árakhoz használható <p> [Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) – a kiskereskedelmi árakhoz használható |
| Fenntartott példány adatai | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Fenntartott példány összegzése | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Fenntartott példányra vonatkozó javaslatok | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Fenntartott példány díjai | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>A migrálás részletei API-k szerint

Az alábbi szakaszok a régi API-kérések példáihoz tartozó új, helyettesítő API-példákat mutatják be.

### <a name="balance-summary-api"></a>Balance Summary API

Használja az alábbi kérés URI-kat az új Balance Summary API meghívásakor. A billingAccountId értékeként használja a regisztrációs számot.

#### <a name="supported-requests"></a>Támogatott kérések

[Lekérés a regisztrációhoz](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>A választörzs módosításai

_Régi válasz törzse_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Új válasz törzse_:

Ugyanezek az adatok az új API-válasz `properties` mezőjében érhetők el. Néhány mező nevében előfordulhatnak kisebb módosítások.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Árlista

Használja az alábbi kérés URI-kat az új Price Sheet API meghívásakor.

#### <a name="supported-requests"></a>Támogatott kérések

 Az API-t az alábbi hatókörökkel hívhatja meg:

- Regisztráció: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Előfizetés: `subscriptions/{subscriptionId}`

[_Lekérés az aktuális számlázási időszakra_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Lekérés adott számlázási időszakra_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>A választörzs módosításai

_Régi válasz_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Új válasz_:

A régi adatok az új API-válasz `pricesheets` mezőjében találhatók. Mérési adatokat is tartalmaz.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Fenntartott példány használati adatai

A Microsoft jelenleg nem dolgozik aktívan a szinkronalapú Reservation Details API-kon. Azt javasoljuk, hogy térjen át az újabb, az egyszerű szolgáltatásnevet támogató aszinkron API-hívásmintára a migrálás részeként. Az aszinkron kérések jobban kezelik a nagy mennyiségű adatot, és csökkentik az időtúllépési hibák számát.

#### <a name="supported-requests"></a>Támogatott kérések

Használja az alábbi kérés URI-kat az új Asynchronous Reservation Details API meghívásakor. A regisztrációs számot kell használnia a `billingAccountId` értékeként. Az API-t az alábbi hatókörökkel hívhatja meg:

- Regisztráció: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Mintakérés a foglalás részleteit tartalmazó jelentés létrehozásához

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Mintakérés a jelentéslétrehozás állapotának lekérdezéséhez

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Lekérdezés mintaválasza

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>A választörzs módosításai

A régebbi, szinkronalapú Reservation Details API válasza lent látható.

_Régi válasz_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Új válasz_:

Az új API egy CSV-fájlt hoz létre. Tekintse meg az alábbi fájlmezőket.

| **Régi tulajdonság** | **Új tulajdonság** | **Megjegyzések** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | A példányrugalmasság új tulajdonsága. |
|  | InstanceFlexibilityRatio | A példányrugalmasság új tulajdonsága. |
| instanceId | InstanceName |  |
|  | Erőforrás | Ez egy új tulajdonság. Az érték `None`, `Reservation` vagy `IncludedQuantity`. |
| reservationId | Foglalásazonosító |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Fenntartott példány kihasználtságának összegzése

Használja az alábbi kérés URI-kat az új Reservation Summaries API meghívásakor.

#### <a name="supported-requests"></a>Támogatott kérések

 Hívja meg az API-t az alábbi hatókörökkel:

- Regisztráció: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_A foglalás összegzésének napi lekérése_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_A foglalás összegzésének havi lekérése_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>A választörzs módosításai

_Régi válasz_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Új válasz_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Fenntartott példányra vonatkozó javaslatok

Használja az alábbi kérés URI-kat az új Reservation Recommendations API meghívásakor.

#### <a name="supported-requests"></a>Támogatott kérések

 Hívja meg az API-t az alábbi hatókörökkel:

- Regisztráció: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Előfizetés: `subscriptions/{subscriptionId}`
- Erőforráscsoportok: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Javaslatok lekérése_](/rest/api/consumption/reservationrecommendations/list)

A megosztott és az önálló hatókörre vonatkozó javaslatok is ezzel az API-val érhetők el. A hatókörre is szűrhet nem kötelező API-paraméterként.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>A választörzs módosításai

A megosztott és az önálló hatókörökre vonatkozó javaslatok egyetlen API-ban vannak összevonva.

_Régi válasz_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Új válasz_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Fenntartott példány díjai

Használja az alábbi kérés URI-kat az új Reserved Instance Charges API meghívásakor.

#### <a name="supported-requests"></a>Támogatott kérések

[_Foglalás költségeinek lekérése dátumtartomány szerint_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>A választörzs módosításai

_Régi válasz_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Új válasz_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>További Cost Management API-k használata

Miután elvégezte a migrálást az Azure Resource Manager API-kba a meglévő jelentéskészítési forgatókönyvek esetében, számos egyéb API-t is használhat. Az API-k elérhetők az Azure Resource Managerben, és a szolgáltatásnév-alapú hitelesítéssel automatizálhatók. Az alábbiakban a használható új funkciók rövid összefoglalóját olvashatja.

- [Költségvetések](/rest/api/consumption/budgets/createorupdate) – Küszöbértékeket állíthat be és proaktív módon monitorozhatja a költségeket, riasztást küldhet az érdekelt feleknek, és automatizálhatja a küszöbértékek átlépésére adott válaszokat.

- [Riasztások](/rest/api/cost-management/alerts) – Riasztási adatokat tekinthet meg, például költségvetési, számla-, jóváírási és kvótariasztásokat.

- [Exportálások](/rest/api/cost-management/exports) – Ütemezheti a költségek adatainak ismétlődő, a választott Azure Storage-fiókba történő exportálását. Ez az ajánlott megoldás azon ügyfelek számára, akiknek sok Azure-előfizetésük van, és elemezni szeretnék az adataikat, hogy a saját belső rendszerükben használhassák őket.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg az [Azure Resource Manager REST API-kkal](/rest/api/azure).
- Szükség esetén állapítsa meg, hogy melyik EA API-kat használja, és az [EA API-k új Azure Resource Manager API-kra való leképezését](#ea-api-mapping-to-new-azure-resource-manager-apis) ismertető cikkben tekintse meg, hogy melyik Azure Resource Manager API-kba helyezheti át őket.
- Ha még nem használ Azure Resource Manager API-kat, [regisztrálja az ügyfélalkalmazását az Azure AD-vel](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Ha szükséges, frissítsen minden programkódot, hogy a szolgáltatásnévhez az [Azure AD-hitelesítést](/rest/api/azure/#create-the-request) használják.