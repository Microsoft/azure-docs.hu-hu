---
title: Ügyfél létrehozása webszolgáltatásként üzembe helyezett modellhez
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hívhat meg egy webszolgáltatás-végpontot, amely a modell üzembe helyezésekor jött létre a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 8cab9331cdd4c15ab76b7c33f956be15ec2259ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861192"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Azure Machine Learning-modell felhasználása webszolgáltatásként


Ha egy Azure Machine Learning-modellt webszolgáltatásként helyez üzembe, létrejön egy REST API-végpont. Adatokat küldhet ennek a végpontnak, és megkaphatja a modell által visszaküldött előrejelzést. Ebből a dokumentumból megtudhatja, hogyan hozhat létre ügyfeleket a webszolgáltatáshoz a C#, a Go, a Java és a Python használatával.

Webszolgáltatást akkor hoz létre, amikor modellt helyez üzembe a helyi környezetben, Azure Container Instances, Azure Kubernetes Service vagy helyszínen programozható kaputömbökben (FPGA). A webszolgáltatás eléréséhez használt URI-t a Azure Machine Learning [SDK-val lehet lekérni.](/python/api/overview/azure/ml/intro) Ha a hitelesítés engedélyezve van, az SDK-val is lekérte a hitelesítési kulcsokat vagy jogkivonatokat.

A Machine Learning webszolgáltatást használó ügyfelek létrehozásának általános munkafolyamata a következő:

1. A kapcsolati adatok lekért adatok az SDK-val.
1. A modell által használt kérelemadatok típusának meghatározása.
1. Hozzon létre egy alkalmazást, amely a webszolgáltatást hívja meg.

