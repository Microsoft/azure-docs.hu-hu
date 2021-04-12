---
title: Az Azure bejárati ajtó standard/prémium szabálykészlet műveleteinek beállítása
description: Ez a cikk felsorolja az Azure bejárati szabályainak készletével végrehajtható különböző műveleteket.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064751"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) szabálykészlet műveletei

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtó standard/prémium [szabálykészlet](concept-rule-set.md) olyan szabályokból áll, amelyek egyezési feltételekkel és műveletekkel rendelkeznek. Ez a cikk részletesen ismerteti az Azure bejárati ajtó standard/prémium szabálykészlet-készletében használható műveleteket. A művelet meghatározza azt a viselkedést, amelyet a rendszer a megfelelési feltétel (ek) azonosítására szolgáló kérelem típusára alkalmaz. Egy Azure bejárati ajtó (standard/prémium) szabálykészlet esetében egy szabály legfeljebb öt műveletet tartalmazhat.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A következő műveletek használhatók az Azure-beli előtérben beállított szabálykészlet számára.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Gyorsítótár lejárata

A **gyorsítótár lejárati** műveletével felülírhatja a végpont élettartam (TTL) értékét azon kérelmek esetében, amelyekre a szabályok megfelelnek a feltételeknek.

> [!NOTE]
> Előfordulhat, hogy a származási hely nem gyorsítótárazza a megadott válaszokat a `Cache-Control` , vagy értékkel rendelkező fejléc használatával `no-cache` `private` `no-store` . Ilyen esetben a bejárati ajtó soha nem fogja gyorsítótárazni a tartalmat, és ez a művelet nem lesz hatással.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-------|------------------|
| Gyorsítótár viselkedése | <ul><li>**Gyorsítótár megkerülése:** A tartalmat nem szabad gyorsítótárazni. Az ARM-sablonok területen állítsa a tulajdonságot a következőre: `cacheBehavior` `BypassCache` .</li><li>**Felülbírálás:** A rendszer felülírja a forrásból visszaadott TTL-értéket a műveletben megadott értékkel. Ez a viselkedés csak akkor lesz alkalmazva, ha a válasz gyorsítótárazható. Az ARM-sablonok területen állítsa a tulajdonságot a következőre: `cacheBehavior` `Override` .</li><li>**Ha hiányzik, állítsa be:** Ha a forrástól nem érkezik TTL-érték, a szabály az ÉLETTARTAMot a műveletben megadott értékre állítja be. Ez a viselkedés csak akkor lesz alkalmazva, ha a válasz gyorsítótárazható. Az ARM-sablonok területen állítsa a tulajdonságot a következőre: `cacheBehavior` `SetIfMissing` .</li></ul> |
| Gyorsítótár időtartama | Ha a _gyorsítótár viselkedése_ a vagy a értékre van állítva `Override` , akkor `Set if missing` ezeknek a mezőknek a használandó gyorsítótár időtartamát kell megadniuk. A maximális időtartam 366 nap.<ul><li>A Azure Portalban: határozza meg a napokat, az órákat, a perceket és a másodperceket.</li><li>Az ARM-sablonokban: az időtartamot a formátumban kell megadni `d.hh:mm:ss` . |

### <a name="example"></a>Példa

Ebben a példában a gyorsítótár lejárati ideje 6 órára van felülbírálva, a megegyező kérelmek esetében, amelyek nem határozzák meg a gyorsítótár időtartamát.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="A gyorsítótár lejárati műveletét bemutató portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Gyorsítótár-kulcs lekérdezési karakterlánca

