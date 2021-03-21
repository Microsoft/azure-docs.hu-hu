---
title: Az ELŐREJELZÉSsel rendelkező gépi tanulási modellek pontszáma
description: Megtudhatja, hogyan szerzi be a gépi tanulási modelleket a dedikált SQL-készlet T-SQL-ELŐREJELZÉSi funkciójával.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9e7d45a588e60cd082f1eef43d1d1b6681b9e912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117741"
---
# <a name="score-machine-learning-models-with-predict"></a>Az ELŐREJELZÉSsel rendelkező gépi tanulási modellek pontszáma

A dedikált SQL-készlet lehetővé teszi, hogy a jól ismert T-SQL nyelv használatával a gépi tanulási modelleket is megszerezze. A T-SQL [előrejelzéssel](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)megadhatja a már meglévő gépi tanulási modelleket, és az adattárház biztonságos határain belül is megszerezheti azokat. A PREDIKTÍV függvény [ONNX (Open neurális hálózati Exchange)](https://onnx.ai/) modellt és adatokat használ bemenetként. Ez a szolgáltatás kiküszöböli az értékes adattárházon kívüli adatáthelyezés lépéseit. Az informatikai szakemberek számára lehetővé teszi, hogy az ismerős T-SQL-felülettel könnyedén üzembe helyezhetik a gépi tanulási modelleket, valamint hogy zökkenőmentesen működjenek együtt a feladataik megfelelő keretrendszerével dolgozó adatszakértőkkel.

> [!NOTE]
> Ez a funkció jelenleg nem támogatott a kiszolgáló nélküli SQL-készletekben.

A funkció használatához a modellt a szinapszis SQL-en kívül kell tanítani. A modell felépítése után töltse be az adattárházba, és a T-SQL előrejelzési szintaxissal szerzi be az adatokból származó elemzéseket.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>A modell betanítása

A dedikált SQL-készlet előre betanított modellt vár. Tartsa szem előtt a következő tényezőket, amikor a dedikált SQL-készletben előrejelzéseket végző gépi tanulási modellt használ.

- A dedikált SQL-készlet csak a ONNX formátumot támogatja. A ONNX egy nyílt forráskódú modell formátuma, amely lehetővé teszi a különböző keretrendszerek közötti modellek cseréjét az együttműködés lehetővé tételéhez. A meglévő modelleket ONNX formátumra konvertálhatja olyan keretrendszerek használatával, amelyek natív módon támogatják vagy a csomagok átalakítását is lehetővé teszik. A [sklearn-Onnx](https://github.com/onnx/sklearn-onnx) csomag például a scikit-Learn modellek konvertálása Onnx. A [ONNX GitHub-adattár](https://github.com/onnx/tutorials#converting-to-onnx-format) a támogatott keretrendszerek és példák listáját tartalmazza.

   Ha az [automatikus ml](../../machine-learning/concept-automated-ml.md) -t használja a betanításhoz, ügyeljen arra, hogy a *enable_onnx_compatible_models* paraméter értéke TRUE (igaz) legyen, hogy Onnx formátumú modellt hozzon létre. Az [automatizált Machine learning notebookon](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) látható egy példa arra, hogyan használható az automatikus ml a ONNX formátumú gépi tanulási modell létrehozásához.

- A bemeneti adatok a következő adattípusokat támogatják:
    - int, bigint, valós, float
    - char, varchar, nvarchar

- A pontozási adatgyűjtési adatként a betanítási adatként megegyező formátumban kell lennie. Az előrejelzés nem támogatja az összetett adattípusokat, például a többdimenziós tömböket. Így a képzéshez győződjön meg arról, hogy a modell minden bemenete megfelel a pontozási tábla egyetlen oszlopának, ahelyett, hogy egyetlen, az összes bemenetet tartalmazó tömböt kellene átadni.

- Győződjön meg arról, hogy a modell bemenetei és adattípusai egyeznek az új előrejelzési adatok oszlopainak neveivel és adattípusaival. A különböző nyílt forráskódú eszközök online elérhetővé tételével egy ONNX-modell megjelenítése további segítséget nyújthat a hibakeresésben.

## <a name="loading-the-model"></a>A modell betöltése

A modell egy dedikált SQL Pool felhasználói táblában van tárolva hexadecimális karakterláncként. Az azonosító és a Leírás további oszlopokat is hozzáadhat a modell táblában a modell azonosításához. Használja a varbinary (max) értéket a modell oszlop adattípusa szerint. Az alábbi példa egy olyan táblázatra mutat be példát, amely a modellek tárolására használható:

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

Ha a modellt egy hexadecimális karakterlánccá alakítja át, és a tábla definíciója meg van adva, a [másolási paranccsal](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest) vagy a kiindulással töltheti be a modellt a dedikált SQL-készlet táblába. A következő mintakód a Másolás parancs használatával tölti be a modellt.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>A modell pontozása

Ha a modell és az adat betöltődik az adattárházba, a **T-SQL előrejelzési** függvénnyel szerzi be a modellt. Győződjön meg arról, hogy az új bemeneti adatok formátuma megegyezik a modell létrehozásához használt képzési adatokkal. A T-SQL előrejelzés két bemenetet vesz igénybe: modell és új pontozási bemeneti adat, és új oszlopokat hoz létre a kimenethez. A modell változóként, literálal vagy skaláris sub_query is megadható. A [WITH common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest) használatával megadhatja az adatparaméter elnevezett eredményhalmaz értékét.

Az alábbi példa egy példaként szolgáló lekérdezést mutat be az előrejelzési függvény használatával. Egy további, a *pontszám* és az adattípusú *lebegőpontos* oszlop jön létre, amely tartalmazza az előrejelzés eredményeit. Az összes bemeneti adatoszlop, valamint a kimeneti előrejelzési oszlopok is elérhetők a SELECT utasítással való megjelenítéshez. További részletek: [előrejelzés (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>Következő lépések

További információ a PREDIKTÍV függvényről: [előrejelzés (Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest).