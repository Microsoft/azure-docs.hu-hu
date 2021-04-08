---
title: Databricks-jegyzetfüzet futtatása a tevékenységgel
description: Ismerje meg a Databricks-jegyzetfüzetek tevékenységeinek használatát egy Azure adat-előállítóban a Databricks-jegyzetfüzetek Databricks-feladatfürtön való futtatásához.
ms.service: data-factory
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 03/12/2018
ms.openlocfilehash: 20253954035798187f28c18c8207c114d27bc9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374082"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Databricks-jegyzetfüzet futtatása a Databricks-jegyzetfüzet tevékenységeivel az Azure Data Factoryban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban egy olyan Azure Data Factory-folyamatot hoz létre az Azure Portal használatával, amely végrehajt egy Databricks-jegyzetfüzetet a Databricks-feladatfürtön. Továbbítja továbbá az Azure Data Factory paramétereit a Databricks-jegyzetfüzetnek a végrehajtás során.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

  - Adat-előállító létrehozása

  - Hozzon létre egy Databricks-jegyzetfüzetek tevékenységeit használó folyamatot.

  - Folyamat futtatásának aktiválása

  - A folyamat futásának monitorozása.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Előfeltételek

  - **Azure Databricks munkaterület**. [Hozzon létre egy Databricks-munkaterületet](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal), vagy használjon egy már meglévőt. Hozzon létre egy Python-jegyzetfüzetet az Azure Databricks-munkaterületen. Ezután hajtsa végre a jegyzetfüzetet, és továbbítson neki paramétereket az Azure Data Factory segítségével.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1.  Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.

