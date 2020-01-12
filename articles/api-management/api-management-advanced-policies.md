---
title: Azure API Management speciális szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható speciális szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903011"
---
# <a name="api-management-advanced-policies"></a>API Management speciális házirendek

Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a>Speciális szabályzatok

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
-   [Trace](#Trace) – egyéni nyomkövetéseket ad az [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimenetéhez, Application Insights Telemetriáiról és a diagnosztikai naplókhoz.
-   [Várakozás](#Wait) a befoglalt [küldési kérelemre](api-management-advanced-policies.md#SendRequest), az [érték beolvasása a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey), vagy a folytatás előtt a flow-szabályzatok [ellenőrzése](api-management-advanced-policies.md#choose) .

## <a name="choose"></a>Vezérlési folyamat

A `choose` szabályzat a logikai kifejezések kiértékelésének eredménye alapján zárt házirend-utasításokat alkalmaz, hasonlóan egy if-then-Else vagy egy switch-összeállításhoz a programozási nyelven.

### <a name="ChoosePolicyStatement"></a>Szabályzati utasítás

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

A vezérlési folyamat szabályzatának tartalmaznia kell legalább egy `<when/>` elemet. A `<otherwise/>` elem nem kötelező. A `<when/>` elemek állapotának kiértékelése a szabályzaton belüli megjelenésük sorrendjében történik. A feltétel attribútummal rendelkező első `<when/>` elemhez tartozó házirend-utasítás (ok) `true` lesz alkalmazva. A `<otherwise/>` elemben lévő szabályzatok, ha vannak, akkor lesznek alkalmazva, ha az összes `<when/>` elem feltétel-attribútuma `false`.

### <a name="examples"></a>Példák

#### <a name="ChooseExample"></a>Például

Az alábbi példa egy [set-változó](api-management-advanced-policies.md#set-variable) házirendet és két vezérlési folyamat-szabályzatot mutat be.

A set változó házirend a bejövő szakaszban található, és egy `isMobile` logikai [környezeti](api-management-policy-expressions.md#ContextVariables) változót hoz létre, amely értéke TRUE (igaz), ha a `User-Agent`-kérelem fejléce tartalmazza `iPad` vagy `iPhone`szövegét.

Az első vezérlési folyamat a bejövő szakaszban is szerepel, és az `isMobile` környezeti változó értékétől függően feltételesen alkalmazza a [lekérdezési karakterláncok két paraméterének](api-management-transformation-policies.md#SetQueryStringParameter) egyikét.

A második vezérlési folyamat a kimenő szakaszban található, és feltételesen alkalmazza az [XML konvertálása JSON-](api-management-transformation-policies.md#ConvertXMLtoJSON) szabályzatot, ha `isMobile` `true`ra van beállítva.

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
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

Ez a példa azt mutatja be, hogyan hajtható végre a tartalom szűrése, ha eltávolít egy adatelemet a háttér-szolgáltatástól kapott válaszból a `Starter` termék használatakor. A szabályzat konfigurálásának és használatának bemutatását lásd: a [Cloud Cover 177-es epizódja: további API Management szolgáltatások a Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 34:30-es gyors előretekeréssel. Indítsa el a 31:50-at a bemutatóhoz használt [Dark Sky előrejelzési API](https://developer.forecast.io/) áttekintéséhez.

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

| Elem   | Leírás                                                                                                                                                                                                                                                               | Szükséges |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| területen válassza a    | Gyökérelem.                                                                                                                                                                                                                                                             | Igen      |
| mikor      | Az `choose` házirend `if` vagy `ifelse` részeihez használandó feltétel. Ha a `choose` házirend több `when` szakaszt tartalmaz, a rendszer sorrendben értékeli ki őket. Ha egy elem `condition` `true`, akkor a rendszer nem értékel ki további `when` feltételeket. | Igen      |
| Ellenkező esetben | Azt a házirend-kódrészletet tartalmazza, amelyet akkor kell használni, ha a `when` feltételek egyikét sem értékeli `true`.                                                                                                                                                                               | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum                                              | Leírás                                                                                               | Szükséges |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| Condition = "logikai kifejezés &#124; Boolean konstans" | Az a logikai kifejezés vagy konstans, amelyet ki kell értékelni, ha a rendszer kiértékeli a tartalmazó `when` házirend-utasítást. | Igen      |

### <a name="ChooseUsage"></a>Használati

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="ForwardRequest"></a>Továbbítási kérelem

A `forward-request` házirend továbbítja a bejövő kérelmet a kérelem [környezetében](api-management-policy-expressions.md#ContextVariables)megadott háttér-szolgáltatásnak. A háttér-szolgáltatás URL-címe az API- [beállításokban](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) van megadva, és a [háttér-szolgáltatási házirend beállítása](api-management-transformation-policies.md) alapján módosítható.

> [!NOTE]
> Ha eltávolítja ezt a házirendet, a rendszer nem továbbítja a kérést a háttér-szolgáltatásnak, és a kimenő szakaszban lévő házirendek azonnal kiértékelésre kerülnek a szabályzatok sikeres befejezése után a bejövő szakaszban.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
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

Ez a műveleti szintű házirend a `base` elem használatával örökli a háttér-szabályzatot a szülő API-szint hatókörből.

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

| Elem         | Leírás   | Szükséges |
| --------------- | ------------- | -------- |
| forward-request | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum                                     | Leírás                                                                                                                                                                                                                                                                                                    | Szükséges | Alapértelmezett |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout = "egész"                             | Az az időtartam másodpercben, ameddig a háttér-szolgáltatás visszaadja a HTTP-válasz fejléceit, mielőtt időtúllépési hibát észlelt. A minimális érték 0 másodperc. A 240 másodpercnél nagyobb értékeket nem lehet megbecsülni, mivel a mögöttes hálózati infrastruktúra ezen idő elteltével el tudja dobni az üresjárati kapcsolatokat. | Nem       | None    |
| az átirányítások követése = " &#124; hamis igaz"          | Megadja, hogy a rendszer az átjárót követi-e a háttér-szolgáltatásból, vagy visszaadja-e a hívónak.                                                                                                                                                                                                    | Nem       | false   |
| puffer – kérelem – törzs = "hamis &#124; igaz"       | Ha a "true" (igaz) értékre van állítva, a rendszer pufferbe állítja, és újból felhasználja az [újrapróbálkozáskor](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Nem       | false   |
| sikertelen művelet – hiba-állapot-kód = "hamis &#124; igaz" | Ha a True (igaz) értékre van állítva, a [hibakódok](api-management-error-handling-policies.md) értéke a 400 és 599 közötti tartományba esik.                                                                                                                                                                      | Nem       | false   |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   A **szabályzat részei:** háttérrendszer
-   **Házirend-hatókörök:** az összes hatókör

## <a name="LimitConcurrency"></a>Egyidejűség korlátozása

A `limit-concurrency` házirend megakadályozza, hogy a megadott számú kérelemnél többször is végrehajtsa a befoglalt házirendeket. Ezen szám meghaladása után az új kérések azonnal meghiúsulnak, mert a 429 túl sok kérést tartalmazó állapotkód jelenik meg.

### <a name="LimitConcurrencyStatement"></a>Szabályzati utasítás

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
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Elem           | Leírás   | Szükséges |
| ----------------- | ------------- | -------- |
| korlátozás – Egyidejűség | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                        | Szükséges | Alapértelmezett |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| kulcs       | Egy karakterlánc. A kifejezés engedélyezett. Megadja a Egyidejűség hatókörét. Több házirend is megosztható. | Igen      | –     |
| maximális darabszám | Egész szám. Megadja a szabályzat megadására jogosult kérelmek maximális számát.           | Igen      | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="log-to-eventhub"></a>Naplózás az Event hub-ba

A `log-to-eventhub` házirend a megadott formátumban küldi az üzeneteket egy adatgyűjtő entitás által definiált Event hub számára. Ahogy a neve is jelenti, a szabályzatot a rendszer a kiválasztott kérelem vagy a válasz környezeti információinak mentésére használja online vagy offline elemzés céljából.

> [!NOTE]
> Az Event hub és a naplózási események konfigurálásának lépésenkénti útmutatója: [API Management események naplózása az Azure Event Hubs használatával](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Példa

Bármely karakterlánc használható a Event Hubsba való bejelentkezéshez. Ebben a példában a dátum és idő, a központi telepítési szolgáltatás neve, a kérelem azonosítója, az IP-cím és a művelet neve minden bejövő híváshoz be van jelentkezve a `contoso-logger`-AZONOSÍTÓval regisztrált Event hub Logger-ba.

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

| Elem         | Leírás                                                                     | Szükséges |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| eventhub | Gyökérelem. Az elem értéke az Event hub-ba való bejelentkezéshez használandó karakterlánc. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                               | Szükséges                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| naplózó – azonosító     | A API Management szolgáltatásban regisztrált naplózó azonosítója.         | Igen                                                                  |
| Partition-ID  | Meghatározza az üzeneteket küldő partíció indexét.             | Választható. Ez az attribútum nem használható, ha `partition-key` van használatban. |
| partíció – kulcs | Megadja az üzenetek küldésekor a partíció-hozzárendeléshez használt értéket. | Választható. Ez az attribútum nem használható, ha `partition-id` van használatban.  |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="mock-response"></a>Mintául szolgáló válasz

A `mock-response`, ahogy a neve is mutatja, az API-k és a műveletek modellezésére szolgál. Megszakítja a folyamat normál végrehajtását, és egy kigúnyolt választ ad vissza a hívónak. A szabályzat mindig a legmagasabb szintű megbízhatósági válaszokat próbálja visszaadni. Ez inkább a válaszok tartalmi példáit részesíti előnyben, ha elérhető. A sémák alapján hoz létre példákat, és a sémákat is megadja, és nem. Ha nem találhatók példák vagy sémák, a rendszer visszaadja a tartalom nélküli válaszokat.

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

| Elem       | Leírás   | Szükséges |
| ------------- | ------------- | -------- |
| mock-response | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum    | Leírás                                                                                           | Szükséges | Alapértelmezett |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| állapot kódja  | Megadja a válasz állapotkódot, és a megfelelő példa vagy séma kiválasztására szolgál.                 | Nem       | 200     |
| Content-Type | Megadja `Content-Type` válasz fejlécének értékét, és a megfelelő példa vagy séma kiválasztására szolgál. | Nem       | None    |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

## <a name="Retry"></a>Próbálja megismételni

A `retry` házirend egyszer végrehajtja az alárendelt házirendeket, majd újrapróbálkozik a végrehajtással, amíg az újrapróbálkozási `condition` `false` vagy újrapróbálkozás `count` kimerült.

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

A következő példában a kérelmek továbbítása egy exponenciális újrapróbálkozási algoritmus használatával tízszer próbálkozik újra. Mivel `first-fast-retry` hamis értékre van állítva, az összes újrapróbálkozási kísérlet az exponenciális újrapróbálkozási algoritmusra vonatkozik.

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

| Elem | Leírás                                                         | Szükséges |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Gyökérelem. Más szabályzatokat is tartalmazhat, amelyek alárendelt elemei. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum        | Leírás                                                                                                                                           | Szükséges | Alapértelmezett |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| feltétel        | Logikai literál vagy [kifejezés](api-management-policy-expressions.md) , amely megadja, hogy az újrapróbálkozásokat le kell-e állítani (`false`) vagy a folytatást (`true`).      | Igen      | –     |
| count            | Egy pozitív szám, amely megadja a kísérlethez szükséges újrapróbálkozások maximális számát.                                                                                | Igen      | –     |
| interval         | Az újrapróbálkozási kísérletek közötti várakozási időközt másodpercben kifejezett pozitív szám.                                                                 | Igen      | –     |
| maximális időköz     | Az újrapróbálkozási kísérletek közötti maximális várakozási időközt megadó pozitív szám másodpercben. Egy exponenciális újrapróbálkozási algoritmus megvalósítására szolgál. | Nem       | –     |
| különbözeti            | A várakozási időköz növekményét megadó pozitív szám másodpercben. A lineáris és exponenciális újrapróbálkozási algoritmusok megvalósítására szolgál.             | Nem       | –     |
| első – gyors újrapróbálkozás | Ha `true` értékre van állítva, a rendszer azonnal végrehajtja az első újrapróbálkozási kísérletet.                                                                                  | Nem       | `false` |

> [!NOTE]
> Ha csak a `interval` van megadva, a rendszer **rögzített** intervallum-újrapróbálkozásokat hajt végre.
> Ha csak a `interval` és `delta` van megadva, a rendszer egy **lineáris** intervallum-újrapróbálkozási algoritmust használ, ahol az újrapróbálkozások közötti várakozási idő a következő képlet – `interval + (count - 1)*delta`alapján számítható ki.
> Ha a `interval`, `max-interval` és `delta` meg van adva, az **exponenciális** intervallum újrapróbálkozási algoritmust alkalmazza a rendszer, ahol az újrapróbálkozások közötti várakozási idő exponenciálisan növekszik a `interval` értéktől a következő képletnek megfelelően `max-interval` értékig.

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) használható. Vegye figyelembe, hogy ez a szabályzat örökli a gyermek-házirend használati korlátozásait.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="ReturnResponse"></a>Visszatérési válasz

Az `return-response` házirend megszakítja a folyamat-végrehajtást, és egy alapértelmezett vagy egyéni választ ad vissza a hívónak. Az alapértelmezett válasz `200 OK` törzs nélkül. Az egyéni válasz a környezeti változók vagy a házirend-utasítások segítségével adható meg. Ha mindkettő meg van adva, a környezeti változóban található választ a házirend utasításai módosítják, mielőtt a rendszer visszaadja a hívónak.

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

| Elem         | Leírás                                                                               | Szükséges |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| visszatérési válasz | Gyökérelem.                                                                             | Igen      |
| set-header      | Egy [set-header](api-management-transformation-policies.md#SetHTTPheader) Policy utasítás. | Nem       |
| törzs beállítása        | Egy [set-test](api-management-transformation-policies.md#SetBody) Policy utasítás.         | Nem       |
| állapot beállítása      | Egy [beállított állapotra](api-management-advanced-policies.md#SetStatus) vonatkozó házirend-utasítás.           | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum              | Leírás                                                                                                                                                                          | Szükséges  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| Válasz-változó – név | A (z) által hivatkozott környezeti változó neve, például egy felsőbb rétegbeli [küldési kérési](api-management-advanced-policies.md#SendRequest) házirend, amely egy `Response` objektumot tartalmaz. | Választható. |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="SendOneWayRequest"></a>Egyirányú kérelem küldése

A `send-one-way-request` házirend a megadott URL-címre küldi el a kérést a válaszra való várakozás nélkül.

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

Ez a példa azt mutatja be, hogyan lehet a `send-one-way-request` szabályzattal üzenetet küldeni egy Slack chat-szobájába, ha a HTTP-válasz kódja nagyobb vagy egyenlő, mint 500. A mintával kapcsolatos további információkért lásd: [külső szolgáltatások használata az Azure API Management szolgáltatásból](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Elem                    | Leírás                                                                                                 | Szükséges                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| egyirányú küldési kérelem       | Gyökérelem.                                                                                               | Igen                             |
| url                        | A kérelem URL-címe.                                                                                     | Nem if Mode = másolás; Ellenkező esetben igen. |
| method                     | A kérelem HTTP-metódusa.                                                                            | Nem if Mode = másolás; Ellenkező esetben igen. |
| header                     | Kérelem fejléce Több fejléc elemet is használhat több kérelem fejlécéhez.                                  | Nem                              |
| törzs                       | A kérelem törzse.                                                                                           | Nem                              |
| hitelesítés – tanúsítvány | [Az ügyfél-hitelesítéshez használandó tanúsítvány](api-management-authentication-policies.md#ClientCertificate) | Nem                              |

### <a name="attributes"></a>Attribútumok

| Attribútum     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "string" | Meghatározza, hogy ez egy új kérelem vagy az aktuális kérelem másolata. Kimenő módban a Mode = Copy nem inicializálja a kérelem törzsét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nem       | Új      |
| név          | Megadja a beállítandó fejléc nevét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Igen      | –      |
| létező – művelet | Meghatározza, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Az attribútumnak a következő értékek egyikével kell rendelkeznie.<br /><br /> -felülbírálás – lecseréli a meglévő fejléc értékét.<br />-Skip – nem helyettesíti a meglévő fejléc értékét.<br />-append – hozzáfűzi az értéket a meglévő fejléc értékéhez.<br />-delete – eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy van beállítva, hogy `override` több bejegyzést ugyanazzal a névvel, akkor a fejléc az összes bejegyzésnek megfelelően be lesz állítva (amely többször szerepel); a rendszer csak a felsorolt értékeket adja meg az eredményben. | Nem       | felülbírálás |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="SendRequest"></a>Kérelem küldése

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

Ez a példa egy hivatkozási token engedélyezési kiszolgálóval való ellenőrzésének egyik módját mutatja be. A mintával kapcsolatos további információkért lásd: [külső szolgáltatások használata az Azure API Management szolgáltatásból](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Elem                    | Leírás                                                                                                 | Szükséges                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| kérelem küldése               | Gyökérelem.                                                                                               | Igen                             |
| url                        | A kérelem URL-címe.                                                                                     | Nem if Mode = másolás; Ellenkező esetben igen. |
| method                     | A kérelem HTTP-metódusa.                                                                            | Nem if Mode = másolás; Ellenkező esetben igen. |
| header                     | Kérelem fejléce Több fejléc elemet is használhat több kérelem fejlécéhez.                                  | Nem                              |
| törzs                       | A kérelem törzse.                                                                                           | Nem                              |
| hitelesítés – tanúsítvány | [Az ügyfél-hitelesítéshez használandó tanúsítvány](api-management-authentication-policies.md#ClientCertificate) | Nem                              |

### <a name="attributes"></a>Attribútumok

| Attribútum                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "string"                   | Meghatározza, hogy ez egy új kérelem vagy az aktuális kérelem másolata. Kimenő módban a Mode = Copy nem inicializálja a kérelem törzsét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nem       | Új      |
| Response-változó-Name = "string" | A válasz objektumot fogadó környezeti változó neve. Ha a változó nem létezik, akkor a szabályzat sikeres végrehajtása után jön létre, és [`context.Variable`](api-management-policy-expressions.md#ContextVariables) gyűjteményen keresztül lesz elérhető.                                                                                                                                                                                                                                                                                                                          | Igen      | –      |
| timeout = "egész"               | Az URL-cím meghívása előtti időtúllépési időköz (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nem       | 60       |
| Mellőzés – hiba                    | Ha az értéke TRUE (igaz), és a kérelem hibát eredményez:<br /><br /> – Ha a Response-változó neve meg lett adva, akkor null értéket fog tartalmazni.<br />-If válasz-változó – a név nincs megadva, kontextus. A kérelmet nem frissíti a rendszer.                                                                                                                                                                                                                                                                                                                                                                                   | Nem       | false    |
| név                            | Megadja a beállítandó fejléc nevét.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Igen      | –      |
| létező – művelet                   | Meghatározza, hogy milyen műveletet kell végrehajtani, ha a fejléc már meg van adva. Az attribútumnak a következő értékek egyikével kell rendelkeznie.<br /><br /> -felülbírálás – lecseréli a meglévő fejléc értékét.<br />-Skip – nem helyettesíti a meglévő fejléc értékét.<br />-append – hozzáfűzi az értéket a meglévő fejléc értékéhez.<br />-delete – eltávolítja a fejlécet a kérelemből.<br /><br /> Ha úgy van beállítva, hogy `override` több bejegyzést ugyanazzal a névvel, akkor a fejléc az összes bejegyzésnek megfelelően be lesz állítva (amely többször szerepel); a rendszer csak a felsorolt értékeket adja meg az eredményben. | Nem       | felülbírálás |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="SetHttpProxy"></a>HTTP-proxy beállítása

A `proxy` szabályzat lehetővé teszi, hogy a kérelmeket HTTP-proxyn keresztül továbbítsa a rendszer a háttérrendszer számára. Az átjáró és a proxy csak a HTTP (nem HTTPS) protokollt támogatja. Csak az alapszintű és az NTLM-hitelesítés.

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

| Elem | Leírás  | Szükséges |
| ------- | ------------ | -------- |
| proxy   | Gyökérelem | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum         | Leírás                                            | Szükséges | Alapértelmezett |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| URL = "karakterlánc"      | A proxy URL-címe http://host:port formában.             | Igen      | –     |
| username = "string" | A proxyval történő hitelesítéshez használandó Felhasználónév. | Nem       | –     |
| password = "string" | A proxyval történő hitelesítéshez használandó jelszó. | Nem       | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="SetRequestMethod"></a>Kérelem metódusának beállítása

A `set-method` házirend lehetővé teszi a kérések HTTP-kérelmi módszerének módosítását.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Példa

Ez a `set-method` házirendet használó minta-szabályzat egy példát mutat be arra, hogy üzenetet küldjön a Slack chat-szobájába, ha a HTTP-válasz kódja nagyobb vagy egyenlő, mint 500. A mintával kapcsolatos további információkért lásd: [külső szolgáltatások használata az Azure API Management szolgáltatásból](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Elem    | Leírás                                                       | Szükséges |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-Method | Gyökérelem. Az elem értéke a HTTP-metódust adja meg. | Igen      |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, on-Error

-   **Házirend-hatókörök:** az összes hatókör

## <a name="SetStatus"></a>Állapotkód beállítása

A `set-status` házirend beállítja a HTTP-állapotkódot a megadott értékre.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Példa

Ez a példa azt szemlélteti, hogyan lehet visszaadni egy 401-es választ, ha az engedélyezési jogkivonat érvénytelen. További információ: [külső szolgáltatások használata az Azure API Management szolgáltatásból](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Elem    | Leírás   | Szükséges |
| ---------- | ------------- | -------- |
| állapot beállítása | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum       | Leírás                                                | Szükséges | Alapértelmezett |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code = "egész_szám"  | A visszaadni kívánt HTTP-állapotkód.                            | Igen      | –     |
| OK = "string" | Az állapotkód visszaküldési okának leírása. | Igen      | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Szabályzatok:** kimenő, háttérbeli, hiba esetén
-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-variable"></a>Változó beállítása

A `set-variable` szabályzat deklarál egy [környezeti](api-management-policy-expressions.md#ContextVariables) változót, és egy [kifejezésen](api-management-policy-expressions.md) vagy egy szövegkonstans-karakterláncon keresztül megadott értéket rendel hozzá. Ha a kifejezésben literál szerepel, a rendszer karakterlánccá alakítja át, és az érték típusa `System.String`lesz.

### <a name="set-variablePolicyStatement"></a>Szabályzati utasítás

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Például

Az alábbi példa egy set változó szabályzatot mutat be a bejövő szakaszban. Ez a set változó házirend egy `isMobile` logikai [környezeti](api-management-policy-expressions.md#ContextVariables) változót hoz létre, amely igaz értékre van állítva, ha a `User-Agent`-kérelem fejléce tartalmazza `iPad` vagy `iPhone`szövegét.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elemek

| Elem      | Leírás   | Szükséges |
| ------------ | ------------- | -------- |
| változó beállítása | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                              | Szükséges |
| --------- | ------------------------------------------------------------------------ | -------- |
| név      | A változó neve.                                                | Igen      |
| érték     | A változó értéke. Ez lehet egy kifejezés vagy egy literális érték. | Igen      |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén
-   **Házirend-hatókörök:** az összes hatókör

### <a name="set-variableAllowedTypes"></a>Engedélyezett típusok

A `set-variable` szabályzatban használt kifejezéseknek a következő alaptípusok egyikét kell visszaadniuk.

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

## <a name="Trace"></a>Nyomkövetési

Az `trace` házirend egy egyéni nyomkövetést ad az API Inspector kimenetéhez, Application Insights telemetriáiról és/vagy diagnosztikai naplókat.

-   A házirend egy egyéni nyomkövetést ad hozzá az [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimenetéhez a nyomkövetés indításakor, azaz `Ocp-Apim-Trace` a kérelem fejléce létezik, és igaz értékre van állítva, és `Ocp-Apim-Subscription-Key` kérelem fejléce megtalálható, és érvényes kulccsal rendelkezik, amely lehetővé teszi a nyomkövetést.
-   A házirend [nyomkövetési](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetria hoz létre Application Insightsban, amikor a [Application Insights integráció](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) engedélyezve van, és a házirendben megadott `severity` szint a diagnosztikai beállításban megadott `verbosity` szintnél vagy annál nagyobb.
-   A házirend egy tulajdonságot ad a naplóbejegyzés számára, ha a [diagnosztikai naplók](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) engedélyezve vannak, és a házirendben megadott súlyossági szint a diagnosztikai beállításban megadott részletességi szintnél vagy annál nagyobb.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="traceExample"></a>Például

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elemek

| Elem  | Leírás                                                                                                                                          | Szükséges |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| nyomkövetési    | Gyökérelem.                                                                                                                                        | Igen      |
| message  | A naplózni kívánt karakterlánc vagy kifejezés.                                                                                                                 | Igen      |
| metaadatok | Egyéni tulajdonságot adhat hozzá a Application Insights [nyomkövetési](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetria. | Nem       |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                               | Szükséges | Alapértelmezett |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | A nyomkövetési megjelenítőnek megfelelő karakterlánc, amely az üzenet forrását határozza meg.                                   | Igen      | –     |
| súlyosság  | Meghatározza a nyomkövetés súlyossági szintjét. Az engedélyezett értékek: `verbose`, `information`, `error` (a legalacsonyabbról a legmagasabbra). | Nem       | Részletezés |
| név      | A tulajdonság neve.                                                                                                     | Igen      | –     |
| érték     | A tulajdonság értéke.                                                                                                    | Igen      | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérbeli, hiba esetén

-   **Házirend-hatókörök:** az összes hatókör

## <a name="Wait"></a>várj

Az `wait` házirend párhuzamosan hajtja végre az azonnali alárendelt házirendeket, és a befejezés előtt megvárja, amíg az összes vagy az egyik azonnali alárendelt házirendje be nem fejeződik. A várakozási szabályzat lehet az azonnali alárendelt házirendek [küldésére vonatkozó kérés](api-management-advanced-policies.md#SendRequest), az [érték beolvasása a gyorsítótárból és a](api-management-caching-policies.md#GetFromCacheByKey) [vezérlési folyamat](api-management-advanced-policies.md#choose) szabályzata.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Példa

A következő példában két `choose` házirend szerepel a `wait` házirend azonnali alárendelt házirendjében. Mindkét `choose` házirend párhuzamosan fut. Minden `choose` házirend egy gyorsítótárazott érték beolvasását kísérli meg. Ha hiányzik a gyorsítótár, a rendszer a háttér-szolgáltatást hívja meg az érték megadásához. Ebben a példában a `wait` házirend addig nem fejeződik be, amíg az összes azonnali alárendelt házirend nem fejeződött be, mert a `for` attribútum értéke `all`. Ebben a példában a környezeti változók (`execute-branch-one`, `value-one`, `execute-branch-two`és `value-two`) a jelen példa házirend hatókörén kívül vannak deklarálva.

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

| Elem | Leírás                                                                                                   | Szükséges |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| várj    | Gyökérelem. Csak gyermek elemeket tartalmazhatnak `send-request`, `cache-lookup-value`és `choose` szabályzatok. | Igen      |

### <a name="attributes"></a>Attribútumok

| Attribútum | Leírás                                                                                                                                                                                                                                                                                                                                                                                                            | Szükséges | Alapértelmezett |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| keressen rá a       | Meghatározza, hogy az `wait` házirend megvárja-e az összes azonnali alárendelt házirend befejeződését, vagy csak egyet. Az engedélyezett értékek a következők:<br /><br /> - `all` – várjon, amíg az összes azonnali alárendelt házirend befejeződik<br />– bármely azonnali alárendelt házirend befejezésére való várakozás. Miután az első azonnali gyermek házirend befejeződik, a `wait` szabályzat befejeződik, és minden más azonnali alárendelt házirend végrehajtása leáll. | Nem       | mind     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő, háttérrendszer
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [Házirend-kifejezések](api-management-policy-expressions.md)
-   Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Házirend-minták](policy-samples.md)
