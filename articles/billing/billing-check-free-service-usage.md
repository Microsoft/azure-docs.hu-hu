---
title: Az ingyenes Azure-szolgáltatások figyelése és nyomon követésére használati |} A Microsoft Docs
description: Ismerkedjen meg az ingyenes szolgáltatások használatának ellenőrzése. Az Azure portal és a használati csv használ.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 4e940a12cd57ef136cfd9ead298f9afcd2d6ad1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617781"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Az ingyenes Azure-fiókkal ingyenes szolgáltatásokat használatának ellenőrzése 

Meg nem kell fizetnie szolgáltatásokért díjmentesen ingyenes Azure-fiókkal, kivéve, ha ezek a szolgáltatások korlátozásait meghaladja. Továbbra is a korlátokat, vagy használhatja az Azure portal vagy a használati fájlban figyeléséhez és ingyenes szolgáltatások használatának nyomon követéséhez. 

## <a name="check-usage-on-the-azure-portal"></a>Ellenőrizze a használatot az Azure Portalon

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2.  Válassza ki a bal oldali navigációs területen **minden szolgáltatás**.

3.  Válassza az **Előfizetések** lehetőséget.

4.  Válassza ki a létrehozott való regisztráció során az ingyenes fiók előfizetést.

    ![Előfizetések bemutató képernyőkép](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Az Áttekintés szakasz bemutatja, például az előfizetés-Azonosítójára, az előfizetéssel kapcsolatos alapvető információkat kínálnak, típusa és az előfizetés nevét. Ha lenne jár le a fiókjához tartozó ingyenes kreditekkel információkat is talál.

    ![Előfizetés alapvető információkat bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Görgessen le a jelenlegi és az előre jelzett költség található információkat. A költségek az ingyenes fiókkal nem szereplő szolgáltatások használatát és a ingyenesen használhatja a szolgáltatásokat meghaladó használat tartalmazza. 

    ![Előfizetési költség információkat bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Az Áttekintés szakasz utolsó része belefoglalja rendelkezik a ingyenes szolgáltatások használatát. 

    ![Ingyenes szolgáltatások használatának bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A táblázatban szerepel a következő oszlopokat:

* **Fogyasztásmérő neve:** A rendszer, elérheti az érték mértékegységét azonosítja. Fogyasztásmérő leképezés szolgáltatás kapcsolatos további információkért lásd: [mérőszám leképezés ingyenes szolgáltatás ismertetése](billing-understand-free-service-meter-mapping.md).
* **Használat/korlát:** Aktuális havi használat és a mértékegységek korlátot. Ezeket az adatokat az állapotsorban is megkeresheti.
* **Állapot:** A mérőszámok használati állapota. A használati minta alapján, akkor az alapszabály egyikét.
  * **Nincs használatban:** Még nem használta az a fogyasztásmérő, vagy a mérőszám a használata még nem elérte a számlázási rendszerhez.
  * **Túllépte a \<dátum >:** Túllépte a korlátot, a mértékegységek a \<dátum >.
  * **Valószínűleg nem lépi túl:** Most már valószínűleg nem lépi túl a korlátot, a mértékegységek.
  * **Túllépés \<dátum >:** Valószínűleg meghaladja a korlátot, a mérő az Ön \<dátum >.

## <a name="check-usage-through-the-usage-file"></a>Ellenőrizze a használatot a használatot részletező fájl segítségével

A használati fájlban az Azure-előfizetéshez a részletes információkat szolgáltat. A havi vagy a napi használati fájlt tölthet le Azure fiókkezelési központjába. Ismerje meg, hogyan a használatot részletező fájl letöltése és megismerése a hozzáférés szükséges, lásd: [első számlák és használati adatok](billing-download-azure-invoice-daily-usage-date.md). A használatot részletező fájl oszlopai kapcsolatos további információkért lásd: [a használat feltételeinek értelmezése](billing-understand-your-usage.md).

A használatot részletező fájl rendelkezik egyaránt ingyenes vagy fizetős szolgáltatás méretkihasználtsági adatait. Ingyenes szolgáltatás mérőszámok kellene **ingyenes** hozzáfűzi a végén a fogyasztásmérő nevét. Ingyenes mérőszámok, nyissa meg a fájlt az excel- és szűrő keresése a **mérőszám kategóriája oszlop** szöveg rendelkező cellák **– ingyenes** (használata Szövegszűrők &rarr; tartalmazza szűrő) &nbsp;

![Ingyenes szolgáltatások használatának bemutató képernyőkép](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).