---
title: Azure API Management átalakítási szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható átalakítási szabályzatokat.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 4f57f7cbc4e93f8a98b64b31ca51e0f1e32c375c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073297"
---
# <a name="api-management-transformation-policies"></a>API Management átalakítási házirendek
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a>Átalakítási házirendek

-   [A JSON konvertálása XML](api-management-transformation-policies.md#ConvertJSONtoXML) formátumba – átalakítja a kérelem vagy a válasz törzsét a JSON-ből az XML-be.

-   [XML konvertálása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) formátumba – átalakítja a kérelem vagy válasz törzsét az XML-ből a JSON-ra.

-   [Karakterlánc keresése és cseréje](api-management-transformation-policies.md#Findandreplacestringinbody) a törzsben – megkeresi a kérelem vagy válasz alkarakterláncot, és egy másik alkarakterlánccal helyettesíti azt.

-   Az [URL-címek maszkolása](api-management-transformation-policies.md#MaskURLSContent) a válasz törzsében lévő tartalom-újraírások (maszkok) alapján, hogy az átjárón keresztül az egyenértékű hivatkozásra mutassanak.

-   [Háttérbeli szolgáltatás beállítása](api-management-transformation-policies.md#SetBackendService) – a háttérrendszer módosítása egy bejövő kérelemnél.

-   [Törzs beállítása](api-management-transformation-policies.md#SetBody) – az üzenettörzs beállítása a bejövő és kimenő kérésekhez.

-   [Http-fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader) – egy érték kiosztása egy meglévő válaszhoz és/vagy kérelem fejhez, vagy új válasz és/vagy kérelem fejlécének megadása.

-   [Lekérdezési karakterlánc paraméterének beállítása](api-management-transformation-policies.md#SetQueryStringParameter) – lekérdezési karakterlánc paraméterének megadása, cseréje vagy törlése.

-   [URL-cím](api-management-transformation-policies.md#RewriteURL) újraírása – a kérés URL-címét a nyilvános űrlapról a webszolgáltatás által várt űrlapra konvertálja.

-   [XML átalakítása XSLT használatával](api-management-transformation-policies.md#XSLTransform) – XSL-transzformációt alkalmaz a kérelem vagy válasz törzsében lévő XML formátumra.

##  <a name="ConvertJSONtoXML"></a>JSON konvertálása XML-fájllá
 A `json-to-xml` szabályzat egy kérelem vagy válasz törzsét átalakítja a JSON-ből az XML-be.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|json-to-xml|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|alkalmaz|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -mindig – mindig alkalmazza a konverziót.<br />-Content-Type-JSON – csak akkor konvertálható, ha a válasz Content-Type fejléc a JSON jelenlétét jelzi.|Igen|–|
|consider-accept-header|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -True – konverzió alkalmazása, ha a kérelem elfogadása fejlécben a JSON-t kéri a rendszer.<br />-FALSE – mindig alkalmazza a konverziót.|Nem|true|
|elemzés – dátum|Ha a Date `false` értékre van állítva, egyszerűen másolja a rendszer az átalakítás során|Nem|true|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="ConvertXMLtoJSON"></a>XML konvertálása JSON formátumba
 A `xml-to-json` házirend XML-ből JSON-ra alakítja át a kérelem vagy a válasz törzsét. Ez a szabályzat az API-k csak XML háttérbeli webszolgáltatások alapján történő modernizálására használható.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|xml-to-json|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|típusú|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -JavaScript-barát – az átalakított JSON a JavaScript-fejlesztőknek készült, felhasználóbarát formában.<br />-Direct – a konvertált JSON az eredeti XML-dokumentum struktúráját tükrözi.|Igen|–|
|alkalmaz|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -mindig-Convert mindig.<br />-Content-Type-XML-Convert csak akkor, ha a válasz Content-Type fejléc jelzi az XML jelenlétét.|Igen|–|
|consider-accept-header|Az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> -True – átalakítás alkalmazása, ha a kérelem elfogadása fejlécben az XML-t kéri a rendszer.<br />-FALSE – mindig alkalmazza a konverziót.|Nem|true|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="Findandreplacestringinbody"></a>Karakterlánc keresése és cseréje a törzsben
 A `find-and-replace` házirend egy kérelem vagy válasz alkarakterláncot talál, és egy másik alkarakterlánccal helyettesíti.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Példa

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|Keresés és csere|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|from|A keresendő karakterlánc.|Igen|–|
|to|A helyettesítő sztring. A keresési karakterlánc eltávolításához nulla hosszúságú helyettesítő karakterláncot kell megadni.|Igen|–|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="MaskURLSContent"></a>URL-címek maszkolása a tartalomban
 `redirect-content-urls` A rewrites (maszkok) hivatkozások a válasz törzsében, hogy a megfelelő hivatkozásra mutassanak az átjárón keresztül. A kimenő szakaszban a válasz törzsére mutató hivatkozások újraírására használhatja, hogy azok az átjáróra mutassanak. Ellentétes hatásként használja a bejövő szakaszt.

> [!NOTE]
>  Ez a szabályzat nem változtatja meg a fejlécek `Location` értékeit, például a fejléceket. A fejléc értékeinek módosításához használja a [set-header](api-management-transformation-policies.md#SetHTTPheader) házirendet.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<redirect-content-urls />
```

### <a name="example"></a>Példa

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|redirect-content-urls|Gyökérelem.|Igen|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="SetBackendService"></a>Háttérbeli szolgáltatás beállítása
 A `set-backend-service` házirend segítségével átirányíthatja a bejövő kérelmeket egy másik háttérre, mint az adott művelet API-beállításaiban megadott beállításokkal. Ez a házirend a bejövő kérelem háttérbeli szolgáltatásának alap URL-címét módosítja a házirendben megadott értékre.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-backend-service base-url="base URL of the backend service" />
```

or

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> A háttérbeli entitásokat a felügyeleti [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) és a [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement)segítségével kezelheti.

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Ebben a példában a háttér-szolgáltatási házirend beállítása a kérelmeket a lekérdezési karakterláncban átadott verzió értéke alapján egy másik háttér-szolgáltatásra irányítja, mint az API-ban megadott érték.

Kezdetben a háttérbeli szolgáltatás alap URL-címe az API-beállításokból származik. Így a kérelem URL `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` - `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` címe `http://contoso.com/api/10.4/` az API-beállításokban megadott háttér-szolgáltatás URL-címe lesz.

Ha a [< válassza\> ](api-management-advanced-policies.md#choose) a házirend-utasítás alkalmazása beállítást, a háttérrendszer URL-címe a `http://contoso.com/api/8.2` Version `http://contoso.com/api/9.1`kérelem lekérdezési paraméterének értékétől függően újra megváltozhat. Ha például az érték `"2013-15"` a végső kérelem URL-címe lesz. `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`

Ha a kérés további átalakítására van szükség, más [átalakítási házirendek](api-management-transformation-policies.md#TransformationPolicies) is használhatók. Ha például most el szeretné távolítani a lekérdezési paramétert, hogy a kérés egy adott verziójú háttérbe legyen irányítva, a [lekérdezési karakterlánc beállítása paraméter](api-management-transformation-policies.md#SetQueryStringParameter) -házirend használatával eltávolíthatja a most redundáns Version attribútumot.

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
Ebben a példában a házirend a kérést egy Service Fabric-háttérre irányítja, a userId lekérdezési karakterláncot használva partíciós kulcsként, a partíció elsődleges replikájának használatával.

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|set-háttér-szolgáltatás|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|alap URL-cím|Új háttér-szolgáltatás alap URL-címe.|A vagy `base-url` `backend-id` az egyikének jelen kell lennie.|–|
|háttér-azonosító|A háttérbeli útvonal azonosítója. (A háttérbeli entitásokat az [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) és a [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement)használatával felügyeli.)|A vagy `base-url` `backend-id` az egyikének jelen kell lennie.|–|
|SF-Partition-Key|Csak akkor alkalmazható, ha a háttérrendszer Service Fabric szolgáltatás, és a "háttér-azonosító" használatával van megadva. Egy adott partíció feloldására szolgál a névfeloldási szolgáltatásból.|Nem|–|
|sf-replica-type|Csak akkor alkalmazható, ha a háttérrendszer Service Fabric szolgáltatás, és a "háttér-azonosító" használatával van megadva. Azt szabályozza, hogy a kérésnek a partíció elsődleges vagy másodlagos replikájának kell-e lennie. |Nem|–|
|sf-resolve-condition|Csak akkor alkalmazható, ha a háttérrendszer Service Fabric szolgáltatás. Annak a feltételnek a meghatározása, hogy Service Fabric háttérbeli hívást meg kell-e ismételni új feloldással.|Nem|–|
|sf-service-instance-name|Csak akkor alkalmazható, ha a háttérrendszer Service Fabric szolgáltatás. Lehetővé teszi a szolgáltatási példányok módosítását futásidőben. |Nem|–|
|sf-listener-name|Csak akkor alkalmazható, ha a háttérrendszer Service Fabric szolgáltatás, és a "háttér-azonosító" használatával van megadva. Service Fabric Reliable Services lehetővé teszi, hogy több figyelőt hozzon létre egy szolgáltatásban. Ez az attribútum egy adott figyelő kiválasztására szolgál, ha a háttérbeli megbízható szolgáltatás több figyelővel rendelkezik. Ha nincs megadva ez az attribútum, a API Management neve nélkül kísérli meg a figyelő használatát. A név nélküli figyelő olyan Reliable Servicesra jellemző, amely csak egy figyelővel rendelkezik. |Nem|–|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, háttérrendszer

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="SetBody"></a>Törzs beállítása
 `set-body` A házirend segítségével állítsa be az üzenettörzs a bejövő és a kimenő kérelmeket. Az üzenet törzsének eléréséhez használhatja a `context.Request.Body` tulajdonságot vagy a `context.Response.Body`(z) függvényt attól függően, hogy a házirend a bejövő vagy kimenő szakaszban van-e.

> [!IMPORTANT]
>  Vegye figyelembe, hogy alapértelmezés szerint az üzenettörzs a vagy `context.Request.Body` `context.Response.Body`a használatával való elérésekor az eredeti üzenet törzse elvész, és úgy kell beállítani, hogy visszaadja a törzs vissza a kifejezésben. A törzs tartalmának megőrzéséhez állítsa a `preserveContent` `true` paramétert az üzenet elérésekor. Ha `preserveContent` a értéke értékre van `true` állítva, és egy másik törzset ad vissza a kifejezés, a rendszer a visszaadott törzset használja.
>
>  A `set-body` szabályzat használatakor vegye figyelembe az alábbi szempontokat.
>
> - Ha a `set-body` szabályzatot egy új vagy frissített törzs visszaadására használja, nem kell beállítania `preserveContent` , `true` mert Ön explicit módon megadja az új szövegtörzs tartalmát.
>   -   A bejövő folyamatban lévő válasz tartalmának megőrzése nem ésszerű, mert még nincs válasz.
>   -   A kimenő folyamatban lévő kérelem tartalmának megőrzése nem ésszerű, mert a kérést már elküldték a háttérnek ezen a ponton.
>   -   Ha ezt a házirendet akkor kell használni, ha nincs üzenettörzs, például egy bejövő GET esetében, kivétel keletkezik.

 További információ: `context.Request.Body`, `context.Response.Body`és a `IMessage` [környezeti változó](api-management-policy-expressions.md#ContextVariables) táblázatának részei.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Példák

#### <a name="literal-text-example"></a>Példa literál szövegre

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Példa a törzs karakterláncként való elérésére. Vegye figyelembe, hogy az eredeti kérelem törzsét megőrizjük, hogy később is hozzáférhessenek a folyamathoz.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Példa a törzs JObject való elérésére. Vegye figyelembe, hogy mivel az eredeti kérelem törzse nem található meg, a folyamat későbbi részében való hozzáférés kivételt eredményez.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Válasz szűrése termék alapján
 Ez a példa azt mutatja be, hogyan hajtható végre a tartalom szűrése, ha eltávolít egy adatelemet a háttér `Starter` -szolgáltatástól kapott válaszból a termék használatakor. A szabályzat konfigurálásával és használatával kapcsolatos bemutatóért lásd [: a Cloud Cover 177-es epizódja: További API Management a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 34:30-es gyors előretekerési funkciók. Indítsa el a 31:50-at a bemutatóhoz használt [Dark Sky előrejelzési API](https://developer.forecast.io/) áttekintéséhez.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>Likvid sablonok használata a set Body használatával
A `set-body` házirend konfigurálható úgy, hogy a [folyékony](https://shopify.github.io/liquid/basics/introduction/) sablonos nyelvet használja a kérelem vagy válasz törzsének átalakításához. Ez nagyon hatékony lehet, ha teljes mértékben át kell alakítania az üzenet formátumát.

> [!IMPORTANT]
> A `set-body` házirendben használt folyadék implementációja "C# Mode" módban van konfigurálva. Ez különösen fontos olyan dolgok esetében, mint a szűrés. A Dátumszűrő használatával például a Pascal-ház és C# a dátum formázása szükséges, például:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Ahhoz, hogy a folyékony sablon használatával megfelelően lehessen kötni egy XML-törzset, `set-header` használjon egy házirendet a tartalom típusának beállításához az Application/XML, a Text/XML (vagy bármilyen, + XML végződésű típus) számára. JSON törzs esetén az alkalmazásnak/JSON-nek, Text/JSON-nak (vagy bármilyen, + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>A JSON konvertálása SZAPPANba folyékony sablon használatával
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>JSON átalakítása folyékony sablon használatával
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|törzs beállítása|Gyökérelem. A törzs szövegét vagy egy törzset visszaadó kifejezést tartalmaz.|Igen|

### <a name="properties"></a>properties

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|sablon|A beállított szövegtörzs által futtatott sablon mód módosításához használatos. Jelenleg az egyetlen támogatott érték a:<br /><br />-Liquid – a beállított szövegtörzs a folyékony sablonrendszer motorját fogja használni |Nem||

A kérelemmel és válaszsal kapcsolatos információkhoz való hozzáféréshez a likvid sablon a következő tulajdonságokkal rendelkező környezeti objektumhoz tud kötni: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérrendszer

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="SetHTTPheader"></a>HTTP-fejléc beállítása
 A `set-header` házirend egy értéket rendel egy meglévő válaszhoz és/vagy kérelem fejlécéhez, vagy új választ és/vagy kérési fejlécet ad hozzá.

 HTTP-fejléceket tartalmazó listát szúr be egy HTTP-üzenetbe. Bejövő folyamatba való helyezéskor ez a házirend beállítja a cél szolgáltatásnak átadott kérelem HTTP-fejléceit. A kimenő folyamatokban ez a házirend állítja be a HTTP-fejléceket az átjáró ügyfelének küldött válaszhoz.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Környezeti információk továbbítása a háttérbeli szolgáltatáshoz
 Ez a példa bemutatja, hogyan alkalmazhatja a szabályzatot az API-szinten a háttérbeli szolgáltatáshoz kapcsolódó környezeti információk biztosításához. A szabályzat konfigurálásával és használatával kapcsolatos bemutatóért lásd [: a Cloud Cover 177-es epizódja: További API Management a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 10:30-es gyors előretekerési funkciók. 12:10-on a fejlesztői portálon egy művelet meghívására szolgáló bemutató látható, ahol megtekintheti a szabályzatot a munkahelyen.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 További információ: [Policy Expressions](api-management-policy-expressions.md) and [Context változó](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Egy fejléc több értéke egy CSV-karakterlánchoz van fűzve, például:`headerName: value1,value2,value3`
>
> A kivételek közé tartoznak a szabványosított fejlécek, amelyek értékei:
> - tartalmazhat vesszőket (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - tartalmazhat dátumot (`Cookie`, `Set-Cookie`, `Warning`),
> - a következőt`Date`tartalmazza: dátum ( `Last-Modified`, `Expires`, `If-Modified-Since` `If-Unmodified-Since`,,, `Retry-After`).
>
> A kivételek esetén több fejléc-érték nem lesz összefűzve egyetlen karakterláncban, és külön fejlécként lesznek átadva, például:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|fejléc beállítása|Gyökérelem.|Igen|
|value|Megadja a beállítani kívánt fejléc értékét. Több azonos nevű fejléchez további `value` elemek is hozzáadhatók.|Igen|

### <a name="properties"></a>properties

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létező – művelet|Meghatározza, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Az attribútumnak a következő értékek egyikével kell rendelkeznie.<br /><br /> -felülbírálás – lecseréli a meglévő fejléc értékét.<br />-Skip – nem helyettesíti a meglévő fejléc értékét.<br />-append – hozzáfűzi az értéket a meglévő fejléc értékéhez.<br />-delete – eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy van `override` beállítva, hogy több bejegyzést is megadjon ugyanazzal a névvel, a fejléc az összes bejegyzésnek megfelelően be lesz állítva (amelyek többször is megjelennek); a rendszer csak a felsorolt értékeket adja meg az eredményben.|Nem|felülbírálás|
|name|Megadja a beállítani kívánt fejléc nevét.|Igen|–|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="SetQueryStringParameter"></a>Lekérdezési karakterlánc paraméterének beállítása
 A `set-query-parameter` házirend hozzáadja, lecseréli az értéket vagy törli a kérelem lekérdezési karakterláncának paraméterét. Felhasználható a háttérrendszer által várt lekérdezési paraméterek átadására, amelyek a kérelemben nem kötelező vagy soha nem jelennek meg.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Környezeti információk továbbítása a háttérbeli szolgáltatáshoz
 Ez a példa bemutatja, hogyan alkalmazhatja a szabályzatot az API-szinten a háttérbeli szolgáltatáshoz kapcsolódó környezeti információk biztosításához. A szabályzat konfigurálásával és használatával kapcsolatos bemutatóért lásd [: a Cloud Cover 177-es epizódja: További API Management a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 10:30-es gyors előretekerési funkciók. 12:10-on a fejlesztői portálon egy művelet meghívására szolgáló bemutató látható, ahol megtekintheti a szabályzatot a munkahelyen.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 További információ: [Policy Expressions](api-management-policy-expressions.md) and [Context változó](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|set-query-parameter|Gyökérelem.|Igen|
|value|Megadja a beállítani kívánt lekérdezési paraméter értékét. Az azonos nevű lekérdezési paraméterekhez további `value` elemek is hozzáadhatók.|Igen|

### <a name="properties"></a>properties

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|létező – művelet|Meghatározza, hogy milyen műveletet kell végrehajtani, ha a lekérdezési paraméter már meg van adva. Az attribútumnak a következő értékek egyikével kell rendelkeznie.<br /><br /> -felülbírálás – lecseréli a meglévő paraméter értékét.<br />-Skip-nem helyettesíti a lekérdezési paraméter meglévő értékét.<br />-append – hozzáfűzi az értéket a meglévő lekérdezési paraméter értékéhez.<br />-delete – eltávolítja a lekérdezési paramétert a kérelemből.<br /><br /> Ha úgy van `override` beállítva, hogy több bejegyzést is megadjon ugyanazzal a névvel, a lekérdezési paraméter az összes bejegyzésnek megfelelően van beállítva (amely többször is megjelenik); a rendszer csak a felsorolt értékeket adja meg az eredményben.|Nem|felülbírálás|
|name|Megadja a beállítani kívánt lekérdezési paraméter nevét.|Igen|–|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, háttérrendszer

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="RewriteURL"></a>URL-cím újraírása
 A `rewrite-uri` szabályzat az alábbi példában látható módon átalakítja a kérés URL-címét a nyilvános űrlapról a webszolgáltatás által várt űrlapra.

- Nyilvános URL-cím –`http://api.example.com/storenumber/ordernumber`

- Kérelem URL-címe –`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Ez a szabályzat akkor használható, ha egy emberi és/vagy böngésző-barát URL-címet át kell alakítani a webszolgáltatás által várt URL-formátumba. Ezt a házirendet csak akkor kell alkalmazni, ha alternatív URL-formátumot tesz elérhetővé, például tiszta URL-címeket, REST-URL-címeket, felhasználóbarát URL-címeket vagy kizárólag olyan URL-címeket, amelyek nem tartalmaznak lekérdezési karakterláncot, hanem csak az erőforrás elérési útját tartalmazzák ( a séma és a hatóság után). Ezt gyakran esztétikai, használhatósági vagy keresőmotor-optimalizálási (SEO) célokra kell elvégezni.

> [!NOTE]
>  A lekérdezési karakterlánc paramétereit csak a szabályzat használatával adhatja hozzá. Az Újraírási URL-címben nem adhat hozzá további sablonbeli elérésiút-paramétereket.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|újraírás – URI|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Attribútum|Leírás|Kötelező|Alapértelmezett|
|---------------|-----------------|--------------|-------------|
|sablon|A tényleges webszolgáltatás URL-címe bármely lekérdezési karakterlánc paraméterrel. Kifejezések használatakor a teljes értéknek kifejezésnek kell lennie.|Igen|–|
|másolás – nem egyező – paraméterek|Megadja, hogy az eredeti URL-sablonban nem szereplő lekérdezési paraméterek hozzáadódnak-e az Újraírási sablon által definiált URL-címhez.|Nem|true|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="XSLTransform"></a>XML átalakítása XSLT használatával
 A `Transform XML using an XSLT` szabályzat egy XSL-transzformációt alkalmaz a kérelem vagy válasz törzsében lévő XML-re.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Példa

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|xsl-transform|Gyökérelem.|Igen|
|paraméter|Az átalakításban használt változók definiálásához használatos|Nem|
|xsl: stíluslap|Gyökér stíluslap eleme. Az összes, a szabványos [XSLT](https://www.w3.org/TR/xslt) -specifikációban definiált elem és attribútum|Igen|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következők témaköröket:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
