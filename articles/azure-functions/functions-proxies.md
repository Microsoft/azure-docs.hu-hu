---
title: Proxyk használata Azure Functions
description: A Azure Functions-proxyk használatának áttekintése
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96601373"
---
# <a name="work-with-azure-functions-proxies"></a>Azure Functions-proxyk használata

Ez a cikk a Azure Functions-proxyk konfigurálását és működését ismerteti. Ezzel a funkcióval olyan végpontokat adhat meg a Function alkalmazásban, amelyeket egy másik erőforrás implementál. Ezekkel a proxykkal a nagyméretű API-k több Function-alkalmazásba is kioszthatók (mint a Service-architektúrában), miközben továbbra is egyetlen API-felületet mutatnak be az ügyfelek számára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> A standard függvények számlázása a proxy végrehajtására vonatkozik. További információ: [Azure functions díjszabása](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Proxy létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre proxyt a functions portálon.

1. Nyissa meg a [Azure Portal], majd nyissa meg a Function alkalmazást.
2. A bal oldali panelen válassza az **új proxy** lehetőséget.
3. Adja meg a proxy nevét.
4. Konfigurálja a függvény alkalmazásban elérhető végpontot az **útválasztási sablon** és a **http-metódusok** megadásával. Ezek a paraméterek a [http-eseményindítók]szabályainak megfelelően viselkednek.
5. Állítsa be a **háttérbeli URL-címet** egy másik végpontra. Ez a végpont lehet függvény egy másik Function alkalmazásban, vagy bármely más API lehet. Az értéknek nem kell statikusnak lennie, és az [alkalmazás beállításait] és [paramétereit is hivatkozhat az eredeti ügyfél-kérelemből].
6. Kattintson a **Létrehozás** lehetőségre.

A proxy már létezik új végpontként a Function alkalmazásban. Az ügyfél szemszögéből a Azure Functions egy HttpTrigger egyenértékű. Az új proxy kipróbálható úgy, hogy átmásolja a proxy URL-címét, és teszteli a kedvenc HTTP-ügyfelével.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Kérelmek és válaszok módosítása

A Azure Functions-proxyk segítségével módosíthatja a háttérbeli kérelmeket és válaszokat. Ezek az átalakítások használhatnak változókat a [használati változókban]definiált módon.

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>A háttérbeli kérelem módosítása

Alapértelmezés szerint a háttér-kérelem az eredeti kérelem másolata van inicializálva. A háttérbeli URL-cím beállítása mellett módosíthatja a HTTP-metódust, a fejléceket és a lekérdezési karakterlánc paramétereit. A módosított értékek hivatkozhatnak az [alkalmazás beállításainak] és [paramétereinek az eredeti ügyfél-kérelem alapján].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *kérelmek felülbírálásának* kibontásával módosíthatók a portálon. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Válasz módosítása

Alapértelmezés szerint az ügyfél válasza a háttérbeli válasz másolata lesz inicializálva. Módosíthatja a válasz állapotkódot, az OK kifejezését, a fejléceket és a törzset. A módosított értékek hivatkozhatnak az [alkalmazás beállításaira], [az eredeti ügyfélalkalmazás paramétereit], valamint [a háttérbeli válasz paramétereit].

A háttérbeli kérelmek a proxy részleteit tartalmazó lap *Válasz felülbírálásának* kibontásával módosíthatók a portálon. 

## <a name="use-variables"></a><a name="using-variables"></a>Változók használata

A proxy konfigurációjának nem kell statikusnak lennie. Felkérheti, hogy az eredeti ügyfél-kérelemből, a háttér-válaszból vagy az alkalmazás beállításaiból származó változókat használjon.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Hivatkozás helyi függvények
`localhost`A használatával közvetlenül is hivatkozhat egy függvényre ugyanazon a Function alkalmazáson belül, egy oda-és visszaúti kérelem nélkül.

`"backendUri": "https://localhost/api/httptriggerC#1"` egy helyi HTTP által aktivált függvényre hivatkozik az útvonalon `/api/httptriggerC#1`

 
>[!Note]  
>Ha a függvény *függvény-, rendszergazdai vagy sys* -engedélyezési szinteket használ, meg kell adnia a kódot és a clientId az eredeti függvény URL-címének megfelelően. Ebben az esetben a hivatkozás a következőképpen néz ki: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` javasoljuk, hogy ezeket a kulcsokat az [Alkalmazásbeállítások] között tárolja, és hivatkozni lehessen rájuk a proxyn. Ezzel elkerülhető a forráskódban tárolt titkos kódok tárolása. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Hivatkozási kérelem paraméterei

A kérési paramétereket bemenetként használhatja a háttérbeli URL tulajdonsághoz, illetve a kérelmek és válaszok módosításának részeként. Bizonyos paraméterek az alapproxy konfigurációjában megadott útválasztási sablonnal köthetők, mások pedig a bejövő kérelem tulajdonságaiból származhatnak.

#### <a name="route-template-parameters"></a>Útválasztási sablon paraméterei
Az útválasztási sablonban használt paraméterek név szerint hivatkozhatók. A paraméterek neve kapcsos zárójelbe () van csatolva {} .

Ha például egy proxynak van egy útválasztási sablonja, például `/pets/{petId}` a, a háttér URL-címe a következő értékkel is rendelkezhet `{petId}` : `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` . Ha az útválasztási sablon egy helyettesítő karakterrel végződik, például: `/api/{*restOfPath}` , akkor az érték a `{restOfPath}` bejövő kérelemben szereplő hátralévő elérésiút-szegmensek karakterlánc-ábrázolása.

#### <a name="additional-request-parameters"></a>További kérések paraméterei
Az útválasztási sablon paraméterei mellett a következő értékek is használhatók a konfigurációs értékekben:

* **{Request. Method}**: az eredeti kérelemben használt http-metódus.
* **{Request. headers. \<HeaderName\> }**: az eredeti kérelemből olvasható fejléc. Cserélje le az helyére az *\<HeaderName\>* olvasni kívánt fejléc nevét. Ha a kérelem nem tartalmazza a fejlécet, az érték az üres karakterlánc lesz.
* **{Request. querystring. \<ParameterName\> }**: lekérdezési karakterlánc paraméter, amely az eredeti kérelemből olvasható. A helyére írja be az *\<ParameterName\>* olvasni kívánt paraméter nevét. Ha a paraméter nem szerepel a kérelemben, az érték az üres karakterlánc lesz.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Hivatkozás háttér-válasz paraméterei

A válasz paramétereit az ügyfélre adott válasz módosításának részeként lehet használni. A konfigurációs értékek a következő értékeket használhatják:

* **{backend. Response. statusCode}**: a háttér-válaszon VISSZAadott http-állapotkód.
* **{backend. Response. statusReason}**: a háttér-válaszon VISSZAadott http-ok kifejezése.
* **{backend. Response. headers. \<HeaderName\> }**: a háttér-válaszból olvasható fejléc. Cserélje le az helyére az *\<HeaderName\>* olvasni kívánt fejléc nevét. Ha a válasz nem tartalmazza a fejlécet, az érték az üres karakterlánc lesz.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Hivatkozási alkalmazás beállításai

A Function alkalmazáshoz [definiált Alkalmazásbeállítások](./functions-how-to-use-azure-function-app-settings.md) is hivatkozhatók, ha a beállítási nevet a százalék jelek (%) értékre helyezi.

Például a (z) "% ORDER_PROCESSING_HOST%" nevű háttérbeli URL-cím a *https://%ORDER_PROCESSING_HOST%/api/orders* ORDER_PROCESSING_HOST beállítás értékével lett lecserélve.

> [!TIP] 
> Ha több központi telepítéssel vagy tesztelési környezettel rendelkezik, a háttérbeli gazdagépekre vonatkozó Alkalmazásbeállítások használata. Így biztos lehet benne, hogy mindig az adott környezet hátterére van szüksége.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Proxyk hibáinak megoldása

Ha hozzáadja a jelölőt `"debug":true` bármely proxyhoz az `proxies.json` Ön számára, akkor engedélyezi a hibakeresési naplózást. A naplókat a `D:\home\LogFiles\Application\Proxies\DetailedTrace` speciális eszközökön (kudu) tárolják és érhetik el. Bármely HTTP-válasz tartalmazni fog egy `Proxy-Trace-Location` fejlécet is, amely URL-címmel rendelkezik a naplófájl eléréséhez.

A proxykat az ügyfél oldaláról a következőhöz tartozó fejléc hozzáadásával lehet hibakeresésre `Proxy-Trace-Enabled` beállítani: `true` . Ez egy nyomkövetést is naplóz a fájlrendszerben, és a válaszban fejlécként visszaküldi a nyomkövetési URL-címet.

### <a name="block-proxy-traces"></a>Proxy nyomkövetésének letiltása

Biztonsági okokból előfordulhat, hogy nem kívánja engedélyezni, hogy a szolgáltatás hívása nyomot állítson elő. A bejelentkezési hitelesítő adataik nélkül nem férhetnek hozzá a nyomkövetési tartalmakhoz, de a nyomkövetés generálja az erőforrásokat, és elérhetővé teszi a funkció-proxyk használatát.

A nyomkövetést teljes egészében letilthatja az `"debug":false` adott proxyhoz való hozzáadásával `proxies.json` .

## <a name="advanced-configuration"></a>Speciális konfiguráció

A konfigurált proxyk a fájlban lévő *proxies.js* tárolódnak, amely a Function app-címtár gyökerében található. Ezt a fájlt manuálisan szerkesztheti, és az alkalmazás részeként telepítheti, ha a függvények által támogatott [központi telepítési módszereket](./functions-continuous-deployment.md) használja. 

> [!TIP] 
> Ha nem állította be az egyik telepítési módszert, akkor a portálon található fájl *proxies.js* is dolgozhat. Nyissa meg a Function alkalmazást, válassza a **platform szolgáltatások** elemet, majd válassza a **app Service Editor** lehetőséget. Ezzel megtekintheti a Function alkalmazás teljes fájljának szerkezetét, majd módosításokat végezhet.

A (z) *Proxies.jst* egy proxys objektum határozza meg, amely névvel ellátott proxyból és azok definíciói áll. Ha a szerkesztő támogatja azt, akkor egy [JSON-sémára](http://json.schemastore.org/proxies) hivatkozhat a kód befejezéséhez. Egy példaként szolgáló fájl a következőhöz hasonló lehet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Minden proxynak van egy felhasználóbarát neve, például a *proxy1* az előző példában. A megfelelő proxy-definíciós objektumot a következő tulajdonságok határozzák meg:

* **matchCondition**: kötelező – a proxy végrehajtását kiváltó kérelmeket meghatározó objektum. Két olyan tulajdonságot tartalmaz, amelyek a [http-eseményindítókkal]vannak megosztva:
    * _metódusok_: a proxy által válaszoló http-metódusok tömbje. Ha nincs megadva, a proxy az útvonalon lévő összes HTTP-metódusra válaszol.
    * _Route_: kötelező – meghatározza az útválasztási sablont, amely szabályozza, hogy a proxy mely URL-címekre válaszol. A http-eseményindítók eltérően nincs alapértelmezett érték.
* **backendUri**: annak a háttér-erőforrásnak az URL-címe, amelyre a kérést proxynak kell lennie. Ez az érték hivatkozhat az alkalmazás beállításaira és a paraméterekre az eredeti ügyfél-kérelem alapján. Ha ez a tulajdonság nem szerepel, Azure Functions válaszol a HTTP 200 OK-val.
* **requestOverrides**: olyan objektum, amely meghatározza a háttér-kérelem átalakításait. Lásd: [requestOverrides objektum definiálása].
* **responseOverrides**: olyan objektum, amely meghatározza az ügyfél válaszának átalakításait. Lásd: [responseOverrides objektum definiálása].

> [!NOTE] 
> A Azure Functions-proxyk *Route* tulajdonsága nem tartja tiszteletben a függvényalkalmazás gazdagép konfigurációjának *routePrefix* tulajdonságát. Ha olyan előtagot szeretne szerepeltetni, mint például `/api` a, szerepelnie kell az *Route* tulajdonságban.

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Egyéni proxyk letiltása

Az egyes proxykat letilthatja a `"disabled": true` fájlban lévő proxyhoz való hozzáadásával `proxies.json` . Ennek hatására a matchCondition a 404-as visszaküldésére irányuló kérések lesznek.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> Alkalmazásbeállítások

A proxy viselkedését több Alkalmazásbeállítások is szabályozhatja. Ezek mind a [functions app Settings dokumentációjában](./functions-app-settings.md) szerepelnek.

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Fenntartott karakterek (karakterlánc formázása)

A proxyk a \ Escape szimbólum használatával beolvassák az összes karakterláncot a JSON-fájlból. A proxyk a kapcsos zárójeleket is értelmezik. Tekintse meg az alábbi példák teljes listáját.

|Karakter|Escape-karakter|Példa|
|-|-|-|
|vagy|{{vagy}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>RequestOverrides objektum definiálása

A requestOverrides objektum a kérelemben a háttér-erőforrás hívásakor végrehajtott módosításokat határozza meg. Az objektumot a következő tulajdonságok határozzák meg:

* **háttér. Request. Method**: a háttér hívásához használt http-metódus.
* **backend. Request. querystring \<ParameterName\> .**: lekérdezési karakterlánc paraméter, amely beállítható a háttérbeli híváshoz. Cserélje le a helyére a *\<ParameterName\>* beállítani kívánt paraméter nevét. Vegye figyelembe, hogy ha üres karakterláncot ad meg, a paraméter továbbra is szerepel a háttér-kérelemben.
* **backend. Request. headers. \<HeaderName\>**: a háttér felé irányuló híváshoz beállítható fejléc. Cserélje le a helyére a *\<HeaderName\>* beállítani kívánt fejléc nevét. Vegye figyelembe, hogy ha üres karakterláncot ad meg, a paraméter továbbra is szerepel a háttér-kérelemben.

Az értékek hivatkozhatnak az alkalmazás beállításainak és paramétereinek az eredeti ügyfél-kérelem alapján.

A konfiguráció például a következőhöz hasonló lehet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>ResponseOverrides objektum definiálása

A requestOverrides objektum az ügyfélnek visszaadott válaszon végrehajtott módosításokat határozza meg. Az objektumot a következő tulajdonságok határozzák meg:

* **Response. statusCode**: az ügyfélnek visszaadott http-állapotkód.
* **Response. statusReason**: az ügyfélnek visszaadott http-ok kifejezése.
* **Response. Body**: az ügyfélnek visszaadott törzs karakterlánc-ábrázolása.
* **Response. headers \<HeaderName\> .**: az ügyfélre adott válaszra beállítható fejléc. Cserélje le a helyére a *\<HeaderName\>* beállítani kívánt fejléc nevét. Ha üres karakterláncot ad meg, a válasz nem tartalmazza a fejlécet.

Az értékek az alkalmazás beállításait, az eredeti ügyfélalkalmazás paramétereit, valamint a háttérbeli válasz paramétereit is hivatkozhatják.

A konfiguráció például a következőhöz hasonló lehet:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Ebben a példában a válasz törzse közvetlenül van beállítva, így nincs `backendUri` szükség tulajdonságra. A példa bemutatja, hogyan használhatja a Azure Functions-proxykt az API-k modellezéséhez.

[Azure Portal]: https://portal.azure.com
[HTTP-eseményindítók]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[RequestOverrides objektum definiálása]: #requestOverrides
[ResponseOverrides objektum definiálása]: #responseOverrides
[Alkalmazásbeállítások]: #use-appsettings
[Változók használata]: #using-variables
[az eredeti ügyfél kérelmének paraméterei]: #request-parameters
[paraméterek a háttér-válaszból]: #response-parameters
