---
title: HTTP-kérelmek előállítása API Management szolgáltatás használatával
description: Ismerje meg, hogyan használhatja a kérelmek és válaszok házirendjeit a API Managementban az API-k külső szolgáltatásainak meghívásához
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742389"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Külső szolgáltatások használata az Azure API Management szolgáltatásból
Az Azure API Management szolgáltatásban elérhető házirendek számos hasznos munkát végezhetnek, tisztán a bejövő kérelem, a kimenő válasz és az alapszintű konfigurációs információk alapján. A külső szolgáltatásokkal való interakcióhoz azonban API Management házirendek számos lehetőséget nyitnak meg.

Korábban már láttuk, hogyan használhatja az [Azure Event hub szolgáltatást a naplózáshoz, a figyeléshez és az elemzéshez](api-management-log-to-eventhub-sample.md). Ez a cikk azokat a szabályzatokat mutatja be, amelyek lehetővé teszik a külső HTTP-alapú szolgáltatásokkal való interakciót. Ezek a házirendek a távoli események kiváltására vagy az eredeti kérelem és válasz kezeléséhez használt információk beolvasására használhatók.

## <a name="send-one-way-request"></a>Egyirányú küldési kérelem
Lehetséges, hogy a legegyszerűbb külső interakció a tűz és a kitakarítási stílus, amely lehetővé teszi, hogy egy külső szolgáltatás értesítést kapjon valamilyen fontos eseményről. A vezérlési folyamat szabályzata `choose` bármely olyan feltétel észlelésére használható, amely érdekli.  Ha a feltétel teljesül, külső HTTP-kérést is elvégezhet a [küldési egyirányú kérelmekkel](./api-management-advanced-policies.md#SendOneWayRequest) kapcsolatos házirend használatával. Ez lehet egy olyan üzenetküldési rendszerre irányuló kérelem, mint például a Hipchat vagy a Slack, vagy egy e-mail API, például a SendGrid vagy a MailChimp, vagy olyan kritikus támogatási incidensek esetében, mint például a PagerDuty. Ezen üzenetkezelő rendszerek mindegyike egyszerű HTTP API-kkal rendelkezik, amelyek meghívhatók.

### <a name="alerting-with-slack"></a>Riasztás a Slacktel
Az alábbi példa bemutatja, hogyan küldhet üzenetet egy Slack chat-szobájába, ha a HTTP-válasz állapotkód nagyobb vagy egyenlő, mint 500. A 500-tartomány hibája hibát jelez a háttér API-val, amelyet az API-ügyfél nem tud feloldani. Általában API Management részben beavatkozásra van szükség.  

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

A Slack a bejövő webhookok fogalma. A bejövő webhookok konfigurálásakor a Slack egy speciális URL-címet hoz létre, amely lehetővé teszi egy egyszerű POST-kérelem elvégzését és egy üzenet átadását a Slack-csatornára. A létrehozott JSON-törzs a Slack által definiált formátumon alapul.

![Slack web Hook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Tűz van, és elég jól elfelejteni?
Bizonyos kompromisszumok használatakor a rendszer tűz-és felejtsds stílusú kéréseket használ. Ha valamilyen okból kifolyólag a kérés meghiúsul, a rendszer nem fogja jelenteni a hibát. Ebben a konkrét helyzetben a másodlagos hibajelentési rendszer és a válaszra való várakozás további teljesítményének összetettsége nem indokolt. Olyan esetekben, amikor elengedhetetlen a válasz ellenőrzését, a [Send-Request](./api-management-advanced-policies.md#SendRequest) házirend jobb megoldás.

## <a name="send-request"></a>Send-Request
A `send-request` házirend lehetővé teszi, hogy egy külső szolgáltatás összetett feldolgozási funkciókat végezzen, és az API Management szolgáltatásba visszaküldse azokat, amelyeket a házirendek további feldolgozásához is használhatnak.

### <a name="authorizing-reference-tokens"></a>Hivatkozási tokenek engedélyezése
A API Management egyik fő funkciója a háttérbeli erőforrások védelme. Ha az API által használt engedélyezési kiszolgáló JWT- [jogkivonatokat](https://jwt.io/) hoz létre a OAuth2 folyamat részeként, ahogy az [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) , akkor a szabályzat segítségével `validate-jwt` ellenőrizheti a jogkivonat érvényességét. Egyes engedélyezési kiszolgálók létrehozzák azokat a [hivatkozási jogkivonatokat](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , amelyek nem ellenőrizhetők az engedélyezési kiszolgáló visszahívása nélkül.

### <a name="standardized-introspection"></a>Szabványosított betekintés
A múltban nem volt szabványosított módszer a hivatkozási token engedélyezési kiszolgálóval való ellenőrzéséhez. Azonban a közelmúltban javasolt [RFC 7662](https://tools.ietf.org/html/rfc7662) -et az IETF közzétette, amely meghatározza, hogy az erőforrás-kiszolgáló hogyan ellenőrizheti a jogkivonat érvényességét.

### <a name="extracting-the-token"></a>A jogkivonat kibontása
Első lépésként ki kell bontania a tokent az engedélyezési fejlécből. A fejléc értékének az `Bearer` engedélyezési sémával, egyetlen szóközzel, majd az engedélyezési jogkivonattal kell formázni az [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1)-ként. Sajnos vannak olyan esetek, amikor az engedélyezési séma ki van hagyva. Ha ezt az elemzés során szeretné figyelembe venni, API Management a fejléc értékét feldarabolja egy szóközzel, és kiválasztja az utolsó karakterláncot a karakterláncok visszaadott tömbből. Ez megkerülő megoldást biztosít a helytelenül formázott engedélyezési fejlécekhez.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Az érvényesítési kérelem készítése
Ha API Management rendelkezik az engedélyezési jogkivonattal, API Management elvégezheti a jogkivonat érvényesítésére vonatkozó kérelmet. Az RFC 7662 ezt a folyamatot hívja meg, és megköveteli, hogy `POST` HTML-űrlapot lehessen betekinteni a befelé irányuló erőforráshoz. A HTML-űrlapnak legalább egy kulcs/érték párokat kell tartalmaznia `token` . Az engedélyezési kiszolgálónak küldött kérést is hitelesíteni kell, hogy a rosszindulatú ügyfelek nem mehetnek át az érvényes tokenek számára.

```xml
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
```

### <a name="checking-the-response"></a>A válasz ellenőrzése
Az `response-variable-name` attribútum a visszaadott válasz elérésére szolgál. Az ebben a tulajdonságban definiált név kulcsként használható a `context.Variables` szótárban az objektum eléréséhez `IResponse` .

A válasz objektumból lekérheti a törzset, és az RFC 7622 azt mutatja API Management, hogy a válasznak JSON-objektumnak kell lennie, és tartalmaznia kell legalább egy logikai érték nevű tulajdonságot `active` . Ha `active` igaz, akkor a jogkivonat érvényesnek minősül.

Ha az engedélyezési kiszolgáló nem tartalmazza az "aktív" mezőt, amely jelzi, hogy a jogkivonat érvényes-e, használjon olyan eszközt, mint a Poster, amely meghatározza, hogy a rendszer milyen tulajdonságokat állít be egy érvényes jogkivonatban. Ha például egy érvényes jogkivonat-válasz "expires_in" nevű tulajdonságot tartalmaz, akkor ellenőrizze, hogy ez a tulajdonságnév létezik-e az engedélyezési kiszolgáló válaszában, így:

<a Condition = "@ ((IResponse) kontextusban. Változók ["tokenstate"]). Body.As <JObject> (). Tulajdonság ("expires_in") = = NULL) ">

### <a name="reporting-failure"></a>Jelentéskészítési hiba
A szabályzattal megállapíthatja, `<choose>` hogy a jogkivonat érvénytelen-e, és ha igen, egy 401 választ ad vissza.

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

Az [RFC 6750-es](https://tools.ietf.org/html/rfc6750#section-3) verziónak megfelelően, amely leírja `bearer` a jogkivonatok használatát, API Management `WWW-Authenticate` a 401-válasz fejlécét is visszaadja. A WWW-Authenticate arra szolgál, hogy egy ügyfelet a megfelelő jogosultságú kérelem létrehozásával utasítsa. A OAuth2-keretrendszerrel lehetséges módszerek széles választéka miatt nehéz kommunikálni az összes szükséges információval. Szerencsére vannak olyan erőfeszítések, amelyek segítségével az [ügyfelek megtudhatják, hogyan lehet megfelelően engedélyezni a kérelmeket az erőforrás-kiszolgálóknak](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Végső megoldás
A végén a következő szabályzatot kapja:

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
              <return-response response-variable-name="existing response variable">
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

Ez csupán egy példa arra, hogy a szabályzat hogyan `send-request` használható a hasznos külső szolgáltatások integrálására a API Management szolgáltatáson keresztül áramló kérelmek és válaszok folyamatában.

## <a name="response-composition"></a>Válasz összetétele
A `send-request` házirend használható a háttérrendszer elsődleges kérelmének javítására, ahogy az előző példában is látható, vagy a háttérbeli hívás teljes lecseréléseként használható. Ezzel a technikával könnyedén létrehozhat összetett erőforrásokat, amelyek több különböző rendszerből vannak összesítve.

### <a name="building-a-dashboard"></a>Irányítópult létrehozása
Időnként előfordulhat, hogy ki szeretné próbálni a több háttérrendszer-rendszerben található információkat, például az irányítópultok elvezetését. A KPI-k minden különböző háttérből származnak, de érdemes nem közvetlen hozzáférést biztosítani számukra, és jó lenne, ha az összes információt egyetlen kérelemben lehet beolvasni. Előfordulhat, hogy a háttérbeli információk némelyikének szüksége van némi szeletelésre és Kockázás, és egy kis tisztításra van szükség. Az összetett erőforrás gyorsítótárazása hasznos lehet a háttérbeli terhelés csökkentése érdekében, mivel tudja, hogy a felhasználók az F5 billentyű lenyomásával megváltoztathatják, hogy az általuk végrehajtott mérőszámok megváltozhatnak-e.    

### <a name="faking-the-resource"></a>Az erőforrás szimulálása
Az irányítópult-erőforrás létrehozásának első lépése egy új művelet konfigurálása a Azure Portalban. Ez egy helyőrző művelet, amely egy összeállítási szabályzat konfigurálására szolgál a dinamikus erőforrás létrehozásához.

![Irányítópult-művelet](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>A kérések elkészítése
A művelet létrehozása után beállíthat egy házirendet kifejezetten ehhez a művelethez. 

![Képernyőfelvétel a szabályzat hatókörét megjelenítő képernyőről.](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Az első lépés a lekérdezési paraméterek kibontása a bejövő kérelemből, hogy továbbítsa azokat a háttérbe. Ebben a példában az irányítópult egy adott időtartamon alapuló információkat jelenít meg, ezért a `fromDate` és `toDate` paraméterrel rendelkezik. A `set-variable` szabályzat segítségével kibonthatja az adatokat a kérelem URL-címéből.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Miután megkapta ezeket az információkat, megteheti a kérelmeket az összes háttérrendszer-rendszerre. Mindegyik kérelem egy új URL-címet hoz létre a paraméter adataival, és meghívja a megfelelő kiszolgálót, és a választ egy környezeti változóban tárolja.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Ezek a kérések a sorrend szerint futnak, ami nem ideális. 

### <a name="responding"></a>Válaszol
Az összetett válasz létrehozásához használhatja a [Return-Response](./api-management-advanced-policies.md#ReturnResponse) házirendet. Az `set-body` elem egy kifejezés használatával új elemet hozhat létre a `JObject` tulajdonságokként beágyazott összes összetevő-ábrázolással.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

A teljes szabályzat a következőképpen néz ki:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

A helyőrző művelet konfigurációjában beállíthatja, hogy az irányítópult-erőforrás legalább egy órán át legyen gyorsítótárazva. 

## <a name="summary"></a>Összefoglalás
Az Azure API Management szolgáltatás rugalmas szabályzatokat biztosít, amelyek szelektíven alkalmazhatók a HTTP-forgalomra, és lehetővé teszik a háttér-szolgáltatások összeállítását. Az API-átjárót a riasztási funkciókkal, az ellenőrzéssel, az érvényesítési lehetőségekkel vagy az új összetett erőforrások több háttér-szolgáltatáson alapuló létrehozásával szeretné növelni, a `send-request` és a kapcsolódó szabályzatok pedig megnyitják a lehetőségeket.
