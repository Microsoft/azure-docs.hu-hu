---
title: Azure Monitor HTTP-adatgyűjtő API-val | Microsoft Docs
description: A Azure Monitor HTTP-adatgyűjtő API-val hozzáadhat JSON-adatok egy Log Analytics munkaterülethez bármely olyan ügyfélről, amely meghívja a REST API. Ez a cikk az API használatát ismerteti, és példákat tartalmaz arra, hogyan tehet közzé adatokat különböző programozási nyelvek használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: e32bf95ef52fdd081eeaa476f44bf5dab99657d6
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452118"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Naplóbejegyzések küldése a Azure Monitornak a HTTP-adatgyűjtő API-val (nyilvános előzetes verzió)
Ez a cikk azt mutatja be, hogyan lehet a HTTP-adatgyűjtő API használatával elküldeni a naplófájlokat a Azure Monitor REST API-ügyfélről.  Ismerteti, hogyan lehet a parancsfájl vagy alkalmazás által gyűjtött adatokat formázni, belefoglalni egy kérelembe, és hogy az Azure Monitor által jóváhagyott kérést.  Ilyenek például a PowerShell, a C# és a Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> A Azure Monitor HTTP-adatgyűjtő API nyilvános előzetes verzióban érhető el.

## <a name="concepts"></a>Alapelvek
A HTTP-adatgyűjtő API-val elküldheti a naplózási adatait egy Log Analytics munkaterületre Azure Monitor bármely olyan ügyféltől, amely képes REST API meghívására.  Ez lehet egy olyan runbook, amely Azure Automation az Azure-ból vagy egy másik felhőből származó felügyeleti adatokat gyűjt, vagy olyan alternatív felügyeleti rendszer, amely Azure Monitor használ a naplózási adatok összesítésére és elemzésére.

A Log Analytics munkaterületen lévő összes adat egy adott bejegyzéstípusú rekordként van tárolva.  Az adatokat úgy formázhatja, hogy a HTTP-adatgyűjtő API-nak több, a JSON-beli rekordként küldje el.  Az adatok elküldésekor a rendszer egy egyedi rekordot hoz létre a tárházban a kérelem hasznos adataiban található minden egyes rekordhoz.


