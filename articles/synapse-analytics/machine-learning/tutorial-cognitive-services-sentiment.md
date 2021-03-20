---
title: 'Oktatóanyag: érzelmek elemzése Cognitive Services'
description: Ismerje meg, hogyan használhatja a Cognitive Services for szentimentális Analysis szolgáltatást az Azure szinapszis Analytics szolgáltatásban
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943697"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Oktatóanyag: érzelmek elemzése a Cognitive Services (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan gazdagíthatja adatait az Azure-beli szinapszis-elemzésekben az [azure Cognitive Services](../../cognitive-services/index.yml)használatával. Az [text Analytics](../../cognitive-services/text-analytics/index.yml) képességek segítségével elvégezheti az érzelmek elemzését. 

Az Azure szinapszis egyik felhasználója egyszerűen kiválaszthat egy olyan táblázatot, amely egy szöveges oszlopot tartalmaz az érzelmekkel való gazdagítás érdekében. Ezek az érzelmek pozitívak, negatívak, vegyesek vagy semlegesek lehetnek. A rendszer A valószínűséget is visszaadja.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> - A kitalált szöveg oszlopot tartalmazó Spark Table-adatkészlet beszerzésének lépései az érzelmek elemzéséhez.
> - Az Azure Szinapszisban az adatCognitive Services-Text Analytics használatával gazdagíthatja az adatgyűjtést a varázsló segítségével.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy Azure Data Lake Storage Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A Data Lake Storage Gen2 fájlrendszer *tárolási blob-Adatközreműködőinek* kell lennie.
- Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Spark-készlet létrehozása az Azure szinapszisban](../quickstart-create-sql-pool-studio.md).
- Az oktatóanyag [konfigurálása Cognitive Services az Azure szinapszisban](tutorial-configure-cognitive-services-synapse.md)című témakörben leírt előzetes konfigurációs lépések.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Spark-tábla létrehozása

Ehhez az oktatóanyaghoz szüksége lesz egy Spark-táblázatra.

1. Töltse le a [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) fájlt, amely tartalmazza a Text Analytics adatkészletét. 

1. Töltse fel a fájlt a Data Lake Storage Gen2 Azure szinapszis Storage-fiókjába.
  
   ![Képernyőkép, amely megjeleníti az adatfeltöltési beállításokat.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Hozzon létre egy Spark-táblázatot a. csv fájlból úgy, hogy a jobb gombbal a fájlra kattint, és az **új jegyzetfüzet**  >  **létrehozása Spark táblát** választja.

   ![A Spark-tábla létrehozásának lehetőségeit bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nevezze el a táblát a Code (kód) cellában, és futtassa a jegyzetfüzetet egy Spark-készleten. Ne felejtse el beállítani `header=True` .

   ![A jegyzetfüzetek futtatását bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>A Cognitive Services varázsló megnyitása

1. Kattintson a jobb gombbal az előző eljárásban létrehozott Spark-táblázatra.   >  A varázsló megnyitásához válassza Machine learning a **meglévő modellel való gazdagítás** elemet.

   ![A pontozási varázsló megnyitására szolgáló beállításokat bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Megjelenik egy konfigurációs panel, és a rendszer megkéri, hogy válasszon ki egy Cognitive Services modellt. Válassza a **text Analytics – Hangulatelemzés** elemet.

   ![Egy Cognitive Services modell kijelölését bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Hitelesítő adatok megadása

A Cognitive Services való hitelesítéshez a kulcstartó titkos kódjára kell hivatkoznia. A következő bemenetek a jelen pont előtt elvégzendő [előfeltételektől](tutorial-configure-cognitive-services-synapse.md) függenek.

- **Azure-előfizetés**: válassza ki azt az Azure-előfizetést, amelyhez a Key Vault tartozik.
- **Cognitive Services fiók**: adja meg azt a Text Analytics-erőforrást, amelyhez csatlakozni fog.
- **Azure Key Vault társított szolgáltatás**: Az előfeltételként szükséges lépések részeként létrehozott egy társított szolgáltatást a Text Analytics-erőforráshoz. Itt választhatja ki.
- **Titkos kód neve**: írja be a kulcstartóban található titkos kulcs nevét, amely a Cognitive Services erőforráshoz való hitelesítéshez szükséges kulcsot tartalmazza.

![A Key Vault hitelesítési adatait bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>A hangulat elemzésének konfigurálása

Ezután konfigurálja az érzelmek elemzését. Válassza ki a következő adatokat:
- **Nyelv**: válassza az **angol** nyelvet annak a szövegnek a nyelvén, amelynek az elemzését el szeretné végezni.
- **Szöveges oszlop**: válassza a **Megjegyzés (karakterlánc)** lehetőséget az adatkészlet azon szöveges oszlopa, amelyet elemezni szeretne, hogy meghatározza a véleményét.

Ha elkészült, válassza a **Jegyzetfüzet megnyitása** lehetőséget. Ez létrehoz egy jegyzetfüzetet az Ön számára az PySpark-kóddal, amely a hangulat elemzését végzi az Azure Cognitive Services.

![Képernyőkép, amely az érzelmek elemzésének konfigurálására szolgáló beállításokat jeleníti meg.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>A notebook futtatása

Az imént megnyitott jegyzetfüzet a [mmlspark könyvtár](https://github.com/Azure/mmlspark) használatával csatlakozik Cognitive Serviceshoz. A megadott Azure Key Vault adatok lehetővé teszik, hogy a tapasztalatok alapján biztonságosan hivatkozzon a titkos kulcsokra.

Mostantól az összes cella használatával gazdagíthatja adatait az érzelmekkel. Válassza **az összes futtatása** lehetőséget. 

Az érzelmek **pozitív**, **negatív**, **semleges** vagy **vegyes** módon lesznek visszaadva. Emellett a valószínűségek alapján is kipróbálhatja. [További információ a Cognitive Servicesbeli érzelmek elemzéséről](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Képernyőkép, amely az érzelmek elemzését mutatja.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Következő lépések
- [Oktatóanyag: anomáliák észlelése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis dedikált SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md)
- [Az Azure Synapse Analytics gépi tanulási képességei](what-is-machine-learning.md)