---
title: Adatok elemzése Azure Machine Learning segítségével
description: A Azure Machine Learning használatával prediktív gépi tanulási modellt hozhat létre az Azure Szinapszisban tárolt adatszolgáltatások alapján.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678659"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Adatok elemzése Azure Machine Learning segítségével

Ez az oktatóanyag a [Azure Machine learning Designer](../../machine-learning/concept-designer.md) használatával készít egy prediktív gépi tanulási modellt. A modell az Azure Szinapszisban tárolt adatszolgáltatásokon alapul. Az oktatóanyag forgatókönyve annak előrejelzése, hogy az ügyfél valószínűleg kerékpárt vásárol-e, vagy sem, hogy az Adventure működik-e, a bike Shop a megcélzó marketing-kampányt is felépítheti.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* egy SQL-készlet előre betöltve a AdventureWorksDW-mintaadatok. Az SQL-készlet kiépítéséhez lásd: [SQL-készlet létrehozása](create-data-warehouse-portal.md) és a mintaadatok betöltésének kiválasztása. Ha már rendelkezik egy adatraktárral, de nem rendelkezik mintaadatok használatával, [manuálisan is betöltheti a mintavételi](./load-data-from-azure-blob-storage-using-copy.md)adatkészleteket.
* egy Azure Machine learning-munkaterület. [Ezt az oktatóanyagot](../../machine-learning/how-to-manage-workspace.md) követve hozzon létre egy újat.

## <a name="get-the-data"></a>Az adatok lekérése

A használt adatértékek a AdventureWorksDW dbo. vTargetMail nézetében szerepelnek. Az ebben az oktatóanyagban az adattár használatához először exportálja az adatokat Azure Data Lake Storage fiókba, mivel az Azure szinapszis jelenleg nem támogatja az adatkészleteket. A Azure Data Factory a [másolási tevékenységgel](../../data-factory/copy-activity-overview.md)Azure Data Lake Storage adatok exportálására is használható az adatraktárból. Az importáláshoz használja a következő lekérdezést:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Ha az adatAzure Data Lake Storageban rendelkezésre állnak, a Azure Machine Learning [adattárolói az Azure Storage-szolgáltatásokhoz való kapcsolódáshoz](../../machine-learning/how-to-access-data.md)használatosak. Az adattár és a megfelelő adatkészlet létrehozásához kövesse az alábbi lépéseket:

1. Indítsa el Azure Machine Learning Studio alkalmazást Azure Portal vagy jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com/).

1. Kattintson az  adattárolók elemre a **kezelés** szakasz bal oldali ablaktábláján, majd kattintson az **új adattár** elemre.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Képernyőkép a Azure Machine Learning felület bal oldali paneljéről":::

1. Adja meg az adattár nevét, válassza ki az "Azure Blob Storage" típust, adja meg a helyet és a hitelesítő adatokat. Ezután kattintson a **Létrehozás** gombra.

1. Ezután kattintson az **adatkészletek** elemre az **eszközök** szakasz bal oldali ablaktábláján. Válassza az **adatkészlet létrehozása** lehetőséget az **adattár** lehetőséggel.

1. Adja meg az adatkészlet nevét, és válassza ki a **táblázatos** típust. Ezután a **tovább** gombra kattintva léphet tovább.

1. Az **adattár kiválasztása vagy létrehozása szakaszban** válassza a **korábban létrehozott adattár** lehetőséget. Válassza ki a korábban létrehozott adattárat. Kattintson a Tovább gombra, és határozza meg az elérési utat és a fájl beállításait. Ha a fájlok tartalmaznak egyet, ügyeljen arra, hogy az oszlopfejléc legyen megadva.

1. Végül kattintson a **Létrehozás** gombra az adatkészlet létrehozásához.

## <a name="configure-designer-experiment"></a>Tervezői kísérlet konfigurálása

Ezután kövesse az alábbi lépéseket a tervező konfigurálásához:

1. Kattintson a **tervező** fülre a **Szerző** szakasz bal oldali paneljén.

1. Válassza a **könnyen használható előre elkészített modulok** lehetőséget egy új folyamat létrehozásához.

1. A jobb oldali beállítások ablaktáblán adja meg a folyamat nevét.

1. Továbbá válasszon ki egy cél számítási fürtöt a teljes kísérlethez a beállítások gombon egy korábban kiosztott fürthöz. A beállítások ablaktábla bezárásához.

## <a name="import-the-data"></a>Adatok importálása

1. A keresőmező alatti bal oldali ablaktáblán kattintson az **adatkészletek** lapra.

1. Húzza a korábban létrehozott adatkészletet a vászonra.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Képernyőfelvétel a vásznon található adatkészlet-modulról.":::

## <a name="clean-the-data"></a>Az adatok megtisztítása

Az adattisztításhoz a modellhez nem kapcsolódó oszlopokat kell eldobnia. Kövesse az alábbi lépéseket:

