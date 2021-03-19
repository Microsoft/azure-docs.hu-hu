---
title: Kivont Azure Cosmos DB teljesítményszint
description: Ismerkedjen meg a Azure Cosmos DBban korábban elérhető S1, S2 és S3 teljesítmény szintjével.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4cb07d9d19d85cd8dff9a52eeeb7e173b60f4d6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080770"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Az S1, S2 és S3 teljesítményszintek kivonása
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT] 
> A cikkben ismertetett S1, S2 és S3 szintű teljesítményszint megszűnik, és az új Azure Cosmos DB fiókok esetében már nem érhetők el.

Ez a cikk az S1, az S2 és az S3 teljesítményszint áttekintését tartalmazza, és bemutatja, hogyan telepíthetők át az ezeket a teljesítményadatokat használó gyűjtemények egy particionált gyűjteményekbe. A cikk elolvasása után a következő kérdésekre tud válaszolni:

- [Miért kerül kivonásra az S1, S2 és S3 szintű teljesítményszint?](#why-retired)
- [Hogyan hasonlítható össze az egypartíciós gyűjtemények és a particionált gyűjtemények az S1, az S2 és az S3 teljesítmény szintjével?](#compare)
- [Mit kell tennem az adataim zavartalan elérésének biztosításához?](#uninterrupted-access)
- [Hogyan változik a gyűjtemény az áttelepítés után?](#collection-change)
- [Hogyan változik a számlázási változás a különálló partíciós gyűjteményekbe való Migrálás után?](#billing-change)
- [Mi a teendő, ha több mint 20 GB tárterületre van szükségem?](#more-storage-needed)
- [Válthatok az S1, az S2 és az S3 teljesítményszint között a tervezett áttelepítés előtt?](#change-before)
- [Hogyan Migrálás az S1, S2 és S3 teljesítményszint között az önálló partíciós gyűjteményekbe?](#migrate-diy)
- [Milyen hatással vagyok az EA-ügyfelek?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Miért kerül kivonásra az S1, S2 és S3 szintű teljesítményszint?

Az S1, S2 és S3 teljesítményszint nem nyújt rugalmasságot a standard Azure Cosmos DB ajánlat számára. Az S1, S2, S3 teljesítményszint esetében az átviteli sebesség és a tárolókapacitás is előre be lett állítva, és nem nyújtott rugalmasságot. A Azure Cosmos DB mostantól lehetővé teszi az átviteli sebesség és a tárterület testreszabását, ami sokkal nagyobb rugalmasságot kínál az igényeinek megfelelő skálázáshoz.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hogyan hasonlítható össze az egypartíciós gyűjtemények és a particionált gyűjtemények az S1, az S2 és az S3 teljesítmény szintjével?

Az alábbi táblázat összehasonlítja az egypartíciós gyűjtemények, a particionált gyűjtemények, valamint az S1, S2 és S3 teljesítményszint esetében elérhető átviteli sebességet és tárolási lehetőségeket. Példa az USA 2. keleti régiójára:

| Kvóta neve  |Particionált gyűjtemény|Egyetlen partíciós gyűjtemény|S1|S2|S3|
|---|---|---|---|---|---|
|Maximális átviteli sebesség|Korlátlan|10K RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Minimális átviteli sebesség|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Maximális tárterület|Korlátlan|20 GB|20 GB|20 GB|20 GB|
|Díj (havi)|Átviteli sebesség: $6/100 RU/s<br><br>Tárterület: 0,25/GB|Átviteli sebesség: $6/100 RU/s<br><br>Tárterület: 0,25/GB|$25 USD|$50 USD|$100 USD|

EA-ügyfél? Ha igen, tekintse meg, hogyan érintettem, [Ha EA-ügyfél vagyok?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Mit kell tennem az adataim zavartalan elérésének biztosításához?

Ha S1, S2 vagy S3 gyűjteménysel rendelkezik, a gyűjteményt a [.net SDK használatával programozott módon](#migrate-diy)telepítse át egyetlen partíciós gyűjteményre. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hogyan változik a gyűjtemény az áttelepítés után?

Ha S1-es gyűjteménysel rendelkezik, áttelepítheti őket egyetlen, 400 RU/s sebességű partíciós gyűjteménybe. a 400 RU/s az egyetlen partíciós gyűjteményeknél elérhető legalacsonyabb átviteli sebesség. Az egypartíciós gyűjteményben lévő 400 RU/s díja azonban nagyjából megegyeznek az S1-gyűjtemény és a 250 RU/s használatával, így Ön nem fizet a további 150 RU/s számára.

Ha van S2-gyűjteménye, áttelepítheti őket egyetlen partíciós gyűjteménybe 1 K RU/s használatával. A rendszer nem módosítja az átviteli sebesség szintjét.

Ha rendelkezik S3-gyűjteménysel, áttelepítheti őket egyetlen partíciós gyűjteménybe, 2,5 K RU/s használatával. A rendszer nem módosítja az átviteli sebesség szintjét.

Ezekben az esetekben a gyűjtemény áttelepítése után testre szabhatja az átviteli sebességet, vagy igény szerint méretezheti, hogy a felhasználók számára alacsony késésű hozzáférést biztosítson. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hogyan változik a számlázási változás az egyetlen partíciós gyűjteményre való Migrálás után?

Tegyük fel, hogy 10 S1-es gyűjteményt tartalmaz, 1 GB tárterületet az USA keleti régiójában, és a 10 S1-es gyűjteményt 10 egypartíciós gyűjteménybe telepíti át a 400 RU/s (a minimális szint) értékre. A számla a következőképpen jelenik meg, ha a 10 egypartíciós gyűjteményt egy teljes hónapra vonatkozóan tartja:

:::image type="content" source="./media/performance-levels/s1-vs-standard-pricing.png" alt-text="Az S1 díjszabása 10 gyűjtemény esetében 10 gyűjtemény összehasonlításával egyetlen partíciós gyűjtemény díjszabása alapján" border="false":::

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Mi a teendő, ha több mint 20 GB tárterületre van szükségem?

Akár S1, S2 vagy S3 szintű teljesítménnyel rendelkezik, akár egyetlen partíciós gyűjteményrel rendelkezik, amelyek mindegyike 20 GB tárterülettel rendelkezik, a Azure Cosmos DB adatáttelepítési eszköz használatával áttelepítheti az adatait egy particionált gyűjteménybe, amely gyakorlatilag korlátlan tárhelyet biztosít. További információ a particionált gyűjtemények előnyeiről: [particionálás és skálázás Azure Cosmos DBban](partitioning-overview.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Válthatok az S1, az S2 és az S3 teljesítményszint között a tervezett áttelepítés előtt?

Csak az S1, S2 és S3 teljesítményű meglévő fiókok módosíthatók, és [a .net SDK használatával](#migrate-diy)programozott módon módosíthatók a teljesítményszint szintjei. Ha S1-ről, S3-ról vagy S3-ról egyetlen partíciós gyűjteményre vált, nem térhet vissza az S1, az S2 vagy az S3 teljesítmény szintjére.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hogyan Migrálás az S1, S2 és S3 teljesítményszint között az önálló partíciós gyűjteményekbe?

Az S1, az S2 és az S3 teljesítmény szintjéről is áttelepíthetők az egypartíciós gyűjtemények programozott [módon a .net SDK használatával](#migrate-diy). Ezt megteheti, mielőtt a tervezett áttelepítés kihasználja az egypartíciós gyűjteményeknél elérhető rugalmas átviteli sebességi lehetőségeket.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrálás egyetlen partíciós gyűjteményre a .NET SDK használatával

Ez a szakasz csak a gyűjtemény teljesítményi szintjének az [SQL .NET API](sql-api-sdk-dotnet.md)-val történő módosítását fedi le, a folyamat azonban hasonló a többi SDK-hoz.

Az alábbi kódrészlet a gyűjtemény átviteli sebességének a másodpercenkénti 5 000-kérelmekre való módosítására szolgál:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Az [MSDN webhelyen](/dotnet/api/microsoft.azure.documents.client.documentclient) további példákat tekinthet meg, és további információkat tudhat meg az ajánlati módszerekről:

* [**ReadOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync)
* [**ReadOffersFeedAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.readoffersfeedasync)
* [**ReplaceOfferAsync**](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync)
* [**CreateOfferQuery**](/previous-versions/azure/dn975114(v=azure.100))

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Milyen hatással vagyok az EA-ügyfelek?

Az EA-ügyfelek az aktuális szerződésük végéig érvényes áron lesznek védve.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az Azure Cosmos DBekkel kapcsolatos díjszabásról és adatkezelésről, tekintse meg ezeket az erőforrásokat:

1.  [Adatparticionálás Cosmos DBban](partitioning-overview.md). Ismerje meg az egypartíciós tároló és a particionált tárolók közötti különbséget, valamint tippeket, hogyan lehet zökkenőmentesen méretezni a particionálási stratégiát.
2.  [Cosmos db díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/). Ismerje meg az átviteli sebesség és a tárolás költségeit.
3.  [Kérelmek egységei](request-units.md) Megtudhatja, hogyan használható a különböző műveleti típusok átviteli sebessége, például olvasás, írás, lekérdezés.