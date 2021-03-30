---
title: Egyéni gyorsítótárazás az Azure API Managementben
description: Megtudhatja, hogyan gyorsítótárazhatja az elemeket kulcs szerint az Azure API Managementban. A kulcsot a kérelem fejlécek használatával módosíthatja.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cf9901b4e49460dd2fb91dceaf239571058c5284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88213324"
---
# <a name="custom-caching-in-azure-api-management"></a>Egyéni gyorsítótárazás az Azure API Managementben
Az Azure API Management Service beépített támogatást nyújt a [http-válaszok gyorsítótárazásához](api-management-howto-cache.md) az erőforrás URL-címe alapján, kulcsként. A kulcs a tulajdonságok használatával módosítható a kérelem fejlécében `vary-by` . Ez a teljes HTTP-válaszok (más néven képviseletek) gyorsítótárazására hasznos, de néha hasznos lehet egy ábrázolás egy részének gyorsítótárazására. Az új [cache-lookup-Value](./api-management-caching-policies.md#GetFromCacheByKey) és [cache-Store-Value](./api-management-caching-policies.md#StoreToCacheByKey) szabályzatok lehetővé teszik tetszőleges adatmennyiségek tárolását és lekérését a házirend-definíciók között. Ez a funkció a korábban bevezetett [küldési kérelmekre](./api-management-advanced-policies.md#SendRequest) vonatkozó szabályzat értékét is hozzáadja, mivel mostantól a külső szolgáltatásoktól származó válaszokat is gyorsítótárazhatja.

## <a name="architecture"></a>Architektúra
API Management a szolgáltatás egy megosztott, bérlői adatgyorsítótárat használ, így ha több egységre is kiterjed, továbbra is hozzáférhet ugyanahhoz a gyorsítótárazott információhoz. Ha azonban többrégiós telepítéssel dolgozik, az egyes régiókban külön gyorsítótárak találhatók. Fontos, hogy a gyorsítótárat ne kezelje adattárként, mert az egyetlen adat forrása. Ha ezt tette, és később úgy döntött, hogy kihasználja a többrégiós telepítést, akkor az utazást használó felhasználók nem férhetnek hozzá a gyorsítótárazott adathoz.

## <a name="fragment-caching"></a>Töredékes gyorsítótárazás
Bizonyos esetekben előfordulhat, hogy a visszaadott válaszok tartalmaznak néhány olyan adatrészletet, amely költséges a meghatározáshoz, és még mindig friss marad az ésszerű időtartamra. Tegyük fel például, hogy egy légitársaság által épített szolgáltatás, amely a repülési foglalásokkal, a repülési állapottal és egyéb információkkal kapcsolatos információkat biztosít. Ha a felhasználó a Airlines Points program tagja, akkor a jelenlegi állapotával és a halmozott kilométerekkel kapcsolatos információkkal is rendelkezhetnek. Előfordulhat, hogy a felhasználóval kapcsolatos adatokat egy másik rendszer tárolja, de érdemes lehet a repülési állapottal és a foglalásokkal kapcsolatban visszaadott válaszokba foglalni. Ezt a kódrészletek gyorsítótárazása nevű folyamat használatával teheti meg. Az elsődleges ábrázolást a forrás-kiszolgáló valamilyen token használatával lehet visszaadni, amely jelzi, hogy a felhasználóval kapcsolatos adatokat be kell szúrni. 

Vegye figyelembe a következő JSON-választ egy háttér-API-ból.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

És a másodlagos erőforrás, `/userprofile/{userid}` amely a következőképpen néz ki:

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

A megfelelő felhasználói adatok meghatározásához API Managementnek azonosítania kell, hogy kik a végfelhasználók. Ez a mechanizmus a megvalósítástól függ. Példaként `Subject` egy jogkivonat jogcímet használok `JWT` . 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

A API Management az `enduserid` értéket egy környezeti változóban tárolja későbbi használatra. A következő lépés annak megállapítása, hogy egy korábbi kérelem már beolvasta-e a felhasználói adatokat, és azt a gyorsítótárban tárolta. Ehhez API Management a `cache-lookup-value` szabályzatot használja.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Ha nincs olyan bejegyzés a gyorsítótárban, amely megfelel a kulcs értékének, akkor a `userprofile` rendszer nem hoz létre környezeti változót. API Management ellenőrzi a keresés sikerességét a `choose` vezérlési folyamat házirendjének használatával.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Ha a `userprofile` környezeti változó nem létezik, akkor API Management meg kell tennie egy HTTP-kérelmet a lekéréséhez.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management a használatával hozza `enduserid` létre a felhasználói profil erőforrás URL-címét. Miután API Management a választ, lekéri a törzs szövegét a válaszból, és visszatárolja azt egy környezeti változóban.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Ha el szeretné kerülni, hogy API Management a HTTP-kérelem újbóli létrehozásakor, amikor ugyanaz a felhasználó egy másik kérést is kezdeményez, megadhatja, hogy a felhasználói profilt a gyorsítótárban tárolja.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management a gyorsítótárban lévő értéket a pontosan megegyező kulccsal tárolja, amely API Management eredetileg megkísérelte a beolvasását. Az érték tárolásának időtartamát az API Management határozza meg, hogy az adatok milyen gyakran változnak, és hogyan használják a toleráns felhasználók az elavult információkat. 

Fontos tisztában lenni azzal, hogy a gyorsítótárból való beolvasás továbbra is folyamaton kívüli, hálózati kérelem, és potenciálisan akár több mint ezredmásodpercet is hozzáadhat a kéréshez. A felhasználói profil adatainak meghatározásakor a rendszer a következő előnyökkel jár, mert az adatbázis-lekérdezéseket vagy az összesített információkat több háttérből kell végrehajtania.

A folyamat utolsó lépéseként frissíteni kell a visszaadott választ a felhasználói profil adataival.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Úgy is kiválaszthatja, hogy a token részeként belefoglalja az idézőjeleket, így még akkor is, ha a csere nem következik be, a válasz még mindig érvényes JSON.  

Ha összekapcsolja ezeket a lépéseket, a végeredmény egy olyan házirend, amely a következőhöz hasonlóan néz ki.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Ez a gyorsítótárazási módszer elsősorban olyan webhelyeken használatos, ahol a HTML a kiszolgálóoldali oldalon található, így egyetlen oldalként jeleníthető meg. Olyan API-k esetében is hasznos lehet, amelyekben az ügyfelek nem tudják az ügyféloldali HTTP-gyorsítótárazást végezni, vagy ha azt szeretné, hogy az ügyfél ne tegye ezt a feladatot.

Ugyanez a töredék-gyorsítótárazás is végezhető a háttér-webkiszolgálókon egy Redis gyorsítótárazási kiszolgáló használatával, azonban a API Management szolgáltatás használatával végezheti el ezt a munkát, ha a gyorsítótárazott töredékek az elsődleges válaszoktól eltérő háttér-végpontokból érkeznek.

## <a name="transparent-versioning"></a>Transzparens verziószámozás
Általános gyakorlat, hogy egy API több különböző implementációs verzióját egyszerre kell támogatni. Például a különböző környezetek (fejlesztési, tesztelési, üzemi stb.) támogatásához vagy az API régebbi verzióinak támogatásához, hogy időt adjon az API-fogyasztók számára az újabb verzióra való áttérésre. 

Az egyik módszer a kezeléshez, ahelyett, hogy az ügyfél-fejlesztőknek meg kellene változtatniuk az URL-címekről a-re, `/v1/customers` `/v2/customers` hogy a fogyasztó profiljában tárolt adatokban tárolják az API-t, és a megfelelő háttérbeli URL-címet hívják meg. Ha meg szeretné határozni, hogy egy adott ügyfélnek milyen helyes háttér-URL-címet kell hívnia, néhány konfigurációs adatot le kell kérdezni. Ennek a konfigurációs adatnak a gyorsítótárazásával API Management csökkentheti a keresési teljesítményre vonatkozó bírságot.

Az első lépés a kívánt verzió konfigurálásához használt azonosító meghatározása. Ebben a példában úgy döntöttem, hogy hozzárendelem a verziót a termék-előfizetési kulcshoz. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management a gyorsítótár-keresési funkció segítségével megtekintheti, hogy már beolvasta-e a kívánt ügyfél-verziót.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Ezután API Management ellenőrzi, hogy nem találta-e meg a gyorsítótárban.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Ha API Management nem találta meg, API Management lekéri.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Bontsa ki a válasz törzsének szövegét a válaszból.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

A későbbi használat érdekében tárolja vissza a gyorsítótárban.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Végül frissítse a háttérbeli URL-címet, és válassza ki az ügyfél által kívánt szolgáltatás verzióját.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

A teljes szabályzat a következő:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Az API-felhasználók számára lehetővé teszi, hogy az ügyfelek frissítése és újbóli üzembe helyezése nélkül is átlátható módon szabályozzák, melyik háttér-verziót szeretné elérni az ügyfeleknek, és ez egy olyan elegáns megoldás, amely számos API-verzióra vonatkozik.

## <a name="tenant-isolation"></a>Bérlő elkülönítése
Nagyobb méretű, több-bérlős üzembe helyezések esetén egyes vállalatok külön csoportokat hoznak létre a különböző, háttérbeli hardveren üzemelő példányokon. Ez lekicsinyíti a háttérbeli hardveres problémák által érintett ügyfelek számát. Azt is lehetővé teszi, hogy az új szoftververziók fokozatosan legyenek kivezetve. Ideális esetben ennek a háttér-architektúrának transzparensnek kell lennie az API-felhasználók számára. Ez az átlátszó verziószámozáshoz hasonló módon érhető el, mert a háttér-URL-cím API-kulcson alapuló konfigurációs állapottal történő manipulálására szolgáló módszeren alapul.  

Ahelyett, hogy az API előnyben részesített verzióját adja vissza az egyes előfizetési kulcsokhoz, olyan azonosítót ad vissza, amely egy bérlőhöz kapcsolódik a hozzárendelt hardver csoporthoz. Ez az azonosító használható a megfelelő háttérbeli URL-cím létrehozásához.

## <a name="summary"></a>Összefoglalás
Az Azure API Management cache bármilyen típusú adat tárolására való használatának szabadsága lehetővé teszi a konfigurációs adathoz való hatékony hozzáférést, amely hatással lehet a bejövő kérelmek feldolgozásának módjára. Emellett olyan adattöredékek tárolására is használható, amelyek a háttérben futó API által visszaadott válaszokat is kiterjeszthetik.
