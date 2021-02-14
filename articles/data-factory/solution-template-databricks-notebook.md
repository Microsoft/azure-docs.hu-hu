---
title: Átalakítás az Azure Databricks szolgáltatással
description: Megtudhatja, hogyan alakíthat át egy megoldást sablon használatával az adatátalakításhoz a Azure Data Factory Databricks-jegyzetfüzettel.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362114"
---
# <a name="transformation-with-azure-databricks"></a>Átalakítás az Azure Databricks szolgáltatással

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban egy végpontok közötti folyamatot hoz létre, amely tartalmazza az **érvényesítési**, az **adatmásolási** és a **Jegyzetfüzet** -tevékenységeket Azure Data Factoryban.

- Az **ellenőrzéssel** biztosítható, hogy a forrás-adatkészlet készen álljon az alsóbb rétegbeli felhasználásra, mielőtt aktiválja a másolási és elemzési feladatot.

- Az **Adatmásolás** duplikálja a forrás adatkészletet a fogadó tárolóba, amely a Azure Databricks jegyzetfüzetben DBFS van csatlakoztatva. Így az adatkészletet közvetlenül a Spark használhatja fel.

- A **Jegyzetfüzet** elindítja a Databricks-jegyzetfüzetet, amely átalakítja az adatkészletet. Emellett hozzáadja az adatkészletet egy feldolgozott mappához vagy az Azure Azure szinapszis Analytics szolgáltatáshoz.

Az egyszerűség kedvéért az oktatóanyagban szereplő sablon nem hoz létre ütemezett triggert. Szükség esetén egyet is hozzáadhat.

