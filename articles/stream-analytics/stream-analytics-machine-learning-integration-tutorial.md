---
title: Azure Stream Analytics integráció Azure Machine Learning Studio (klasszikus)
description: Ez a cikk azt ismerteti, hogyan lehet gyorsan beállítani egy egyszerű Azure Stream Analytics feladatot, amely egy felhasználó által definiált függvény használatával integrálja Azure Machine Learning Studio (klasszikus).
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 89b1b91270d1fe30ffcb5ae0caa5f319aa616683
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178524"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Azure Stream Analytics és Azure Machine Learning Studio (klasszikus) a hangulat elemzése

Ebből a cikkből megtudhatja, hogyan állíthat be egy egyszerű Azure Stream Analytics feladatot, amely a Azure Machine Learning Studio (klasszikus) szolgáltatást használja az érzelmek elemzéséhez. A Cortana Intelligence Gallery Studio (klasszikus) hangulatú elemzési modelljét használja a folyamatos szöveges adatok elemzéséhez és a hangulati pontszám meghatározásához.

> [!TIP]
> A jobb teljesítmény és megbízhatóság érdekében javasoljuk, hogy Azure Machine Learning Studio (klasszikus) UDF helyett [Azure Machine learning UDF](machine-learning-udf.md) használjon.

A jelen cikkből megismerheti a következő forgatókönyveket:

* Valós idejű érzelmek elemzése a Twitter-adatok folyamatos átviteléhez.
* Az ügyfél-csevegések rekordjainak elemzése a támogatási munkatársakkal.
* Megjegyzések kiértékelése fórumokon, blogokon és videókon.
* Számos más valós idejű, prediktív pontozási forgatókönyv.

Az Ön által létrehozott streaming Analytics-feladat felhasználói függvényként (UDF) alkalmazza a hangulati elemzési modellt a blob-tárolóban található mintaszöveg-adatokon. A kimenet (a hangulat elemzése eredményét) ugyanabba a blob-tárolóba írja a rendszer, amely egy másik CSV-fájlban van. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-előfizetés.

* Egy CSV-fájl, amely néhány Twitter-adattal rendelkezik. A [githubról](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)letöltheti a mintát, vagy létrehozhatja a saját fájlját is. A valós forgatókönyvekben közvetlenül a Twitter-adatfolyamból szerezheti be az adatait.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Storage-tároló létrehozása és a CSV-bemeneti fájl feltöltése

Ebben a lépésben egy CSV-fájlt tölt fel a Storage-tárolóba.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **Storage**-  >  **fiók** lehetőséget.

