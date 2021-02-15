---
title: 'Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Visual Studio használatával '
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre másolási tevékenységgel rendelkező Azure Data Factory-folyamatot a Visual Studio használatával.
author: linda33wj
ms.service: data-factory
ms.custom: vs-azure
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f6ff31c3c0d37078f8dfbd934593adb4b6a6ef4b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377108"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Visual Studio használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy adatfeldolgozót a Microsoft Visual Studio használatával egy olyan folyamattal, amely egy Azure Blob Storage-ból másol egy Azure SQL Database. Ha még csak ismerkedik az Azure Data Factory szolgáltatással, olvassa el a [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md) című cikket az oktatóanyag elvégzése előtt.   

Az oktatóanyag segítségével egyetlen tevékenységgel (másolási tevékenységgel) rendelkező folyamatot hozhat létre. A másolási tevékenység adatokat másol a forrásadattárból egy támogatott fogadó adattárba. A forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md).

Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További információ az [egy folyamaton belüli több tevékenységről](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) szóló témakörben található.

> [!NOTE] 
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. Az adatok Azure Data Factory használatával történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Olvassa el [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.       
2. Data Factory-példány létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
3. A számítógépre a következőket kell telepíteni: 
   * Visual Studio 2013 vagy Visual Studio 2015
   * Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
   * Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). A beépülő modult a következő lépések végrehajtásával is frissítheti: a menüben kattintson az **eszközök**  ->  **bővítmények és frissítések**  ->  **online**  ->  **Visual Studio Gallery**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update** lehetőségre.

## <a name="steps"></a>Lépések

Az oktatóanyag során a következő lépéseket fogja elvégezni:

1. Hozzon létre **társított szolgáltatásokat** az adat-előállítóban. Ebben a lépésben a következő két típusú társított szolgáltatást hozza létre: Azure Storage és Azure SQL Database. 

    Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Az [Előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)részeként létrehozott egy tárolót, és feltöltötte az adattárat ebbe a Storage-fiókba.   

    A AzureSqlLinkedService a Azure SQL Database az adatelőállítóra mutató hivatkozásokat. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az SQL-táblát az adatbázisban.     

2. Hozza létre a bemeneti és kimeneti **adatkészleteket** az adat-előállítóban.  

    Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

    Hasonlóképpen, a Azure SQL Database társított szolgáltatás határozza meg azt a kapcsolati karakterláncot, amelyet a Data Factory szolgáltatás futás közben használ a Azure SQL Databasehoz való kapcsolódáshoz. Az SQL-tábla kimeneti adatkészlete határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja.

3. Hozzon létre egy **folyamatot** az adat-előállítóban. Ebben a lépésben létre fog hozni egy másolási tevékenységgel rendelkező folyamatot.   

    A másolási tevékenység az Azure Blob Storage-ban lévő blobból másol át egy Azure SQL Databaseba egy táblába. A folyamat másolási tevékenységével adatokat másolhat bármely támogatott forrásból bármely támogatott célhelyre. A támogatott adattárak listájáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

4. Hozzon létre egy Azure **adat-előállítót** Data Factory-entitások (összekapcsolt szolgáltatások adatkészletek/táblák és folyamatok) telepítésekor. 

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Indítsa el a **Visual Studio 2015-öt**. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre. Meg kell jelennie a **New project** (Új projekt) párbeszédpanelnek.  

2. A **New project** (Új projekt) párbeszédpanelen jelölje ki a **DataFactory** sablont, és kattintson az **Empty Data Factory Project** (Üres Data Factory-projekt) elemre.  

    ![A New project (Új projekt) párbeszédpanel](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Adja meg a projekt nevét, a megoldás helyét és nevét, majd kattintson az **OK** gombra.

    ![Megoldáskezelő](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben az oktatóanyagban nem használunk számítási szolgáltatásokat (például Azure HDInsight vagy Azure Data Lake Analytics). Csak kétféle típusú adattárat használunk: Azure Storage (forrás) és Azure SQL Database (cél). 

Ezért a következő két típusú társított szolgáltatást hozza létre: AzureStorage és AzureSQLDatabase.  

Az Azure Storage társított szolgáltatás az Azure tárfiókot társítja az adat-előállítóval. Ebben a tárfiókban hozta létre a tárolót, és ebbe töltötte fel adatokat az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként.   

Az Azure SQL társított szolgáltatás az adatgyárhoz Azure SQL Database hivatkozásokat tartalmaz. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az emp táblát az adatbázisban.

A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. A másolási tevékenység által támogatott forrásokért és fogadókért tekintse meg a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) című részt. A Data Factory által támogatott számítási szolgáltatások listájáért tekintse meg a [számítási társított szolgáltatások](data-factory-compute-linked-services.md) című részt. Az oktatóanyag során ne használjon számítási szolgáltatásokat. 

### <a name="create-the-azure-storage-linked-service"></a>Az Azure Storage társított szolgáltatás létrehozása

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) elemre.      