![HTTP-adatgyűjtő – áttekintés](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Kérelem létrehozása
A HTTP-adatgyűjtő API használatához létre kell hoznia egy POST-kérelmet, amely tartalmazza a JavaScript Object Notationba (JSON) küldendő adatküldési kérelmeket.  A következő három táblázat az egyes kérelmekhez szükséges attribútumokat sorolja fel. Az egyes attribútumokat a cikk későbbi részében részletesebben ismertetjük.

### <a name="request-uri"></a>Kérés URI-ja
| Attribútum | Tulajdonság |
|:--- |:--- |
| Metódus |POST |
| URI |https:// \<CustomerId\> . ODS.opinsights.Azure.com/API/logs?API-Version=2016-04-01 |
| Tartalomtípus |application/json |

### <a name="request-uri-parameters"></a>Kérelem URI-paraméterei
| Paraméter | Leírás |
|:--- |:--- |
| CustomerID |A Log Analytics munkaterület egyedi azonosítója. |
| Erőforrás |Az API-erőforrás neve:/API/logs. |
| API-verzió |A kérelemmel használni kívánt API verziója. Jelenleg ez 2016-04-01. |

### <a name="request-headers"></a>Kérésfejlécek
| Fejléc | Leírás |
|:--- |:--- |
| Engedélyezés |Az engedélyezési aláírás. A cikk későbbi részében olvashat arról, hogyan hozhat létre egy HMAC-SHA256 fejlécet. |
| Log-Type |Adja meg az elküldött adatok bejegyzéstípusát. Csak betűket, számokat és aláhúzást (_) tartalmazhat, és nem lehet hosszabb 100 karakternél. |
| x-MS-Date |A kérelem feldolgozásának dátuma, RFC 1123 formátumban. |
| x-MS-Erőforrásazonosító | Az Azure-erőforrás erőforrás-azonosítója, amelyhez az adatforrást társítani kell. Ezzel feltölti a [_ResourceId](./log-standard-columns.md#_resourceid) tulajdonságot, és lehetővé teszi, hogy az adatok szerepeljenek az [erőforrás-kontextus](./design-logs-deployment.md#access-mode) lekérdezésekben. Ha ez a mező nincs megadva, a rendszer nem fogja tartalmazni az erőforrás-környezeti lekérdezésekben szereplő adatforrásokat. |
| idő által generált mező | Az adattétel időbélyegét tartalmazó mező neve az adatobjektumban. Ha megad egy mezőt, a rendszer a **TimeGenerated** használja a tartalmát. Ha ez a mező nincs megadva, a **TimeGenerated** alapértelmezett értéke az üzenet betöltésének időpontja. Az üzenet mező tartalmának az ISO 8601 formátum éééé-hh-NNTóó: PP: ssZ kell lennie. |

## <a name="authorization"></a>Engedélyezés
A Azure Monitor HTTP-adatgyűjtő API-nak küldött összes kérésnek tartalmaznia kell egy engedélyezési fejlécet. A kérések hitelesítéséhez a kérést a munkaterülethez tartozó elsődleges vagy másodlagos kulccsal kell aláírnia. Ezután adja át az aláírást a kérelem részeként.   

Az engedélyezési fejléc formátuma a következő:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

A *munkaterület azonosítója* az log Analytics munkaterület egyedi azonosítója. Az *aláírás* a kérelemből létrehozott [KIVONATOLÓ üzenethitelesítő kód (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256) , amelyet a rendszer a [sha256 algoritmus](/dotnet/api/system.security.cryptography.sha256)használatával számít ki. Ezt követően Base64 kódolással kódolja.

Ezzel a formátummal kódolja a **SharedKey** aláírási karakterláncát:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Példa egy aláírási karakterláncra:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Ha rendelkezik az aláírási karakterlánccal, kódolja a HMAC-SHA256 algoritmus használatával az UTF-8 kódolású karakterláncon, majd az eredményt Base64-ként kódolja. Használja ezt a formátumot:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

A következő szakaszban szereplő mintákhoz minta kód tartozik, amely segítséget nyújt az engedélyezési fejléc létrehozásához.

## <a name="request-body"></a>A kérés törzse
Az üzenet törzsének JSON formátumúnak kell lennie. Tartalmaznia kell egy vagy több olyan rekordot, amelynek a tulajdonság neve és értéke párokat a következő formátumban kell megadni. A tulajdonság neve csak betűket, számokat és aláhúzást (_) tartalmazhat.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

A következő formátum használatával egyszerre több rekordot is létrehozhat egyetlen kérelemben. Az összes rekordnak azonos típusúnak kell lennie.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Rekord típusa és tulajdonságai
Ha az Azure Monitor HTTP-adatgyűjtő API-n keresztül küld el adatküldést, egyéni bejegyzéstípust határozhat meg. Jelenleg nem írhatók adatok olyan meglévő bejegyzéstípusokba, amelyeket más adattípusok és megoldások hoztak létre. Azure Monitor beolvassa a beérkező adatokat, majd olyan tulajdonságokat hoz létre, amelyek megfelelnek a megadott értékek adattípusának.

Az adatgyűjtő API-nak minden kérelemnek tartalmaznia kell egy **log-Type** fejlécet a bejegyzéstípus nevével. A rendszer automatikusan hozzáfűzi a **_CL** utótagot a megadott névhez, hogy az egyéni naplóként megkülönböztetse azt más típusú naplókból. Ha például a **MyNewRecordType** nevet adja meg, akkor a Azure Monitor egy **MyNewRecordType_CL** típusú rekordot hoz létre. Ezzel biztosítható, hogy a felhasználó által létrehozott nevek és a jelenlegi vagy jövőbeli Microsoft-megoldásokban leszállított típusok ne legyenek ütközések.

A tulajdonság adattípusának azonosításához Azure Monitor hozzáadja az utótagot a tulajdonság nevéhez. Ha egy tulajdonság null értéket tartalmaz, a tulajdonság nem szerepel a rekordban. Ez a tábla a tulajdonság adattípusát és a hozzá tartozó utótagot sorolja fel:

| Tulajdonság adattípusa | Utótag |
|:--- |:--- |
| Sztring |_s |
| Logikai |_b |
| Dupla |_d |
| Dátum/idő |_t |
| GUID (karakterláncként tárolva) |_g |

> [!NOTE]
> A GUID azonosítóként megjelenő karakterlánc-értékek a _g utótagot kapják meg, és GUID-ként lesznek formázva, még akkor is, ha a bejövő érték kötőjeleket tartalmaz. Például a "8145d822-13a7-44ad-859c-36f31a84f6dd" és a "8145d82213a744ad859c36f31a84f6dd" is a "8145d822-13a7-44ad-859c-36f31a84f6dd" néven lesz tárolva. Az egyetlen különbség a és egy másik karakterlánc között a név és a kötőjelek beszúrásának _g, ha nincsenek megadva a bemenetben. 

Az egyes tulajdonságokhoz Azure Monitor által használt adattípus attól függ, hogy az új rekord bejegyzéstípusa már létezik-e.

* Ha a bejegyzéstípus nem létezik, Azure Monitor létrehoz egy újat a következő JSON-típus használatával: az új rekordhoz tartozó egyes tulajdonságok adattípusának meghatározása.
* Ha a bejegyzéstípus létezik, Azure Monitor megpróbál új rekordot létrehozni a meglévő tulajdonságok alapján. Ha az új rekordban lévő tulajdonság adattípusa nem egyezik, és nem konvertálható a meglévő típusra, vagy ha a rekord nem létező tulajdonságot tartalmaz, Azure Monitor létrehoz egy új tulajdonságot, amely tartalmazza a megfelelő utótagot.

Ez a beküldési bejegyzés például egy három tulajdonsággal rendelkező rekordot hoz létre, **number_d**, **boolean_b** és **string_s**:

![1. minta rekord](media/data-collector-api/record-01.png)

Ha ezt követően elküldte ezt a következő bejegyzést, az összes karakterláncként formázott értékkel, a tulajdonságok nem változnak. Ezek az értékek meglévő adattípusokra alakíthatók:

![2. minta rekord](media/data-collector-api/record-02.png)

Ha azonban ezt követően elvégezte a következő beküldést, Azure Monitor az új tulajdonságokat **boolean_d** és a **string_d** hozza létre. Ezek az értékek nem alakíthatók át:

![3. minta rekord](media/data-collector-api/record-03.png)

Ha ezt követően elküldte a következő bejegyzést, a bejegyzéstípus létrehozása előtt Azure Monitor egy olyan rekordot hoz létre, amely három tulajdonsággal rendelkezik, **number_s**, **boolean_s** és **string_s**. Ebben a bejegyzésben minden kezdeti érték karakterláncként van formázva:

![4. minta rekord](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Foglalt tulajdonságok
A következő tulajdonságok vannak fenntartva, és nem használhatók egyéni bejegyzéstípusban. Hibaüzenet jelenik meg, ha a hasznos adatok tartalmazzák a következő tulajdonságokat.

- Bérlő

## <a name="data-limits"></a>Adatkorlátok
Bizonyos korlátozások vonatkoznak a Azure Monitor adatgyűjtési API-ra közzétett adatterületekre.

* Azure Monitor adatgyűjtő API-ra legfeljebb 30 MB/post. Ez egy adott bejegyzésre vonatkozó méretkorlát. Ha a 30 MB-ot meghaladó egyetlen bejegyzésből származó adatok mérete meghaladja az adatok mennyiségét, a kisebb méretű adattömbökre kell osztania, és egyszerre kell elküldeni őket.
* A mezőértékek maximális mérete 32 kB. Ha a mezőérték nagyobb 32 kB-nál, az adatok csonkolva lesznek.
* Egy adott típushoz tartozó mezők ajánlott maximális száma 50. Ez az észszerű korlát a használati és keresési teljesítmény szempontjából.  
* Egy Log Analytics munkaterületen lévő tábla csak 500 oszlopot támogat (ez a cikk egy mezője). 
* Az oszlopnév legfeljebb 500 karakterből állhat.

## <a name="return-codes"></a>Visszatérési kódok
A 200-es HTTP-állapotkód azt jelenti, hogy a kérelem feldolgozásra érkezett. Ez azt jelzi, hogy a művelet sikeresen befejeződött.

Ez a táblázat felsorolja a szolgáltatás által visszaadott állapotkódok teljes készletét:

| Code | Állapot | Hibakód | Leírás |
|:--- |:--- |:--- |:--- |
| 200 |OK | |A kérés elfogadása sikeresen megtörtént. |
| 400 |Hibás kérelem |InactiveCustomer |A munkaterület le lett zárva. |
| 400 |Hibás kérelem |InvalidApiVersion |A szolgáltatás nem ismerte fel a megadott API-verziót. |
| 400 |Hibás kérelem |InvalidCustomerId |A megadott munkaterület-azonosító érvénytelen. |
| 400 |Hibás kérelem |InvalidDataFormat |Érvénytelen JSON lett elküldve. A válasz törzse további információkat is tartalmazhat a hiba megoldásával kapcsolatban. |
| 400 |Hibás kérelem |InvalidLogType |A megadott naplózási típus speciális karaktereket vagy számokat tartalmaz. |
| 400 |Hibás kérelem |MissingApiVersion |Nincs megadva az API-verzió. |
| 400 |Hibás kérelem |MissingContentType |A tartalomtípus nincs megadva. |
| 400 |Hibás kérelem |MissingLogType |Nem adta meg a szükséges érték napló típusát. |
| 400 |Hibás kérelem |UnsupportedContentType |A tartalomtípus nem az **Application/JSON** értékre lett beállítva. |
| 403 |Forbidden |InvalidAuthorization |A szolgáltatás nem tudta hitelesíteni a kérelmet. Ellenőrizze, hogy érvényes-e a munkaterület azonosítója és a csatlakoztatási kulcs. |
| 404 |Nem található | | Vagy a megadott URL-cím helytelen, vagy a kérelem túl nagy. |
| 429 |Túl sok kérelem | | A szolgáltatás nagy mennyiségű adattal rendelkezik a fiókból. Próbálkozzon újra a kéréssel. |
| 500 |Belső kiszolgálóhiba |UnspecifiedError |A szolgáltatás belső hibát észlelt. Próbálkozzon újra a kéréssel. |
| 503 |A szolgáltatás nem érhető el |ServiceUnavailable |A szolgáltatás jelenleg nem érhető el a kérelmek fogadásához. Próbálkozzon újra a kéréssel. |

## <a name="query-data"></a>Adatok lekérdezése
A Azure Monitor HTTP-adatgyűjtő API által küldött adatok lekérdezéséhez keressen olyan **típusú** rekordokat, amelyek a megadott **LogType** -értékkel egyenlőek, **_CL** hozzáfűzéssel. Ha például a **MyCustomLog** használta, akkor az összes rekordot visszaadja a következővel: `MyCustomLog_CL` .

## <a name="sample-requests"></a>Példák a kérelmekre
A következő részekben példákat talál arra, hogyan küldhet adatokat a Azure Monitor HTTP-adatgyűjtő API-nak különböző programozási nyelvek használatával.

Az egyes mintákhoz az alábbi lépéseket követve állíthatja be az engedélyezési fejléc változóit:

1. A Azure Portal keresse meg Log Analytics munkaterületét.
2. Válassza az **ügynökök kezelése** lehetőséget.
2. A **munkaterület-azonosító** jobb oldalán válassza a másolás ikont, majd illessze be az azonosítót az **ügyfél-azonosító** változó értékeként.
3. Az **elsődleges kulcs** jobb oldalán válassza a másolás ikont, majd illessze be az azonosítót a **Shared Key** változó értékeként.

Azt is megteheti, hogy megváltoztatja a napló típusát és a JSON-adat változóit.

### <a name="powershell-sample"></a>PowerShell-minta
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-minta
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2 minta
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Python 3 minta
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Java-minta

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternatívák és szempontok
Habár az adatgyűjtő API-nak le kell fednie a legtöbb szükséges adatot a szabad formátumú adatok Azure-naplókba való gyűjtéséhez, vannak olyan példányok, amelyekben szükség lehet az API bizonyos korlátainak leküzdésére. Az összes lehetőség a következő:

| Alternatív | Leírás | Legmegfelelőbb a következőhöz: |
|---|---|---|
| [Egyéni események](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): natív SDK-alapú betöltés Application Insights | Application Insights, amely jellemzően az alkalmazáson belüli SDK-n keresztül lett kialakítva, lehetővé teszi, hogy egyéni eseményeken keresztül küldjön egyéni adatait. | <ul><li> Az alkalmazásban létrehozott, de az SDK által az alapértelmezett adattípusok (kérések, függőségek, kivételek stb.) egyikén keresztül generált adatmennyiség.</li><li> A leggyakrabban a Application Insightsban lévő egyéb alkalmazásadatok korrelációját képező adatértékek </li></ul> |
| Adatgyűjtő API a Azure Monitor-naplókban | A Azure Monitor-naplókban található adatgyűjtő API teljesen nyitott módon tölti fel az adatmennyiséget. A JSON-objektumokban formázott összes adat itt küldhető el. Az elküldés után a rendszer feldolgozza és elérhetővé teszi a naplókban, hogy a naplókban vagy más Application Insights-adatszolgáltatásokban is korreláljon. <br/><br/> Az adatok egy Azure Blob-blobba való feltöltése viszonylag egyszerű, így a fájlok feldolgozására és a Log Analyticsba való feltöltésére is sor kerül. Tekintse meg [ezt](./create-pipeline-datacollector-api.md) a folyamatot, amely egy ilyen folyamat megvalósítását ismerteti. | <ul><li> A Application Insightson belüli alkalmazásban nem szükségszerűen generált adatértékek.</li><li> Ilyenek például a keresési és a egyedkapcsolati táblázatok, a hivatkozási adatok, az előre összevont statisztikák és így tovább. </li><li> Más Azure Monitor-adattípusokkal (Application Insights, egyéb naplók adattípusokkal, Security Centerekkel, tároló-elemzésekkel/virtuális gépekkel stb.) való hivatkozással. </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Az Azure Adatkezelő (ADX) az adatplatform, amely Application Insights elemzési és Azure Monitor naplókra épül. A már általánosan elérhető ("GA") az adatplatform nyers formájában való használata biztosítja a teljes rugalmasságot (de a felügyelet terhelését igényli) a fürtön (Kubernetes RBAC, megőrzési arány, séma stb.). A ADX számos betöltési [lehetőséget](/azure/data-explorer/ingest-data-overview#ingestion-methods) biztosít, többek között a [CSV-, a TSV-és a JSON](/azure/kusto/management/mappings) -fájlokat. | <ul><li> Olyan adat, amely nem felel meg a Application Insights vagy a naplók alatt lévő többi adatnak. </li><li> Olyan speciális betöltési vagy feldolgozási képességeket igénylő adatfeldolgozási funkciók, amelyek jelenleg nem érhetők el Azure Monitor naplókban. </li></ul> |


## <a name="next-steps"></a>Következő lépések
- Az Log Analytics munkaterületről származó adatok lekérdezéséhez használja a [log Search API](./log-query-overview.md) -t.

- További információ arról, hogyan [hozhat létre adatfolyamatot az adatgyűjtő API-val az](create-pipeline-datacollector-api.md) Logic apps munkafolyamattal Azure monitor.