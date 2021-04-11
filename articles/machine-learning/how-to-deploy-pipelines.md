---
title: ML-folyamatok közzététele
titleSuffix: Azure Machine Learning
description: Gépi tanulási munkafolyamatok futtatása gépi tanulási folyamatokkal és a Pythonhoz készült Azure Machine Learning SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520176"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Gépi tanulási folyamatok közzététele és nyomon követése



Ez a cikk bemutatja, hogyan oszthat meg egy gépi tanulási folyamatot kollégáival vagy ügyfeleivel.

A gépi tanulási folyamatok a gépi tanulási feladatok újrafelhasználható munkafolyamatai. A folyamatok egyik előnye a nagyobb együttműködés. Emellett olyan folyamatokat is használhat, amelyek lehetővé teszik az ügyfelek számára az aktuális modell használatát, amikor új verzióval dolgozik. 

## <a name="prerequisites"></a>Előfeltételek

* [Azure Machine learning munkaterület](how-to-manage-workspace.md) létrehozása az összes folyamat erőforrásának tárolásához

* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) az Azure Machine learning SDK telepítéséhez, vagy használjon olyan [Azure Machine learning számítási példányt](concept-compute-instance.md) , amelyen már telepítve van az SDK

* Hozzon létre és futtasson egy gépi tanulási folyamatot, például a következő [oktatóanyaggal: Azure Machine learning folyamat létrehozása a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md). További lehetőségek: [Machine learning-folyamatok létrehozása és futtatása Azure Machine learning SDK-val](./how-to-create-machine-learning-pipelines.md)

## <a name="publish-a-pipeline"></a>Folyamat közzététele

Ha már rendelkezik egy folyamattal, akkor közzétehet egy folyamatot, hogy az a különböző bemenetekkel fusson. Ahhoz, hogy egy már közzétett folyamat REST-végpontja fogadja a paramétereket, konfigurálnia kell a folyamatot úgy, hogy `PipelineParameter` objektumokat használjon a változó argumentumokhoz.

1. A folyamat paramétereinek létrehozásához használjon egy alapértelmezett értéket használó [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) objektumot.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adja hozzá ezt az `PipelineParameter` objektumot paraméterként a folyamat lépéseihez a következőképpen:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Tegye közzé ezt a folyamatot, amely fogadja a paramétert a meghívásakor.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Közzétett folyamat futtatása

Minden közzétett folyamat REST-végponttal rendelkezik. A folyamat végpontja segítségével bármely külső rendszerből elindíthatja a folyamat futtatását, beleértve a nem Python-ügyfeleket is. Ez a végpont lehetővé teszi a "felügyelt ismételhetőség" használatát a Batch-pontozási és-átképzési forgatókönyvekben.

