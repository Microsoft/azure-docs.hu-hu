---
title: Az Azure IoT Hub biztonság ismertetése | Microsoft Docs
description: Fejlesztői útmutató – a IoT Hubhoz való hozzáférés szabályozása az eszközök alkalmazásaihoz és a háttérbeli alkalmazásokhoz. A biztonsági jogkivonatokkal és az X. 509 tanúsítványok támogatásával kapcsolatos információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 3ddc8c78bac47ed85266037341328585e3c7cb1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825119"
---
# <a name="control-access-to-iot-hub"></a>IoT Hub-hozzáférés szabályozása

Ez a cikk az IoT hub biztonságossá tételének lehetőségeit ismerteti. IoT Hub az *engedélyek* használatával engedélyezi az egyes IoT hub-végpontokhoz való hozzáférést. Az engedélyek a funkcionalitás alapján korlátozzák az IoT hub elérését.

Ez a cikk a következőket mutatja be:

* A IoT hub eléréséhez az eszközre vagy a háttér-alkalmazásra vonatkozó különböző engedélyek adhatók meg.
* A hitelesítési folyamat és az általa használt tokenek az engedélyek ellenőrzéséhez.
* A hitelesítő adatok hatókörének korlátozása az adott erőforrásokhoz való hozzáférés korlátozására.
* Az X. 509 tanúsítványok IoT Hub támogatása.
* Egyéni eszköz-hitelesítési mechanizmusok, amelyek meglévő eszköz-identitást vagy hitelesítési sémákat használnak.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A IoT Hub-végpontok bármelyikének eléréséhez megfelelő engedélyekkel kell rendelkeznie. Például egy eszköznek tartalmaznia kell egy biztonsági hitelesítő adatokat tartalmazó jogkivonatot, valamint minden üzenetet, amelyet a IoT Hub küld.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

