---
title: Az Excelben webszolgáltatás felhasználása
titleSuffix: Azure Machine Learning Studio
description: Az Azure Machine Learning Studio megkönnyíti a webszolgáltatások meghívása közvetlenül az Excelből, kód írása nélkül.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773757"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Egy Azure Machine Learning Studio webszolgáltatás az Excelből felhasználása

 Az Azure Machine Learning Studio megkönnyíti a webszolgáltatások meghívása közvetlenül az Excelből, kód írása nélkül.

Ha az Excel 2013-as (vagy újabb verzió) vagy az Excel online-ban, akkor azt javasoljuk, hogy az Excel használatával [Excel-bővítmény](excel-add-in-for-web-services.md).



## <a name="steps"></a>Lépések
Webszolgáltatás közzététele. [3. oktatóanyag: Kredit kockázati modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md) azt ismerteti, hogyan teheti meg. Az Excel-munkafüzet funkció jelenleg csak egy egyetlen kimeneti (azaz egy pontozó egycímkés) kérés/válasz szolgáltatások esetében támogatott. 

Ha már rendelkezik egy webszolgáltatás, kattintson a a **WEBSZOLGÁLTATÁSOK** bal oldalán a studio szakaszt, és válassza ki a web service által az Excelből.

**Klasszikus webszolgáltatás**

1. Az a **IRÁNYÍTÓPULT** lapon, a web service van egy sort a **KÉRÉS/válasz** szolgáltatás. Ha ezt a szolgáltatást egy kimeneti, megjelenik a **Excel-munkafüzet letöltése** hivatkozás abban a sorban.

    ![A Studio-webszolgáltatás portálon Excel-munkafüzet letöltése](./media/consuming-from-excel/excellink.png)
2. Kattintson a **Excel-munkafüzet letöltése**.

**Új webszolgáltatás**

1. Az Azure Machine Learning Web Service-portálon válassza **felhasználás**.
2. A felhasználás az oldalon a a **webes szolgáltatás adathasználati beállítások** területén kattintson az Excel ikon.

**A munkafüzet használatának**

1. Nyissa meg a munkafüzetet.
2. Biztonsági figyelmeztetés jelenik meg; Kattintson a **Szerkesztés engedélyezése** gombra.

    ![Povolit úpravy eltávolításához a védett nézet biztonsági figyelmeztetés](./media/consuming-from-excel/enableeditting.png)
3. Biztonsági figyelmeztetés jelenik meg. Kattintson a **tartalom engedélyezése** gomb makrók futtatását a munkalapon.

    ![Engedélyezheti a tartalom számára a makrók letiltásával biztonsági figyelmeztetés mellőzése](./media/consuming-from-excel/enablecontent.png)
4. Ha makrók engedélyezve vannak, egy tábla jön létre. Oszlopok kék is szükséges bemenetként az RRS webes szolgáltatás, vagy **paraméterek**. Vegye figyelembe a kimenet az RRS szolgáltatás **előre JELZETT értékek** zöld színnel. Amikor egy adott sorának az összes oszlop ki vannak töltve, a munkafüzet automatikusan a pontozási API- és a pontozott eredményeit jeleníti meg.

    ![A paraméter bemeneti és a létrejövő tábla előre jelzett értékek](./media/consuming-from-excel/sampletable.png)
5. Pontszámot rendelni az egynél több sorral, adja meg a második sor az adatok és az előre jelzett értékek előállítása. Több sor egyszerre is beillesztheti.

Használhatja az Excel-szolgáltatások (diagramokat, a power mappel, feltételes formázás, stb.) az előre jelzett értékek segítségével jelenítheti meg az adatokat.

## <a name="sharing-your-workbook"></a>A munkafüzet megosztása
A makrók működjön az API-kulcsot a számolótábla részét kell lennie. Ez azt jelenti, hogy ossza meg a munkafüzetet csak a megbízható entitások/szolgáltatásra.

## <a name="automatic-updates"></a>Automatikus frissítések
Az RRS kezdeményezték a két ezekben a helyzetekben:

1. Először a sor nullértékkel rendelkezik, tartalom mindegyikét a **paraméterek**
2. Bármikor, bármely a **paraméterek** kellett az összes sor változásai a **paraméterek** megadott.