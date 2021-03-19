---
title: Kimeneti adatokat az Azure Storage-ba .NET-fájl konvenciói könyvtárával maradhat
description: Ismerje meg, hogyan használhatja Azure Batch file Conventions Library for .NET-t a Batch-feladatok & feladat kimenetének Azure Storage-ba való megőrzéséhez, és megtekintheti a kimenetet a Azure Portalban.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 1a45eed421dd8d734fcef0dd452df1d4a65fd053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936962"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Feladat-és tevékenységadatok megőrzése az Azure Storage-ban a Batch file Conventions Library for .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A feladatok adatok megőrzésének egyik módja a [Azure batch file Conventions Library for .net][nuget_package]használata. A file Conventions könyvtár leegyszerűsíti a feladat kimeneti adatainak az Azure Storage-ba történő tárolását és lekérését. A file Conventions Library a feladatban és az ügyfél kódjában is használható a fájlok megőrzése érdekében &mdash; , és az ügyfél kódjában listázhatja és lekérheti azokat. A feladat kódja a függvénytárat is használhatja a felsőbb rétegbeli tevékenységek kimenetének lekéréséhez, például egy [tevékenység függőségeinek](batch-task-dependencies.md) forgatókönyvében.

Ha a fájl konvenciók könyvtárával szeretné lekérni a kimeneti fájlokat, megkeresheti az adott feladat vagy feladat fájljait az azonosító és a cél alapján. Nem kell tudnia a fájlok nevét vagy helyét. Használhatja például a file Conventions könyvtárat egy adott feladat összes köztes fájljának listázásához, vagy egy adott feladat előnézeti fájljának beolvasásához.

