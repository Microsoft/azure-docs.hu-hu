---
title: 'ML Studio (klasszikus): webszolgáltatások felhasználása az Excelben – Azure'
description: A Azure Machine Learning Studio (klasszikus) megkönnyíti a webszolgáltatások közvetlen meghívását az Excelből anélkül, hogy kódot kellene írnia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517858"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása az Excelből

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

A Azure Machine Learning Studio (klasszikus) megkönnyíti a webszolgáltatások közvetlen meghívását az Excelből anélkül, hogy kódot kellene írnia.

Ha az Excel 2013-as (vagy újabb) vagy az Excel online-t használja, javasoljuk, hogy használja az Excel [Excel-bővítményt](excel-add-in-for-web-services.md).



## <a name="steps"></a>Lépések
Webszolgáltatás közzététele. [3. Oktatóanyag: a hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md) elmagyarázza, hogyan teheti meg. Jelenleg az Excel-munkafüzet funkció csak olyan kérés/válasz szolgáltatások esetében támogatott, amelyek egyetlen kimenettel rendelkeznek (azaz egyetlen pontozási címkével). 

Ha rendelkezik webszolgáltatással, kattintson a Studio bal oldalán található **WEBszolgáltatások** szakaszra, majd válassza ki az Excelből használni kívánt webszolgáltatást.

**Klasszikus webszolgáltatás**

1. A webszolgáltatás **irányítópult** lapján a **kérelem/válasz** szolgáltatás sora szerepel. Ha a szolgáltatás egyetlen kimenettel rendelkezett, akkor az **Excel-munkafüzet letöltése** hivatkozásra kell mutatnia az adott sorban.

    ![Excel-munkafüzet letöltése a Studio (klasszikus) webszolgáltatás-portál használatával](./media/consuming-from-excel/excellink.png)
2. Kattintson az **Excel-munkafüzet letöltése** elemre.

**Új webszolgáltatás**

1. A Azure Machine Learning webszolgáltatási portálon válassza **a** felhasználás elemet.
2. A **Felhasználás lap webszolgáltatás-használat beállításai** szakaszában kattintson az Excel ikonra.

**A munkafüzet használata**

1. Nyissa meg a munkafüzetet.
2. Megjelenik egy biztonsági figyelmeztetés; kattintson a **Szerkesztés engedélyezése** gombra.

    ![Szerkesztés engedélyezése a védett nézet biztonsági figyelmeztetésének eltávolításához](./media/consuming-from-excel/enableeditting.png)
3. Biztonsági figyelmeztetés jelenik meg. Kattintson a **tartalom engedélyezése** gombra a makrók futtatásához a számolótáblán.

    ![Tartalom engedélyezése a makrók letiltására vonatkozó biztonsági figyelmeztetés elvetéséhez](./media/consuming-from-excel/enablecontent.png)
4. A makrók engedélyezése után létrejön egy tábla. A kék színű oszlopoknak az ERŐFORRÁSREKORDOK webszolgáltatásba vagy a **paraméterekbe** való bevitelre van szükségük. Jegyezze fel a ERŐFORRÁSREKORD-szolgáltatás kimenetét, az **előre jelzett értékeket** zöld színnel. Ha az adott sorhoz tartozó összes oszlop ki van töltve, a munkafüzet automatikusan meghívja a pontozási API-t, és megjeleníti a pontozásos eredményeket.

    ![A paraméterek bemenetei és az eredményül kapott előre jelzett értékek táblázata](./media/consuming-from-excel/sampletable.png)
5. Több sor kitöltéséhez töltse ki a második sort az adatokkal, és a rendszer az előre jelzett értékeket adja meg. Egyszerre több sort is beilleszthet.

Az adatok megjelenítéséhez az Excel összes funkcióját (gráfok, Power map, feltételes formázás stb.) használhatja az előre jelzett értékekkel.

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
Ahhoz, hogy a makrók működjenek, az API-kulcsnak a táblázat részét kell képeznie. Ez azt jelenti, hogy csak a megbízható entitásokkal vagy személyekkel kell megosztania a munkafüzetet.

## <a name="automatic-updates"></a>Automatikus frissítések
A rendszer a következő két helyzetben tesz elérhetővé egy ERŐFORRÁSREKORDOK-hívást:

1. Az első alkalommal, amikor egy sor tartalma az összes **paraméterében** szerepel
2. A **Paraméterek** bármelyike olyan sorban változik, amelyben minden **paraméter** be van írva.