A gyorsítótár **kulcs lekérdezési karakterlánca** művelettel módosíthatja a gyorsítótár-kulcsot a lekérdezési karakterláncok alapján. A gyorsítótár kulcsa a bevezető ajtó a gyorsítótárra vonatkozó egyedi kérések azonosítására szolgál.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-------|------------------|
| Működés | <ul><li>**Többek között:** A paraméterekben megadott lekérdezési karakterláncok a gyorsítótár kulcsának generálásakor szerepelnek. Az ARM-sablonok területen állítsa a tulajdonságot a következőre: `queryStringBehavior` `Include` .</li><li>**Gyorsítótár minden egyedi URL-címe:** Mindegyik egyedi URL-címnek saját gyorsítótár-kulcsa van. Az ARM-sablonokban használja a-t `queryStringBehavior` `IncludeAll` .</li><li>**Kizárás:** A paraméterekben megadott lekérdezési karakterláncok kimaradnak a gyorsítótár-kulcs generálása után. Az ARM-sablonok területen állítsa a tulajdonságot a következőre: `queryStringBehavior` `Exclude` .</li><li>**Lekérdezési karakterláncok figyelmen kívül hagyása:** A lekérdezési karakterláncok nem tekintendők a gyorsítótárbeli kulcs generálásakor. Az ARM-sablonok területen állítsa a tulajdonságot a következőre: `queryStringBehavior` `ExcludeAll` .</li></ul>  |
| Paraméterek | A lekérdezési karakterlánc-paraméterek neveinek listája, vesszővel elválasztva. |

### <a name="example"></a>Példa

Ebben a példában a gyorsítótár-kulcsot úgy módosítjuk, hogy tartalmazza a nevű lekérdezési karakterlánc paraméterét `customerId` .

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="A gyorsítótár-kulcs lekérdezési karakterláncának műveletét bemutató portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Kérelem fejlécének módosítása

A kérelem **fejlécének módosítása** művelettel módosíthatja a kérésben szereplő fejléceket, amikor a rendszer elküldi a forrásnak.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-------|------------------|
| Operátor | <ul><li>**Hozzáfűzés:** A megadott fejléc a megadott értékkel lesz hozzáadva a kéréshez. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő fejléc értékéhez a karakterlánc-Összefűzés használatával. Nincsenek felvéve határolójelek. Az ARM-sablonokban használja a-t `headerAction` `Append` .</li><li>**Felülírás:** A megadott fejléc a megadott értékkel lesz hozzáadva a kéréshez. Ha a fejléc már létezik, a megadott érték felülírja a meglévő értéket. Az ARM-sablonokban használja a-t `headerAction` `Overwrite` .</li><li>**Törlés:** Ha a szabályban megadott fejléc szerepel, a rendszer törli a fejlécet a kérelemből. Az ARM-sablonokban használja a-t `headerAction` `Delete` .</li></ul> |
| Fejléc neve | A módosítandó fejléc neve. |
| Fejléc értéke | A hozzáfűzni vagy felülírni kívánt érték. |

### <a name="example"></a>Példa

