---
title: Az első adatgyár létrehozása (Visual Studio)
description: Az oktatóanyag során létrehoz egy mintául szolgáló Azure Data Factory-folyamatot a Visual Studio használatával.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: vs-azure
ms.date: 01/22/2018
ms.openlocfilehash: 2ae8e24b10a6661f5a18f3d04e243232436b2911
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377448"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Oktatóanyag: adat-előállító létrehozása a Visual Studióval
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg [az adat-előállító Azure Data Factoryvel való létrehozását ismertető rövid útmutatót](../quickstart-create-data-factory-dot-net.md).

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure Data Factory a Visual Studio használatával. Létrehozhat egy Visual Studio projektet a Data Factory projektsablon használatával, definiálhatja a Data Factory entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatot) JSON formátumban, majd közzéteheti/üzembe helyezheti az entitásokat a felhőben. 

A jelen oktatóanyagban szereplő folyamat egyetlen tevékenységet tartalmaz: ez a **HDInsight Hive-tevékenység**. A tevékenység egy hive-szkriptet futtat egy Azure HDInsight fürtön, amely a bemeneti adatokat átalakítja a kimeneti adatok előállításához. A folyamat úgy van ütemezve, hogy havonta egyszer fusson a megadott kezdő és befejező időpontok közt. 

> [!NOTE]
> Ez az oktatóanyag nem tartalmazza az adatok Azure Data Factory használatával történő másolásának leírását. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További tudnivalókért lásd: [Ütemezés és végrehajtás a Data Factoryban](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Útmutató: Data Factory-entitások létrehozása és közzététele
Az útmutató során a következő lépéseket fogja elvégezni:

1. Létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService1** és **HDInsightOnDemandLinkedService1**. 
   
    Ebben az oktatóanyagban a hive-tevékenység bemeneti és kimeneti adatai ugyanabban az Azure Blob Storage-tárolóban vannak. A meglévő bemeneti adatokat egy igény szerinti HDInsight-fürt használatával dolgozhatja fel a kimeneti adatok előállításához. Az igény szerinti HDInsight-fürtöt az Azure Data Factory automatikusan hozza létre futásidőben, amikor a bemeneti adatok készen állnak a feldolgozásra. Az adattárakat vagy számítási erőforrásokat társítani kell a data factoryhoz, hogy a Data Factory szolgáltatás futásidőben képes legyen csatlakozni hozzájuk. Ezért az AzureStorageLinkedService1 használatával társíthatja az Azure Storage-fiókját a data factoryhoz, a HDInsightOnDemandLinkedService1 használatával pedig egy igény szerinti HDInsight-fürtöt társíthat. A közzétételkor adja meg a létrehozni kívánt vagy egy meglévő data factory nevét.  
2. Létrehoz két adatkészletet: az **InputDataset** és az **OutputDataset** adatkészletet, amelyek az Azure Blob Storage-tárolóban tárolt bemeneti/kimeneti adatokat képviselik. 
   
    Ezek az adatkészlet-definíciók az előző lépésben létrehozott Azure Storage társított szolgáltatásra vonatkoznak. Az InputDataset adatkészlethez megadja a blobtárolót (adfgetstarted) és a bemeneti adatokkal rendelkező blobot tároló mappát (inputdata). Az OutputDataset adatkészlethez megadja a blobtárolót (adfgetstarted) és a kimeneti adatokat tároló mappát (partitioneddata). Egyéb tulajdonságokat is megad, például a szerkezetet, rendelkezésre állást és a szabályzatot.
3. Hozzon létre egy folyamatot **MyFirstPipeline** néven. 
  
    A jelen útmutatóban a folyamat egyetlen tevékenységet tartalmaz: ez a **HDInsight Hive-tevékenység**. A tevékenység a bemeneti adatokat átalakítja a kimeneti adatok előállításához, amihez egy hive-szkriptet futtat egy igény szerinti HDInsight-fürtön. A hive-tevékenységgel kapcsolatos további információk: [Hive-tevékenység](data-factory-hive-activity.md) 
4. Létrehoz egy **DataFactoryUsingVS** nevű data factoryt. Üzembe helyezi a data factoryt és a Data Factory-entitásokat (társított szolgáltatások, táblák és a folyamat).
5. A közzététel után az Azure Portal paneljei és a Figyelés + felügyelet alkalmazás használatával figyelheti a folyamatot. 
  
### <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Olvassa el [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket. Választhatja az **Áttekintés és előfeltételek** lehetőséget is a felül lévő legördülő listában, ha a cikkre szeretne váltani. Miután végzett az előfeltételekkel, váltson vissza erre a cikkre a **Visual Studio** lehetőség kiválasztásával a legördülő listában.
2. Data Factory-példány létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.  
3. A számítógépre a következőket kell telepíteni:
   * Visual Studio 2013 vagy Visual Studio 2015
   * Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
   * Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). A beépülő modult a következő lépések végrehajtásával is frissítheti: a menüben kattintson az **eszközök**  ->  **bővítmények és frissítések**  ->  **online**  ->  **Visual Studio Gallery**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update** lehetőségre.

