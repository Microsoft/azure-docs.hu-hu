---
title: Oktatóanyag – az ACS SMS API-val való bejelentkezés és kérelmek küldése
titleSuffix: An Azure Communication Services tutorial
description: Megtudhatja, hogyan lehet aláírni és kezdeményezni az ACS-t a Poster használatával SMS-üzenet küldéséhez.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5805734a9253962d672a4236a5650e9de8b37f0a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044294"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Oktatóanyag: a Poster aláírása és a kérelmek küldése
Ebben az oktatóanyagban a Poster-t fogjuk beállítani és használni, hogy a HTTP-t használó Azure kommunikációs szolgáltatások (ACS) szolgáltatásokra vonatkozó kérést hozzon létre. Az oktatóanyag végén az ACS és a Poster használatával sikeresen elküldte az SMS-üzenetet, és a Poster használatával más API-kat is megvizsgálhat az ACS-n belül.

Ebben az oktatóanyagban a következők lesznek:
> [!div class="checklist"]
> * Poster letöltése
> * A Poster beállítása a HTTP-kérelmek aláírására
> * Az ACS SMS API-val kapcsolatos kérelem küldése üzenet küldéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. Az ingyenes fiók $200 az Azure-kreditekben, hogy kipróbálja a szolgáltatások bármely kombinációját.
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Útmutató a kommunikációs szolgáltatások erőforrásának létrehozásához](../quickstarts/create-communication-resource.md).
- Egy ACS-telefonszám, amely SMS-üzeneteket tud küldeni, a [telefonszám beszerzése](../quickstarts/telephony-sms/get-phone-number.md) lehetőségre kattintva megtekintheti az egyiket.

## <a name="downloading-and-installing-postman"></a>Poster letöltése és telepítése

