---
title: 'Oktatóanyag: gépi tanulási modell pontozási varázsló dedikált SQL-készletekhez'
description: Oktatóanyag a gépi tanulási modell pontozási varázslójának használatáról a dedikált SQL-készletekbe tartozó adatfrissítéshez.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935225"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Oktatóanyag: gépi tanulási modell pontozása varázsló (előzetes verzió) dedikált SQL-készletekhez

Megtudhatja, hogyan gazdagíthatja adatait a dedikált SQL-készletekben a prediktív gépi tanulási modellekkel. Az adatszakértők által létrehozott modellek mostantól könnyen elérhetők az adatszakemberek számára a prediktív elemzésekhez. Az Azure szinapszis Analyticsben az adatszakember egyszerűen kiválaszthatja a modelleket az Azure szinapszis SQL-készletekben való üzembe helyezéshez a Azure Machine Learning-modell beállításjegyzékből, és előrejelzéseket indíthat az adatok dúsítására.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> - A prediktív gépi tanulási modell betanítása és a modell regisztrálása a Azure Machine Learning modell beállításjegyzékében.
> - Az SQL pontozás varázsló segítségével elindíthatja az előrejelzéseket egy dedikált SQL-készletben.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy Azure Data Lake Storage Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A Data Lake Storage Gen2 fájlrendszer *tárolási blob-Adatközreműködőinek* kell lennie.
- Dedikált SQL-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd [a DEDIKÁLT SQL-készlet létrehozása](../quickstart-create-sql-pool-studio.md)című témakört.
- Azure Machine Learning társított szolgáltatás az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Azure Machine learning társított szolgáltatás létrehozása az Azure szinapszisban](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Modell betanítása Azure Machine Learning

Mielőtt elkezdené, ellenőrizze, hogy a sklearn verziója 0.20.3-e.

A jegyzetfüzet összes cellájának futtatása előtt győződjön meg arról, hogy a számítási példány fut.

![A Azure Machine Learning számításának ellenőrzését bemutató képernyőkép.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Lépjen a Azure Machine Learning munkaterületre.

1. Töltse le a [New York-i tipp. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Nyissa meg [Azure Machine Learning Studio](https://ml.azure.com)Azure Machine learning munkaterületét.

1. Válassza a **jegyzetfüzetek**  >  **fájlok feltöltése** lehetőséget. Ezután válassza ki a **várható NYC taxi tippek. ipynb** fájlt, amelyet letöltött, és töltse fel.
   ![A fájl feltöltésére szolgáló gomb képernyőképe.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Miután feltöltötte és megnyitotta a jegyzetfüzetet, válassza az **összes cella futtatása** lehetőséget.

   Előfordulhat, hogy az egyik cella meghibásodik, és az Azure-ban történő hitelesítést kéri. Figyelje meg ezt a cella kimenetében, és a kód beírásával végezze el a hitelesítést a böngészőben. Ezután futtassa újra a jegyzetfüzetet.

1. A jegyzetfüzet ONNX-modellt fog betanítani, és regisztrálja azt a MLflow. Nyissa meg a **modelleket** , és győződjön meg arról, hogy az új modell regisztrálása megfelelő.
   ![Képernyőkép, amely megjeleníti a modellt a beállításjegyzékben.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. A jegyzetfüzet futtatásakor a rendszer egy CSV-fájlba is exportálja a tesztelési adatait. Töltse le a CSV-fájlt a helyi rendszeren. Később importálja a CSV-fájlt egy dedikált SQL-készletbe, és az adat használatával teszteli a modellt.

   A CSV-fájl a jegyzetfüzet-fájllal megegyező mappában jön létre. Ha nem látja azonnal, válassza a **frissítés** a Fájlkezelőben lehetőséget.

   ![A C S V fájlt megjelenítő képernyőkép.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Előrejelzések indítása az SQL pontozási varázslóval

1. Nyissa meg az Azure szinapszis munkaterületet a szinapszis Studióval.

1. Nyissa meg az **adatkapcsolattal**  >  **társított**  >  **Storage-fiókokat**. Töltse fel `test_data.csv` az alapértelmezett Storage-fiókba.

   ![Képernyőkép, amely megjeleníti az adatfeltöltési beállításokat.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Ugrás az SQL-parancsfájlok **fejlesztéséhez**  >  . Hozzon létre egy új SQL-szkriptet a dedikált SQL-készletbe való betöltéshez `test_data.csv` .

   > [!NOTE]
   > A szkript futtatása előtt frissítse a fájl URL-címét.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Adat betöltése dedikált SQL-készletbe](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Ugrás az   >  **munkaterületre**. Az SQL-pontozási varázsló megnyitásához kattintson a jobb gombbal a dedikált SQL-készlet táblára. Válassza   >  **a Machine learning a meglévő modellel való** bővítés lehetőséget.

   > [!NOTE]
   > A Machine learning lehetőség nem jelenik meg, ha nem rendelkezik társított szolgáltatással a Azure Machine Learninghoz. (Az oktatóanyag elején tekintse meg az [előfeltételeket](#prerequisites) .)

   ![A Machine learning-beállítást bemutató képernyőkép.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Válassza ki a csatolt Azure Machine Learning munkaterületet a legördülő listából. Ez a lépés betölti a gépi tanulási modellek listáját a kiválasztott Azure Machine Learning munkaterület modell-beállításjegyzékében. Jelenleg csak a ONNX modellek támogatottak, ezért ez a lépés csak ONNX-modelleket fog megjeleníteni.

1. Válassza ki az imént betanított modellt, majd kattintson a **Folytatás** gombra.

   ![Képernyőkép, amely a Azure Machine Learning modell kiválasztását mutatja.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Rendelje hozzá a táblázat oszlopait a modell bemenetekhez, és adja meg a modell kimeneteit. Ha a modellt a MLflow formátumba menti, és a modell aláírása fel van töltve, a leképezést a rendszer automatikusan végrehajtja a nevek hasonlósága alapján. Az illesztőfelület a manuális leképezést is támogatja.

   Válassza a **Folytatás** lehetőséget.

   ![A táblázat – modell leképezést bemutató képernyőkép.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. A generált T-SQL-kód egy tárolt eljárásba van csomagolva. Ezért meg kell adnia egy tárolt eljárás nevét. A modell bináris fájlja, beleértve a metaadatokat (verzió, leírás és egyéb információk), a rendszer fizikailag átmásolja Azure Machine Learningról egy dedikált SQL Pool-táblába. Ezért meg kell adnia, hogy melyik táblát mentse a modellbe. 

   Választhat vagy létrehozhat egy **meglévő táblát** , vagy **újat is létrehozhat**. Ha elkészült, válassza a modell **telepítése + parancsfájl megnyitása** lehetőséget a modell üzembe helyezéséhez és a T-SQL előrejelzési parancsfájl létrehozásához.

   ![Képernyőkép, amely a tárolt eljárások létrehozásához szükséges beállításokat jeleníti meg.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. A szkript létrehozása után válassza a **Futtatás** lehetőséget a pontozás végrehajtásához és az előrejelzések lekéréséhez.

   ![A pontozást és az előrejelzéseket bemutató képernyőkép.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Következő lépések

- [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása az Azure Szinapszisban](quickstart-integrate-azure-machine-learning.md)
- [Az Azure Synapse Analytics gépi tanulási képességei](what-is-machine-learning.md)
