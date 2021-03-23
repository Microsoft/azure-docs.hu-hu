---
title: Azure Data Factory – minták
description: A Azure Data Factory szolgáltatással szállított minták adatait tartalmazza.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0d78cc9d99bd7ace2d214c2a2a1d7f75a74df098
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785053"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory – minták
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg [Az Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=data-factory)katalógusában található [PowerShell-példákat a Data Factory](../samples-powershell.md) és a kód példákban.


## <a name="samples-on-github"></a>Példák a GitHubon
A [GitHub Azure-DataFactory adattár](https://github.com/azure/azure-datafactory) számos olyan mintát tartalmaz, amelyek segítségével gyorsan felgyorsítható a Azure Data Factory szolgáltatás (vagy) a parancsfájlok módosítása és használata a saját alkalmazásokban. A Samples\JSON mappa a gyakori forgatókönyvek JSON-kódrészleteit tartalmazza.

| Sample | Leírás |
|:--- |:--- |
| [ADF-útmutató](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Ez a példa egy teljes körű útmutatót biztosít a naplófájlok feldolgozásához a Azure Data Factory használatával az adatoknak a naplófájlokban való bekapcsolásához. <br/><br/>Ebben az útmutatóban a Data Factory folyamat adatnaplókat gyűjt, feldolgozza és gazdagítja a naplókból származó adatokat, és átalakítja az adatokat, hogy kiértékelje a közelmúltban elindított marketingkampány hatékonyságát. |
| [JSON-minták](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Ez a példa JSON-példákat biztosít a gyakori forgatókönyvekhez. |
| [Http-adatletöltő minta](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Ez a példa az adatok HTTP-végpontról Blob Storage Azure-ba történő letöltését mutatja be az egyéni .NET-tevékenységek használatával. |
| [Több mint alkalmazástartomány dot Net-tevékenység mintája](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Ez a minta lehetővé teszi egy olyan egyéni .NET-tevékenység létrehozását, amely nem korlátozódik az ADF-indító által használt szerelvény-verzióra (például WindowsAzure. Storage v 4.3.0, Newtonsoft.Jsv 6.0. x stb.). |
| [R-szkript futtatása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Ez a minta tartalmazza a RScript.exe meghívásához használható Data Factory egyéni tevékenységet. Ez a minta csak a saját (nem igény szerinti) HDInsight-fürttel működik, amelyen már telepítve van az R. |
| [Spark-feladatok meghívása a HDInsight Hadoop-fürtön](../tutorial-transform-data-spark-portal.md) |Ez a minta bemutatja, hogyan hívhat meg egy Spark-programot a MapReduce tevékenység használatával. A Spark program csak az egyik Azure Blob-tárolóból másol át egy másikba. |
| [Twitter-elemzés Azure Machine Learning Studio (klasszikus) batch pontozási tevékenység használatával](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Ez a minta azt mutatja be, hogyan használható a AzureMLBatchScoringActivity egy olyan Azure Machine Learning modell meghívására, amely Twitter-hangulat elemzését, pontozását, előrejelzését stb. |
| [Twitter-elemzés egyéni tevékenység használatával](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Ez a minta azt mutatja be, hogyan használható egy egyéni .NET-tevékenység egy olyan Azure Machine Learning modell meghívásához, amely Twitter-hangulat elemzését, pontozását, előrejelzését stb. végzi. |
| [Paraméteres folyamatok Azure Machine Learninghoz](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |A minta egy végpontok közötti C#-kódot biztosít, amellyel N folyamatokat helyezhet üzembe a pontozáshoz és az átképzéshez egy másik régió paraméterrel, ahol a régiók listája egy parameters.txt fájlból származik, amely ebben a példában szerepel. |
| [A Azure Stream Analytics-feladatokra vonatkozó referenciák frissítése](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Ez a minta azt mutatja be, hogyan használhatók a Azure Data Factory és a Azure Stream Analytics együtt a lekérdezések és a hivatkozási adataik futtatására, valamint az ütemtervek frissítésének beállítására. |
| [Hibrid folyamat helyszíni Hortonworks-Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |A minta egy helyszíni Hadoop-fürtöt használ számítási célként a feladatok Data Factory való futtatásához, ugyanúgy, mint a Felhőbeli HDInsight-alapú Hadoop-fürtökhöz. |
| [JSON-átalakító eszköz](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Ez az eszköz lehetővé teszi a JSON-verziók konvertálását a 2015-07-01 előtti verzióról a legújabbra vagy a 2015-07-01-Preview-ra (alapértelmezett). |
| [U-SQL minta bemeneti fájl](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Ez a fájl egy U-SQL-tevékenység által használt minta fájl. |
| [BLOB-fájl törlése](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Ez a példa egy C#-fájlt mutat be, amely az egyéni .net-tevékenység ADF részeként használható a fájlok másolását követően a forrás Azure-Blob helyéről való törléséhez.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
A Data Factory a GitHubon a következő Azure Resource Manager sablonokat találhatja meg.

| Sablon | Leírás |
| --- | --- |
| [Másolás az Azure Blob Storageból a Azure SQL Databaseba](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |A sablon üzembe helyezése egy Azure-beli adatelőállítót hoz létre egy olyan folyamattal, amely átmásolja a megadott Azure Blob Storage-ból származó adatok Azure SQL Database |
| [Másolás a Salesforce-ből az Azure-ba Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |A sablon üzembe helyezése egy Azure-beli adatelőállítót hoz létre egy olyan folyamattal, amely átmásolja a megadott Salesforce-fiókból az Azure Blob Storage-ba. |
| [Adatátalakítás a kaptár-parancsfájl futtatásával egy Azure HDInsight-fürtön](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |A sablon üzembe helyezése egy Azure-beli adatelőállítót hoz létre egy olyan folyamattal, amely átalakítja az adatszerkezetet egy Azure HDInsight Hadoop-fürtön a minta struktúra-parancsfájl futtatásával. |

## <a name="samples-in-azure-portal"></a>Minták Azure Portal
Az adat-előállító kezdőlapján a **mintavételezési folyamatok** csempe használatával üzembe helyezheti a mintavételezési folyamatokat és a hozzájuk tartozó entitásokat (adatkészleteket és társított szolgáltatásokat) az adat-előállítóba.

1. Hozzon létre egy adatelőállítót, vagy nyisson meg egy meglévő adatgyárat. Az adatok-előállító létrehozásához szükséges lépésekért lásd: [adatok másolása blob Storageról SQL Databasera Data Factory használatával](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. Az adatelőállító **adatelőállító** paneljén kattintson a **mintavételezési folyamatok** csempére.

    ![Mintavételezési folyamatok csempe](./media/data-factory-samples/SamplePipelinesTile.png)
3. A **mintavételezési folyamatok** panelen kattintson arra a **mintára** , amelyet központilag telepíteni szeretne.

    ![Mintavételezési folyamatok panel](./media/data-factory-samples/SampleTile.png)
4. A minta konfigurációs beállításainak megadása. Például az Azure Storage-fiók neve és a fiók kulcsa, a logikai SQL-kiszolgáló neve, az adatbázis, a felhasználói azonosító és a jelszó stb.

    ![Minta panel](./media/data-factory-samples/SampleBlade.png)
5. Miután végzett a konfigurációs beállítások megadásával, a **Létrehozás** gombra kattintva hozhatja létre és helyezheti üzembe a folyamatok által használt mintavételi folyamatokat és társított szolgáltatásokat/táblákat.
6. A telepítési állapotot a minta csempén láthatja, amelyet korábban a **mintavételezési folyamatok** panelen kattintott.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik az **üzembe helyezés sikeres** üzenete a minta csempén, akkor zárjuk be a **mintavételezési folyamatok** panelt.  
8. A **adat-előállító** panelen láthatja, hogy a társított szolgáltatások, adatkészletek és folyamatok hozzáadódnak az adat-előállítóhoz.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Minták a Visual Studióban
### <a name="prerequisites"></a>Előfeltételek
A számítógépre a következőket kell telepíteni:

* Visual Studio 2013 vagy Visual Studio 2015
* Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
* Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Ha a Visual Studio 2013-at használja, a beépülő modult a következő lépések végrehajtásával is frissítheti: a menüben kattintson az **eszközök**  ->  **bővítmények** elemre, és frissítse az  ->  **online**  ->  **Visual Studio Gallery**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update szolgáltatást**.

### <a name="use-data-factory-templates"></a>Data Factory-sablonok használata
1. Kattintson a menüben a **fájl** elemre, mutasson az **új** elemre, majd kattintson a **projekt** elemre.
2. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

   1. A **sablonok** területen válassza a **DataFactory** lehetőséget.
   2. Válassza ki **Data Factory sablonokat** a jobb oldali ablaktáblán.
   3. Adjon meg a projekt **nevét**.
   4. Válassza ki a projekt **helyét** .
   5. Kattintson az **OK** gombra.

      ![A New project (Új projekt) párbeszédpanel](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. A **Data Factory sablonok** párbeszédpanelen válassza ki a minta sablont a **használati eset sablonok** szakaszban, majd kattintson a **tovább** gombra. Az alábbi lépések végigvezetik az **ügyfél profilkészítési** sablonjának használatával. A lépések hasonlóak a többi mintához.

    ![Data Factory Sablonok párbeszédpanel](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. A **Data Factory konfiguráció** párbeszédpanelen kattintson a **tovább** gombra az **Data Factory alapjai** lapon.
5. Az **adatgyár konfigurálása** oldalon hajtsa végre a következő lépéseket:
   1. Válassza az **új Data Factory létrehozása** lehetőséget. Kiválaszthatja a **meglévő adatelőállító használata** lehetőséget is.
   2. Adja meg az adatelőállító **nevét** .
   3. Válassza ki azt az **Azure-előfizetést** , amelyben létre kívánja hozni az adatbázis-előállítót.
   4. Válassza ki az adatelőállítóhoz tartozó **erőforráscsoportot** .
   5. Válassza ki az **USA nyugati** régiójában, az **USA keleti** **régiójában** vagy **Észak-Európában** .
   6. Kattintson a **Tovább** gombra.
6. Az **adattárak konfigurálása** lapon adja meg a meglévő **adatbázist a Azure SQL Database** és az **Azure Storage-fiókban** (vagy) hozzon létre egy adatbázist/tárolót, és kattintson a Tovább gombra.
7. A **számítás konfigurálása** lapon válassza az Alapértelmezések lehetőséget, majd kattintson a **tovább** gombra.
8. Az **Összefoglalás** lapon tekintse át az összes beállítást, majd kattintson a **tovább** gombra.
9. A **telepítés állapota** lapon várja meg a telepítés befejeződését, majd kattintson a **Befejezés** gombra.
10. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra.
11. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
12. A következő párbeszédpanelnek kell megjelennie:

    ![Publish (Közzététel) párbeszédpanel](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. A **Data Factory konfigurálási** lapján hajtsa végre a következő lépéseket:

    1. Győződjön meg arról, hogy a **meglévő adatgyár használata** lehetőség van.
    2. Válassza ki a sablon használatakor kiválasztott **adatelőállítót** .
    3. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz. (Ha a **Tovább** gomb le van tiltva, nyomja le a **TAB** billentyűt a Name (Név) mezőből való kilépéshez.)
14. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.     
15. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
16. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra.

Lásd: [az első adat-előállító létrehozása (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) a Visual studio használatával Data Factory entitások létrehozásához és az Azure-ba való közzétételéhez.