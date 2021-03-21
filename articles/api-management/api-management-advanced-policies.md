---
title: Azure API Management speciális szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható speciális szabályzatokat. Tekintse át a példákat, és tekintse meg a további elérhető erőforrásokat
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 03529fd3c0231617c477f4f16773039a02386683
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562484"
---
# <a name="api-management-advanced-policies"></a>API Management – Speciális szabályzatok

Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](./api-management-policies.md).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> Speciális szabályzatok

-   [Vezérlési folyamat](api-management-advanced-policies.md#choose) – a rendszer a logikai [kifejezések](api-management-policy-expressions.md)kiértékelésének eredményei alapján feltételesen alkalmazza a házirend-utasításokat.
-   [Továbbítási kérelem](#ForwardRequest) – továbbítja a kérést a háttér-szolgáltatásnak.
-   [Egyidejűség korlátozása](#LimitConcurrency) – megakadályozza, hogy a befoglalt szabályzatok a megadott számú kérelemnél több időpontban legyenek végrehajtva.
-   [Eseménynapló az Event hub](#log-to-eventhub) -ba – a megadott formátumban küldi az üzeneteket egy adatgyűjtő entitás által definiált Event hub számára.
-   [Modell válasza](#mock-response) – megszakítja a folyamat végrehajtását, és egy kigúnyolt választ ad vissza közvetlenül a hívónak.
-   [Újrapróbálkozás](#Retry) – újrapróbálkozik a mellékelt házirend-utasítások végrehajtásával, ha a feltétel teljesül. A végrehajtás a megadott időintervallumokban és a megadott újrapróbálkozások számával megismétlődik.
-   [Visszatérési válasz](#ReturnResponse) – megszakítja a folyamat végrehajtását, és a megadott választ közvetlenül a hívónak adja vissza.
-   [Egyirányú kérelem küldése](#SendOneWayRequest) – kérés küldése a megadott URL-címre a válaszra való várakozás nélkül.
-   [Kérelem küldése](#SendRequest) – kérelem küldése a megadott URL-címre.
-   [Http-proxy beállítása](#SetHttpProxy) – lehetővé teszi a továbbított kérések továbbítását egy http-proxyn keresztül.
-   [Kérelem módszerének beállítása](#SetRequestMethod) – lehetővé teszi a kérések http-metódusának módosítását.
-   [Állapotkód beállítása](#SetStatus) – a HTTP-állapotkódot a megadott értékre módosítja.
-   [Változó beállítása](api-management-advanced-policies.md#set-variable) – megőrzi az értéket egy nevesített [környezeti](api-management-policy-expressions.md#ContextVariables) változóban a későbbi hozzáférés érdekében.
-   [Trace](#Trace) – egyéni nyomkövetéseket ad az [API Inspector](./api-management-howto-api-inspector.md) kimenetéhez, Application Insights telemetriáiról és az erőforrás-naplókhoz.
-   [Várakozás](#Wait) a befoglalt [küldési kérelemre](api-management-advanced-policies.md#SendRequest), az [érték beolvasása a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey), vagy a folytatás előtt a flow-szabályzatok [ellenőrzése](api-management-advanced-policies.md#choose) .

## <a name="control-flow"></a><a name="choose"></a> Vezérlési folyamat

A `choose` szabályzat a logikai kifejezések kiértékelésének eredménye alapján zárt házirend-utasításokat alkalmaz, hasonlóan egy if-then-Else vagy egy switch-összeállításhoz a programozási nyelven.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> Szabályzati utasítás

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

A vezérlési folyamat házirendjének legalább egy elemet tartalmaznia kell `<when/>` . Az `<otherwise/>` elem nem kötelező. Az `<when/>` elemekben lévő feltételek kiértékelése a szabályzaton belüli megjelenésük sorrendjében történik. A rendszer az első `<when/>` elemben, a Condition attribútummal együtt csatolt házirend-utasítást `true` alkalmazza. A `<otherwise/>` elemen belül foglalt szabályzatok, ha vannak, akkor lesznek alkalmazva, ha az összes `<when/>` elem feltétel attribútuma `false` .

### <a name="examples"></a>Példák

#### <a name="example"></a><a name="ChooseExample"></a> Például

Az alábbi példa egy [set-változó](api-management-advanced-policies.md#set-variable) házirendet és két vezérlési folyamat-szabályzatot mutat be.

A set változó házirend a bejövő szakaszban található, és egy `isMobile` logikai [környezeti](api-management-policy-expressions.md#ContextVariables) változót hoz létre, amely értéke TRUE (igaz), ha a `User-Agent` kérelem fejlécében a szöveg vagy a szerepel `iPad` `iPhone` .

Az első vezérlési folyamat a bejövő szakaszban is szerepel, és a környezeti változó értékétől függően a két [beállított lekérdezési karakterlánc](api-management-transformation-policies.md#SetQueryStringParameter) -házirend egyikét alkalmazza `isMobile` .

A második vezérlési folyamat a kimenő szakaszban található, és az [XML konvertálása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) -szabályzatra vonatkozik, ha a értéke `isMobile` `true` .

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Példa

Ez a példa azt mutatja be, hogyan hajtható végre a tartalom szűrése, ha eltávolít egy adatelemet a háttér-szolgáltatástól kapott válaszból a termék használatakor `Starter` . A szabályzat konfigurálásának és használatának bemutatását lásd: a [Cloud Cover 177-es epizódja: további API Management szolgáltatások a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 34:30-es gyors előretekeréssel. Indítsa el a 31:50-at a bemutatóhoz használt [Dark Sky előrejelzési API](https://developer.forecast.io/) áttekintéséhez.

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

### <a name="elements"></a>Elemek

| Elem   | Leírás                                                                                                                                                                                                                                                               | Kötelező |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Válassza    | Gyökérelem.                                                                                                                                                                                                                                                             | Yes      |
| mikor      | A `if` szabályzat vagy részeihez használandó feltétel `ifelse` `choose` . Ha a `choose` házirend több `when` szakaszt tartalmaz, a rendszer sorrendben értékeli ki őket. Ha az `condition` elem kiértékelése megtörtént `true` , nem kerül sor további `when` feltételek kiértékelésére. | Yes      |
| Ellenkező esetben | Azt a házirend-kódrészletet tartalmazza, amelyet akkor kell használni, ha egyik `when` feltétel sincs kiértékelve `true` .                                                                                                                                                                               | No       |

### <a name="attributes"></a>Attribútumok

| Attribútum                                              | Leírás                                                                                               | Kötelező |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| Condition = "boolean kifejezés &#124; Boolean konstans" | A `when` vonatkozó házirend-utasítás kiértékelése után kiértékelt logikai kifejezés vagy konstans. | Yes      |

### <a name="usage"></a><a name="ChooseUsage"></a> Használati

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="forward-request"></a><a name="ForwardRequest"></a> Továbbítási kérelem

A `forward-request` házirend továbbítja a bejövő kérelmet a kérelem [kontextusában](api-management-policy-expressions.md#ContextVariables)megadott háttér-szolgáltatásnak. A háttér-szolgáltatás URL-címe az API- [beállításokban](./import-and-publish.md) van megadva, és a [háttér-szolgáltatási házirend beállítása](api-management-transformation-policies.md) alapján módosítható.

> [!NOTE]
> Ha eltávolítja ezt a házirendet, a rendszer nem továbbítja a kérést a háttér-szolgáltatásnak, és a kimenő szakaszban lévő házirendek azonnal kiértékelésre kerülnek a szabályzatok sikeres befejezése után a bejövő szakaszban.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" buffer-response="true | false" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

A következő API-szintű házirend az összes API-kérést továbbítja a háttér-szolgáltatásnak, és 60 másodperces időtúllépési időközt.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa

Ez a műveleti szintű házirend az `base` elemet használja a háttér-házirend öröklésére a SZÜLŐ API-szint hatókörből.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa

Ez a műveleti szintű házirend explicit módon továbbítja az összes kérelmet a háttér-szolgáltatásnak 120-as időtúllépéssel, és nem örökli a szülő API-szint háttér-szabályzatát. Ha a háttér-szolgáltatás a 400 és 599 közötti kódú hibakódra válaszol, [a rendszer a hiba utáni](api-management-error-handling-policies.md) szakaszt aktiválja.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Példa

Ez a műveleti szintű házirend nem továbbítja a kéréseket a háttér-szolgáltatásnak.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elemek

| Elem         | Leírás   | Kötelező |
| --------------- | ------------- | -------- |
| továbbítás – kérelem | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum                                     | Leírás                                                                                                                                                                                                                                                                                                    | Kötelező | Alapértelmezett |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout = "egész"                             | Az az időtartam másodpercben, ameddig a háttér-szolgáltatás visszaadja a HTTP-válasz fejléceit, mielőtt időtúllépési hibát észlelt. A minimális érték 0 másodperc. A 240 másodpercnél nagyobb értékeket nem lehet megbecsülni, mivel a mögöttes hálózati infrastruktúra ezen idő elteltével el tudja dobni az üresjárati kapcsolatokat. | No       | Nincsenek    |
| az átirányítások követése = "false &#124; true"          | Megadja, hogy a rendszer az átjárót követi-e a háttér-szolgáltatásból, vagy visszaadja-e a hívónak.                                                                                                                                                                                                    | No       | hamis   |
| puffer-Request-Body = "false &#124; true"       | Ha a "true" (igaz) értékre van állítva, a rendszer pufferbe állítja, és újból felhasználja az [újrapróbálkozáskor](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | No       | hamis   |
| puffer-Response = "false &#124; true" | A darabolásos válaszok feldolgozását befolyásolja. Ha a "false" értékre van állítva, a rendszer a háttérből kapott összes adathalmazt azonnal visszaadja a hívónak. Ha a értéke "true" (igaz), akkor a rendszer pufferelt (8 kb, kivéve, ha az adatfolyam végét észleli), és csak ezután tért vissza a hívónak. | No | true |
| sikertelen művelet – hiba-állapot-kód = "false &#124; true" | Ha a True (igaz) értékre van állítva, a [hibakódok](api-management-error-handling-policies.md) értéke a 400 és 599 közötti tartományba esik.                                                                                                                                                                      | No       | hamis   |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   A **szabályzat részei:** háttérrendszer
-   **Házirend-hatókörök:** az összes hatókör

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a> Egyidejűség korlátozása

A házirend megakadályozza, hogy a befoglalt `limit-concurrency` szabályzatok a megadott számú kérelemnél többször is végrehajtás alatt legyenek. Ezen szám meghaladása után az új kérések azonnal meghiúsulnak, mert a 429 túl sok kérést tartalmazó állapotkód jelenik meg.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> Szabályzati utasítás

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Példák

#### <a name="example"></a>Példa

Az alábbi példa bemutatja, hogyan korlátozható a háttérbe továbbított kérelmek száma egy környezeti változó értéke alapján.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Elem           | Leírás   | Kötelező |
| ----------------- | ------------- | -------- |
| korlátozás – Egyidejűség | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                        | Kötelező | Alapértelmezett |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| kulcs       | Egy karakterlánc. A kifejezés engedélyezett. Megadja a Egyidejűség hatókörét. Több házirend is megosztható. | Yes      | N/A     |
| maximális darabszám | Egész szám. Megadja a szabályzat megadására jogosult kérelmek maximális számát.           | Yes      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> Naplózás az Event hub-ba

A `log-to-eventhub` házirend a megadott formátumban küldi az üzeneteket egy adatgyűjtő entitás által meghatározott Event hub számára. Ahogy a neve is jelenti, a szabályzatot a rendszer a kiválasztott kérelem vagy a válasz környezeti információinak mentésére használja online vagy offline elemzés céljából.

> [!NOTE]
> Az Event hub és a naplózási események konfigurálásának lépésenkénti útmutatója: [API Management események naplózása az Azure Event Hubs használatával](./api-management-howto-log-event-hubs.md).

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Példa

Bármely karakterlánc használható a Event Hubsba való bejelentkezéshez. Ebben a példában a dátum és idő, a központi telepítési szolgáltatás neve, a kérelem azonosítója, az IP-cím és a művelet neve az összes bejövő híváshoz az azonosítóval regisztrált Event hub Logger naplóba kerül. `contoso-logger`

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Elem         | Leírás                                                                     | Kötelező |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| eventhub | Gyökérelem. Az elem értéke az Event hub-ba való bejelentkezéshez használandó karakterlánc. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                               | Kötelező                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| naplózó – azonosító     | A API Management szolgáltatásban regisztrált naplózó azonosítója.         | Yes                                                                  |
| Partition-ID  | Meghatározza az üzeneteket küldő partíció indexét.             | Választható. Ez az attribútum nem használható, ha `partition-key` használatban van. |
| partíció – kulcs | Megadja az üzenetek küldésekor a partíció-hozzárendeléshez használt értéket. | Választható. Ez az attribútum nem használható, ha `partition-id` használatban van.  |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="mock-response"></a><a name="mock-response"></a> Mintául szolgáló válasz

A `mock-response` , ahogy a neve is mutatja, az API-k és a műveletek modellezésére szolgál. Megszakítja a folyamat normál végrehajtását, és egy kigúnyolt választ ad vissza a hívónak. A szabályzat mindig a legmagasabb szintű megbízhatósági válaszokat próbálja visszaadni. Ez inkább a válaszok tartalmi példáit részesíti előnyben, ha elérhető. A sémák alapján hoz létre példákat, és a sémákat is megadja, és nem. Ha nem találhatók példák vagy sémák, a rendszer visszaadja a tartalom nélküli válaszokat.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Példák

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elemek

| Elem       | Leírás   | Kötelező |
| ------------- | ------------- | -------- |
| modell – válasz | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum    | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| állapot kódja  | Megadja a válasz állapotkódot, és a megfelelő példa vagy séma kiválasztására szolgál.                 | No       | 200     |
| Content-Type | Megadja a `Content-Type` Válasz fejlécének értékét, és a megfelelő példa vagy séma kiválasztására szolgál. | No       | Nincsenek    |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

## <a name="retry"></a><a name="Retry"></a> Próbálja megismételni

A `retry` házirend egyszer végrehajtja az alárendelt házirendeket, majd újrapróbálkozik a végrehajtással, amíg az újrapróbálkozási kísérlet el `condition` `false` nem válik, vagy az újbóli próbálkozás `count` megszakad.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Példa

A következő példában a kérelmek továbbítása egy exponenciális újrapróbálkozási algoritmus használatával tízszer próbálkozik újra. Mivel a értéke `first-fast-retry` false (hamis), az összes újrapróbálkozási kísérlet az exponenciális újrapróbálkozási algoritmusra vonatkozik.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elemek

| Elem | Leírás                                                         | Kötelező |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Gyökérelem. Más szabályzatokat is tartalmazhat, amelyek alárendelt elemei. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                                                                                                                                           | Kötelező | Alapértelmezett |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| feltétel        | Logikai literál vagy [kifejezés](api-management-policy-expressions.md) , amely meghatározza, hogy az újrapróbálkozásokat le kell-e állítani ( `false` ) vagy a folytatást ( `true` ).      | Yes      | N/A     |
| count            | Egy pozitív szám, amely megadja a kísérlethez szükséges újrapróbálkozások maximális számát.                                                                                | Yes      | N/A     |
| interval         | Az újrapróbálkozási kísérletek közötti várakozási időközt másodpercben kifejezett pozitív szám.                                                                 | Yes      | N/A     |
| maximális időköz     | Az újrapróbálkozási kísérletek közötti maximális várakozási időközt megadó pozitív szám másodpercben. Egy exponenciális újrapróbálkozási algoritmus megvalósítására szolgál. | No       | N/A     |
| delta            | A várakozási időköz növekményét megadó pozitív szám másodpercben. A lineáris és exponenciális újrapróbálkozási algoritmusok megvalósítására szolgál.             | No       | N/A     |
| első – gyors újrapróbálkozás | Ha a értékre `true` van állítva, a rendszer azonnal végrehajtja az első újrapróbálkozási kísérletet.                                                                                  | No       | `false` |

> [!NOTE]
> Ha csak a `interval` érték van megadva, a **rögzített** intervallum-újrapróbálkozások lesznek elvégezve.
> Ha csak a `interval` és a `delta` van megadva, a rendszer egy **lineáris** intervallum újrapróbálkozási algoritmust használ, ahol az újrapróbálkozások közötti várakozási idő a következő képlet szerint számítható ki: `interval + (count - 1)*delta` .
> Ha a `interval` `max-interval` és a `delta` meg van adva, az **exponenciális** intervallum újrapróbálkozási algoritmusa lesz alkalmazva, ahol az újrapróbálkozások közötti várakozási idő exponenciálisan növekszik az értéktől a `interval` `max-interval` következő képletnek megfelelően `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)` .

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes) használható. Vegye figyelembe, hogy ez a szabályzat örökli a gyermek-házirend használati korlátozásait.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="return-response"></a><a name="ReturnResponse"></a> Visszatérési válasz

A `return-response` házirend megszakítja a folyamat végrehajtását, és egy alapértelmezett vagy egyéni választ ad vissza a hívónak. Az alapértelmezett válasz `200 OK` a törzs nélkül van. Az egyéni válasz a környezeti változók vagy a házirend-utasítások segítségével adható meg. Ha mindkettő meg van adva, a környezeti változóban található választ a házirend utasításai módosítják, mielőtt a rendszer visszaadja a hívónak.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Példa

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elemek

| Elem         | Leírás                                                                               | Kötelező |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| visszatérési válasz | Gyökérelem.                                                                             | Yes      |
| fejléc beállítása      | Egy [set-header](api-management-transformation-policies.md#SetHTTPheader) Policy utasítás. | No       |
| törzs beállítása        | Egy [set-test](api-management-transformation-policies.md#SetBody) Policy utasítás.         | No       |
| állapot beállítása      | Egy [beállított állapotra](api-management-advanced-policies.md#SetStatus) vonatkozó házirend-utasítás.           | No       |

### <a name="attributes"></a>Attribútumok

| Attribútum              | Leírás                                                                                                                                                                          | Kötelező  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| Válasz-változó – név | A (z) által hivatkozott környezeti változó neve, például egy felsőbb rétegbeli [küldési kérelmekre](api-management-advanced-policies.md#SendRequest) vonatkozó házirend, amely tartalmaz egy `Response` objektumot. | Választható. |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> Egyirányú kérelem küldése

A `send-one-way-request` házirend a megadott URL-címre küldi el a megadott kérelmet a válaszra való várakozás nélkül.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Példa

Ez a példa egy olyan példát mutat be, amely a `send-one-way-request` házirend használatával üzenetet küld egy Slack chat-szobájába, ha a http-válasz kódja nagyobb vagy egyenlő, mint 500. A mintával kapcsolatos további információkért lásd: [külső szolgáltatások használata az Azure API Management szolgáltatásból](./api-management-sample-send-request.md).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elemek

| Elem                    | Leírás                                                                                                 | Kötelező                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| egyirányú küldési kérelem       | Gyökérelem.                                                                                               | Yes                             |
| url                        | A kérelem URL-címe.                                                                                     | Nem if Mode = másolás; Ellenkező esetben igen. |
| method                     | A kérelem HTTP-metódusa.                                                                            | Nem if Mode = másolás; Ellenkező esetben igen. |
| fejléc                     | Kérelem fejléce Több fejléc elemet is használhat több kérelem fejlécéhez.                                  | No                              |
| body (Törzs)                       | A kérelem törzse.                                                                                           | No                              |
| hitelesítés – tanúsítvány | [Az ügyfél-hitelesítéshez használandó tanúsítvány](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "string" | Meghatározza, hogy ez egy új kérelem vagy az aktuális kérelem másolata. Kimenő módban a Mode = Copy nem inicializálja a kérelem törzsét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | Új      |
| name          | Megadja a beállítandó fejléc nevét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | N/A      |
| létező – művelet | Meghatározza, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Az attribútumnak a következő értékek egyikével kell rendelkeznie.<br /><br /> -felülbírálás – lecseréli a meglévő fejléc értékét.<br />-Skip – nem helyettesíti a meglévő fejléc értékét.<br />-append – hozzáfűzi az értéket a meglévő fejléc értékéhez.<br />-delete – eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy van beállítva, hogy `override` több bejegyzést is megadjon ugyanazzal a névvel, a fejléc az összes bejegyzésnek megfelelően be lesz állítva (amelyek többször is megjelennek); a rendszer csak a felsorolt értékeket adja meg az eredményben. | No       | felülbírálás |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="send-request"></a><a name="SendRequest"></a> Kérelem küldése

A `send-request` házirend a megadott URL-címre küldi el a megadott kérelmet, amely a beállított időtúllépési értéknél nem hosszabb ideig várakozik.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Példa

Ez a példa egy hivatkozási token engedélyezési kiszolgálóval való ellenőrzésének egyik módját mutatja be. A mintával kapcsolatos további információkért lásd: [külső szolgáltatások használata az Azure API Management szolgáltatásból](./api-management-sample-send-request.md).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elemek

| Elem                    | Leírás                                                                                                 | Kötelező                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| kérelem küldése               | Gyökérelem.                                                                                               | Yes                             |
| url                        | A kérelem URL-címe.                                                                                     | Nem if Mode = másolás; Ellenkező esetben igen. |
| method                     | A kérelem HTTP-metódusa.                                                                            | Nem if Mode = másolás; Ellenkező esetben igen. |
| fejléc                     | Kérelem fejléce Több fejléc elemet is használhat több kérelem fejlécéhez.                                  | No                              |
| body (Törzs)                       | A kérelem törzse.                                                                                           | No                              |
| hitelesítés – tanúsítvány | [Az ügyfél-hitelesítéshez használandó tanúsítvány](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>Attribútumok

| Attribútum                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "string"                   | Meghatározza, hogy ez egy új kérelem vagy az aktuális kérelem másolata. Kimenő módban a Mode = Copy nem inicializálja a kérelem törzsét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | Új      |
| Response-változó-Name = "string" | A válasz objektumot fogadó környezeti változó neve. Ha a változó nem létezik, akkor a szabályzat sikeres végrehajtása után jön létre, és a gyűjteményen keresztül lesz elérhető [`context.Variable`](api-management-policy-expressions.md#ContextVariables) .                                                                                                                                                                                                                                                                                                                          | Yes      | N/A      |
| timeout = "egész"               | Az URL-cím meghívása előtti időtúllépési időköz (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | No       | 60       |
| Mellőzés – hiba                    | Ha az értéke TRUE (igaz), és a kérelem hibát eredményez:<br /><br /> – Ha a Response-változó neve meg lett adva, akkor null értéket fog tartalmazni.<br />-If válasz-változó – a név nincs megadva, kontextus. A kérelmet nem frissíti a rendszer.                                                                                                                                                                                                                                                                                                                                                                                   | No       | hamis    |
| name                            | Megadja a beállítandó fejléc nevét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | N/A      |
| létező – művelet                   | Meghatározza, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Az attribútumnak a következő értékek egyikével kell rendelkeznie.<br /><br /> -felülbírálás – lecseréli a meglévő fejléc értékét.<br />-Skip – nem helyettesíti a meglévő fejléc értékét.<br />-append – hozzáfűzi az értéket a meglévő fejléc értékéhez.<br />-delete – eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy van beállítva, hogy `override` több bejegyzést is megadjon ugyanazzal a névvel, a fejléc az összes bejegyzésnek megfelelően be lesz állítva (amelyek többször is megjelennek); a rendszer csak a felsorolt értékeket adja meg az eredményben. | No       | felülbírálás |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> HTTP-proxy beállítása

A `proxy` házirend lehetővé teszi, hogy a kérelmeket http-proxyn keresztül továbbítsa a rendszer a háttérrendszer számára. Az átjáró és a proxy csak a HTTP (nem HTTPS) protokollt támogatja. Csak az alapszintű és az NTLM-hitelesítés.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Példa

Figyelje meg, hogy a [Tulajdonságok](api-management-howto-properties.md) a Felhasználónév és a jelszó értékeiként vannak tárolva, hogy elkerülje a bizalmas információk tárolását a szabályzat dokumentumában.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elemek

| Elem | Leírás  | Kötelező |
| ------- | ------------ | -------- |
| proxy   | Gyökérelem | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum         | Leírás                                            | Kötelező | Alapértelmezett |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| URL = "karakterlánc"      | A proxy URL-címe a formájában http://host:port .             | Yes      | N/A     |
| username = "string" | A proxyval történő hitelesítéshez használandó Felhasználónév. | No       | N/A     |
| password = "string" | A proxyval történő hitelesítéshez használandó jelszó. | No       | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> Kérelem metódusának beállítása

A `set-method` házirend lehetővé teszi a kérések http-kérelmi módszerének módosítását.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Példa

A `set-method` házirendet használó minta-szabályzat egy példát mutat be arra, hogy üzenetet küldjön a Slack chat-szobájába, ha a http-válasz kódja nagyobb vagy egyenlő, mint 500. A mintával kapcsolatos további információkért lásd: [külső szolgáltatások használata az Azure API Management szolgáltatásból](./api-management-sample-send-request.md).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elemek

| Elem    | Leírás                                                       | Kötelező |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-Method | Gyökérelem. Az elem értéke a HTTP-metódust adja meg. | Yes      |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-status-code"></a><a name="SetStatus"></a> Állapotkód beállítása

A `set-status` házirend a HTTP-állapotkódot a megadott értékre állítja.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Példa

Ez a példa azt szemlélteti, hogyan lehet visszaadni egy 401-es választ, ha az engedélyezési jogkivonat érvénytelen. További információ: [külső szolgáltatások használata az Azure API Management szolgáltatásból](./api-management-sample-send-request.md)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elemek

| Elem    | Leírás   | Kötelező |
| ---------- | ------------- | -------- |
| állapot beállítása | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum       | Leírás                                                | Kötelező | Alapértelmezett |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code = "egész_szám"  | A visszaadni kívánt HTTP-állapotkód.                            | Yes      | N/A     |
| OK = "string" | Az állapotkód visszaküldési okának leírása. | Yes      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Szabályzatok:** kimenő, háttérbeli, hiba esetén
-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-variable"></a><a name="set-variable"></a> Változó beállítása

A `set-variable` szabályzat deklarál egy [környezeti](api-management-policy-expressions.md#ContextVariables) változót, és egy [kifejezésen](api-management-policy-expressions.md) vagy egy szövegkonstans-karakterláncon keresztül megadott értéket rendel hozzá. Ha a kifejezésben literál szerepel, a rendszer karakterlánccá alakítja át, és az érték típusa lesz `System.String` .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> Szabályzati utasítás

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> Például

Az alábbi példa egy set változó szabályzatot mutat be a bejövő szakaszban. Ez a set változó házirend egy `isMobile` logikai [környezeti](api-management-policy-expressions.md#ContextVariables) változót hoz létre, amelynek értéke TRUE (igaz), ha a `User-Agent` kérelem fejlécében a szöveg vagy a szerepel `iPad` `iPhone` .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
```

### <a name="elements"></a>Elemek

| Elem      | Leírás   | Kötelező |
| ------------ | ------------- | -------- |
| változó beállítása | Gyökérelem. | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                              | Kötelező |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | A változó neve.                                                | Yes      |
| érték     | A változó értéke. Ez lehet egy kifejezés vagy egy literális érték. | Yes      |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
-   **Házirend-hatókörök:** az összes hatókör

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> Engedélyezett típusok

A szabályzatban használt kifejezéseknek `set-variable` a következő alapvető típusok egyikét kell visszaadniuk.

-   System. Boolean
-   System. sbyte érték
-   System. byte
-   System. UInt16
-   System. UInt32
-   System. UInt64
-   System. Int16
-   System. Int32
-   System. Int64
-   System. decimális
-   System. Single
-   System. Double
-   System. GUID
-   System. String
-   System. char
-   System. DateTime
-   System. TimeSpan
-   System. byte?
-   System. UInt16?
-   System. UInt32?
-   System. UInt64?
-   System. Int16?
-   System. Int32?
-   System. Int64?
-   System. decimális?
-   System. Single?
-   System. Double?
-   System. GUID?
-   System. String?
-   System. char?
-   System. DateTime?

## <a name="trace"></a><a name="Trace"></a> Nyomkövetési

A `trace` házirend egy egyéni nyomkövetést ad az API Inspector kimenetéhez, Application Insights telemetriáiról és/vagy erőforrás-naplókhoz.

-   A házirend egy egyéni nyomkövetést ad hozzá az [API Inspector](./api-management-howto-api-inspector.md) kimenetéhez a nyomkövetés indításakor, azaz a `Ocp-Apim-Trace` kérelem fejléce létezik, és igaz értékre van állítva, és `Ocp-Apim-Subscription-Key` a kérelem fejléce tartalmaz egy érvényes kulcsot, amely lehetővé teszi a nyomkövetést.
-   A házirend [nyomkövetési](../azure-monitor/app/data-model-trace-telemetry.md) telemetria hoz létre Application Insightsban, ha a [Application Insights integráció](./api-management-howto-app-insights.md) engedélyezve van, és a `severity` házirendben megadott érték egyenlő vagy nagyobb, mint a `verbosity` diagnosztikai beállításban megadott érték.
-   A házirend egy tulajdonságot ad a naplóbejegyzés számára, ha az [erőforrás-naplók](./api-management-howto-use-azure-monitor.md#activity-logs) engedélyezve vannak, és a házirendben megadott súlyossági szint a diagnosztikai beállításban megadott részletességi szintnél vagy annál nagyobb.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> Például

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elemek

| Elem  | Leírás                                                                                                                                          | Kötelező |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| nyomkövetési    | Gyökérelem.                                                                                                                                        | Yes      |
| message  | A naplózni kívánt karakterlánc vagy kifejezés.                                                                                                                 | Yes      |
| metaadatok | Egyéni tulajdonságot adhat hozzá a Application Insights [nyomkövetési](../azure-monitor/app/data-model-trace-telemetry.md) telemetria. | No       |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                               | Kötelező | Alapértelmezett |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | A nyomkövetési megjelenítőnek megfelelő karakterlánc, amely az üzenet forrását határozza meg.                                   | Yes      | N/A     |
| súlyosság  | Meghatározza a nyomkövetés súlyossági szintjét. Az engedélyezett értékek:, `verbose` `information` `error` (a legalacsonyabbtól a legmagasabbig). | No       | Részletes |
| name      | A tulajdonság neve.                                                                                                     | Yes      | N/A     |
| érték     | A tulajdonság értéke.                                                                                                    | Yes      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes) használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="wait"></a><a name="Wait"></a> várj

A `wait` házirend párhuzamosan hajtja végre az azonnali alárendelt házirendeket, és megvárja, amíg az összes vagy az egyik azonnali alárendelt házirend befejeződik. A várakozási szabályzat lehet az azonnali alárendelt házirendek [küldésére vonatkozó kérés](api-management-advanced-policies.md#SendRequest), az [érték beolvasása a gyorsítótárból és a](api-management-caching-policies.md#GetFromCacheByKey) [vezérlési folyamat](api-management-advanced-policies.md#choose) szabályzata.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Példa

A következő példában két házirend van `choose` , mint a szabályzat közvetlen alárendelt házirendjei `wait` . Ezek a `choose` házirendek párhuzamosan futnak. Minden `choose` szabályzat megkísérli beolvasni a gyorsítótárazott értéket. Ha hiányzik a gyorsítótár, a rendszer a háttér-szolgáltatást hívja meg az érték megadásához. Ebben a példában a `wait` házirend addig nem fejeződik be, amíg az összes azonnali alárendelt házirend nem fejeződött be, mert az attribútum a következőre `for` van beállítva: `all` . Ebben a példában a környezeti változók ( `execute-branch-one` , `value-one` ,, `execute-branch-two` és `value-two` ) a jelen példa házirend hatókörén kívül vannak deklarálva.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elemek

| Elem | Leírás                                                                                                   | Kötelező |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| várj    | Gyökérelem. Csak alárendelt elemek `send-request` , `cache-lookup-value` és szabályzatok szerepelhetnek benne `choose` . | Yes      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező | Alapértelmezett |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| a következőhöz:       | Meghatározza, hogy a `wait` házirend megvárja-e az összes azonnali alárendelt házirend befejeződését, vagy csak egyet. Az engedélyezett értékek a következők:<br /><br /> - `all` – Várjon, amíg az összes azonnali alárendelt házirend befejeződik<br />– bármely azonnali alárendelt házirend befejezésére való várakozás. Miután az első azonnali gyermek házirend befejeződik, a szabályzat befejeződik, `wait` és minden más azonnali alárendelt házirend végrehajtása leáll. | No       | összes     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérrendszer
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [Házirend-kifejezések](api-management-policy-expressions.md)
-   Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Szabályzatminták](./policy-reference.md)
