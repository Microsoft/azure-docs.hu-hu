---
title: Azure-foglalások költségének költséghelyi elszámolása
description: Megtudhatja, hogyan tekintheti meg az Azure-foglalások költségeit a költséghelyi elszámoláshoz.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532753"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure-foglalások költségének költséghelyi elszámolása

A Nagyvállalati Szerződéssel és a Microsoft Ügyfélszerződéssel rendelkező számlázási olvasók láthatják a foglalások amortizált költségadatait. A költségadatokból költséghelyi elszámolást készíthetnek egy előfizetés, erőforráscsoport, erőforrás vagy címke pénzbeli értékéről a partnereiknek. Az amortizált adatokban a tényleges ár az arányosított óránkénti foglalási költség. A költség az erőforrás foglaláshasználatának teljes költsége az adott napra vonatkozóan.

Az önálló előfizetéssel rendelkező felhasználók a használati fájlból szerezhetik meg az amortizált költségadatokat. Ha egy erőforrás foglalási kedvezményt kap, a használati fájl *AdditionalInfo* szakasza tartalmazza a foglalás adatait. További információért lásd [a használati adatok Azure Portalról való letöltését](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv) bemutató cikket.

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Tekintse meg a foglalás használati adatait a vissza- és költség-visszahasználattal kapcsolatban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a **Költségkezelés + számlázás** elemre. 
3. Válassza a **Költségelemzés lehetőséget** a bal oldali navigációs sávon 
4. A **Tényleges költségek** területen válassza az **Amortizált költség** metrikát.
5. Ha azt szeretné megtekinteni, hogy egy adott foglalás mely erőforrásokat használta, alkalmazzon szűrőt a **Foglalás** esetében, és válassza ki a kívánt foglalásokat.
6. A **Részletesség** értéke legyen **Havi** vagy **Napi**.
7. A diagram típusa **Táblázat** legyen.
8. Állítsa a **Csoportosítás** lehetőséget **Erőforrás** értékre.

[![Példa foglalási erőforrás költségeire, amelyek költséghelyi elszámoláshoz használhatóak](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Az alábbi videó bemutatja, hogyan lehet megtekinteni a foglalások használati költségeit az előfizetésben, az erőforráscsoportban és az erőforrásszinten a Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>A visszamutatja és vissza nem számolt adatok lekért adatait
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a **Költségkezelés + számlázás** elemre. 
3. Válassza **az Exportálás** lehetőséget a bal oldali navigációs sávon 
4. Kattintson a **Hozzáadás gombra**
5. Válassza az Amortized cost (Amortizált költség) lehetőséget metrikagombként, és adja meg az exportálás beállítását

A foglalási kedvezményt kapó használat effectivePrice ára a foglalás arányos költsége (nulla helyett). Ezzel megtudhatja a foglalási fogyasztás előfizetésenkénti, erőforráscsoportonkénti, vagy erőforrásonkénti pénzben kifejezett értékét, és segít abban, hogy a foglalási használatot belsőleg számolja el költséghelyi elszámolással. Az adathalmaz tartalmaz továbbá fel nem használt foglalási órákat. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Azure fogyasztási és foglalási használati adatok lekérése API használatával

Az adatokat lekérheti az API használatával, vagy letöltheti azokat az Azure Portalról.

Az új adatok lekéréséhez hívja meg a [Usage Details API-t](/rest/api/consumption/usagedetails/list). A terminológiával kapcsolatos részletek megismeréséhez tekintse meg a [használattal kapcsolatos kifejezéseket](../understand/understand-usage.md) ismertető cikket.

Itt látható egy példahívás a Usage Details API-hoz:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Ha többet szeretne tudni az {enrollmentId} és {billingPeriodId} változókról, tekintse meg a [Használati adatok – lista](/rest/api/consumption/usagedetails/list) API-cikket.

A mérőket és szűrőket tartalmazó következő táblázat segítséget nyújt a gyakori foglalási problémák megoldásához.

| **Az API-adatok típusa** | API-hívásművelet |
| --- | --- |
| **Összes díj (használat és vásárlások)** | a {metric} kicserélése ActualCostra |
| **Foglalási kedvezményben részesült használat** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/reservationId%20ne%20 |
| **Foglalási kedvezményben nem részesült használat** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/reservationId%20eq%20 |
| **Amortizált díjak (használat és vásárlások)** | a {metric} kicserélése AmortizedCostra |
| **Jelentés a nem használt foglalásokról** | a {metric} kicserélése AmortizedCostra<br><br>a {filter} kicserélése a következőre: properties/ChargeType%20eq%20'UnusedReservation' |
| **Foglalásvásárlások** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/ChargeType%20eq%20'Purchase'  |
| **Visszatérítések** | a {metric} kicserélése ActualCostra<br><br>a {filter} kicserélése a következőre: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Az új használati adatokat tartalmazó CSV-fájl letöltése

Ha Ön EA-rendszergazda, letöltheti az új használati adatokat tartalmazó CSV-fájlt a Azure Portal. Ezek az adatok nem érhetők el az EA Portalról (ea.azure.com). Az új adatok megtekintéséhez le kell töltenie a felhasználási adatokat tartalmazó fájlt az Azure Portalról (portal.azure.com).

Lépjen a [Költségkezelés + számlázás](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts) menüponthoz az Azure Portalon.

1. Válassza ki a számlázási fiókot.
2. Kattintson a **Felhasználás + díjak** lehetőségre.
3. Kattintson a **Letöltés** gombra.  
![Példa a használati adatokat tartalmazó CSV-fájl letöltési helyére az Azure Portalról](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. A **Usage Details (Használati adatok) alatt** válassza az **Amortized usage data (Amortizált használati adatok) lehetőséget.**

A letölteni kívánt CSV-fájlok a tényleges költségeket és az amortizált költségeket is tartalmazzák.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések
- Az Azure Reservations használati adataival kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Nagyvállalati Szerződés és Microsoft Ügyfélszerződés költségek és használat](understand-reserved-instance-usage-ea.md)
 