A következő módokon adhat meg [engedélyeket](#iot-hub-permissions) :

* **IoT hub szintű megosztott hozzáférési szabályzatok**. A közös hozzáférésű szabályzatok bármely [engedély](#iot-hub-permissions)kombinációját megadhatják. Házirendeket adhat meg a [Azure Portalban](https://portal.azure.com), programozott módon, a [IoT hub erőforrás REST API](/rest/api/iothub/iothubresource)-k használatával vagy az az [IoT hub Policy](/cli/azure/iot/hub/policy) CLI használatával. Az újonnan létrehozott IoT hub a következő alapértelmezett házirendekkel rendelkezik:
  
  | Megosztott elérési házirend | Engedélyek |
  | -------------------- | ----------- |
  | iothubowner | Minden engedély |
  | szolgáltatás | **ServiceConnect** engedélyek |
  | eszköz | **DeviceConnect** engedélyek |
  | registryRead | **RegistryRead** engedélyek |
  | registryReadWrite | **RegistryRead** és **RegistryWrite** engedélyek |

* **Eszközönkénti biztonsági hitelesítő adatok**. Minden IoT Hub tartalmaz egy [identitás-beállításjegyzéket](iot-hub-devguide-identity-registry.md) az identitás-beállításjegyzék minden eszközéhez, konfigurálhatja azokat a biztonsági hitelesítő adatokat, amelyek a **DeviceConnect** engedélyeket biztosítják a megfelelő eszköz-végpontokra.

Például egy tipikus IoT-megoldásban:

* Az eszközkezelés összetevő a *registryReadWrite* szabályzatot használja.
* Az Event Processor összetevő a *szolgáltatási* házirendet használja.
* A futásidejű eszköz üzleti logikája összetevő a *szolgáltatási* házirendet használja.
* Az egyes eszközök az IoT hub Identity registryben tárolt hitelesítő adatok használatával csatlakoznak.

> [!NOTE]
> Részletes információk: [engedélyek](#iot-hub-permissions) .

## <a name="authentication"></a>Hitelesítés

Az Azure IoT Hub egy biztonsági jogkivonatnak a megosztott hozzáférési szabályzatok és az identitásjegyzékbeli biztonsági hitelesítő adatok alapján végzett ellenőrzésével ad hozzáférést a végpontokhoz.

A biztonsági hitelesítő adatokat, például a szimmetrikus kulcsokat a rendszer soha nem továbbítja a huzalon.

> [!NOTE]
> Az Azure IoT Hub erőforrás-szolgáltatót az Azure-előfizetése biztosítja, mint a [Azure Resource Manager](../azure-resource-manager/management/overview.md)összes szolgáltatója.

További információ a biztonsági jogkivonatok létrehozásáról és használatáról: [IoT hub biztonsági jogkivonatok](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokollok sajátosságai

Minden támogatott protokoll, például a MQTT, a AMQP és a HTTPS, különböző módokon szállítja át a jogkivonatokat.

A MQTT használatakor a KAPCSOLÓDÁSi csomag rendelkezik a deviceId ClientId, a `{iothubhostname}/{deviceId}` Felhasználónév mezőben és egy sas-jogkivonattal a Password (jelszó) mezőben. `{iothubhostname}` az IoT hub teljes CName értékének kell lennie (például contoso.azure-devices.net).

A [AMQP](https://www.amqp.org/)használatakor a IoT hub támogatja a [SASL Plain](https://tools.ietf.org/html/rfc4616) és a [AMQP-alapú biztonsági jogcímeket](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Ha a AMQP-alapú biztonsági jogcímeket használja, a standard határozza meg, hogyan továbbíthatja ezeket a jogkivonatokat.

SASL PLAIN a **Felhasználónév** a következő lehet:

* `{policyName}@sas.root.{iothubName}` IoT hub szintű jogkivonatok használata esetén.
* `{deviceId}@sas.{iothubname}` eszköz-hatókörű tokenek használata esetén.

A jelszó mező mindkét esetben tartalmazza a jogkivonatot a [IoT hub biztonsági jogkivonatok](iot-hub-devguide-security.md#security-tokens)című cikkben leírtak szerint.

A HTTPS a hitelesítést az **engedélyezési** kérelem fejlécében szereplő érvényes jogkivonat belefoglalásával valósítja meg.

#### <a name="example"></a>Példa

Felhasználónév (a DeviceId a kis-és nagybetűk megkülönböztetése): `iothubname.azure-devices.net/DeviceId`

Password (létrehozhat egy SAS-jogkivonatot a CLI kiterjesztési paranccsal az [IOT hub létrehoz-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)vagy a [Visual Studio Code-hoz készült Azure IOT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)használatával):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k automatikusan állítanak elő jogkivonatokat a szolgáltatáshoz való csatlakozáskor. Bizonyos esetekben az Azure IoT SDK-k nem támogatják az összes protokollt vagy az összes hitelesítési módszert.

### <a name="special-considerations-for-sasl-plain"></a>A SASL PLAIN speciális szempontjai

Ha SASL PLAINt használ a AMQP-vel, az IoT hub-hoz csatlakozó ügyfelek egyetlen jogkivonatot használhatnak az egyes TCP-kapcsolatokhoz. Ha a jogkivonat lejár, a TCP-kapcsolat megszakad a szolgáltatástól, és újracsatlakozást indít el. Ez a viselkedés, miközben a háttérbeli alkalmazások esetében nem problematikus, a következő okok miatt károsítja az eszköz alkalmazásait:

* Az átjárók általában sok eszköz nevében csatlakoznak. SASL PLAIN használatakor külön TCP-kapcsolatot kell létrehoznia minden olyan eszközhöz, amely egy IoT hubhoz csatlakozik. Ez a forgatókönyv jelentősen növeli az energiaellátási és hálózati erőforrások felhasználását, és növeli az egyes eszközök kapcsolatának késését.

* Az erőforrás-korlátozás alá tartozó eszközöket hátrányosan befolyásolja az erőforrások megnövekedett használata az egyes tokenek lejárata utáni újrakapcsolódáshoz.

## <a name="scope-iot-hub-level-credentials"></a>Hatókör IoT-hub szintű hitelesítő adatai

A IoT hub-szintű biztonsági házirendek hatókörét korlátozott erőforrás-URI-val rendelkező jogkivonatok létrehozásával is elvégezheti. Például az eszközről a felhőbe irányuló üzenetek **/Devices/{deviceId}/messages/Events** való küldésének végpontja. Az IoT hub szintű közös hozzáférési szabályzatot **DeviceConnect** engedélyekkel is elvégezheti olyan token aláírására, amelynek resourceURI a **/Devices/{deviceId}**. Ez a megközelítés olyan jogkivonatot hoz létre, amely csak az eszköz **deviceId** nevében való üzenetküldésre használható.

Ez a mechanizmus hasonló a [Event Hubs közzétevői házirendhez](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab), és lehetővé teszi az egyéni hitelesítési módszerek megvalósítását.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

A IoT Hub biztonsági jogkivonatokat használ az eszközök és szolgáltatások hitelesítéséhez, hogy elkerülje a kulcsok küldését a huzalon. Emellett a biztonsági jogkivonatok időbeli érvényessége és hatóköre korlátozott. Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k automatikusan állítanak elő jogkivonatokat anélkül, hogy speciális konfigurációra lenne szükség. Egyes esetekben a biztonsági jogkivonatok közvetlen létrehozását és használatát igénylik. Ilyen forgatókönyvek például a következők:

* A MQTT, a AMQP vagy a HTTPS felületek közvetlen használata.

* A jogkivonat-szolgáltatási minta implementációja, ahogy az az [Egyéni eszközök hitelesítése](iot-hub-devguide-security.md#custom-device-and-module-authentication)című részben is látható.

A IoT Hub lehetővé teszi az eszközök számára, hogy a IoT Hub [X. 509 tanúsítvánnyal](iot-hub-devguide-security.md#supported-x509-certificates)hitelesítsék magukat.

### <a name="security-token-structure"></a>Biztonsági jogkivonat szerkezete

A biztonsági jogkivonatok segítségével időt és szolgáltatásokat biztosíthat az eszközökhöz és szolgáltatásokhoz a IoT Hub adott funkcióihoz. Ahhoz, hogy engedélyt kapjon a IoT Hubhoz való kapcsolódásra, az eszközöknek és a szolgáltatásoknak a közös hozzáférésű vagy szimmetrikus kulccsal aláírt biztonsági jogkivonatokat kell küldeniük. Ezeket a kulcsokat egy eszköz identitása tárolja a személyazonossági beállításjegyzékben.

Egy megosztott hozzáférési kulccsal aláírt jogkivonat hozzáférést biztosít a megosztott hozzáférési szabályzat engedélyeihez társított összes funkcióhoz. Az eszköz identitásának szimmetrikus kulcsával aláírt jogkivonat csak a **DeviceConnect** engedélyt biztosítja a társított eszköz identitásához.

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

A várt értékek a következők:

| Érték | Leírás |
| --- | --- |
| aláírás |Az űrlap HMAC-SHA256 aláírási karakterlánca: `{URL-encoded-resourceURI} + "\n" + expiry` . **Fontos**: a kulcs dekódolása base64-ből történik, és kulcsként használatos a HMAC-sha256 számítás végrehajtásához. |
| ResourceURI |A jogkivonattal elérhető végpontok URI-előtagja (szegmens szerint), az IoT hub állomásneve (nincs protokoll). Például: `myHub.azure-devices.net/devices/device1` |
| lejárta |UTF8-karakterláncok a 00:00:00-es, 1970-os UTC-kor óta eltelt idő másodpercben. |
| {URL-kódolt-resourceURI} |Kisbetűs URL-cím – a kisbetűs erőforrás URI-ja kódolása |
| PolicyName |Annak a megosztott hozzáférési házirendnek a neve, amelyre ez a jogkivonat hivatkozik. Hiányzik, ha a jogkivonat az eszköz beállításjegyzékbeli hitelesítő adataira hivatkozik. |

**Megjegyzés az előtagnál**: az URI-előtagot szegmens és nem karakter alapján számítja ki a rendszer. Például `/a/b` egy előtag, `/a/b/c` de nem `/a/bc` .

A következő Node.js kódrészlet egy **generateSasToken** nevű függvényt mutat be, amely kiszámítja a tokent a bemenetekben `resourceUri, signingKey, policyName, expiresInMins` . A következő részek részletesen ismertetik, hogyan inicializálhatja a különböző adatforrásokat a különböző jogkivonat-használati esetekben.

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

Összehasonlításként a biztonsági jogkivonat létrehozásához megfelelő Python-kód a következő:

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

A C#-ben a biztonsági jogkivonat létrehozásához szükséges funkciók a következőek:

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

Java esetén:
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
> Mivel a jogkivonat érvényessége IoT Hub gépeken van érvényesítve, a tokent generáló gép óráján való eltolódásnak minimálisnak kell lennie.

### <a name="use-sas-tokens-in-a-device-app"></a>SAS-tokenek használata egy eszköz alkalmazásban

A **DeviceConnect** -engedélyeket kétféleképpen lehet megszerezni IoT hub biztonsági jogkivonatokkal: használjon [szimmetrikus eszközt az azonosító beállításjegyzékből](#use-a-symmetric-key-in-the-identity-registry), vagy használjon [megosztott hozzáférési kulcsot](#use-a-shared-access-policy).

Ne feledje, hogy az eszközökről elérhető összes funkció megtervezése az előtaggal rendelkező végpontokon történik `/devices/{deviceId}` .

> [!IMPORTANT]
> Egy adott eszköz hitelesítésének egyetlen módja, ha a IoT Hub az eszköz identitásának szimmetrikus kulcsát használja. Azokban az esetekben, amikor megosztott hozzáférési házirendet használ az eszköz funkcióinak eléréséhez, a megoldásnak meg kell fontolnia a biztonsági jogkivonatot kiállító összetevőt megbízható alösszetevőként.

Az eszközre irányuló végpontok (a protokolltól függetlenül):

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Eszközről a felhőbe irányuló üzenetek küldése. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |A felhőből az eszközre irányuló üzenetek fogadása. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Szimmetrikus kulcs használata az Identity registryben

Ha az eszköz identitásának szimmetrikus kulcsát használja a jogkivonat létrehozásához, a jogkivonat policyName ( `skn` ) elemét a rendszer kihagyja.

Az összes eszköz funkció eléréséhez létrehozott tokennek például a következő paraméterekkel kell rendelkeznie:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* aláíró kulcs: az identitáshoz tartozó bármely szimmetrikus kulcs `{device id}` ,
* nincs szabályzat neve,
* bármely lejárati idő.

Az előző Node.js függvényt használó példa a következő:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Az eredmény, amely a device1 összes funkciója számára hozzáférést biztosít:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> SAS-tokent hozhat létre a CLI-bővítmény paranccsal az [IOT hub generált-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)vagy a [Visual Studio Code-hoz készült Azure IOT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)használatával.

### <a name="use-a-shared-access-policy"></a>Megosztott elérési házirend használata

Ha megosztott hozzáférési házirendből hoz létre jogkivonatot, állítsa a `skn` mezőt a szabályzat nevére. Ennek a szabályzatnak meg kell adnia a **DeviceConnect** engedélyt.

Az eszköz funkcióinak eléréséhez a következő két fő forgatókönyv használható a megosztott hozzáférési házirendek használatára:

* [Cloud Protocol-átjárók](iot-hub-devguide-endpoints.md),
* egyéni hitelesítési sémák megvalósításához használt [jogkivonat-szolgáltatások](iot-hub-devguide-security.md#custom-device-and-module-authentication) .

Mivel a megosztott hozzáférési házirend bármely eszközhöz hozzáférést biztosíthat a csatlakozáshoz, fontos, hogy a megfelelő erőforrás-URI-t használja a biztonsági jogkivonatok létrehozásakor. Ez a beállítás különösen fontos a jogkivonat-szolgáltatások esetében, amelyeknek a tokent egy adott eszközre kell kiterjedniük az erőforrás URI-ja alapján. Ez a pont kevésbé vonatkozik a protokoll-átjárók esetében, mivel azok már közvetítik a forgalmat az összes eszközön.

Például az **eszköz** nevű, előre létrehozott megosztott elérési házirendet használó jogkivonat-szolgáltatás létrehoz egy jogkivonatot a következő paraméterekkel:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* aláíró kulcs: a szabályzat egyik kulcsa `device` ,
* Házirend neve: `device` ,
* bármely lejárati idő.

Az előző Node.js függvényt használó példa a következő:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely a device1 összes funkciója számára hozzáférést biztosít:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Egy protokoll-átjáró ugyanazt a jogkivonatot használhatja az összes eszközhöz, egyszerűen állítsa be az erőforrás-URI-t `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Biztonsági jogkivonatok használata a szolgáltatás-összetevőkből

A szolgáltatás-összetevők csak a korábban ismertetett megfelelő engedélyeket biztosító megosztott hozzáférési szabályzatok használatával hozhatnak elő biztonsági jogkivonatokat.

Itt láthatók a végpontokon elérhető szolgáltatási függvények:

| Végpont | Funkció |
| --- | --- |
| `{iot hub host name}/devices` |Eszköz-identitások létrehozása, frissítése, beolvasása és törlése. |
| `{iot hub host name}/messages/events` |Eszközről a felhőbe irányuló üzenetek fogadása. |
| `{iot hub host name}/servicebound/feedback` |Visszajelzés küldése a felhőből az eszközre irányuló üzenetek fogadásához. |
| `{iot hub host name}/devicebound` |Üzenetküldés a felhőből az eszközökre. |

Például a **registryRead** nevű, előre létrehozott megosztott elérési házirend használatával generált szolgáltatás létrehoz egy jogkivonatot a következő paraméterekkel:

* erőforrás URI-ja: `{IoT hub name}.azure-devices.net/devices` ,
* aláíró kulcs: a szabályzat egyik kulcsa `registryRead` ,
* Házirend neve: `registryRead` ,
* bármely lejárati idő.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely hozzáférést biztosít az összes eszköz identitásának olvasásához, a következő lenne:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Támogatott X. 509 tanúsítványok

Bármely X. 509 tanúsítvány használatával hitelesítheti az eszközöket IoT Hub egy tanúsítvány-ujjlenyomat vagy egy hitelesítésszolgáltató (CA) az Azure IoT Hubba való feltöltésével. Hitelesítés a ujjlenyomatai megfelelnek használatával ellenőrzi, hogy a megjelenített ujjlenyomat megegyezik-e a beállított ujjlenyomattal. A hitelesítésszolgáltatóval történő hitelesítés ellenőrzi a tanúsítványláncot. Mindkét esetben a TLS-kézfogás megköveteli, hogy az eszköz rendelkezzen érvényes tanúsítvánnyal és titkos kulccsal. További részletekért tekintse meg a TLS-specifikációt, például: [RFC 5246 – a Transport Layer Security (TLS) protokoll 1,2-es verziója](https://tools.ietf.org/html/rfc5246/).

A támogatott tanúsítványok a következők:

* **Egy meglévő X. 509 tanúsítvány**. Lehetséges, hogy egy eszközhöz már van társítva X. 509 tanúsítvány. Az eszköz ezt a tanúsítványt használja a IoT Hub való hitelesítéshez. Ujjlenyomattal vagy HITELESÍTÉSSZOLGÁLTATÓI hitelesítéssel is működik. 

* **Hitelesítésszolgáltató által aláírt X. 509 tanúsítvány**. Az eszköz azonosításához és a IoT Hub használatával történő hitelesítéséhez használhatja a hitelesítésszolgáltató által létrehozott és aláírt X. 509 tanúsítványt. Ujjlenyomattal vagy HITELESÍTÉSSZOLGÁLTATÓI hitelesítéssel is működik.

* **Egy saját maga által létrehozott és önaláírt X-509 tanúsítvány**. Az eszköz gyártója vagy a házon belüli telepítő előállíthatja ezeket a tanúsítványokat, és tárolhatja a megfelelő titkos kulcsot (és tanúsítványt) az eszközön. Erre a célra olyan eszközöket is használhat, mint például az [OpenSSL](https://www.openssl.org/) és a [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) segédprogram. Csak ujjlenyomat-hitelesítéssel működik.

Egy eszköz X. 509 tanúsítványt vagy biztonsági jogkivonatot használhat a hitelesítéshez, de mindkettőt nem. Az X. 509 tanúsítványalapú hitelesítéssel ellenőrizze, hogy van-e olyan stratégia, amely a tanúsítvány átváltását kezeli, ha egy meglévő tanúsítvány lejár.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítést használó eszközök esetében a következő funkciók nem támogatottak:

* HTTPS, MQTT over WebSockets, és AMQP over WebSockets protokollok.
* Fájlfeltöltés (minden protokoll).

A hitelesítésszolgáltatóval történő hitelesítéssel kapcsolatos további információkért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](iot-hub-x509ca-overview.md). További információ az IoT hub-beli hitelesítésszolgáltató feltöltéséről és ellenőrzéséről: az [X. 509 Biztonság beállítása az Azure IoT hub-ban](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>X. 509 tanúsítvány regisztrálása egy eszközhöz

A C#-hoz készült [Azure IoT Service SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (1.0.8 + verzió) támogatja az X. 509 tanúsítvánnyal rendelkező eszközök regisztrálását a hitelesítéshez. Más API-k, például az eszközök importálása/exportálása is támogatja az X. 509 tanúsítványokat.

Az eszközökhöz tartozó X. 509 tanúsítványok konfigurálásához a CLI-bővítmény parancs az [IOT hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) parancsot is használhatja.

### <a name="c-support"></a>C- \# támogatás

A **RegistryManager** osztály az eszközök regisztrálására szolgáló programozott módszert biztosít. A **AddDeviceAsync** és a **UpdateDeviceAsync** metódus lehetővé teszi az eszközök regisztrálását és frissítését az IoT hub Identity registryben. Ez a két módszer bemenetként veszi fel az **eszköz** példányát. Az **eszközosztály** tartalmaz egy olyan **hitelesítési** tulajdonságot, amely lehetővé teszi az elsődleges és másodlagos X. 509 tanúsítvány ujjlenyomatai megfelelnek megadását. Az ujjlenyomat az X. 509 tanúsítvány SHA256 kivonatát jelöli (a bináris DER kódolással tárolva). Lehetősége van elsődleges ujjlenyomat vagy másodlagos ujjlenyomat vagy mindkettő megadására. A tanúsítvány-átváltási forgatókönyvek kezeléséhez az elsődleges és a másodlagos ujjlenyomatai megfelelnek is támogatott.

Az alábbi minta C kódrészlettel \# regisztrálhat egy eszközt X. 509 tanúsítvány-ujjlenyomat használatával:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>X. 509 tanúsítvány használata a futásidejű műveletek során

A [.net-hez készült Azure IoT Device SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (1.0.11 +) támogatja az X. 509 tanúsítványok használatát.

### <a name="c-support"></a>C- \# támogatás

A **DeviceAuthenticationWithX509Certificate** osztály támogatja a **DeviceClient** -példányok létrehozását X. 509 tanúsítvány használatával. Az X. 509 tanúsítványnak a titkos kulcsot tartalmazó PFX (más néven PKCS #12) formátumúnak kell lennie.

Az alábbi kódrészlet a következő:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Egyéni eszköz-és modul-hitelesítés

Az egyes eszközök/modulok biztonsági hitelesítő adatai és a hozzáférés-vezérlés [tokenek](iot-hub-devguide-security.md#security-tokens)használatával történő konfigurálásához használhatja a IoT hub [Identity registryt](iot-hub-devguide-identity-registry.md) . Ha egy IoT-megoldásnak már van egyéni identitás-beállításjegyzéke és/vagy hitelesítési sémája, érdemes lehet létrehozni egy *jogkivonat-szolgáltatást* az infrastruktúra IoT hub való integrálásához. Így más IoT funkciókat is használhat a megoldásban.

A jogkivonat-szolgáltatás egy egyéni felhőalapú szolgáltatás. Egy IoT Hub *megosztott hozzáférési szabályzatot* használ, amely **DeviceConnect** vagy **ModuleConnect** engedélyekkel rendelkezik az *eszközre* vagy a *modulra* vonatkozó hatókörű tokenek létrehozásához. Ezek a tokenek lehetővé teszik az eszközök és modulok kapcsolódását az IoT hubhoz.

![A jogkivonat-szolgáltatási minta lépései](./media/iot-hub-devguide-security/tokenservice.png)

A jogkivonat-szolgáltatás mintájának fő lépései a következők:

1. Hozzon létre egy IoT Hub megosztott hozzáférési szabályzatot az IoT hub **DeviceConnect** -vagy **ModuleConnect** -engedélyeivel. Ezt a szabályzatot a [Azure Portal](https://portal.azure.com) vagy programozott módon is létrehozhatja. A jogkivonat-szolgáltatás ezt a házirendet használja a létrehozott jogkivonatok aláírásához.

2. Ha egy eszköznek/modulnak hozzá kell férnie az IoT hub-hoz, a jogkivonat-szolgáltatás aláírt jogkivonatot kér. Az eszköz hitelesíthető az egyéni identitás-regisztrációs vagy hitelesítési sémával annak megállapításához, hogy a jogkivonat-szolgáltatás melyik eszközt használja a jogkivonat létrehozásához.

3. A jogkivonat-szolgáltatás visszaadja a tokent. A jogkivonat a vagy a használatával jön létre `/devices/{deviceId}` `/devices/{deviceId}/module/{moduleId}` `resourceURI` , `deviceId` mint az eszköz hitelesítve vagy `moduleId` a hitelesítő modul. A jogkivonat-szolgáltatás a megosztott elérési házirendet használja a jogkivonat létrehozásához.

4. Az eszköz/modul a tokent közvetlenül az IoT hub használatával használja.

> [!NOTE]
> A token szolgáltatásban a .NET Class [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) vagy a Java Class [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) használatával hozhat létre jogkivonatot.

A jogkivonat-szolgáltatás igény szerint állíthatja be a jogkivonat lejáratát. Ha a jogkivonat lejár, az IoT hub megszakítja az eszköz/modul kapcsolatát. Ezt követően az eszköznek/modulnak új jogkivonatot kell igényelnie a jogkivonat-szolgáltatástól. A rövid lejárati idő növeli az eszköz/modul és a jogkivonat-szolgáltatás terhelését.

Ahhoz, hogy egy eszköz/modul csatlakozhasson a központhoz, hozzá kell adnia azt a IoT Hub Identity registryhez, annak ellenére, hogy tokent használ, és nem a kapcsolódáshoz szükséges kulcsot. Így továbbra is használhatja az eszközönkénti/Modulos hozzáférés-vezérlést az eszközök/modulok identitásának engedélyezésével vagy letiltásával az [Identity registryben](iot-hub-devguide-identity-registry.md). Ez a megközelítés csökkenti a jogkivonatok hosszú lejárati idővel való használatának kockázatait.

### <a name="comparison-with-a-custom-gateway"></a>Összehasonlítás egyéni átjáróval

A jogkivonat-szolgáltatás mintája az ajánlott módszer az egyéni azonosító beállításjegyzék/hitelesítési séma megvalósítására IoT Hub használatával. Ez a minta azért ajánlott, mert IoT Hub továbbra is kezeli a megoldási forgalom nagy részét. Ha azonban az egyéni hitelesítési séma össze van társítva a protokollal, előfordulhat, hogy az összes forgalom feldolgozásához *Egyéni átjáróra* van szükség. Ilyen eset például az [Transport Layer Security (TLS) és az előmegosztott kulcsok (PSKs)](https://tools.ietf.org/html/rfc4279)használata. További információt a [protokoll-átjáróval](iot-hub-protocol-gateway.md) foglalkozó cikkben talál.

## <a name="reference-topics"></a>Hivatkozási témakörök:

A következő témakörök további információkat nyújtanak az IoT hub elérésének szabályozásáról.

## <a name="iot-hub-permissions"></a>Engedélyek IoT Hub

A következő táblázat felsorolja az IoT hub elérésének szabályozásához használható engedélyeket.

| Engedély | Jegyzetek |
| --- | --- |
| **RegistryRead** |Olvasási hozzáférést biztosít az identitás-beállításjegyzékhez. További információ: [Identity Registry](iot-hub-devguide-identity-registry.md). <br/>Ezt az engedélyt a háttérbeli felhőalapú szolgáltatások használják. |
| **RegistryReadWrite** |Olvasási és írási hozzáférést biztosít az identitás-beállításjegyzékhez. További információ: [Identity Registry](iot-hub-devguide-identity-registry.md). <br/>Ezt az engedélyt a háttérbeli felhőalapú szolgáltatások használják. |
| **ServiceConnect** |Hozzáférést biztosít a felhőalapú szolgáltatások felé irányuló kommunikációs és figyelési végpontokhoz. <br/>Engedélyt ad az eszközről a felhőbe irányuló üzenetek fogadására, a felhőből az eszközre küldött üzenetek küldésére és a megfelelő kézbesítési visszaigazolások beolvasására. <br/>Engedélyt ad a fájlok feltöltésére vonatkozó kézbesítési visszaigazolások beolvasására. <br/>Engedélyeket biztosít az ikreknek a címkék és a kívánt tulajdonságok frissítéséhez, a jelentett tulajdonságok lekéréséhez és a lekérdezések futtatásához. <br/>Ezt az engedélyt a háttérbeli felhőalapú szolgáltatások használják. |
| **DeviceConnect** |Hozzáférést biztosít az eszköz felé irányuló végpontokhoz. <br/>Engedélyt ad az eszközről a felhőbe irányuló üzenetek küldésére és a felhőből az eszközre irányuló üzenetek fogadására. <br/>Engedélyt ad a fájlfeltöltés eszközről történő elvégzésére. <br/>Engedélyt ad az eszköz Twin kívánt tulajdonságainak fogadására és az eszköz kettős jelentett tulajdonságainak frissítésére. <br/>Engedélyeket biztosít a fájlfeltöltés végrehajtásához. <br/>Ezt az engedélyt az eszközök használják. |

## <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* [IoT hub végpontok](iot-hub-devguide-endpoints.md) ismertetik a különböző végpontokat, amelyeket az egyes IoT hub a futásidejű és a felügyeleti műveletek számára tesz elérhetővé.

* A [szabályozás és a kvóták](iot-hub-devguide-quotas-throttling.md) a IoT hub szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket ismertetik.

* Az [Azure IoT-eszközök és-szolgáltatások SDK](iot-hub-devguide-sdks.md) -k felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md) leírja a lekérdezési nyelvet, amellyel információkat kérhet le az eszközökről, és feladatairól IoT hub.

* [IOT hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

* [RFC 5246 – a Transport Layer Security (TLS) protokoll 1,2-es verziója](https://tools.ietf.org/html/rfc5246/) további információkat nyújt a TLS-hitelesítésről.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a hozzáférési IoT Hub vezérlését, az alábbi IoT Hub fejlesztői útmutató témaköreiben találhat további információt:

* [Az állapot és a konfigurációk szinkronizálása az eszközök ikrek használatával](iot-hub-devguide-device-twins.md)
* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Ha szeretné kipróbálni a cikkben ismertetett fogalmak némelyikét, tekintse meg a következő IoT Hub oktatóanyagokat:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-node.md)
* [A felhőből az eszközre irányuló üzenetek küldése IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [IoT Hub eszközről a felhőbe irányuló üzenetek feldolgozása](tutorial-routing.md)
