---
title: Azure-foglalások költségének költséghelyi elszámolása
description: Megtudhatja, hogyan tekintheti meg az Azure-foglalások költségeit a költséghelyi elszámoláshoz.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: aba6ea467788c51d179ef9377243efb6035b6f98
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148351"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure-foglalások költségének költséghelyi elszámolása

A Nagyvállalati Szerződéssel és a Microsoft Ügyfélszerződéssel rendelkező számlázási olvasók láthatják a foglalások amortizált költségadatait. A költségadatokból költséghelyi elszámolást készíthetnek egy előfizetés, erőforráscsoport, erőforrás vagy címke pénzbeli értékéről a partnereiknek. Az amortizált adatokban a tényleges ár az arányosított óránkénti foglalási költség. A költség az erőforrás foglaláshasználatának teljes költsége az adott napra vonatkozóan.

Az önálló előfizetéssel rendelkező felhasználók a használati fájlból szerezhetik meg az amortizált költségadatokat. Ha egy erőforrás foglalási kedvezményt kap, a használati fájl *AdditionalInfo* szakasza tartalmazza a foglalás adatait. További információért lásd [a használati adatok Azure Portalról való letöltését](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv) bemutató cikket.

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Foglalások költséghelyi elszámolási adatainak beszerzése költséghelyi elszámoláshoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen a **Költségkezelés + számlázás** elemre.
1. A **Tényleges költségek** területen válassza az **Amortizált költség** metrikát.
1. Ha azt szeretné megtekinteni, hogy egy adott foglalás mely erőforrásokat használta, alkalmazzon szűrőt a **Foglalás** esetében, és válassza ki a kívánt foglalásokat.
1. A **Részletesség** értéke legyen **Havi** vagy **Napi**.
1. A diagram típusa **Táblázat** legyen.
1. Állítsa a **Csoportosítás** lehetőséget **Erőforrás** értékre.

[![Példa foglalási erőforrás költségeire, amelyek költséghelyi elszámoláshoz használhatóak](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

A következő videó bemutatja, hogyan tekinthetők meg az Azure Portalon a foglalás kihasználtságának költségei.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](manage-reserved-vm-instance.md) szakaszt.
- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [A Reservations kezelése az Azure-ban](manage-reserved-vm-instance.md)