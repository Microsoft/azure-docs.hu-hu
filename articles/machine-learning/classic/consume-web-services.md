---
title: 'ML Studio (klasszikus): webszolgáltatások felhasználása – Azure'
description: Ha a gépi tanulási szolgáltatás központi telepítése Azure Machine Learning Studio (klasszikus), a REST-alapú webszolgáltatás akár valós idejű kérelem-válasz szolgáltatásként, akár batch-végrehajtási szolgáltatásként is felhasználható.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 0dc49265c0ea799e194e4ac7004b558d8a9d4dd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519269"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Machine Learning Studio (klasszikus) webszolgáltatás felhasználása

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik.  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus)   ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Miután telepített egy Azure Machine Learning Studio (klasszikus) prediktív modellt webszolgáltatásként, használhat egy REST API az adatküldés és az előrejelzések beszerzése érdekében. Az adatküldés valós időben vagy batch módban is elvégezhető.

A Machine Learning webszolgáltatások Machine Learning Studio (klasszikus) használatával történő létrehozásáról és üzembe helyezéséről további információt itt talál:

* Az Machine Learning Studio (klasszikus) kísérlet létrehozásával kapcsolatos oktatóanyagért lásd: [az első kísérlet létrehozása](create-experiment.md).
* A webszolgáltatások üzembe helyezésével kapcsolatos részletekért lásd: [Machine learning webszolgáltatás üzembe](deploy-a-machine-learning-web-service.md)helyezése.
* Az általános Machine Learning kapcsolatos további információkért látogasson el a [Machine learning dokumentációs központba](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Áttekintés
A Azure Machine Learning webszolgáltatással egy külső alkalmazás valós időben kommunikál egy Machine Learning munkafolyamat-pontozási modellel. Egy Machine Learning webszolgáltatás hívása egy külső alkalmazásnak adja vissza az előrejelzési eredményeket. Machine Learning webszolgáltatás-hívás létrehozásához át kell adnia egy, az előrejelzés központi telepítésekor létrehozott API-kulcsot. A Machine Learning webszolgáltatás a webes programozási projektek számára népszerű architektúrán alapul.

A Azure Machine Learning Studio (klasszikus) két típusú szolgáltatást tartalmaz:

* Request-Response Service (RR) – alacsony késésű, rugalmasan méretezhető szolgáltatás, amely felületet biztosít a Machine Learning Studio (klasszikus) által létrehozott és üzembe helyezett állapot nélküli modellekhez.
* Batch végrehajtási szolgáltatás (BES) – egy aszinkron szolgáltatás, amely az adatrekordok kötegét szerzi be.

A Machine Learning webszolgáltatásokkal kapcsolatos további információkért lásd: [Machine learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Engedélyezési kulcs beszerzése
A kísérlet telepítésekor az API-kulcsok jönnek létre a webszolgáltatáshoz. A kulcsokat több helyről is lekérheti.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A Microsoft Azure Machine Learning webszolgáltatások portálján
Jelentkezzen be a [Microsoft Azure Machine learning Web Services](https://services.azureml.net) portálra.

Az új Machine Learning webszolgáltatás API-kulcsának beolvasása:

1. A Azure Machine Learning webszolgáltatások portálján kattintson a **webszolgáltatások** lehetőségre a felső menüben.
2. Kattintson arra a webszolgáltatásra, amelynek a kulcsát le szeretné kérni.
3. A felső **menüben kattintson a** felhasználás elemre.
4. Másolja és mentse az **elsődleges kulcsot**.

A klasszikus Machine Learning webszolgáltatás API-kulcsának beolvasása:

1. A Azure Machine Learning webszolgáltatások portálján kattintson a **klasszikus webes szolgáltatások** elemre a felső menüben.
2. Kattintson arra a webszolgáltatásra, amellyel dolgozik.
3. Kattintson arra a végpontra, amelynek a kulcsát le szeretné kérni.
4. A felső **menüben kattintson a** felhasználás elemre.
5. Másolja és mentse az **elsődleges kulcsot**.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
 A klasszikus webszolgáltatások kulcsát a Machine Learning Studio (klasszikus) webhelyről is lekérheti.

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klasszikus)
1. Machine Learning Studio (klasszikus) területen kattintson a bal oldali **WEBszolgáltatások** elemre.
2. Kattintson egy webszolgáltatásra. Az **API-kulcs** az **irányítópult** lapon található.

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Kapcsolódás Machine Learning webszolgáltatáshoz
Csatlakozhat egy Machine Learning webszolgáltatáshoz bármilyen programozási nyelv használatával, amely támogatja a HTTP-kérést és a választ. A C#, a Python és az R alkalmazásban példákat tekinthet meg egy Machine Learning webszolgáltatás Súgó oldaláról.

**Machine learning API Súgó** Webszolgáltatások telepítésekor a rendszer Machine Learning API súgóját hozza létre. Lásd [: 3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).
A Machine Learning API súgója az előrejelzési webszolgáltatás részleteit tartalmazza.

1. Kattintson arra a webszolgáltatásra, amellyel dolgozik.
2. Kattintson arra a végpontra, amelynek meg szeretné tekinteni az API-Súgó lapját.
3. A felső **menüben kattintson a** felhasználás elemre.
4. Kattintson az **API-Súgó lap** Request-Response vagy batch végrehajtási végpontok elemére.

**Új webszolgáltatás Machine Learning API-súgójának megtekintése**

A [Azure Machine learning Web Services portálon](https://services.azureml.net/):

1. A felső menüben kattintson a **Web Services** elemre.
2. Kattintson arra a webszolgáltatásra, amelynek a kulcsát le szeretné kérni.

Kattintson a **webszolgáltatások használata** lehetőségre az URI-k beszerzéséhez a Request-Response és a Batch-végrehajtási szolgáltatások és a mintakód számára a C#, az R és a Python nyelvben.

Kattintson a **hencegés API** lehetőségre a megadott URI-k által hívott API-k hencegő dokumentációjának beszerzéséhez.

### <a name="c-sample"></a>C# minta
Machine Learning webszolgáltatáshoz való kapcsolódáshoz használjon egy **HttpClient** Passing ScoreData. A ScoreData tartalmaz egy FeatureVector, amely a ScoreData jelképező numerikus funkciók n-dimenziós vektorát tartalmazza. A hitelesítést a Machine Learning szolgáltatásban egy API-kulccsal végezheti el.

Machine Learning webszolgáltatáshoz való kapcsolódáshoz telepíteni kell a **Microsoft. AspNet. WebApi. Client** NuGet-csomagot.

**A Microsoft. AspNet. WebApi. Client NuGet telepítése a Visual Studióban**

1. Tegye közzé a letöltési adatkészletet az UCI: Adult 2 Class adatkészlet webszolgáltatás webszolgáltatásból.
2. Kattintson az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzol** elemre.
3. Válassza a **Microsoft. AspNet. WebApi. Client csomag telepítése** lehetőséget.

**A mintakód futtatása**

1. Tegye közzé az "1. minta: adatkészletet az UCI: felnőtt 2 osztályú adatkészletből" kísérletet, amely a Machine Learning minta gyűjtemény részét képezi.
2. ApiKey kiosztása a kulccsal egy webszolgáltatásból. Lásd **a fenti engedélyezési kulcs beszerzése** című témakört.
3. Rendelje hozzá a serviceUri a kérelem URI-ja alapján.

**A teljes kérelem így fog kinézni.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python-minta
Ha Machine Learning webszolgáltatáshoz szeretne csatlakozni, használja a Python 2. X és a **urllib.** **urllib2** függvénytárát a Python 3. x verzióhoz. Átadja a ScoreData, amely tartalmaz egy FeatureVector, amely a ScoreData jelképező numerikus funkciók n-dimenziós vektorát tartalmazza. A hitelesítést a Machine Learning szolgáltatásban egy API-kulccsal végezheti el.

**A mintakód futtatása**

1. Telepítse az "1. minta: adathalmaz letöltése az UCI-ből: felnőtt 2 osztályú adatkészletből" kísérletet, amely a Machine Learning minta gyűjtemény részét képezi.
2. ApiKey kiosztása a kulccsal egy webszolgáltatásból. Tekintse meg a jelen cikk elején található **engedélyezési kulcs beszerzése** című szakaszt.
3. Rendelje hozzá a serviceUri a kérelem URI-ja alapján.

**A teljes kérelem így fog kinézni.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R minta

Machine Learning webszolgáltatáshoz való kapcsolódáshoz használja a **RCurl** és a **rjson** kódtárat a kérelem és a visszaadott JSON-válasz feldolgozásához. Átadja a ScoreData, amely tartalmaz egy FeatureVector, amely a ScoreData jelképező numerikus funkciók n-dimenziós vektorát tartalmazza. A hitelesítést a Machine Learning szolgáltatásban egy API-kulccsal végezheti el.

**A teljes kérelem így fog kinézni.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>JavaScript-minta

Ha Machine Learning webszolgáltatáshoz szeretne csatlakozni, használja a **NPM-csomagot a** projektben. Az `JSON` objektumot a bemenet formázásához és az eredmény elemzéséhez is használni fogja. Telepítse a szolgáltatást a használatával `npm install request --save` , vagy vegye fel a `"request": "*"` package.jsa alá `dependencies` és futtassa `npm install` .

**A teljes kérelem így fog kinézni.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```