---
title: Az Azure Enterprise beléptetési számlázási adatai áttekintése REST API
description: Megtudhatja, hogyan tekintheti át a nagyvállalati regisztrációk számlázási adatait Azure REST API-kkal.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4ac9c38c3f86c1226edf3b448324205cf5f98568
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058867"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Nagyvállalati regisztráció számlázásának áttekintése REST API-kkal

Az Azure Reporting API-k az Azure-költségek áttekintésében és kezelésében segítenek.

Ebből a cikkből megtudhatja, hogyan kérheti le a számlázási fiókokkal, részlegekkel vagy Nagyvállalati Szerződéssel (EA) rendelkező regisztrációs fiókokkal kapcsolatos számlázási adatokat az Azure REST API-k használatával.

## <a name="individual-account-billing"></a>Egyéni fiókok számlázása

Egy részlegben lévő fiókok használati adatainak lekérése:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{billingAccountId}` paraméter megadása kötelező, és tartalmaznia kell a fiók azonosítóját.

A következő fejlécek megadása kötelező:

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [API-kulcsra](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A teljesítmény érdekében a szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az elmúlt 36 hónap adataiért [aszinkron módon is meghívhatja az API-t](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based).


## <a name="response"></a>Reagálás  

Sikeres válasz esetén a rendszer a 200-as (OK) állapotkódot adja vissza, amely a fiók részletes költségeinek listáját tartalmazza.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Ez a példa rövidítve van; a válaszmezők és a hibakezelés teljes leírását a [számlázási fiókok használati adatainak lekérésével](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy) kapcsolatos szakaszban találja.

## <a name="department-billing"></a>Részleg számlázása

Lekérheti egy részleg összes fiókjának összesített használati adatait.

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{departmentId}` paraméter megadása kötelező, és tartalmaznia kell a regisztrációs fiók részlegének azonosítóját.

A következő fejlécek megadása kötelező:

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [API-kulcsra](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A teljesítmény érdekében a szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az elmúlt 36 hónap adataiért [aszinkron módon is meghívhatja az API-t](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based).

### <a name="response"></a>Reagálás  

Sikeres válasz esetén a rendszer a 200 (OK) állapotkódot adja vissza, amely tartalmazza egy adott számlázási időszak részletes használati adatainak és költségeinek listáját, valamint a részleg számlájának azonosítóját.


Az alábbi példa az `1234` részleg REST API-jának kimenetét mutatja be.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Ez a példa rövidítve van; a válaszmezők és a hibakezelés teljes leírását a [részlegek használati adatainak lekérésével](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy) kapcsolatos szakaszban találja.

## <a name="enrollment-account-billing"></a>Regisztrációs fiók számlázása

Lekérheti a regisztrációs fiók összesített használati adatait.

```http
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Az `{enrollmentAccountId}` paraméter megadása kötelező, és tartalmaznia kell a regisztrációs fiók azonosítóját.

A következő fejlécek megadása kötelező:

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [API-kulcsra](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A teljesítmény érdekében a szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az elmúlt 36 hónap adataiért [aszinkron módon is meghívhatja az API-t](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based).

### <a name="response"></a>Reagálás  

Sikeres válasz esetén a rendszer a 200 (OK) állapotkódot adja vissza, amely tartalmazza egy adott számlázási időszak részletes használati adatainak és költségeinek listáját, valamint a részleg számlájának azonosítóját.

Az alábbi példa az `1234` vállalati regisztráció REST API-jának kimenetét mutatja be.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```

Ez a példa rövidítve van; a válaszmezők és a hibakezelés teljes leírását a [regisztrációs fiókok használati adatainak lekérésével](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy) kapcsolatos szakaszban találja.

## <a name="next-steps"></a>További lépések
- Tekintse meg [a vállalati jelentéskészítés áttekintését](./enterprise-api.md) ismertető szakaszt
- Vizsgálja meg az [Enterprise Billing REST API](/rest/api/billing/) használatának lehetőségét   
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)