---
title: A Purview Studio használata
description: Ez a fogalmi cikk az Azure hatáskörébe Studio használatát ismerteti.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: ca25bbb72ff853f819f3e8ce4e0092ddb762b156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183811"
---
# <a name="use-purview-studio"></a>A Purview Studio használata

Ez a cikk áttekintést nyújt az Azure hatáskörébe tartozó főbb funkciókról.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív hatáskörébe tartozó fiók már létre van hozva Azure Portalban, és a felhasználónak jogosultsága van a hatáskörébe studióhoz való hozzáféréshez.

## <a name="launch-purview-account"></a>A hatáskörébe tartozó fiók elindítása

* A hatáskörébe tartozó fiók elindításához lépjen a Azure Portal a hatáskörébe tartozó fiókok elemre, válassza ki azt a fiókot, amelyet el szeretne indítani, és indítsa el a fiókot.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Képernyőkép a kijelölésről az Azure hatáskörébe tartozó fiók katalógusának elindításához.":::

* A hatáskörébe tartozó fiók indításának másik módja, ha `https://web.purview.azure.com` a fiók megnyitásához kiválasztja a **Azure Active Directory** és a fiók nevét.

## <a name="home-page"></a>Kezdőlap

A **Kezdőlap** az Azure hatáskörébe tartozó ügyfél kezdőlapja.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Képernyőkép a kezdőlapról.":::

Az alábbi lista a **Kezdőlap** főbb szolgáltatásait foglalja össze. A listában szereplő egyes számok az előző képernyőképen szereplő Kiemelt számnak felelnek meg.

1. A katalógus rövid neve. Megadhatja a katalógus nevét a **felügyeleti központ**  >  **fiókjának adataiban**.

2. A katalógus Analytics a következőket jeleníti meg:
    - Felhasználók, csoportok és alkalmazások
    - Adatforrások
    - Objektumok
    - Szószedet kifejezései

3. A keresőmező lehetővé teszi az adategységek keresését az adatkatalógusban.

4. A gyors hozzáférési gombok hozzáférést biztosítanak az alkalmazás gyakran használt funkcióihoz. A megjelenített gombok a felhasználói fiókhoz rendelt szerepkörtől függenek.

    - Az *adatkurátorok* számára a gombok a **Knowledge Center**, az **eszközök tallózása**, a **szószedetek kezelése** és az elemzések **megtekintése**.
    - Adatolvasó esetén a Kiemelt gombok a **Knowledge Center**, az **eszközök tallózása**, a **Szószedet megtekintése** és az *elemzések* **megtekintése**.
    - Az adatforrás *adminisztrátori* adatkezelője esetében  +  a Kiemelt gombok a **Knowledge Center**, az **adatforrások regisztrálása**, az **eszközök tallózása** és a **Szószedet kezelése**.
    - Az adatforrás- *adminisztrátor*  +  *adatolvasójának* Kiemelt gombjai a **Knowledge Center**, az **adatforrások regisztrálása**, az **eszközök tallózása** és a **Szószedet megtekintése**.

5. A bal oldali navigációs sáv segít megkeresni az alkalmazás fő lapjait. A megjelenített gombok a felhasználói fiókhoz rendelt szerepkörtől függenek.

    - Az *adatkurátorok* esetében a gombok a **Kezdőlap**, a Szószedet **, az** elemzések és a **felügyeleti központ**.
    - Az *adatolvasóhoz* a gombok a **Kezdőlap**, a Szószedet **, az** elemzések és a **felügyeleti központ**.
    - Az adatforrás *adminisztrátori* adatkezelője  +  */olvasója* számára a gombok a **Kezdőlap**, a **forrás**, a **Szószedet**, az elemzés és a **felügyeleti központ**.
  
6. A **legutóbb elérhető** lap a közelmúltban elért adategységek listáját jeleníti meg. További információ az adategységek eléréséről: [Keresés a Data Catalog](how-to-search-catalog.md) és Tallózás az eszközök [típusa szerint](how-to-browse-catalog.md#browse-experience).  A **saját elemek** lap a bejelentkezett felhasználó tulajdonában lévő adategységek listája.
7. A **hasznos hivatkozások** a régiók állapotára, a dokumentációra, a díjszabásra, az áttekintésre és a hatáskörébe tartozó állapotokra mutató hivatkozásokat
8. A felső navigációs sáv a kibocsátási megjegyzések/frissítések, a Change hatáskörébe tartozó fiók, az értesítések, a Súgó és a visszajelzés szakaszban található információkat tartalmazza.

## <a name="knowledge-center"></a>Knowledge Center

A Tudásközpont segítségével megtalálhatja a hatáskörébe tartozó összes videót és oktatóanyagot.

## <a name="guided-tours"></a>Interaktív túrák

Az Azure hatáskörébe Studióban minden egyes UX idegenvezetést biztosít az oldal áttekintéséhez. Az interaktív bemutató elindításához válassza a **Súgó** lehetőséget a felső sávon, és válassza az **interaktív túrák** lehetőséget.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Képernyőkép az interaktív bemutatóról.":::

> [!Important]
   > Az adatforrás-rendszergazdai szerepkör önmagában nem fér hozzá a hatáskörébe studióhoz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Rendszerbiztonsági tag hozzáadása](tutorial-scan-data.md)
