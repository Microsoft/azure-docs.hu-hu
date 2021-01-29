---
title: Egyéni előfizetés Azure-beli foglalásának használata
description: Megtanulhatja értelmezni a használati adatokat annak megismeréséhez, hogyan lesz alkalmazva az Azure-foglalás a használatalapú fizetéses egyedi előfizetésre.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f6aad15d70157dda110fce233fbdafb7f3d896ad
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055059"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Használatalapú fizetéses egyéni előfizetés Azure-beli foglalása használatának ismertetése

A foglalási használat kiértékeléséhez használja a ReservationId a [foglalás oldalon](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) és a használati fájlból a [Azure Portal](https://portal.azure.com) .

Ha Ön egy Nagyvállalati Szerződéssel rendelkező ügyfél, tekintse meg [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md) című cikket.

Ez a cikk azt feltételezi, hogy a foglalás egyetlen előfizetésre van alkalmazva. Ha a foglalás több előfizetésre van alkalmazva, akkor a foglalási kedvezmény több használati CSV-fájlra is kiterjedhet.

## <a name="usage-for-reserved-virtual-machine-instances"></a>A Reserved Virtual Machine Instances használata

Az alábbi szakaszokban azt feltételezzük, hogy egy Standard_DS1_v2 Windows rendszerű virtuális gépet futtat az USA keleti régiójában, és a fenntartott virtuálisgép-példány adatai az alábbi táblázathoz hasonlóak:

| Mező | Érték |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Mennyiség |1|
|SKU | Standard_DS1_v2|
|Régió | eastus |

A virtuális gép hardveres része fedezve van, mivel az üzembe helyezett virtuális gép egyezik a foglalási attribútumokkal. A fenntartott virtuálisgép-példány által nem fedezett Windows-szoftverek megtekintéséhez tekintse meg [az Azure Reserved VM Instances Windows-szoftvereinek költségeit](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>A virtuális gépek CSV-fájljainak Statement szakasza

A CSV-fájl ezen szakasza a foglalás teljes használatát mutatja. Alkalmazza a **„Reservation-”** kifejezést tartalmazó szűrőt a **Meter Subcategory** mezőre. Az alábbi képernyőképhez hasonló eredmény jelenik meg:

![Képernyőkép a foglalási használat szűrt részleteiről és díjairól](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A **Reservation-Base VM** sor a foglalás által fedezett órák teljes számát mutatja. Ebben a sorban a $0.00 érték szerepel, mivel a foglalás fedezi. A **Reservation-Windows Svr (1 Core)** sor a Windows-szoftver költségét fedezi.

### <a name="daily-usage-section-of-csv-file"></a>A CSV-fájl Daily usage szakasza

Szűrjön az **Additional Info** mezőre, és adja meg a **foglalásazonosítót**. Az alábbi képernyőképen a foglaláshoz kapcsolódó mezők láthatók.

![Képernyőfelvétel a napi használat részleteiről és díjairól](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. Az **Additional Info** mező **ReservationId** értéke a virtuális gépre alkalmazott foglalást jelöli.
2. A **ConsumptionMeter** érték a virtuális gép fogyasztásmérő-azonosítója.
3. A **Reservation-Base VM** **Meter Subcategory** sor a $0 költséget jelöli a Statement szakaszban. A virtuális gép futtatásának költségét már kifizette a foglalás.
4. A **Meter ID** a foglalás fogyasztásmérő-azonosítóját jelöli. A fogyasztásmérő költsége $0. Ez a fogyasztásmérő-azonosító minden olyan virtuális gép esetében megjelenik, amely jogosult a foglalási kedvezményre.
5. A Standard_DS1_v2 egy olyan virtuális gép, amely egy virtuális processzorral rendelkezik, és az Azure Hybrid Benefit nélkül van üzembe helyezve. Így ez a fogyasztásmérő fedezi a Windows-szoftver többletköltségét. A D sorozatú, 1 magos virtuális gépekhez tartozó fogyasztásmérő megkereséséhez tekintse meg [az Azure Reserved VM Instances Windows-szoftvereinek költségeit](reserved-instance-windows-software-costs.md). Ha rendelkezik az Azure Hybrid Benefittel, ez a többletköltség nem érvényes Önre.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Az SQL Database- és a Cosmos DB-foglalások használata

Az alábbi szakaszok az Azure SQL Database példáján keresztül ismertetik a használati jelentést. Ugyanezeket a lépéseket használhatja az Azure Cosmos DB használatának lekéréséhez is.

Tegyük fel, hogy egy 4. generációs SQL Database-adatbázist futtat az USA keleti régiójában, és a foglalási adatok az alábbi táblázathoz hasonlóan néznek ki:

| Mező | Érték |
|---| --- |
|Foglalásazonosító |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Mennyiség |2|
|Product| SQL Database Gen 4 (2 Core)|
|Régió | eastus |

### <a name="statement-section-of-csv-file"></a>A CSV-fájl Statement szakasza

Szűrjön a **Reserved Instance Usage** fogyasztómérőnévre, és válassza ki a szükséges **Meter Category** értéket: az Azure SQL Database-t vagy az Azure Cosmos DB-t. Az alábbi képernyőképhez hasonló eredmény jelenik meg:

![Képernyőkép egy Fogyasztásmérő kategóriája típusú bejegyzésről.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

A **Reserved Instance Usage** sor a magok foglalás által fedezett óráinak teljes számát mutatja. Ebben a sorban $0 értékű díj szerepel, mivel a foglalás fedezi a költséget.

### <a name="detail-section-of-csv-file"></a>A CSV-fájl Detail szakasza

Szűrjön az **Additional Info** mezőre, és adja meg a **foglalásazonosítót**. Az alábbi képernyőképen a fenntartott SQL Database-kapacitás foglalásához kapcsolódó mezők láthatók.

![Képernyőkép egy fenntartott kapacitáshoz tartozó CSV-fájl részleteiről.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. Az **Additional Info** mező **ReservationId** értéke az SQL Database-erőforrásra alkalmazott fenntartott SQL Database-kapacitás foglalását jelöli.
2. A **ConsumptionMeter** érték az SQL Database-erőforrás fogyasztómérő-azonosítóját jelöli.
3. A **Meter Id** a foglalás fogyasztásmérőjét jelöli. A fogyasztásmérő költsége $0. Ez a fogyasztómérő-azonosító minden olyan SQL Database-erőforrás CSV-fájljában megjelenik, amely jogosult a foglalási kedvezményre.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../../azure-sql/database/reserved-capacity-overview.md)
- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
- [A foglalási kedvezmény alkalmazásának ismertetése](../manage/understand-vm-reservation-charges.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)