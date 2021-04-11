---
title: A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegének nyomon követése
description: Megtudhatja, hogyan ellenőrizheti a Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegét.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: 96c37ded2750ad3e4882b203d71c75c9f4608e9d
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285909"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegének nyomon követése

A Microsoft-ügyfélszerződés számlázási fiókjához tartozó Azure-kreditek egyenlegét az Azure Portalon vagy REST API-k segítségével ellenőrizheti.

A Microsoft-ügyfélszerződéshez tartozó számlázási fiókok esetében a kreditek egy számlázási profilhoz vannak hozzárendelve. Mindegyik számlázási profil saját kreditekkel rendelkezik, amelyeket automatikusan alkalmaz a számlán található díjakra. Egy számlázási profil Azure-kreditegyenlegének megtekintéséhez tulajdonosi, közreműködői, olvasói, illetve számlakezelői szerepkörrel kell rendelkeznie a számlázási profilra, illetve tulajdonosi, közreműködői vagy olvasói szerepkörrel a számlázási fiókra vonatkozóan. Ha többet szeretne megtudni a szerepkörökről, tekintse meg[A Microsoft-ügyfélszerződéshez tartozó felügyeleti szerepkörök ismertetése az Azure-ban](understand-mca-roles.md) című szakaszt.

> [!NOTE]
> Akár 24 órába is telhet, amíg az új jóváírás megjelenik az Azure Portalon. Ha új jóváírást kap, amely nem jelenik meg a Portalon, várjon 24 órát, hogy a jóváírás megjelenjen.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>A kreditegyenleg ellenőrzése

