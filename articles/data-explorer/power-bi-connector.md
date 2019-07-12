---
title: Adatok megjelenítése a Power bi-hoz az Azure Data Explorer-összekötő használatával
description: 'Ebből a cikkből elsajátíthatja, hogyan használja a következő három lehetőség közül használatával megjelenítheti az adatokat a Power bi-ban: a Power BI-összekötő az Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: cfe7d5fa82197a05ddadd08a8811dc86067a05d7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806469"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Adatok megjelenítése a Power bi-hoz az Azure Data Explorer-összekötő használatával

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

A Power BI-adatokhoz való csatlakozásról három lehetőséget kínál a Azure Data Explorer: használja a beépített összekötő, importálhat egy lekérdezést az Azure Data Explorer vagy egy SQL-lekérdezést. Ez a cikk bemutatja, hogyan adatokat, és megjelenítheti a Power BI-jelentés a beépített összekötő használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a következőkre lesz szüksége:

* Szervezeti e-mail-fiókkal, amely tagja az Azure Active directory, így kapcsolódhat a [Azure adatkezelő súgófürtben](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasszon **ingyenes letöltés**)

## <a name="get-data-from-azure-data-explorer"></a>Adatok lekérése az Azure Data Explorer

Először csatlakozik az Azure Data Explorer súgófürtben, majd származó adatok egy részét a tenné a *StormEvents* tábla. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. A Power BI Desktopban a a **kezdőlap** lapon jelölje be **adatok lekérése** majd **további**.

    ![Adatok lekérése](media/power-bi-connector/get-data-more.png)

1. Keresse meg *Azure adatkezelő*válassza **Azure adatkezelő** majd **Connect**.

    ![Adatok keresése és lekérése](media/power-bi-connector/search-get-data.png)

1. Az a **Azure Data Explorer (Kusto)** lapon töltse ki az űrlapot a következő információkat.

    ![Fürt-, adatbázis- és táblázatbeállítások](media/power-bi-connector/cluster-database-table.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Fürt | *https://help.kusto.windows.net* | A súgófürtben URL-CÍMÉT. Egyéb fürtök esetében az URL-cím szerepel az űrlap *https://\<ClusterName\>.\< Régió\>. kusto.windows.net*. |
    | Adatbázis | Hagyja üresen | Egy adatbázis, amely a fürt üzemelteti, amelyhez csatlakozik. Ez egy későbbi lépésben választjuk ki. |
    | Tábla neve | Hagyja üresen | A táblák az adatbázisban, vagy egy lekérdezés egyikét, például <code>StormEvents \| take 1000</code>. Ez egy későbbi lépésben választjuk ki. |
    | Speciális beállítások | Hagyja üresen | Lehetőségek a lekérdezésekhez, például az eredmény méretének beállítása. |
    | Adatkapcsolati mód | *DirectQuery* | Meghatározza, hogy a Power BI importálta az adatokat, vagy közvetlenül csatlakozik az adatforráshoz. Mindkét módszerhez ezzel az összekötővel. |
    | | | |

1. Ha már nincs a súgófürtben csatlakozni, jelentkezzen be. Jelentkezzen be szervezeti fiókkal, majd válassza a **Connect**.

    ![Bejelentkezés](media/power-bi-connector/sign-in.png)

1. Az a **kezelő** bontsa ki a **minták** adatbázisra, válassza **StormEvents** majd **szerkesztése**.

    ![Tábla kiválasztása](media/power-bi-connector/select-table.png)

    A tábla megnyílik a Power Query-szerkesztőben, ahol az adatok importálása előtt Ön szerkesztheti a sorokat és oszlopokat.

1. A Power Query-szerkesztőben, jelölje a nyílra a **DamageCrops** oszlop majd **Rendezés csökkenő sorrendben**.

    ![Csökkenő rendezés DamageCrops](media/power-bi-connector/sort-descending.png)

1. Az a **kezdőlap** lapon jelölje be **sorok megtartása** majd **legelső sorok megtartása**. Adjon meg egy értéket a *1000* területén az első 1000 sort a rendezett tábla.

    ![Legelső sorok megtartása](media/power-bi-connector/keep-top-rows.png)

1. Az a **kezdőlap** lapon jelölje be **Bezárás és alkalmazás**.

    ![Bezárás és alkalmazás](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>A jelentésben szereplő adatok megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott jelentés ebben a cikkben, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése Power BI-ban az importált lekérdezés használatával](power-bi-imported-query.md)
