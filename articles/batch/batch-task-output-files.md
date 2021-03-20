---
title: Kimeneti adatokat az Azure Storage-ba a Batch Service API-val
description: Megtudhatja, hogyan használhatja a Batch szolgáltatás API-ját a Batch-feladatok és a feladat-kimeneti adatok Azure Storage-ba való megőrzéséhez.
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936928"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Tevékenységadatok megőrzése az Azure Storage-ban a Batch szolgáltatás API-val

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A Batch szolgáltatás API támogatja a kimeneti adatok Azure Storage-ba való megőrzését olyan feladatokhoz és Feladatkezelő-feladatokhoz, amelyek a virtuális gép konfigurációjával rendelkező készleteken futnak. Feladat hozzáadásakor megadhat egy tárolót az Azure Storage-ban a feladat kimenetének célhelye. A Batch szolgáltatás ezután az adott tárolóba írja a kimeneti adatokat, ha a feladat befejeződött.

A Batch szolgáltatás API-ját a feladat kimenetének megőrzésére használhatja, így nem kell módosítania az alkalmazást, amelyen a feladat fut. Ehelyett az ügyfélalkalmazás néhány módosításával megtarthatja a feladat kimenetét a feladatot létrehozó kódból.

> [!IMPORTANT]
> A Batch Service API-val a feladat adatai az Azure Storage-ba való megőrzése nem működik a [2018. február 1](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204). előtt létrehozott készletekkel.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Mikor kell használni a Batch szolgáltatás API-ját a feladat kimenetének megőrzéséhez?

Azure Batch több módszert biztosít a feladat kimenetének megőrzéséhez. A Batch szolgáltatás API-ját a legmegfelelőbb módszer a következő forgatókönyvek esetén:

- Kódot szeretne írni a feladat kimenetének megtartásához az ügyfélalkalmazás alapján, a feladat által futtatott alkalmazás módosítása nélkül.
- A virtuális gép konfigurációjával létrehozott készletekben szeretné megőrizni a Batch-feladatok és a Feladatkezelő-feladatok kimenetét.
- Egy tetszőleges nevű Azure Storage-tárolóban szeretné megőrizni a kimenetet.
- Meg szeretné őrizni a kimenetet egy nevű Azure Storage-tárolóba a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)utasításnak megfelelően.

Ha a forgatókönyv eltér a fent felsorolttól, előfordulhat, hogy más megközelítést kell figyelembe vennie. A Batch szolgáltatás API-je például jelenleg nem támogatja az Azure Storage-ba történő adatfolyam-továbbítást, amíg a feladat fut. A kimenet továbbításához érdemes lehet a .NET-hez elérhető batch file Conventions könyvtárat használni. Más nyelveken a saját megoldását kell megvalósítani. A tevékenységek kimenetének megőrzésével kapcsolatos további információkért lásd: [feladatok és tevékenységek kimenetének megőrzése az Azure Storage](batch-task-output.md)-ban.

## <a name="create-a-container-in-azure-storage"></a>Tároló létrehozása az Azure Storage-ban

Ahhoz, hogy a feladat kimenete az Azure Storage-ba kerüljön, létre kell hoznia egy tárolót, amely a kimeneti fájlok céljaként szolgál. A feladat futtatása előtt hozza létre a tárolót, lehetőleg a feladat elküldése előtt. A tároló létrehozásához használja a megfelelő Azure Storage ügyféloldali kódtárat vagy SDK-t. Az Azure Storage API-kkal kapcsolatos további információkért tekintse meg az [Azure Storage dokumentációját](../storage/index.yml).

Ha például a C# nyelven ír egy alkalmazást, használja az [Azure Storage ügyféloldali kódtárat a .net-hez](https://www.nuget.org/packages/WindowsAzure.Storage/). Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Megosztott hozzáférési aláírás beszerzése a tárolóhoz