![A folyamat ábrája](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Blob Storage-fiók, amely fogadóként való használatra lett meghívva `sinkdata` .

  Jegyezze fel a Storage-fiók nevét, a tároló nevét és a hozzáférési kulcsot. Ezekre az értékekre később szüksége lesz a sablonban.

- Egy Azure Databricks munkaterület.

## <a name="import-a-notebook-for-transformation"></a>Jegyzetfüzet importálása átalakításhoz

**Átalakítási** jegyzetfüzet importálása a Databricks-munkaterületre:

1. Jelentkezzen be Azure Databricks munkaterületére, majd válassza az **Importálás** lehetőséget.
       ![A munkaterületek importálására szolgáló menüparancsok a ](media/solution-template-Databricks-notebook/import-notebook.png) munkaterület elérési útjának különbözőek lehetnek, de később is megjegyezhető.
1. Válassza **az Importálás innen: URL** lehetőséget. A szövegmezőbe írja be a szöveget `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Jegyzetfüzetek importálásának kiválasztása](media/solution-template-Databricks-notebook/import-from-url.png)

1. Most frissítse az **átalakítási** jegyzetfüzetet a Storage-kapcsolatok adataival.

   Az importált jegyzetfüzetben lépjen az **5. parancsra** az alábbi kódrészletben látható módon.

   - Cserélje le `<storage name>` a és a értékét `<access key>` a saját Storage-kapcsolatok adataira.
   - Használja a Storage-fiókot a `sinkdata` tárolóval.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. **Databricks hozzáférési token** létrehozása Data Factory számára a Databricks eléréséhez.
   1. A Databricks munkaterületen válassza ki a felhasználói profil ikont a jobb felső sarokban.
   1. Válassza a **felhasználói beállítások** lehetőséget.
    ![A felhasználói beállítások menü parancsa](media/solution-template-Databricks-notebook/user-setting.png)
   1. Válassza az **új jogkivonat előállítása** lehetőséget a **hozzáférési tokenek** lapon.
   1. Válassza a **készítés** lehetőséget.

    !["Generált" gomb](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Mentse a hozzáférési jogkivonatot* későbbi használatra a Databricks társított szolgáltatás létrehozásához. A hozzáférési jogkivonat valahogy így néz ki `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>A sablon használata

1. Nyissa meg az **átalakítást Azure Databricks** sablonnal, és hozzon létre új társított szolgáltatásokat a következő kapcsolatokhoz.

   ![Kapcsolatok beállítása](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Forrásként szolgáló blob-kapcsolat** – a forrásadatok eléréséhez.

       Ebben a gyakorlatban használhatja a forrásfájlokat tartalmazó nyilvános BLOB-tárolót. Az alábbi képernyőképen a konfigurációra kell hivatkoznia. A következő **sas URL-cím** használatával csatlakozhat a forrás tárolóhoz (csak olvasási hozzáférés):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![A hitelesítési módszer és az SAS URL-cím kiválasztása](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Cél blob-kapcsolódás** – a másolt adattárolók tárolása.

       Az **új társított szolgáltatás** ablakban válassza ki a fogadó tároló blobját.

       ![Tároló-blob fogadó új társított szolgáltatásként](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** – a Databricks-fürthöz való kapcsolódáshoz.

        Hozzon létre egy Databricks társított szolgáltatást a korábban létrehozott hozzáférési kulcs használatával. Ha van, választhat egy *interaktív fürtöt* . Ez a példa az **új feladatütemezés** beállítást használja.

        ![A fürthöz való csatlakozásra kiválasztott beállítások](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Válassza **a sablon használata** lehetőséget. Ekkor létrejön egy folyamat.

    ![Folyamat létrehozása](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>A folyamat bevezetése és konfigurálása

Az új folyamat során a legtöbb beállítás automatikusan be van állítva az alapértelmezett értékekkel. Tekintse át a folyamat konfigurációit, és végezze el a szükséges módosításokat.

1. Az **érvényesítési** tevékenység **rendelkezésre állása jelzőben** ellenőrizze, hogy a forrás **adatkészlet** értéke a korábban létrehozott értékre van-e állítva `SourceAvailabilityDataset` .

   ![Forrás adatkészlet értéke](media/solution-template-Databricks-notebook/validation-settings.png)

1. Az **Adatmásolási** tevékenység **fájl – blob** elemnél keresse meg a **forrás** és **a** fogadó lapokat. Szükség esetén módosítsa a beállításokat.

   - **Forrás** lap ![ forrás lapja](media/solution-template-Databricks-notebook/copy-source-settings.png)

   -  Fogadó lap fogadójának ![ lapja](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. A **Jegyzetfüzet** tevékenység- **átalakítás** területén szükség szerint tekintse át és frissítse az elérési utakat és beállításokat.

   A **Databricks társított szolgáltatásnak** előre ki kell töltenie egy előző lépésben megadott értékkel, ahogy az látható: ![ a Databricks társított szolgáltatás feltöltésének értéke](media/solution-template-Databricks-notebook/notebook-activity.png)

   A **Jegyzetfüzet** beállításainak ellenõrzése:
  
    1. Válassza a **Beállítások** lapot. A **Jegyzetfüzet elérési útja** beállításnál ellenőrizze, hogy helyes-e az alapértelmezett elérési út. Előfordulhat, hogy a megfelelő jegyzetfüzet elérési útját kell megkeresnie és kiválasztania.

       ![Jegyzetfüzet elérési útja](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Bontsa ki az **alapparaméterek** választót, és ellenőrizze, hogy a paraméterek egyeznek-e a következő képernyőképen láthatókkal. Ezeket a paramétereket a rendszer átadja a Databricks notebookjának Data Factory.

       ![Alapparaméterek](media/solution-template-Databricks-notebook/base-parameters.png)

1. Ellenőrizze, hogy a **folyamat paramétereinek** megfelelnek-e a következő képernyőképen láthatók: ![ folyamat paraméterei](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Kapcsolódjon az adatkészletekhez.

    >[!NOTE]
    >Az alábbi adatkészletekben a fájl elérési útja automatikusan meg van adva a sablonban. Ha bármilyen módosításra van szükség, győződjön meg arról, hogy a **tároló** és a **könyvtár** elérési útját is megadja, ha bármilyen hiba történt.

   - **SourceAvailabilityDataset** – annak ellenőrzését, hogy a forrásadatok elérhetők-e.

     ![A társított szolgáltatás és a fájl elérési útjának kijelölése a SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** – a forrásadatok eléréséhez.

       ![A társított szolgáltatás és a fájl elérési útjának kijelölése a SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** – az adatgyűjtés célhelyére másolhatja az Adatmásolást. Használja a következő értékeket:

     - **Társított szolgáltatás**  -  `sinkBlob_LS` , amelyet az előző lépésben hozott létre.

     - **Fájl elérési útja**  -  `sinkdata/staged_sink`

       ![A társított szolgáltatás és a fájl elérési útjának kijelölése a DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Válassza a **hibakeresés** lehetőséget a folyamat futtatásához. A Databricks-naplókra mutató hivatkozás a részletes Spark-naplókra mutat.

    ![Hivatkozás a kimenetből származó Databricks-naplókra](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Az adatfájlt Azure Storage Explorer használatával is ellenőrizheti.

    > [!NOTE]
    > A Data Factory-folyamat futtatásával való korrelációhoz ez a példa a folyamat futtatási AZONOSÍTÓját fűzi hozzá az adatok gyárból a kimeneti mappába. Ez segít nyomon követni az egyes futtatások által létrehozott fájlokat.
    > ![Hozzáfűzött folyamat futtatási azonosítója](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory bemutatása](introduction.md)
