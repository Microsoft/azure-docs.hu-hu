---
title: Ügyfél létrehozása webszolgáltatásként üzembe helyezett modellhez
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hívhat meg egy webszolgáltatási végpontot, amely akkor jött létre, amikor a modell telepítése Azure Machine Learningról történik.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: e9fb801fce3e47fc83febeddd6f331ce2af207e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506973"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Azure Machine Learning-modell felhasználása webszolgáltatásként


Ha egy Azure Machine Learning-modellt webszolgáltatásként helyez üzembe, létrejön egy REST API-végpont. Adatokat küldhet ennek a végpontnak, és megkaphatja a modell által visszaküldött előrejelzést. Ebből a dokumentumból megtudhatja, hogyan hozhat létre ügyfeleket a webszolgáltatáshoz a C#, a go, a Java és a Python használatával.

Webszolgáltatást akkor hozhat létre, ha a helyi környezetbe, Azure Container Instancesba, Azure Kubernetes szolgáltatásba vagy mezőre programozható Gate tömbökbe (FPGA) helyezi üzembe a modellt. A webszolgáltatáshoz való hozzáféréshez használt URI-t a [Azure Machine learning SDK](/python/api/overview/azure/ml/intro)-val kéri le. Ha a hitelesítés engedélyezve van, az SDK használatával is beolvashatja a hitelesítési kulcsokat vagy jogkivonatokat.

A Machine learning-webszolgáltatást használó ügyfelek létrehozásának általános munkafolyamata a következő:

1. A kapcsolódási adatok beszerzéséhez használja az SDK-t.
1. Határozza meg a modell által használt kérelem-adattípust.
1. Hozzon létre egy alkalmazást, amely meghívja a webszolgáltatást.