> [!TIP]
> A 2017-05-01-es verziótól kezdődően a Batch szolgáltatás API támogatja a kimeneti adatok Azure Storage-ba való megőrzését olyan feladatokhoz és Feladatkezelő-feladatokhoz, amelyek a virtuális gép konfigurációjával létrehozott készleteken futnak. A Batch szolgáltatás API egyszerű módszert biztosít a kimenet megtartására egy olyan kódból, amely létrehoz egy feladatot, és a fájl-konvenciók könyvtár alternatívájaként szolgál. A Batch-ügyfélalkalmazások úgy módosíthatók, hogy a feladat futása előtt ne kelljen frissíteni a kimenetet. További információkért lásd: tevékenységadatok megőrzése az [Azure Storage-ban a Batch szolgáltatás API-val](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Mikor kell a file Conventions függvénytárat használni a feladat kimenetének megőrzéséhez?

Azure Batch több módszert biztosít a feladat kimenetének megőrzéséhez. A fájlokra vonatkozó konvenciók a legmegfelelőbbek a következő forgatókönyvekhez:

- Egyszerűen módosíthatja annak az alkalmazásnak a kódját, amelyet a tevékenység futtat, hogy a fájl-konvenciók könyvtár használatával megőrzi a fájlokat.
- Az adatok átvitele az Azure Storage-ba, miközben a feladat még fut.
- A Cloud Service-konfigurációval vagy a virtuális gép konfigurációjával létrehozott készletek adatait szeretné megőrizni.
- Az ügyfélalkalmazás vagy a feladat egyéb feladatai alapján a feladat kimeneti fájljait azonosító vagy szándékosan kell megkeresni és letölteni.
- A Azure Portalban szeretné megtekinteni a feladat kimenetét.

Ha a forgatókönyv eltér a fent felsorolttól, előfordulhat, hogy más megközelítést kell figyelembe vennie. A tevékenységek kimenetének megőrzésével kapcsolatos további információkért lásd: [feladatok és tevékenységek kimenetének megőrzése az Azure Storage](batch-task-output.md)-ban.

## <a name="what-is-the-batch-file-conventions-standard"></a>Mi a Batch file Conventions standard?

A [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) egy elnevezési sémát biztosít a cél tárolók és a blob elérési útjaihoz, amelyekhez a kimeneti fájlok íródnak. Az Azure Storage szolgáltatásban megőrzött fájlok, amelyek megfelelnek a fájl konvencióinak, automatikusan megtekinthetők a Azure Portalban. A portál tisztában van az elnevezési konvencióval, ezért képes megjeleníteni azokat a fájlokat, amelyek betartják azt.

A file Conventions Library for .NET automatikusan a fájl konvenciói standardnak megfelelően nevezi el a tárolókat és a feladat kimeneti fájljait. A file Conventions Library az Azure Storage-beli kimeneti fájlok lekérdezési módszereit is biztosítja a feladat azonosítója, a feladat azonosítója vagy a célja alapján.

Ha a-t a .NET-től eltérő nyelven fejleszti, saját alkalmazásában is megvalósíthatja a fájl konvencióit. További információ: [a Batch file Conventions standard implementálása](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Azure Storage-fiók összekapcsolása a Batch-fiókkal

Ahhoz, hogy a kimeneti adatokat az Azure Storage-ba a file Conventions Library használatával megőrzi, először egy Azure Storage-fiókot kell összekapcsolnia a Batch-fiókkal. Ha még nem tette meg, csatolja a Storage-fiókot a Batch-fiókjához a [Azure Portal](https://portal.azure.com)használatával:

1. Az Azure portálon lépjen Batch-fiókjára.
1. A **Beállítások** területen válassza a **Storage-fiók** elemet.
1. Ha még nem rendelkezik a Batch-fiókjához társított Storage-fiókkal, kattintson a **Storage-fiók (nincs)** elemre.
1. Válasszon ki egy Storage-fiókot az előfizetéséhez tartozó listából. A legjobb teljesítmény érdekében olyan Azure Storage-fiókot használjon, amely ugyanabban a régióban található, mint a feladatokat futtató batch-fiók.

## <a name="persist-output-data"></a>Kimeneti adatmegőrzés

Ha a fájl-konvenciók könyvtárával szeretné megőrizni a feladatok és a feladatok kimeneti adatait, hozzon létre egy tárolót az Azure Storage-ban, majd mentse a kimenetet a tárolóba. A feladathoz tartozó [Azure Storage ügyféloldali kódtárat](https://www.nuget.org/packages/WindowsAzure.Storage) használhatja a .net-hez a feladat kimenetének tárolóba való feltöltéséhez.

További információ a tárolók és Blobok Azure Storage-beli használatáról: az [Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md).

> [!WARNING]
> A file Conventions függvénytárban megőrzött összes feladat és feladat kimenete ugyanabban a tárolóban van tárolva. Ha nagy számú feladat próbálkozik a fájlok egyidejű megtartásával, az Azure Storage-szabályozás korlátozásai kényszerítve lehetnek. További információ a szabályozás korlátairól: [a blob Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Storage-tároló létrehozása

Ha a feladat kimenetét az Azure Storage-ba szeretné megőrizni, először hozzon létre egy tárolót a [CloudJob][net_cloudjob]meghívásával. [PrepareOutputStorageAsync][net_prepareoutputasync]. Ez a kiterjesztési metódus egy [CloudStorageAccount][net_cloudstorageaccount] objektumot fogad el paraméterként. Létrehoz egy nevű tárolót a fájl-konvenciók standard alapján, így annak tartalma a Azure Portal és a cikk későbbi részében tárgyalt lekérési módszerek által megkereshetővé válik.

A kódot általában úgy helyezi el, hogy létrehoz egy tárolót az ügyfélalkalmazás számára &mdash; a készleteket, feladatokat és tevékenységeket létrehozó alkalmazást.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Feladat kimenetének tárolása

Most, hogy előkészített egy tárolót az Azure Storage-ban, a feladatok a fájl konvenciói könyvtárban található [TaskOutputStorage][net_taskoutputstorage] osztály használatával menthetik a kimenetet a tárolóba.

A feladathoz tartozó kódban először hozzon létre egy [TaskOutputStorage][net_taskoutputstorage] objektumot, majd amikor a feladat befejezte a munkáját, hívja meg a [TaskOutputStorage][net_taskoutputstorage]. A [SaveAsync][net_saveasync] metódussal mentheti a kimenetét az Azure Storage-ba.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

A `kind` [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage)paramétere.[ A SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) metódus kategorizálja a megőrzött fájlokat. Négy előre definiált [TaskOutputKind][net_taskoutputkind] -típus létezik: `TaskOutput` ,, `TaskPreview` `TaskLog` , és `TaskIntermediate.` Egyéni kimeneti kategóriákat is meghatározhat.

Ezek a kimeneti típusok lehetővé teszik annak megadását, hogy a rendszer mely típusú kimeneteket sorolja fel, amikor később lekérdezi a Batch-t egy adott feladat megőrzött kimenetére. Más szóval, amikor egy tevékenység kimeneteit sorolja fel, szűrheti a listát a kimeneti típusok egyikén. Adja meg például a következőt: "az *előnézet* kimenete a *109* feladat számára". A kimenetek listázásával és lekérésével kapcsolatos további tudnivalókat a cikk későbbi, a lekérése című részében találja.

> [!TIP]
> A kimeneti típus azt is meghatározza, hogy a Azure Portal hol jelenjenek meg egy adott fájl: a *TaskOutput* kategorizált fájlok a feladat **kimeneti fájljai** alatt jelennek meg, és a *TaskLog* -fájlok megjelennek a **feladatok naplói** alatt.

### <a name="store-job-outputs"></a>A feladatok kimenetének tárolása

A feladatok kimenetének tárolása mellett a teljes feladathoz társított kimeneteket is tárolhatja. Például egy mozgókép-renderelési feladat egyesítési feladatában megtarthatja a teljes renderelt mozgóképet feladat kimenetként. Ha a feladat befejeződött, az ügyfélalkalmazás listázhatja és lekérheti a feladat kimeneteit, és nem kell lekérdezni az egyes feladatokat.

A [JobOutputStorage][net_joboutputstorage]meghívásával tárolja a feladatok kimenetét. [SaveAsync][net_joboutputstorage_saveasync] metódus, és a [JobOutputKind][net_joboutputkind] és a fájlnév meghatározása:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Ahogy a **TaskOutputKind** típusa is, a [JobOutputKind][net_joboutputkind] típus használatával kategorizálhatja a feladatok megőrzött fájljait. Ez a paraméter lehetővé teszi egy adott típusú kimenet későbbi lekérdezését (listázása). A **JobOutputKind** típusa a kimenet és az előnézet kategóriát is tartalmazza, és támogatja az egyéni kategóriák létrehozását.

### <a name="store-task-logs"></a>A feladatok naplóinak tárolása

Ha egy adott feladat vagy feladat befejezése után egy fájl tartós tárterületen marad, előfordulhat, hogy meg kell őriznie a feladatok naplófájljainak végrehajtása során frissített fájlokat, &mdash; vagy `stdout.txt` például a `stderr.txt` következőt:. Erre a célra a Azure Batch file Conventions Library biztosítja a [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync][net_savetrackedasync] metódus. A [SaveTrackedAsync][net_savetrackedasync]segítségével nyomon követheti a csomóponton lévő fájlok frissítéseit (a megadott intervallumban), és megőrizheti ezeket a frissítéseket az Azure Storage-ban.

A következő kódrészletben a [SaveTrackedAsync][net_savetrackedasync] használatával 15 másodpercenként frissítjük az `stdout.txt` Azure Storage-t a feladat végrehajtása során:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

A kommentált szakasz a `Code to process data and produce output file(s)` feladat szokásosan végrehajtandó kódjának helyőrzője. Előfordulhat például, hogy olyan kódot tartalmaz, amely az Azure Storage-ból tölti le az adatait, és az átalakítást vagy a számítást végzi. A kódrészlet fontos része azt mutatja be, hogyan lehet egy blokkban becsomagolni egy ilyen kódot, `using` hogy rendszeresen frissítsen egy fájlt a [SaveTrackedAsync][net_savetrackedasync].

A csomópont-ügynök egy olyan program, amely a készlet minden csomópontján fut, és a parancs-és vezérlési felületet biztosítja a csomópont és a Batch szolgáltatás között. A `Task.Delay` blokk végén meg kell hívni a hívást `using` annak biztosítására, hogy a csomópont-ügynöknek ideje legyen a standard tartalmának kiürítésére a csomóponton lévő stdout.txt fájlba. Ezen késés nélkül lehetséges a kimenet utolsó néhány másodpercének kihagyása. Előfordulhat, hogy ez a késleltetés nem szükséges minden fájlhoz.

> [!NOTE]
> Ha engedélyezi a **SaveTrackedAsync** a fájlok nyomon követését, a rendszer csak a követett fájlhoz *fűzi hozzá* az Azure Storage-ban. Ezt a metódust csak a nem forgó naplófájlok vagy más, a fájl végén hozzáfűzési művelettel írt fájlok nyomon követésére használhatja.

## <a name="retrieve-output-data"></a>Kimeneti adatokat kér le

Ha a megőrzött kimenetet a Azure Batch file Conventions Library használatával kéri le, akkor a feladat-és a feladat-központú módon történik. Az adott feladat vagy feladat kimenetét anélkül kérheti le, hogy az Azure Storage-beli elérési útját vagy annak fájlnevét kellene tudnia. Ehelyett a feladat vagy a feladat azonosítója alapján is kérheti a kimeneti fájlokat.

A következő kódrészlet megismétli egy feladat feladatait, kinyomtat néhány információt a feladat kimeneti fájljairól, majd letölti a fájljait a tárolóból.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Kimeneti fájlok megtekintése a Azure Portalban

A Azure Portal megjeleníti a társított Azure Storage-fiókban megőrzött feladatok kimeneti fájljait és naplóit a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)használatával. Ezeket a konvenciókat saját maga is megvalósíthatja, vagy használhatja a fájl konvenciók tárat is a .NET-alkalmazásokban.

Ha engedélyezni szeretné a kimeneti fájlok megjelenítését a portálon, a következő követelményeknek kell megfelelnie:

1. Egy Azure Storage-fiók összekapcsolása a Batch-fiókkal.
1. Adja meg a tárolók és a fájlok előre meghatározott elnevezési konvencióit a kimenetek megőrzésekor. Ezen konvenciók definícióját a fájl-konvenciók könyvtárában találja [.][github_file_conventions_readme] Ha a [Azure batch fájl konvenciói][nuget_package] könyvtárat használja a kimenet megőrzéséhez, a fájlok a fájl-konvenciók standardnak megfelelően megmaradnak.

Ha meg szeretné tekinteni a feladat kimeneti fájljait és naplóit a Azure Portalban, Navigáljon arra a feladatra, amelynek a kimenetét érdekli, majd kattintson a **mentett kimeneti fájlok** vagy a **Mentett naplók** elemre. Ez a kép a "007" AZONOSÍTÓJÚ feladat **mentett kimeneti fájljait** jeleníti meg:

![Feladat kimenetei panel a Azure Portal][2]

## <a name="code-sample"></a>Kódminta

A [PersistOutputs][github_persistoutputs] minta projekt a githubon lévő [Azure batch Code-minták][github_samples] egyike. Ez a Visual Studio-megoldás bemutatja, hogyan használható a Azure Batch file Conventions könyvtár a feladatok kimenetének tartós tárterületre való megőrzéséhez. A minta futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2019**-ben.
2. Adja hozzá a Batch és a Storage- **fiók hitelesítő adatait** a **AccountSettings. Settings** Microsoft.Azure.BatCH. Samples. Common projektben.
3. A megoldás **létrehozása** (de ne fusson). Ha a rendszer kéri, állítsa vissza az NuGet-csomagokat.
4. A Azure Portal használatával töltse fel a **PersistOutputsTask** [alkalmazási csomagját](batch-application-packages.md) . Adja `PersistOutputsTask.exe` meg a és a függő szerelvényeit a. zip csomagban, állítsa az alkalmazás azonosítóját "PersistOutputsTask" értékre, az alkalmazáscsomag verzióját pedig "1,0"-re.
5. **Indítsa el** (futtassa) a **PersistOutputs** projektet.
6. Amikor a rendszer felszólítja, hogy válassza ki a minta futtatásához használni kívánt adatmegőrzési technológiát, írja be az **1** értéket a minta futtatásához a fájl konvenciók könyvtár használatával a feladat kimenetének megőrzése érdekében. 

## <a name="next-steps"></a>Következő lépések

### <a name="get-the-batch-file-conventions-library-for-net"></a>A .NET-hez készült batch file Conventions Library letöltése

A .NET-hez készült batch file Conventions Library a [NuGet][nuget_package]címen érhető el. A könyvtár kiterjeszti a [CloudJob][net_cloudjob] és a [CloudTask][net_cloudtask] osztályt új metódusokkal. Tekintse meg a file Conventions Library [dokumentációját](/dotnet/api/microsoft.azure.batch.conventions.files) is.

A file Conventions könyvtár [forráskódja][github_file_conventions] elérhető a githubon a .net-hez készült Microsoft Azure SDK-tárházban. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>A kimeneti adatokat megtartó egyéb megközelítések megismerése

- Tekintse meg az Azure Storage-ban a feladatok [és feladatok kimenetének](batch-task-output.md) megőrzése című témakört, amely áttekintést nyújt a feladat-és tevékenységadatok megőrzéséről.
- Lásd: tevékenységadatok megőrzése az [Azure Storage-ban a Batch szolgáltatás API-val](batch-task-output-files.md) , amelyből megtudhatja, hogyan használhatja a Batch szolgáltatás API-ját a kimeneti adatok megőrzésére.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudstorageaccount]: /java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputkind
[net_joboutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage
[net_joboutputstorage_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_prepareoutputasync]: /dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync
[net_saveasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync
[net_savetrackedasync]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync
[net_taskoutputkind]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputkind
[net_taskoutputstorage]: /dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Mentett kimeneti fájlok és mentett naplók választói a portálon"
[2]: ./media/batch-task-output/task-output-02.png "Feladat kimenetei panel a Azure Portal"
