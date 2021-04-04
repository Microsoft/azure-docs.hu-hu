---
title: Kivételek kezelése – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Tudnivalók a kivételek kezeléséről a Threat Modeling Tool. Tekintse meg a kockázatcsökkentő információkat és a kód megjelenítése példákat.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517427"
---
# <a name="security-frame-exception-management--mitigations"></a>Biztonsági keret: kivételek kezelése | Enyhítését 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF – a konfigurációs fájlban ne szerepeljen a serviceDebug csomópont](#servicedebug)</li><li>[WCF – a konfigurációs fájlban ne szerepeljen a serviceMetadata csomópont](#servicemetadata)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a ASP.NET web API-ban a megfelelő kivételek kezelésére kerül sor.](#exception)</li></ul> |
| **Webalkalmazás** | <ul><li>[Ne tegye elérhetővé a biztonsági adatokat a hibaüzenetekben](#messages)</li><li>[Alapértelmezett hibakezelő lap implementálása](#default)</li><li>[Üzembe helyezési módszer beállítása a kiskereskedelmi környezetbe az IIS-ben](#deployment)</li><li>[A kivételek biztonságosan meghiúsulnak](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF – a konfigurációs fájlban ne szerepeljen a serviceDebug csomópont

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Lépések** | A Windows Communication Framework (WCF) szolgáltatások beállítható a hibakeresési információk megjelenítésére. A hibakeresési adatok nem használhatók éles környezetekben. A `<serviceDebug>` címke határozza meg, hogy a hibakeresési információ funkció engedélyezve van-e a WCF szolgáltatáshoz. Ha a includeExceptionDetailInFaults attribútum értéke TRUE (igaz), a rendszer az alkalmazásból származó kivételi adatokat adja vissza az ügyfeleknek. A támadók az alkalmazás által használt keretrendszerre, adatbázisra vagy más erőforrásokra irányuló támadásokhoz való csatlakozáshoz szükséges további információkat is kihasználhatják. |

### <a name="example"></a>Példa
A következő konfigurációs fájl tartalmazza a `<serviceDebug>` címkét: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Hibakeresési információk letiltása a szolgáltatásban. Ez a `<serviceDebug>` címke az alkalmazás konfigurációs fájljából való eltávolításával hajtható végre. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF – a konfigurációs fájlban ne szerepeljen a serviceMetadata csomópont

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Általános, NET-keretrendszer 3 |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Lépések** | Egy szolgáltatás nyilvánosan elérhetővé téve a támadók számára értékes képet kaphat a szolgáltatás kihasználásáról. A `<serviceMetadata>` címke engedélyezi a metaadatok közzétételi funkcióját. A szolgáltatás metaadatainak bizalmas adatokat tartalmazhatnak, amelyek nem lehetnek nyilvánosan elérhetők. Minimálisan csak a megbízható felhasználók számára engedélyezze a metaadatok elérését, és gondoskodjon arról, hogy a szükségtelen információk ne legyenek elérhetők. Még jobb, ha teljes mértékben letiltja a metaadatok közzétételének lehetőségét. A biztonságos WCF-konfiguráció nem tartalmazza a `<serviceMetadata>` címkét. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Győződjön meg arról, hogy a ASP.NET web API-ban a megfelelő kivételek kezelésére kerül sor.

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | MVC 5, MVC 6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Kivételek a ASP.net web API](https://www.asp.net/web-api/overview/error-handling/exception-handling)-ban, [a modell érvényesítése a ASP.net webes API-ban](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépések** | Alapértelmezés szerint a ASP.NET webes API legtöbb nem kezelt kivétele egy HTTP-válaszra van lefordítva, állapotkód `500, Internal Server Error`|

### <a name="example"></a>Példa
Az API által visszaadott állapotkód szabályozásához az `HttpResponseException` alábbi ábrákat használhatja: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Példa
A kivételre adott válasz további szabályozása érdekében az `HttpResponseMessage` osztály az alábbi módon használható: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
A nem a típustól származó nem kezelt kivételek észleléséhez használhat `HttpResponseException` kivételi szűrőket. A kivételt képező szűrők implementálják az `System.Web.Http.Filters.IExceptionFilter` illesztőfelületet. A kivételek szűrésének legegyszerűbb módja az `System.Web.Http.Filters.ExceptionFilterAttribute` osztályból származtatott és a OnException metódus felülbírálása. 

### <a name="example"></a>Példa
Itt látható egy szűrő, amely a `NotImplementedException` kivételeket a http-állapotkód szerint konvertálja `501, Not Implemented` : 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

A webes API-kivételek szűrőjét többféleképpen is regisztrálhatja:
- Művelet szerint
- Vezérlő szerint
- Globálisan

### <a name="example"></a>Példa
Ha a szűrőt egy adott műveletre szeretné alkalmazni, adja hozzá a szűrőt attribútumként a művelethez: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Példa
Ha a szűrőt az a összes műveletére alkalmazni szeretné `controller` , adja hozzá a szűrőt attribútumként a `controller` osztályhoz: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Példa
Ha globálisan szeretné alkalmazni a szűrőt az összes webes API-vezérlőre, vegye fel a szűrő egy példányát a `GlobalConfiguration.Configuration.Filters` gyűjteménybe. A gyűjteményben lévő kivételi szűrők a web API-vezérlő bármely műveletére érvényesek. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Példa
A modell érvényesítéséhez a modell állapota a CreateErrorResponse metódusnak adható át az alábbiak szerint: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

A ASP.NET web API kivételes kezelési és modell-ellenőrzésével kapcsolatos további részletekért olvassa el a hivatkozások szakaszban található hivatkozásokat. 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Ne tegye elérhetővé a biztonsági adatokat a hibaüzenetekben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Az általános hibaüzenetek közvetlenül a felhasználó számára érhetők el, anélkül, hogy bizalmas alkalmazásadatok is beletartoznak. A bizalmas adatokra például a következők tartoznak:</p><ul><li>Kiszolgálók nevei</li><li>Kapcsolati sztringek</li><li>Felhasználónevek</li><li>Jelszavak</li><li>SQL-eljárások</li><li>A dinamikus SQL-hibák részletei</li><li>Verem nyomkövetése és a kód sorai</li><li>Memóriában tárolt változók</li><li>Meghajtók és mappák helye</li><li>Alkalmazás telepítési pontjai</li><li>Gazdagép konfigurációs beállításai</li><li>Egyéb belső alkalmazás részletei</li></ul><p>Az alkalmazáson belüli hibák és általános hibaüzenetek megadásával, valamint az IIS-n belüli egyéni hibák elhárításával megelőzhető az adatokhoz való illetéktelen hozzáférés. SQL Server adatbázis-és .NET-kivételek kezelését, többek között az architektúrák kezelésére, különösen részletesek, és rendkívül hasznosak az alkalmazás rosszindulatú felhasználói profilkészítéséhez. Ne jelenítse meg közvetlenül a .NET-kivétel osztályból származtatott osztály tartalmát, és ügyeljen arra, hogy megfelelő kivételek legyenek, hogy egy váratlan kivételt ne közvetlenül a felhasználóhoz lehessen kiemelni.</p><ul><li>Adja meg az általános hibaüzeneteket közvetlenül a felhasználó számára, amely a kivétel/hiba üzenetben közvetlenül megtalálható a megadott adatokból.</li><li>A .NET-kivételi osztály tartalmának megjelenítése közvetlenül a felhasználó számára</li><li>Az összes hibaüzenet alátöltése, és ha szükséges, az alkalmazás ügyfelének küldött általános hibaüzeneten keresztül tájékoztatja a felhasználót</li><li>Ne tegye elérhetővé a kivétel osztály tartalmát közvetlenül a felhasználónak, különösen a visszatérési értéket `.ToString()` , vagy az üzenet vagy a StackTrace tulajdonságainak értékét. Biztonságosan naplózhatja ezeket az információkat, és egy ártalmatlan üzenetet jeleníthet meg a felhasználónak</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Alapértelmezett hibakezelő lap implementálása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az ASP.NET hibalapok beállításainak szerkesztése párbeszédablak](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Lépések** | <p>Ha egy ASP.NET-alkalmazás meghibásodik, és a HTTP/1. x 500 belső kiszolgálóhiba miatt vagy egy szolgáltatás konfigurációja (például a kérelmek szűrése) megakadályozza a lapok megjelenítését, hibaüzenetet fog generálni. A rendszergazdák megadhatják, hogy az alkalmazásnak barátságos üzenetet kell-e megjelenítenie az ügyfélnek, részletes hibaüzenetet küld az ügyfélnek, vagy részletes hibaüzenetet kap a localhost-ra. A `<customErrors>` web.config címkéjének három módja van:</p><ul><li>Ekkor **:** Megadja, hogy az egyéni hibák engedélyezve vannak. Ha nincs megadva defaultRedirect attribútum, a felhasználók általános hibát látnak. Az egyéni hibák a távoli ügyfelek és a helyi gazdagép számára jelennek meg</li><li>**Kikapcsolva:** Megadja, hogy az egyéni hibák le vannak tiltva. A részletes ASP.NET hibák a távoli ügyfelek és a helyi gazdagép számára jelennek meg</li><li>**RemoteOnly:** Megadja, hogy az egyéni hibák csak a távoli ügyfeleknél jelenjenek meg, és hogy a ASP.NET hibák a helyi gazdagépen jelennek meg. Ez az alapértelmezett érték</li></ul><p>Nyissa meg a `web.config` fájlt az alkalmazáshoz vagy a webhelyhez, és győződjön meg arról, hogy a címke vagy a `<customErrors mode="RemoteOnly" />` `<customErrors mode="On" />` definiálva van.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Üzembe helyezési módszer beállítása a kiskereskedelmi környezetbe az IIS-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [üzembe helyezési elem (ASP.NET-beállítási séma)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Lépések** | <p>A `<deployment retail>` kapcsoló üzemi IIS-kiszolgálók általi használatra készült. Ezzel a kapcsolóval az alkalmazások a lehető legjobb teljesítménnyel és a lehető legkevesebb biztonsági információval futnak, ha letiltja az alkalmazás nyomkövetési kimenetét egy oldalon, letiltva a részletes hibaüzenetek megjelenítését a végfelhasználók számára, és letiltja a hibakeresési kapcsolót.</p><p>Az aktív fejlesztés során az olyan gyakran használt kapcsolók és beállítások, amelyek fejlesztői fókuszban vannak, például a sikertelen kérelmek nyomon követése és a hibakeresés. Azt javasoljuk, hogy a telepítési módszer bármely üzemi kiszolgálón legyen a kiskereskedelmi értékre állítva. Nyissa meg a machine.config fájlt, és győződjön meg arról, hogy a `<deployment retail="true" />` változatlanul igaz értékre van állítva.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>A kivételek biztonságosan meghiúsulnak

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL-fázis**               | Létrehozás |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Biztonságos feladatátvétel](https://owasp.org/www-community/Fail_securely) |
| **Lépések** | Az alkalmazásnak biztonságosan kell működnie. Bármely olyan metódus, amely egy logikai értéket ad vissza, amely alapján bizonyos döntés születik, a kivételek blokkolását alaposan létre kell hozni. Sok logikai hiba történt, mivel a biztonsági problémák a bekúszik a alkalmazásban, amikor a kivételi blokkot gondatlanul írták.|

### <a name="example"></a>Példa
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
A fenti módszer mindig igaz értéket ad vissza, ha valamilyen kivétel történik. Ha a végfelhasználó helytelenül formázott URL-címet ad meg, a böngésző figyelembe veszi, de a `Uri()` konstruktor nem, ez kivételt jelez, és az áldozat az érvényes, de helytelenül formázott URL-címre kerül.