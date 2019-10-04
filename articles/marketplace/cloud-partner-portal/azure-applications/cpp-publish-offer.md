---
title: Az Azure application ajánlat közzététele |} Az Azure Marketplace-en
description: A folyamat és az Azure Marketplace-en az Azure application ajánlat közzétételének lépéseit ismerteti.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 2326ce1a591d1276dbaf9c7f3238f7214e5134ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942911"
---
# <a name="publish-azure-application-offer"></a>Azure-alkalmazásajánlat közzététele

Az adatokat azáltal, hogy az ajánlat létrehozása után a **új ajánlat** lap, közzéteheti az ajánlatot. Válassza ki **közzététel** a közzétételi folyamat elindításához.

Az alábbi ábrán látható a fő lépések egy "az élő esemény indításra" ajánlat a közzétételi folyamat.

![Az ajánlat közzétételi lépéseket](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Közzétételi lépések részletes leírása

A következő táblázat sorolja fel és közzétételi lépésre ismerteti, és egy becsült minden egyes időtartam biztosít.  Időpontokban becslése a "days" munkanapon belül vannak meghatározva, amely zárja ki a hétvégéket és a munkaszüneti napokat.

|  **Közzétételi lépés**           | **idő**    | **Leírás**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Előfeltételek ellenőrzése         | < 15 perc    | Információt kínálnak, és kínálnak a rendszer érvényesíti a beállításokat.                        |
| Befolyásolt bevétel beállítások érvényesítése | < 15 perc  | Az ajánlat Azure-Erőforrás kihasználtsága attribution be van jelölve.             |
| Tanúsítvány                  | < 1 nap     | Az ajánlat az Azure hitelesítő csapata által elemzett. Az ajánlat a vírusok, kártevő szoftverek, biztonsági, megfelelőségi és biztonsági problémákat vizsgálja. Az ajánlat be van jelölve, hogy megfelel-e az összes megfelelőségi feltételeket megtekintéséhez. További információkért lásd: [Előfeltételek](./cpp-prerequisites.md). Ha problémát talál, visszajelzés érkezett. |
| Teszt meghajtó ellenőrzése          | < 2 óra   | (Nem kötelező) Ha jelen egy Test Drive, a Microsoft ellenőrzi, hogy azt telepíthető és replikálja.  |
| Formátumokat támogató csomagolási és Érdeklődők generálása regisztráció | < 1 óra  | Ajánlat technikai eszközök ügyfél használatra vannak csomagolva, és az átfutási rendszerek konfigurált és telepített. |
|  Közzétevő jóváhagyás             |  Manuális    | Végső közzétevő áttekintése és az ajánlat élesíti előtt megerősítést. Az ajánlat már elérhető előzetes verzióban.  Az ajánlat (az ajánlat információk lépések) a kijelölt előfizetésekben telepíthet, hogy megfelelnek-e a szükséges összes követelmény.  Miután ellenőrizte az ajánlatot, válassza ki a **élő** , az ajánlat helyezheti át a következő lépéssel. |
| A Microsoft áttekintése                | 7 – 14 nap | A Microsoft azok alkalmazásfüggőségeit áttekinti az Azure-alkalmazásokat, és e-mailt küld, ha problémák.  Ebben a lépésben hossza attól függ, hogy az alkalmazást, a felderített hibák, és hogyan azonnal reagálhat rájuk bonyolultságát.  |
| Élő                           | < 1 nap | Az ajánlat nyilvánosan, a megadott régióban replikálja, és nyilvánosan elérhetővé. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Figyelheti a közzétételi folyamat a **állapot** az ajánlatra, a Cloud Partner portálra a lap.

![Az Azure-alkalmazás ajánlat állapota lap](./media/offer-status-tab.png)

Miután elvégezte a közzétételi folyamat, az ajánlat jelenik meg a [Microsoft Azure Marketplace-alkalmazás kategóriához](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Cloud Solution Providers (CSP) partner csatorna vehetnek részt már elérhető.  Lásd: [Cloud Solution Providers](../../cloud-solution-providers.md) további tájékoztatást a marketing, az ajánlat keretében a Microsoft CSP partner-csatornákon.

## <a name="errors-and-review-feedback"></a>Hibák és a felülvizsgálati visszajelzés

Az ajánlat közzétételi állapotának megjelenítése mellett az **állapot** lapon megjelenik a hibaüzenetek és visszajelzései olyan közzétételi lépéseket, ahol a rendszer észlelt egy problémát.  Ha a probléma fontos, majd közzététele megszakítva.  Kell majd javítsa ki a jelentett hiba/hibák meghatározásához, és tegye közzé újra az ajánlatot.  Mivel a **Microsoft felülvizsgálati** lépés egy kiterjedt át kell tekinteni az ajánlat és a kapcsolódó technikai eszközökre (különösen az Azure Resource Manager-sablon) jelöli, a problémák általában bemutatják, a lekéréses kérelmet (PR) hivatkozásokat.  Megtekintése, és ezek a lekéréses kérelmek megválaszolásához magyarázatát lásd [kezelésének áttekintése visszajelzés](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>További lépések

Egy vagy több közzétételi lépéseket, hibát, ha kell, javítsa ki azokat, és közzé az ajánlatot még.  Ha a kritikus fontosságú problémáit a program hibát a **áttekintése a Microsoft** lépésben kell [kezelni a felülvizsgálati visszajelzés](./cpp-handling-review-feedback.md) elérése a Microsoft által tekintse át az Azure DevOps-tárház csapata.

Miután egy Azure-alkalmazáshoz sikeresen közzé lett téve, akkor is [a létező ajánlat frissítése](./cpp-update-existing-offer.md) megfelelően a változó üzleti vagy technikai követelményeket. 