A tároló létrehozása után szerezzen be egy közös hozzáférésű aláírást (SAS), amely írási hozzáféréssel rendelkezik a tárolóhoz. A SAS delegált hozzáférést biztosít a tárolóhoz. Az SAS hozzáférést biztosít a megadott engedélyekkel, és egy adott időintervallumon belül. A Batch szolgáltatásnak szüksége van egy írási engedéllyel rendelkező SAS-re a feladat kimenetének tárolóba írásához. Az SAS-vel kapcsolatos további információkért lásd: a [közös hozzáférésű aláírások használata \( \) Az Azure Storage-ban](../storage/common/storage-sas-overview.md).

Ha az Azure Storage API-k használatával kap SAS-t, az API SAS-jogkivonat karakterláncot ad vissza. Ez a jogkivonat-karakterlánc tartalmazza az SAS összes paraméterét, beleértve az engedélyeket és a SAS érvényességi időtartamát. Ahhoz, hogy az SAS-t egy tároló Azure Storage-beli eléréséhez használja, hozzá kell fűzni az SAS-jogkivonat karakterláncát az erőforrás URI azonosítóhoz. Az erőforrás URI-ja a hozzáfűzött SAS-jogkivonattal együtt hitelesített hozzáférést biztosít az Azure Storage-hoz.

Az alábbi példa bemutatja, hogyan kérhető le csak írható SAS-token sztring a tárolóhoz, majd hozzáfűzi az SAS-t a tároló URI-hoz:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Kimeneti fájlok megadása a feladat kimenetéhez

Egy feladat kimeneti fájljainak megadásához hozzon létre egy [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) -objektumot, és rendelje hozzá a [CloudTask. OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) tulajdonsághoz a feladat létrehozásakor.

A következő C#-kódrészlet olyan feladatot hoz létre, amely véletlenszerű számokat ír egy nevű fájlba `output.txt` . A példa létrehoz egy kimeneti fájlt a `output.txt` tárolóba való íráshoz. A példa a fájl mintájának megfelelő naplófájlok kimeneti fájljait is létrehozza `std*.txt` (_például_ `stdout.txt` és `stderr.txt` ). A tároló URL-címéhez a tárolóhoz korábban létrehozott SAS szükséges. A Batch szolgáltatás az SAS használatával hitelesíti a tárolóhoz való hozzáférést:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

> [!NOTE]
> Ha a jelen példában a Linux-t használja, ügyeljen arra, hogy a perjeleket a perjelek értékre módosítsa.

### <a name="specify-a-file-pattern-for-matching"></a>A megfelelő fájl mintázatának megadása

Ha kimeneti fájlt ad meg, a [OutputFile. FilePattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) tulajdonsággal határozhatja meg a megfeleltetéshez használandó fájl mintázatát. A fájl mintája nulla fájlokhoz, egyetlen fájlhoz vagy a feladat által létrehozott fájlok készletéhez is tartozhat.

A **FilePattern** tulajdonság a standard fájlrendszerbeli helyettesítő karaktereket `*` (például nem rekurzív egyezések esetén) és `**` (rekurzív egyezések esetén) támogatja. A fenti mintakód például a nem rekurzív módon való egyezésre szolgáló fájl mintát adja meg `std*.txt` :

`filePattern: @"..\std*.txt"`

Egyetlen fájl feltöltéséhez egy helyettesítő karakter nélküli fájlt kell megadnia. A fenti mintakód például a következőnek megfelelő fájltípust adja meg `output.txt` :

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Feltöltési feltétel meghatározása

A [OutputFileUploadOptions. UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) tulajdonság a kimeneti fájlok feltételes feltöltését teszi lehetővé. Gyakori eset, ha a feladat sikeres, és egy másik fájl, ha sikertelen, fel kell töltenie egy-egy készletet. Előfordulhat például, hogy csak akkor szeretné feltölteni a részletes naplófájlokat, ha a feladat meghiúsul, és nem nulla kilépési kóddal kilép. Hasonlóképpen előfordulhat, hogy csak akkor szeretné feltölteni az eredményeket, ha a feladat sikeres, mivel előfordulhat, hogy a fájlok hiányoznak vagy hiányosak, ha a feladat meghiúsul.

