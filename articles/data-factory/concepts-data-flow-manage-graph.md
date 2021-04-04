---
title: A megfeleltetési adatfolyam gráfjának kezelése
description: A leképezési Adatfolyam-diagram hatékony kezelése és szerkesztése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: f55364fe0eabae4bb1698bd655cde8d3e01b127a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590460"
---
# <a name="managing-the-mapping-data-flow-graph"></a>A megfeleltetési adatfolyam gráfjának kezelése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatfolyamatok leképezése egy tervezési felületen keresztül történik, amely az adatfolyam-gráfot ismeri. A gráfban az átalakítási logika balról jobbra van kialakítva, és a további adatfolyamok felül lesznek adva. Új átalakítás hozzáadásához válassza a meglévő átalakítás jobb alsó sarkában látható plusz jelre.

![Vászon](media/data-flow/canvas-2.png)

Mivel az adatforgalom összetettebb, a következő módszerekkel kezelheti és felügyelheti az adatfolyam-diagramot. 

## <a name="moving-transformations"></a>Átalakítások áthelyezése

Az adatfolyamatok leképezése során a csatlakoztatott transzformációs logika egy készletét nevezzük **streamnek**. A **bejövő adatfolyam** mező azt határozza meg, hogy melyik adatfolyam táplálja az aktuális transzformációt. Minden átalakítás egy vagy két bejövő streamtel rendelkezik a függvénytől függően, és kimeneti adatfolyamot jelöl. A bejövő adatfolyamok kimeneti sémája határozza meg, hogy az aktuális átalakítás milyen oszlop-metaadatokat hivatkozhat.

![Csomópont áthelyezése](media/data-flow/move-nodes.png "csomópont áthelyezése")

A pipeline-vászontól eltérően az adatfolyam-átalakítások nem szerkeszthetők a fogd és vidd modell használatával. Ha módosítani szeretné a bejövő adatfolyamot, vagy "áthelyez" egy átalakítást, válasszon másik értéket a **bejövő adatfolyam** legördülő listából. Ha ezt teszi, a rendszer az összes alsóbb rétegbeli átalakítást a szerkesztett transzformáció mellett helyezi át. A gráf automatikusan frissül az új logikai folyamat megjelenítéséhez. Ha a bejövő adatfolyamot olyan átalakításra módosítja, amely már rendelkezik alsóbb rétegbeli átalakítással, akkor létrejön egy új ág vagy párhuzamos adatfolyam. További információ [az új ágakról a leképezési](data-flow-new-branch.md)adatfolyamban.

## <a name="hide-graph-and-show-graph"></a>Gráf elrejtése és grafikon megjelenítése

Az átalakítás szerkesztése során kiterjesztheti a konfigurációs panelt, hogy a teljes vászonra felvegye a diagramot. Kattintson a vászon jobb oldalán található felfelé irányuló Chevron elemre.

![Gráf elrejtése](media/data-flow/hide-graph.png "gráf elrejtése")

Ha a gráf rejtett, a **tovább** vagy az **előző** gombra kattintva átválthat a streamek közötti átalakítások között. A diagram megjelenítéséhez kattintson a lefelé néző francia idézőjelre.

![Gráf megjelenítése](media/data-flow/show-graph.png "gráf megjelenítése")

## <a name="searching-for-transformations"></a>Átalakítások keresése

A gráfban található átalakítás gyors megtalálásához kattintson a nagyítási beállítás feletti **Keresés** ikonra.

![A képernyőképen a Keresés gomb látható.](media/data-flow/search-1.png "Keresési gráf")

A transzformációk neve vagy leírása alapján megkeresheti az átalakítást.

![Képernyőfelvétel: a keresés szövegmező.](media/data-flow/search-2.png "Keresési gráf")

## <a name="hide-reference-nodes"></a>Hivatkozási csomópontok elrejtése

Ha az adatfolyam bármely illesztési, keresési, létező vagy Union-átalakítással rendelkezik, az adatfolyam az összes bejövő adatfolyamra hivatkozó csomópontokat jelenít meg. Ha szeretné minimálisra csökkenteni a felhasznált vertikális terület méretét, csökkentheti a hivatkozási csomópontjait. Ehhez kattintson a jobb gombbal a vászonra, és válassza a **hivatkozási csomópontok elrejtése** lehetőséget.

![Hivatkozási csomópontok elrejtése](media/data-flow/hide-reference-nodes.png "Hivatkozási csomópontok elrejtése")

## <a name="next-steps"></a>Következő lépések

Az adatfolyam-logika befejezése után kapcsolja be a [hibakeresési módot](concepts-data-flow-debug-mode.md) , és tesztelje az adatelőnézetben.