A Poster egy asztali alkalmazás, amely képes API-kérelmeket létrehozni bármely HTTP API-n. Az API-k tesztelésére és feltárására általában használatos. A legújabb [asztali verziót a Poster webhelyéről](https://www.postman.com/downloads/)fogjuk letölteni. A Poster Windows-, Mac-és Linux-verziókkal rendelkezik, ezért töltse le az operációs rendszerének megfelelő verziót. A letöltés után nyissa meg az alkalmazást. Ekkor megjelenik egy kezdőképernyő, amely arra kéri, hogy jelentkezzen be, vagy hozzon létre egy Poster-fiókot. A fiók létrehozása nem kötelező, és az "Ugrás az alkalmazáshoz" hivatkozásra kattintva kihagyható. A fiók létrehozásakor a rendszer menti az API-kérelmek beállításait a Poster-re, amely lehetővé teszi, hogy a kéréseit más számítógépekre is felvegye.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="A Poster kezdőképernyőn megjelenik a fiók létrehozása, illetve az alkalmazásra való ugrás és ugrás.":::

Miután létrehozott egy fiókot, vagy kihagyta a létrehozását, a Poster főablakát kell látnia.

## <a name="creating-and-configuring-a-postman-collection"></a>Poster-gyűjtemény létrehozása és konfigurálása

A Poster számos módon rendszerezheti a kérelmeket. Ebben az oktatóanyagban. Létrehozunk egy Poster-gyűjteményt. Ehhez kattintson a gyűjtemények gombra az alkalmazás bal oldali oldalán:

:::image type="content" source="media/postman/collections-tab.png" alt-text="A Poster főképernyője a gyűjtemények lapon kiemelve.":::

Ha kijelöli, kattintson az "új gyűjtemény létrehozása" elemre a gyűjtemény létrehozási folyamatának elindításához. Ekkor megnyílik egy új lap a Poster középső területén. Adja meg a gyűjtemény nevét, amit szeretne. Itt a gyűjtemény neve "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="A Poster egy megnyitott ACS-gyűjteményt és a gyűjtemény nevét emeli ki.":::

Miután létrehozta és elnevezte a gyűjteményt, készen áll a konfigurálására.

### <a name="adding-collection-variables"></a>Gyűjtemény változóinak hozzáadása

A hitelesítés kezeléséhez és a kérések megkönnyítéséhez két gyűjtemény változót adunk meg az újonnan létrehozott ACS-gyűjteményen belül. Ezek a változók az ACS-gyűjteményben lévő összes kérelem számára elérhetők. A változók létrehozásának megkezdéséhez látogasson el a gyűjtemény Variable's lapjára.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Poster egy ACS-gyűjtemény változók lapjával.":::

A gyűjtemény lapon hozzon létre két változót:
- kulcs – ez a változó a Azure Portalon belüli Azure kommunikációs szolgáltatások kulcsfontosságú oldalának egyik kulcsa. Például: `oW...A==`.
- végpont – ezt a változót az Azure kommunikációs szolgáltatások végpontjának kell lennie a kulcs oldalon. **Győződjön meg róla, hogy eltávolítja a záró perjelet**. Például: `https://contoso.communication.azure.com`.

Adja meg ezeket az értékeket a változók képernyő "kezdeti érték" oszlopában. Ha belépett, nyomja le az "összes megtartása" gombot a jobb oldalon található tábla fölött. Ha helyesen konfigurálta a Poster-képernyőt, a következőhöz hasonlóan kell kinéznie:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="A Poster az ACS-gyűjtemény változóinak megfelelően lett beállítva.":::

A változókról a [Poster dokumentációjában](https://learning.postman.com/docs/sending-requests/variables)olvashat bővebben.

### <a name="creating-a-pre-request-script"></a>Előkérési parancsfájl létrehozása

A következő lépés egy előkérési parancsfájl létrehozása a postán belül. Az előkérési parancsfájl egy olyan parancsfájl, amely a Poster minden kérelme előtt fut, és az Ön nevében módosíthatja vagy megváltoztathatja a kérelmek paramétereit. Ezt a HTTP-kérések aláírására fogjuk használni, hogy az ACS szolgáltatásai hitelesíthetők legyenek. Az aláírási követelményekkel kapcsolatos további információkért [olvassa el az útmutatót a hitelesítéshez](/rest/api/communication/authentication).

Ezt a szkriptet a gyűjteményen belül fogjuk létrehozni úgy, hogy az a gyűjteményen belül bármilyen kérelemre fusson. Ehhez a gyűjtemény lapon kattintson a "pre-Request script" Sub-Tab elemre.

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="A Poster egy ACS-gyűjtemény előkérési parancsfájl Sub-Tab kiválasztva.":::

Ezen az allapon létrehozhat egy előkérési parancsfájlt úgy, hogy beírja azt az alábbi szövegmezőbe. Ez a teljes programkód-szerkesztőben, például a [Visual Studio Code](https://code.visualstudio.com/) -ban való beillesztés előtt könnyebben írható. Ebben az oktatóanyagban a szkript egyes részeit mutatjuk be. Nyugodtan ugorjon a végére, ha szeretné átmásolni a Poster-be és az első lépésekhez. Kezdjük a szkript megírását.

### <a name="writing-the-pre-request-script"></a>Az előkérési parancsfájl írása

Ennek első lépéseként egy egyezményes világidő (UTC) karakterláncot hozunk létre, és hozzáadja a "date" HTTP-fejléchez. Ezt a karakterláncot egy változóban is tároljuk, hogy később is használni lehessen az aláíráskor:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Ezután a kérelem törzsét az SHA 256 használatával használjuk, és elhelyezjük a `x-ms-content-sha256` fejlécben. A Poster tartalmaz néhány [szabványos kódtárat](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) a kivonatoláshoz és az aláíráshoz globálisan, ezért nem kell telepítenie őket, és nem kell azokat megkövetelni:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

Most a korábban megadott Endpoint változót fogjuk használni a HTTP-állomásfejléc értékének megkülönböztetni. Ezt azért kell megtenni, mert a gazdagép fejléce nincs beállítva a szkript feldolgozását követően:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Ezzel az információval létrehozjuk a karakterláncot, amelyet a HTTP-kérelemhez fogunk aláírni, ez több korábban létrehozott értékből áll:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Végül ezt a karakterláncot az ACS-kulccsal kell aláírnia, majd hozzá kell adnia a kéréshez a `Authorization` fejlécben:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>A végső előkérési parancsfájl

A végső előkérési parancsfájlnak a következőhöz hasonlóan kell kinéznie:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Adja meg vagy illessze be ezt a végső parancsfájlt az előkérési parancsfájl lapon lévő szövegmezőbe:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="A Poster egy ACS-gyűjtemény előkérési parancsfájlját adta meg.":::

Ha megadta a parancsot, nyomja le a CTRL + S billentyűkombinációt, vagy nyomja le a Mentés gombot, ezzel menti a szkriptet a gyűjteménybe. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="A Poster mentés előtti parancsfájljának mentése gomb.":::

## <a name="creating-a-request-in-postman"></a>Kérelem létrehozása a Poster-ban

Most, hogy minden be van állítva, készen áll egy ACS-kérelem létrehozására a Poster-on belül. A kezdéshez kattintson az ACS-gyűjtemény melletti plusz (+) ikonra:

:::image type="content" source="media/postman/create-request.png" alt-text="A Poster plusz gombja.":::

Ez egy új fület hoz létre a postán belüli kérelemhez. A létrehozásához konfigurálni kell azt. A rendszer az SMS Send API-val kapcsolatos kérést küld, ezért segítségért tekintse meg az [API dokumentációját](/rest/api/communication/sms/send). Adja meg a Poster kérelmét.

Kezdés: beállítás, a kérelem típusa `POST` és beírása `{{endpoint}}/sms?api-version=2021-03-07` a kérelem URL-mezőjébe. Ez az URL-cím a korábban létrehozott `endpoint` változó használatával automatikusan elküldi az ACS-erőforrásnak.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Egy Poster-kérelem, amelynek típusa a POST és az URL-cím helyesen van beállítva.":::

Most válassza ki a kérelem törzs lapját, majd módosítsa a választógombot a "RAW" értékre. A jobb oldalon van egy legördülő lista, amely a "text" szöveget, a JSON-ra való váltást írja le:

:::image type="content" source="media/postman/postman-json.png" alt-text="A kérelem törzsének beállítása nyersre és JSON-ra":::

Ezzel a beállítással a rendszer JSON formátumban küldi el és fogadja az adatokat.

Az alábbi szövegmezőben meg kell adnia egy kérelem törzsét, amelynek a következő formátumúnak kell lennie:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

A "from" értéknél a korábban említettek szerint be kell [szereznie egy telefonszámot](../quickstarts/telephony-sms/get-phone-number.md) az ACS-portálon. Az országkód nélkül szóközöket és előtagot adjon meg. Példa: `+15555551234`. Az "üzenet" lehet, amit el szeretne küldeni, de `Hello from ACS` jó példa. A "to" értéknek olyan telefonnak kell lennie, amelyhez hozzáférése van, amely SMS-üzeneteket tud fogadni. A saját mobil használata jó ötlet.

A beírást követően a kérelmet a korábban létrehozott ACS-gyűjteménybe kell menteni. Ezzel biztosíthatja, hogy a rendszer felveszi a korábban létrehozott változókat és előkérési parancsfájlt. Ehhez kattintson a kérelem területének jobb felső részén található Save (Mentés) gombra.

:::image type="content" source="media/postman/postman-save.png" alt-text="A Poster-kérelem mentés gombja.":::

Ekkor megjelenik egy párbeszédpanel, amely megkérdezi, hogy mit szeretne meghívni a kérést, és hová szeretné menteni. Megadhatja, hogy mit szeretne, de győződjön meg róla, hogy kijelöli az ACS-gyűjteményt a párbeszédpanel alsó felén:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="A Poster Save Request (kérelem mentése) párbeszédpanel a kiválasztott ACS-gyűjteménysel.":::

## <a name="sending-a-request"></a>Kérelem küldése

Most, hogy minden be van állítva, képesnek kell lennie arra, hogy elküldje a kérést, és SMS-üzenetet kapjon a telefonjára. Ehhez győződjön meg arról, hogy a létrehozott kérés van kiválasztva, majd kattintson a jobb oldalon található "Küldés" gombra:

:::image type="content" source="media/postman/postman-send.png" alt-text="Egy Poster-kérelem, a Küldés gomb kiemelve.":::

Ha minden jól járt, az ACS válaszát kell látnia, amelynek 202-as állapotkódot kell lennie:

:::image type="content" source="media/postman/postman-202.png" alt-text="Egy Poster-kérelem sikeresen elküldve egy 202-as állapotkódot.":::

A "to" értékben megadott számot birtokló mobiltelefonnak SMS-üzenetet is kell kapnia. Most már van egy működő Poster-beállítása, amely képes kommunikálni az ACS szolgáltatásokkal, és SMS-üzeneteket küldhet.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Az [ACS API](/rest/api/communication/) 
>  -k megismerése [További információ a hitelesítésről](/rest/api/communication/authentication) 
>  [További információ a Poster-ról](https://learning.postman.com/)

A következőket is érdemes lehet:

- [Csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
- [A hitelesítés ismertetése](../concepts/authentication.md)