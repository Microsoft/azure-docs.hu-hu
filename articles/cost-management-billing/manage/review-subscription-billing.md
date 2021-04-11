---
title: Azure-előfizetés számlázási adatainak áttekintése REST API-val
description: Megtudhatja, hogyan tekintheti át az előfizetés számlázási adatait Azure REST API-kkal. Szűrők használatával személyre szabhatja az eredményeket.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f23beeb735c83cdf2d42a01ff684510dbd0d9feb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058833"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Előfizetés számlázásának áttekintése REST API-kkal

Az Azure Reporting API-k az Azure-költségek áttekintésében és kezelésében segítenek.

A szűrők az eredmények igényekre szabásában segítenek.

Itt megtudhatja, hogyan kérheti le egy előfizetés számlázási adatait egy adott dátumtartományhoz REST API használatával.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem felépítése

A `{subscriptionID}` paraméter megadása kötelező, és az előfizetési célt azonosítja.

A `{billingPeriod}` paraméter megadása kötelező, és az aktuális [számlázási időszakot](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) határozza meg.

A `${startDate}` és az `${endDate}` paraméter megadása kötelező ehhez a példához, de a végponthoz nem kötelezők. Sztringként határozzák meg a dátumtartományt ÉÉÉÉ-HH-NN formátumban (például: `'20180501'` és `'20180615'`).

A következő fejlécek megadása kötelező:

|Kérelem fejléce|Leírás|
|--------------------|-----------------|
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Válasz

Sikeres válasz esetén a rendszer a 200-as (OK) állapotkódot adja vissza, amely a fiók részletes költségeinek listáját tartalmazza.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

A **value** alatt lévő összes elem egy szolgáltatás használatával kapcsolatos részletet jelöl:

|Választulajdonság|Leírás|
|----------------|----------|
|**subscriptionGuid** | Az előfizetés globálisan egyedi azonosítója. |
|**startDate** | A használat megkezdésének dátuma. |
|**endDate** | A használat befejezésének dátuma. |
|**useageQuantity** | A használt mennyiség. |
|**billableQuantity** | A ténylegesen számlázott mennyiség. |
|**pretaxCost** | A vonatkozó adók előtti számlázott költségek. |
|**meterDetails** | A használattal kapcsolatos részletes adatok. |
|**nextLink**| Amikor meg van határozva, a következő részletező „oldal” URL-címét határozza meg. Üres, amikor az oldal az utolsó. |

Ez a példa rövidítve van; a válaszmezők teljes leírását a [használati részletek felsorolásában](/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy) találja.

A többi állapotkód hibafeltételt jelez. Ilyen esetekben a válaszobjektum ad magyarázatot arra, miért hiúsult meg a kérelem.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>További lépések
- Tekintse meg [a vállalati jelentéskészítés áttekintését](./enterprise-api.md) ismertető szakaszt
- Vizsgálja meg az [Enterprise Billing REST API](/rest/api/billing/) használatának lehetőségét
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)