---
title: Egyéni tevékenységek használata Azure Data Factory-folyamatban
description: Ismerje meg, hogyan hozhat létre egyéni tevékenységeket, és hogyan használhatja őket egy Azure Data Factory folyamat során.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 3832175910f3a6d3e6a7de8da932b32436cc2452
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393020"
---
# <a name="use-custom-activities-in-an-azure-data-factory-version-1-pipeline"></a>Egyéni tevékenységek használata egy Azure Data Factory 1-es verziójú adatcsatornán
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-use-custom-activities.md)
> * [2-es verzió (aktuális verzió)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [egyéni tevékenységek a v2-ben](../transform-data-using-dotnet-custom-activity.md)című témakört.

A Azure Data Factory-folyamatokban két típusú tevékenység használható.

- Adatáthelyezési [tevékenységek](data-factory-data-movement-activities.md) a [támogatott forrás-és fogadó adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats)közötti adatátvitel céljából.
- [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) az adatok számítási szolgáltatásokkal, például az Azure HDInsight, a Azure batch és a Azure Machine learning Studio (klasszikus) használatával történő átalakításához.

Ha olyan adattárból/adattárba szeretne áthelyezni egy Data Factory, amelyet nem támogat, hozzon létre **egyéni tevékenységet** saját adatáthelyezési logikával, és használja a folyamatot egy folyamaton belül. Hasonlóképpen, ha a Data Factory által nem támogatott módon kívánja átalakítani vagy feldolgozni az adatfeldolgozást, hozzon létre egy egyéni tevékenységet a saját Adatátalakítási logikával, és használja a folyamatot egy folyamaton belül.

Egyéni tevékenységeket úgy konfigurálhat, hogy a virtuális gépek **Azure batch** készletén fusson. Azure Batch használatakor csak egy meglévő Azure Batch-készletet használhat.

Az alábbi bemutató lépésről lépésre bemutatja, hogyan hozhat létre egyéni .NET-tevékenységeket, és hogyan használhatja az egyéni tevékenységet egy folyamaton belül. Az útmutató egy **Azure batch** társított szolgáltatást használ.

> [!IMPORTANT]
> - A helyszíni adatforrásokhoz való hozzáféréshez nem használható adatkezelés átjáró egyéni tevékenységből. A [adatkezelés-átjáró](data-factory-data-management-gateway.md) jelenleg csak a másolási tevékenységet és a tárolt eljárási tevékenységet támogatja a Data Factoryban.

## <a name="walkthrough-create-a-custom-activity"></a>Útmutató: egyéni tevékenység létrehozása
### <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2012/2013/2015/2017
* Az [Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése.

### <a name="azure-batch-prerequisites"></a>Előfeltételek Azure Batch
Az útmutatóban az egyéni .NET-tevékenységeket Azure Batch számítási erőforrásként futtathatja. Az **Azure Batch** platformszolgáltatás lehetővé teszi, hogy hatékonyan futtasson nagyméretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. Azure Batch a **virtuális gépek felügyelt gyűjteményén** futó, nagy számítási igényű munkát ütemezhet, és automatikusan méretezheti a számítási erőforrásokat a feladatok igényeinek kielégítése érdekében. A Azure Batch szolgáltatás részletes áttekintését a [Azure batch alapjairól][batch-technical-overview] szóló cikkben találja.

Az oktatóanyaghoz hozzon létre egy Azure Batch-fiókot virtuális gépek készletével. A lépések a következők:

1. Hozzon létre egy **Azure batch fiókot** a [Azure Portal](https://portal.azure.com)használatával. Útmutatásért tekintse meg a [Azure batch-fiók létrehozása és kezelése][batch-create-account] című cikket.
2. Jegyezze fel a Azure Batch fiók nevét, a fiók kulcsát, az URI-t és a készlet nevét. Szükség van rájuk egy Azure Batch társított szolgáltatás létrehozásához.
    1. Azure Batch fiók kezdőlapján a következő formátumban jelenik meg egy **URL-cím** : `https://myaccount.westus.batch.azure.com` . Ebben a példában a **MyAccount** a Azure batch fiók neve. A társított szolgáltatás definíciójában használt URI az URL-cím a fiók neve nélkül. Példa: `https://<region>.batch.azure.com`.
    2. A bal oldali menüben kattintson a **kulcsok** elemre, és másolja ki az **elsődleges hozzáférési kulcsot**.
    3. Meglévő készlet használatához kattintson a menü **készletek** elemére, és jegyezze fel a készlet **azonosítóját** . Ha nem rendelkezik meglévő készlettel, lépjen a következő lépésre.
2. Hozzon létre egy **Azure batch készletet**.

   1. A [Azure Portal](https://portal.azure.com)kattintson a bal oldali menüben a **Tallózás** elemre, majd kattintson a **Batch-fiókok** elemre.
   2. Válassza ki a Azure Batch fiókot a **Batch-fiók** panel megnyitásához.
   3. Kattintson a **készletek** csempére.
   4. A **készletek** panelen kattintson a Hozzáadás gombra az eszköztáron a készlet hozzáadásához.
      1. Adja meg a készlet azonosítóját (készlet azonosítója). Jegyezze **fel a készlet azonosítóját**; a Data Factory megoldás létrehozásakor szüksége lesz rá.
      2. A **Windows Server 2012 R2** értéket kell megadnia az operációs rendszer családjának beállításához.
      3. Válassza ki a **csomópontok díjszabási szintjét**.
      4. A **cél dedikált** beállítás értékeként adja meg a **2** értéket.
      5. Adja meg a **2** értéket a **maximális feladatokhoz a csomópont** -beállításnál.
   5. A készlet létrehozásához kattintson az **OK** gombra.
   6. Jegyezze fel a készlet **azonosítóját** .

### <a name="high-level-steps"></a>Magas szintű lépések
Az alábbi két, az útmutató részeként elvégzendő lépés:

1. Hozzon létre egy egyéni tevékenységet, amely egyszerű Adatátalakítási/-feldolgozási logikát tartalmaz.
2. Hozzon létre egy Azure-beli adatelőállítót egy olyan folyamattal, amely az egyéni tevékenységet használja.

### <a name="create-a-custom-activity"></a>Egyéni tevékenység létrehozása
.NET-alapú egyéni tevékenység létrehozásához hozzon létre egy, a **IDotNetActivity** felületet megvalósító osztállyal rendelkező **.net-osztálybeli függvénytár** -projektet. Ez az illesztőfelület csak egy metódussal rendelkezik: [Execute](/dotnet/api/microsoft.azure.management.datafactories.runtime.idotnetactivity) és aláírása:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

A metódus négy paramétert vesz igénybe:

- **linkedServices**. Ez a tulajdonság a tevékenységhez tartozó bemeneti/kimeneti adatkészletek által hivatkozott Adattárbeli társított szolgáltatások enumerable listája.
- **adatkészletek**. Ez a tulajdonság a tevékenység bemeneti/kimeneti adatkészletei enumerable listája. Ezzel a paraméterrel beolvashatja a bemeneti és kimeneti adatkészletek által meghatározott helyet és sémákat.
- **tevékenység**. Ez a tulajdonság az aktuális tevékenységet jelöli. Az egyéni tevékenységhez társított bővített tulajdonságok elérésére használható. További részletek: [hozzáférés a kiterjesztett tulajdonságokhoz](#access-extended-properties) .
- **naplózó**. Ez az objektum lehetővé teszi a folyamathoz tartozó felhasználói naplóban lévő felületi hibakeresési megjegyzések írását.

A metódus egy olyan szótárt ad vissza, amely az egyéni tevékenységek a jövőben történő láncolására használható. Ez a szolgáltatás még nincs megvalósítva, ezért a metódusból üres szótárt ad vissza.

### <a name="procedure"></a>Eljárás

1. Hozzon létre egy **.net Class Library** -projektet.
   
    <ol type="a">
     <li>Indítsa el a Visual studiót.</li>
     <li>Kattintson a <b>File</b> (Fájl) menüre, mutasson a <b>New</b> (Új) elemre, és kattintson a <b>Project</b> (Projekt) lehetőségre.</li>
     <li>Bontsa ki a <b>Sablonok</b> lehetőséget, és válassza a <b>Visual C#</b> lehetőséget. Ebben az útmutatóban a C# nyelvet használja, de bármilyen .NET-nyelv használatával fejlesztheti az egyéni tevékenységeket.</li>
     <li>A jobb oldalon válassza ki az <b>osztály könyvtára</b> elemet a projekttípus listából. A Visual Studióban válassza az <b>osztály könyvtára (.NET-keretrendszer) lehetőséget.</b> </li>
     <li>Adja <b></b> meg a MyDotNetActivity <b>nevet</b>.</li>
     <li>A <b>helyhez</b>válassza a <b>C:\ADFGetStarted</b> lehetőséget.</li>
     <li>A projekt létrehozásához kattintson az <b>OK</b> gombra.</li>
   </ol>

2. Kattintson az **Eszközök** elemre, mutasson a **NuGet Package Manager** (NuGet-csomagkezelő) lehetőségre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre.

3. A Package Manager konzolon hajtsa végre a következő parancsot a **Microsoft. Azure. Management. DataFactories** importálásához.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```

4. Importálja az **Azure Storage** NuGet-csomagot a projektbe.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory Service Launcher használatához a WindowsAzure. Storage 4,3-es verziója szükséges. Ha az Azure Storage-szerelvény egy újabb verziójára mutató hivatkozást ad hozzá az egyéni tevékenység projektben, hibaüzenet jelenik meg a tevékenység végrehajtásakor. A hiba elhárításához lásd: [alkalmazástartomány elkülönítése](#appdomain-isolation) szakasz.
5. Adja hozzá a következő **using** utasítást a forrásfájlban a projektben.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

6. Módosítsa a **névtér** nevét a **MyDotNetActivityNS** értékre.

    ```csharp
    namespace MyDotNetActivityNS
    ```

7. Módosítsa az osztály nevét a **MyDotNetActivity** értékre, és származtatja azt a **IDotNetActivity** felületen az alábbi kódrészletben látható módon:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```

8. Implementálja (adja hozzá) a **IDotNetActivity** felület **Execute** metódusát a **MyDotNetActivity** osztályhoz, és másolja az alábbi mintakód a metódusba.

    Az alábbi minta a keresési kifejezés ("Microsoft") előfordulásának számát számolja az adatszelethez társított minden blobban.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);

            // Calculate method returns the number of occurrences of
            // the search term ("Microsoft") in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```

9. Adja hozzá a következő segítő metódusokat:

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>

    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    A GetFolderPath metódus Visszaadja annak a mappának az elérési útját, amelyre az adatkészlet mutat, és a GetFileName metódus Visszaadja annak a blobnak/fájlnak a nevét, amelyre az adatkészlet mutat. Ha a folderPath olyan változókat használ, mint például a {Year}, a {month}, a {Day} stb., a metódus visszaadja a karakterláncot, mivel a futásidejű értékekkel nem helyettesíti őket. A SliceStart, SliceEnd stb. elérésével kapcsolatos részletekért lásd: [hozzáférés a kiterjesztett tulajdonságokhoz](#access-extended-properties) szakasz.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    A számítás módszer kiszámítja a Microsoft kulcsszó példányainak számát a bemeneti fájlokban (Blobok a mappában). A keresési kifejezés ("Microsoft") nem rögzített a kódban.

10. A projekt fordítása. Kattintson a **Létrehozás** lehetőségre a menüben, majd kattintson a **megoldás létrehozása** lehetőségre.

    > [!IMPORTANT]
    > A .NET-keretrendszer 4.5.2-es verziójának beállítása a projekt célként szolgáló keretrendszereként: kattintson a jobb gombbal a projektre, majd kattintson a **Tulajdonságok** elemre a cél keretrendszer beállításához. A Data Factory nem támogatja az 4.5.2-nél újabb .NET-keretrendszer-verziókkal összeállított egyéni tevékenységeket.

11. Indítsa el a **Windows Intézőt**, és a Build típusától függően navigáljon a **bin\debug** vagy a **bin\release** mappához.

12. Hozzon létre egy zip-fájl **MyDotNetActivity.zip** , amely a \bin\Debug mappában található összes bináris fájlt tartalmazza \<project folder\> . Adja meg a **MyDotNetActivity. pdb** fájlt, hogy további részleteket kapjon, például a forráskódban található sorszámot, amely miatt hiba történt.

    > [!IMPORTANT]
    > Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.

    ![Bináris kimeneti fájlok](./media/data-factory-use-custom-activities/Binaries.png)

13. Hozzon létre egy **customactivitycontainer** nevű BLOB-tárolót, ha még nem létezik.

14. Töltse fel MyDotNetActivity.zip blobként a customactivitycontainer egy **általános célú** Azure Blob Storage-tárolóba (nem a gyors elérésű blob Storage-ba), amelyre a AzureStorageLinkedService hivatkozik.

> [!IMPORTANT]
> Ha hozzáadja ezt a .NET-tevékenység projektet egy olyan megoldáshoz a Visual Studióban, amely Data Factory projektet tartalmaz, és hozzáadja a .NET-tevékenység projekthez való hivatkozást a Data Factory alkalmazás-projektből, nem kell végrehajtania a zip-fájl manuális létrehozásához és az általános célú Azure Blob Storage-hoz való feltöltéséhez szükséges utolsó két lépést. Amikor a Visual Studióval tesz közzé Data Factory entitásokat, ezeket a lépéseket a közzétételi folyamat automatikusan végrehajtja. További információ: [Data Factory Project in Visual Studio](#data-factory-project-in-visual-studio) szakasz.

## <a name="create-a-pipeline-with-custom-activity"></a>Folyamat létrehozása egyéni tevékenységgel

Létrehozott egy egyéni tevékenységet, és feltöltte a bináris fájlokat egy **általános célú** Azure Storage-fiókban található blob-tárolóba. Ebben a szakaszban egy Azure-beli adatelőállítót hoz létre egy olyan folyamattal, amely az egyéni tevékenységet használja.

Az egyéni tevékenység bemeneti adatkészlete blobokat (fájlokat) jelöl a blob Storage adftutorial tárolójának customactivityinput mappájába. A tevékenység kimeneti adatkészlete a blob Storage adftutorial tárolójának customactivityoutput mappájában lévő kimeneti blobokat jelöli.

Hozzon létre **file.txt** fájlt a következő tartalommal, és töltse fel a **adftutorial** -tároló **customactivityinput** mappájába. Ha még nem létezik, hozza létre az adftutorial tárolót.

```
test custom activity Microsoft test custom activity Microsoft
```

A bemeneti mappa a Azure Data Factory egy szeletének felel meg, még akkor is, ha a mappa két vagy több fájllal rendelkezik. Ha az egyes szeleteket a folyamat dolgozza fel, az egyéni tevékenység megismétli az adott szelet bemeneti mappájában található összes blobot.

Egy kimeneti fájl jelenik meg a adftutorial\customactivityoutput mappában egy vagy több sorral (ugyanaz, mint a bemeneti mappában található Blobok száma):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Ebben a szakaszban a következő lépéseket hajtja végre:

1. Hozzon létre egy **adatelőállítót**.
2. **Társított szolgáltatásokat** hozhat létre azon virtuális gépek Azure batch készletéhez, amelyeken az egyéni tevékenység fut, valamint az Azure Storage-t, amely a bemeneti/kimeneti blobokat tárolja.
3. Olyan bemeneti és kimeneti **adatkészleteket** hozhat létre, amelyek az egyéni tevékenység bemenetét és kimenetét jelölik.
4. Hozzon **létre egy folyamatot** , amely az egyéni tevékenységet használja.

> [!NOTE]
> Hozza létre a **file.txt** , és töltse fel egy blob-tárolóba, ha még nem tette meg. Lásd az előző szakaszban található utasításokat.

### <a name="step-1-create-the-data-factory"></a>1. lépés: az adatelőállító létrehozása
1. A Azure Portalba való bejelentkezés után hajtsa végre a következő lépéseket:
   1. Kattintson az **erőforrás létrehozása** elemre a bal oldali menüben.
   2. Kattintson a **adatok és Analitika** elemre az **új** panelen.
   3. Kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen.

      ![Új Azure Data Factory menü](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Az **új adatgyár** panelen írja be a **CustomActivityFactory** nevet. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja: a **"CustomActivityFactory" nem érhető el** az adatfeldolgozó neve, módosítsa az adatgyár nevét (például **yournameCustomActivityFactory**), és próbálkozzon újra a létrehozással.

    ![Új Azure Data Factory panel](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kattintson az **ERŐFORRÁSCSOPORT neve** lehetőségre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.
4. Győződjön meg arról, hogy a megfelelő **előfizetést** és **régiót** használja, ahol létre szeretné hozni az adatok előállítóját.
5. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
6. Ekkor megjelenik a Azure Portal **irányítópultján** létrehozott adatelőállító.
7. Miután az adatelőállító sikeresen létrejött, megjelenik a Data Factory panel, amely megjeleníti az adatelőállító tartalmát.

    ![A Data Factory panel](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>2. lépés: társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Ebben a lépésben összekapcsolja az Azure Storage-fiókját, és Azure Batch fiókot az adatgyárhoz.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
1. Kattintson a **szerzőre, és telepítse** a csempét a **CUSTOMACTIVITYFACTORY** az **adatgyár** paneljén. Ekkor megjelenik a Data Factory Editor.
2. Kattintson a parancssáv **új adattár** elemére, és válassza az **Azure Storage** lehetőséget. A szerkesztőben megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-parancsfájl.

    ![Új adattár – Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Cserélje le az `<accountname>` Azure Storage-fiók nevét és `<accountkey>` Az Azure Storage-fiók hozzáférési kulcsát. A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.

    ![Azure Storage – szeretett szolgáltatás](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

#### <a name="create-azure-batch-linked-service"></a>Azure Batch társított szolgáltatás létrehozása
1. A Data Factory-szerkesztőben kattintson a **... elemre. További** információért kattintson a parancssáv **új számítás** elemére, majd válassza a menü **Azure batch** elemét.

    ![Új számítás – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Hajtsa végre a következő módosításokat a JSON-parancsfájlban:

   1. Adja meg Azure Batch fióknevet a **accountName** tulajdonsághoz. A **Azure batch fiók** panel **URL-címe** a következő formátumú: `http://accountname.region.batch.azure.com` . A JSON **batchUri** tulajdonságához el kell távolítania `accountname.` az URL-címet, és a `accountname` for the `accountName` JSON tulajdonságot kell használnia.
   2. Itt adhatja meg a **accessKey** tulajdonsághoz tartozó Azure batch-fiók kulcsát.
   3. Adja meg a **poolName** tulajdonság előfeltételeinek részeként létrehozott készlet nevét. A készlet AZONOSÍTÓját a készlet neve helyett is megadhatja.
   4. Azure Batch URI megadása a **batchUri** tulajdonsághoz. Példa: `https://westus.batch.azure.com`.
   5. A **linkedServiceName** tulajdonság **AzureStorageLinkedService** megadása.

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       A **poolName** tulajdonsághoz a készlet neve helyett a készlet azonosítóját is megadhatja.

### <a name="step-3-create-datasets"></a>3. lépés: adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre a bemeneti és kimeneti adatok ábrázolásához.

#### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. A Data Factory **szerkesztőjében** kattintson a... elemre. **További információ** a parancssáv, kattintson az **új adatkészlet** elemre, majd válassza ki az **Azure Blob Storage** lehetőséget a legördülő menüből.
2. Cserélje le a JSON-t a jobb oldali ablaktáblán a következő JSON-kódrészletre:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```

   Az útmutató későbbi részében létrehoz egy folyamatot a kezdési időponttal: 2016-11-16T00:00:00Z és befejezési idő: 2016-11-16T05:00:00Z. Az adatok óránkénti előállítására vannak ütemezve, így öt bemeneti/kimeneti szelet van ( **00**: 00:00 – > **05**: 00:00).

   A bemeneti adatkészlet **gyakorisága** és **intervalluma** **óra** és **1** értékre van állítva, ami azt jelenti, hogy a bemeneti szelet óránként elérhető. Ebben a példában ugyanaz a fájl (file.txt) szerepel a intputfolder.

   Itt láthatók az egyes szeletek kezdő időpontjai, amelyeket a fenti JSON-kódrészletben a SliceStart System változó képvisel.
3. A **InputDataset** létrehozásához és üzembe helyezéséhez kattintson az eszköztár **üzembe helyezés** gombjára. Győződjön meg arról, hogy a szerkesztő címsorában megjelenik a **TABLE CREATED SUCCESSFULLY** (A TÁBLA SIKERESEN LÉTREJÖTT) üzenet.

#### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
1. A **Data Factory-szerkesztőben** kattintson a **... elemre. További információ** a parancssáv, kattintson az **új adatkészlet**, majd az **Azure Blob Storage** elemre.
2. Cserélje le a JSON-szkriptet a jobb oldali ablaktáblán a következő JSON-szkripttel:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     A kimeneti hely a **adftutorial/customactivityoutput/** és a kimeneti fájl neve yyyy-MM-dd-HH.txt, ahol az éééé-hh-nn-hh a létrehozott szelet éve, hónapja, dátuma és órája. A részletekért tekintse meg a [fejlesztői referenciát][adf-developer-reference] .

    Minden bemeneti szelethez kimeneti blob/fájl jön létre. Az egyes szeletekhez tartozó kimeneti fájl nevét itt találja. Az összes kimeneti fájl egy kimeneti mappában jön létre: **adftutorial\customactivityoutput**.

   | Szelet | Kezdési idő | Kimeneti fájl |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Ne feledje, hogy a bemeneti mappában lévő összes fájl egy szelet részét képezi a fent említett kezdési időpontokban. A szelet feldolgozásakor az egyéni tevékenység átvizsgálja az egyes fájlokat, és létrehoz egy sort a kimeneti fájlban a keresési kifejezés ("Microsoft") előfordulásának számával. Ha a bemeneti mappában három fájl található, a kimeneti fájlban három sor található az óránkénti szeletekhez: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt stb.
3. A **OutputDataset** üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Egyéni tevékenységet használó folyamat létrehozása és futtatása
1. A Data Factory-szerkesztőben kattintson a **... elemre. Továbbiak**, majd válassza a parancssáv **új folyamat** elemét.
2. Cserélje le a JSON-t a jobb oldali ablaktáblán a következő JSON-szkripttel:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Vegye figyelembe a következő szempontokat:

   * A **Egyidejűség** **2** értékre van állítva, hogy két szeletet párhuzamosan dolgozza fel a rendszer a Azure batch készletben lévő 2 virtuális géppel.
   * A tevékenységek szakaszban egy tevékenység található, és a következő típusú: **DotNetActivity**.
   * A **AssemblyName** értéke a dll neve: **MyDotnetActivity.dll**.
   * A **BelépésiPont** értéke **MyDotNetActivityNS. MyDotNetActivity**.
   * A **PackageLinkedService** értéke **AzureStorageLinkedService** , amely az egyéni tevékenység zip-fájlját tartalmazó blob Storage-ra mutat. Ha különböző Azure Storage-fiókokat használ a bemeneti/kimeneti fájlokhoz és az egyéni tevékenység zip-fájljához, akkor létrehoz egy másik Azure Storage-beli társított szolgáltatást. Ez a cikk azt feltételezi, hogy ugyanazt az Azure Storage-fiókot használja.
   * A **PackageFile** értéke **customactivitycontainer/MyDotNetActivity.zip**. Formátuma: containerforthezip/nameofthezip.zip.
   * Az egyéni tevékenység kimenetként a bemeneti és a **OutputDataset** **InputDataset** veszi át.
   * Az egyéni tevékenység linkedServiceName tulajdonsága a **AzureBatchLinkedService** mutat, ami azt jelzi, Azure Data Factory, hogy az egyéni tevékenységnek Azure batch virtuális gépeken kell futnia.
   * a **ispaused fogalmak** tulajdonság alapértelmezés szerint **hamis** értékre van állítva. Ebben a példában a folyamat azonnal fut, mert a szeletek a múltban kezdődnek. Ezt a tulajdonságot igaz értékre állíthatja, ha szüneteltetni szeretné a folyamatot, és visszaállítja a False (hamis) értéket az újraindításhoz.
   * A **kezdési** és a **befejezési** idő **öt** óra, a szeletek pedig óránként jönnek létre, így a folyamat öt szeletet állít elő.
3. A folyamat üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. A Azure Portal Data Factory paneljén kattintson a **diagram** elemre.

    ![Diagram csempe](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. A diagram nézetben most kattintson a OutputDataset.

    ![Diagramnézet](./media/data-factory-use-custom-activities/diagram.png)
3. Látnia kell, hogy az öt kimeneti szelet üzemkész állapotban van. Ha nincsenek kész állapotban, még nem állították elő őket.

   ![Kimeneti szeletek](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Győződjön meg arról, hogy a kimeneti fájlok a blob Storage-ban jönnek létre a **adftutorial** -tárolóban.

   ![kimenet egyéni tevékenységből][image-data-factory-output-from-custom-activity]
5. A kimeneti fájl megnyitásakor az alábbi kimenethez hasonló kimenetnek kell megjelennie:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. A [Azure Portal][azure-preview-portal] vagy Azure PowerShell parancsmagokkal figyelheti az adat-előállítót, a folyamatokat és az adatkészleteket. A **ActivityLogger** lévő üzeneteket a naplókban (kifejezetten User-0. log) lévő egyéni tevékenység kódjában tekintheti meg, amelyet a portálról tölthet le, vagy parancsmagokat használhat.

   ![naplók letöltése egyéni tevékenységből][image-data-factory-download-logs-from-custom-activity]

Az adatkészletek és a folyamatok figyelésének részletes lépéseiért lásd: [folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md) .

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projekt a Visual Studióban
Data Factory entitásokat a Azure Portal használata helyett a Visual Studióval hozhat létre és tehet közzé. A Visual Studióval Data Factory entitások létrehozásával és közzétételével kapcsolatos részletes információkért lásd: [az első folyamat létrehozása a Visual Studióval](data-factory-build-your-first-pipeline-using-vs.md) és az [adatok másolása az Azure Blobból az Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) -cikkekbe.

Ha Data Factory projektet hoz létre a Visual Studióban, hajtsa végre a következő további lépéseket:

1. Adja hozzá a Data Factory projektet a Visual Studio-megoldáshoz, amely az egyéni tevékenység projektjét tartalmazza.
2. Adjon hozzá egy hivatkozást a .NET-tevékenység projekthez a Data Factory projektből. Kattintson a jobb gombbal Data Factory projekt elemre, mutasson a **Hozzáadás** elemre, majd kattintson a **hivatkozás** elemre.
3. A **hivatkozás hozzáadása** párbeszédpanelen válassza ki a **MyDotNetActivity** projektet, majd kattintson az **OK** gombra.
4. Hozza létre és tegye közzé a megoldást.

    > [!IMPORTANT]
    > Data Factory entitások közzétételekor a rendszer automatikusan létrehoz egy zip-fájlt, és feltölti a blob-tárolóba: customactivitycontainer. Ha a blob-tároló nem létezik, az automatikusan létrejön.

## <a name="data-factory-and-batch-integration"></a>Data Factory és batch-integráció
A Data Factory szolgáltatás létrehoz egy feladatot a Azure Batchban a (z): **ADF-poolname: Job-XXX** néven. Kattintson a bal oldali menü **feladatok** elemére.

![Azure Data Factory – batch-feladatok](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

A rendszer létrehoz egy feladatot egy szelet minden tevékenységéhez. Ha öt szelet áll készen a feldolgozásra, öt feladat jön létre ebben a feladatban. Ha a Batch-készletben több számítási csomópont is található, a két vagy több szelet párhuzamosan futtatható. Ha a számítási csomópontok maximális feladatait > 1 értékre állítja be, akkor ugyanazon a számításon belül egynél több szelet is fut.

![Azure Data Factory – kötegelt feladat feladatai](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

A következő ábra a Azure Data Factory és a kötegelt feladatok közötti kapcsolatot szemlélteti.

![Data Factory & batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Hibák elhárítása
A hibaelhárítás néhány alapvető módszerből áll:

1. Ha a következő hibaüzenet jelenik meg, akkor előfordulhat, hogy egy általános célú Azure Blob Storage helyett egy gyors/lassú blob Storage-tárolót használ. Töltse fel a zip-fájlt egy **általános célú Azure Storage-fiókba**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Ha a következő hiba jelenik meg, ellenőrizze, hogy a CS-fájlban szereplő osztály neve megegyezik-e a folyamat JSON- **BelépésiPont** tulajdonságához megadott névvel. Az útmutatóban az osztály neve: MyDotNetActivity, a JSON-ban pedig a BelépésiPont: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Ha a nevek egyeznek, győződjön meg arról, hogy az összes bináris fájl a zip-fájl **gyökérkönyvtárában** található. Ez azt eredményezi, hogy a zip-fájl megnyitásakor a gyökérmappa összes fájlját látnia kell, nem pedig az almappákban.
3. Ha a bemeneti szelet nem **üzemkész** állapotra van állítva, ellenőrizze, hogy helyesek-e a bemeneti mappa szerkezete, és **file.txt** létezik-e a bemeneti mappákban.
3. Az egyéni tevékenység **végrehajtás** metódusában a **IActivityLogger** objektum használatával naplózhatja azokat az információkat, amelyek segítenek a hibák elhárításában. A naplózott üzenetek a felhasználói naplófájlokban jelennek meg (egy vagy több fájl neve: user-0. log, User-1. log, User-2. log stb.).

   A **OutputDataset** panelen kattintson a szeletre az adott szelethez tartozó **adatszelet** panel megjelenítéséhez. Ekkor megjelenik az adott szelet **tevékenység-futtatása** . Ekkor egy tevékenységnek kell futnia a szelethez. Ha a parancssáv Futtatás gombjára kattint, elindíthat egy másik tevékenység futtatását ugyanarra a szeletre.

   Amikor rákattint a tevékenység futtatására, a **tevékenység futtatása részletek panel** jelenik meg a naplófájlok listájával. A naplózott üzenetek a user_0. log fájlban láthatók. Hiba esetén három tevékenység fut, mert az újrapróbálkozások száma a folyamat/tevékenység JSON-ben 3 értékre van állítva. Amikor rákattint a tevékenység futtatására, a naplófájlokat láthatja, amelyeket a hiba megoldásához tekinthet meg.

   A naplófájlok listájában kattintson a **User-0. log** fájlra. A jobb oldali panelen a **IActivityLogger. Write** metódus használatának eredményei láthatók. Ha nem látja az összes üzenetet, ellenőrizze, hogy van-e további naplófájl neve: user_1. log, user_2. log stb. Ellenkező esetben előfordulhat, hogy a kód az utolsó naplózott üzenet után meghiúsult.

   Továbbá az **System-0. log naplófájlban** találhatja meg a rendszerhibákat és a kivételeket.
4. Adja meg a **PDB** -fájlt a zip-fájlban, hogy a hiba részletei olyan információkkal rendelkezzenek, mint a **hívási verem** , ha hiba történik.
5. Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.
6. Győződjön meg arról, hogy a **assemblyName** (MyDotNetActivity.dll), a **BelépésiPont**(MyDotNetActivityNS. MyDotNetActivity), a **packageFile** (Customactivitycontainer/MyDotNetActivity.zip) és a **packageLinkedService** (a zip-fájlt tartalmazó **általános célú** Azure Blob-tárolóra kell mutatnia) a helyes értékre van állítva.
7. Ha kijavított egy hibát, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre az **OutputDataset** panelen, és kattintson a **Futtatás** parancsra.
8. Ha a következő hibaüzenet jelenik meg, akkor az Azure Storage-csomagot használja > 4.3.0. Data Factory Service Launcher használatához a WindowsAzure. Storage 4,3-es verziója szükséges. Ha az Azure Storage szerelvény újabb verzióját kell használnia, tekintse meg a [alkalmazástartomány elkülönítése](#appdomain-isolation) című szakaszt.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Ha az Azure Storage-csomag 4.3.0 verzióját használja, távolítsa el a meglévő Azure Storage-csomagra > 4.3.0 verzióra mutató hivatkozást. Ezután futtassa a következő parancsot a NuGet Package Manager konzolról.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    A projekt felépítése. Törölje az Azure. Storage szerelvényt > 4.3.0 a bin\Debug mappából. Hozzon létre egy zip-fájlt bináris fájlokkal és a PDB-fájllal. Cserélje le a régi zip-fájlt erre a blob-tárolóba (customactivitycontainer). Futtassa újra a meghiúsult szeleteket (kattintson a jobb gombbal a szeletre, majd kattintson a Futtatás parancsra).
8. Az egyéni tevékenység nem használja a csomag **app.config** fájlját. Ezért ha a kód a konfigurációs fájlból olvassa be a kapcsolatok karakterláncait, nem működik futásidőben. A Azure Batch használata esetén az ajánlott eljárás az **Azure** kulcstartóban található összes titok **tárolására, egy** tanúsítványalapú egyszerű szolgáltatásnév használatával gondoskodik a kulcstartó védelméről, és elosztja a tanúsítványt Azure batch készletbe. A .NET egyéni tevékenysége ezután elérheti a titkos kulcsokat a kulcstartóból a futtatáskor. Ez a megoldás általános megoldás, és bármilyen típusú titokra méretezhető, nem csak a kapcsolódási sztringre.

   Egyszerűbb megkerülő megoldás (de nem ajánlott eljárás): létrehozhat egy **Azure SQL társított szolgáltatást** kapcsolati karakterlánc-beállításokkal, létrehozhat egy olyan adatkészletet, amely a társított szolgáltatást használja, és az adatkészletet az egyéni .net-tevékenységhez tartozó, dummy bemeneti adatkészletként láncba helyezi. Ezután elérheti a társított szolgáltatás kapcsolati karakterláncát az egyéni tevékenység kódjában.

## <a name="update-custom-activity"></a>Egyéni tevékenység frissítése
Ha frissíti az egyéni tevékenység kódját, hozza létre, és töltse fel a blob Storage-ba új bináris fájlokat tartalmazó zip-fájlt.

## <a name="appdomain-isolation"></a>Alkalmazástartomány elkülönítése
Lásd: [alkalmazástartomány minta](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) , amely bemutatja, hogyan hozható létre olyan egyéni tevékenység, amely nem korlátozott a Data Factory indító által használt szerelvény-verzióra (például: WindowsAzure. Storage v 4.3.0, Newtonsoft.Jsa v 6.0. x stb.).

## <a name="access-extended-properties"></a>Kiterjesztett tulajdonságok elérése
A következő példában látható módon deklarálhatja a speciális tulajdonságokat a JSON-tevékenységben:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

A példában két további tulajdonság létezik: **SliceStart** és **DataFactoryName**. A SliceStart értéke a SliceStart System változón alapul. A támogatott rendszerváltozók listáját a [rendszerváltozók](data-factory-functions-variables.md) részben tekintheti meg. A DataFactoryName értéke rögzített a CustomActivityFactory.

Ha ezeket a kiterjesztett tulajdonságokat a **végrehajtási** metódusban szeretné elérni, használja a következő kódhoz hasonló kódot:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch automatikus méretezése
Létrehozhat egy Azure Batch készletet is az **autoscale** funkcióval. Létrehozhat például egy 0 dedikált virtuális géppel rendelkező Azure batch-készletet és egy, a függőben lévő feladatok számán alapuló autoskálázási képletet.

A minta képlet a következő viselkedést éri el: a készlet első létrehozásakor 1 virtuális géppel kezdődik. $PendingTasks metrika meghatározza a futó + aktív (várólistán lévő) állapotú feladatok számát.  A képlet megkeresi a függőben lévő feladatok átlagos számát az utolsó 180 másodpercben, és ennek megfelelően beállítja a TargetDedicated. Biztosítja, hogy a TargetDedicated soha ne haladja meg a 25 virtuális gépet. Így az új feladatok elküldésekor a készlet automatikusan növekszik, és a feladatok elvégzése után a virtuális gépek egyszer sem lesznek elérhetők, és az automatikus skálázás csökkenti ezeket a virtuális gépeket. a startingNumberOfVMs és a maxNumberofVMs igényeihez igazítható.

Autoskálázási képlet:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

A részletekért lásd: [számítási csomópontok automatikus méretezése egy Azure batch készletben](../../batch/batch-automatic-scaling.md) .

Ha a készlet az alapértelmezett [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)használja, a Batch szolgáltatás 15-30 percet is igénybe vehet, hogy az egyéni tevékenység futtatása előtt előkészítse a virtuális gépet.  Ha a készlet eltérő autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percet is igénybe vehet.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Egyéni tevékenység létrehozása a .NET SDK használatával
A cikkben található útmutatóban létrehoz egy adatelőállítót egy olyan folyamattal, amely az egyéni tevékenységet használja az Azure Portal használatával. A következő kód bemutatja, hogyan hozhatja létre az adatelőállítót a .NET SDK használatával. Az SDK használatáról további részleteket a folyamat [létrehozása másolási tevékenységgel a .NET API használatával](data-factory-copy-activity-tutorial-using-dotnet-api.md) című cikkben talál.

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Egyéni tevékenység hibakeresése a Visual Studióban
A GitHubon található [Azure Data Factory helyi környezeti](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) minta olyan eszközt tartalmaz, amely lehetővé teszi az egyéni .net-tevékenységek hibakeresését a Visual Studióban.

## <a name="sample-custom-activities-on-github"></a>Egyéni tevékenységek mintája a GitHubon
| Sample | Milyen egyéni tevékenységet végez |
| --- | --- |
| [Http-adatletöltő](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Az adatok letöltése egy HTTP-végpontról az Azure-ba Blob Storage a Data Factory egyéni C#-tevékenységével. |
| [Twitter Hangulatelemzés minta](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Egy Azure Machine Learning Studio (klasszikus) modellt hív meg, és elvégzi az érzelmek elemzését, pontozását, előrejelzését stb. |
| [R-szkript futtatása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Az R-szkript meghívása RScript.exe futtatásával a HDInsight-fürtön, amelyen már telepítve van az R. |
| [Több alkalmazástartomány .NET-tevékenység](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |A Data Factory Launcher által használt különböző szerelvény-verziókat használ |
| [Modell újrafeldolgozása Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Azure Analysis Services-modell újrafeldolgozása. |

[batch-net-library]: ../../batch/quick-run-dotnet.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]:../../azure-sql/database/sql-database-paas-overview.md
[batch-get-started]: ../../batch/quick-run-dotnet.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: /previous-versions/azure/mt125880(v=azure.100)
[new-azure-batch-pool]: /previous-versions/azure/mt125936(v=azure.100)
[azure-batch-blog]: /archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png