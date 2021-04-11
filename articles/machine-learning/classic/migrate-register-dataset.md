---
title: 'ML Studio (klasszikus): áttelepítés Azure Machine Learning-Újraépítés adatkészletbe'
description: A Studio (klasszikus) adatkészletek újraépítése a Azure Machine Learning Designerben
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565043"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Studio (klasszikus) adatkészlet migrálása Azure Machine Learningre

Ebből a cikkből megtudhatja, hogyan telepítheti át a Studio (klasszikus) adatkészletet Azure Machine Learningba. A studióból (klasszikus) való áttelepítéssel kapcsolatos további információkért tekintse meg [az áttelepítés áttekintése című cikket](migrate-overview.md).

Három lehetősége van áttelepíteni az adatkészletet Azure Machine Learningba. Olvassa el az egyes szakaszokat, és határozza meg, melyik lehetőség a legmegfelelőbb a forgatókönyvhöz.


|Hol vannak az adathalmazok? | Áttelepítési lehetőség  |
|---------|---------|
|Studióban (klasszikus)     |  1. lehetőség: [töltse le az adatkészletet a studióból (klasszikus), és töltse fel Azure Machine Learningba](#download-the-dataset-from-studio-classic).      |
|Felhőalapú tárolás     | 2. lehetőség: [adatkészlet regisztrálása egy felhőalapú forrásból](#import-data-from-cloud-sources). <br><br>  3. lehetőség: [az adatok importálása modul használatával beolvashatja az adatok egy felhőalapú forrásból](#import-data-from-cloud-sources).        |

> [!NOTE]
> A Azure Machine Learning az adatkészletek létrehozásához és kezeléséhez is támogatja a [kód első munkafolyamatait](../how-to-create-register-datasets.md) . 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy Azure Machine Learning-munkaterület. [Hozzon létre egy Azure Machine learning munkaterületet](../how-to-manage-workspace.md#create-a-workspace).
- Az áttelepíteni kívánt Studio (klasszikus) adatkészlet.


## <a name="download-the-dataset-from-studio-classic"></a>Az adatkészlet letöltése a studióból (klasszikus)

A Studio (klasszikus) adatkészletnek a Azure Machine Learningre való áttelepítésének legegyszerűbb módja az adatkészlet letöltése, és a Azure Machine Learning regisztrálása. Ez létrehozza az adatkészlet egy új példányát, és feltölti azt egy Azure Machine Learning adattárba.

A következő Studio (klasszikus) adatkészleteket közvetlenül is letöltheti.

* Egyszerű szöveg (. txt)
* Vesszővel tagolt értékek (CSV) fejléctel (. csv) vagy anélkül (.nh.csv)
* Tabulátorral tagolt értékek (TSV) fejléctel (. TSV) vagy anélkül (. NH. TSV)
* Excel-fájl
* Zip-fájl (. zip)

Az adatkészletek közvetlen letöltése:
1. Nyissa meg a Studio (klasszikus) munkaterületet ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. A bal oldali navigációs sávon válassza az **adatkészletek** lapot.
1. Válassza ki a letölteni kívánt adatkészletet (ka) t.
1. Az alsó műveleti sávban válassza a **Letöltés** lehetőséget.

    ![A Studio (klasszikus) adatkészletének letöltését bemutató képernyőkép](./media/migrate-register-dataset/download-dataset.png)

A következő adattípusok esetében a **Konvertálás CSV** -modulba lehetőséget kell használnia az adatkészletek letöltéséhez.

* SVMLight-adathalmazok (. SVMLight) 
* Attribútum-kapcsolatok fájlformátuma (ARFF) (. ARFF) 
* R-objektum vagy munkaterület-fájl (. RData
* Adatkészlet típusa (. adat). Az adatkészlet típusa a modul kimenetének Studio (klasszikus) belső adattípusa.

Az adatkészlet CSV-re való átalakításához és az eredmények letöltéséhez:

1. Nyissa meg a Studio (klasszikus) munkaterületet ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Hozzon létre egy új kísérletet.
1. Húzza a vászonra a letölteni kívánt adatkészletet.
1. Adjon hozzá egy **Konvertálás CSV-** modult.
1. Kapcsolódjon a **Convert to CSV** bemeneti porthoz az adatkészlet kimeneti portjához.
1. Futtassa a kísérletet.
1. Kattintson a jobb gombbal a **Konvertálás CSV** -modulra elemre.
1. Válassza az **eredmények adatkészlet**  >  **letöltése** lehetőséget.

    ![A CSV-folyamat átalakításának beállítását bemutató képernyőkép](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Az adatkészlet feltöltése Azure Machine Learning

Az adatfájl letöltése után regisztrálhatja az adatkészletet a Azure Machine Learningban:

1. Nyissa meg a Azure Machine Learning studiót ([ml.Azure.com](https://ml.azure.com)).
1. A bal oldali navigációs panelen kattintson az **adatkészletek** fülre.
1. Válassza az **adatkészlet létrehozása**  >  **helyi fájlokból** lehetőséget.
    ![Az adatkészletek lapot és a helyi fájl létrehozására szolgáló gombot ábrázoló képernyőkép](./media/migrate-register-dataset/register-dataset.png)
1. Adja meg a nevet és a leírást.
1. Az **adatkészlet típusa** mezőben válassza a **táblázatos** lehetőséget.

    > [!NOTE]
    > A ZIP-fájlokat adatkészletként is feltöltheti. ZIP-fájl feltöltéséhez válassza a **fájl** lehetőséget az **adatkészlet típusához**.

1. Az **adattár és a fájl kiválasztásához** válassza ki azt az adattárat, amelybe fel szeretné tölteni az adatkészlet-fájlt.

    Alapértelmezés szerint a Azure Machine Learning az adatkészletet az alapértelmezett munkaterület blobtárhely tárolja. További információ az [adattárakról: Kapcsolódás a tárolási szolgáltatásokhoz](../how-to-access-data.md).

1. Állítsa be az adatkészlethez tartozó adatelemzési beállításokat és sémát. Ezután erősítse meg a beállításokat.

## <a name="import-data-from-cloud-sources"></a>Adatok importálása a felhőalapú forrásokból

Ha az adatai már Felhőbeli tárolási szolgáltatásban vannak, és az adataikat a natív helyükön szeretné megőrizni. A következő lehetőségek egyikét használhatja:

|Betöltési módszer|Leírás|
|---| --- |
|Azure Machine Learning adatkészlet regisztrálása|Helyi és online adatforrásokból (blob, ADLS Gen1, ADLS Gen2, fájlmegosztás, SQL DB) származó adatok beolvasása. <br><br>Az adatforrásra mutató hivatkozást hoz létre, amelyet a rendszer a futás közben kiértékel. Akkor használja ezt a beállítást, ha ismételten hozzáfér ehhez az adatkészlethez, és szeretné engedélyezni a speciális adatszolgáltatásokat, például az adatverziószámozást és a figyelést.
|Adatmodul importálása|Online adatforrásokból (blob, ADLS Gen1, ADLS Gen2, fájlmegosztás, SQL DB) származó adatok beolvasása. <br><br> Az adatkészlet csak az aktuális tervezői folyamat futtatására lett importálva.


>[!Note]
> A Studio (klasszikus) felhasználói vegye figyelembe, hogy a következő felhőalapú források nem támogatottak natív módon a Azure Machine Learningban:
> - Struktúra-lekérdezés
> - Azure-tábla
> - Azure Cosmos DB
> - Helyszíni SQL Database
>
> Javasoljuk, hogy a felhasználók Azure Data Factory használatával migrálják az adataikat egy támogatott tárolási szolgáltatásba.  

### <a name="register-an-azure-machine-learning-dataset"></a>Azure Machine Learning adatkészlet regisztrálása

Az alábbi lépéseket követve regisztrálhat egy adatkészletet, hogy Azure Machine Learning egy felhőalapú szolgáltatásból: 

1. [Hozzon létre egy adattárt](../how-to-connect-data-ui.md#create-datastores), amely összeköti a felhőalapú tárolási szolgáltatást a Azure Machine learning munkaterülettel. 

1. [Adatkészlet regisztrálása](../how-to-connect-data-ui.md#create-datasets). Ha egy Studio (klasszikus) adatkészletet telepít át, válassza a **táblázatos** adatkészlet beállítást.

Miután regisztrált egy adatkészletet a Azure Machine Learningban, a következőt használhatja a Designerben:
 
1. Hozzon létre egy új tervezői folyamat piszkozatát.
1. A bal oldali modul palettán bontsa ki az **adatkészletek** szakaszt.
1. Húzza a regisztrált adatkészletet a vászonra. 

### <a name="use-the-import-data-module"></a>Az Adatimportálási modul használata

A következő lépésekkel importálhatja az adatait közvetlenül a tervezői folyamatba:

1. [Hozzon létre egy adattárt](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores), amely összeköti a felhőalapú tárolási szolgáltatást a Azure Machine learning munkaterülettel. 

Miután létrehozta az adattárat, használhatja az [**adatok importálása**](../algorithm-module-reference/import-data.md) modult a tervezőben az adatok betöltéséhez:

1. Hozzon létre egy új tervezői folyamat piszkozatát.
1. A bal oldali modul palettáján keresse meg az **adatimportálás** modult, és húzza a vászonra.
1. Válassza ki az **adatimportálási** modult, és a jobb oldali panelen található beállításokkal konfigurálja az adatforrást.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan telepíthet át egy Studio (klasszikus) adatkészletet Azure Machine Learningba. A következő lépés [egy Studio (klasszikus) betanítási folyamat újraépítése](migrate-rebuild-experiment.md).


Tekintse meg a Studio (klasszikus) áttelepítési sorozat további cikkeit:

1. [Áttelepítési áttekintés](migrate-overview.md).
1. **Adatkészletek migrálása**.
1. [Építse újra a Studio (klasszikus) betanítási folyamatát](migrate-rebuild-experiment.md).
1. [Építse újra a Studio (klasszikus) webszolgáltatást](migrate-rebuild-web-service.md).
1. [Azure Machine learning webszolgáltatások integrálása az ügyfélalkalmazások alkalmazásával](migrate-rebuild-integrate-with-client-app.md).
1. [Telepítse át az R-szkriptet](migrate-execute-r-script.md).
