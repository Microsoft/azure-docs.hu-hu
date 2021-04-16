---
title: A Azure IoT Hub biztonsági | Microsoft Docs
description: Fejlesztői útmutató – az eszközalkalmazások IoT Hub háttéralkalmazások hozzáférésének szabályozása. Információkat tartalmaz a biztonsági jogkivonatokkal és az X.509-tanúsítványok támogatásával kapcsolatban.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: e72af412f61f2084fb78907c15a92a22b9e3bc99
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567179"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub-hozzáférés szabályozása

Ez a cikk az IoT Hub biztonságossá tétele érdekében elérhető lehetőségeket ismerteti. IoT Hub *engedélyeket használ az* egyes IoT Hub-végpontok hozzáférésének megadásához. Az engedélyek funkció alapján korlátozzák az IoT Hubhoz való hozzáférést.

Ez a cikk a következőt mutat be:

* Az eszközöknek vagy háttéralkalmazásnak az IoT Hub eléréséhez elérhető különböző engedélyek.
* A hitelesítési folyamat és az engedélyek ellenőrzéséhez használt jogkivonatok.
* Hitelesítő adatok hatókörének megadása adott erőforrásokhoz való hozzáférés korlátozására.
* IoT Hub X.509-tanúsítványok támogatása.
* Egyéni eszközhitelesítési mechanizmusok, amelyek meglévő eszközidentitás-regisztrációs adatbázisokat vagy hitelesítési sémákat használnak.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A végpontok bármelyikének eléréséhez megfelelő engedélyekkel IoT Hub rendelkeznie. Egy eszköznek például tartalmaznia kell egy biztonsági hitelesítő adatokat tartalmazó jogkivonatot, valamint minden üzenetet, amit a IoT Hub.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

