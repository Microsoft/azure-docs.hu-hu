---
title: Példák a programozott elemzések lekérdezésére
description: Ezekkel a lekérdezésekkel programozott módon érheti el a Microsoft kereskedelmi piactér elemzési szolgáltatásait.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583828"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Példák a programozott elemzések lekérdezésére

Ez a cikk a Microsoft kereskedelmi piactérre vonatkozó megrendelések, használati és vevői jelentések lekérdezéseit tartalmazza. Ezeket a lekérdezéseket a [jelentés lekérdezési](analytics-programmatic-access.md#create-report-query-api) API-végpontjának meghívásával is használhatja. Ha szükséges, a [jelentés lekérdezése](analytics-programmatic-access.md#create-report-query-api) API hívása módosítható további oszlopok hozzáadásához, a számítási időszak módosításához és a szűrési feltételek hozzáadásához. A támogatott időszakok hat hónap (6M), 12 hónap (12M) és egyéni időtartam.

Az oszlopnevek, attribútumok és leírások részleteiért tekintse meg az alábbi táblázatokat:

- [Ügyfél részletei tábla](customer-dashboard.md#customer-details-table)
- [Megrendelések részletei tábla](orders-dashboard.md#orders-details-table)
- [Használat részletei tábla](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Ügyfelek jelentéseinek lekérdezése

Ezek a minta-lekérdezések az ügyfelek jelentésére vonatkoznak.

| **Lekérdezés leírása** | **Példa lekérdezésre** |
| --- | --- |
| A partner aktív ügyfelei a választott dátumig | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| A partner átvevő ügyfelei a választott dátumig | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Az elmúlt hat hónapban egy adott földrajzi hely új ügyfeleinek listája | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Használati jelentés lekérdezései

Ezek a minta-lekérdezések a használati jelentésre vonatkoznak.

| **Lekérdezés leírása** | **Példa lekérdezésre** |
| --- | --- |
| A virtuális gép (VM) normalizált használata a "számlázás az Azure-ban" Piactéri licencelési típus az utolsó 6M-re | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Virtuálisgép-nyers használat a "számlázás az Azure-ban" Piactéri licencelési típus az elmúlt 12M-ben | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| A virtuális gép normalizált használata a "saját licenc használata" Piactéri licencelési típus az utolsó 6M-re | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Virtuálisgép-nyers használat a "saját licenc használata" Piactéri licencelési típus az utolsó 6M-re | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| A használati dátum, a napi teljes normalizált használat és a "becsült kiterjesztett díjak (PC/CC)" alapján az elmúlt hónapra vonatkozó díjköteles csomagok esetében | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| A használat dátuma, a napi teljes nyers használat és a "becsült kiterjesztett díjak (PC/CC)" alapján az elmúlt hónapra vonatkozó díjköteles csomagok esetében | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Egy adott ajánlat neve esetén a virtuális gép normalizált használata a "számlázás az Azure-ban" piactér-licenc típusa az utolsó 6M-re | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Egy adott ajánlat neve esetén az utolsó 6M mért használati díja | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>A megrendelések jelentésének lekérdezései

Ezek a lekérdezések a rendelések jelentésre vonatkoznak.

| **Lekérdezés leírása** | **Példa lekérdezésre** |
| --- | --- |
| Az Azure-licenc típusának jelentése "Enterprise", az utolsó 6M esetében | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| A megrendelések jelentése az Azure-licenc típusaként az utolsó 6M-ben "fizetés közben | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| A rendelések jelentés az utolsó 6M adott ajánlatának nevéről | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| A megrendelések jelentés az utolsó 6M-es aktív rendelésekről | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Megrendelések jelentés az utolsó 6M megszakított rendeléseiről | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Következő lépések

- [A kereskedelmi piactér elemzési adatokhoz való hozzáféréshez használható API-k](analytics-available-apis.md)
