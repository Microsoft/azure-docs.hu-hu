---
title: Translator V 3.0 – dokumentáció
titleSuffix: Azure Cognitive Services
description: A Translator V 3.0 dokumentációja. A Translator 3. verziója modern JSON-alapú webes API-t biztosít.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 567e28ee7f698565d6ad0020db7abdca0557f053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100650762"
---
# <a name="translator-v30"></a>Translator v 3.0

## <a name="whats-new"></a>Újdonságok

A Translator 3. verziója modern JSON-alapú webes API-t biztosít. Javítja a használhatóságot és a teljesítményt azáltal, hogy a meglévő funkciókat kevesebb műveletre összevonja, és új funkciókat biztosít.

 * A nyelv átalakítása egy parancsfájlból egy másik parancsfájlba.
 * Fordítás több nyelvre egy kérelemben.
 * Nyelvfelismerés, fordítás és írás egyetlen kérelemben.
 * Szótár egy kifejezés alternatív fordításának kereséséhez, hogy megkeresse a háttér-fordításokat és példákat a kontextusban használt kifejezések megjelenítéséhez.
 * További informatív nyelvfelismerés eredményei.

## <a name="base-urls"></a>Alap URL-címek

A Microsoft Translator több adatközpont-helyből is kiszolgálható. Jelenleg 10 [Azure](https://azure.microsoft.com/global-infrastructure/regions)-régióban találhatók:

* **Amerika:** USA keleti régiója, USA déli középső régiója, az USA nyugati középső régiója és az USA 2. nyugati régiója 
* **Ázsia és a csendes-óceáni térség:** Dél-Korea, Kelet-Japán, Délkelet-Ázsia és Kelet-Ausztrália
* **Európa:** Észak-Európa, Nyugat-Európa, Észak-Svájc <sup>1, 2</sup>és Nyugat-Svájc <sup>1, 2</sup>

A Microsoft Translatorre irányuló kéréseket a legtöbb esetben az adatközpont kezeli, amely a kérelem eredeti helyétől legközelebb esik. Adatközpont meghibásodása esetén a kérést az Azure földrajzán kívül is át lehet irányítani.

Ha szeretné kényszeríteni a kérést, hogy az adott Azure földrajza kezelhető legyen, módosítsa az API-kérés globális végpontját a kívánt földrajzi végpontra:

|Leírás|Az Azure földrajza|Alap URL-cím (földrajzi végpont)|
|:--|:--|:--|
|Azure|Globális (nem regionális)|   api.cognitive.microsofttranslator.com|
|Azure|Egyesült Államok|   api-nam.cognitive.microsofttranslator.com|
|Azure|Európa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Ázsia és a Csendes-óceáni térség|    api-apc.cognitive.microsofttranslator.com|

<sup>1</sup> Észak-Svájc vagy Nyugat-Svájc található erőforrással rendelkező ügyfél gondoskodhat arról, hogy a szöveges API-kérések Svájcban legyenek kézbesítve. Annak érdekében, hogy a kérelmeket Svájcban kezeljék, hozza létre a fordítói erőforrást az "erőforrás-régió" Észak-Svájc vagy a "Nyugat-Svájc" területen, majd használja az erőforrás egyéni végpontját az API-kérelmekben. Ha például létrehoz egy Translator-erőforrást Azure Portal az "erőforrás-régióval", mint "Észak-Svájc", és az erőforrás neve "My-CH-n", akkor az egyéni végpont " https://my-ch-n.cognitiveservices.azure.com ". A fordítási kérelem a következő:
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> Az egyéni Translator jelenleg nem érhető el Svájcban.

## <a name="authentication"></a>Hitelesítés

Fizessen elő Translator vagy [Cognitive Services Multi-Service szolgáltatásra](https://azure.microsoft.com/pricing/details/cognitive-services/) az Azure-ban Cognitive Services, és használja az előfizetési kulcsát (a Azure Portalben elérhető) a hitelesítéshez. 

Az előfizetés hitelesítéséhez három fejléc használható. Ez a táblázat a használatuk módját ismerteti:

|Fejlécek|Leírás|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Ha a titkos kulcsot átadja, használja Cognitive Services-előfizetéssel*.<br/>Az érték a Translator-előfizetés Azure-beli titkos kulcsa.|
|Engedélyezés|*Ha hitelesítési tokent továbbít, használja Cognitive Services-előfizetést.*<br/>Az érték a tulajdonosi jogkivonat: `Bearer <token>` .|
|OCP-APIM-előfizetés-régió|*Cognitive Services Multi-Service és regionális Translator erőforrással használható.*<br/>Az érték a Multi-Service vagy a regionális Translator erőforrás régiója. Ez az érték nem kötelező, ha globális Translator-erőforrást használ.|

###  <a name="secret-key"></a>Titkos kulcs
Az első lehetőség a fejléc használatával történő hitelesítés `Ocp-Apim-Subscription-Key` . Adja hozzá a `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` fejlécet a kérelemhez.

#### <a name="authenticating-with-a-global-resource"></a>Hitelesítés globális erőforrással

Ha [globális Translator-erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)használ, egy fejlécet kell tartalmaznia a fordító meghívásához.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték a Translator-előfizetés Azure-beli titkos kulcsa.|

Íme egy példa arra, hogy meghívjuk a fordítót a Global Translator erőforrás használatával

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Hitelesítés regionális erőforrással

Ha [regionális Translator-erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)használ.
2 fejlécre van szükség a fordító meghívásához.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték a Translator-előfizetés Azure-beli titkos kulcsa.|
|OCP-APIM-előfizetés-régió| Az érték a fordítói erőforrás régiója. |

Íme egy példa a fordító meghívására a regionális Translator erőforrás használatával

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Hitelesítés több szolgáltatással rendelkező erőforrásokkal

Ha a kognitív szolgáltatás több szolgáltatást használó erőforrását használja. Ez lehetővé teszi, hogy egyetlen titkos kulcsot használjon a kérelmek több szolgáltatáshoz való hitelesítéséhez. 

Több szolgáltatásból álló titkos kulcs használata esetén két hitelesítési fejlécet kell tartalmaznia a kérelemmel. 2 fejlécre van szükség a fordító meghívásához.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték a több szolgáltatásra épülő erőforráshoz tartozó Azure titkos kulcs.|
|OCP-APIM-előfizetés-régió| Az érték a több szolgáltatásból álló erőforrás régiója. |

A Multi-Service Text API-előfizetéshez régió szükséges. A kiválasztott régió az egyetlen olyan régió, amelyet a többszolgáltatásos előfizetési kulcs használatakor használhat a szöveges fordításhoz, és a Azure Portalon keresztül a többszolgáltatásos előfizetésre való feliratkozáskor választott régiónak kell lennie.

Az elérhető régiók:,,,,,,,,,,,,,,,,,,,,, `australiaeast` `brazilsouth` `canadacentral` `centralindia` `centralus` `centraluseuap` `eastasia` `eastus` `eastus2` `francecentral` `japaneast` `japanwest` `koreacentral` `northcentralus` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` és `southafricanorth` .

Ha a lekérdezési sztringben megadja a titkos kulcsot a paraméterrel `Subscription-Key` , akkor a régiót a lekérdezési paraméterrel kell megadnia `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Hitelesítés hozzáférési jogkivonattal
Azt is megteheti, hogy kicseréli a titkos kulcsot egy hozzáférési jogkivonatra. Ezt a tokent az egyes kérések `Authorization` fejlécként tartalmazzák. Az engedélyezési jogkivonat beszerzéséhez tegyen `POST` fel egy kérelmet a következő URL-címre:

| Erőforrás típusa     | Hitelesítési szolgáltatás URL-címe                                |
|-----------------|-----------------------------------------------------------|
| Globális          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionális vagy több szolgáltatás | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Az alábbi példa egy titkos kulcsot tartalmazó jogkivonat beszerzésére vonatkozó kérelmeket mutat be:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Egy sikeres kérelem visszaadja a kódolt hozzáférési tokent egyszerű szövegként a válasz törzsében. Az érvényes jogkivonatot tulajdonosi jogkivonatként adja át a fordító szolgáltatásnak az engedélyezésben.

```http
Authorization: Bearer <Base64-access_token>
```

A hitelesítési jogkivonat 10 percig érvényes. A tokent újra fel kell használni, amikor több hívást végez a fordítónak. Ha azonban a program hosszabb időn keresztül kezdeményezi a kéréseket a fordítónak, akkor a programnak rendszeres időközönként új hozzáférési jogkivonatot kell igényelnie (például 8 percenként).

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása

A Translator Service mostantól Virtual Network (VNET) képességekkel érhető el az Azure nyilvános felhő összes régiójában. Virtual Network engedélyezéséhez tekintse meg az [Azure Cognitive Services Virtual Networks konfigurálása](../../cognitive-services-virtual-networks.md?tabs=portal)című témakört. 

Ha bekapcsolta ezt a funkciót, az egyéni végpontot kell használnia a fordító meghívásához. A globális Translator Endpoint ("api.cognitive.microsofttranslator.com") nem használható, és nem végezhető el a hitelesítés egy hozzáférési jogkivonattal.

Az egyéni végpontot a [fordítói erőforrások](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) létrehozása után, valamint a kiválasztott hálózatokból és privát végpontokból való hozzáférés engedélyezése után találja meg.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték a Translator-előfizetés Azure-beli titkos kulcsa.|
|OCP-APIM-előfizetés-régió| Az érték a fordítói erőforrás régiója. Ez az érték nem kötelező, ha az erőforrás `global`|

Íme egy példa arra a kérésre, hogy a fordítót az egyéni végpont használatával hívja meg.

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Hibák

A standard hibaérték a name/Value pár nevű JSON-objektum `error` . Az érték egy JSON-objektum is, amely tulajdonságokkal rendelkezik:

  * `code`: Kiszolgáló által definiált hibakód.
  * `message`: Egy karakterlánc, amely a hiba ember által olvasható ábrázolását adja meg.

Az ingyenes próbaverziós előfizetéssel rendelkező ügyfelek például a következő hibaüzenetet kapják meg az ingyenes kvóta kimerülése után:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok:

| Code | Leírás |
|:----|:-----|
| 400000| Az egyik kérelem bemenete érvénytelen.|
| 400001| A "scope" paraméter érvénytelen.|
| 400002| A "Category" paraméter érvénytelen.|
| 400003| A nyelv megadása hiányzik vagy érvénytelen.|
| 400004| A célként megadott parancsfájl-megadó ("to script") hiányzik vagy érvénytelen.|
| 400005| Egy bemeneti szöveg hiányzik vagy érvénytelen.|
| 400006| A nyelv és a szkript kombinációja érvénytelen.|
| 400018| A forrás parancsfájl-megadása ("from script") hiányzik vagy érvénytelen.|
| 400019| A megadott nyelvek egyike nem támogatott.|
| 400020| A bemeneti szöveg tömb egyik eleme nem érvényes.|
| 400021| Az API-verzió paramétere hiányzik vagy érvénytelen.|
| 400023| A megadott nyelvi párok egyike érvénytelen.|
| 400035| A forrás nyelve ("from" mező) érvénytelen.|
| 400036| A célként megadott nyelv ("to" mező) hiányzik vagy érvénytelen.|
| 400042| A megadott beállítások egyike ("beállítások" mező) érvénytelen.|
| 400043| Az ügyfél nyomkövetési azonosítója (ClientTraceId mező vagy X-ClientTranceId fejléc) hiányzik vagy érvénytelen.|
| 400050| A bemeneti szöveg túl hosszú. [Kérelmek korlátozásának](../request-limits.md)megtekintése.|
| 400064| A "Translation" paraméter hiányzik vagy érvénytelen.|
| 400070| A cél-parancsfájlok (ToScript paraméter) száma nem egyezik meg a célként megadott nyelvek számával (a paraméterrel).|
| 400071| Az érték nem érvényes a TextType.|
| 400072| A bemeneti szöveg tömbje túl sok elemet tartalmaz.|
| 400073| A parancsfájl paramétere érvénytelen.|
| 400074| A kérelem törzse nem érvényes JSON.|
| 400075| A nyelvi pár és a kategória kombináció érvénytelen.|
| 400077| Túllépte a kérések maximális méretét. [Kérelmek korlátozásának](../request-limits.md)megtekintése.|
| 400079| A és a nyelv közötti fordításra kért egyéni rendszer nem létezik.|
| 400080| A nyelv vagy a parancsfájl nem támogatja az írást.|
| 401000| A kérés nincs engedélyezve, mert a hitelesítő adatok hiányoznak vagy érvénytelenek.|
| 401015| "A megadott hitelesítő adatok a Speech API-hoz tartoznak. Ehhez a kérelemhez a Text API hitelesítő adatai szükségesek. Használjon fordítói előfizetést. "|
| 403000| A művelet nem engedélyezett.|
| 403001| A művelet nem engedélyezett, mert az előfizetés túllépte az ingyenes kvótát.|
| 405000| A kért erőforrás nem támogatja a kérelem metódusát.|
| 408001| A kért fordítási rendszer előkészítése folyamatban van. Próbálkozzon újra néhány perc múlva.|
| 408002| A kérelem időtúllépést várt a bejövő adatfolyamra való várakozás közben. Az ügyfél nem hozott létre kérelmet a kiszolgáló várakozási ideje alatt. Az ügyfél bármikor megismételheti a kérést anélkül, hogy később módosításokat kellene megismételnie.|
| 415000| A Content-Type fejléc hiányzik vagy érvénytelen.|
| 429000, 429001, 429002| A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.|
| 500000| Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a hibát dátum/idő szerint, a válasz fejléce X-kérelemazonosító, valamint az ügyfél-azonosítót az X-ClientTraceId kérelem fejlécében.|
| 503000| A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra. Ha a hiba továbbra is fennáll, jelentse a hibát dátum/idő szerint, a válasz fejléce X-kérelemazonosító, valamint az ügyfél-azonosítót az X-ClientTraceId kérelem fejlécében.|

## <a name="metrics"></a>Mérőszámok 
A metrikák segítségével megtekintheti a Translator használati és rendelkezésre állási adatait Azure Portalban, a metrikák szakaszban, ahogy az alábbi képernyőképen is látható. További információ: [adatok és platform metrikák](../../../azure-monitor/essentials/data-platform-metrics.md).

![Fordítói metrikák](../media/translatormetrics.png)

Ez a táblázat a fordítási API-hívások figyelésére szolgáló leírással sorolja fel az elérhető metrikákat.

| Mérőszámok | Leírás |
|:----|:-----|
| TotalCalls| Az API-hívások teljes száma.|
| TotalTokenCalls| A jogkivonat-szolgáltatáson keresztüli API-hívások teljes száma a hitelesítési jogkivonat használatával.|
| SuccessfulCalls| A sikeres hívások száma.|
| TotalErrors| A hiba-választal rendelkező hívások száma.|
| BlockedCalls| Azon hívások száma, amelyek túllépték a sebességet vagy a kvóta korlátját.|
| Kiszolgálóhibái| A kiszolgálói belső hibával (5XX) rendelkező hívások száma.|
| ClientErrors| Az ügyféloldali hibával (4XX) rendelkező hívások száma.|
| Késés| A kérelem elvégzésének időtartama ezredmásodpercben.|
| CharactersTranslated| A bejövő szöveges kérelemben szereplő karakterek teljes száma.|
