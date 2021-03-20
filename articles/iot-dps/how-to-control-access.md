---
title: Biztonsági végpontok a IoT Device kiépítési szolgáltatásban | Microsoft Docs
description: Fogalmak – a IoT Device kiépítési szolgáltatáshoz (DPS) való hozzáférés szabályozása háttérbeli alkalmazásokhoz. A biztonsági jogkivonatokkal kapcsolatos információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954324"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Az Azure IoT Hub Device Provisioning Service hozzáférés-vezérlése

Ez a cikk a IoT-eszközök kiépítési szolgáltatásának biztonságossá tételének lehetőségeit ismerteti. A kiépítési szolgáltatás *engedélyeket* használ az egyes végpontokhoz való hozzáférés biztosításához. Az engedélyek funkció alapján korlátozzák a szolgáltatáshoz való hozzáférést.

Ez a cikk a következőket ismerteti:

* A háttér-alkalmazások számára a kiépítési szolgáltatás eléréséhez megadható különböző engedélyek.
* A hitelesítési folyamat és az általa használt tokenek az engedélyek ellenőrzéséhez.

### <a name="when-to-use"></a>A következő esetekben használja

Megfelelő engedélyekkel kell rendelkeznie a kiépítési szolgáltatási végpontok eléréséhez. Például egy háttérbeli alkalmazásnak tartalmaznia kell egy biztonsági hitelesítő adatokat tartalmazó jogkivonatot, valamint a szolgáltatásnak küldött összes üzenetet.

## <a name="access-control-and-permissions"></a>Hozzáférés-vezérlés és engedélyek