A fenti mintakód a **UploadCondition** tulajdonságot **TaskCompletion** értékre állítja. Ezzel a beállítással adható meg, hogy a rendszer feltöltse-e a fájlt a feladatok befejeződése után, a kilépési kód értékétől függetlenül.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

További beállításokért tekintse meg a [OutputFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enumerálást.

### <a name="disambiguate-files-with-the-same-name"></a>Azonos nevű egyértelműsítse-fájlok

A feladat feladatai létrehozhatnak azonos nevű fájlokat. Például a és a egy feladatban `stdout.txt` `stderr.txt` futó minden feladathoz jönnek létre. Mivel minden egyes feladat a saját környezetében fut, ezek a fájlok nem ütköznek a csomópont fájlrendszerével. Ha azonban több feladatból is tölt fel fájlokat egy megosztott tárolóba, a fájlokat ugyanazzal a névvel kell egyértelműsítse.

A [OutputFileBlobContainerDestination. Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) tulajdonság a kimeneti fájlok céljának blobját vagy virtuális könyvtárát adja meg. A **path** tulajdonság segítségével úgy nevezheti el a blobot vagy a virtuális könyvtárat, hogy az azonos nevű fájlok egyedi névvel vannak ellátva az Azure Storage-ban. Az elérési úton található feladat-azonosító használata jó módszer az egyedi nevek biztosítására és a fájlok egyszerű azonosítására.

Ha a **FilePattern** tulajdonság egy helyettesítő kifejezésre van beállítva, akkor a mintázatnak megfelelő összes fájl fel lesz töltve a **path** tulajdonság által megadott virtuális könyvtárba. Ha például a tároló `mycontainer` , a feladat azonosítója `mytask` , és a fájl mintája `..\std*.txt` , akkor az Azure Storage-ban a kimeneti fájlok abszolút URI-azonosítói a következőkhöz hasonlóak lesznek:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Ha a **FilePattern** tulajdonság úgy van beállítva, hogy egyetlen fájlnévnek felel meg, ami azt jelenti, hogy nem tartalmaz helyettesítő karaktert, a **path** tulajdonság értéke a teljes blob nevét adja meg. Ha azt tervezi, hogy a rendszer több feladatból származó egyetlen fájllal ütközik az elnevezési feladatokkal, akkor a fájl nevének részeként adja meg a virtuális könyvtár nevét, hogy egyértelműsítse ezeket a fájlokat. Állítsa be például a **path** tulajdonságot úgy, hogy tartalmazza a feladat azonosítóját, a határoló karaktert (általában egy perjelet) és a fájlnevet:

`path: taskId + @"/output.txt"`

A tevékenységek kimeneti fájljainak abszolút URI-azonosítói a következőhöz hasonlóak lesznek:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

További információ az Azure Storage-beli virtuális könyvtárakról: [a tárolóban lévő Blobok listázása](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Fájlfeltöltés-hibák diagnosztizálása

Ha a kimeneti fájlok feltöltése az Azure Storage-ba meghiúsul, akkor a feladat **befejeződött** állapotba kerül, és a [TaskExecutionInformation. FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) tulajdonság be van állítva. Ellenőrizze a **FailureInformation** tulajdonságot, hogy milyen hibát észlelt a hiba. Például itt a fájl feltöltésekor felmerülő hiba, ha a tároló nem található:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Minden fájl feltöltésekor a Batch két naplófájlt ír a számítási csomópontba, `fileuploadout.txt` és `fileuploaderr.txt` . Megvizsgálhatja ezeket a naplófájlokat, ha többet szeretne megtudni egy adott hibáról. Azokban az esetekben, amikor a fájlfeltöltés soha nem történt meg, például azért, mert maga a feladat nem tudott futni, akkor ezek a naplófájlok nem fognak létezni.

## <a name="diagnose-file-upload-performance"></a>Fájlfeltöltés teljesítményének diagnosztizálása

A `fileuploadout.txt` fájl naplófájlok feltöltésének folyamata folyamatban van. Ezt a fájlt megvizsgálva további információkat tudhat meg arról, hogy mennyi ideig tart a fájlfeltöltés. Ne feledje, hogy a teljesítmény feltöltésének számos tényezője van, beleértve a csomópont méretét, a csomópont más tevékenységeit a feltöltéskor, hogy a cél tároló ugyanabban a régióban van-e, mint a Batch-készlet, hány csomópontot töltenek fel a Storage-fiókba, és így tovább.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>A Batch szolgáltatás API használata a Batch file Conventions standard használatával

Ha a Batch szolgáltatás API-val megőrzi a feladatok kimenetét, a cél tárolót és a blobokat azonban Ön is elnevezheti. Azt is megteheti, hogy a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)alapján nevezi el őket. A file Conventions standard meghatározza a cél tároló és a blob nevét az Azure Storage-ban egy adott kimeneti fájl számára a feladat és a feladat neve alapján. Ha a fájl-konvenciók standardot használja a kimeneti fájlok elnevezéséhez, a kimeneti fájlok megtekinthetők a [Azure Portalban](https://portal.azure.com).

Ha C# nyelven fejleszt, használhatja a [Batch file Conventions Library for .net-hez](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files)beépített metódusokat. Ez a kódtár létrehozza a megfelelő névvel ellátott tárolókat és blob-elérési utakat. Meghívhatja például az API-t a tároló helyes nevének beolvasására a feladatnév alapján:

```csharp
string containerName = job.OutputStorageContainerName();
```

A [CloudJobExtensions. GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metódussal olyan közös hozzáférésű aláírási (SAS) URL-címet adhat vissza, amely a tárolóba való íráshoz használható. Ezután átadhatja ezt az SAS-t a [OutputFileBlobContainerDestination](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktornak.

Ha a C# nyelvtől eltérő nyelvet fejleszt, saját maga is végre kell hajtania a fájl konvencióit.

## <a name="code-sample"></a>Kódminta

A [PersistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) minta projekt a githubon lévő [Azure batch Code-minták](https://github.com/Azure/azure-batch-samples) egyike. Ez a Visual Studio-megoldás bemutatja, hogyan használható a Batch ügyféloldali kódtára a .NET-hez a feladat kimenetének tartós tárterületre való megőrzéséhez. A minta futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2019**-ben.
2. Adja hozzá a Batch és a Storage- **fiók hitelesítő adatait** a **AccountSettings. Settings** Microsoft.Azure.BatCH. Samples. Common projektben.
3. A megoldás **létrehozása** (de ne fusson). Ha a rendszer kéri, állítsa vissza az NuGet-csomagokat.
4. A Azure Portal használatával töltse fel a **PersistOutputsTask** [alkalmazási csomagját](batch-application-packages.md) . Adja `PersistOutputsTask.exe` meg a és a függő szerelvényeit a. zip csomagban, állítsa az alkalmazás azonosítóját "PersistOutputsTask" értékre, az alkalmazáscsomag verzióját pedig "1,0"-re.
5. **Indítsa el** (futtassa) a **PersistOutputs** projektet.
6. Amikor a rendszer kéri, hogy válassza ki a minta futtatásához használni kívánt adatmegőrzési technológiát, írja be a **2** értéket a minta futtatásához a Batch szolgáltatás API használatával a feladat kimenetének megőrzése érdekében.
7. Ha szeretné, futtassa újra a mintát, és a **3** érték megadásával maradjon meg a Batch szolgáltatás API-val, és nevezze el a cél tárolót és a blob elérési útját a file Conventions standard utasításnak megfelelően.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni arról, hogy a rendszer hogyan őrzi meg a feladatokat a .NET-hez készült file Conventions Library használatával, tekintse meg [a feladatok és feladatok adatainak megőrzése az Azure Storage-ba a .net-hez készült batch file Conventions](batch-task-output-file-conventions.md)
- Ha többet szeretne megtudni a Azure Batch kimeneti adatainak megőrzésével kapcsolatos egyéb megközelítésekről, tekintse meg a [feladatok és tevékenységek kimenetének megőrzése az Azure Storage](batch-task-output.md)-ba című témakört.
