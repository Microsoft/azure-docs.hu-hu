---
title: Szószedet jelentés az adatairól a hatáskörébe-elemzések használatával
description: Ez a útmutató ismerteti, hogyan tekinthetők meg és használhatók a hatáskörébe tartozó elemzések az adatairól.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095850"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Az Azure hatáskörébe tartozó adataival kapcsolatos Szószedet

Ez a útmutató ismerteti, hogyan lehet hozzáférni, megtekinteni és szűrni a hatáskörébe tartozó Szószedeti jelentéseket az adataihoz.

Ebben a útmutatóban megismerheti a következőket:

> [!div class="checklist"]
> - Keresse meg a hatáskörébe tartozó fiókját
> - A madár szem nézetének beolvasása az adatairól

## <a name="prerequisites"></a>Előfeltételek

A hatáskörébe tartozó ismeretek megismerése előtt győződjön meg arról, hogy végrehajtotta a következő lépéseket:

- Állítsa be az Azure-erőforrásokat, és töltse fel a fiókot adatokkal

- A forrás típusának beállítása és a vizsgálat befejezése

- Szószedet beállítása és eszközök csatolása a Szószedet feltételeihez

További információ: [adatforrások kezelése az Azure hatáskörébe (előzetes verzió)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>A hatáskörébe tartozó Szószedet-felismerések használata

Az Azure hatáskörébe tartozik a Szószedet feltétele, és csatolható az eszközökhöz. Később megtekintheti a Szószedet-eloszlást a Szószedetbeli áttekintésekben. Ez azt jelzi, hogy a Szószedet állapota az eszközökhöz csatolt feltételek szerint van. Emellett az állapot és a szerepkörök eloszlása alapján is megadja a feltételeket a felhasználók száma szerint.

**A Szószedet-áttekintések megtekintéséhez:**

1. Nyissa meg a Azure Portal **Azure hatáskörébe** tartozó [példány képernyőjét](https://aka.ms/purviewportal) , és válassza ki a hatáskörébe tartozó fiókot.

1. Az **Áttekintés** lap első **lépések** szakaszában válassza a hatáskörébe tartozó fiók **indítása** csempét.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="A hatáskörébe tartozó Azure Portal elindítása":::

1. A hatáskörébe tartozó **kezdőlapon** válassza az eredmények **megtekintése** csempét az információhoz való hozzáféréshez  :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Tekintse meg az Azure Portal":::

1. Az **Áttekintés** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: területen válassza a **Szószedet** lehetőséget a hatáskörébe tartozó **Szószedet** -információk jelentés megjelenítéséhez.

A **Szószedetbeli** információk üzleti felhasználóként és értékes információkkal szolgálnak a szervezet számára jól definiált Szószedet fenntartásához.

1. A jelentés **magas szintű KPI-** kkel kezdődik, amelyek az **_összes kifejezést_ megjelenítik *a hatáskörébe tartozó fiókban***. Ezen értékek mindegyike segítséget nyújt a Szószedet állapotának azonosításában.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Szószedet-áttekintési KPI megtekintése"::: 


2. **A használati feltételek szakaszának pillanatképe** (fent látható) megmutatja, hogy a kifejezés állapota " ***_",*_"_"_*, "*_Alert_*_" és*"_lejárt_** ".

3. A **továbbiak megtekintése** elemre kattintva megtekintheti a különböző állapotú kifejezéseket, és további részleteket tudhat meg a **_stewards_*_ és a _*_szakértőkről_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="A feltételek és az eszközök nélkül készített Pillanatképek":::  

4. Ha a (z) "továbbiak" megtekintése a (z) "az **assets" jóváhagyott feltételeknél** lehetőségre kattint, a rendszer az információk alapján megkeresi az _ *Szószedet** kifejezés részletes oldalát, ahol tovább megtekintheti az eszközök listáját a csatolt feltételekkel. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="A Szószedet bepillantást nyerhet"::: 

4. A Szószedet elemzése lapon megtekintheti a **hiányos feltételek** eloszlását a hiányzó információk típusa szerint. A diagram a hiányzó definícióval rendelkező kifejezések számát jeleníti meg: _ ***, _*_hiányzó szakértő_: _ *,*_hiányzó Steward_*_ és _*_több mező hiányzik_** .

1. A hiányzó információkat tartalmazó feltételek megtekintéséhez kattintson a ***részletek megtekintése** _ * hiányos feltételek * * elemre. A hiányzó információk megadásához és a Szószedet kifejezésének befejezéséhez navigáljon a Szószedet részletes adatai lapra.

## <a name="next-steps"></a>Következő lépések

További információ [a Szószedet-](./how-to-create-import-export-glossary.md) kifejezés létrehozásáról a Szószedet használatával