1.  Kattintson az **Erőforrás létrehozása** elemre a bal oldali menüben, majd az **Analitika**, végül a **Data Factory** elemre.

    ![Új adat-előállító létrehozása](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az Azure-beli adatgyár nevének *globálisan egyedinek* kell lennie. Ha a következő hibát látja, módosítsa az adat-előállító nevét. (Például a **\<yourname\> ADFTutorialDataFactory** használata). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](./naming-rules.md) ismertető cikkben találja.

    ![Az új adat-előállító elnevezése](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni.

1.  **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
    
    - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.
    
    - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.

    A rövid útmutató egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.

1.  A **Verzió** résznél válassza a **V2** értéket.

1.  **Hely:** válassza ki az adat-előállító helyét.

    Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. A Data Factory által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
1.  Válassza a **Létrehozás** lehetőséget.


1.  A létrehozás befejezése után megjelenik az **Adat-előállító** lap. A Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

    ![Az adat-előállító felhasználói felület alkalmazás indítása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a szakaszban létrehoz egy Databricks társított szolgáltatást. Ez a társított szolgáltatás tartalmazza a Databricks-fürt kapcsolatadatait:

### <a name="create-an-azure-databricks-linked-service"></a>Azure Databricks-beli társított szolgáltatás létrehozása

1.  Az **Első lépések** lapon váltson a **Szerkesztés** lapra a bal oldali ablaktáblán.

    ![Az új társított szolgáltatás szerkesztése](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Kattintson az ablak alján látható **Kapcsolatok**, majd az **+ Új** elemre.
    
    ![Új kapcsolat létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Az **Új társított szolgáltatás** ablakban válassza a **Compute** \> **Azure Databricks** lehetőséget, majd kattintson a **Folytatás** elemre.
    
    ![Databricks társított szolgáltatás megadása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket:
    
    1.  A **név** mezőbe írja be a következőt: **_AzureDatabricks \_ LinkedService_**
    
    1.  Válassza ki a megfelelő **Databricks-munkaterületet**, amelyen a jegyzetfüzetet futtatni fogja

    1.  A **Fürt kiválasztása** mezőben válassza az **Új feladatfürt** lehetőséget
    
    1.  A **Tartomány/régió** mezőben az adatoknak automatikusan ki kell töltődnie

    1.  A **Hozzáférési jogkivonatot** hozza létre az Azure Databricks-munkaterületről. A lépéseket [itt](https://docs.databricks.com/api/latest/authentication.html#generate-token) találhatja meg.

    1.  A **fürt verziója** esetében válassza a **4,2** elemet (Apache Spark 2.3.1, Scala 2,11)

    1.  A **Fürtcsomópont típusa** mezőben válassza a **Standard\_D3\_v2** lehetőséget az **Általános célú (HDD)** területen ehhez az oktatóanyaghoz. 
    
    1.  A **Feldolgozók** mezőben adja meg a **2** mennyiséget.
    
    1.  Válassza a **Befejezés** lehetőséget

        ![A társított szolgáltatás létrehozásának befejezése](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1.  Válassza a **+** (plusz) gombot, majd válassza a menü **folyamat** elemét.

    ![Új folyamat létrehozására szolgáló gombok](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Hozzon létre egy, a **folyamatban** használni kívánt **paramétert**. Később ezt a paramétert átadhatja a Databricks-jegyzetfüzet tevékenységeinek. Az üres folyamatban kattintson a **Paraméterek** lapra, majd az **Új** lehetőségre, és adja neki a „**name**” nevet.

    ![Új paraméter létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![A név paraméter létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  A **Tevékenységek** eszközkészletben bontsa ki a **Databricks** elemet. Húzza a **Jegyzetfüzet** tevékenységet a **Tevékenységek** eszközkészletből a folyamat tervezőfelületére.

    ![A jegyzetfüzet áthúzása a tervezőfelületre](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  A **Databricks-** **jegyzetfüzet** tevékenységeinek tulajdonságok ablakában végezze el az alábbi lépéseket:

    a. Váltson az **Azure Databricks** lapra.

    b. Válassza ki az előző lépésben létrehozott **AzureDatabricks\_LinkedService** elemet.

    c. Váltson a **Beállítások** lapra

    c. Tallózással válasszon ki egy Databricks-**jegyzetfüzet útvonalat**. Hozzunk létre egy jegyzetfüzetet, és adjuk meg itt az útvonalat. A következő néhány lépést követve megkaphatja a jegyzetfüzet útvonalat.

       1. Az Azure Databricks-munkaterület indítása

       1. Hozzon létre egy **Új mappát** a munkaterületen, és adja neki az **adftutorial** nevet.

          ![Új mappa létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Hozzon létre egy új jegyzetfüzetet](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), hívjuk meg **mynotebook** a **adftutorial** mappában, és kattintson a **Létrehozás gombra.**

          ![Új jegyzetfüzet létrehozása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Az új jegyzetfüzet tulajdonságainak beállítása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. Az újonnan létrehozott „mynotebook” jegyzetfüzetben adja hozzá a következő kódot:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Widgetek létrehozása paraméterekhez](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. Ebben az esetben a **jegyzetfüzet útvonala****/adftutorial/mynotebook**.

1.  Váltson vissza a **Data Factory felhasználói felületet létrehozó eszközre**. Navigáljon a **Beállítások** lapra az **1. jegyzetfüzet tevékenységei** területen.

    a.  **Adjon paramétert** a jegyzetfüzet tevékenységeihez. Ugyanazt a paramétert használja, mint amit korábban hozzáadott a **folyamathoz**.

       ![Paraméter hozzáadása](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Adjon nevet a paraméternek **bemenetként** , és adja meg az értéket kifejezés-feldolgozási **\@ (). Parameters. name** néven.

1.  A folyamat érvényesítéséhez kattintson az **Érvényesítés** gombra az eszköztáron. Az érvényesítési ablak bezárásához kattintson a **\>\>** (jobbra mutató nyíl) gombra.

    ![A folyamat érvényesítése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Kattintson **Az összes közzététele** gombra. A Data Factory felhasználói felülete entitásokat (társított szolgáltatásokat és folyamatot) tesz közzé az Azure Data Factory szolgáltatásban.

    ![Az új adat-előállító entitások közzététele](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget.

![Az Aktiválás most parancs kiválasztása](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A **Folyamatfuttatás** párbeszédpanel kéri a **név** paramétert. Itt paraméterként használja a következőt: **/path/filename**. Kattintson a **Befejezés gombra.**

![Érték megadása a név paraméterekhez](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1.  Váltson a **figyelés** lapra. Győződjön meg arról, hogy a folyamat futása megjelenik. Egy, a jegyzetfüzetet végrehajtó Databricks feladatfürt létrehozása körülbelül 5–8 percet vesz igénybe.

    ![A folyamat figyelése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Rendszeres időközönként kattintson a **Frissítés** gombra a folyamat futási állapotának ellenőrzéséhez.

1.  A folyamat futásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** elemre a **Műveletek** oszlopban.

    ![A tevékenységfuttatások megtekintése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

A fenti **Folyamatok** elemre kattintva visszaválthat a folyamatfuttatások nézetre.

## <a name="verify-the-output"></a>Kimenet ellenőrzése

Jelentkezzen be az **Azure Databricks-munkaterületre**, lépjen a **Fürtökhöz**, és láthatja, hogy a **Feladat** állapota *végrehajtás függőben, fut vagy megszakítva*.

![A feladatfürt és a feladat megtekintése](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

A **Feladat nevére** kattintva megtekintheti a további részleteket. Ha a futás sikeres, érvényesítheti az átadott paramétereket és a Python-jegyzetfüzet kimenetét.

![A futtatás részleteinek és kimenetének megtekintése](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Következő lépések

A mintában szereplő folyamat elindít egy Databricks-jegyzetfüzet tevékenységet, és átad neki egy paramétert. Megtanulta végrehajtani az alábbi műveleteket:

  - Adat-előállító létrehozása

  - Hozzon létre egy Databricks-jegyzetfüzetek tevékenységeit használó folyamatot.

  - Folyamat futtatásának aktiválása

  - A folyamat futásának monitorozása.