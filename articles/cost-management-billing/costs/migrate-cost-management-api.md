---
title: EA migrálása Microsoft Customer Agreement API-kra – Azure
description: Ez a cikk a Microsoft Nagyvállalati Szerződések (EA) Microsoft Ügyfélszerződésbe való migrálásának következményeit ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078349"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrálás Nagyvállalati Szerződésről Microsoft Customer Agreement API-kra

Ez a cikk a Nagyvállalati Szerződés- (EA-) és a Microsoft Ügyfélszerződés- (MCA-) fiókok közötti különbségeket ismerteti az adatszerkezet, az API és az egyéb rendszer-integrációs szempontok tekintetében. Az Azure Cost Management mindkét fióktípus API-jait támogatja. A folytatás előtt tekintse át a Microsoft Ügyfélszerződés [számlázási fiókjának beállításával](../manage/mca-setup-account.md) kapcsolatos szakaszt.

A meglévő EA-fiókkal rendelkező szervezeteknek egy MCA-fiók beállítása mellett át kell tekinteniük ezt a cikket. Korábban az EA-fiókok megújításakor minimális energiaráfordításra volt szükség a régi regisztrációról egy újra való átálláshoz. Az MCA-fiókra való migrálás azonban további befektetett munkát igényel. A további erőfeszítésekre a mögöttes számlázási alrendszer változásai miatt van szükség, amelyek hatással vannak a költségekkel kapcsolatos összes API-ra és szolgáltatásajánlatra.

## <a name="mca-apis-and-integration"></a>MCA API-k és integráció

Az MCA API-k és az új integráció a következőket teszik lehetővé:

- Az API-k teljes körű rendelkezésre állása a natív Azure API-kban.
- Több számla konfigurálása egyetlen számlázási fiókban.
- Egy kombinált API elérése Azure-szolgáltatáshasználathoz, a külső Marketplace-használathoz és Marketplace-beli vásárlásokhoz.
- Különböző számlázási profilok (ugyanazok, mint a regisztrációk) költségeinek megtekintése az Azure Cost Managementtel.
- Új API-k érhetők el a költségek megjelenítéséhez, értesítések beállításához, amelyek jelzik, ha a költségek túllépik az előre megadott küszöbértékeket, illetve a nyers adatok automatikus exportálásához.

## <a name="migration-checklist"></a>Migrálási ellenőrzőlista

A következő szempontok segítenek az MCA API-kra való átállásban.

- Ismerkedjen meg az új, [Microsoft Ügyfélszerződéshez tartozó számlázási fiókkal](../understand/mca-overview.md).
- Határozza meg, hogy mely API-kat használja, és tekintse meg a következő szakaszban, hogy melyek lesznek lecserélve.
- Ismerkedjen meg az [Azure Resource Manager REST API-kkal](/rest/api/azure).
- Ha még nem használ Azure Resource Manager API-kat, [regisztrálja az ügyfélalkalmazását az Azure AD-vel](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Frissítsen minden programkódot az [Azure AD-hitelesítés használatára](/rest/api/azure/#create-the-request).
- Frissítsen minden programkódot úgy, hogy az EA API-hívásokat MCA API-hívásokra cseréljék.
- Frissítse a hibakezelést az új hibakódok használatára.
- Az egyéb szükséges műveletekkel kapcsolatban tekintse át a további integrációs ajánlatokat, például a Cloudynt és a Power BI-t.

## <a name="ea-apis-replaced-with-mca-apis"></a>MCA API-kkal lecserélt EA API-k

Az EA API-k API-kulcsot használnak a hitelesítéshez és az engedélyezéshez. Az MCA API-k Azure AD-hitelesítést használnak.

| Cél | EA API | MCA API |
| --- | --- | --- |
| Egyenleg és jóváírások | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Használat (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Használat (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-használat (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Számlázási időszakok | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Árlista | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. Billing/billingAccounts/billingProfiles/árlista/alapértelmezett/letöltési formátum = JSON \| CSV Microsoft. Billing/billingAccounts/.../billingProfiles/.../számlák/... /pricesheet/default/Download formátum = JSON \| CSV Microsoft. Billing/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Foglalásvásárlások | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Foglalási javaslatok | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Foglalás használata | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Az Azure-szolgáltatás és a külső Marketplace-használat a [Usage Details API-val](/rest/api/consumption/usagedetails) érhető el.

A következő API-k érhetőek el az MCA számlázási fiókokhoz:

| Cél | Microsoft Customer Agreement (MCA) API |
| --- | --- |
| Számlázási fiókok<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Számlázási profilok<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Számlaszakaszok<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Számlák | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Számlázási előfizetések | {scope}/billingSubscriptions |

<sup>2</sup> Az API-k objektumok listáit adják vissza, amelyek hatókörök, ahol a Cost Management-felületek működnek az Azure Portalon és az API-kon belül. A Cost Management-hatókörökkel kapcsolatos további információért tekintse meg [A hatókörök ismertetése és használata](understand-work-scopes.md) című szakaszt.

Ha meglévő EA API-kat használ, frissítenie kell őket az MCA számlázási fiókjainak támogatásához. Az alábbi táblázat további integrációs változásokat mutat be:

| Cél | Régi ajánlat | Új ajánlat |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)-tartalomcsomag és -összekötő |  [Azure Consumption Insights-összekötő](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API-k egyenleg és jóváírások lekérdezéséhez

A [Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API a következők havi összegzését nyújtja:

- Egyenlegek
- Új vásárlások
- Az Azure Marketplace szolgáltatási díjai
- Adjustments (Módosító tételek)
- A szolgáltatás kerettúllépési díjai

Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request).

A Get Balance Summary API-t a Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API váltja fel.

A rendelkezésre álló egyenleg lekérése az Available Balance API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Költségek és használat lekérésére szolgáló API-k

A következő API-kkal az Azure-szolgáltatáshasználat, a külső Marketplace-használat és a Marketplace-beli vásárlások költségeinek napi lebontását kérheti le. A következő különálló API-k össze lettek vonva az Azure-szolgáltatások és a külső Marketplace-használat esetében. A régi API-kat a [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API váltja fel. Hozzáadja továbbá a Marketplace-beli vásárlásokat, amelyek korábban csak az eddigi egyenlegösszegzésben voltak láthatók.

- [Get usage detail/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get usage detail/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get marketplace store charge/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Get marketplace store charge/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request).

Az összes korábbi API-t a Consumption/Usage Details API váltja fel.

Használati adatok lekérése a Usage Details API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A Usage Details API az összes Cost Management API-hoz hasonlóan több hatókörben is elérhető. A számlázott költségekhez (amelyet hagyományosan a regisztrációs szinten kapna) használja a számlázási profil hatókört.  A Cost Management-hatókörökkel kapcsolatos további információért tekintse meg [A hatókörök ismertetése és használata](understand-work-scopes.md) című szakaszt.

| Típus | Azonosító formátuma |
| --- | --- |
| Számlázási fiók | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Számlázási profil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Előfizetés | `/subscriptions/{subscriptionId}` |
| Erőforráscsoport | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

A következő lekérdezésisztring-paraméterekkel frissítse a programozási kódokat.

| Régi paraméterek | Új paraméterek |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nem támogatott |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

A válasz törzse is módosult.

Régi válasz törzse:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Új válasz törzse:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

A használati rekordok tömbjét tartalmazó tulajdonságnév adatról _értékre_ változott. Régebben mindegyik rekord a részletes tulajdonságok strukturálatlan listáját tartalmazta. Most azonban minden egyes rekord részletei egy _tulajdonságok_ nevű beágyazott tulajdonságban szerepel, a címkék kivételével. Az új struktúra konzisztens a többi Azure API-val. Néhány tulajdonságnév megváltozott. Az alábbi táblázatban láthatók a megfelelő tulajdonságok.

| Régi tulajdonság | Új tulajdonság | Jegyzetek |
| --- | --- | --- |
| AccountId | N/A | Az előfizetés létrehozója nincs nyomon követve. Használja a következőt: invoiceSectionId (ugyanaz, mint a departmentId). |
| AccountNameAccountOwnerId és AccountOwnerEmail | N/A | Az előfizetés létrehozója nincs nyomon követve. Használja a következőt: invoiceSectionName (ugyanaz, mint a departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Vegye figyelembe, hogy ezek a tulajdonságok egymás ellentétei. Ha az isAzureCreditEnabled értéke igaz, akkor a ChargesBilledSeparately hamis. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | A pontos sztringértékek eltérhetnek. |
| ConsumedServiceId | Nincsenek | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date és usageStartDate | dátum | &nbsp;  |
| Nap | Nincsenek | A napot elemzi a dátumból. |
| DepartmentId | invoiceSectionId | A pontos értékek eltérhetnek. |
| DepartmentName | invoiceSectionName | A pontos sztringértékek eltérhetnek. Szükség esetén konfigurálja úgy a számlázási szakaszokat, hogy megfeleljenek a részlegeknek. |
| ExtendedCost és Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Ismétlődő költség | Nincs | &nbsp;  |
| Hely | location | &nbsp;  |
| MeterCategory | meterCategory | A pontos sztringértékek eltérhetnek. |
| MeterId | meterId | A pontos sztringértékek eltérőek. |
| MeterName | meterName | A pontos sztringértékek eltérhetnek. |
| MeterRegion | meterRegion | A pontos sztringértékek eltérhetnek. |
| MeterSubCategory | meterSubCategory | A pontos sztringértékek eltérhetnek. |
| Month (hónap) | Nincsenek | A hónapot elemzi a dátumból. |
| Offer Name | Nincsenek | Használja a következőt: publisherName és productOrderName. |
| OfferID | Nincsenek | &nbsp;  |
| Order Number | Nincsenek | &nbsp;  |
| PartNumber | Nincsenek | Használja meterId azonosítót és productOrderName nevet az árak egyedi azonosításához. |
| Plan Name | productOrderName | &nbsp;  |
| Termék | Termék |   |
| ProductId | productId | A pontos sztringértékek eltérőek. |
| Publisher Name | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid (Erőforrás GUID azonosítója) | meterId | A pontos sztringértékek eltérőek. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Nincsenek | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/A | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | A pontos sztringértékek eltérhetnek. |
| ServiceTier | meterSubCategory | A pontos sztringértékek eltérhetnek. |
| StoreServiceIdentifier | N/A | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Címkék | tags | A címkék tulajdonság a gyökérszintű objektumra vonatkozik, nem a beágyazott tulajdonságok tulajdonságra. |
| UnitOfMeasure | unitOfMeasure | A pontos sztringértékek eltérőek. |
| usageEndDate | dátum | &nbsp;  |
| Year | Nincsenek | Az évet elemzi a dátumból. |
| (új) | billingCurrency | A díjhoz használt pénznem. |
| (új) | billingProfileId | A számlázási profil (a regisztráció) egyedi azonosítója. |
| (új) | billingProfileName | A számlázási profil (a regisztráció) neve. |
| (új) | chargeType | Az Azure-szolgáltatáshasználat, a Marketplace-használat és a vásárlások megkülönböztetésére szolgál. |
| (új) | invoiceId | A számla egyedi azonosítója. Az aktuális, nyitott hónap esetében üres. |
| (új) | publisherType | A vásárlások közzétevőjének típusa. A használat esetében üres. |
| (új) | serviceFamily | A vásárlás típusa. A használat esetében üres. |
| (új) | servicePeriodEndDate | A megvásárolt szolgáltatás záródátuma. |
| (új) | servicePeriodStartDate | A megvásárolt szolgáltatás kezdődátuma. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>A Billing Periods API-t az Invoices API váltja fel

Az MCA számlázási fiókjai nem használnak számlázási időszakokat. Ehelyett a számlákkal szűkítik le a költségeket adott számlázási időszakokra. A [Billing Periods API-t](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) az Invoices API váltja fel. Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request).

Számlák lekérése az Invoices API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Price Sheet API-k

Ez a szakasz a meglévő Price Sheet API-kat ismerteti, és javaslatokat nyújt a Price Sheet API-ra való áttéréshez a Microsoft Ügyfélszerződés kapcsán. A Microsoft Ügyfélszerződések Price Sheet API-ját is tárgyalja, továbbá leírást ad az árlistákon szereplő mezőkről. Az [Enterprise Get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) és az [Enterprise Get billing periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API-kat a Microsoft Ügyfélszerződések Price Sheet API-ja váltja fel (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). Az új API támogatja a JSON és a CSV formátumokat is, aszinkron REST formátumokban. Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Billing Enterprise API-k

Régebben Billing Enterprise API-kat használt a vállalati regisztrációkhoz az árakkal és a számlázási időszakkal kapcsolatos információk lekéréséhez. A hitelesítés és engedélyezés webes Azure Active Directory-jogkivonatot használt.

A megadott vállalati regisztráció érvényes árainak lekérése a Price Sheet és a Billing Period API-kkal:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Price Sheet API Microsoft Ügyfélszerződésekhez

A Microsoft Ügyfélszerződések Price Sheet API-jával tekintheti meg az összes Azure- és Marketplace-felhasználási szolgáltatás árait. A számlázási profilhoz megjelenített árak a számlázási profilhoz tartozó összes előfizetésre érvényesek.

A Price Sheet API-val tekintheti meg az összes Azure-felhasználási szolgáltatás árlistaadatait CSV formátumban:

| Metódus | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

A Price Sheet API-val tekintheti meg az összes Azure-felhasználási szolgáltatás árlistaadatait JSON formátumban:

| Metódus | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Az API a teljes fiókra vonatkozó árlistát adja vissza. Emellett azonban lekérheti az árlista tömör verzióját is PDF formátumban. Az összefoglalás egy adott számlához kiszámlázott Azure- és Marketplace-felhasználási szolgáltatásokat tartalmazza. A számlát az {invoiceId} azonosítja, amely ugyanaz, mint a Számlaösszesítő PDF-fájlokban látható **Számlaszám**. Íme egy példa.

![Szemléltető kép, amelyen az InvoiceId-nak megfelelő Számlaszám látható](./media/migrate-cost-management-api/invoicesummary.png)

Számlainformációk megtekintése a Price Sheet API-val CSV formátumban:

| Metódus | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Számlainformációk megtekintése a Price Sheet API-val JSON formátumban:

| Metódus | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Az aktuális nyitott számlázási ciklusban vagy szolgáltatási időszakban bármely Azure- vagy Marketplace-felhasználási szolgáltatás becsült árát is lekérheti.

A fogyasztási szolgáltatások becsült árainak megtekintése a Price Sheet API-val CSV formátumban:

| Metódus | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

A fogyasztási szolgáltatások becsült árainak megtekintése a Price Sheet API-val JSON formátumban:

| Metódus | Kérés URI-ja |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

A Microsoft Ügyfélszerződés Price Sheet API-jai *aszinkron REST API-k*. Az API-k válaszai módosultak a régi szinkron API-kéhoz képest. Az API-válasz törzse is módosult.

#### <a name="old-response-body"></a>Régi válasz törzse

Itt látható egy példa a szinkron REST API-válaszra:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
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
    ]
```

#### <a name="new-response-body"></a>Új válasz törzse

Az API-k támogatják az [Azure REST aszinkron](../../azure-resource-manager/management/async-operations.md) formátumot. Ha meghívja az API-t a GET paranccsal, a következő választ kapja:

```
No Response Body

HTTP Status 202 Accepted
```

A következő fejlécek lesznek elküldve a kimenet helyével:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Végezzen még egy GET-hívást a helyre irányulóan. A GET-hívásra adott válasz ugyanaz, amíg a művelet be nem fejeződik vagy hibás állapotot nem ér el. Ha befejeződött, a GET-hívási helyre adott válasz a letöltési URL-t adja vissza. Ugyanúgy, mintha egy időben hajtotta volna végre a műveletet. Bemutatunk egy példát:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Az ügyfél is indíthat GET-hívást a következőhöz: `Azure-AsyncOperation`. A végpont a művelet állapotát adja vissza.

A következő táblázat a régebbi Enterprise Get Price Sheet API mezőit jeleníti meg. A Microsoft Ügyfélszerződések új árlistájában lévő megfelelő mezőket tartalmazza:

| Régi tulajdonság | Új tulajdonság | Jegyzetek |
| --- | --- | --- |
| billingPeriodId  | _Nem alkalmazható_ | Nem alkalmazható. A Microsoft Ügyfélszerződések esetében a számla és a társított árlista felváltotta a billingPeriodId azonosítót. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | A pontos sztringértékek eltérhetnek. |
| includedQuantity  | includedQuantity | A Microsoft Ügyfélszerződésekben szereplő szolgáltatásokra nem alkalmazható. |
| partNumber  | _Nem alkalmazható_ | Ehelyett használja a productOrderName (ugyanaz, mint az offerID) és a meterID kombinációját. |
| unitPrice  | unitPrice | Az egységár a Microsoft Ügyfélszerződések keretében felhasznált szolgáltatásokra érvényes. |
| currencyCode  | pricingCurrency | A Microsoft Ügyfélszerződések árai díjszabási pénznemben és számlázási pénznemben jelennek meg. A currencyCode a Microsoft Ügyfélszerződések pricingCurrency paraméterének felel meg. |
| offerID | productOrderName | Az OfferID helyett használhatja a productOrderName nevet, de az nem egyezik meg az OfferID azonosítóval. A productOrderName és a mérő határozza meg azonban a meterId és az OfferID azonosítóhoz kapcsolódó díjszabást a Microsoft Ügyfélszerződésekben a régebbi regisztrációk esetében. |

## <a name="consumption-price-sheet-api-operations"></a>A Consumption Price Sheet API műveletei

Nagyvállalati szerződések esetén a Consumption Price Sheet API [Get](/rest/api/consumption/pricesheet/get) és [Get By Billing Period](/rest/api/consumption/pricesheet/getbybillingperiod) műveleteit használta a subscriptionId azonosítón vagy számlázási időszakon alapuló hatókörhöz. Az API Azure Resource Manager-hitelesítést használ.

Hatókör árlista-információinak lekérése a Price Sheet API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Árlista-információk lekérése számlázási időszak alapján a Price Sheet API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

A fenti API-végpontok helyett a következőket használja a Microsoft Ügyfélszerződésekhez:

**Price Sheet API Microsoft Ügyfélszerződésekhez (aszinkron REST API)**

Ez az API a Microsoft Ügyfélszerződésekhez használható, és további attribútumokat ad meg.

**Számlázási profil hatóköréhez tartozó árlista egy számlázási fiókban**

Ez az API a meglévő API. Frissítve lett, hogy megadja egy számlázási fiókban lévő számlázási profil árlistáját.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Árlista számlázási fiók szerinti hatókörhöz

Az árlista egy számlázási fiók regisztrációs hatókörében történő lekérésekor a rendszer Azure Resource Manager-hitelesítést használ.

Az árlista lekérése számlázási fiókban lévő regisztrációs fiók esetében:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft Ügyfélszerződés esetén a következő szakaszban szereplő információkat használja. A Microsoft Ügyfélszerződésekhez használt tulajdonságokat biztosítja.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Árlista számlázási fiókban lévő számlázási profil hatóköréhez

A frissített Price Sheet by billing account API CSV formátumban kéri le az árlistát. Az árlista lekérése egy MCA számlázási profilra vonatkozó hatókörében:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Az EA regisztrációs hatókörében az API-válasz és a tulajdonságok azonosak. A tulajdonságok ugyanazoknak az MCA-tulajdonságoknak felelnek meg.

Az [Azure Resource Manager Price Sheet API-k](/rest/api/consumption/pricesheet) régebbi tulajdonságai és a megfelelő új tulajdonságok a következő táblázatban szerepelnek.

| Régi Azure Resource Manager Price Sheet API-tulajdonság  | Új Microsoft Customer Agreement Price Sheet API-tulajdonság   | Leírás |
| --- | --- | --- |
| Meter ID | _meterId_ | A fogyasztásmérő egyedi azonosítója. Ugyanaz, mint a meterID. |
| Fogyasztásmérő neve | meterName | A fogyasztásmérő neve. A fogyasztásmérő az Azure-szolgáltatás üzembe helyezhető erőforrását jelöli. |
| Meter Category (Mérési kategória)  | szolgáltatás | A fogyasztásmérő osztályozási kategóriájának neve. Ugyanaz, mint a Microsoft Ügyfélszerződés árlistáján szereplő szolgáltatás. A pontos sztringértékek eltérőek. |
| Meter Subcategory (Mérési alkategória) | meterSubCategory | A fogyasztásmérő osztályozási alkategóriájának neve. A szolgáltatás általános szolgáltatáskészlet-megkülönböztetésének besorolásán alapul. Például Basic SQL DB vagy Standard SQL DB. |
| Fogyasztásmérő régiója | meterRegion | &nbsp;  |
| Unit (Egység) | _Nem alkalmazható_ | A unitOfMeasure egységből elemezhető. |
| Mértékegység | unitOfMeasure | &nbsp;  |
| Cikkszám | _Nem alkalmazható_ | A cikkszám helyett a productOrderName és a MeterID segítségével azonosítsa egyedileg a számlázási profil árát. A mezők az MCA-számlák cikkszáma helyett az MCA-számlán szerepelnek. |
| Egységár | unitPrice | Microsoft Ügyfélszerződés egységára. |
| Pénznemkód | pricingCurrency | A Microsoft Ügyfélszerződések árai díjszabási pénznemben és számlázási pénznemben jelennek meg. A pénznemkód megegyezik a Microsoft Ügyfélszerződések pricingCurrency paraméterével. |
| Szolgáltatási keret | includedQuantity | A Microsoft Ügyfélszerződésekben szereplő szolgáltatásokra nem alkalmazható. Megjelenítés nulla értékekkel. |
|  Ajánlat azonosítója  | productOrderName | Az OfferID helyett használja a productOrderName nevet. Nem ugyanaz, mint az OfferID, de a productOrderName és a fogyasztásmérő határozza meg a Microsoft Ügyfélszerződések díjszabását. A régebbi regisztrációkban a meterId és az OfferID azonosítókhoz köthető. |

A Microsoft Ügyfélszerződések ára máshogy van meghatározva, mint a Nagyvállalati szerződéseké. A nagyvállalati regisztráció szolgáltatásainak ára a terméktől, cikkszámtól, fogyasztásmérőtől és ajánlattól függően egyedi. A Microsoft Ügyfélszerződések nem használnak cikkszámot.

Az Azure-felhasználási szolgáltatás a Microsoft Ügyfélszerződés részét képező ára a productOrderName és a meterID szerint egyedi. Ezek jelölik a szolgáltatás fogyasztásmérőjét és a termékcsomagot.

Az árlista és a Usage Details API-ban szereplő használat egyeztetéséhez a productOrderName és a meterID paramétereket használhatja.

A számlázásiprofil-tulajdonosi, közreműködői, olvasói és számlakezelői jogosultságokkal rendelkező felhasználók letölthetik az árlistát.

Az árlistán azon szolgáltatások árai szerepelnek, amelyeknek az ára használaton alapul. A szolgáltatások közé tartozik az Azure-felhasználás és a Marketplace-felhasználás. Az egyes szolgáltatási időszakok legutolsó ára zárolva lesz, és egyetlen szolgáltatási időszakban lesz alkalmazva a használatra. Az Azure-felhasználási szolgáltatások esetében a szolgáltatási időszak általában egy naptári hónap.

### <a name="retired-price-sheet-api-fields"></a>A Price Sheet API megszüntetett mezői

A következő mezők vagy nem érhetők el a Microsoft Ügyfélszerződés Price Sheet API-jaiban, vagy ugyanazokkal a mezőkkel rendelkeznek.

|Megszüntetett mező| Leírás|
|---|---|
| billingPeriodId | Nem alkalmazható. Az MCA InvoiceId paraméterének felel meg. |
| offerID | Nem alkalmazható. Az MCA productOrderName paraméterének felel meg. |
| meterCategory  | Nem alkalmazható. Az MCA Service paraméterének felel meg. |
| egység | Nem alkalmazható. A unitOfMeasure egységből elemezhető. |
| currencyCode | Ugyanaz, mint az MCA pricingCurrency paramétere. |
| meterLocation | Ugyanaz, mint az MCA meterRegion paramétere. |
| partNumber partnumber | Nem alkalmazható, mert a cikkszám nem szerepel az MCA-számlákon. A cikkszám helyett használja a meterId és a productOrderName kombinációját az árak egyedi azonosításához. |
| totalIncludedQuantity | Nem alkalmazható. |
| pretaxStandardRate  | Nem alkalmazható. |

## <a name="reservation-instance-charge-api-replaced"></a>Lecserélt Reservation Instance Charge API

A [Reservation Instance Charge API-val](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) számlázási tranzakciókat kérhet le a foglalásvásárlásokhoz. Az új API magában foglalja az összes vásárlást, beleértve a harmadik féltől származó Marketplace-ajánlatokat is. Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request). A Reserved Instance Charge API-t a Transactions API váltja fel.

Foglalásvásárlások tranzakcióinak lekérése a Transactions API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Lecserélt Recommendations API-k

A Reserved Instance Purchase Recommendations API-k az elmúlt 7, 30 vagy 60 nap virtuálisgép-használatáról nyújtanak információt. Az API-k a foglalásvásárlással kapcsolatos javaslatokat is biztosítanak. Ezek közé tartoznak például az alábbiak:

- [Shared Reserved Instance Recommendation API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Single Reserved Instance Recommendations API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request). A korábban felsorolt, foglalási javaslatokkal kapcsolatos API-k helyébe a [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API lép.

Foglalási javaslatok lekérése a Reservation Recommendations API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Lecserélt Reservation Usage API-k

A Reserved Instance Usage API-val kérheti le a regisztrációk foglaláshasználatát. Ha egy regisztrációban több fenntartott példány is van, ezzel az API-val lekérheti az összes fenntartottpéldány-vásárlással kapcsolatos használatot is.

Ezek közé tartoznak például az alábbiak:

- [Fenntartott példány használata részletei](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Fenntartott példány kihasználtságának összegzése](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Az összes használati API-t natív Azure API-k váltják fel, amelyek az Azure AD-t használják a hitelesítéshez és az engedélyezéshez. További információ az Azure REST API-k hívásáról: [Ismerkedés a REST-tel](/rest/api/azure/#create-the-request). A korábban felsorolt, foglalási javaslatokkal kapcsolatos API-k helyébe a [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) és a [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API-k lépnek.

Foglalási részletek lekérése a Reservation Details API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Foglalási összegzések lekérése a Reservation Summaries API-val:

| Metódus | Kérés URI-ja |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Áttérés a Cloudynról a Cost Managementre

A [Cloudynt](https://cloudyn.com) használó szervezeteknek érdemes elkezdeniük az [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) használatát a költségkezelési igényeik kielégítéséhez. A Cost Management előkészítés nélkül, nyolc órás késleltetéssel érhető el az Azure Portalon. További információt a [Cost Management dokumentációjában](../index.yml) talál.

Az Azure Cost Management a következőket teszi lehetővé:

- Adott időszakra vonatkozó költségek megtekintése előre meghatározott költségvetés alapján. Napi költségminták elemzése a kiadások rendellenességeinek azonosítása és megakadályozása érdekében. Költségek lebontása címkék, erőforráscsoportok, szolgáltatás és hely szerint.
- Költségvetések létrehozása a használattal és költségekkel kapcsolatos korlátok felállításához, továbbá értesítések beállítása a fontos küszöbértékek megközelítésének jelzésére. Automatizálás beállítása műveleti csoportokkal az egyéni események elindításához és a feltételek rögzített korlátainak kikényszerítéséhez.
- A költségek és a használat optimalizálása az Azure Advisor javaslataival. A vásárlások optimalizálása foglalásokkal, a kihasználatlan virtuális gépek méretének csökkentésével és a nem használt erőforrások törlésével, hogy a költségkereten belül maradhasson.
- Költség- és használati adatok exportálásának ütemezése, hogy naponta közzétegyen egy CSV-fájlt a tárfiókban. A külső rendszerekkel való integráció automatizálása, hogy a számlázási adatok szinkronban maradjanak és naprakészek legyenek.

## <a name="power-bi-integration"></a>Power BI-integráció

A költségek jelentéséhez a Power BI-t is használhatja. A Power BI Desktophoz készült [Azure Cost Management-összekötővel](/power-bi/desktop-connect-azure-cost-management) hatékony, személyre szabott jelentéseket hozhat létre, amelyek segítségével jobban megismerheti az Azure-beli kiadásait. Az Azure Cost Management-összekötő jelenleg a Microsoft Ügyfélszerződéssel és a Nagyvállalati Szerződéssel (EA) rendelkező ügyfeleket támogatja.

## <a name="next-steps"></a>Következő lépések

- Az Azure-beli költségek monitorozásáról és szabályozásáról a [Cost Management dokumentációjában](../index.yml) olvashat. Továbbá azt is megtudhatja, hogyan optimalizálhatja az erőforrás-használatot a Cost Managementtel.