> [!TIP]
> A dokumentum példái az OpenAPI- (Swagger-) specifikációk használata nélkül, manuálisan vannak létrehozva. Ha engedélyezte az OpenAPI-specifikációt az üzemelő példányhoz, a [swagger-codegen](https://github.com/swagger-api/swagger-codegen) és hasonló eszközök használatával ügyfélkódtárakat hozhat létre a szolgáltatáshoz.

## <a name="connection-information"></a>Kapcsolati adatok

> [!NOTE]
> A Azure Machine Learning SDK-val lekért webszolgáltatás-információk. Ez egy Python SDK. A szolgáltatáshoz bármilyen nyelvet használhat ügyfél létrehozásához.

Az [azureml.core.Webservice](/python/api/azureml-core/azureml.core.webservice%28class%29) osztály biztosítja az ügyfél létrehozásához szükséges információkat. Az alábbi `Webservice` tulajdonságok hasznosak az ügyfélalkalmazások létrehozásához:

* `auth_enabled` - Ha a kulcshitelesítés engedélyezve van, `True` ; egyébként `False` .
* `token_auth_enabled` - Ha a jogkivonat-hitelesítés engedélyezve van, `True` ; egyébként `False` .
* `scoring_uri` – A REST API címe.
* `swagger_uri` – Az OpenAPI-specifikáció címe. Ez az URI akkor érhető el, ha engedélyezte az automatikus sémagenerációt. További információ: [Modellek üzembe helyezése a Azure Machine Learning.](how-to-deploy-and-where.md)

Az üzembe helyezett webszolgáltatásokhoz többféleképpen is lekérheti ezt az információt:

# <a name="python"></a>[Python](#tab/python)

* Modell üzembe helyezésekor a rendszer egy objektumot ad vissza a `Webservice` szolgáltatásra vonatkozó információkkal:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* A használatával lekérheti a munkaterületen lévő modellek üzembe `Webservice.list` helyezett webszolgáltatásának listáját. Szűrőkkel szűkítheti a visszaadott információk listáját. A szűrhető adatokról a [Webservice.list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice) referenciadokumentációban talál további információt.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Ha ismeri az üzembe helyezett szolgáltatás nevét, létrehozhatja a új példányát, és paraméterekként meg kell adnia a munkaterület és `Webservice` a szolgáltatás nevét. Az új objektum az üzembe helyezett szolgáltatásról tartalmaz információkat.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha ismeri az üzembe helyezett szolgáltatás nevét, használja [az az ml service show](/cli/azure/ml/service#az_ml_service_show) parancsot:

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Machine Learning stúdió válassza a __Végpontok,__ a __Valós idejű__ végpontok lehetőséget, majd a végpont nevét. A végpont részleteiben a __REST-végpont__ mező tartalmazza a pontozási URI-t. A __Swagger URI tartalmazza__ a swagger URI-t.

---

Az alábbi táblázat bemutatja, hogyan néznek ki ezek az URI-k:

| URI típusa | Példa |
| ----- | ----- |
| Pontozási URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> Az üzemelő példány IP-címe eltérő lesz. Minden AKS-fürtnek saját IP-címe lesz, amelyet a fürt üzemelő példányai közösen osztják meg.

### <a name="secured-web-service"></a>Biztonságos webszolgáltatás

Ha az üzembe helyezett webszolgáltatást TLS/SSL-tanúsítvánnyal biztosította, a [HTTPS](https://en.wikipedia.org/wiki/HTTPS) használatával csatlakozhat a szolgáltatáshoz a pontozási vagy swagger URI használatával. A HTTPS a kettő közötti kommunikáció titkosítása révén segíti az ügyfél és a webszolgáltatás közötti kommunikáció biztonságát. A titkosítás a [Transport Layer Security (TLS) protokollt használja.](https://en.wikipedia.org/wiki/Transport_Layer_Security) A TLS-t néha továbbra *is SSL* (SSL) néven is nevezik, amely a TLS elődje volt.

> [!IMPORTANT]
> A által üzembe helyezett Azure Machine Learning csak a TLS 1.2-es verzióját támogatják. Ügyfélalkalmazás létrehozásakor győződjön meg arról, hogy az támogatja ezt a verziót.

További információkért lásd: [Webszolgáltatás biztonságossá](how-to-secure-web-service.md)tere a TLS használatával a Azure Machine Learning.

### <a name="authentication-for-services"></a>Szolgáltatások hitelesítése

Azure Machine Learning két módszert kínál a webszolgáltatásokhoz való hozzáférés szabályozására.

|Hitelesítési módszer|Aci|AKS|
|---|---|---|
|Kulcs|Alapértelmezés szerint le van tiltva| Alapértelmezés szerint engedélyezett|
|Jogkivonat| Nem érhető el| Alapértelmezés szerint le van tiltva |

Amikor egy kulccsal vagy jogkivonattal védett szolgáltatásnak  küld kérést, az Engedélyezés fejléccel adja át a kulcsot vagy a jogkivonatot. A kulcsot vagy a jogkivonatot a következő formátumban kell `Bearer <key-or-token>` formázni: , ahol a a kulcs vagy a `<key-or-token>` jogkivonat értéke.

A kulcsok és a jogkivonatok közötti elsődleges különbség az, hogy a kulcsok statikusak, és manuálisan újragenerálhatóak, és a jogkivonatokat a lejáratkor frissíteni **kell.** A kulcsalapú hitelesítés az Azure Container Instance és Azure Kubernetes Service webszolgáltatások esetén támogatott, a  jogkivonat-alapú hitelesítés pedig csak a Azure Kubernetes Service érhető el. A hitelesítés konfigurálásával kapcsolatos további információkért lásd: [Configure authentication for models deployed as web services (Hitelesítés](how-to-authenticate-web-service.md)konfigurálása webszolgáltatásként üzembe helyezett modellekhez).


#### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Amikor engedélyezi a hitelesítést egy központi telepítéshez, automatikusan létrehozza a hitelesítési kulcsokat.

* A hitelesítés alapértelmezés szerint engedélyezve van a központi telepítés Azure Kubernetes Service.
* A hitelesítés alapértelmezés szerint le van tiltva, amikor üzembe helyez egy Azure Container Instances.

A hitelesítés vezérléséhez használja a paramétert az üzemelő példány `auth_enabled` létrehozásakor vagy frissítésekkor.

Ha a hitelesítés engedélyezve van, a módszerrel lekérhet egy elsődleges `get_keys` és egy másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra létre kell hoznia egy kulcsot, használja a [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) et.

#### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználónak meg kell adnia Azure Machine Learning JWT-jogkivonatot a webszolgáltatásnak a hozzáféréshez. 

* A jogkivonatok hitelesítése alapértelmezés szerint le van tiltva, amikor üzembe helyez egy Azure Kubernetes Service.
* A jogkivonat-hitelesítés nem támogatott az üzembe helyezés Azure Container Instances.

A jogkivonat-hitelesítés vezérléséhez használja a paramétert az üzemelő példány `token_auth_enabled` létrehozásakor vagy frissítésekkor.

Ha a jogkivonat-hitelesítés engedélyezve van, a metódussal lekérheti a jogkivonatot `get_token` és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

Ha az [Azure CLI-t](reference-azure-machine-learning-cli.md)és a Machine Learning-bővítményt használja, a következő paranccsal kaphat jogkivonatot:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Jelenleg a jogkivonat lekérésének egyetlen módja a Azure Machine Learning SDK vagy az Azure CLI machine learning bővítmény használata.

A jogkivonat lekérése után új jogkivonatot kell `refresh_by` kérnie. 

## <a name="request-data"></a>Adatok kérése

A REST API szerint a kérelem törzse egy JSON-dokumentum lesz, amely a következő struktúrával rendelkezik:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Az adatok struktúrájának meg kell egyeznie a pontozó szkript és a modell által a szolgáltatásban elvárt struktúrával. A pontozó szkript módosíthatja az adatokat, mielőtt továbbkik a modellnek.

### <a name="binary-data"></a>Bináris adatok

A bináris adatok szolgáltatásban való támogatásának engedélyezésével kapcsolatos információkért lásd: [Bináris adatok.](how-to-deploy-advanced-entry-script.md#binary-data)

> [!TIP]
> A bináris adatok támogatása az üzembe score.py modell által használt fájlban történik. Az ügyfélről használja a programozási nyelv HTTP-funkcióját. Az alábbi kódrészlet például egy JPG-fájl tartalmát küldi el egy webszolgáltatásnak:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztása (CORS)

A CORS-támogatás a szolgáltatásban való engedélyezésével kapcsolatos további információkért lásd: Több forrásból való [erőforrás-megosztás.](how-to-deploy-advanced-entry-script.md#cors)

## <a name="call-the-service-c"></a>Szolgáltatás hívása (C#)

Ez a példa bemutatja, hogyan hívható meg a betanítás jegyzetfüzetben példa a C# [használatával:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

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

A visszaadott eredmények az alábbi JSON-dokumentumhoz hasonlók:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Szolgáltatás hívása (Go)

Ez a példa bemutatja, hogyan hívható meg a Go a Betanítás a jegyzetfüzetben példából létrehozott [webszolgáltatással:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb)

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

A visszaadott eredmények az alábbi JSON-dokumentumhoz hasonlók:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Szolgáltatás hívása (Java)

Ez a példa bemutatja, hogyan hívhatja meg a Betanítás a [jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) példában létrehozott webszolgáltatást a Java használatával:

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

A visszaadott eredmények az alábbi JSON-dokumentumhoz hasonlók:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>A szolgáltatás hívása (Python)

Ez a példa bemutatja, hogyan hívható meg a Betanítás jegyzetfüzetben példa alapján létrehozott webszolgáltatás [a](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) Python használatával:

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

A visszaadott eredmények az alábbi JSON-dokumentumhoz hasonlók:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Webszolgáltatás-séma (OpenAPI-specifikáció)

Ha automatikus sémagenerációt használt az üzemelő példányhoz, a szolgáltatás OpenAPI-specifikációjának címét a swagger_uri [használatával kaphatja meg.](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) (Például: `print(service.swagger_uri)` .) Get kérés használatával, vagy nyissa meg az URI-t egy böngészőben a specifikáció lekéréseként.

Az alábbi JSON-dokumentum egy üzembe helyezéshez létrehozott sémára (OpenAPI-specifikációra) mutat példát:

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

További információ: [OpenAPI-specifikáció.](https://swagger.io/specification/)

Ha olyan segédprogramot keres, amely ügyfélkódtárakat tud létrehozni a specifikációból, tekintse meg a [swagger-codegen kódot.](https://github.com/swagger-api/swagger-codegen)


> [!TIP]
> A szolgáltatás üzembe helyezése után lekérheti a séma JSON-dokumentumát. Az [swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) webszolgáltatás (például ) swagger_uri tulajdonságának használatával lekérte a helyi `service.swagger_uri` webszolgáltatás Swagger-fájljában található URI-t.

## <a name="consume-the-service-from-power-bi"></a>A szolgáltatás Power BI

Power BI támogatja a Azure Machine Learning- és webszolgáltatás-használatot, hogy az adatok előrejelzésekkel Power BI gazdagítsa az adatokat. 

A Power BI által támogatott webszolgáltatás létrehozásához a sémának támogatnia kell a Power BI. [Megtudhatja, hogyan hozhat létre Power BI által támogatott sémát.](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint)

A webszolgáltatás az üzembe helyezése után a Power BI-adatfolyamokból használható. [Ismerje meg, hogyan használható Azure Machine Learning webszolgáltatás a Power BI-ból](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Következő lépések

A Python- és mélytanulási modellek valós idejű pontozásának referenciaarchitektúráját az Azure Architecture [Centerben megtekintheti.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
