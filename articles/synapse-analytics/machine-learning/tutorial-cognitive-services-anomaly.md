---
title: 'Oktatóanyag: anomáliák észlelése Cognitive Services'
description: Ismerje meg, hogyan használhatja a Cognitive Services az anomáliák észleléséhez az Azure szinapszis Analyticsben.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943503"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Oktatóanyag: anomáliák észlelése Cognitive Servicessal (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan gazdagíthatja adatait az Azure-beli szinapszis-elemzésekben az [azure Cognitive Services](../../cognitive-services/index.yml)használatával. A rendellenességek észleléséhez a [anomália detektort](../../cognitive-services/anomaly-detector/index.yml) fogja használni. Az Azure szinapszis egyik felhasználója egyszerűen kiválaszthat egy táblázatot, amely gazdagabbá teszi a rendellenességek észlelését.

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
> - Az idősorozat-adatokat tartalmazó Spark Table-adathalmaz beszerzésének lépései.
> - A varázsló használata az Azure Szinapszisban az adatelemzéshez a Cognitive Services rendellenesség-detektor használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy Azure Data Lake Storage Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A Data Lake Storage Gen2 fájlrendszer *tárolási blob-Adatközreműködőinek* kell lennie.
- Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Spark-készlet létrehozása az Azure szinapszisban](../quickstart-create-sql-pool-studio.md).
- A [konfigurálási Cognitive Services konfigurálása az Azure szinapszis](tutorial-configure-cognitive-services-synapse.md) oktatóanyagában.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Spark-tábla létrehozása

Ehhez az oktatóanyaghoz szüksége lesz egy Spark-táblázatra.

1. Töltse le a következő jegyzetfüzet-fájlt, amely kódot tartalmaz a Spark-tábla létrehozásához: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Töltse fel a fájlt az Azure szinapszis-munkaterületére.

   ![A jegyzetfüzetek feltöltésére szolgáló beállításokat bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Nyissa meg a jegyzetfüzet-fájlt, és válassza az **összes futtatása** lehetőséget az összes cella futtatásához.

   ![A Spark-tábla létrehozásának lehetőségeit bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Egy **anomaly_detector_testing_data** nevű Spark-táblázatnak ekkor az alapértelmezett Spark-adatbázisban kell megjelennie.

## <a name="open-the-cognitive-services-wizard"></a>A Cognitive Services varázsló megnyitása

1. Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblázatra.   >  A varázsló megnyitásához válassza Machine learning a **meglévő modellel való gazdagítás** elemet.

   ![A pontozási varázsló megnyitására szolgáló beállításokat bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Megjelenik egy konfigurációs panel, és a rendszer megkéri, hogy válasszon ki egy Cognitive Services modellt. Válassza ki a **anomália detektort**.

   ![Képernyőkép, amely az anomália detektor modellként való kiválasztását mutatja be.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Hitelesítő adatok megadása

A Cognitive Services való hitelesítéshez a Key vaultban lévő titkos kulcsra kell hivatkoznia. A következő bemenetek attól függnek, hogy milyen [lépéseket](tutorial-configure-cognitive-services-synapse.md) kell végrehajtania a jelen pont előtt.

- **Azure-előfizetés**: válassza ki azt az Azure-előfizetést, amelyhez a Key Vault tartozik.
- **Cognitive Services fiók**: adja meg azt a Text Analytics-erőforrást, amelyhez csatlakozni fog.
- **Azure Key Vault társított szolgáltatás**: Az előfeltételként szükséges lépések részeként létrehozott egy társított szolgáltatást a Text Analytics-erőforráshoz. Itt választhatja ki.
- **Titkos kód neve**: írja be a kulcstartóban található titkos kulcs nevét, amely a Cognitive Services erőforráshoz való hitelesítéshez szükséges kulcsot tartalmazza.

![A Key Vault hitelesítési adatait bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Anomália-detektor konfigurálása

Az anomália-detektor konfigurálásához adja meg a következő adatokat:

- **Részletesség**: az adatmintavételezési sebesség. Válassza a **havi** lehetőséget. 

- **Időbélyeg-oszlop**: az adatsorozat időpontját jelölő oszlop. Válassza az **időbélyeg (karakterlánc)** lehetőséget.

- **Idősor érték oszlop**: az adatsorozat értékét jelölő oszlop, amely az időbélyegző oszlopban megadott időpontban szerepel. Válassza az **érték (Double)** lehetőséget.

- **Csoportosítási oszlop**: az adatsorozatot csoportosító oszlop. Ez azt eredményezi, hogy az ebben az oszlopban szereplő összes sornak egy idősorozatot kell alkotnia. Válassza a **csoport (karakterlánc)** lehetőséget.

Ha elkészült, válassza a **Jegyzetfüzet megnyitása** lehetőséget. Ez létrehoz egy jegyzetfüzetet az Ön számára az Azure Cognitive Servicest használó PySpark-kóddal, hogy észlelje a rendellenességeket.

![Képernyőkép, amely az anomália-detektor konfigurációs adatait jeleníti meg.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>A notebook futtatása

Az imént megnyitott jegyzetfüzet a [mmlspark könyvtár](https://github.com/Azure/mmlspark) használatával csatlakozik Cognitive Serviceshoz. A megadott Azure Key Vault adatok lehetővé teszik, hogy a tapasztalatok alapján biztonságosan hivatkozzon a titkos kulcsokra.

Mostantól az összes cellát futtathatja a anomáliák észlelésének elvégzéséhez. Válassza **az összes futtatása** lehetőséget. [További információ a Cognitive Services anomália-detektorról](../../cognitive-services/anomaly-detector/index.yml).

![A anomáliák észlelését bemutató képernyőkép.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: érzelmek elemzése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis dedikált SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md)
- [Az Azure Synapse Analytics gépi tanulási képességei](what-is-machine-learning.md)