Ebben a példában az értéket `AdditionalValue` a `MyRequestHeader` kérelem fejlécébe fűzi. Ha a forrás a válasz fejlécét értékre állítja `ValueSetByClient` , akkor a művelet alkalmazása után a kérelem fejlécének értéke a következő lesz: `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="A módosítási kérelem fejlécét ábrázoló portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Válasz fejlécének módosítása

A válasz- **fejléc módosítása** művelettel módosíthatja azokat a fejléceket, amelyek a válaszokban jelennek meg, mielőtt visszatérnek az ügyfeleknek.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|-------|------------------|
| Operátor | <ul><li>**Hozzáfűzés:** A megadott fejléc hozzá lesz adva a válaszhoz a megadott értékkel. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő fejléc értékéhez a karakterlánc-Összefűzés használatával. Nincsenek felvéve határolójelek. Az ARM-sablonokban használja a-t `headerAction` `Append` .</li><li>**Felülírás:** A megadott fejléc hozzá lesz adva a válaszhoz a megadott értékkel. Ha a fejléc már létezik, a megadott érték felülírja a meglévő értéket. Az ARM-sablonokban használja a-t `headerAction` `Overwrite` .</li><li>**Törlés:** Ha a szabályban megadott fejléc jelen van, a rendszer törli a fejlécet a válaszból.  Az ARM-sablonokban használja a-t `headerAction` `Delete` .</li></ul> |
| Fejléc neve | A módosítandó fejléc neve. |
| Fejléc értéke | A hozzáfűzni vagy felülírni kívánt érték. |

### <a name="example"></a>Példa

Ebben a példában a válaszban szereplő névvel töröljük a fejlécet, `X-Powered-By` mielőtt azok visszakerülnek az ügyfélnek.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Az a portál képernyőképe, amely a válasz fejlécének módosítása műveletet mutatja.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> URL-átirányítás

Az **URL-átirányítás** művelet használatával átirányíthatja az ügyfeleket egy új URL-címre. Az ügyfelek átirányítási választ kapnak az előtérben.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|----------|------------------|
| Átirányítás típusa | A kérelmezőnek visszaadni kívánt válasz típusa. <ul><li>A Azure Portal: found (302), mozgott (301), ideiglenes átirányítás (307), állandó átirányítás (308).</li><li>ARM-sablonokban: `Found` ,, `Moved` `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Átirányítási protokoll | <ul><li>A Azure Portal: `Match Request` , `HTTP` , `HTTPS`</li><li>Az ARM-sablonokban: `MatchRequest` , `Http` , `Https`</li></ul> |
| Cél gazdagép | Annak az állomásnévnek a neve, amelyre át szeretné irányítani a kérést. Hagyja üresen a bejövő gazdagép megőrzését. |
| Cél elérési útja | Az átirányítás során használandó elérési út. Adja meg a sortávolságot `/` . Hagyja üresen a bejövő elérési út megőrzését. |
| Lekérdezési sztring | Az átirányítás során használt lekérdezési karakterlánc. Ne adja meg a sortávolságot `?` . Hagyja üresen a bejövő lekérdezési karakterlánc megőrzését. |
| Cél töredéke | Az átirányítás során használandó töredék. Hagyja üresen a bejövő töredék megőrzése érdekében. |

### <a name="example"></a>Példa

