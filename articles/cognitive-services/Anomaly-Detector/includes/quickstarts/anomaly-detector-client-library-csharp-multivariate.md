---
title: anomáliadetektor .NET többváltozós ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: a657b8f5bf967131a0168dbea5bb1db86b3b559e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800320"
---
Első lépések a anomáliadetektor .NET-hez való többválozós ügyféloldali kódtárával. Kövesse az alábbi lépéseket a csomag telepítéséhez és a szolgáltatás által biztosított algoritmusok használatának elkezdéhez. Az új többváltozós anomáliadetektálási API-k a fejlett AI egyszerű integrálásával teszik lehetővé a fejlesztők számára a metrikák csoportjaiból származó anomáliadetektálást anélkül, hogy gépi tanulási ismeretekre vagy címkével jelölt adatokra lenne szükség. A különböző jelek közötti függőségeket és összefüggéseket a rendszer automatikusan kulcsfontosságú tényezőknek számítja. Ez segít proaktív módon megvédeni az összetett rendszereket a hibáktól.

Használja a anomáliadetektor .NET-hez használható többválozós ügyféloldali kódtárát a következőre:

* Rendszerszintű anomáliák észlelése idősorok egy csoportjából.
* Ha egy adott idősor nem árul el sokat, és minden jelet meg kell néznie a probléma észlelése érdekében.
* Drága fizikai eszközök predikatív karbantartása több tíz-száz különböző típusú érzékelővel, amelyek a rendszer állapotának különböző aspektusait mérik.

[Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector)  |  [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* A [.NET Core aktuális verziója](https://dotnet.microsoft.com/download/dotnet-core)
* Az Azure-előfizetés létrehozása után hozzon létre egy anomáliadetektor-erőforrást, anomáliadetektor erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Várja meg az üzembe helyezést, és válassza az **Erőforráshoz ugrás** gombot.
    * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a anomáliadetektor API-hoz. Illessze be a kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába.
    Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `anomaly-detector-quickstart-multivariate` néven. Ez a parancs egy egyszerű "Hello World" projektet hoz létre egyetlen C#-forrásfájllal: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappába. Az alkalmazást a következővel építheti fel:

```dotnetcli
dotnet build
```

A build kimenete nem tartalmazhat figyelmeztetéseket vagy hibákat.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazáskönyvtárban telepítse a .NET-hez anomáliadetektor ügyféloldali kódtárat a következő paranccsal:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

A projektkönyvtárból nyissa meg a *program.cs fájlt,* és adja hozzá a következőket a `directives` használatával:

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

Az alkalmazás metódusában hozzon létre változókat az erőforrás Azure-végpontja, az API-kulcs és egy egyéni `main()` adatforrás számára.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 A többváltozós ANOMÁLIADETEKTOR való használathoz az észlelés előtt be kell betanítanunk a saját modellünket. A betanításhoz használt adatok idősorkötetek, és minden idősornak CSV formátumúnak kell lennie két oszloppal, időbélyegzővel és értékkel. Az idősorokat egyetlen zip-fájlba kell tömörítenünk, és fel kell tölteni az [Azure Blob Storage-ba.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Alapértelmezés szerint a fájlnév az idősor változóját képviseli. Azt is meta.js, hogy a zip-fájlban egy további fájlnevet is fel kell venni, ha azt szeretné, hogy a változó neve eltér a .zip fájl nevétől. Miután létrehoztunk egy SAS- (közös hozzáférésű [jogosultságú) URL-címet,](../../../../storage/common/storage-sas-overview.md)a zip-fájl URL-címét használjuk a betanításhoz.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következőket mutatják be anomáliadetektor .NET-hez anomáliadetektor ügyfélkódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [A modell betanítása](#train-the-model)
* [Anomáliák észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Példányosíthat egy anomáliadetektor-ügyfelet a végponttal és a kulccsal.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>A modell betanítása

Hozzon létre egy új privát aszinkron feladatot az alábbiak szerint a modell betanításának kezeléshez. A használatával fogja `TrainMultivariateModel` betanítni a modellt, és `GetMultivariateModelAysnc` ellenőrizni, hogy mikor fejeződött be a betanítás.

```csharp
private async Task trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        ModelStatus? model_status = null;
        int tryout_count = 0;
        TimeSpan create_limit = new TimeSpan(0, 3, 0);
        while (tryout_count < max_tryout & model_status != ModelStatus.Ready)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            ModelInfo model_info = get_response.Value.ModelInfo;
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, model_info.Status));

            if (model_info != null)
            {
                model_status = model_info.Status;
            }
            tryout_count += 1;
        };
        get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);

        if (model_status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Anomáliák észlelése

Ha az újonnan betanított modell használatával észlelni tudja az anomáliákat, hozzon létre egy `private async Task` `detectAsync` nevűt. Létre fog hozni egy újat, `DetectionRequest` és paraméterként át fogja adni a következőnek: `DetectAnomalyAsync` .

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        int tryout_count = 0;
        while (result.Value.Summary.Status != DetectionStatus.Ready & tryout_count < max_tryout)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
            tryout_count += 1;
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Modell exportálása

A korábban betanított modell exportáláshoz hozzon létre egy `private async Task` `exportAysnc` nevűt. Ezt fogja használni, és át fogja adni az exportálni kívánt `ExportModelAsync` modell azonosítóját.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Modell törlése

Egy korábban létrehozott modell törléséhez használja a parancsot, és adja át a törölni kívánt `DeleteMultivariateModelAsync` modell azonosítóját. A modellazonosító lekérését a következővel kaphatja `getModelNumberAsync` meg:

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>A fő metódus

Most, hogy az összes összetevővel megvan, további kódot kell hozzáadnia a fő metódushoz az újonnan létrehozott feladatok hívásához.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `dotnet run` alkalmazás könyvtárában található paranccsal.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Következő lépések

* [anomáliadetektor többválékos ajánlott eljárások](../../concepts/best-practices-multivariate.md)
