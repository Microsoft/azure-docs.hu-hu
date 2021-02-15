---
title: Nagyméretű adatkészletek feldolgozása Data Factory és batch használatával
description: Ismerteti, hogyan lehet a Azure Batch párhuzamos feldolgozási képességével nagy mennyiségű adatmennyiséget feldolgozni egy Azure Data Factory-folyamatban.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c59e164d67a665a5e99cb5045b3b0cf38e33dc87
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364273"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Nagyméretű adatkészletek feldolgozása Data Factory és batch használatával
> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [Data Factory egyéni tevékenységek](../transform-data-using-dotnet-custom-activity.md)című témakört.

Ez a cikk a nagyméretű adatkészletek automatikus és ütemezett módon történő mozgatására és feldolgozására szolgáló minta megoldás architektúráját ismerteti. Emellett egy teljes körű útmutatót is biztosít a megoldás megvalósításához Data Factory és Azure Batch használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk hosszabb, mint egy tipikus cikk, mivel egy teljes minta megoldásról szóló bemutatót tartalmaz. Ha még nem ismeri a Batch és a Data Factoryt, megismerheti ezeket a szolgáltatásokat, és hogyan működnek együtt. Ha ismeri a szolgáltatásokat, és megtervezi/fejleszti a megoldást, akkor a cikk architektúra szakaszára koncentrálhat. Ha prototípust vagy megoldást fejleszt, érdemes kipróbálnia az útmutatóban ismertetett lépésenkénti útmutatót. A tartalommal kapcsolatos megjegyzéseit és felhasználásának módját meghívjuk.

Először nézzük meg, hogyan Data Factory és a Batch-szolgáltatások segíthetnek a nagyméretű adathalmazok feldolgozásában a felhőben.     


## <a name="why-azure-batch"></a>Miért Azure Batch?
 A Batch használatával hatékonyan futtathat nagy méretű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazásokat a felhőben. Ez egy platform-szolgáltatás, amely a nagy teljesítményű virtuális gépek (VM-EK) felügyelt gyűjteményén futtatott számítási igényű munkát ütemezhet. Automatikusan méretezheti a számítási erőforrásokat a feladatok igényeinek kielégítése érdekében.

A Batch szolgáltatással Azure számítási erőforrásokat határoz meg az alkalmazások párhuzamos és méretezhető futtatásához. Igény szerinti vagy ütemezett feladatokat is futtathat. Nem kell manuálisan létrehoznia, konfigurálnia és felügyelni a HPC-fürtöt, az egyes virtuális gépeket, a virtuális hálózatokat, illetve az összetett feladatokat és a feladatütemezés-infrastruktúrát.

 Ha nem ismeri a Batch szolgáltatást, a következő cikkek segítenek megérteni a cikkben ismertetett megoldás architektúráját/megvalósítását:   

* [A Batch alapjai](../../azure-sql/database/sql-database-paas-overview.md)
* [A Batch funkcióinak áttekintése](../../batch/batch-service-workflow-features.md)

Ha többet szeretne megtudni a Batch szolgáltatásról, tekintse meg [a Batch dokumentációját](../../batch/index.yml).

## <a name="why-azure-data-factory"></a>Miért Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. A Data Factory használatával olyan felügyelt adatfolyamatokat hozhat létre, amelyek a helyszíni és a Felhőbeli adattárakból egy központi adattárba helyezik át az adatátvitelt. Példa az Azure Blob Storage-ra. A Data Factory használatával az Azure HDInsight és a Azure Machine Learning szolgáltatásokkal dolgozhat fel és alakíthat át adatátalakítást. Az adatfolyamatokat ütemezett módon is ütemezheti (például óránként, naponta és hetente). Egy pillantással figyelheti és kezelheti a folyamatokat a problémák azonosításához és a műveletek elvégzéséhez.

  Ha nem ismeri a Data Factoryt, az alábbi cikkek segítenek megérteni a cikkben ismertetett megoldás architektúráját/megvalósítását:  

* [A Data Factory bemutatása](data-factory-introduction.md)
* [Az első adatfolyamat létrehozása](data-factory-build-your-first-pipeline.md)   

