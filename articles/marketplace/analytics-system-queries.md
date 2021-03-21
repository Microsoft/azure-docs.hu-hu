---
title: Rendszerlekérdezések listája
description: Ismerje meg azokat a rendszerlekérdezéseket, amelyekkel programozott módon szerezhet be elemzési információkat a Microsoft kereskedelmi piactéren elérhető ajánlatokról.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583825"
---
# <a name="list-of-system-queries"></a>Rendszerlekérdezések listája

A következő rendszerlekérdezések használhatók közvetlenül a [jelentés létrehozása API](analytics-programmatic-access.md#create-report-api) -val `QueryId` . A rendszerlekérdezések olyanok, mint a partner Center exportálási jelentései egy hat hónapos (6M) számítási időszakra vonatkozóan.

Az oszlopnevek, attribútumok és leírás további információit a következő cikkekben találja:

- [Az ügyfelek irányítópultja a kereskedelmi piactér elemzésében](customer-dashboard.md#customer-details-table)
- [A Rendelések irányítópult a kereskedelmi piactér-elemzésekben](orders-dashboard.md#orders-details-table)
- [Használati irányítópult a kereskedelmi piactér elemzésében](usage-dashboard.md#usage-details-table)
- [A Marketplace Insights irányítópult a kereskedelmi piactér-elemzésekben](insights-dashboard.md#marketplace-insights-details-table)

A következő szakaszokban a jelentések különböző jelentésekhez nyújtanak lekérdezéseket.

## <a name="customers-report-query"></a>Ügyfelek jelentésének lekérdezése

**Jelentés leírása**: az elmúlt 6m-hez tartozó ügyfelek jelentés

**QueryID**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Jelentés lekérdezése**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Rendelések jelentésének lekérdezése

**Jelentés leírása**: Orders jelentés az utolsó 6m-hez

**QueryID**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Jelentés lekérdezése**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Használati jelentés lekérdezései

**Jelentés leírása**: a virtuális gép normalizált használati jelentése az utolsó 6m-es időként

**QueryID**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Jelentés lekérdezése**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Jelentés leírása**: virtuális gép nyers használati jelentése az utolsó 6m-es időként

**QueryID**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Jelentés lekérdezése**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Jelentés leírása**: mért használati jelentés az utolsó 6m-hez

**QueryID**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Jelentés lekérdezése**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Marketplace-bejelentési jelentések lekérdezése

**Jelentés leírása**: az utolsó 6m-es Piactéri jelentés

**QueryID**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Jelentés lekérdezése**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Következő lépések

- [A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k](analytics-available-apis.md)
- [Példa a kereskedelmi piactér elemzési adatokhoz való hozzáférésre](analytics-sample-application.md)