1. Válassza a **modulok** lapra a bal oldali ablaktáblán.

1. Húzza a **Select Columns (oszlopok kiválasztása** ) modult az **adatátalakítási < manipuláció** a vászonra elemre. Kapcsolja össze ezt a modult az **adatkészlet** modulhoz.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Képernyőfelvétel a vászon oszlop kiválasztása moduljáról." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Kattintson a modulra a Tulajdonságok ablaktábla megnyitásához. Az oszlop szerkesztése elemre kattintva megadhatja, hogy mely oszlopokat szeretné eldobni.

1. Két oszlop kizárása: CustomerAlternateKey és GeographyKey. Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Az eldobott oszlopokat ábrázoló képernyőkép.":::

## <a name="build-the-model"></a>A modell létrehozása

Az adathalmaz 80-20:80%-ot oszt ki egy Machine learning-modell betanításához, és 20%-ot a modell teszteléséhez. Ez a bináris besorolási probléma "kétosztályos" algoritmust használ.

1. Húzza az **Adatfelosztási** modult a vászonra.

1. A Tulajdonságok ablaktáblán adja meg a 0,8 értéket **az első kimeneti adatkészletben lévő sorok töredékének** megadásához.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Képernyőfelvétel: a 0,8 felosztási arányának ábrázolása.":::

1. Húzza a **Két osztályú súlyozott döntési fa** modult a vászonra.

1. Húzza a **vonat modell** modult a vászonra. A bemeneteket úgy adhatja meg, hogy összekapcsolja a **kétosztályos kibővített döntési fába** (ml algoritmus) és az **adatok felosztása** (az algoritmus betanítása) modulok számára.

1. A betanítási modell modellje elemnél, a Tulajdonságok ablaktábla **címke oszlopa** területén válassza az oszlop szerkesztése lehetőséget. Válassza ki a **kerékpárvásárló** oszlopot az előrejelzéshez, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="A Label (címke) oszlopot, a Kerékpárvásárló és a kiválasztott elemet ábrázoló képernyőkép.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Képernyőfelvétel: Two-Class növelt döntési fa és az adatmodulok felosztása.":::

## <a name="score-the-model"></a>A modell pontozása

Most tesztelje, hogyan hajtja végre a modell a tesztelési adatokon. A rendszer két különböző algoritmust használ, amelyekkel megtekintheti, melyik végez jobbat. Kövesse az alábbi lépéseket:

1. Húzza a **pontszám modell** modult a vászonra, és kapcsolja össze a **modell betanítása** és a **felosztott** adatmodulok használatával.

1. Húzza a **kétosztályos Bayes átlagos Perceptron** a kísérleti vászonra. Összehasonlítja, hogy ez az algoritmus hogyan hajtja végre a Two-Class növelt döntési fájához képest.

1. Másolja és illessze be a modulok **betanítási modelljét** és **pontszámát** a vásznon.

1. Húzza a **Modell kiértékelése** modult a vászonra a két algoritmus összehasonlításához.

1. Kattintson a **Submit (elküldés** ) gombra a folyamat futtatásának beállításához.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Képernyőkép a vásznon található többi modulról." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. A Futtatás befejezése után kattintson a jobb gombbal a **modell kiértékelése** modulra, majd kattintson a **kiértékelés eredményeinek megjelenítése** lehetőségre.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Képernyőkép az eredményekről.":::

A megadott metrikák a ROC görbe, a precíziós visszahívás diagram és a lift görbe. Tekintse meg ezeket a mérőszámokat, és figyelje meg, hogy az első modell jobban teljesített, mint a második. Ha szeretné megtekinteni az első modell előrejelzését, kattintson a jobb gombbal a pontszám modell modulra, és kattintson a pontozásos adatkészlet megjelenítése elemre az előre jelzett eredmények megtekintéséhez.

Ekkor két további oszlop jelenik meg a tesztelési adatkészlethez.

* Pontozott valószínűség: annak valószínűsége, hogy az ügyfél kerékpárvásárló.
* Pontozott címkék: a modell által elvégzett osztályozás – kerékpárvásárló (1) vagy sem (0). A címkézés valószínűségi küszöbértéke 50 százalékra van beállítva és módosítható.

Hasonlítsa össze az oszlop Kerékpárvásárló (tényleges) és a pontozásos címkékkel (előrejelzéssel), hogy megtekintse a modell végrehajtását. Ezt a modellt használhatja arra, hogy előrejelzéseket készítsen az új ügyfelek számára. [Ezt a modellt webszolgáltatásként](../../machine-learning/tutorial-designer-automobile-price-deploy.md) teheti közzé, vagy visszaállíthatja az eredményeket az Azure szinapszisba.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Machine Learningről, tekintse meg az Azure-beli [Machine learning bemutatása](../../machine-learning/overview-what-is-azure-ml.md)című témakört.

Ismerje meg a beépített pontozást az adattárházban. [](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)