Most használjuk a Visual studiót egy Azure Data Factory létrehozásához.

### <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása
1. Indítsa el a **Visual Studio 2013-at** vagy a **Visual Studio 2015-öt**. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre. Meg kell jelennie a **New project** (Új projekt) párbeszédpanelnek.  
2. A **New project** (Új projekt) párbeszédpanelen jelölje ki a **DataFactory** sablont, és kattintson az **Empty Data Factory Project** (Üres Data Factory-projekt) elemre.   

    ![A New project (Új projekt) párbeszédpanel](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Adja meg a projekt **nevét**, a **helyet**, valamint a **megoldás** nevét, és kattintson az **OK** gombra.

    ![Megoldáskezelő](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben létrehozza a következő két társított szolgáltatást: **Azure Storage** és **igény szerinti HDInsight**. 

Az Azure Storage társított szolgáltatás a kapcsolódási adatok megadásával társítja Azure Storage-fiókját a data factoryhoz. A Data Factory szolgáltatás a társított szolgáltatás beállításából származó kapcsolati sztringet használja az Azure-tárolóhoz való csatlakozáshoz futásidőben. Ez a tároló tárolja a folyamat bemeneti és kimeneti adatait, valamint a hive-tevékenység által használt hive-szkriptet. 

Az igény szerinti HDInsight társított szolgáltatással a HDInsight-fürt automatikusan jön létre futásidőben, amikor a bemeneti adatok készen állnak a feldolgozásra. A fürtöt a rendszer törli a feldolgozás befejezését követően, miután egy adott ideig tétlen volt. 

> [!NOTE]
> A data factory létrehozásához meg kell adni annak nevét és beállításait a Data Factory megoldás közzétételekor.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) elemre.      
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Storage Linked Service** (Azure Storage társított szolgáltatás) elemet, és kattintson az **Add** (Hozzáadás) parancsra.
    ![Képernyőkép, amely kiemeli az Azure Storage társított szolgáltatást a listában.](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Cserélje le az `<accountname>` és az `<accountkey>` kifejezést az Azure Storage-tárfiókja nevére, illetve kulcsára. A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.
    ![Azure Storage társított szolgáltatás](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Mentse az **AzureStorageLinkedService1.json** fájlt.

#### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás létrehozása
1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) elemre.
2. Válassza a **HDInsight On Demand Linked Service** (HDInsight igény szerinti társított szolgáltatás) lehetőséget, és kattintson az **Add** (Hozzáadás) parancsra.
3. Cserélje le a **JSON-t** a következő JSON-ra:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    Tulajdonság | Leírás
    -------- | ----------- 
    ClusterSize | Megadja a HDInsight Hadoop-fürt méretét.
    TimeToLive | Megadja, hogy a HDInsight-fürt mennyi ideig lehet tétlen, mielőtt törölné a rendszer.
    linkedServiceName | Megadja a HDInsight Hadoop-fürt által előállított naplók tárolására szolgáló tárfiókot. 

    > [!IMPORTANT]
    > A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-ban megadott blob-tárolóban (linkedServiceName). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer a szeletek feldolgozásakor mindig létrehoz egy HDInsight-fürtöt, kivéve, ha van meglévő élő fürt (timeToLive). A fürt automatikusan törlődik a feldolgozás megtörténtekor.
    > 
    > Ahogy a rendszer egyre több szeletet dolgoz fel, egyre több tároló jelenik meg az Azure Blob Storage-fiókban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Az Azure Blob Storage-tárolóban lévő tárolók törléséhez használja a [Microsoft Azure Storage Explorer](https://storageexplorer.com/) eszközöket.

    A JSON-tulajdonságokkal kapcsolatos további információkért lásd a [Társított szolgáltatások számítása](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) című cikket. 
4. Mentse a **HDInsightOnDemandLinkedService1.json** fájlt.

### <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek a Hive-feldolgozás bemeneti és kimeneti adatait képviselik. Ezek az adatkészletek az oktatóanyag során korábban létrehozott **AzureStorageLinkedService1** szolgáltatásra hivatkoznak. A társított szolgáltatás egy Azure Storage-fiókra mutat, az adatkészletek pedig meghatározzák a bemeneti és kimeneti adatokat tartalmazó tárban lévő tárolót, mappát és fájlnevet.   

#### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) lehetőségre.
2. Válassza ki a listából az **Azure Blob** lehetőséget, módosítsa a fájl nevét **InputDataSet.json** névre, és kattintson az **Add** (Hozzáadás) parancsra.
3. A szerkesztőben cserélje le a **JSON-t** a következő JSON-kódrészletre:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Ez a JSON-kódrészlet definiál egy **AzureBlobInput** nevű adatkészletet, amely a folyamat hive-tevékenységének bemeneti adatait képviseli. Ön adja meg, hogy a bemeneti adatok az `adfgetstarted` nevű blobtárolóban és az `inputdata` nevű mappában találhatóak.

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    Tulajdonság | Leírás |
    -------- | ----------- |
    típus |A Type tulajdonság a **AzureBlob** értékre van állítva, mert az Azure Blob Storageban található adat.
    linkedServiceName | A korábban létrehozott AzureStorageLinkedService1 szolgáltatásra hivatkozik.
    fileName |Ez a tulajdonság nem kötelező. Ha kihagyja, az összes fájl ki lesz választva a folderPath útvonalról. Ebben az esetben csak az input.log fájl lesz feldolgozva.
    típus | A naplófájlok szövegformátumúak, ezért a TextFormat típust használjuk. |
    columnDelimiter | A naplófájlokban vesszővel (`,`) vannak elválasztva az oszlopok
    frequency/interval | A frequency (gyakoriság) beállítása Month (Hónap), az interval (időköz) beállítása pedig 1, ami azt jelenti, hogy a bemeneti szeletek havonta érhetők el.
    external | Ez a tulajdonság true (igaz) értékre van állítva, ha a tevékenység bemeneti adatait nem a folyamat hozta létre. Ez a tulajdonság csak a bemeneti adatkészleteken van meghatározva. Az első tevékenység bemeneti adatkészlete esetében mindig állítsa igaz értékre.
4. Mentse az **InputDataset.json** fájlt.

#### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
Most a kimeneti adatkészletet hozza létre, amely az Azure Blob Storage-tárolóban tárolt kimeneti adatokat jelöli.

1. A **megoldáskezelő** kattintson a jobb gombbal a **táblák** elemre, mutasson a **Hozzáadás** elemre, majd kattintson az **új elem** lehetőségre.
2. Válassza ki a listából az **Azure Blob** lehetőséget, módosítsa a fájl nevét **OutputDataset.json** névre, és kattintson az **Add** (Hozzáadás) parancsra.
3. A szerkesztőben cserélje le a **JSON-t** a következő JSON-ra:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }
    ```
    Ez a JSON-kódrészlet definiál egy **AzureBlobOutput** nevű adatkészletet, amely a folyamat hive-tevékenysége által előállított kimeneti adatokat képviseli. Ön adja meg, hogy a hive-tevékenység által előállított kimeneti adatok az `adfgetstarted` nevű blobtárolóba és a `partitioneddata` nevű mappába kerülnek. 
    
    Az **availability** (rendelkezésre állás) szakasz meghatározza, hogy a kimeneti adatkészlet előállítása havonta történik. A kimeneti adatkészlet határozza meg a folyamat ütemezését. A folyamat havonta egyszer fut le a kezdő és befejező időpontjai közt. 

    A tulajdonságok leírását a **Bemeneti adatkészlet létrehozása** című szakaszban tekintheti meg. Külső adatkészlet esetén nem kell beállítani az external (külső) tulajdonságot, mert az adatkészletet a folyamat állítja elő.
4. Mentse az **OutputDataset.json** fájlt.

### <a name="create-pipeline"></a>Folyamat létrehozása
Eddig létrehozta az Azure Storage társított szolgáltatást, valamint a bemeneti és kimeneti adatkészleteket. Most létrehozhat egy folyamatot egy **HDInsightHive**-tevékenységgel. A tevékenység **bemenetének** beállítása **AzureBlobInput**, a **kimeneté** pedig **AzureBlobOutput**. A bemeneti adatkészlet szelete havonta érhető el (frequency: Month, interval: 1), és a kimeneti szelet előállítása is havonta történik. 

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Pipelines** (Folyamatok) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) lehetőségre.
2. Válassza ki a listából a **Hive Transformation Pipeline** (Hive átalakítási folyamat) lehetőséget, és kattintson az **Add** (Hozzáadás) parancsra.
3. Cserélje le a **JSON** -t a következő kódrészletre:

    > [!IMPORTANT]
    > Cserélje le a `<storageaccountname>` kifejezést a tárfiókja nevére.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Cserélje le a `<storageaccountname>` kifejezést a tárfiókja nevére.

    A JSON-kódrészlet meghatároz egy folyamatot, amely egyetlen tevékenységből áll (Hive-tevékenység). A tevékenység egy Hive-szkriptet futtat a bemeneti adatok feldolgozásához és a kimeneti adatok előállításához egy igény szerinti HDInsight-fürtön. A folyamat JSON-fájljának tevékenységek szakaszában csak egyetlen tevékenység látható a tömbben, **HDInsightHive** típusbeállítással. 

    A HDInsight Hive-tevékenység specifikus típustulajdonságaiban megadja, hogy az Azure Storage társított szolgáltatás tartalmazza a hive-szkriptfájlt, a szkriptfájl útvonalát és paramétereit. 

    A **partitionweblogs.hql** nevű Hive-szkriptfájl tárolása a (scriptLinkedService szolgáltatással megadott) Azure Storage-fiókban és az `adfgetstarted` tároló `script` mappájában történik.

    A `defines` szakasz meghatározza a futásidő beállításait, amelyek Hive konfigurációs értékekként (például `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`) lesznek átadva a Hive-parancsfájlnak.

    A folyamat **start** (kezdés) és **end** (befejezés) tulajdonságai a folyamat aktív időszakát határozzák meg. Az adatkészletet úgy állította be, hogy havonta legyen előállítva, így a folyamat csak egyetlen szeletet állít elő (mivel a hónap a kezdő és a befejező dátumban egyezik).

    A tevékenység JSON-fájljában meg van határozva, hogy a Hive-parancsfájl a **linkedServiceName** – **HDInsightOnDemandLinkedService** által meghatározott számítási szolgáltatáson fut.
4. Mentse a **HiveActivity1.json** fájlt.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>A partitionweblogs.hql és az input.log fájl hozzáadása függőségként
1. A **Solution Explorer** (Megoldáskezelő) ablakában kattintson a jobb gombbal a **Dependencies** (Függőségek) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson az **Existing Item** (Meglévő elem) lehetőségre.  
2. Lépjen a **C:\ADFGettingStarted** mappába, jelölje ki a **partitionweblogs.hql** és az **input.log** fájlt, majd kattintson az **Add** (Hozzáadás) parancsra. Ezt a két fájlt [Az oktatóanyag áttekintése](data-factory-build-your-first-pipeline.md) című cikk előfeltételeinek részeként hozta létre.

Amikor a következő lépésben közzéteszi a megoldást, a **partitionweblogs.hql** fájlt az `adfgetstarted` blob-tároló **script** mappájába tölti fel a rendszer.   

### <a name="publishdeploy-data-factory-entities"></a>Data Factory-entitások közzététele/üzembe helyezése
Ebben a lépésben a projektben lévő Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) teszi közzé az Azure Data Factory szolgáltatásban. A közzétételi folyamatban megadja a data factory nevét. 

1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra.
2. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
3. A következő párbeszédpanelnek kell megjelennie:

   ![Publish (Közzététel) párbeszédpanel](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. A **Data Factory konfigurálási** lapján hajtsa végre a következő lépéseket:

    ![Közzététel – Új data factory beállításai](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Válassza a **Create New Data Factory** (Új data factory létrehozása) lehetőséget.
   2. Adja meg a data factory egyedi **nevét**. Például: **DataFactoryUsingVS09152016**. A névnek globálisan egyedinek kell lennie.
   3. A **Subscription** (Előfizetés) mezőben válassza ki a megfelelő előfizetést. 
        > [!IMPORTANT]
        > Ha egy előfizetést sem lát, ellenőrizze, hogy olyan fiókkal jelentkezett-e be, amely rendszergazdája vagy társadminisztrátora az előfizetésnek.
   4. Válassza ki a data factoryhoz az **erőforráscsoportot**.
   5. Válassza ki a data factoryhoz a **régiót**.
   6. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz. (Ha a **Tovább** gomb le van tiltva, nyomja le a **TAB** billentyűt a Name (Név) mezőből való kilépéshez.)

      > [!IMPORTANT]
      > Ha a közzététel során a **Data factory name “DataFactoryUsingVS” is not available** (A „DataFactoryUsingVS” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a nevet (például: azÖnneveDataFactoryUsingVS). A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.   
1. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.

    ![Publish items (Elemek közzététele) oldal](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.

    ![Összefoglaló lap](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra.

Fontos tudnivalók:

- Ha a következő hibaüzenetet kapja: **Ez az előfizetés nincs regisztrálva a Microsoft. DataFactory névtér használatára**, hajtsa végre az alábbi műveletek egyikét, majd próbálkozzon újra a közzétételsel:
    - Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához.
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e.

        ```powershell
        Get-AzResourceProvider
        ```
    - Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.
- Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.
- Data Factory-példányok létrehozásához az Azure-előfizetés rendszergazdájának vagy társadminisztrátorának kell lennie.

### <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben a data factory Diagramnézete használatával figyeli a folyamatot. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Folyamat figyelése diagramnézetben
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és tegye a következőket:
   1. Kattintson a **További szolgáltatások**, majd az **Adat-előállítók** elemre.
       
        ![Data factoryk tallózása](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Válassza ki a data factory nevét a data factoryk listájából (például: **DataFactoryUsingVS09152016**).
   
       ![A data factory kiválasztása](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. A data factory kezdőlapján kattintson a **Diagram** lehetőségre.

    ![Diagram csempe](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. A diagramnézet áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.

    ![Diagramnézet](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. A folyamat összes tevékenységének megtekintéséhez kattintson a jobb gombbal a folyamatra a diagramban, majd kattintson a Feldolgozási sor megnyitása elemre.

    ![Folyamat megnyitása menü](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Győződjön meg arról, hogy a HDInsightHive tevékenység megjelenik a folyamatban.

    ![Folyamat megnyitása nézet](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Az előző nézethez való visszatéréshez az oldal tetején lévő navigációs menüben kattintson a **Data factory** elemre.
6. A **diagramnézetben** kattintson duplán az **AzureBlobInput** adatkészletre. Győződjön meg arról, hogy a szelet **Ready** (Kész) állapotú. Eltarthat néhány percig, amíg a szelet Ready (Kész) állapotúként jelenik meg. Ha ez azután sem történik meg, hogy vár néhány percet, ellenőrizze, hogy a megfelelő tárolóba (`adfgetstarted`) és mappába (`inputdata`) helyezte-e a bemeneti fájlt (input.log). Arról is győződjön meg, hogy a bemeneti adatkészlet **external** (külső) tulajdonságának beállítása **true** (igaz). 

   ![Kész állapotú bemeneti szelet](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Kattintson az **X** elemre az **AzureBlobInput** panel bezárásához.
8. A **diagram nézetben** kattintson duplán az adatkészlet **AzureBlobOutput**. Látni fogja, hogy a szelet feldolgozás alatt áll.

   ![A AzureBlobOutput adatkészletet kiemelő képernyőkép.](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. A feldolgozás befejeztével a szelet **Ready** (Kész) állapotúra vált.

   > [!IMPORTANT]
   > Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig (körülbelül 20 percig). Ezért várhatóan **körülbelül 30 percet** vesz igénybe a folyamat a szelet feldolgozásához.  
   
    ![Adathalmaz](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Ha a szelet **Kész** állapotú, a Blob Storage-tároló `adfgetstarted` tárolójában tekintse meg a `partitioneddata` mappát a kimeneti adatokért.  

    ![kimeneti adatok](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. A szelet részleteinek az **Adatszelet** panelen való megtekintéséhez kattintson a szeletre.

    ![Adatszelet részletei](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Kattintson egy tevékenységfuttatásra az **Activity runs list** (Tevékenységfuttatások listája) területen a tevékenységfuttatás (ebben az esetben Hive-tevékenység) részleteinek az **Activity run details** (Tevékenységfuttatás részletei) ablakban való megjelenítéséhez. 
  
    ![Tevékenységfuttatás részletei](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    A naplófájlokban láthatja a végrehajtott Hive-lekérdezést és az állapotadatokat. A naplók hasznosak bármilyen hiba esetén a hibaelhárításban.  

Az ebben az oktatóanyagban létrehozott folyamat és adatkészletek figyelésére vonatkozó utasításokért tekintse Azure Portal meg az [adatkészletek és a folyamat figyelése](data-factory-monitor-manage-pipelines.md) című részt.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Folyamat figyelése a Monitor & Manage alkalmazással
A folyamatok figyeléséhez a Monitor & Manage alkalmazást is használhatja. Az alkalmazás használatával kapcsolatos részletes információkért olvassa el a [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring and Management használatával) című cikket.

1. Kattintson a Monitor & Manage csempére.

    ![Monitor & Manage csempe](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Meg kell jelennie a Monitor & Manage alkalmazásnak. Módosítsa a **kezdési idő** és a **befejezési idő** értékét, hogy megfeleljen a folyamat kezdési (04-01-2016 12:00 AM) és befejezési (04-02-2016 12:00 AM) idejének, és kattintson az **Apply** (Alkalmaz) gombra.

    ![Monitor & Manage alkalmazás](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Ha látni szeretné egy tevékenységablak részleteit, jelölje ki az **Activity Windows** (Tevékenységablakok) listában.
    ![Tevékenységablakok részletei](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> A szelet sikeres feldolgozásakor a rendszer törli a bemeneti fájlt. Ezért ha újra szeretné futtatni a szeletet, vagy újra el szeretné végezni az oktatóanyagot, töltse fel a bemeneti fájlt (input.log) az `adfgetstarted` tároló `inputdata` mappájába.

### <a name="additional-notes"></a>További megjegyzések
- A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Ilyen például a másolási tevékenység, amely adatokat másol a forrásadattárból a céladattárba, és a HDInsight Hive tevékenység, amely egy Hive-szkriptet futtat a bemeneti adatok átalakításához. A másolási tevékenység által támogatott forrásokért és fogadókért tekintse meg a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) című részt. A Data Factory által támogatott számítási szolgáltatások listájáért tekintse meg a [számítási társított szolgáltatások](data-factory-compute-linked-services.md) című részt.
- A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat csatolnak egy Azure Data Factoryhoz. A másolási tevékenység által támogatott forrásokért és fogadókért tekintse meg a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) című részt. A Data Factory által támogatott számítási szolgáltatások és a futtatható [átalakítási tevékenységek](data-factory-compute-linked-services.md) listáját a [számítási társított szolgáltatásokat](data-factory-data-transformation-activities.md) ismertető részben találja.
- Az Azure Storage társított szolgáltatás definíciójában használt JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése az Azure Blobból vagy az Azure Blobba](data-factory-azure-blob-connector.md#azure-storage-linked-service).
- Igény szerinti HDInsight-fürt helyett saját HDInsight-fürtöt is használhat. További információ: [Compute Linked Services](data-factory-compute-linked-services.md) (Számítási társított szolgáltatás).
-  A Data Factory létrehoz egy **Linux-alapú** HDInsight-fürtöt az előző JSON-fájllal. További információkért lásd: [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).
- A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-ban megadott blob-tárolóban (linkedServiceName). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer a szeletek feldolgozásakor mindig létrehoz egy HDInsight-fürtöt, kivéve, ha van meglévő élő fürt (timeToLive). A fürt automatikusan törlődik a feldolgozás megtörténtekor.
    
    Ahogy a rendszer egyre több szeletet dolgoz fel, egyre több tároló jelenik meg az Azure Blob Storage-fiókban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Az Azure Blob Storage-tárolóban lévő tárolók törléséhez használja a [Microsoft Azure Storage Explorer](https://storageexplorer.com/) eszközöket.
- Jelenleg a kimeneti adatkészlet vezérli az ütemezést, ezért kimeneti adatkészletet akkor is létre kell hoznia, ha a tevékenység nem állít elő semmilyen kimenetet. Ha a tevékenység nem fogad semmilyen bemenetet, kihagyhatja a bemeneti adatkészlet létrehozását. 
- Ez az oktatóanyag nem tartalmazza az adatok Azure Data Factory használatával történő másolásának leírását. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-server-explorer-to-view-data-factories"></a>A Kiszolgókezelő használata data factoryk megtekintéséhez
1. A **Visual Studio** menüjében kattintson a **View** (Megtekintés), majd a **Server Explorer** (Kiszolgálókezelő) elemre.
2. A Server Explorer (Kiszolgálókezelő) ablakban bontsa ki az **Azure**, majd a **Data Factory** elemet. Ha megjelenik a **Sign in to Visual Studio** (Jelentkezzen be a Visual Studióba) üzenet, adja meg az Azure-előfizetéséhez társított **fiókot**, és kattintson a **Continue** (Folytatás) elemre. Adja meg a **jelszót**, és kattintson a **Sign in** (Bejelentkezés) elemre. A Visual Studio megpróbálja lekérni az információkat az előfizetésében elérhető összes Azure data factoryről. Ennek a műveletnek az állapota a **Data Factory Task List** (Data Factory-feladatlista) ablakban jelenik meg.

    ![Server Explorer (Kiszolgálókezelő)](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Ha egy meglévő data factory alapján szeretne létrehozni Visual Studio-projektet, kattintson a jobb gombbal egy dara factoryre, és válassza az **Export Data Factory to New Project** (Data factory exportálása új projektbe) lehetőséget.

    ![Export data factory (Data factory exportálása) menüelem](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studióhoz készült Data Factory-eszközök frissítése
A Visual Studióhoz készült Data Factory-eszközök frissítéséhez tegye a következőket:

1. Kattintson a menüben a **Tools** (Eszközök) elemre, és válassza az **Extensions and Updates** (Bővítmények és frissítések) lehetőséget.
2. A bal oldali panelen válassza az **Updates** (Frissítések) elemet, majd válassza a **Visual Studio Gallery** (Visual Studio-gyűjtemény) lehetőséget.
3. Válassza az **Azure Data Factory tools for Visual Studio** lehetőséget, és kattintson az **Update** (Frissítés) elemre. Ha nem látja ezt a bejegyzést, már rendelkezik az eszközök legújabb verziójával.

## <a name="use-configuration-files"></a>Konfigurációs fájlok használata
A Visual Studióban konfigurációs fájlokat használhat, ha az egyes környezetekhez eltérően szeretné konfigurálni a társított szolgáltatások/táblák/folyamatok tulajdonságait.

Használja például az alábbi JSON-definíciót egy Azure Storage társított szolgáltatáshoz. A **connectionString** tulajdonság accountname és accountkey értékeit annak a környezetnek (fejlesztői/teszt/éles) megfelelően adja meg, amelyikben üzembe helyezi a Data Factory-entitásokat. Az ilyen működést úgy érheti el, hogy minden környezethez külön konfigurációs fájlt használ.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Konfigurációs fájl hozzáadása
Adjon hozzá konfigurációs fájlt az egyes környezetekhez a következő lépések végrehajtásával:   

1. A Visual Studio-megoldásban kattintson a jobb gombbal a Data Factory-projektre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.
2. A telepített sablonok bal oldali listájában válassza a **Config** elemet, jelölje ki a **Configuration File** (Konfigurációs fájl) lehetőséget, adja meg a konfigurációs fájl **nevét**, majd kattintson az **Add** (Hozzáadás) elemre.

    ![Konfigurációs fájl hozzáadása](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Adja meg a konfigurációs paramétereket és az értéküket az alábbi formátumban:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Ez a példa konfigurálja egy Azure Storage társított szolgáltatás és egy Azure SQL társított szolgáltatás connectionString tulajdonságát. Figyelje meg, hogy a névmegadás szintaxisa a [JsonPath](https://goessner.net/articles/JsonPath/).   

    Ha a JSON-fájlban szerepel egy olyan tulajdonság, amely értékek tömbjével rendelkezik a következő kódban látható módon:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Konfigurálja a tulajdonságokat a következő konfigurációs fájlban látható módon (használjon nulláról induló indexelést):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Tulajdonságnevek szóközökkel
Ha egy tulajdonságnév szóközöket tartalmaz, használjon szögletes zárójeleket az alábbi példában látható módon (Adatbázis-kiszolgáló neve):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Megoldás üzembe helyezése konfiguráció használatával
Amikor Azure Data Factory-entitásokat tesz közzé a Visual Studióban, megadhatja azt a konfigurációt, amelyet a közzétételi művelethez szeretne használni.

Entitások közzététele Azure Data Factory-projektben konfigurációs fájl használatával:   

1. Kattintson a jobb gombbal a Data Factory projektre, majd kattintson a **Publish** (Közzététel) elemre a **Publish Items** (Elemek közzététele) párbeszédpanel megjelenítéséhez.
2. Válasszon ki egy meglévő data factoryt, vagy adja meg az értékeket egy új data factory létrehozásához a **Configure data factory** (Data factory konfigurálása) oldalon, és kattintson a **Next** (Tovább) gombra.   
3. A **Publish Items** (Elemek közzététele) oldalon megjelenik egy legördülő lista a **Select Deployment Config** (Üzembehelyezési konfiguráció kiválasztása) mező elérhető beállításaival.

    ![Konfigurációs fájl kiválasztása](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Válassza ki a használni kívánt **konfigurációs fájlt**, és kattintson a **Next** (Tovább) gombra.
5. Győződjön meg arról, hogy a JSON-fájl neve megjelenik a **Summary** (Összegzés) oldalon, és kattintson a **Next** (Tovább) gombra.
6. Miután befejeződött az üzembehelyezési művelet, kattintson a **Finish** (Befejezés) gombra.

Az üzembe helyezéskor a rendszer a konfigurációs fájlban szereplő értékek alapján beállítja a JSON-fájlokban szereplő tulajdonságok értékeit, mielőtt üzembe helyezné az entitásokat az Azure Data Factoryban.   

## <a name="use-azure-key-vault"></a>Az Azure Key Vault használata
A bizalmas adatok, például kapcsolati sztringek véglegesítése a kódtárban ellenjavallt, és gyakran a biztonsági szabályzatba ütközik. A bizalmas adatok az Azure Key Vaultban való tárolásának és a Data Factory-entitások közzétételekor való használatának elsajátításához lásd az [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) mintát a Githubon. A Visual Studio Secure Publish (Biztonságos közzététel) bővítménye lehetővé teszi a titkos kulcsok a Key Vaultban való tárolását, és csak hivatkozások meghatározását azokra a társított szolgáltatásokban/telepítési konfigurációkban. A hivatkozások feloldására az Azure Data Factory-entitások Azure-ban való közzétételekor kerül sor. Ezen fájlok ekkor a titkos kulcsok közzététele nélkül véglegesíthetők a forrástárházban.

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban létrehozott egy Azure Data Factory az adatfeldolgozáshoz a HDInsight Hadoop-fürtön futó kaptár-parancsfájl futtatásával. Az Azure Portal Data Factory Editor eszközét használta a következő lépések végrehajtásához:  

1. Létrehozott egy Azure **data factoryt**.
2. Létrehozott két **társított szolgáltatást**:
   1. Az **Azure Storage** társított szolgáltatást, amely a bemeneti és kimeneti adatokat tároló Azure blob-tárolót társítja a data factoryval.
   2. Az **Azure HDInsight** igény szerinti társított szolgáltatást, amely egy igény szerinti HDInsight Hadoop-fürtöt társít a data factoryval. Az Azure Data Factory létrehoz egy HDInsight Hadoop-fürtöt, amely igény szerint dolgozza fel a bemeneti adatokat és állítja elő a kimeneti adatokat.
3. Létrehozott két **adatkészletet**, amelyek leírják a bemeneti és kimeneti adatokat az adatcsatorna HDInsight Hive-tevékenysége számára.
4. Létrehozott egy **folyamatot** egy **HDInsight Hive**-tevékenységgel.  

## <a name="next-steps"></a>Következő lépések
Az oktatóanyag során létrehozott egy folyamatot egy adatátalakítási tevékenységgel (HDInsight-tevékenység), amely Hive-parancsfájlt futtat egy igény szerinti HDInsight-fürtön. Az adatok Azure-Blobból az Azure SQLba való másolásának módját lásd [: oktatóanyag: adatok másolása Azure-blobból az Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)szolgáltatásba.

Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket. 


## <a name="see-also"></a>Lásd még:

| Témakör | Description |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Ebből a cikkből megismerheti az Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) |Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban. |
| [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) |Ez a cikk felsorolja az Azure Data Factory által támogatott adatátalakítási tevékenységeket (mint például a jelen oktatóanyagban használt HDInsight Hive-átalakítás). |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) |Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) |Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. |