A következő módokon adhat meg [engedélyeket](#device-provisioning-service-permissions) :

* **Megosztott hozzáférés engedélyezési házirendjei**. A közös hozzáférésű szabályzatok bármely [engedély](#device-provisioning-service-permissions)kombinációját megadhatják. A házirendeket megadhatja a [Azure Portalban][lnk-management-portal], vagy programozott módon a [Device kiépítési szolgáltatás REST API-jai][lnk-resource-provider-apis]használatával. Az újonnan létrehozott kiépítési szolgáltatás a következő alapértelmezett házirenddel rendelkezik:

* **provisioningserviceowner**: szabályzat minden engedéllyel.

> [!NOTE]
> Részletes információk: [engedélyek](#device-provisioning-service-permissions) .

## <a name="authentication"></a>Hitelesítés

Az Azure IoT Hub Device Provisioning Service hozzáférést biztosít a végpontokhoz egy jogkivonat ellenőrzésével a közös hozzáférési házirendekkel. A biztonsági hitelesítő adatokat, például a szimmetrikus kulcsokat a rendszer soha nem továbbítja a huzalon.

> [!NOTE]
> Az eszköz kiépítési szolgáltatásának erőforrás-szolgáltatója az Azure-előfizetésen keresztül védett, ahogy a [Azure Resource Manager][lnk-azure-resource-manager]összes szolgáltatója.

A biztonsági jogkivonatok létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő szakaszt.

A HTTP az egyetlen támogatott protokoll, és a hitelesítést az **engedélyezési** kérelem fejlécében szereplő érvényes jogkivonat használatával valósítja meg.

#### <a name="example"></a>Példa
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Az [Azure IoT Device kiépítési szolgáltatás SDK][lnk-sdks] -k automatikusan hoznak létre jogkivonatokat a szolgáltatáshoz való csatlakozáskor.

## <a name="security-tokens"></a>Biztonsági jogkivonatok

Az eszköz kiépítési szolgáltatása biztonsági jogkivonatokat használ a szolgáltatások hitelesítéséhez, így elkerülhető a kulcsok továbbítása a dróton. Emellett a biztonsági jogkivonatok időbeli érvényessége és hatóköre korlátozott. Az [Azure IoT Device kiépítési szolgáltatás SDK][lnk-sdks] -k automatikusan hoznak létre tokeneket anélkül, hogy speciális konfigurációra lenne szükség. Egyes esetekben a biztonsági jogkivonatok közvetlen létrehozását és használatát igénylik. Ilyen esetekben a HTTP-felület közvetlen használata is lehetséges.

### <a name="security-token-structure"></a>Biztonsági jogkivonat szerkezete

A biztonsági jogkivonatok használatával időkorlátú hozzáférést biztosíthat a szolgáltatásoknak a IoT-eszköz kiépítési szolgáltatásának adott funkcióihoz. Ahhoz, hogy engedélyt kapjon a kiépítési szolgáltatáshoz való kapcsolódásra, a szolgáltatásoknak közös hozzáférésű vagy szimmetrikus kulccsal aláírt biztonsági jogkivonatokat kell küldeniük.

Egy megosztott hozzáférési kulccsal aláírt jogkivonat hozzáférést biztosít a megosztott hozzáférési szabályzat engedélyeihez társított összes funkcióhoz. 

A biztonsági jogkivonat formátuma a következő:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

A várt értékek a következők:

| Érték | Leírás |
| --- | --- |
| aláírás |Az űrlap HMAC-SHA256 aláírási karakterlánca: `{URL-encoded-resourceURI} + "\n" + expiry` . **Fontos**: a kulcs dekódolása base64-ből történik, és kulcsként használatos a HMAC-sha256 számítás végrehajtásához.|
| lejárta |UTF8-karakterláncok a 00:00:00-es, 1970-os UTC-kor óta eltelt idő másodpercben. |
| {URL-kódolt-resourceURI} | Kisbetűs URL-cím – a kisbetűs erőforrás URI-ja kódolása. A jogkivonattal elérhető végpontok URI-előtagja (szegmens szerint), a IoT-eszköz kiépítési szolgáltatásának állomásneve (nincs protokoll). Például: `mydps.azure-devices-provisioning.net`. |
| PolicyName |Annak a megosztott hozzáférési házirendnek a neve, amelyre ez a jogkivonat hivatkozik. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Összehasonlításként a biztonsági jogkivonat létrehozásához megfelelő Python-kód a következő:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Mivel a jogkivonat időbeli érvényességét a rendszer a IoT-eszköz kiépítési szolgáltatásán ellenőrzi, a tokent generáló gép óráján való eltolódásnak minimálisnak kell lennie.

### <a name="use-security-tokens-from-service-components"></a>Biztonsági jogkivonatok használata a szolgáltatás-összetevőkből

A szolgáltatás-összetevők csak a korábban ismertetett megfelelő engedélyeket biztosító megosztott hozzáférési szabályzatok használatával hozhatnak elő biztonsági jogkivonatokat.

Itt láthatók a végpontokon elérhető szolgáltatási függvények:

| Végpont | Funkció |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Eszköz-beléptetési műveleteket biztosít az eszköz kiépítési szolgáltatásával. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Az eszközök beléptetési csoportjainak felügyeletére szolgáló műveleteket biztosít. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Az eszközök regisztrációinak állapotának beolvasására és kezelésére szolgáló műveleteket biztosít. |


Például egy **enrollmentread** nevű, előre létrehozott megosztott hozzáférési házirend használatával generált szolgáltatás létrehoz egy jogkivonatot a következő paraméterekkel:

* erőforrás URI-ja: `{mydps}.azure-devices-provisioning.net` ,
* aláíró kulcs: a szabályzat egyik kulcsa `enrollmentread` ,
* Házirend neve: `enrollmentread` ,
* bármely lejárati idő. backn

![Megosztott hozzáférési szabályzat létrehozása az eszköz kiépítési szolgáltatásának példányához a portálon][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Az eredmény, amely hozzáférést biztosít az összes beléptetési rekord olvasásához:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Hivatkozási témakörök:

A következő témakörök további információkat nyújtanak a IoT-eszközök kiépítési szolgáltatásához való hozzáférés szabályozásáról.

### <a name="device-provisioning-service-permissions"></a>Eszköz kiépítési szolgáltatásának engedélyei

A következő táblázat felsorolja azokat az engedélyeket, amelyek segítségével szabályozhatja a IoT-eszközök kiépítési szolgáltatásának elérését.

| Engedély | Jegyzetek |
| --- | --- |
| **ServiceConfig** |Hozzáférést biztosít a szolgáltatás konfigurációinak módosításához. <br/>Ezt az engedélyt a háttérbeli Cloud Services használja. |
| **EnrollmentRead** |Olvasási hozzáférést biztosít az eszközök regisztrálásához és a beléptetési csoportokhoz. <br/>Ezt az engedélyt a háttérbeli Cloud Services használja. |
| **EnrollmentWrite** |Írási hozzáférést biztosít az eszközök regisztrálásához és a beléptetési csoportokhoz. <br/>Ezt az engedélyt a háttérbeli Cloud Services használja. |
| **RegistrationStatusRead** |Olvasási hozzáférést biztosít az eszköz regisztrációs állapotához. <br/>Ezt az engedélyt a háttérbeli Cloud Services használja. |
| **RegistrationStatusWrite**  |Engedélyezi az eszköz regisztrációs állapotának törlését. <br/>Ezt az engedélyt a háttérbeli Cloud Services használja. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/