### <a name="azure-portal"></a>[Azure Portalra](#tab/portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről.](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. A számlázási hatókörök lapon válassza ki azt a számlázási fiókot, amelynek kreditegyenlegét nyomon szeretné követni. A számlázási fióknak **Microsoft-ügyfélszerződés** típusú lesz.

    ![A számlázási hatóköröket megjelenítő képernyőkép.](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Az Azure Portal megjegyzi az utoljára használt számlázási hatókört, és megjeleníti a Költségkezelés + Számlázás lap következő megnyitásakor. A számlázási hatókörök lap nem jelenik meg, ha már korábban meglátogatta a Költségkezelés + Számlázás lapot. Ebben az esetben ellenőrizze, hogy a [megfelelő hatókörben](#check-access-to-a-microsoft-customer-agreement) van-e. Ha nem, [váltson hatókört](view-all-accounts.md#switch-billing-scope-in-the-azure-portal), és válassza ki a Microsoft-ügyfélszerződéshez kapcsolódó számlázási fiókot.

3. A bal oldalon válassza ki a **Fizetési módok**, majd az **Azure-kreditek** elemet.

   ![A becsült és aktuális egyenlegeket megjelenítő képernyőkép.](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. Az Azure-kreditek lap az alábbi szakaszokból áll:

   #### <a name="balance"></a>Egyenleg

   Az egyenleg szakasz az Azure-kreditegyenlegének összefoglalását jeleníti meg.

   ![Képernyőkép a számlázási profil kreditegyenlegéről](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Időszak               | Meghatározás                           |
   |--------------------|--------------------------------------------------------|
   | Becsült egyenleg  | A meglévő kreditek becsült mennyisége az összes számlázott és függőben lévő tranzakció figyelembevételével |
   | Aktuális egyenleg    | A kreditek mennyisége a legutóbbi számla óta. Ez nem tartalmazza a függő tranzakciókat |

   Ha a becsült egyenleg 0-ra csökken, a teljes használatért fizetnie kell, beleértve a kreditek által fedezett termékeket is.

   #### <a name="credits-list"></a>Kreditlista

   A kreditlista szakasz megjeleníti az Azure-kreditek listáját.

   ![Képernyőkép a számlázási profilhoz tartozó kreditek listájáról](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Időszak | Meghatározás |
   |---|---|
   | Forrás | A kredit beszerzésének forrása |
   | Kezdő dátum | A kredit beszerzésének dátuma |
   | Lejárati dátum | A kredit lejáratának dátuma |
   | Aktuális egyenleg | A legutóbbi számla egyenlege |
   | Eredeti mennyiség | A kredit eredeti mennyisége |
   | status | A kredit aktuális állapota. Az Állapot lehet aktív, felhasznált, lejárt vagy lejáró |

   #### <a name="transactions"></a>Tranzakciók

   A tranzakciók szakasz az Azure-kreditegyenlegét befolyásoló számlázási tranzakciókat jeleníti meg.

   ![Képernyőkép a számlázási profil kredittranzakcióiról](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Időszak | Meghatározás |
   |---|---|
   | Tranzakció dátuma | A tranzakció dátuma |
   | Leírás | A tranzakció leírása |
   | Mennyiség| A tranzakció összege |
   | Egyenleg | A tranzakció utáni egyenleg |

    > [!NOTE]
    >
    > Ha nem lát Azure-krediteket a fizetési módszerek oldalon, akkor vagy nem rendelkezik kredittel, vagy nem a megfelelő hatókört választotta ki. Válasszon ki kreditekkel rendelkező számlázási fiókot vagy egy számlázási profilját. További információ a hatókörök módosításáról: [A számlázás hatókörének módosítása az Azure Portalon](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Ha az Azure-krediteket a számlázási fiók hatókörében tekinti meg, és a számlázási fióknak több számlázási profilja van, az Azure-kreditek lapon megjelenik egy táblázat, amely összegzi az egyes számlázási profilokhoz tartozó Azure-krediteket. Egy számlázási profil részleteinek megtekintéséhez válasszon ki egy számlázási profilt a listából, és válassza ki a Fizetési módok, majd az Azure-kreditek elemet.

    ![Képernyőkép a számlázási fiókhoz tartozó kreditek listájáról](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST API](#tab/rest)

Használhatja az [Azure számlázási](/rest/api/billing/) vagy [használati](/rest/api/consumption/) API-jait a számlázási fiókhoz tartozó kreditegyenleg programozott módon való lekérdezéséhez.

Az alábbi példák REST API-kat használnak. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-billing-profiles-you-have-access-to"></a>Olyan számlázási profilok keresése, amelyekhez hozzáférése van

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
Az API-válasz visszaadja a számlázási fiókok és az azokhoz tartozó számlázási profilok listáját.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

A számlázási profil `displayName` tulajdonságával azonosíthatja azt a számlázási profilt, amelynél ellenőrizni szeretné a kreditegyenleget. Másolja a számlázási profil `id` elemét. Például ha a **Fejlesztés** számlázási profiljának kreditegyenlegét szeretné ellenőrizni, másolja a ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx``` értéket. Illessze be valahova ezt az értéket, hogy a következő lépésben használni tudja.

### <a name="get-azure-credit-balance"></a>Azure-kreditegyenleg lekérdezése

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingProfileId>` értéket az előző lépésben kimásolt `id` értékkel (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Az API-válasz visszaadja a számlázási profil becsült és aktuális kreditegyenlegét.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Elem neve  | Leírás                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | A meglévő kreditek becsült mennyisége az összes számlázott és függőben lévő tranzakció figyelembevételével. |
| `currentBalance`   | A kreditek mennyisége a legutóbbi számla óta. Ez nem tartalmazza a függő tranzakciókat.    |
| `pendingCreditAdjustments`      | A visszatérítésekhez hasonló módosító tényezők, amelyek még nincsenek számlázva.  |
| `expiredCredit`      |  Az utolsó számla óta lejárt kreditek.  |
| `pendingEligibleCharges`  | Azok a kreditre jogosult díjak, amelyek még nincsenek számlázva.   |

### <a name="get-list-of-credits"></a>Kreditek lekérdezése

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingProfileId>` értéket az előző lépésben kimásolt `id` értékkel (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Az API-válasz visszaadja a számlázási profilhoz tartozó Azure-kreditek listáját.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Elem neve  | Leírás                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | A kredit eredeti mennyisége. |
| `closedBalance`   | A legutóbbi számla egyenlege.    |
| `source`      | A forrás, amely meghatározza, hogy ki hogyan tett szert a kreditre. |
| `startDate`      |  A kredit aktiválásának dátuma.  |
| `expirationDate`  | A kredit lejáratának dátuma.   |
| `poNumber`  | Annak a számlának a rendelési száma, amelyen a kredit számlázva lett.   |

### <a name="get-transactions-that-affected-credit-balance"></a>A kreditegyenleget befolyásoló tranzakciók lekérdezése

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingProfileId>` értéket az előző lépésben kimásolt `id` értékkel (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Meg kell adnia egy **startDate** értéket és egy **endDate** értéket, hogy megkapja a kért időszak tranzakcióit.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Az API-válasz minden olyan tranzakciót visszaad, amely befolyásolta a számlázási profilhoz tartozó kreditegyenleget.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Elem neve  | Leírás                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | A tranzakció végbemenetelének dátuma. |
| `description` | A tranzakció leírása. |
| `adjustments`   | A tranzakcióra vonatkozó kreditmódosítások.    |
| `creditExpired`      | A lejárt kreditek összege. |
| `charges`      |  A tranzakció díjai.  |
| `closedBalance`  | A tranzakciót követő egyenleg.   |
| `eventType`  | A tranzakció típusa.   |
| `invoiceNumber`  | Annak a számlának a száma, amelyen a tranzakció számlázva lett. Függőben lévő tranzakció esetében üres lesz.   |

---

## <a name="how-credits-are-used"></a>A kreditek felhasználása

A Microsoft-ügyfélszerződésekhez tartozó számlázási fiókokban a számlázási profilokkal kezelheti a számlákat és a fizetési módokat. A rendszer havonta létrehoz egy számlát minden számlázási profil számára, a fizetési módok használatával pedig kifizetheti a számlát.

Hozzárendelheti a számlázási profilhoz beszerzett krediteket. A számla számlázási profil számára történő létrehozásakor a rendszer automatikusan alkalmazza a krediteket az összköltségre a fizetendő összeg kiszámításához. A fennmaradó összeget egy fizetési mód segítségével, például csekkel/átutalással vagy bankkártyával fizetheti ki.

## <a name="products-that-arent-covered-by-azure-credits"></a>Az Azure-kreditek által nem fedezett termékek

 Az alábbi termékeket nem fedezi az Azure-kredit. E termékek használatáért a kreditegyenlegétől függetlenül fizetnie kell:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (havi)
- Visual Studio Enterprise (éves)
- Visual Studio Professional (havi)
- Visual Studio Professional (éves)
- Azure Marketplace-termékek
- Azure-támogatás ügyfeleknek

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [A Microsoft-ügyfélszerződéshez tartozó számlázási fiók ismertetése](../understand/mca-overview.md)
- [A Microsoft-ügyfélszerződéshez tartozó számlán szereplő kifejezések értelmezése](../understand/mca-understand-your-invoice.md)