Az [engedélyeket a következő](#iot-hub-permissions) módokon használhatja:

* **IoT Hub-szintű megosztott hozzáférési szabályzatok.** A megosztott hozzáférési szabályzatok az engedélyek bármilyen [kombinációját biztosítják.](#iot-hub-permissions) Szabályzatokat programozott módon határozhat meg a [Azure Portal](https://portal.azure.com)a IoT Hub [Resource REST API-k](/rest/api/iothub/iothubresource)vagy az [az iot hub policy](/cli/azure/iot/hub/policy) CLI használatával. Az újonnan létrehozott IoT Hub az alábbi alapértelmezett szabályzatokkal rendelkezik:
  
  | Megosztott hozzáférési szabályzat | Engedélyek |
  | -------------------- | ----------- |
  | iothubowner | Minden engedély |
  | szolgáltatás | **ServiceConnect-engedélyek** |
  | eszköz | **DeviceConnect-engedélyek** |
  | registryRead (beállításjegyzékolvasás) | **RegistryRead-engedélyek** |
  | registryReadWrite | **RegistryRead-** és **RegistryWrite-engedélyek** |

* **Eszközönkénti biztonsági hitelesítő adatok.** Minden IoT Hub tartalmaz egy identitásjegyzéket [Az](iot-hub-devguide-identity-registry.md) identitásjegyzékben található összes eszközhöz konfigurálhatja azokat a biztonsági hitelesítő adatokat, amelyek **DeviceConnect-engedélyeket** stb. engedélyeznek a megfelelő eszközvégpontra vonatkozó hatókörrel.

Egy tipikus IoT-megoldásban például:

* Az eszközkezelési összetevő a *registryReadWrite szabályzatot* használja.
* Az eseményfeldolgozó összetevő a szolgáltatási *szabályzatot* használja.
* A futásidőben futó eszköz üzleti logikai összetevője a szolgáltatási *szabályzatot* használja.
* Az egyes eszközök az IoT Hub identitásjegyzékében tárolt hitelesítő adatokkal csatlakoznak.

> [!NOTE]
> Részletes [információkért tekintse meg](#iot-hub-permissions) az engedélyeket.

## <a name="authentication"></a>Hitelesítés

Az Azure IoT Hub egy biztonsági jogkivonatnak a megosztott hozzáférési szabályzatok és az identitásjegyzékbeli biztonsági hitelesítő adatok alapján végzett ellenőrzésével ad hozzáférést a végpontokhoz.

A rendszer soha nem küldi el a biztonsági hitelesítő adatokat, például a szimmetrikus kulcsokat.

> [!NOTE]
> A Azure IoT Hub erőforrás-szolgáltatót az Ön Azure-előfizetése biztosítja, ahogyan az összes szolgáltató a [Azure Resource Manager.](../azure-resource-manager/management/overview.md)

További információ a biztonsági jogkivonatok felépítéséről és használatával kapcsolatban: IoT Hub [jogkivonatok használata.](iot-hub-devguide-security.md#security-tokens)

### <a name="protocol-specifics"></a>Protokollspecifikus adatok

Minden támogatott protokoll, például az MQTT, az AMQP és a HTTPS, különböző módokon szállítja a jogkivonatokat.

MQTT használata esetén a CONNECT csomag deviceId azonosítóval rendelkezik ClientId-ként, `{iothubhostname}/{deviceId}` a Felhasználónév mezőben, valamint egy SAS-jogkivonattal a Jelszó mezőben. `{iothubhostname}` az IoT Hub teljes CName -ének kell lennie (például contoso.azure-devices.net).

[AmQP használata](https://www.amqp.org/)esetén a IoT Hub támogatja [SASL PLAIN](https://tools.ietf.org/html/rfc4616) AMQP jogcímalapú [biztonsági beállításokat.](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)

AMQP jogcímalapú biztonság használata esetén a szabvány határozza meg a jogkivonatok továbbításának mikéntját.

A SASL PLAIN felhasználónév **a** következő lehet:

* `{policyName}@sas.root.{iothubName}` IoT Hub-szintű jogkivonatok használata esetén.
* `{deviceId}@sas.{iothubname}` eszközhatókörű jogkivonatok használata esetén.

A jelszó mező mindkét esetben tartalmazza a jogkivonatot, amint az a [biztonsági IoT Hub ismerteti.](iot-hub-devguide-security.md#security-tokens)

A HTTPS úgy valósítja meg a hitelesítést, hogy egy érvényes jogkivonatot ad meg **az engedélyezési kérelem** fejlécében.

#### <a name="example"></a>Példa

Felhasználónév (a DeviceId megkülönbözteti a kis- és nagybetűket): `iothubname.azure-devices.net/DeviceId`

Jelszó (SAS-jogkivonatot az az [iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)CLI-bővítményparancs vagy az Azure IoT Tools Code-hoz Visual Studio [paranccsal hozhat létre):](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan hoznak létre jogkivonatokat, amikor csatlakoznak a szolgáltatáshoz. Bizonyos esetekben az Azure IoT SDK-k nem támogatják az összes protokollt vagy az összes hitelesítési módszert.

### <a name="special-considerations-for-sasl-plain"></a>Speciális szempontok a SASL PLAIN

Ha AMQP SASL PLAIN használ, az IoT Hubhoz csatlakozó ügyfelek minden TCP-kapcsolathoz egyetlen tokent használhatnak. Amikor a jogkivonat lejár, a TCP-kapcsolat megszakad a szolgáltatással, és újracsatlakozik. Ez a viselkedés, bár a háttéralkalmazások használata nem okoz problémát, az alábbi okok miatt káros az eszközalkalmazások számára:

* Az átjárók általában számos eszköz nevében csatlakoznak. A SASL PLAIN létre kell hoznia egy külön TCP-kapcsolatot az IoT Hubhoz csatlakozó összes eszközhöz. Ez a forgatókönyv jelentősen megnöveli az energiaellátási és hálózati erőforrások felhasználását, és növeli az egyes eszközkapcsolatok késését.

* A korlátozott erőforrásokkal rendelkező eszközöket hátrányosan befolyásolja, hogy a jogkivonatok lejárata után egyre több erőforrást kell újracsatlakozni.

## <a name="scope-iot-hub-level-credentials"></a>IoT Hub-szintű hitelesítő adatok hatókörének megadása

Az IoT hubszintű biztonsági szabályzatok hatókörének korlátozásához korlátozott erőforrás URI-val hozzon létre jogkivonatokat. Az eszközről a felhőbe küldött üzenetek végpontja például a **következő: /devices/{deviceId}/messages/events.** Eszközcsatlakozási engedélyekkel rendelkező IoT Hub-szintű megosztott hozzáférési szabályzattal is aláírhat egy olyan jogkivonatot, amelynek resourceURI-ja **/devices/{deviceId}**.  Ez a módszer létrehoz egy jogkivonatot, amely csak a deviceId nevében küldhető el **üzenetekhez.**

Ez a mechanizmus hasonló az Event Hubs [közzétevői házirendhez,](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)és lehetővé teszi egyéni hitelesítési módszerek használatát.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

IoT Hub biztonsági jogkivonatokkal hitelesíti az eszközöket és a szolgáltatásokat, hogy elkerülje a kulcsok vezetéken való küldését. A biztonsági jogkivonatok emellett korlátozott időben érvényessége és hatóköre is korlátozott. [Az Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan hoznak létre jogkivonatokat anélkül, hogy speciális konfigurációra lenne szükség. Egyes forgatókönyvekhez biztonsági jogkivonatok közvetlen generálás és használata szükséges. Ilyen forgatókönyvek például a következők:

* Az MQTT, AMQP vagy HTTPS felület közvetlen használata.

* A jogkivonat-szolgáltatás mintáját az Egyéni [eszközhitelesítés részben leírtak szerint implementációja.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

IoT Hub lehetővé teszi az eszközök hitelesítését IoT Hub [X.509-tanúsítványokkal.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Biztonsági jogkivonatok struktúrája

A biztonsági jogkivonatokkal időkorrekta hozzáférést adhat az eszközökhöz és szolgáltatásokhoz a IoT Hub. Ahhoz, hogy engedélyt kap IoT Hub-hoz való csatlakozáshoz, az eszközöknek és szolgáltatásoknak megosztott hozzáféréssel vagy szimmetrikus kulccsal aláírt biztonsági jogkivonatokat kell küldeniük. Ezeket a kulcsokat egy eszközidentitás tárolja az identitásjegyzékben.

A megosztott hozzáférési kulccsal aláírt jogkivonat hozzáférést biztosít a megosztott hozzáférési szabályzat engedélyéhez társított összes funkcióhoz. Az eszközidentitás szimmetrikus kulcsával aláírt jogkivonat csak **DeviceConnect** engedélyt biztosít a társított eszközidentitáshoz.

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

A várt értékek a következőek:

| Érték | Leírás |
| --- | --- |
| {signature} |A következő űrlap HMAC-SHA256 aláírási sztringet tartalmaz: `{URL-encoded-resourceURI} + "\n" + expiry` . **Fontos:** A kulcs a base64-ről van dekódolva, és kulcsként használható a HMAC-SHA256 számítás végrehajtásához. |
| {resourceURI} |Az ezzel a jogkivonattal elérhető végpontok URI-előtagja (szegmensek szerint), kezdve az IoT Hub állomásnevét (nincs protokoll). Például: `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8-sztringek az 1970. január 1-jén 00:00:00 UTC-i epoch óta történt másodpercek számára. |
| {URL-kódolású-resourceURI} |Kisbetűs URL-kódolás az kisbetűs erőforrás URI-ján |
| {policyName} |Annak a megosztott hozzáférési szabályzatnak a neve, amelyre a jogkivonat vonatkozik. Hiányzik, ha a jogkivonat eszközjegyzékbeli hitelesítő adatokra vonatkozik. |

**Megjegyzés az előtagnál:** Az URI-előtag kiszámítása szegmens, nem pedig karakter alapján történt. A például `/a/b` a előtagja, `/a/b/c` de nem a előtagja. `/a/bc`

Az alábbi Node.js egy **generateSasToken** nevű függvényt mutat be, amely kiszámítja a jogkivonatot a `resourceUri, signingKey, policyName, expiresInMins` bemenetből. A következő szakaszok a különböző tokenhasználati esetek különböző bemenetének inicializálását ismertetik.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Összehasonlításképpen, a biztonsági jogkivonat létrehozásához megfelelő Python-kód a következő:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

A C# biztonsági jogkivonatot generáló funkciója a következő:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```

Java nyelven:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Mivel a jogkivonat érvényességi ideje IoT Hub gépeken van ellenőrizve, a jogkivonatot generáló gép óraeltávolának minimálisnak kell lennie.

### <a name="use-sas-tokens-in-a-device-app"></a>SAS-jogkivonatok használata egy eszközalkalmazásban

A **DeviceConnect-engedélyeket** kétféleképpen szerezheti be a IoT Hub biztonsági jogkivonatokkal: használjon szimmetrikus eszközkulcsot az identitásjegyzékből, [](#use-a-symmetric-key-in-the-identity-registry)vagy használjon megosztott hozzáférési [kulcsot.](#use-a-shared-access-policy)

Ne feledje, hogy az eszközökről elérhető összes funkció a előtaggal meghatározott végpontok számára érhető `/devices/{deviceId}` el.

> [!IMPORTANT]
> Egy adott eszköz IoT Hub csak az eszközidentitás szimmetrikus kulcsának használatával hitelesíthető. Ha megosztott hozzáférési szabályzattal fér hozzá az eszköz funkcióihoz, a megoldásnak a biztonsági jogkivonatot kiállító összetevőt megbízható alkomponensnek kell tekintenünk.

Az eszköz felé néző végpontok (a protokolltól függetlenül):

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Eszközről a felhőbe küldött üzenetek. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Felhőből az eszközre küldött üzenetek fogadása. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Szimmetrikus kulcs használata az identitásjegyzékben

Ha egy eszközidentitás szimmetrikus kulcsával hoz létre jogkivonatot, a rendszer kihagyja a jogkivonat policyName ( `skn` ) elemét.

Az összes eszközfunkció eléréséhez létrehozott jogkivonatnak például a következő paraméterekkel kell lennie:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* aláírókulcs: az identitás szimmetrikus `{device id}` kulcsa,
* nincs szabályzatnév,
* bármilyen lejárati idő.

Példa a fenti Node.js függvényre:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Az eredmény, amely hozzáférést biztosít a device1 összes funkcióhoz, a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> SAS-jogkivonatot az [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)CLI-bővítményparancs, vagy az Azure IoT Tools kódhoz Visual Studio [hozhat létre.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="use-a-shared-access-policy"></a>Megosztott hozzáférési szabályzat használata

Ha megosztott hozzáférési szabályzatból hoz létre jogkivonatot, állítsa a mezőt a szabályzat `skn` nevére. Ennek a szabályzatnak Meg kell **adnunk a DeviceConnect engedélyt.**

Az eszközfunkciók megosztott hozzáférési szabályzatokkal való elérésének két fő forgatókönyve a következő:

* [felhőalapú protokollátjárók,](iot-hub-devguide-endpoints.md)
* [egyéni hitelesítési](iot-hub-devguide-security.md#custom-device-and-module-authentication) sémák megvalósításához használt jogkivonat-szolgáltatások.

Mivel a megosztott hozzáférési szabályzat bármely eszközhöz hozzáférést adhat a csatlakozáshoz, fontos, hogy a megfelelő erőforrás URI-t használja a biztonsági jogkivonatok létrehozásakor. Ez a beállítás különösen fontos olyan jogkivonat-szolgáltatások esetén, amelyekhez a jogkivonatot egy adott eszközre kell hatókörként használni az erőforrás URI-ját használva. Ez a pont kevésbé releváns a protokollátjárók esetében, mivel már az összes eszköz forgalmát közvetítik.

Egy eszköz nevű, előre létrehozott megosztott hozzáférési szabályzatot használó jogkivonat-szolgáltatás például a következő paraméterekkel hoz létre jogkivonatot: 

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* aláírókulcs: a házirend egyik `device` kulcsa,
* szabályzat neve: `device` ,
* bármilyen lejárati idő.

A fenti példa a Node.js függvényt használja:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely hozzáférést biztosít az eszköz1 összes funkciója számára, a következő lesz:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Egy protokollátjáró használhatja ugyanazt a jogkivonatot minden eszköz esetében, egyszerűen az erőforrás URI-ját a következőre használhatja: `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Biztonsági jogkivonatok használata szolgáltatás-összetevőkből

A szolgáltatás-összetevők csak megosztott hozzáférési szabályzatok használatával hozhatnak létre biztonsági jogkivonatokat, amelyek a korábban leírtaknak megfelelően biztosítják a megfelelő engedélyeket.

A végpontok a következő szolgáltatásfunkciókat fedik fel:

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices` |Eszköz identitások létrehozása, frissítése, lekérése és törlése. |
| `{iot hub host name}/messages/events` |Az eszközről a felhőbe küldött üzenetek fogadása. |
| `{iot hub host name}/servicebound/feedback` |Visszajelzés a felhőből az eszközre küldött üzenetekről. |
| `{iot hub host name}/devicebound` |A felhőből az eszközre küldött üzenetek. |

Például egy **registryRead** nevű, előre létrehozott megosztott hozzáférési szabályzatot használó szolgáltatás létrehoz egy jogkivonatot a következő paraméterekkel:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices` ,
* aláírókulcs: a házirend egyik `registryRead` kulcsa,
* szabályzat neve: `registryRead` ,
* bármilyen lejárati idő.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény a következő lesz, amely hozzáférést biztosít az összes eszköz identitásának olvasása számára:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Támogatott X.509-tanúsítványok

Bármilyen X.509-tanúsítvány használatával hitelesíthet egy eszközt az IoT Hub-val úgy, hogy feltölt egy tanúsítvány-ujjlenyomatot vagy egy hitelesítésszolgáltatót (CA) a Azure IoT Hub. A tanúsítvány-ujjlenyomatokkal való hitelesítés ellenőrzi, hogy a bemutatott ujjlenyomat megegyezik-e a konfigurált ujjlenyomattal. A hitelesítésszolgáltatóval való hitelesítés érvényesíti a tanúsítványláncot. Mindkét esetben a TLS-kézfogás megköveteli, hogy az eszköz érvényes tanúsítvánnyal és titkos kulccsal rendelkezik. A részletekért tekintse meg a TLS-specifikációt, például: [RFC 5246 – A Transport Layer Security (TLS) protokoll 1.2-es verziója.](https://tools.ietf.org/html/rfc5246/)

A támogatott tanúsítványok a következők:

* **Egy meglévő X.509-tanúsítvány.** Előfordulhat, hogy egy eszközhöz már társítva van egy X.509-tanúsítvány. Az eszköz ezzel a tanúsítvánnyal hitelesítheti magát a IoT Hub. Ujjlenyomattal vagy hitelesítésszolgáltatói hitelesítéssel használható. 

* **Hitelesítésszolgáltató által aláírt X.509-tanúsítvány.** Az eszközök azonosításához és IoT Hub hitelesítéséhez használhat egy hitelesítésszolgáltató (CA) által létrehozott és aláírt X.509-tanúsítványt. Ujjlenyomattal vagy hitelesítésszolgáltatói hitelesítéssel használható.

* **Egy ön által létrehozott és önaírt X-509-tanúsítvány.** Az eszköz gyártója vagy a házon lévő üzembe helyező létrehozhatja ezeket a tanúsítványokat, és tárolhatja a megfelelő titkos kulcsot (és tanúsítványt) az eszközön. Erre a célra olyan eszközöket használhat, mint az [OpenSSL](https://www.openssl.org/) és a [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) segédprogram. Csak ujjlenyomatos hitelesítéssel működik.

Az eszközök használhatnak X.509-tanúsítványt vagy biztonsági jogkivonatot a hitelesítéshez, de mindkettőt nem. Az X.509-tanúsítványhitelesítéssel győződjön meg arról, hogy rendelkezik olyan stratégiával, amely a tanúsítványváltást kezeli egy meglévő tanúsítvány lejártakor.

Az X.509 hitelesítésszolgáltatói hitelesítést (CA) felhasználó eszközökre vonatkozó alábbi funkciók még nem általánosan elérhetők, és engedélyezni kell az előnézeti [módot:](iot-hub-preview-mode.md)

* HTTPS, WebSocketen keresztüli MQTT, webSocket protokollon keresztüli AMQP.
* Fájlfeltöltések (minden protokoll).

További információ a hitelesítésszolgáltatóval való hitelesítésről: Eszközhitelesítés [X.509 hitelesítésszolgáltatói tanúsítványokkal.](iot-hub-x509ca-overview.md) További információ hitelesítésszolgáltatónak az IoT Hubbal való feltöltésével és ellenőrzésével kapcsolatban: [X.509-biztonság](iot-hub-security-x509-get-started.md)beállítása az Azure IoT Hubban.

### <a name="register-an-x509-certificate-for-a-device"></a>X.509-tanúsítvány regisztrálása egy eszközhöz

Az [Azure IoT Service SDK for C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (1.0.8-as vagy újabb verzió) támogatja az X.509-tanúsítványt hitelesítésre használó eszközök regisztrálását. Más API-k, például az eszközök importálása/exportálása szintén támogatják az X.509-tanúsítványokat.

Az x.509-tanúsítványokat az [az iot hub device-identity CLI-bővítményparancs](/cli/azure/ext/azure-iot/iot/hub/device-identity) használatával is konfigurálhatja.

### <a name="c-support"></a>\#C-támogatás

A **RegistryManager** osztály programozott módon regisztrálja az eszközt. Az **AddDeviceAsync** és **az UpdateDeviceAsync** metódussal regisztrálhat és frissítheti az eszközt az IoT Hub identitásjegyzékben. Ez a két metódus bemenetként egy **Eszközpéldányt** használ. Az **Eszköz** osztály tartalmaz egy **Hitelesítési tulajdonságot,** amely lehetővé teszi az elsődleges és másodlagos X.509-tanúsítvány ujjlenyomatának megadását. Az ujjlenyomat az X.509-tanúsítvány SHA256 kivonatát jelöli (bináris DER-kódolással tárolva). Megadhatja az elsődleges ujjlenyomatot, a másodlagos ujjlenyomatot vagy mindkettőt. Az elsődleges és másodlagos ujjlenyomatok a tanúsítványváltási forgatókönyvek kezeléséhez támogatottak.

Itt található egy C-mintakódrészlet egy eszköz \# X.509-tanúsítvány-ujjlenyomattal való regisztrálásához:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>X.509-tanúsítvány használata futás közbeni műveletek során

A [.NET-hez készült Azure IoT eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (1.0.11-es vagy újabb verzió) támogatja az X.509-tanúsítványok használatát.

### <a name="c-support"></a>\#C-támogatás

A **DeviceAuthenticationWithX509Certificate** osztály támogatja a **DeviceClient-példányok** létrehozását X.509 tanúsítvánnyal. Az X.509-tanúsítványnak a titkos kulcsot tartalmazó PFX (más néven PKCS #12) formátumban kell lennie.

Itt egy mintakódrészlet:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Egyéni eszköz- és modulhitelesítés

A IoT Hub [identitásjegyzékével](iot-hub-devguide-identity-registry.md) konfigurálhatja az eszközönként/modulonkénti biztonsági hitelesítő adatokat és hozzáférés-vezérlést [a jogkivonatok használatával.](iot-hub-devguide-security.md#security-tokens) Ha egy IoT-megoldás már rendelkezik egyéni identitásjegyzékvel és/vagy hitelesítési sémával, fontolja meg egy *jogkivonat-szolgáltatás* létrehozását az infrastruktúra és a IoT Hub. Így más IoT-funkciókat is használhat a megoldásában.

A jogkivonat-szolgáltatás egy egyéni felhőszolgáltatás. Eszközhatókörű vagy  IoT Hub modulra vonatkozó jogkivonatok létrehozásához közös hozzáférési szabályzatot használ **DeviceConnect-** vagy *ModuleConnect-engedélyekkel.*   Ezek a jogkivonatok lehetővé teszik, hogy egy eszköz és egy modul csatlakozzon az IoT Hubhoz.

![A jogkivonat-szolgáltatási minta lépései](./media/iot-hub-devguide-security/tokenservice.png)

A jogkivonat-szolgáltatási minta fő lépései a következőek:

1. Hozzon létre IoT Hub megosztott hozzáférési szabályzatot **a DeviceConnect** vagy **a ModuleConnect** engedélyekkel az IoT Hubhoz. Ezt a szabályzatot létrehozhatja a [Azure Portal](https://portal.azure.com) programozott módon. A jogkivonat-szolgáltatás ezzel a szabályzattal írja alá a létrehozott jogkivonatokat.

2. Ha egy eszköznek/modulnak hozzá kell férni az IoT Hubhoz, aláírt jogkivonatot kér a jogkivonat-szolgáltatástól. Az eszköz hitelesíthet az egyéni identitásjegyzékkel/hitelesítési sémával annak meghatározásához, hogy a tokenszolgáltatás milyen eszköz-/modulidentitást használ a jogkivonat létrehozásához.

3. A jogkivonat-szolgáltatás egy jogkivonatot ad vissza. A jogkivonat a vagy a használatával jön létre, és a hitelesítve lesz az eszközként vagy a modul `/devices/{deviceId}` `/devices/{deviceId}/module/{moduleId}` `resourceURI` `deviceId` `moduleId` hitelesítése alatt áll. A jogkivonat-szolgáltatás a megosztott hozzáférési szabályzatot használja a jogkivonat felépítéséhez.

4. Az eszköz/modul a jogkivonatot közvetlenül az IoT Hubbal használja.

> [!NOTE]
> Jogkivonatot a [.NET SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) vagy az [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) Java-osztály használatával hozhat létre a jogkivonat-szolgáltatásban.

A jogkivonat-szolgáltatás a jogkivonat lejáratát a kívánt módon állíthatja be. Amikor a jogkivonat lejár, az IoT Hub megszabadja az eszköz/modul kapcsolatát. Ezután az eszköznek/modulnak új jogkivonatot kell kérnie a jogkivonat-szolgáltatástól. A rövid lejárati idő növeli az eszköz/modul és a tokenszolgáltatás terhelését.

Ahhoz, hogy egy eszköz/modul csatlakozzon a hubhoz, akkor is hozzá kell azt adni az IoT Hub Identity Registryhez– annak ellenére, hogy az egy jogkivonatot használ, és nem kulcsot a csatlakozáshoz. Ezért továbbra is használhatja az eszközönkénti/modulonkénti hozzáférés-vezérlést, ha engedélyezi vagy letiltja az eszköz-/modulidentitásokat az [identitásjegyzékben.](iot-hub-devguide-identity-registry.md) Ez a megközelítés csökkenti a hosszú lejárati idővel járó jogkivonatok használatának kockázatát.

### <a name="comparison-with-a-custom-gateway"></a>Összehasonlítás egyéni átjáróval

A tokenszolgáltatás-minta az ajánlott módszer egy egyéni identitásjegyzék/hitelesítési séma implementálja a IoT Hub. Ez a minta azért ajánlott, IoT Hub továbbra is a megoldás nagy része továbbra is kezelni tudja a forgalmat. Ha azonban az egyéni hitelesítési séma annyira össze van fonva *a* protokollal, hogy az összes forgalom feldolgozásához egyéni átjáróra lehet szükség. Ilyen forgatókönyv például a Transport Layer Security (TLS) és az elő megosztott kulcsok [(PSK-k) használata.](https://tools.ietf.org/html/rfc4279) További információt a [protokollátjáróról szóló cikkben](iot-hub-protocol-gateway.md) talál.

## <a name="reference-topics"></a>Referencia-témakörök:

Az alábbi referencia-témakörök további információt nyújtanak az IoT Hubhoz való hozzáférés szabályozásával kapcsolatban.

## <a name="iot-hub-permissions"></a>IoT Hub engedélyek

A következő táblázat az IoT Hubhoz való hozzáférés szabályozásához használható engedélyeket sorolja fel.

| Engedély | Jegyzetek |
| --- | --- |
| **RegistryRead** |Olvasási hozzáférést biztosít az identitásjegyzékhez. További információ: [Identitásjegyzék.](iot-hub-devguide-identity-registry.md) <br/>Ezt az engedélyt a háttérbeli felhőszolgáltatások használják. |
| **RegistryReadWrite** |Olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információ: [Identitásjegyzék.](iot-hub-devguide-identity-registry.md) <br/>Ezt az engedélyt a háttérbeli felhőszolgáltatások használják. |
| **ServiceConnect** |Hozzáférést biztosít a felhőszolgáltatás felé néző kommunikációs és monitorozási végponthoz. <br/>Engedélyt biztosít az eszközről a felhőbe küldött üzenetek fogadására, a felhőből az eszközre küldött üzenetek küldésére és a megfelelő kézbesítési nyugták lekérésére. <br/>Engedélyt biztosít a fájlfeltöltések kézbesítési nyugtázásának lekérésére. <br/>Engedélyezi az ikereszköz hozzáférését a címkék és a kívánt tulajdonságok frissítéséhez, jelentett tulajdonságok lekéréséhez és lekérdezések futtatásához. <br/>Ezt az engedélyt a háttérbeli felhőszolgáltatások használják. |
| **DeviceConnect** |Hozzáférést biztosít az eszköz felé néző végpontok számára. <br/>Engedélyt biztosít az eszközről a felhőbe küldött üzenetek küldésére és a felhőből az eszközre küldött üzenetek fogadására. <br/>Engedélyezi a fájlfeltöltést egy eszközről. <br/>Engedélyezi az ikereszköz kívánt tulajdonságértesítéseinek fogadását és az ikereszköz jelentett tulajdonságainak frissítését. <br/>Engedélyezi a fájlfeltöltést. <br/>Ezt az engedélyt az eszközök használják. |

## <a name="additional-reference-material"></a>További referenciaanyagok

A fejlesztői útmutató további referencia-IoT Hub a következők:

* [IoT Hub végpontok](iot-hub-devguide-endpoints.md) az egyes IoT Hubok által a futásidőre és a felügyeleti műveletekre elérhetővé tevés különböző végpontokat ismertetik.

* [A szabályozás és a](iot-hub-devguide-quotas-throttling.md) kvóták azokat a kvótákat és szabályozási viselkedéseket ismertetik, amelyek a IoT Hub vonatkoznak.

* [Az Azure IoT eszköz- és szolgáltatás-SDK-k](iot-hub-devguide-sdks.md) felsorolják azokat a különböző nyelvi SDK-kat, amelyek használhatók az eszköz- és szolgáltatásalkalmazások fejlesztéséhez, amelyek együttműködnek a IoT Hub.

* [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) azt a lekérdezési nyelvet írja le, amely segítségével információkat lehet lekérni IoT Hub ikereszközeiről és -feladatairól.

* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat tartalmaz az MQTT IoT Hub támogatásához.

* [RFC 5246 – A Transport Layer Security (TLS) protokoll 1.2-es](https://tools.ietf.org/html/rfc5246/) verziója további információt nyújt a TLS-hitelesítésről.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan szabályozhatja a IoT Hub, érdekelheti az alábbi IoT Hub fejlesztői útmutató témakörei:

* [Ikereszköz használata az állapot és a konfigurációk szinkronizálása során](iot-hub-devguide-device-twins.md)
* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Ha ki szeretné próbálni a cikkben ismertetett fogalmakat, tekintse meg a következő IoT Hub oktatóanyagokat:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-node.md)
* [Felhőből az eszközre küldött üzenetek küldése IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Eszközről IoT Hub felhőbe küldött üzenetek feldolgozása](tutorial-routing.md)
