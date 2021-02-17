---
title: Ismerkedés a dokumentumok fordításával
description: Dokumentum-fordítási szolgáltatás létrehozása a C#, a go, a Java, a Node.js vagy a Python programozási nyelveken és platformokon
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: f6b68ea756253a30f006983c214e287c75637278
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642373"
---
# <a name="get-started-with-document-translation-preview"></a>Ismerkedés a dokumentumok fordításával (előzetes verzió)

 Ebből a cikkből megtudhatja, hogyan használhatja a dokumentumok fordítását HTTP REST API módszerekkel. A dokumentum fordítása az [Azure Translator](../translator-info-overview.md) Service felhőalapú szolgáltatása.  A dokumentum-fordítási API lehetővé teszi a teljes dokumentumok fordítását a forrás dokumentum-struktúra és a szövegformázás megőrzése mellett.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Aktív [**Azure-fiók**](https://azure.microsoft.com/free/cognitive-services/).  Ha még nem rendelkezik ilyennel, [**létrehozhat egy ingyenes fiókot**](https://azure.microsoft.com/free/).

* Egy [**Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) Service-erőforrás (**nem** Cognitive Services erőforrás). 

* Egy [**Azure Blob Storage-fiók**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik.

> [!NOTE]
> A dokumentumok fordítása jelenleg csak a Translator (Single-Service) erőforrásban támogatott, **nem** pedig a Cognitive Services (több szolgáltatás) erőforrás.

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Egyéni tartománynév és előfizetési kulcs beszerzése

> [!IMPORTANT]
>
> * Nem használhatja a végpontot a Azure Portal erőforrás- _kulcsok és a végpontok_ oldalán, sem a globális Translator végponton –, `api.cognitive.microsofttranslator.com` hogy http-kéréseket lehessen felvenni a dokumentumok fordítására.
> * **A dokumentum-fordítási szolgáltatáshoz tartozó összes API-kérelemhez egyéni tartományi végpont szükséges**.

### <a name="what-is-the-custom-domain-endpoint"></a>Mi az egyéni tartomány végpontja? 

Az egyéni tartomány végpontja az erőforrás neve, az állomásnév és a fordító alkönyvtárral formázott URL-cím:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Egyéni tartománynév megkeresése

A **Name (** *Egyéni tartománynév*) paraméter a **Name (név** ) mezőben a fordítói erőforrás létrehozásakor megadott érték.

:::image type="content" source="../media/instance-details.png" alt-text="A Azure Portal képe, erőforrás létrehozása, azonnali részletek, név mező.":::

### <a name="get-your-subscription-key"></a>Az előfizetési kulcs beszerzése

A Translator Service-kérelmekhez csak olvasható kulcs szükséges a hozzáférés hitelesítéséhez.

1. Ha létrehozott egy új erőforrást, az üzembe helyezés után válassza **az Ugrás az erőforráshoz** lehetőséget. Ha meglévő dokumentum-fordítási erőforrással rendelkezik, navigáljon közvetlenül az erőforrás lapjához.
1. A bal oldali sín erőforrás- *kezelés* területén válassza a **kulcsok és végpont** lehetőséget.
1. Másolja és illessze be az előfizetési kulcsot egy kényelmes helyre, például a *Microsoft Jegyzettömbbe*.
1. Az alábbi kód bebeillesztésével hitelesítheti kérelmét a dokumentum-fordítási szolgáltatásban.

:::image type="content" source="../media/translator-keys.png" alt-text="Az előfizetési kulcs lekérése mezőjének képe Azure Portal.":::

## <a name="create-your-azure-blob-storage-containers"></a>Azure Blob Storage-tárolók létrehozása

[**Létre kell hoznia egy tárolót**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) az [**Azure Blob Storage-fiókban**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) a forrás, a cél és a nem kötelező Szószedet-fájlok számára.

* **Forrás tároló**. Ebben a tárolóban töltheti fel a fájlokat fordításra (kötelező).
* **Cél tároló**. Ebben a tárolóban lesznek tárolva a lefordított fájlok (kötelező).  
* **Szószedet-tároló**. Ebben a tárolóban töltheti fel a Szószedet fájljait (opcionális).  

*Lásd:* **sas hozzáférési jogkivonatok létrehozása dokumentum fordításához**

A `sourceUrl` , `targetUrl` , és opcionálisan `glossaryUrl`  tartalmaznia kell egy közös hozzáférésű aláírás (SAS) tokent, amely lekérdezési karakterláncként van hozzáfűzve. A jogkivonat hozzárendelhető a tárolóhoz vagy egy adott blobhoz.

* A **forrás** tárolónak vagy a blobnak kijelölt  **olvasási** és **listázási** hozzáféréssel kell rendelkeznie.
* A **célként** megadott tárolónak vagy blobnak a kijelölt  **írási** és **listázási** hozzáféréssel kell rendelkeznie.
* A **Szószedet** tárolójának vagy blobjának kijelölt  **olvasási** és **listázási** hozzáféréssel kell rendelkeznie.

> [!TIP]
>
> * Ha **több** fájlt (blobot) fordít egy műveletbe, **az SAS-hozzáférés delegálása a tároló szintjén**.  
> * Ha egy műveletben **egyetlen** fájlt (blobot) fordít le, **az SAS-hozzáférés delegálása a blob szintjén**.  
>

## <a name="set-up-your-coding-platform"></a>A kódolási platform beállítása

### <a name="c"></a>[C#](#tab/csharp)

* Új projekt létrehozása.
* Cserélje le az Program.cs-t az alább látható C#-kódra.
* Állítsa be a végpontot. az előfizetési kulcs és a tároló URL-értékei a Program.cs-ben.
* A JSON-adatfeldolgozáshoz vegyen fel [Newtonsoft.Jsa csomagba a .net CLI használatával](https://www.nuget.org/packages/Newtonsoft.Json/).
* Futtassa a programot a projekt könyvtárából.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Hozzon létre egy új Node.js projektet.
* Telepítse a Axios könyvtárat a-val `npm i axios` .
* Másolja be az alábbi kódot a projektbe.
* Állítsa be a végpontot, az előfizetési kulcsot és a tároló URL-értékeit.
* Futtassa a programot.

### <a name="python"></a>[Python](#tab/python)  

* Új projekt létrehozása.
* Másolja és illessze be a kódot az egyik mintából a projektbe.
* Állítsa be a végpontot, az előfizetési kulcsot és a tároló URL-értékeit.
* Futtassa a programot. Példa: `python translate.py`.
  
### <a name="java"></a>[Java](#tab/java)

* Hozzon létre egy munkakönyvtárat a projekthez. Például:

```powershell
mkdir sample-project
```

* A projekt könyvtárában hozza létre a következő alkönyvtár-struktúrát:  

  src</br>
&emsp; └ Main</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

* A Java-forrásfájlok (például a _sample. Java_) a src/Main/**Java** nyelven élnek.

* A gyökérkönyvtárban (például *minta-projekt*) inicializálja a projektet a Gradle:

```powershell
gradle init --type basic
```

* Amikor a rendszer rákérdez a **DSL** kiválasztására, válassza a **Kotlin** lehetőséget.
* Frissítse a `build.gradle.kts`  fájlt. Ne feledje, hogy a mintától függően frissítenie kell a következőt `mainClassName` :

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Hozzon létre egy Java-fájlt a **Java** könyvtárban, és másolja/illessze be a kódot a megadott mintából. Ne felejtse el felvenni az előfizetési kulcsot és a végpontot.
Hozza **létre és futtassa a mintát a gyökérkönyvtárból**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Ugrás](#tab/go)  

* Hozzon létre egy új go-projektet.
* Adja hozzá az alábbi kódot.
* Állítsa be a végpontot, az előfizetési kulcsot és a tároló URL-értékeit.
* Mentse a fájlt „.go” kiterjesztéssel.
* Nyisson meg parancssort egy számítógépen, amelyen a Go telepítve van.
* Hozza létre a fájlt, például: "Go Build example-code. go".
* Futtassa a fájlt, például: "example-code".

---

## <a name="make-document-translation-requests"></a>Dokumentum-fordítási kérelmek készítése

A Batch-dokumentumok fordítására vonatkozó kérelmet POST kérelem útján küldi el a Translator Service-végpontnak. Ha a művelet sikeres, a POST metódus visszaadja a `202 Accepted`  válaszüzenetet, és a Batch-kérést a szolgáltatás hozza létre.

### <a name="http-headers"></a>HTTP-fejlécek

A következő fejlécek szerepelnek az egyes Document Translator API-kérelmekben:

|HTTP-fejléc|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**Kötelező**: az érték a fordítóhoz vagy Cognitive Services erőforráshoz tartozó Azure-előfizetési kulcs.|
|Content-Type|**Kötelező**: az adattartalom tartalomtípusát adja meg. Az elfogadott értékek az Application/JSON vagy a charset = UTF-8.|
|Content-Length|**Kötelező**: a kérelem törzsének hossza.|

### <a name="post-request-body-properties"></a>POST kérelem törzsének tulajdonságai

* A POST kérelem törzse egy nevű JSON-objektum `inputs` .
* Az `inputs` objektum tartalmazza a  `sourceURL` `targetURL`  forrás-és a cél nyelvi párok és a tárolók címét is, és a `glossaryURL` tároló címét is tartalmazhatja.
* A `prefix` és a `suffix` mezők (opcionális) a tárolóban lévő dokumentumok szűrésére szolgálnak, beleértve a mappákat is.
* A  `glossaries`  (nem kötelező) mező értékét a rendszer a dokumentum fordításakor alkalmazza.
* A `targetUrl` cél nyelvének egyedinek kell lennie.

>[!NOTE]
> Ha már létezik ilyen nevű fájl a célhelyen, a rendszer felülírja.

### <a name="post-a-translation-request"></a>Fordítási kérelem közzététele

> [!IMPORTANT]
>
> * Az alábbi kód-minták esetében előfordulhat, hogy a művelettől függően frissítenie kell a következő mezőket:

>> [!div class="checklist"]
>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (Job ID)
>>
> * A feladatot a `id`  post metódus válasz fejlécének `Operation-Location`  URL-értékében találja. Az URL-cím utolsó paramétere a művelet feladata **`id`** .  
> * A feladatok beolvasása kérelem használatával is lekérheti a feladatot `id`  egy dokumentum-fordítási művelethez.
> * Az alábbi minták esetében a kulcsot és a végpontot a jelzett módon kell megadnia. Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan.  
>
> A hitelesítő adatok biztonságos tárolásához és eléréséhez tekintse meg az [Azure Cognitive Services biztonsága](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) című témakört.

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>POST kérelem törzse opcionális glossaryURL nélkül

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>POST kérelem törzse opcionális glossaryURL

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="_post-document-translation_-request-code-samples"></a>_Dokumentum-fordítási_ kérelem kód mintáinak közzététele

Batch-dokumentum fordítási kérelmének elküldése a fordítási szolgáltatásnak.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Ugrás](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_-code-samples"></a>_Fájlformátumok kódjának beolvasása_ minták

A támogatott fájlformátumok listájának beolvasása. Ha ez sikeres, a metódus visszaadja a `200 OK` Válasz kódját.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ugrás](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_-code-samples"></a>A feladatok állapotára vonatkozó kód mintáinak _beolvasása_

Egy adott feladat aktuális állapotának beolvasása, valamint az összes feladat összefoglalója egy dokumentum-fordítási kérelemben. Ha ez sikeres, a metódus visszaadja a `200 OK` Válasz kódját.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ugrás](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_-code-samples"></a>_Dokumentum állapot-kódjának beolvasása_ minták

### <a name="brief-overview"></a>Rövid áttekintés

Egy adott dokumentum állapotának lekérése egy dokumentum-fordítási kérelemben. Ha ez sikeres, a metódus visszaadja a `200 OK` Válasz kódját.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ugrás](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_-code-samples"></a>Feladatkártya-minták _törlése_

### <a name="brief-overview"></a>Rövid áttekintés

Folyamatban van a feldolgozás vagy a várólistára helyezett feladat megszakítása. Csak azok a dokumentumok lesznek megszakítva, amelyekhez nem indult el a fordítás.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ugrás](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Tartalmi korlátok

Az alábbi táblázat felsorolja a dokumentumok fordítására küldött adatkorlátokat.

|Attribútum | Korlát|
|---|---|
|Dokumentum mérete| ≤ 40 MB |
|A fájlok teljes száma.|≤ 1000 |
|Teljes tartalom mérete egy kötegben | ≤ 250 MB|
|A kötegben lévő célcsoportok száma| ≤ 10 |
|A fordítási memória fájljának mérete| ≤ 10 MB|

> [!NOTE]
> A fenti tartalom korlátai a nyilvános kiadás előtt változhatnak.

## <a name="learn-more"></a>Tudjon meg többet

* [Translator V3 API-referenciák](../reference/v3-0-reference.md)
* [Nyelvi támogatás](../language-support.md)
* [Előfizetések az Azure API Managementban](/azure/api-management/api-management-subscriptions).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Testreszabott nyelvi rendszer létrehozása egyéni fordító használatával](../custom-translator/overview.md)
>
>