2. Töltse ki az *alapok* lapot a következő részletekkel, és hagyja meg a fennmaradó mezők alapértelmezett értékeit:

   |Mező  |Érték  |
   |---------|---------|
   |Előfizetés|Válassza ki az előfizetését.|
   |Erőforráscsoport|Válassza ki az erőforráscsoportot.|
   |Tárfiók neve|Adja meg a tárfiók nevét. A névnek egyedinek kell lennie az Azure-ban.|
   |Hely|Válassza ki a helyet. Minden erőforrásnak ugyanazt a helyet kell használnia.|
   |Fiók altípusa|BlobStorage|

   ![adja meg a Storage-fiók adatait](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. Ezután válassza a **Létrehozás** lehetőséget a Storage-fiók telepítéséhez.

4. Az üzembe helyezés befejezésekor navigáljon a Storage-fiókhoz. A **Blob Service** szakaszban válassza a **Tárolók** lehetőséget. Ezután a **+ tároló** lehetőségre kattintva hozzon létre egy új tárolót.

   ![BLOB Storage-tároló létrehozása a bevitelhez](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Adja meg a tároló nevét, és győződjön meg arról, hogy a **nyilvános hozzáférési szint** **privát** értékre van állítva. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   ![BLOB-tároló részleteinek megadása](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Navigáljon az újonnan létrehozott tárolóhoz, és válassza a **feltöltés** lehetőséget. Töltse fel a korábban letöltött **sampleinput.csv** fájlt.

   ![Tároló "feltöltés" gombja](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adja hozzá az Cortana Intelligence Gallery

Most, hogy a mintaadatok egy blobban vannak, a Cortana Intelligence Galleryban engedélyezheti a hangulat elemzési modelljét.

1. Lépjen az Cortana Intelligence Gallery [prediktív hangulat elemzési modell](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) lapjára.  

2. Válassza **a Megnyitás a Studióban (klasszikus)** lehetőséget.  
   
   ![Stream Analytics Azure Machine Learning Studio (klasszikus), Open Studio (klasszikus)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. A munkaterületre való ugráshoz jelentkezzen be. Válasszon ki egy helyet.

4. Válassza a **Futtatás** lehetőséget az oldal alján. A folyamat fut, ami körülbelül egy percet vesz igénybe.

   ![Kísérlet futtatása a Studióban (klasszikus)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. A folyamat sikeres futtatása után válassza a **webszolgáltatások üzembe helyezése** elemet az oldal alján.

   ![Kísérlet üzembe helyezése a Studio (klasszikus) szolgáltatásban webszolgáltatásként](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Annak ellenőrzéséhez, hogy a hangulat-elemzési modell készen áll-e a használatra, válassza a **teszt** gombot. Adja meg a szövegbeviteli adatokat, például a "szeretem a Microsoftot".

   ![Tesztelési kísérlet a Studióban (klasszikus)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   Ha a teszt működik, az alábbi példához hasonló eredmény jelenik meg:

   ![Tesztelési eredmények a Studióban (klasszikus)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Az **alkalmazások** oszlopban válassza az **Excel 2010 vagy korábbi munkafüzet** hivatkozását az Excel-munkafüzet letöltéséhez. A munkafüzet tartalmazza azt az API-kulcsot és URL-címet, amelyre később szükség lesz a Stream Analytics feladatokhoz.

    ![Stream Analytics Azure Machine Learning Studio (klasszikus), gyors áttekintés](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>A Studio (klasszikus) modellt használó Stream Analytics-feladatok létrehozása

Most létrehozhat egy Stream Analytics feladatot, amely a blob Storage-ban lévő CSV-fájlból beolvassa a minta tweeteket.

### <a name="create-the-job"></a>A feladat létrehozása

Lépjen a [Azure Portalra](https://portal.azure.com) , és hozzon létre egy stream Analytics feladatot. Ha nem ismeri ezt a folyamatot, olvassa el a [stream Analytics-feladatok létrehozása a Azure Portal használatával](stream-analytics-quick-create-portal.md)című témakört.

### <a name="configure-the-job-input"></a>A feladatok bevitelének konfigurálása

A feladatot a rendszer a blob Storage-ba korábban feltöltött CSV-fájlból olvassa be.

1. Keresse meg a Stream Analytics-feladatot. A **feladatok topológiája** területen válassza a **bemenetek** lehetőséget. Válassza a **stream bemeneti**  > **blob Storage** hozzáadása lehetőséget.

2. Töltse ki a **blob Storage** részleteket a következő értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   |Bemeneti alias|Adja meg a bemenet nevét. Jegyezze fel ezt az aliast a lekérdezés írásakor.|
   |Előfizetés|Válassza ki előfizetését.|
   |A(z)|Válassza ki az előző lépésben létrehozott Storage-fiókot.|
   |Tároló|Válassza ki az előző lépésben létrehozott tárolót.|
   |Eseményszerializációs formátum|CSV|

3. Kattintson a **Mentés** gombra.

### <a name="configure-the-job-output"></a>A feladatok kimenetének konfigurálása

A művelet az eredményeket ugyanarra a blob-tárolóra küldi, ahol a bemenet bekerül.

1. Keresse meg a Stream Analytics-feladatot. A **feladatok topológiája** területen válassza a **kimenetek** lehetőséget. Válassza a  >  **blob Storage** hozzáadása elemet.

2. Töltse ki a **blob Storage** űrlapot a következő értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   |Bemeneti alias|Adja meg a bemenet nevét. Jegyezze fel ezt az aliast a lekérdezés írásakor.|
   |Előfizetés|Válassza ki előfizetését.|
   |A(z)|Válassza ki az előző lépésben létrehozott Storage-fiókot.|
   |Tároló|Válassza ki az előző lépésben létrehozott tárolót.|
   |Eseményszerializációs formátum|CSV|

3. Kattintson a **Mentés** gombra.

### <a name="add-the-studio-classic-function"></a>A Studio (klasszikus) függvény hozzáadása

Korábban egy Studio (klasszikus) modellt adott közzé egy webszolgáltatáshoz. Ebben az esetben a stream Analysis-feladatok futtatásakor a rendszer az összes minta tweetet elküldi a webszolgáltatásnak az érzelmek elemzéséhez. A Studio (klasszikus) webszolgáltatás egy érzelem ( `positive` ,, vagy) értéket ad vissza, `neutral` és a `negative` csiripelés valószínűsége pozitív.

Ebben a szakaszban egy függvényt határoz meg a stream Analysis feladatban. A függvény meghívható úgy, hogy csipogt küldjön a webszolgáltatásnak, és visszakapja a választ.

1. Győződjön meg arról, hogy rendelkezik az Excel-munkafüzetben korábban letöltött webszolgáltatás URL-címével és API-kulcsával.

2. Keresse meg a Stream Analytics-feladatot. Ezután válassza a **függvények**  >  **+**  >  **Azure-ml Studio** hozzáadása lehetőséget

3. Töltse ki a **Azure Machine learning függvény** űrlapot a következő értékekkel:

   |Mező  |Érték  |
   |---------|---------|
   | Függvény aliasa | Használja a nevet, `sentiment` majd válassza a **Azure Machine learning-függvény beállításainak megadása manuálisan** lehetőséget, amely lehetővé teszi az URL-cím és a kulcs megadását.      |
   | URL-cím| Illessze be a webszolgáltatás URL-címét.|
   |Kulcs | Illessze be az API-kulcsot. |

4. Válassza a **Mentés** lehetőséget.

### <a name="create-a-query-to-transform-the-data"></a>Lekérdezés létrehozása az adatátalakításhoz

Stream Analytics egy deklaratív, SQL-alapú lekérdezést használ a bevitel vizsgálatára és feldolgozására. Ebben a szakaszban egy olyan lekérdezést hoz létre, amely beolvassa az egyes tweeteket a bemenetből, majd meghívja a Studio (klasszikus) függvényt az érzelmi elemzés elvégzéséhez. A lekérdezés ezután elküldi az eredményt a megadott kimenetre (blob Storage).

1. Térjen vissza a Stream Analytics feladatok áttekintéséhez.

2. A **feladatok topológiája** területen válassza a **lekérdezés** lehetőséget.

3. Adja meg a következő lekérdezést:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   A lekérdezés meghívja a korábban létrehozott függvényt `sentiment` , hogy elvégezze az összes Tweet-elemzést a bemenetben.

4. a lekérdezés mentéséhez válassza a **Mentés** lehetőséget.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>A Stream Analytics-feladat indítása és a kimenet ellenőrzése

Most már elindíthatja a Stream Analytics feladatot.

### <a name="start-the-job"></a>A feladat indítása

1. Térjen vissza a Stream Analytics feladatok áttekintéséhez.

2. Válassza a **Start** lehetőséget az oldal tetején.

3. A **kezdési feladatban** válassza az **Egyéni** lehetőséget, majd válasszon egy napot, mielőtt feltölti a CSV-fájlt a blob Storage-ba. Amikor elkészült, válassza ki az **Indítás** lehetőséget.  

### <a name="check-the-output"></a>A kimenet keresése

1. A feladatok futtatásának engedélyezése néhány percig, amíg meg nem jelenik a tevékenység a **figyelés** mezőben.

2. Ha olyan eszközzel rendelkezik, amelyet általában a blob Storage tartalmának vizsgálatára használ, az eszköz használatával vizsgálja meg a tárolót. Másik lehetőségként hajtsa végre a következő lépéseket a Azure Portalban:

      1. A Azure Portal keresse meg a Storage-fiókját, és a fiókon belül keresse meg a tárolót. A tárolóban két fájl látható: Ez a fájl tartalmazza a minta tweeteket és a Stream Analytics feladatokból létrehozott CSV-fájlt.
      2. Kattintson a jobb gombbal a létrehozott fájlra, majd válassza a **Letöltés** lehetőséget.

3. Nyissa meg a létrehozott CSV-fájlt. A következő példához hasonlóan jelenik meg:  

   ![Stream Analytics Azure Machine Learning Studio (klasszikus), CSV-nézet](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>Metrikák megtekintése

Emellett megtekintheti a Studio (klasszikus) függvényekkel kapcsolatos metrikákat is. A következő függvényhez kapcsolódó mérőszámok a feladat áttekintésének **figyelés** mezőjében jelennek meg:

* A **függvények kérései** a Studio (klasszikus) webszolgáltatás számára küldött kérések számát jelzik.  
* A **függvény eseményei** a kérelemben szereplő események számát jelzik. Alapértelmezés szerint a Studio (klasszikus) webszolgáltatás minden kérése legfeljebb 1 000 eseményt tartalmaz.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API és Machine Learning Studio integrálása (klasszikus)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)