> [!TIP]
> A dokumentumban szereplő példákat manuálisan hozza létre a OpenAPI (hencegés) specifikációk használata nélkül. Ha engedélyezte a OpenAPI-specifikációt a központi telepítéshez, olyan eszközöket használhat, mint például a [hencegés-CODEGEN](https://github.com/swagger-api/swagger-codegen) a szolgáltatáshoz tartozó ügyféloldali kódtárak létrehozásához.

## <a name="connection-information"></a>Kapcsolatok adatai

> [!NOTE]
> A webszolgáltatási információk beszerzéséhez használja a Azure Machine Learning SDK-t. Ez egy Python SDK. A szolgáltatáshoz bármilyen nyelven létrehozhat egy ügyfelet.

A [azureml. Core. webszolgáltatási](/python/api/azureml-core/azureml.core.webservice%28class%29) osztály a-ügyfél létrehozásához szükséges információkat tartalmazza. Az `Webservice` ügyfélalkalmazások létrehozásához a következő tulajdonságok hasznosak:

* `auth_enabled` – Ha a kulcsos hitelesítés engedélyezve van, `True` egyéb esetben `False` .
* `token_auth_enabled` – Ha engedélyezve van a jogkivonat-hitelesítés, `True` más esetben `False` .
* `scoring_uri` – A REST API címe.
* `swagger_uri` – A OpenAPI-specifikáció címe. Ez az URI akkor érhető el, ha engedélyezte az automatikus séma létrehozását. További információ: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

Ezen információk több módon is beolvashatók az üzembe helyezett webszolgáltatásokhoz:

# <a name="python"></a>[Python](#tab/python)

* Modell telepítésekor a rendszer egy `Webservice` objektumot ad vissza a szolgáltatással kapcsolatos információkkal:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* A paranccsal `Webservice.list` lekérheti az üzembe helyezett webszolgáltatások listáját a munkaterületen. Szűrőket adhat hozzá a visszaadott információk listájának szűkítéséhez. További információ arról, hogy mit lehet szűrni [. a webszolgáltatások listáját](/python/api/azureml-core/azureml.core.webservice.webservice.webservice) ismertető dokumentációban talál további információt.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Ha ismeri a telepített szolgáltatás nevét, létrehozhat egy új példányt `Webservice` , és paraméterként megadhatja a munkaterület és a szolgáltatás nevét. Az új objektum a központilag telepített szolgáltatással kapcsolatos információkat tartalmaz.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha ismeri a telepített szolgáltatás nevét, használja az az [ml Service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) parancsot:

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

Azure Machine Learning studióból válassza a __végpontok__, a __valós idejű végpontok__, majd a végpont nevét. A végpont részleteit a REST- __végpont__ mező tartalmazza a pontozási URI-t. A __hencegés URI__ -ja tartalmazza a HENCEGő URI-t.

---

A következő táblázat az URI-k megjelenését szemlélteti:

| URI-típus | Példa |
| ----- | ----- |
| Pontozási URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Hencegő URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> Az IP-cím eltérő lesz az üzemelő példánynál. Mindegyik AK-fürt saját IP-címmel rendelkezik, amelyet az adott fürtön üzemelő központi telepítések közösen kapnak.

### <a name="secured-web-service"></a>Biztonságos webszolgáltatás

Ha a telepített webszolgáltatást TLS/SSL-tanúsítvánnyal védi, [https](https://en.wikipedia.org/wiki/HTTPS) használatával kapcsolódhat a szolgáltatáshoz a pontozási vagy a hencegő URI használatával. A HTTPS segíti a kommunikációt az ügyfél és a webszolgáltatás között a kettő közötti kommunikáció titkosításával. A titkosítás [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)protokollt használ. A TLS-t néha *SSL* (SSL) néven is emlegetik, amely a TLS elődje volt.

> [!IMPORTANT]
> A Azure Machine Learning által központilag telepített webszolgáltatások támogatják a TLS 1,2-es verzióját. Ügyfélalkalmazás létrehozásakor győződjön meg arról, hogy az támogatja ezt a verziót.

További információkért lásd: [webszolgáltatások biztonságossá tétele a TLS használatával Azure Machine Learningon keresztül](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Szolgáltatások hitelesítése

A Azure Machine Learning két módszert biztosít a webszolgáltatásokhoz való hozzáférés vezérlésére.

|Hitelesítési módszer|ACI|AKS|
|---|---|---|
|Kulcs|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezett|
|Jogkivonat| Nem érhető el| Alapértelmezés szerint letiltva |

Egy kulccsal vagy jogkivonattal védett szolgáltatásnak küldött kérelem küldésekor az __engedélyezési__ fejléc használatával adja át a kulcsot vagy a jogkivonatot. A kulcsot vagy jogkivonatot úgy kell formázni `Bearer <key-or-token>` , hogy hol `<key-or-token>` található a kulcs vagy a jogkivonat értéke.

A kulcsok és a tokenek közötti elsődleges különbség az, hogy a **kulcsok statikusak, és manuálisan** is újrahozhatók, és **a tokeneket a lejárat után frissíteni** kell. A kulcs alapú hitelesítés az Azure Container instance és az Azure Kubernetes Service szolgáltatásban telepített webszolgáltatások esetében támogatott, és a jogkivonat-alapú hitelesítés **csak** az Azure Kubernetes Service-példányok esetében érhető el. A hitelesítés konfigurálásával kapcsolatos további információkért lásd: a [webszolgáltatásként üzembe helyezett modellek hitelesítésének konfigurálása](how-to-authenticate-web-service.md).


#### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha engedélyezi a hitelesítést a központi telepítéshez, automatikusan létrehozza a hitelesítési kulcsokat.

* A hitelesítés alapértelmezés szerint engedélyezve van az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A hitelesítés alapértelmezés szerint le van tiltva, amikor Azure Container Instances telepíti.

A hitelesítés vezérléséhez használja a `auth_enabled` paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha engedélyezve van a hitelesítés, a metódus használatával kérhet `get_keys` le elsődleges és másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell létrehoznia egy kulcsot, használja a következőt: [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) .

#### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználónak meg kell adnia egy Azure Machine Learning JWT tokent a webszolgáltatásnak az eléréséhez. 

* Alapértelmezés szerint a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés nem támogatott a Azure Container Instances való telepítésekor.

A jogkivonat-hitelesítés vezérléséhez használja a `token_auth_enabled` paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha engedélyezve van a jogkivonat-hitelesítés, a `get_token` metódussal kérheti le a tulajdonosi jogkivonatot és a jogkivonatok lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

Ha rendelkezik az [Azure CLI-vel és a Machine learning bővítménnyel](reference-azure-machine-learning-cli.md), a következő paranccsal kérhet le jogkivonatot:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Jelenleg a jogkivonat lekérésének egyetlen módja a Azure Machine Learning SDK vagy az Azure CLI Machine learning bővítmény használata.

A jogkivonat időpontját követően új jogkivonatot kell kérnie `refresh_by` . 

## <a name="request-data"></a>Adatkérés

A REST API azt várja, hogy a kérelem törzse JSON-dokumentum legyen a következő szerkezettel:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Az adatok struktúrájának meg kell egyeznie a szolgáltatás által várt pontozási szkripttel és modellel. Előfordulhat, hogy a pontozási parancsfájl módosítja az adattípust, mielőtt átadná azt a modellnek.

### <a name="binary-data"></a>Bináris adatok

További információ a szolgáltatásban található bináris adatok támogatásának engedélyezéséről: [bináris adatok](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> A bináris adattípusok támogatásának engedélyezése a telepített modell által használt score.py fájlban történik. Az ügyfélen használja a programozási nyelv HTTP-funkcióit. Az alábbi kódrészlet például egy JPG-fájl tartalmát küldi el egy webszolgáltatásnak:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)

Az CORS-támogatás szolgáltatásban való engedélyezésével kapcsolatos információkért lásd: a [több eredetű erőforrás-megosztás](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>A szolgáltatás meghívása (C#)

Ez a példa azt mutatja be, hogy a C# használatával hogyan hívhatja meg a vonattal létrehozott webszolgáltatást a [notebookon belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) . példa:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

A visszaadott eredmények a következő JSON-dokumentumhoz hasonlóak:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>A szolgáltatás meghívása (go)

Ez a példa azt szemlélteti, hogyan használhatja a Go-t a [vonattal a jegyzetfüzetből](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) létrehozott webszolgáltatás meghívásához:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

A visszaadott eredmények a következő JSON-dokumentumhoz hasonlóak:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>A szolgáltatás meghívása (Java)

Ebből a példából megtudhatja, hogyan használhatja a Java-t a [vonattal a notebookon belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) létrehozott webszolgáltatás meghívására:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

A visszaadott eredmények a következő JSON-dokumentumhoz hasonlóak:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Szolgáltatás meghívása (Python)

Ez a példa bemutatja, hogyan használhatja a Pythont a [vonattal a notebookon belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) létrehozott webszolgáltatás meghívásához:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

A visszaadott eredmények a következő JSON-dokumentumhoz hasonlóak:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Webszolgáltatás sémája (OpenAPI-specifikáció)

Ha a központi telepítéshez automatikus sémát használ, a [swagger_uri tulajdonság](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri)használatával lekérheti a szolgáltatás OpenAPI-specifikációjának a címeit. (Például: `print(service.swagger_uri)` .) Használja a GET kérelmet, vagy nyissa meg az URI-t egy böngészőben a specifikáció lekéréséhez.

A következő JSON-dokumentum egy példa egy központi telepítéshez létrehozott sémára (OpenAPI-specifikáció):

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

További információ: OpenAPI- [specifikáció](https://swagger.io/specification/).

Egy olyan segédprogram esetében, amely a specifikációból tud ügyféloldali kódtárakat létrehozni, tekintse meg a következőt: [hencegés-CODEGEN](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> A séma JSON-dokumentum a szolgáltatás telepítése után kérhető le. Használja a központilag telepített webszolgáltatás [swagger_uri tulajdonságát](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) (például) a `service.swagger_uri` helyi webszolgáltatás hencegő fájljához tartozó URI-azonosító lekéréséhez.

## <a name="consume-the-service-from-power-bi"></a>A szolgáltatás felhasználása Power BI

A Power BI támogatja a Azure Machine Learning webszolgáltatások felhasználását, hogy az előrejelzésekkel gazdagítsa Power BI az adatmennyiséget. 

Ha olyan webszolgáltatást szeretne előállítani, amelyet a Power BIban való használathoz támogat, a sémának támogatnia kell a Power BI számára szükséges formátumot. [Megtudhatja, hogyan hozhat létre Power bi által támogatott sémát](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

A webszolgáltatás az üzembe helyezése után a Power BI-adatfolyamokból használható. [Ismerje meg, hogyan használható Azure Machine Learning webszolgáltatás a Power BI-ból](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Következő lépések

A Python és a Deep learning modellek valós idejű pontozására szolgáló hivatkozási architektúra megtekintéséhez nyissa meg az [Azure Architecture centert](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