Ebben a példában átirányítjuk a kérést a `https://contoso.com/exampleredirection?clientIp={client_ip}` -ra, miközben megőrzi a töredéket. HTTP ideiglenes átirányítás (307) van használatban. Az ügyfél IP-címét a `{client_ip}` `client_ip` [rendszer a kiszolgálói változó](#server-variables)használatával használja az URL-címen található jogkivonat helyén.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Az URL-átirányítási műveletet bemutató portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> URL-újraírás

Az **URL-cím újraírása** művelettel átírhatja egy olyan kérelem elérési útját, amely az Ön által használt úton van.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság | Támogatott értékek |
|----------|------------------|
| Forrás mintája | Adja meg a forrás mintát a lecserélni kívánt URL-útvonalon. Jelenleg a forrás minta előtag-alapú egyezést használ. Az összes URL-cím eléréséhez használjon egy perjelet ( `/` ) a forrás minta értékének megfelelően. |
| Cél | Adja meg az újraíráshoz használandó célhely elérési útját. A cél elérési útja felülírja a forrás mintát. |
| Páratlan elérési út megőrzése | Ha az _Igen_ értékre van állítva, a rendszer a forrás minta után hátralévő elérési utat hozzáfűzi az új cél elérési úthoz. |

### <a name="example"></a>Példa

Ebben a példában az összes kérést újraírta az elérési útra `/redirection` , és nem őrzi meg az elérési út hátralévő részét.

# <a name="portal"></a>[Portál](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Az URL-írási műveletet megjelenítő portál képernyőképe.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Kiszolgálói változók

A szabálykészlet kiszolgálói változói a kérelemmel kapcsolatos strukturált adatokhoz biztosítanak hozzáférést. A kiszolgálói változók használatával dinamikusan módosíthatja a kérelmek/válaszok fejléceit vagy URL-írási útvonalait/lekérdezési karakterláncait, például egy új oldal betöltését vagy egy űrlap közzétételét.

### <a name="supported-variables"></a>Támogatott változók

| Változó neve    | Leírás                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Az Azure-beli első ajtó széléhez való közvetlen kapcsolódás IP-címe. Ha az ügyfél egy HTTP-proxy vagy egy terheléselosztó használatával küldi el a kérést, a érték a `socket_ip` proxy vagy a terheléselosztó IP-címe.                                                                      |
| `client_ip`      | Annak az ügyfélnek az IP-címe, amely az eredeti kérelmet elvégezte. Ha `X-Forwarded-For` a kérelemben fejléc szerepel, akkor az ügyfél IP-címe a fejlécből lesz kiválasztva.                                                                                                               |
| `client_port`    | Az ügyfél IP-portja, amely a kérést elvégezte.                                                                                                                                                                                                                                          |
| `hostname`       | Az ügyféltől érkező kérelemben szereplő állomásnév.                                                                                                                                                                                                                                             |
| `geo_country`    | Azt jelzi, hogy a kérelmező országa/régiója az ország/régió kódja alapján van-e.                                                                                                                                                                                                       |
| `http_method`    | Az URL-kérelem elvégzéséhez használt metódus, például: `GET` vagy `POST` .                                                                                                                                                                                                                         |
| `http_version`   | A kérelem protokollja. Általában `HTTP/1.0` , `HTTP/1.1` vagy `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | A kért URL-cím "?" értékét követő változó/érték párok listája.<br />A kérelemben például `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` az érték a következő `query_string` lesz: `id=123&title=fabrikam` .                                                      |
| `request_scheme` | A kérelem sémája: `http` vagy `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | A teljes eredeti kérelem URI-ja (argumentumokkal).<br />A kérelemben például `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` az érték a következő `request_uri` lesz: `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Egy létesített TLS-kapcsolat protokollja.                                                                                                                                                                                                                                            |
| `server_port`    | A kérelmet fogadó kiszolgáló portja.                                                                                                                                                                                                                                           |
| `url_path`       | Annak a gazdagépnek az adott erőforrását azonosítja, amelyet a webes ügyfél szeretne elérni. Ez az argumentumok nélküli kérelem URI-ja.<br />A kérelemben például `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` az érték a következő `uri_path` lesz: `/article.aspx` . |

### <a name="server-variable-format"></a>Kiszolgálói változó formátuma    

A kiszolgálói változók a következő formátumok használatával adhatók meg:

* `{variable}`: A teljes kiszolgálói változó belefoglalása. Ha például az ügyfél IP-címe, `111.222.333.444` akkor a `{client_ip}` jogkivonat kiértékelése megtörténik `111.222.333.444` .
* `{variable:offset}`: Adja meg a kiszolgálói változót egy adott eltolás után, amíg a változó véget nem ér. Az eltolás nulla-alapú. Ha például az ügyfél IP-címe, `111.222.333.444` akkor a `{client_ip:3}` jogkivonat kiértékelése megtörténik `.222.333.444` .
* `{variable:offset:length}`: Adja meg a kiszolgálói változót egy adott eltolás után, a megadott hosszig. Az eltolás nulla-alapú. Ha például az ügyfél IP-címe, `111.222.333.444` akkor a `{client_ip:4:3}` jogkivonat kiértékelése megtörténik `222` .

### <a name="supported-actions"></a>Támogatott műveletek

A kiszolgálói változók a következő műveletekben támogatottak:

* Gyorsítótár-kulcs lekérdezési karakterlánca
* Kérelem fejlécének módosítása
* Válasz fejlécének módosítása
* URL-átirányítás
* URL-átírás

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure bejárati ajtó standard/prémium szabálykészlet beállításáról](concept-rule-set.md).
* További információ a [szabálykészlet egyeztetési feltételeiről](concept-rule-set-match-conditions.md).