2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Storage Linked Service** (Azure Storage társított szolgáltatás) elemet, és kattintson az **Add** (Hozzáadás) parancsra. 

    ![Új társított szolgáltatás](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)

3. Cserélje le az `<accountname>` és az `<accountkey>`* kifejezést az Azure Storage-tárfiókja nevére, illetve kulcsára. 

    ![Azure Storage társított szolgáltatás](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Mentse az **AzureStorageLinkedService1.json** fájlt.

    További információ a társított szolgáltatás definíciójában található JSON-tulajdonságokról: [Azure Blob Storage-összekötő](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Az Azure SQL társított szolgáltatás létrehozása

1. A **Solution Explorerben** (Megoldáskezelőben) ismét kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) csomópontra, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre. 
2. Ezúttal válassza a **Azure SQL Linked Service** (Azure SQL társított szolgáltatás) lehetőséget, és kattintson az **Add** (Hozzáadás) elemre. 
3. A **fájlAzureSqlLinkedService1.js** cserélje le a, a, a `<servername>` `<databasename>` `<username@servername>` és `<password>` a nevet a kiszolgáló, az adatbázis, a felhasználói fiók és a jelszó nevére.    
4. Mentse az **AzureSqlLinkedService1.json** fájlt. 

    További információ a JSON-tulajdonságokról: [Azure SQL Database-összekötő](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Adatkészletek létrehozása

Az előző lépésben társított szolgáltatásokat hozott létre az Azure Storage-fiók összekapcsolásához és a Azure SQL Databaseához. Ebben a lépésben két adatkészletet határoz meg – InputDataset és OutputDataset néven –, amelyek az AzureStorageLinkedService1 és az AzureSqlLinkedService1 szolgáltatás által hivatkozott bemeneti és kimeneti adatokat jelölik.

Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete (InputDataset) pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

Hasonlóképpen, a Azure SQL Database társított szolgáltatás határozza meg azt a kapcsolati karakterláncot, amelyet a Data Factory szolgáltatás futás közben használ a Azure SQL Databasehoz való kapcsolódáshoz. Az SQL-tábla kimeneti adatkészlete (OututDataset) határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja. 

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása

Ebben a lépésben hozza létre az InputDataset nevű adatkészletet, amely az AzureStorageLinkedService1 társított szolgáltatás által hivatkozott Azure Storage blobtárolójának (adftutorial) gyökérmappájában található blobfájlra mutat (emp.txt). Ha nem ad meg értéket a fájlnévnek (vagy kihagyja azt), a rendszer a bemeneti mappában található összes blob adatát a célhelyre másolja. Ebben az oktatóanyagban a fileName értékét adja meg. 

Az „adatkészletek” helyett itt inkább a „táblák” kifejezés használandó. A tábla egy téglalap alakú adatkészlet, és jelenleg ez az egyetlen támogatott adatkészlettípus. 

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) elemre.
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Blob** elemet, és kattintson az **Add** (Hozzáadás) elemre.   
3. Cserélje a JSON-szöveget az alábbi szövegre, és mentse az **AzureBlobLocation1.json** fájlt. 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
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
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | típus | A Type tulajdonság értéke **AzureBlob** , mert az anAzure blob Storage-ban található adat. |
    | linkedServiceName | A korábban youcreated **AzureStorageLinkedService** hivatkozik. |
    | folderPath | Megadja a blob- **tárolót** és a **mappa** thatcontains a bemeneti blobokat. Ebben az oktatóanyagban a adftutorial a andfolder blob tárolója. | 
    | fileName | Ez a tulajdonság nem kötelező. Ha kihagyja ezt a tulajdonságot, a rendszer a folderPath összes filesfrom kiválasztja. Ebben az oktatóanyagban **emp.txt** van megadva a fájlnévhez, így csak az adott fájl lesz feldolgozva feldolgozásra. |
    | formátum -> típus |A bemeneti fájl szöveges formátumú, ezért a **Szövegformátum** használjuk. |
    | columnDelimiter | A bemeneti fájlban lévő oszlopokat a **commacharacter ( `,` )** tagolja. |
    | frequency/interval | A gyakoriság értéke **óra** és intervallum setto **1**, ami azt jelenti, hogy a bemeneti szeletek **óránként** elérhetők. A otherwords-ben a Data Factory szolgáltatás óránként keresi a bemeneti adatokat a blob-tároló (**adftutorial**) megadott gyökérmappa. A thedata a folyamat kezdő és befejező időpontjain belül, nem pedig a thesetimes előtt és után keresi.  |
    | external | Ez a tulajdonság TRUE ( **igaz** ) értékre van beállítva, ha az adatbythis-folyamat nem jön létre. Az oktatóanyagban szereplő bemeneti adatok a emp.txt fájlban szerepelnek, amelyet ez a folyamat nem hozott létre, ezért a tulajdonságot igaz értékre kell állítani. |

    Ezekről a JSON-tulajdonságokról további tudnivalók az [Azure Blob-összekötőről](data-factory-azure-blob-connector.md#dataset-properties) szóló cikkben olvashatók.   

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása

Ebben a lépésben egy kimeneti adatkészletet hoz létre **OutputDataset** néven. Ez az adatkészlet a **AzureSqlLinkedService1** által képviselt Azure SQL Database egy SQL-táblára mutat. 

1. A **Solution Explorerben** (Megoldáskezelőben) ismét kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure SQL** elemet, és kattintson az **Add** (Hozzáadás) elemre. 
3. Cserélje a JSON-szöveget az alábbi JSON-szövegre, és mentse az **AzureSqlTableLocation1.json** fájlt.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
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
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
        }
    }
    ```

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | típus | A Type tulajdonság a **tulajdonsága azuresqltable** értékre van állítva, mert az adatmásolása egy Azure SQL Database egy táblába történik. |
    | linkedServiceName | A korábban létrehozott **AzureSqlLinkedService** szolgáltatásra hivatkozik. |
    | tableName | Azt a **táblát** határozza meg, amelybe a rendszer az adatokat másolja. | 
    | frequency/interval | A frequency (gyakoriság) értéke **Hour** (Óra), az interval (időköz) értéke pedig **1**, azaz a rendszer a kimeneti szeleteket **óránként** állítja elő a folyamat kezdő és befejező időpontja közti időszakban (és nem azon kívül).  |

    Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.

    További információ ezekről a JSON-tulajdonságokról: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#dataset-properties).

## <a name="create-pipeline"></a>Folyamat létrehozása

Ebben a lépésben létrehoz egy **másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **InputDataset**, kimenetként pedig az **OutputDataset** adatkészletet használja.

Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Az oktatóanyagban a kimeneti adatkészletet úgy konfiguráljuk, hogy a szeletek létrehozása óránként történjen meg. A folyamat kezdő és befejező időpontja között egy nap, azaz 24 óra telik el. Ezért a folyamat a kimeneti adatkészletből 24 szeletet hoz létre. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a **Pipelines** (Folyamatok) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.  
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza a **Copy Data Pipeline** (Adatok másolása folyamat) elemet, és kattintson az **Add** (Hozzáadás) parancsra. 
3. Cserélje le a JSON-t a következő JSON- **ra** , és mentse aCopyActivity1.jsfájlt.

   ```json
  {
   "name": "ADFTutorialPipeline",
   "properties": {
     "description": "Copy data from a blob to Azure SQL table",
     "activities": [
       {
         "name": "CopyFromBlobToSQL",
         "type": "Copy",
         "inputs": [
           {
             "name": "InputDataset"
           }
         ],
         "outputs": [
           {
             "name": "OutputDataset"
           }
         ],
         "typeProperties": {
           "source": {
             "type": "BlobSource"
           },
           "sink": {
             "type": "SqlSink",
             "writeBatchSize": 10000,
             "writeBatchTimeout": "60:00:00"
           }
         },
         "Policy": {
           "concurrency": 1,
           "executionPriorityOrder": "NewestFirst",
           "style": "StartOfInterval",
           "retry": 0,
           "timeout": "01:00:00"
         }
       }
     ],
     "start": "2017-05-11T00:00:00Z",
     "end": "2017-05-12T00:00:00Z",
     "isPaused": false
   }
  }
    ```   
   - A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa****Copy** értékre van beállítva. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A Data Factory megoldásaiban használhatja az [adatátalakítási tevékenységeket](data-factory-data-transformation-activities.md) is.
   - A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. 
   - A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. A másolási tevékenység által forrásként és fogadóként támogatott adattárak teljes listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Egy forrásként/fogadóként támogatott konkrét adattár használatával kapcsolatos útmutatóért kattintson a tábla adott hivatkozására.  
     
     A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2016-02-03” értéket, amely a következőnek felel meg: „2016-02-03T00:00:00Z”
     
     Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](https://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
     
     Ha nem ad meg értéket a **Befejezés** tulajdonsághoz, a rendszer a következőt számítja ki: "**Start + 48 óra**". A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
     
     Az előző példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.

     A folyamathoz tartozó definíció JSON-tulajdonságainak leírásáért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). A másolási tevékenységhez tartozó definíció JSON-tulajdonságainak leírásáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A BlobSource által támogatott JSON-tulajdonságok leírásáért lásd: [Azure Blob-összekötő](data-factory-azure-blob-connector.md). Az SqlSink által támogatott JSON-tulajdonságok leírása az [Azure SQL Database-összekötő](data-factory-azure-sql-connector.md) című cikkben található.

## <a name="publishdeploy-data-factory-entities"></a>Data Factory-entitások közzététele/üzembe helyezése
Ebben a lépésben a korábban létrehozott Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) teszi közzé. Emellett megadja az entitások tárolására szolgáló új data factory nevét.  

1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra. 
2. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
3. A következő párbeszédpanelnek kell megjelennie:
   
   ![Publish (Közzététel) párbeszédpanel](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. A Data Factory konfigurálása lapon hajtsa végre a következő lépéseket: 
   
   1. Válassza a **Create New Data Factory** (Új data factory létrehozása) lehetőséget.
   2. A **Name** (Név) mezőbe írja be a következőt: **VSTutorialFactory**.  
      
      > [!IMPORTANT]
      > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a közzététel során hibaüzenetet kap a data factory nevéről, módosítsa a data factory nevét (például sajátnévVSTutorialFactory értékre), majd próbálkozzon újra a közzététellel. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.        
      > 
      > 
   3. A **Subscription** (Előfizetés) mezőben válassza ki az Azure-előfizetést.
      
      > [!IMPORTANT]
      > Ha egy előfizetést sem lát, ellenőrizze, hogy olyan fiókkal jelentkezett-e be, amely rendszergazdája vagy társadminisztrátora az előfizetésnek.  
      > 
      > 
   4. Válassza ki a data factoryhoz az **erőforráscsoportot**. 
   5. Válassza ki a data factoryhoz a **régiót**. A legördülő listában csak a Data Factory szolgáltatás által támogatott régiók jelennek meg.
   6. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz.
      
       ![Configure data factory (Data factory konfigurálása) oldal](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.
   
   ![Publish items (Elemek közzététele) oldal](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
   
   ![Publish summary (Összefoglaló közzététele) oldal](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra.
 
   ![Üzembe helyezés állapota lap](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Vegye figyelembe a következő szempontokat: 

* Ha a „This subscription is not registered to use namespace Microsoft.DataFactory” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 
  
  * Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 

  ```powershell    
  Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
  Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e. 
    
  ```powershell
  Get-AzResourceProvider
    ```
  * Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.
* Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.

> [!IMPORTANT]
> Data Factory példányok létrehozásához az Azure-előfizetés rendszergazdájának/társ-rendszergazdájának kell lennie

## <a name="monitor-pipeline"></a>Folyamat figyelése
Lépjen az adat-előállítója kezdőlapjára:

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).
2. A bal oldali menüben kattintson a **További szolgáltatások**, majd az **Adat-előállítók** elemre.

  ![Data factoryk tallózása](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Kezdje el begépelni az adat-előállító nevét.

  ![Adat-előállító neve](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Kattintson az adat-előállítóra az eredménylistában az adat-előállító kezdőlapjának megnyitásához.

  ![Data factory kezdőlap](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Az [Adatkészletek és folyamatok figyelése](data-factory-monitor-manage-pipelines.md) című cikk útmutatásait követve monitorozhatja az oktatóanyagban létrehozott folyamatot és adatkészleteket. A Visual Studio jelenleg nem támogatja a Data Factory-folyamatok monitorozását. 

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban létrehozott egy Azure-beli adatgyárat az adatok Azure-blobból Azure SQL Databaseba való másolásához. A Visual Studiót használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1. Létrehozott egy Azure **data factoryt**.
2. **Társított szolgáltatásokat** hozott létre:
   1. Egy **Azure Storage** társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók társításához.     
   2. Egy **Azure SQL** társított szolgáltatás a kimeneti adatokat tároló adatbázis összekapcsolásához. 
3. **Adatkészleteket** hozott létre, amelyek az adatcsatorna bemeneti és kimeneti adatait írják le.
4. Létrehozott egy **folyamatot** egy **Másolási tevékenységgel**, ahol a **BlobSource** a forrás, az **SqlSink** pedig a fogadó. 

Ha szeretné megtudni, hogyan lehet az Azure HDInsight-fürtön keresztül átalakítani az HDInsight-struktúra tevékenységeit, tekintse meg [az oktatóanyag: az első folyamat létrehozása az adatátalakításhoz a Hadoop-fürt használatával](data-factory-build-your-first-pipeline.md)című témakört.

Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket. 

## <a name="view-all-data-factories-in-server-explorer"></a>Az összes adat-előállító megjelenítése a Kiszolgálókezelőben (Server Explorer)
Ez a szakasz azt ismerteti, hogy hogyan használható a Visual Studio Kiszolgálókezelője az Azure előfizetéshez tartozó összes adat-előállító megjelenítésére, és hogyan hozható létre Visual Studio projekt egy meglévő adat-előállító alapján. 

1. A **Visual Studio** menüjében kattintson a **View** (Megtekintés), majd a **Server Explorer** (Kiszolgálókezelő) elemre.
2. A Server Explorer (Kiszolgálókezelő) ablakban bontsa ki az **Azure**, majd a **Data Factory** elemet. Ha megjelenik a **Sign in to Visual Studio** (Jelentkezzen be a Visual Studióba) üzenet, adja meg az Azure-előfizetéséhez társított **fiókot**, és kattintson a **Continue** (Folytatás) elemre. Adja meg a **jelszót**, és kattintson a **Sign in** (Bejelentkezés) elemre. A Visual Studio megpróbálja lekérni az információkat az előfizetésében elérhető összes Azure data factoryről. Ennek a műveletnek az állapota a **Data Factory Task List** (Data Factory-feladatlista) ablakban jelenik meg.

    ![Server Explorer (Kiszolgálókezelő)](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Visual Studio-projekt létrehozása egy meglévő adat-előállító alapján

- Ha egy meglévő adat-előállító alapján szeretne létrehozni Visual Studio-projektet, kattintson a jobb gombbal egy adat-előállítóra a Kiszolgálókezelőben, és válassza az **Export Data Factory to New Project** (Adat-előállító exportálása új projektbe) lehetőséget.

  ![Data factory exportálása VS-projektbe](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

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
              "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban az Azure Blob Storage-t forrás adattárként használta, és a másolási művelet során Azure SQL Database célként megadott adattárként. Az alábbi táblázatban a másolási tevékenység által támogatott forrásadattárak és céladattárak listája látható: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

A táblázatban lévő adattárak hivatkozására kattintva megismerheti az adattárakba és az adattárakból történő adatmásolás módszereit.
