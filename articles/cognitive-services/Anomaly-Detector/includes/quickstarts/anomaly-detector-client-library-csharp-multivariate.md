---
title: Anomália-detektor .NET többváltozós ügyféloldali kódtár rövid útmutatója
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 1318a8c410f14f4a1dc91072d66f18e39f7ca7e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316028"
---
Ismerkedjen meg a következővel: rendellenesség-érzékelő többváltozós ügyféloldali kódtára a .NET-hez. A következő lépésekkel telepítheti a csomagot, és megkezdheti a szolgáltatás által biztosított algoritmusok használatát. Az új többváltozós anomália-észlelési API-k lehetővé teszik a fejlesztők számára, hogy a speciális AI-t egyszerűen integrálják a mérőszámokból származó rendellenességek észlelése érdekében, anélkül, hogy gépi tanulási ismeretekre vagy címkézett adatokra lenne A különböző jelek közötti függőségek és korrelációk közötti összefüggések automatikusan kulcsfontosságú tényezőként számítanak. Ezzel a megoldással proaktív módon védhető meg az összetett rendszerek hibái.

A (z) rendszerben a következőhöz tartozó anomália-detektor többváltozós ügyféloldali kódtára használható:

* A rendszerszintű rendellenességek észlelése idősorozatok csoportjából.
* Ha az egyes idősorozatok nem sokat mondanak, és az összes jelet meg kell vizsgálnia a probléma észlelése érdekében.
* A Predicative több száz különböző típusú érzékelővel, a rendszerállapot különböző szempontjainak mérésével a költséges fizikai eszközök karbantartását is megfigyelheti.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core) jelenlegi verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. Illessze be a kulcsot és a végpontot a rövid útmutató későbbi részében található kódra.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `anomaly-detector-quickstart-multivariate` . Ez a parancs egy egyszerű "Hello World" projektet hoz létre egyetlen C# forrásfájl: *program. cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```dotnetcli
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a következő paranccsal a .NET-hez készült rendellenesség-Kiderítő ügyféloldali kódtárat:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

A projekt könyvtárában nyissa meg a *program. cs* fájlt, és adja hozzá a következőt a használatával `directives` :

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

Az alkalmazás `main()` metódusában hozzon létre változókat az erőforrás Azure-végpontja, az API-kulcs és egy egyéni adatforrás számára.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Az anomáliák többváltozós API-k használatához az észlelés használata előtt be kell tanítani a saját modelljét. A betanításhoz használt adatmennyiség az idősorozatok egy kötege, minden egyes idősorozatnak CSV formátumúnak kell lennie két oszloppal, egy időbélyeggel és egy értékkel. Az összes idősorozatot egy zip-fájlba kell csomagolni, és fel kell tölteni az [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs)-ba. Alapértelmezés szerint a rendszer a fájlnevet fogja használni az idősorozat változójának ábrázolásához. Azt is megteheti, hogy a fájl egy további meta.jsis tartalmazhatja a zip-fájlban, ha azt szeretné, hogy a változó neve eltér a. zip-fájl nevétől. A [blob sas (közös hozzáférésű aláírások) URL-címének](../../../../storage/common/storage-sas-overview.md)létrehozása után a betanításhoz használhatja a zip-fájl URL-címét.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el az alábbi műveleteket a "a" rendellenesség-Kiderítő többváltozós ügyféloldali kódtár .NET-hez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [A modell betanítása](#train-the-model)
* [Rendellenességek észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Anomália-detektor ügyfelet hoz létre a végponttal és a kulccsal.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>A modell betanítása

Hozzon létre egy új, privát aszinkron feladatot a modell képzésének kezeléséhez. Ezzel a paranccsal `TrainMultivariateModel` betaníthatja a modellt, és `GetMultivariateModelAysnc` megtekintheti a képzés befejeződését.

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

## <a name="detect-anomalies"></a>Rendellenességek észlelése

Az újonnan betanított modellel felhasználható rendellenességek észleléséhez hozzon létre egy `private async Task` nevesített nevet `detectAsync` . Hozzon létre egy újat, `DetectionRequest` és adja át a paraméterként `DetectAnomalyAsync` .

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

A korábban betanított modell exportálásához hozzon létre egy `private async Task` nevesített nevet `exportAysnc` . Ezt fogja használni `ExportModelAsync` , majd továbbítja a modell azonosítóját, amelyet exportálni szeretne.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Response model_response = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        Stream model;
        if (model_response.ContentStream != null)
        {
            model = model_response.ContentStream;
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

A korábban létrehozott modell törléséhez adja meg a `DeleteMultivariateModelAsync` törölni kívánt modell azonosítóját. A modell AZONOSÍTÓjának lekéréséhez a `getModelNumberAsync` következőket teheti:

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

Most, hogy már rendelkezik az összes összetevő részével, további kódokat kell hozzáadnia a Main metódushoz az újonnan létrehozott feladatok meghívásához.

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

Futtassa az alkalmazást a `dotnet run` paranccsal az alkalmazás könyvtárából.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Következő lépések

* [Anomália-érzékelő többváltozós ajánlott eljárásai](../../concepts/best-practices-multivariate.md)