> [!IMPORTANT]
> Ha Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ a folyamathoz való hozzáférés kezelésére, [állítsa be a folyamathoz tartozó forgatókönyv (képzés vagy pontozás) engedélyeit](how-to-assign-roles.md#common-scenarios).

Az előző folyamat futtatásának meghívásához egy Azure Active Directory hitelesítési fejléc tokenre van szükség. Az ilyen jogkivonatok lekérése a [AzureCliAuthentication osztály](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) referenciájában és a Azure Machine learning jegyzetfüzetben történő [hitelesítésben](https://aka.ms/pl-restep-auth) olvasható.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

A `json` post kérelem argumentumának tartalmaznia kell a `ParameterAssignments` kulcshoz a folyamat paramétereit és azok értékeit tartalmazó szótárt. Emellett az `json` argumentum a következő kulcsokat is tartalmazhatja:

| Kulcs | Leírás |
| --- | --- | 
| `ExperimentName` | A végponthoz társított kísérlet neve |
| `Description` | A végpontot leíró szabadkézi szöveg | 
| `Tags` | A kérelmek címkézésére és feliratozására használható szabadkézi kulcs-érték párok  |
| `DataSetDefinitionValueAssignments` | Az adatkészletek átképzés nélküli módosítására használt szótár (lásd az alábbi vitát) | 
| `DataPathAssignments` | A datapaths átképzés nélküli módosítására használt szótár (lásd az alábbi vitát) | 

### <a name="run-a-published-pipeline-using-c"></a>Közzétett folyamat futtatása C használatával # 

A következő kód bemutatja, hogyan hívható aszinkron módon a folyamat a C#-ból. A részleges kódrészlet csak azt mutatja, hogy a hívás szerkezete nem része egy Microsoft-mintának. Nem jeleníti meg a teljes osztályokat vagy a hibakezelés. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Közzétett folyamat futtatása a Java használatával

A következő kód egy olyan folyamatot mutat be, amely hitelesítést igényel (lásd: [Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](how-to-setup-authentication.md)). Ha a folyamat nyilvánosan központilag van telepítve, nincs szükség a terméket előállító hívásokra `authKey` . A részleges kódrészlet nem jeleníti meg a Java osztályt és a kivételek kezelésére vonatkozó táblázatokat. A kód `Optional.flatMap` olyan függvények összekapcsolását használja, amelyek üres értéket adhatnak vissza `Optional` . A használata `flatMap` lerövidíti és tisztázza a kódot, de vegye figyelembe, hogy a `getRequestBody()` lenyelési kivételek.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Adatkészletek és datapaths módosítása átképzés nélkül

Előfordulhat, hogy különböző adatkészleteken és datapaths szeretne betanítani és következtetni. Előfordulhat például, hogy egy kisebb adatkészletre szeretne betanítani, de a teljes adatkészletre következtetni fog. Az adatkészleteket a `DataSetDefinitionValueAssignments` kérelem argumentumában szereplő kulccsal kell váltania `json` . A datapaths kapcsolót a rel kell váltania `DataPathAssignments` . Mindkét módszer a következőhöz hasonló:

1. A folyamat definíciós parancsfájljában hozzon létre egy `PipelineParameter` adatkészletet. Hozza létre a `DatasetConsumptionConfig` vagy a `DataPath` következőt `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. A ML-szkriptben a használatával érheti el a dinamikusan megadott adatkészletet `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Figyelje meg, hogy az ML-szkript a () értékhez a () értéket adja meg `DatasetConsumptionConfig` `tabular_dataset` , nem pedig a `PipelineParameter` () értéket `tabular_ds_param` .

1. A folyamat definíciós parancsfájljában állítsa a `DatasetConsumptionConfig` paramétert a következőre `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Ha dinamikusan szeretné váltani az adatkészleteket a következtetésben lévő REST-hívásban, használja a következőt `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

Az [adatkészletet és a PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) bemutató jegyzetfüzetek, valamint a [DataPath és a PipelineParameter bemutatása](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) teljes példát mutat erre a technikára.

## <a name="create-a-versioned-pipeline-endpoint"></a>Verzióval ellátott folyamat végpontjának létrehozása

A folyamat végpontja több közzétett folyamattal is létrehozható. Ez a módszer egy rögzített REST-végpontot biztosít, amellyel megismételheti és frissítheti a ML-folyamatokat.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Feladatok elküldése egy folyamat végpontjának

Elküldheti a feladatokat egy folyamat-végpont alapértelmezett verziójára:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

A feladatokat egy adott verzióra is elküldheti:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Ugyanezt a REST API használatával is elvégezheti:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Közzétett folyamatok használata a Studióban

A közzétett folyamatokat a studióból is futtathatja:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. [Megtekintheti a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a **végpontok** lehetőséget.

1. A felső részen válassza a **folyamat-végpontok** lehetőséget.
 ![a Machine learning által közzétett folyamatok listája](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Válasszon ki egy adott folyamatot a folyamat végpontjának korábbi futtatásainak futtatásához, felhasználásához vagy áttekintéséhez.

## <a name="disable-a-published-pipeline&quot;></a>Közzétett folyamat letiltása

Ha el szeretné rejteni egy folyamatot a közzétett folyamatok listájáról, tiltsa le azt a Studióban vagy az SDK-ban:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id=&quot;068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

A használatával újra engedélyezheti `p.enable()` . További információ: PublishedPipeline- [osztály](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline) referenciája.

## <a name="next-steps"></a>Következő lépések

- [Ezeket a Jupyter-jegyzetfüzeteket a githubon](https://aka.ms/aml-pipeline-readme) a gépi tanulási folyamatok további megismeréséhez használhatja.
- Tekintse meg a [azureml-pipeline-Core](/python/api/azureml-pipeline-core/) csomag és a azureml-folyamatok – [STEPs](/python/api/azureml-pipeline-steps/) csomag SDK-referenciáját.
- A folyamatokkal kapcsolatos hibakeresési és hibaelhárítási tippekért lásd: [útmutató](how-to-debug-pipelines.md) .