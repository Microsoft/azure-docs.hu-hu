---
title: 'Gyors útmutató: adatok beolvasása ADLS Gen2ról a pandák dataframe'
description: Adatok beolvasása egy Azure Data Lake Storage Gen2-fiókból egy Panda dataframe a Python használatával a szinapszis Studióban az Azure szinapszis Analyticsben.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969868"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Gyors útmutató: adatok beolvasása ADLS Gen2ról a pandák dataframe az Azure szinapszis Analyticsben

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Pythont az adatok Azure Data Lake Storage (ADLS) Gen2 való beolvasására az Azure szinapszis Analytics egy Panda-dataframe.

A szinapszis Studio jegyzetfüzetből:

- Kapcsolódjon az Azure szinapszis Analytics-munkaterülethez kapcsolódó Data Lake Storage Gen2 lévő tárolóhoz
- PySpark-jegyzetfüzetből származó adatok olvasása a következő használatával: `spark.read.load`
- az adatátalakítás egy Panda dataframe a használatával `.toPandas()`

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
- A szinapszis Analytics-munkaterülete, amely az alapértelmezett tárolóként van konfigurálva Data Lake Storage Gen2 – a Data Lake Storage Gen2 fájlrendszer **tárolási blob-Adatközreműködőinek** kell lennie. A munkaterület létrehozásával kapcsolatos további információkért lásd: [szinapszis-munkaterület létrehozása](get-started-create-workspace.md).
- Apache Spark készlet a munkaterületen – lásd: [kiszolgáló nélküli Apache Spark készlet létrehozása](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Mintaadatok feltöltése ADLS Gen2ba

1. A Azure Portal hozzon létre egy tárolót a szinapszis Studio által használt Data Lake Storage Gen2. Ezt a lépést kihagyhatja, ha az alapértelmezett társított Storage-fiókot szeretné használni az Azure szinapszis Analytics-munkaterületen.

1. A szinapszis Studióban kattintson az **adat** elemre, válassza a **csatolt** fület, és válassza ki a tárolót **Azure Data Lake Storage Gen2** alatt.

1. Töltse le a mintát [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) és töltse fel a tárolóba.

1. Válassza ki a feltöltött fájlt, kattintson a **Tulajdonságok** elemre, és másolja a **ABFSS útvonal** értékét.

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Adatok beolvasása ADLS Gen2ból egy Panda dataframe

1. A bal oldali ablaktáblán kattintson a **fejlesztés** elemre.

1. **+** Új jegyzetfüzet létrehozásához kattintson és válassza a "jegyzetfüzet" lehetőséget.

1. A **csatolva** elemnél válassza ki a Apache Spark készletet. Ha még nem rendelkezik ilyennel, kattintson a **Create Apache Spark Pool (készlet létrehozása**) elemre.

1. A jegyzetfüzet-kód cellában illessze be a következő Python-kódot, és szúrja be a korábban átmásolt ABFSS útvonalat:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Futtassa a cellát.

Néhány perc elteltével a megjelenített szöveg a következőhöz hasonlóan fog kinézni.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure Synapse Analytics?](overview-what-is.md)
- [Ismerkedés az Azure szinapszis Analytics szolgáltatással](get-started.md)
- [Kiszolgáló nélküli Apache Spark készlet létrehozása](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