Ha további információra van szükség a Data Factoryről, tekintse meg [a Data Factory dokumentációját](/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory és batch együtt
A Data Factory beépített tevékenységeket tartalmaz. A másolási tevékenység például az adatok egy forrás adattárból a célhely adattárba való másolására/áthelyezésére szolgál. A kaptár tevékenység az Azure-beli Hadoop-fürtök (HDInsight-EK) használatával dolgozza fel az adatfeldolgozást. A támogatott átalakítási tevékenységek listáját az [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md)című részben tekintheti meg.

Egyéni .NET-tevékenységeket is létrehozhat az adatáthelyezéshez és az adatfeldolgozáshoz saját logikával. Ezeket a tevékenységeket HDInsight-fürtökön vagy virtuális gépek batch-készletén is futtathatja. A Batch használatakor beállíthatja a készletet úgy, hogy az Ön által megadott képlet alapján automatikusan méretezést (virtuális gépeket adjon hozzá vagy távolítson el a munkaterhelés alapján).     

## <a name="architecture-of-a-sample-solution"></a>Egy példa megoldás architektúrája
  A cikkben ismertetett architektúra egy egyszerű megoldás. Az összetett forgatókönyvek, például a pénzügyi szolgáltatások, a képfeldolgozás és-megjelenítés, valamint a genomikai elemzések esetében is relevánsak.

Az ábra azt szemlélteti, hogy Data Factory hogyan hangolja össze az adatáthelyezést és a feldolgozást. Azt is bemutatja, hogyan dolgozza fel párhuzamosan a Batch az adatfeldolgozást. Töltse le és nyomtassa ki a diagramot az egyszerű hivatkozáshoz (11 x 17 hüvelyk vagy a3 méret). Ha a diagramot szeretné elérni, hogy ki tudja nyomtatni, tekintse meg a [HPC és az adatelőkészítés a Batch és a Data Factory használatával](https://go.microsoft.com/fwlink/?LinkId=717686)című témakört.

[![Nagy léptékű adatfeldolgozási diagram](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Az alábbi lista a folyamat alapvető lépéseit ismerteti. A megoldás a teljes körű megoldás létrehozásához szükséges kódot és magyarázatokat tartalmaz.

* **Konfigurálja a Batch-t a számítási csomópontok (VM-EK) készletével.** Megadhatja a csomópontok számát és az egyes csomópontok méretét.

* **Hozzon létre egy olyan Data Factory-példányt** , amely blob Storage-t, a Batch számítási szolgáltatást, a bemeneti/kimeneti adatokat, valamint egy olyan munkafolyamatot/folyamatot, amely az adatokat áthelyező és átalakító tevékenységekkel van konfigurálva.

* **Hozzon létre egy egyéni .NET-tevékenységet a Data Factoryi folyamatban.** A tevékenység a Batch-készleten futó felhasználói kód.

* **Nagy mennyiségű bemeneti adat tárolása blobként az Azure Storage-ban.** Az adatértékek logikai szeletekre vannak osztva (általában idő szerint).

* **Data Factory másolja a párhuzamosan feldolgozott, a másodlagos helyre feldolgozható adatlemezeket** .

* **Data Factory futtatja az egyéni tevékenységet a Batch által lefoglalt készlet használatával.** A Data Factory egyszerre is futtathat tevékenységeket. Minden tevékenység egy adatszeletet dolgoz fel. Az eredményeket a rendszer a tárolóban tárolja.

* **Data Factory a végső eredményeket harmadik helyre helyezi át,** vagy egy alkalmazáson keresztül történő terjesztésre, vagy más eszközök általi további feldolgozásra.

## <a name="implementation-of-the-sample-solution"></a>A minta megoldás implementálása
A minta megoldás szándékosan egyszerű. A szolgáltatás célja, hogy bemutassa a Data Factory és a Batch együttes használatát az adatkészletek feldolgozásához. A megoldás megszámolja a "Microsoft" keresési kifejezés előfordulásainak számát az idősorozatokban rendezett bemeneti fájlokban. Ezután kiírja a darabszámot a kimeneti fájlok számára.

**Idő:** Ha már ismeri az Azure, a Data Factory és a Batch alapjait, és elvégezte a következő előfeltételeket, ez a megoldás egy-két órát vesz igénybe.

### <a name="prerequisites"></a>Előfeltételek
#### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, gyorsan létrehozhat egy ingyenes próbaverziós fiókot. További információ: [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-fiók
Az ebben az oktatóanyagban tárolt adattárolási fiók használatával tárolhatja az adattárakat. Ha nem rendelkezik Storage-fiókkal, tekintse meg [a Storage-fiók létrehozása](../../storage/common/storage-account-create.md)című témakört. A minta megoldás blob Storage-t használ.

#### <a name="azure-batch-account"></a>Azure Batch-fiók
Hozzon létre egy batch-fiókot a [Azure Portal](https://portal.azure.com/)használatával. További információ: [Batch-fiók létrehozása és kezelése](../../batch/batch-account-create-portal.md). Jegyezze fel a Batch-fiók nevét és a fiók kulcsát. A [New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) parancsmaggal létrehozhat egy batch-fiókot is. A parancsmag használatáról a következő témakörben talál útmutatást: a [Batch PowerShell-parancsmagok használatának első](../../batch/batch-powershell-cmdlets-get-started.md)lépései.

A minta megoldás a Batch (egy adatfeldolgozó folyamaton keresztüli) használatával párhuzamosan dolgozza fel az adatok mennyiségét a számítási csomópontok (felügyelt virtuális gépek) készletén.

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch virtuális gépek készlete
Hozzon létre egy batch-készletet legalább két számítási csomóponttal.

1. A [Azure Portal](https://portal.azure.com)válassza a bal oldali menü **Tallózás** elemét, majd válassza a **Batch-fiókok** lehetőséget.

1. Válassza ki a Batch-fiókot a **Batch-fiók** panel megnyitásához.

1. Válassza ki a **készletek** csempét.

1. A **készletek** panelen válassza a **Hozzáadás** gombot az eszköztáron a készlet hozzáadásához.

   a. Adja meg a készlet azonosítóját (**készlet azonosítója**). Jegyezze fel a készlet AZONOSÍTÓját. Szüksége lesz rá, amikor létrehozza a adatfeldolgozó megoldást.

   b. A **Windows Server 2012 R2** értéket kell megadnia az **operációs rendszer családjának** beállításához.

   c. Válassza ki a **csomópontok díjszabási szintjét**.

   d. A **cél dedikált** beállítás értékeként adja meg a **2** értéket.

   e. Adja meg a **2** értéket a **maximális feladatokhoz a csomópontok száma** beállításnál.

   f. A készlet létrehozásához kattintson **az OK gombra** .

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
[Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) vagy [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (a ClumsyLeaf szoftverből) használatával megvizsgálhatja és megváltoztathatja a tárolási projektjeiben lévő adatok vizsgálatát és módosítását. A felhőben üzemeltetett alkalmazások naplófájljaiban is ellenőrizheti és módosíthatja az adatait.

1. Hozzon létre egy **mycontainer** nevű tárolót privát hozzáféréssel (névtelen hozzáférés nélkül).

1. Ha CloudXplorer használ, hozzon létre mappákat és almappákat a következő szerkezettel:

   ![Mappa és almappa szerkezete](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` és a `outputfolder` legfelső szintű mappák a ben `mycontainer` . A `inputfolder` mappa almappákkal rendelkezik, és dátum-idő bélyegzőket tartalmaz (éééé-hh-nn-hh).

   Ha Storage Explorer használ, a következő lépésben feltölti a fájlokat a következő nevekkel: `inputfolder/2015-11-16-00/file.txt` , `inputfolder/2015-11-16-01/file.txt` és így tovább. Ez a lépés automatikusan létrehozza a mappákat.

1. Hozzon létre egy szövegfájlt **file.txt** a gépen a **Microsoft** kulcsszóval rendelkező tartalommal. Ilyen például az "egyéni tevékenység tesztelése Microsoft test Custom Activity Microsoft".

1. Töltse fel a fájlt a következő bemeneti mappákba a blob Storage-ban:

   ![Bemeneti mappák](./media/data-factory-data-processing-using-batch/image4.png)

   Ha Storage Explorer használ, töltse fel a **file.txt** fájlt a **mycontainer**. A blob másolatának létrehozásához válassza az eszköztár **Másolás** elemét. A **blob másolása** párbeszédpanelen módosítsa a **cél blob nevét** a következőre: `inputfolder/2015-11-16-00/file.txt` . Ismételje meg ezt a lépést a (z),,, `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt` `inputfolder/2015-11-16-03/file.txt` `inputfolder/2015-11-16-04/file.txt` stb. létrehozásához. Ez a művelet automatikusan létrehozza a mappákat.

1. Hozzon létre egy nevű másik tárolót `customactivitycontainer` . Töltse fel az egyéni tevékenység zip-fájlját a tárolóba.

#### <a name="visual-studio"></a>Visual Studio
Telepítse a Visual Studio 2012-es vagy újabb verzióját, és hozzon létre egy egyéni batch-tevékenységet, amelyet az adatfeldolgozó megoldásban kíván használni.

### <a name="high-level-steps-to-create-the-solution"></a>A megoldás létrehozásának magas szintű lépései
1. Hozzon létre egy egyéni tevékenységet, amely tartalmazza az adatfeldolgozási logikát.

1. Hozzon létre egy olyan adatelőállítót, amely az egyéni tevékenységet használja.

### <a name="create-the-custom-activity"></a>Egyéni tevékenység létrehozása
Az egyéni adatfeldolgozó tevékenység a mintaoldat szíve. A minta megoldás a Batch használatával futtatja az egyéni tevékenységet. További információ az egyéni tevékenységek fejlesztéséről és a adat-előállító folyamatokban való használatáról: [egyéni tevékenységek használata a adat-előállító folyamatokban](data-factory-use-custom-activities.md).

Egy olyan .NET-alapú egyéni tevékenység létrehozásához, amelyet egy adatfeldolgozó-folyamatban használhat, létrehoz egy .NET-osztály-függvénytár-projektet egy olyan osztállyal, amely megvalósítja a IDotNetActivity felületet. Ez az illesztőfelület csak egy metódussal rendelkezik: EXECUTE. Itt látható a metódus aláírása:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

A metódusnak néhány kulcsfontosságú összetevője van, amelyeket meg kell értenie:

* A metódus négy paramétert vesz igénybe:

  * **linkedServices**. Ez a paraméter a társított szolgáltatások olyan enumerable listája, amely a bemeneti/kimeneti adatforrásokat (például blob Storage) a adat-előállítóhoz kapcsolja. Ebben a példában egyetlen, az Azure Storage típusú, bemenethez és kimenethez használt társított szolgáltatás szerepel.
  * **adatkészletek**. Ez a paraméter az adatkészletek enumerable listája. Ezzel a paraméterrel beolvashatja a bemeneti és kimeneti adatkészletek által meghatározott helyet és sémákat.
  * **tevékenység**. Ez a paraméter az aktuális számítási entitást jelöli. Ebben az esetben ez egy batch szolgáltatás.
  * **naplózó**. A naplózó használatával olyan hibakeresési megjegyzéseket írhat, amelyek a folyamat "felhasználó" naplójába kerülnek.
* A metódus egy olyan szótárt ad vissza, amely az egyéni tevékenységek a jövőben történő láncolására használható. Ez a szolgáltatás még nincs megvalósítva, ezért csak üres szótárt ad vissza a metódusból.

#### <a name="procedure-create-the-custom-activity"></a>Eljárás: az egyéni tevékenység létrehozása
1. .NET-osztálybeli függvénytár-projekt létrehozása a Visual Studióban.

   a. Indítsa el a Visual Studio 2012/2013/2015 alkalmazást.

   b. Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

   c. Bontsa ki a **sablonok** csomópontot, majd válassza a **Visual C \#** elemet. Ebben az útmutatóban C \# -t használ, de bármilyen .NET-nyelvet használhat az egyéni tevékenység fejlesztéséhez.

   d. A jobb oldalon válassza ki az **osztály könyvtára** elemet a projekttípus listából.

   e. Adja  meg a MyDotNetActivity **nevet**.

   f. Válassza a **C: \\ ADF** lehetőséget a **helyhez**. Ha nem létezik, hozza létre az **ADF** mappát.

   : A projekt létrehozásához válassza az **OK** lehetőséget.

1. Válassza az **Eszközök** > **NuGet-csomagkezelő** > **Package Csomagkezelő konzol** elemet.

1. A Package Manager konzolon hajtsa végre a következő parancsot a Microsoft. Azure. Management. DataFactories importálásához:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importálja az **Azure Storage** NuGet-csomagot a projektbe. Erre a csomagra azért van szükség, mert ebben a példában a Blob Storage API-t használja:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adja hozzá a következő using direktívák használatával a forrásfájlban a projektben:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Módosítsa a névtér nevét a **MyDotNetActivityNS** értékre.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Módosítsa az osztály nevét a **MyDotNetActivity** értékre, és származtatja a **IDotNetActivity** felületről az alábbiak szerint:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementálja (adja hozzá) a **IDotNetActivity** felület **Execute** metódusát a **MyDotNetActivity** osztályhoz. Másolja az alábbi mintakód a metódusba. Az ebben a metódusban használt logika magyarázatát lásd: [metódus végrehajtása](#execute-method) szakasz.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
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

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Adja hozzá a következő segítő metódusokat a osztályhoz. Ezeket a metódusokat a **végrehajtás** módszere hívja meg. A legfontosabb, hogy a **számítás** módszer elkülöníti az egyes blobokon keresztül ismétlődő kódot.

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

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

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

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    A GetFolderPath metódus Visszaadja annak a mappának az elérési útját, amelyre az adatkészlet mutat, és a GetFileName metódus Visszaadja annak a blobnak/fájlnak a nevét, amelyre az adatkészlet mutat.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    A számítás módszer kiszámítja a "Microsoft" kulcsszó példányainak számát a bemeneti fájlokban (Blobok a mappában). A "Microsoft" keresési kifejezés a kódban nem rögzített.

1. A projekt fordítása. Válassza a **Létrehozás** lehetőséget a menüben, majd válassza a **megoldás létrehozása** lehetőséget.

1. Indítsa el a Windows Intézőt, és nyissa meg a **bin \\ Debug** vagy a **bin \\ Release** mappát. A mappa választása a Build típusától függ.

1. Hozzon létre egy zip-fájl **MyDotNetActivity.zip** , amely a **\\ bin \\ Debug** mappában található összes bináris fájlt tartalmazza. Előfordulhat, hogy fel szeretné venni a MyDotNetActivity. a **PDB** -fájlt, hogy további részleteket kapjon, például a forráskódban lévő sorszámot, amely a hiba bekövetkezésekor okozta a hibát.

   ![A bin\Debug mappák listája](./media/data-factory-data-processing-using-batch/image5.png)

1. Töltse fel **MyDotNetActivity.zip** blobként a blob Storage-tárolóba, `customactivitycontainer` amelyet a ADFTutorialDataFactory StorageLinkedService társított szolgáltatás használ. Ha még nem létezik, hozza létre a BLOB-tárolót `customactivitycontainer` .

#### <a name="execute-method"></a>Végrehajtási metódus
Ez a szakasz további részleteket tartalmaz a kód végrehajtásához a végrehajtási metódusban.

1. A bemeneti gyűjteményen keresztül megismételni kívánt tagok a [Microsoft. WindowsAzure. Storage. blob](/java/api/com.microsoft.azure.storage.blob) névtérben találhatók. A blob-gyűjteményen keresztüli iterációhoz a **BlobContinuationToken** osztályt kell használnia. Lényegében egy do-while hurkot kell használnia a tokenhez, mint a hurok kilépésének mechanizmusát. További információ: [blob Storage használata a .net-ről](../../storage/blobs/storage-quickstart-blobs-dotnet.md). Itt látható egy alapszintű hurok:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   További információkért tekintse meg a [ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) metódus dokumentációját.

1. A Blobok készletén végzett munkavégzéshez szükséges kód logikailag a do-while cikluson belülre esik. A **végrehajtás** metódusban a do-while hurok a Blobok listáját egy **kiszámított** metódusba továbbítja. A metódus egy **kimenet** nevű karakterlánc-változót ad vissza, amely azt eredményezi, hogy a rendszer megismétli a szegmens összes blobját.

   Visszaadja a "Microsoft" keresési kifejezés előfordulásainak számát a **számítás** metódusnak átadott blobban.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. A **kiszámítási** módszer befejezése után egy új blobba kell írni. Minden feldolgozott blob esetében egy új blob írható az eredményekkel. Egy új blobba való íráshoz először keresse meg a kimeneti adatkészletet.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. A kód a **GetFolderPath** segítő metódust is meghívja a mappa elérési útjának (a tároló neve) lekéréséhez.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   A GetFolderPath metódus az adatkészlet objektumot egy AzureBlobDataSet, amely egy FolderPath nevű tulajdonsággal rendelkezik.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. A kód meghívja a **GetFileName** metódust a fájl nevének lekéréséhez (blob neve). A kód hasonlít a mappa elérési útjának beolvasására használt korábbi kódhoz.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. A fájl nevét egy URI-objektum létrehozásával kell megírni. Az URI-konstruktor a **BlobEndpoint** tulajdonság használatával adja vissza a tároló nevét. A rendszer hozzáadja a mappa elérési útját és fájlnevét a kimeneti blob URI létrehozásához.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. A fájl nevének megírása után a kimeneti karakterláncot a **számítás** metódusból egy új blobba írhatja:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Az adatelőállító létrehozása
Az [egyéni tevékenység létrehozása](#create-the-custom-activity) szakaszban létrehozott egy egyéni tevékenységet, és feltöltötte a bináris fájlokat és a PDB-fájlt egy blob-tárolóba. Ebben a szakaszban egy adatelőállítót hoz létre egy olyan folyamattal, amely az egyéni tevékenységet használja.

Az egyéni tevékenység bemeneti adatkészlete a blob Storage-ban lévő bemeneti mappában () lévő blobokat (fájlokat) jelöli `mycontainer\\inputfolder` . A tevékenység kimeneti adatkészlete a blob Storage kimeneti mappájában () lévő kimeneti blobokat jelöli `mycontainer\\outputfolder` .

Egy vagy több fájl eldobása a bemeneti mappákba:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Például dobjon egy fájlt (file.txt) a következő tartalommal az egyes mappákba:

```
test custom activity Microsoft test custom activity Microsoft
```

Az egyes bemeneti mappák a adat-előállítóban lévő szeleteknek felelnek meg, még akkor is, ha a mappában kettő vagy több fájl található. Ha az egyes szeleteket a folyamat dolgozza fel, az egyéni tevékenység megismétli az adott szelet bemeneti mappájában található összes blobot.

Öt kimeneti fájl jelenik meg ugyanazzal a tartalommal. Például az 2015-11-16-00 mappában található fájl feldolgozásának kimeneti fájlja a következő tartalommal rendelkezik:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Ha több fájlt (file.txt, file2.txt, file3.txt) húz el ugyanazzal a tartalommal a bemeneti mappába, a kimeneti fájlban a következő tartalom jelenik meg. Az egyes mappák (2015-11-16-00 stb.) egy szeletnek felelnek meg ebben a mintában annak ellenére, hogy a mappában több bemeneti fájl található.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

A kimeneti fájl három sorral rendelkezik, egyet a szelethez társított mappában lévő minden bemeneti fájlhoz (blob) (2015-11-16-00).

Létrejön egy feladat minden egyes tevékenység futtatásához. Ebben a példában csak egy tevékenység található a folyamatban. Ha a folyamat egy szeletet dolgoz fel, az egyéni tevékenység a kötegben fut a szelet feldolgozásához. Mivel öt szelet van (minden szelet több blobtal vagy fájllal is rendelkezhet), öt feladat jön létre a Batchben. Ha egy feladat a Batch szolgáltatásban fut, akkor az az egyéni tevékenység, amely fut.

A következő útmutató további részleteket tartalmaz.

#### <a name="step-1-create-the-data-factory"></a>1. lépés: az adatelőállító létrehozása
1. Miután bejelentkezett a [Azure Portalba](https://portal.azure.com/), hajtsa végre a következő lépéseket:

   a. Válassza az **új** lehetőséget a bal oldali menüben.

   b. Az **új** panelen válassza a **adatok és Analitika** lehetőséget.

   c. Válassza a **Data Factory** lehetőséget az **adatelemzés** panelen.

1. Az **új adatgyár** panelen írja be a **CustomActivityFactory** nevet. Az adat-előállító nevének globálisan egyedinek kell lennie. Ha "a CustomActivityFactory neve nem érhető el" hibaüzenet jelenik meg, módosítsa az adatelőállító nevét. Használja például a yournameCustomActivityFactory, és hozza létre újra az adatelőállítót.

1. Válassza ki az **ERŐFORRÁSCSOPORT nevét**, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.

1. Ellenőrizze, hogy helyesek-e az előfizetés és a régió, ahová az adatelőállítót létre szeretné hozni.

1. Válassza a **Létrehozás** lehetőséget az **új adatgyár** panelen.

1. A rendszer létrehoz egy adatelőállítót a portál irányítópultján.

1. Miután a rendszer sikeresen létrehozta az adatelőállítót, megjelenik az **adatelőállító** oldal, amely megjeleníti az adatelőállító tartalmát.

   ![Az adatfeldolgozó lap](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2. lépés: társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat kapcsolnak össze egy adatgyárban. Ebben a lépésben a Storage-fiókot és a Batch-fiókot a saját adatgyárához kapcsolja.

#### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
1. Válassza ki a **Szerző és üzembe helyezés** csempét a **CustomActivityFactory**-hez készült **adatfeldolgozó** panelen. Megjelenik a Data Factory szerkesztő.

1. Válassza a parancssáv **új adattár** elemét, és válassza az **Azure Storage lehetőséget.** Megjelenik a Storage társított szolgáltatás létrehozásához használt JSON-szkript a szerkesztőben.

   ![Új adattár](./media/data-factory-data-processing-using-batch/image7.png)

1. Cserélje le az **account name** kifejezést a tárfiókja nevére. Az **account key** kifejezést cserélje le a tárfiók hozzáférési kulcsára. A Storage-hozzáférési kulcs beszerzéséről a Storage- [fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben olvashat bővebben.

1. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

   ![Üzembe helyezés](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Azure Batch társított szolgáltatás létrehozása
Ebben a lépésben létrehoz egy társított szolgáltatást a Batch-fiókjához, amelyet a rendszer az adatfeldolgozó egyéni tevékenységének futtatására használ.

1. Válassza a parancssáv **új számítás** elemét, és válassza a **Azure batch lehetőséget.** Megjelenik egy batch társított szolgáltatás létrehozásához használt JSON-szkript a szerkesztőben.

1. A JSON-parancsfájlban:

   a. Cserélje le a **fióknevet** a Batch-fiók nevére.

   b. Cserélje le a **hozzáférési kulcsot** a Batch-fiók hozzáférési kulcsára.

   c. Adja meg a készlet AZONOSÍTÓját a **poolName** tulajdonsághoz. Ebben a tulajdonságban megadhatja a készlet nevét vagy a készlet AZONOSÍTÓját.

   d. Adja meg a Batch URI-JÁT a **batchUri** JSON tulajdonságához.

      > [!IMPORTANT]
      > A **Batch-fiók** panel URL-címe a következő formátumban van: \<accountname\> . \<region\> . batch.azure.com. A JSON-parancsfájl **batchUri** tulajdonságához el kell távolítania a "accountname" a88. * * az URL-címről. Például: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Batch-fiók panel](./media/data-factory-data-processing-using-batch/image9.png)

      A **poolName** tulajdonsághoz a készlet neve helyett a készlet azonosítóját is megadhatja.

      > [!NOTE]
      > A Data Factory szolgáltatás nem támogatja az igény szerinti beállítást a Batch esetében, mivel az a HDInsight esetében működik. Csak a saját batch-készletét használhatja egy adatelőállítóban.
      >
      >

   e. **StorageLinkedService** megadása a **linkedServiceName** tulajdonsághoz. Ezt a társított szolgáltatást az előző lépésben hozta létre. Ez a tároló a fájlok és naplók előkészítési részeként használatos.

1. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

#### <a name="step-3-create-datasets"></a>3. lépés: adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre a bemeneti és kimeneti adatok ábrázolásához.

#### <a name="create-the-input-dataset"></a>A bemeneti adatkészlet létrehozása
1. A Data Factory szerkesztőben kattintson az **új adatkészlet** gombra az eszköztáron. Válassza ki az **Azure Blob Storage** -t a legördülő listából.

1. Cserélje le a JSON-szkriptet a jobb oldali ablaktáblán a következő JSON-kódrészletre:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Az útmutató későbbi részében létrehoz egy folyamatot a 2015-11 – 16T00:00:00Z és a befejezési idő 2015-11-16T05:00:00Z. Az adatok óránkénti előállítására van ütemezve, így öt bemeneti/kimeneti szelet van ( **00**: 00:00 – \> **05**: 00:00 között).

    A bemeneti adatkészlet **gyakorisága** és **intervalluma** **óra** és **1** értékre van állítva, ami azt jelenti, hogy a bemeneti szelet óránként elérhető.

    Az egyes szeletek kezdő időpontját a **SliceStart** rendszerváltozó jelöli az előző JSON-kódrészletben. Itt láthatók az egyes szeletek kezdő időpontjai.

    | **Szelet** | **Kezdési idő**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T **00**: 00:00 |
    | 2         | 2015-11-16T **01**: 00:00 |
    | 3         | 2015-11-16T **02**: 00:00 |
    | 4         | 2015-11-16T **03**: 00:00 |
    | 5         | 2015-11-16T **04**: 00:00 |

    A **folderPath** a szelet kezdési időpontja (**SliceStart**) év, hónap, nap és óra része alapján számítjuk ki. Itt láthatja, hogy egy bemeneti mappa hogyan van leképezve egy szeletre.

    | **Szelet** | **Kezdési idő**          | **Bemeneti mappa**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16 –**00** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16 –**01** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16 –**02** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16 –**03** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16 –**04** |

1. Az eszköztáron válassza a **telepítés** lehetőséget a **InputDataset** tábla létrehozásához és üzembe helyezéséhez.

#### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
Ebben a lépésben egy másik AzureBlob típusú adatkészletet hoz létre, amely a kimeneti adatokat jelöli.

1. A Data Factory szerkesztőben kattintson az **új adatkészlet** gombra az eszköztáron. Válassza ki az **Azure Blob Storage** -t a legördülő listából.

1. Cserélje le a JSON-szkriptet a jobb oldali ablaktáblán a következő JSON-kódrészletre:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Minden bemeneti szelethez kimeneti blob/fájl jön létre. Az egyes szeletekhez tartozó kimeneti fájl nevét itt találja. Az összes kimeneti fájl egy kimeneti mappában jön létre `mycontainer\\outputfolder` .

    | **Szelet** | **Kezdési idő**          | **Kimeneti fájl**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16 –**00.txt** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16 –**01.txt** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16 –**02.txt** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16 –**03.txt** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16 –**04.txt** |

    Ne feledje, hogy a bemeneti mappában lévő összes fájl (például 2015-11-16-00) egy szelet részét képezi a 2015-11-16-00 kezdési idővel. A szelet feldolgozásakor az egyéni tevékenység átvizsgálja az egyes fájlokat, és létrehoz egy sort a kimeneti fájlban a "Microsoft" keresési kifejezés előfordulásainak számával. Ha a 2015-11-16-00 mappában három fájl található, a kimeneti fájlban három sor van 2015-11-16-00.txt.

1. A **OutputDataset** létrehozásához és üzembe helyezéséhez válassza az eszköztár **üzembe helyezés** elemét.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>4. lépés: a folyamat létrehozása és futtatása egyéni tevékenységgel
Ebben a lépésben létrehoz egy folyamatot egy tevékenységgel, a korábban létrehozott egyéni tevékenységgel.

> [!IMPORTANT]
> Ha a blob-tárolóban nem töltött fel **file.txt** a bemeneti mappákba, ezt a folyamat létrehozása előtt tegye meg. A **ispaused fogalmak** tulajdonság a folyamat JSON-fájljában hamis értékre van állítva, így a folyamat azonnal fut, mert a **kezdő** dátum a múltban van.
>
>

1. A Data Factory szerkesztőben válassza a parancssáv **új folyamat** elemét. Ha nem látja a parancsot, válassza a három pont szimbólumot a megjelenítéshez.

1. Cserélje le a JSON-szkriptet a jobb oldali ablaktáblán a következő JSON-kódrészletre:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
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
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Vegye figyelembe a következő szempontokat:

   * Csak egy tevékenység van a folyamatban, és a típusa **DotNetActivity**.
   * A **AssemblyName** értéke a DLL- **MyDotNetActivity.dll** neve.
   * A **BelépésiPont** értéke **MyDotNetActivityNS. MyDotNetActivity**. Alapvetően \<namespace\> .\<classname\> a kódban.
   * A **PackageLinkedService** értéke **StorageLinkedService**, amely az egyéni tevékenység zip-fájlját tartalmazó blob Storage-ra mutat. Ha különböző tárolási fiókokat használ a bemeneti/kimeneti fájlokhoz és az egyéni tevékenység zip-fájljához, létre kell hoznia egy másik Storage-beli társított szolgáltatást. Ez a cikk azt feltételezi, hogy ugyanazt a Storage-fiókot használja.
   * A **PackageFile** értéke **customactivitycontainer/MyDotNetActivity.zip**. A formátuma \<containerforthezip\> / \<nameofthezip.zip\> .
   * Az egyéni tevékenység kimenetként a bemeneti és a **OutputDataset** **InputDataset** veszi át.
   * Az egyéni tevékenység **linkedServiceName** tulajdonsága a **AzureBatchLinkedService**-ra mutat, ami azt jelzi, Data Factory, hogy az egyéni tevékenységnek futnia kell a Batch szolgáltatásban.
   * A **Egyidejűség** beállítása fontos. Ha az alapértelmezett értéket használja (1), akkor is, ha a Batch-készletben kettő vagy több számítási csomópont van, akkor a szeletek feldolgozása egy másik után történik. Ezért nem használja ki a Batch párhuzamos feldolgozási funkciójának előnyeit. Ha nagyobb értékre állítja a **párhuzamosságot** , mondjuk 2, azt jelenti, hogy két szelet (amely a Batch két feladatának felel meg) egyszerre is feldolgozható. Ebben az esetben a rendszer a Batch-készletben lévő virtuális gépeket is használja. Állítsa be a párhuzamossági tulajdonságot megfelelően.
   * Alapértelmezés szerint a virtuális gépen csak egy feladat (szelet) kerül végrehajtásra. Alapértelmezés szerint a **virtuális gépek maximális száma** 1 értékre van állítva egy batch-készletnél. Az előfeltételek részeként létrehozott egy készletet, amelynek a tulajdonsága 2 értékre van beállítva. Ezért a virtuális gépen két adatfeldolgozó szelet futhat egyszerre.
     - A **ispaused fogalmak** tulajdonság alapértelmezés szerint hamis értékre van állítva. Ebben a példában a folyamat azonnal fut, mert a szeletek a múltban kezdődnek. Ezt a tulajdonságot **igaz** értékre állíthatja, ha szüneteltetni szeretné a folyamatot, és visszaállítja a **false (hamis) értéket** az újraindításhoz.
     -   A **kezdő** és a **záró** időpont öt óra egymástól. A szeletek óránként jönnek létre, így a folyamat öt szeletet állít elő.

1. A folyamat üzembe helyezéséhez kattintson a parancssor **Üzembe helyezés** elemére.

#### <a name="step-5-test-the-pipeline"></a>5. lépés: a folyamat tesztelése
Ebben a lépésben teszteli a folyamatot a fájlok bemeneti mappákba való eldobásával. Első lépésként tesztelje a folyamatot az egyes bemeneti mappák egy fájljával.

1. A Azure Portal **adatgyár** paneljén válassza a **diagram** elemet.

   ![Ábra](./media/data-factory-data-processing-using-batch/image10.png)

1. A **diagram** nézetben kattintson duplán a bemeneti adatkészlet **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. A **InputDataset** panel minden öt szeletre készen jelenik meg. Figyelje meg, hogy a **szelet kezdő időpontja** és a **szelet befejezési időpontja** minden szeletnél.

   ![A bemeneti szelet kezdési és befejezési időpontja](./media/data-factory-data-processing-using-batch/image12.png)

1. A **diagram** nézetben válassza a **OutputDataset** lehetőséget.

1. Az öt kimeneti szelet **kész** állapotban jelenik meg, ha létrejöttek.

   ![Kimeneti szelet kezdő és befejező időpontja](./media/data-factory-data-processing-using-batch/image13.png)

1. A portálon megtekintheti a szeletekhez társított feladatokat, és megtekintheti, hogy az egyes szeletek milyen virtuális gépen futottak. További információ: [Data Factory és batch Integration](#data-factory-and-batch-integration) szakasz.

1. A kimeneti fájlok megjelennek a `mycontainer` `outputfolder` blob Storage-ban.

   ![Kimeneti fájlok a Storage-ban](./media/data-factory-data-processing-using-batch/image15.png)

   Öt kimeneti fájl szerepel a felsorolásban, egyet az egyes bemeneti szeletekhez. A kimeneti fájlok mindegyike a következő kimenethez hasonló tartalommal rendelkezik:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Az alábbi ábra azt szemlélteti, hogyan képezhetők le a adat-előállító a Batch-feladatok feladataihoz. Ebben a példában egy szeletnek csak egy futtatása van.

   ![Szelet-hozzárendelési diagram](./media/data-factory-data-processing-using-batch/image16.png)

1. Most próbálkozzon több fájllal egy mappában. Hozza létre a fájlokat **file2.txt**, **file3.txt**, **file4.txt** és **file5.txt** ugyanazzal a tartalommal, mint a **2015-11-06-01** mappában található file.txt.

1. A kimeneti mappában törölje a **2015-11-16-01.txt** kimeneti fájlt.

1. A **OutputDataset** panelen kattintson a jobb gombbal arra a szeletre, amelyben a **szelet kezdési időpontja** **11/16/2015 01:00:00**-re van állítva. Válassza a **Futtatás** lehetőséget a szelet újrafuttatásához/újrafeldolgozásához. A szelet most már öt fájlból áll egy fájl helyett.

    ![Futtatás](./media/data-factory-data-processing-using-batch/image17.png)

1. A szelet futtatása után az állapota **elkészült**, ellenőrizze a szelet kimeneti fájljában lévő tartalmat (**2015-11-16-01.txt**). A kimeneti fájl megjelenik a `mycontainer` `outputfolder` blob Storage-ban. A szelet minden fájljához meg kell adni egy sort.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Ha nem törölte a kimeneti fájlt 2015-11-16-01.txt az öt bemeneti fájl megkezdése előtt, egy sor jelenik meg az előző szeletből és az aktuális szelet futtatásának öt sorából. Alapértelmezés szerint a rendszer hozzáfűzi a tartalmat a kimeneti fájlhoz, ha az már létezik.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory és batch-integráció
A Data Factory szolgáltatás létrehoz egy feladatot a Batchben a névvel `adf-poolname:job-xxx` .

![Kötegelt feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

A feladatban a szeletek minden egyes tevékenységéhez létrejön egy feladat. Ha 10 szelet készen áll a feldolgozásra, 10 feladat jön létre a feladatban. Ha több számítási csomóponttal rendelkezik a készletben, több párhuzamosan futó szelettel is rendelkezhet. Ha a számítási csomópontok maximális száma nagyobb, mint egy, akkor több szelet is futhat ugyanazon a számítási feladaton.

Ebben a példában öt szelet van, így öt feladat van a Batchben. Ha a **Egyidejűség** értéke **5** , a folyamat JSON-je a adat-előállítóban, és a **virtuális gépek maximális száma** **2** értékre van állítva a Batch-készletben **2** virtuális géppel, a feladatok gyorsan futnak. (A feladatok kezdési és befejezési idejének megadásához.)

A portálon megtekintheti a Batch-feladatot és annak a szeletekhez társított feladatait, és megtekintheti, hogy az egyes szeletek milyen virtuális gépen futottak.

![Kötegelt feladat feladatai](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Hibakeresés a folyamaton
A hibakeresés néhány alapvető módszerből áll.

1. Ha a bemeneti szelet nem **üzemkész** állapotra van állítva, ellenőrizze, hogy helyesek-e a bemeneti mappa szerkezete, és hogy file.txt létezik-e a bemeneti mappákban.

   ![Bemeneti mappa szerkezete](./media/data-factory-data-processing-using-batch/image3.png)

1. Az egyéni tevékenység **végrehajtás** metódusában a **IActivityLogger** objektum használatával naplózhatja azokat az információkat, amelyek segítenek a hibák elhárításában. A naplózott üzenetek megjelennek a User \_ 0. log fájlban.

   A **OutputDataset** panelen válassza ki a szeletet az adott szelethez tartozó **adatszelet** panel megjelenítéséhez. A **tevékenység futtatása** területen megjelenik egy tevékenység futtatása a szelethez. Ha a parancssáv **Futtatás** parancsát választja, egy másik tevékenység futtatását is elindíthatja ugyanahhoz a szelethez.

   Amikor kiválasztja a tevékenység futtatását, a **tevékenység futtatása részletek panel** jelenik meg a naplófájlok listájával. A naplózott üzenetek a \_ 0. log fájlban láthatók. Hiba esetén három tevékenység fut, mert az újrapróbálkozások száma a folyamat/tevékenység JSON-ben 3 értékre van állítva. Amikor kiválasztja a tevékenység futtatását, megjelenik a naplófájlok, amelyeket a hiba megoldása érdekében áttekinthet.

   ![OutputDataset és adatszeletek pengéi](./media/data-factory-data-processing-using-batch/image18.png)

   A naplófájlok listájában válassza a **User-0. log** fájlt. A jobb oldali panelen megjelennek a **IActivityLogger. Write** metódus használatának eredményei.

   ![Tevékenység futtatási részletei panel](./media/data-factory-data-processing-using-batch/image19.png)

   Az System-0. log naplófájlban keresse meg a rendszerhibákat és a kivételeket.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Adja meg a **PDB** -fájlt a zip-fájlban, hogy a hiba részletei olyan információkkal rendelkezzenek, mint a hívási verem, ha hiba történik.

1. Az egyéni tevékenységhez tartozó zip-fájlban lévő összes fájlnak a legfelső szinten kell lennie, almappák nélkül.

   ![Egyéni tevékenység zip-fájljának listája](./media/data-factory-data-processing-using-batch/image20.png)

1. Győződjön meg arról, hogy a **assemblyName** (MyDotNetActivity.dll), a **BelépésiPont** (MyDotNetActivityNS. MyDotNetActivity), a **packageFile** (Customactivitycontainer/MyDotNetActivity.zip) és a **packageLinkedService** (a zip-fájlt tartalmazó blob Storage-ra kell mutatnia) a megfelelő értékre van állítva.

1. Ha kijavított egy hibát, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre a **OutputDataset** panelen, és válassza a **Futtatás** lehetőséget.

   ![OutputDataset panel futtatási beállítása](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > A tároló a blob Storage nevű tárolóban található `adfjobs` . Ez a tároló nem törlődik automatikusan, de a megoldás tesztelésének befejezése után biztonságosan törölheti. Hasonlóképpen, a Refactory megoldás létrehoz egy nevű batch-feladatot `adf-\<pool ID/name\>:job-0000000001` . Ezt a feladatot a megoldás tesztelése után is törölheti, ha szeretné.
   >
   >
1. Az egyéni tevékenység nem használja a csomag **app.config** fájlját. Ezért ha a kód a konfigurációs fájlból olvassa be a kapcsolatok karakterláncait, nem működik futásidőben. A Batch használata esetén az ajánlott eljárás a Azure Key Vault titkos kulcsainak megtartása. Ezután használjon egy tanúsítványalapú szolgáltatásnevet a kulcstartó védeleméhez, és ossza el a tanúsítványt a Batch-készletbe. A .NET-alapú egyéni tevékenység futtatáskor a Key vaultban elérheti a titkos kulcsokat. Ez az általános megoldás bármilyen típusú titokra méretezhető, nem csak a kapcsolódási karakterláncra.

    Egyszerűbb megkerülő megoldás, de nem ajánlott eljárás. Létrehozhat egy SQL Database társított szolgáltatást a kapcsolati karakterlánc beállításaival. Ezután létrehozhat egy olyan adatkészletet, amely a társított szolgáltatást használja, és az adatkészletet az egyéni .NET-tevékenységhez tartozó dummy bemeneti adatkészletként láncba helyezi. Ezután elérheti a társított szolgáltatás kapcsolati karakterláncát az egyéni tevékenység kódjában. Ez a megoldás a futás közben is működik.  

#### <a name="extend-the-sample"></a>A minta kiterjesztése
Ezt a mintát kiterjesztheti Data Factory és a Batch funkcióinak megismeréséhez. Ha például egy másik időtartományban szeretné feldolgozni a szeleteket, hajtsa végre a következő lépéseket:

1. Adja hozzá a következő almappákat a ben `inputfolder` : 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 és 2015-11-16-09. Helyezzen be bemeneti fájlokat ezekben a mappákba. Módosítsa a folyamat befejezési idejét a verzióról a verzióra `2015-11-16T05:00:00Z` `2015-11-16T10:00:00Z` . A **diagram** nézetben kattintson duplán a **InputDataset** elemre, és ellenőrizze, hogy a bemeneti szeletek készen állnak-e. Kattintson duplán a **OutputDataset** elemre a kimeneti szeletek állapotának megtekintéséhez. Ha **kész** állapotban van, ellenőrizze a kimeneti fájlok kimeneti mappáját.

1. Növelje vagy csökkentse a **Egyidejűség** beállítását, hogy megtudja, hogyan befolyásolja a megoldás teljesítményét, különösen a Batch szolgáltatásban előforduló feldolgozást. További információ a **párhuzamossági** beállításról: "4. lépés: a folyamat létrehozása és futtatása egyéni tevékenységgel".

1. Hozzon létre egy készletet **virtuális gépenként magasabb/alacsonyabb maximális feladatokkal**. A létrehozott új készlet használatához frissítse a Batch társított szolgáltatást a Refactory megoldásban. A **virtuális gépek maximális feladataival** kapcsolatos további információkért tekintse meg a "4. lépés: a folyamat létrehozása és futtatása egyéni tevékenységgel" című témakört.

1. Hozzon létre egy batch-készletet az **autoscale** funkcióval. A számítási csomópontok automatikus méretezése egy batch-készletben az alkalmazás által használt feldolgozási teljesítmény dinamikus beállítása.

    A minta képlet itt a következő viselkedést éri el. A készlet első létrehozásakor egy virtuális géppel kezdődik. A $PendingTasks metrika meghatározza a futó és az aktív (várólistán lévő) állapotú feladatok számát. A képlet megkeresi a függőben lévő feladatok átlagos számát az utolsó 180 másodpercben, és ennek megfelelően beállítja a TargetDedicated. Biztosítja, hogy a TargetDedicated soha ne haladja meg a 25 virtuális gépet. Az új feladatok elküldésekor a készlet automatikusan növekszik. A feladatok elvégzése után a virtuális gépek csak egyszer válnak szabaddá, és az automatikus skálázás csökkenti ezeket a virtuális gépeket. Igény szerint módosíthatja a startingNumberOfVMs és a maxNumberofVMs.

    Autoskálázási képlet:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   További információ: [számítási csomópontok automatikus skálázása egy batch-készletben](../../batch/batch-automatic-scaling.md).

   Ha a készlet az alapértelmezett [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)használja, a Batch szolgáltatás 15 – 30 percet is igénybe vehet a virtuális gép előkészítéséhez az egyéni tevékenység futtatása előtt. Ha a készlet eltérő autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval és 10 percet is igénybe vehet.

1. A minta megoldásban a **végrehajtás** metódus meghívja a **számítás** metódust, amely egy bemeneti adatszeletet dolgoz fel egy kimeneti adatszelet létrehozásához. Megírhatja a saját metódusát a bemeneti adatok feldolgozásához, és lecserélheti a metódus **kiszámítása** hívást a **végrehajtás** metódusban a metódus hívásával.

### <a name="next-steps-consume-the-data"></a>Következő lépések: az adatfelhasználás
Az adatfeldolgozást követően online eszközökkel, például Power BI használhatja azokat. Az alábbi hivatkozások segítséget nyújtanak a Power BI és az Azure-ban való használat megismerésében:

* [Adathalmaz megismerése Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Első lépések a Power BI Desktopban](/power-bi/fundamentals/desktop-getting-started)
* [Power BIban lévő Adatfrissítés](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure és Power BI: alapszintű áttekintés](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Hivatkozások
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [A Data Factory szolgáltatás bemutatása](data-factory-introduction.md)
  * [Ismerkedés a Data Factory](data-factory-build-your-first-pipeline.md)
  * [Egyéni tevékenységek használata egy Data Factory-folyamatban](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [A Batch alapjai](../../azure-sql/database/sql-database-paas-overview.md)
  * [A Batch szolgáltatásainak áttekintése](../../batch/batch-service-workflow-features.md))
  * [Batch-fiók létrehozása és kezelése a Azure Portalban](../../batch/batch-account-create-portal.md)
  * [Ismerkedés a .NET-hez készült batch ügyféloldali kódtáraval](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough