---
title: Kulcs átváltásának aláírása a Microsoft Identity platformon
description: Ez a cikk a Azure Active Directoryhoz tartozó, az aláírási kulcsra vonatkozó ajánlott eljárásokat ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: f3bf9ffeb8da8e877a27ab8758572b2c8277146d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090315"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Kulcs átváltásának aláírása a Microsoft Identity platformon
Ez a cikk azt ismerteti, hogy mit kell tudni a Microsoft Identity platform által a biztonsági jogkivonatok aláírásához használt nyilvános kulcsokról. Fontos megjegyezni, hogy ezek a kulcsok rendszeres időközönként, és vészhelyzetben azonnal összeállíthatók. A Microsoft Identity platformot használó összes alkalmazásnak képesnek kell lennie programozott módon kezelni a kulcs átváltási folyamatát. Folytassa az olvasással, hogy megtudja, hogyan működik a kulcsok működése, hogyan állapítható meg a rollover hatása az alkalmazásra, illetve hogyan lehet frissíteni az alkalmazást, vagy létre kell hozni egy rendszeres manuális átütemezési folyamatot a kulcsok átváltásához, ha szükséges.

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Az aláíró kulcsok áttekintése a Microsoft Identity platformon
A Microsoft Identity platform az iparági szabványokon alapuló, nyilvános kulcsú titkosítást használ a saját maga és az azt használó alkalmazások közötti megbízhatósági kapcsolat létrehozásához. A gyakorlatban ez a következő módon működik: a Microsoft Identity platform egy nyilvános és titkos kulcspárből álló aláíró kulcsot használ. Amikor a felhasználó bejelentkezik egy olyan alkalmazásba, amely a Microsoft Identity platformot használja a hitelesítéshez, a Microsoft Identity platform létrehoz egy biztonsági jogkivonatot, amely a felhasználóval kapcsolatos információkat tartalmaz. Ezt a tokent a Microsoft Identity platform írja alá a saját titkos kulcsának használatával, mielőtt visszaküldi az alkalmazásnak. Annak ellenőrzéséhez, hogy a jogkivonat érvényes-e, és hogy a Microsoft Identity platformból származik-e, az alkalmazásnak ellenőriznie kell a jogkivonat aláírását a bérlő [OpenID Connect Discovery-dokumentumában](https://openid.net/specs/openid-connect-discovery-1_0.html) vagy SAML/ws-fed [összevonási metaadat-dokumentumban](../azuread-dev/azure-ad-federation-metadata.md)található Microsoft Identity platform által közzétett nyilvános kulcsok használatával.

Biztonsági okokból a Microsoft Identity platformhoz tartozó aláíró kulcs rendszeres időközönként, és vészhelyzet esetén azonnal átgörgethető. Nincs beállított vagy garantált idő a legfontosabb tekercsek között – a Microsoft Identity platformmal integrálható bármely alkalmazást fel kell készülnie arra, hogy kezelni tudja a kulcsfontosságú átváltási eseményt, függetlenül attól, hogy milyen gyakran fordul elő. Ha ez nem történik meg, és az alkalmazás egy lejárt kulcsot próbál használni az aláírás ellenőrzéséhez egy jogkivonat esetében, a bejelentkezési kérelem sikertelen lesz.  A frissítések 24 óránkénti ellenőrzése az ajánlott eljárás, amelynek során a rendszer leszabályozza a kulcsfontosságú dokumentumok azonnali frissítését, ha a jogkivonat ismeretlen kulcs-azonosítóval van észlelve. 

Az OpenID Connect Discovery-dokumentumban és az összevonási metaadat-dokumentumban mindig több érvényes kulcs érhető el. Az alkalmazásnak fel kell készülnie a dokumentumban megadott összes kulcs használatára, mivel előfordulhat, hogy az egyik kulcs hamarosan fel van tekerve, a másik pedig a pótlása, és így tovább.  A megjelenő kulcsok száma a Microsoft Identity platform belső architektúrája alapján változhat, mivel új platformokat, új felhőket vagy új hitelesítési protokollokat támogat. Sem a kulcsok sorrendjét a JSON-válaszban, sem a kitett sorrendet a meaninful kell tekinteni az alkalmazásnak. 

Az olyan alkalmazások, amelyek csak egyetlen aláíró kulcsot támogatnak, vagy amelyek az aláíró kulcsok manuális frissítését igénylik, eleve kevésbé biztonságosak és megbízhatók.  Ezeket úgy kell frissíteni, hogy a [szabványos kódtárak](reference-v2-libraries.md) használatával biztosítsák, hogy mindig naprakész aláíró kulcsokat használjanak az ajánlott eljárások között. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Annak megállapítása, hogy az alkalmazás érintett-e, és mi a teendő
Az alkalmazás által kezelt kulcsok átállításának módja az olyan változóktól függ, mint az alkalmazás típusa vagy a használt azonosító protokoll és a könyvtár. Az alábbi szakaszokból megtudhatja, hogy a fő rollover befolyásolja-e az alkalmazások leggyakoribb típusait, és útmutatást nyújt arról, hogyan frissítheti az alkalmazást az automatikus rollover vagy a kulcs manuális frissítésének támogatásához.

* [Erőforrások eléréséhez használható natív ügyfélalkalmazások](#nativeclient)
* [Erőforrások eléréséhez használható webalkalmazások/API-k](#webclient)
* [Az Azure App Services használatával létrehozott webalkalmazások/API-k védik az erőforrásokat](#appservices)
* [Webalkalmazások/API-k az erőforrások védelme .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication middleware használatával](#owin)
* [Webalkalmazások/API-k az erőforrások védelme .NET Core OpenID Connect vagy JwtBearerAuthentication middleware használatával](#owincore)
* [Webalkalmazások/API-k az erőforrások védelme Node.js Passport használatával – Azure-ad modul](#passport)
* [A Visual Studio 2015-es vagy újabb verziójával létrehozott webalkalmazások és API-k az erőforrásokat védik](#vs2015)
* [Az erőforrásokat védő és a Visual Studio 2013-mel létrehozott webalkalmazások](#vs2013)
* Az erőforrásokat védő webes API-k és a Visual Studio 2013
* [Az erőforrásokat védő és a Visual Studio 2012-mel létrehozott webalkalmazások](#vs2012)
* [Az erőforrásokat védő és a Visual Studio 2010, 2008 o használatával létrehozott webes alkalmazások a Windows Identity Foundation használatával](#vs2010)
* [Webalkalmazások/API-k, amelyek bármely más kódtár használatával védik az erőforrásokat, vagy manuálisan implementálják a támogatott protokollokat](#other)

Ez az útmutató **nem** alkalmazható a következőkre:

* Az Azure AD Application Galleryből (az egyéni verzióval együtt) hozzáadott alkalmazások külön útmutatással rendelkeznek az aláíró kulcsok tekintetében. [További információ.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Az Application proxyn keresztül közzétett helyszíni alkalmazásoknak nem kell aggódniuk az aláíró kulcsok miatt.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Erőforrások eléréséhez használható natív ügyfélalkalmazások
Olyan alkalmazások, amelyek csak erőforrásokhoz férnek hozzá (azaz A Microsoft Graph, a kulcstartó, az Outlook API és más Microsoft API-k) általában csak jogkivonatot szereznek be, és az erőforrás-tulajdonoshoz adják át azt. Mivel nem biztosítanak semmilyen erőforrást, nem vizsgálja meg a jogkivonatot, ezért nincs szükség a megfelelő aláírására.

A natív ügyfélalkalmazások, függetlenül attól, hogy az asztali vagy mobil, ebbe a kategóriába tartoznak, ezért az átváltási művelet nem érinti.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Erőforrások eléréséhez használható webalkalmazások/API-k
Olyan alkalmazások, amelyek csak erőforrásokhoz férnek hozzá (azaz A Microsoft Graph, a kulcstartó, az Outlook API és más Microsoft API-k) általában csak jogkivonatot szereznek be, és az erőforrás-tulajdonoshoz adják át azt. Mivel nem biztosítanak semmilyen erőforrást, nem vizsgálja meg a jogkivonatot, ezért nincs szükség a megfelelő aláírására.

Ez a kategória olyan webalkalmazásokat és webes API-kat használ, amelyek az alkalmazáson belüli folyamatot (ügyfél-hitelesítő adatok/ügyféltanúsítvány) használják a jogkivonatok igénylésére, ezért az átváltási folyamat nem érinti.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Az Azure App Services használatával létrehozott webalkalmazások/API-k védik az erőforrásokat
Az Azure App Services hitelesítés/engedélyezés (EasyAuth) funkciója már rendelkezik a kulcs átváltásának automatikus kezeléséhez szükséges logikával.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webalkalmazások/API-k az erőforrások védelme .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication middleware használatával
Ha az alkalmazás a .NET OWIN OpenID Connect, WS-Fed vagy WindowsAzureActiveDirectoryBearerAuthentication middleware-t használja, akkor már rendelkezik a kulcs átváltásának automatikus kezelésére szolgáló szükséges logikával.

Az alkalmazás Startup.cs-vagy Startup.Auth.cs-fájljaiban az alábbi kódrészletek bármelyikének megkeresésével ellenőrizheti, hogy az alkalmazás használja-e ezeket.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webalkalmazások/API-k az erőforrások védelme .NET Core OpenID Connect vagy JwtBearerAuthentication middleware használatával
Ha az alkalmazás a .NET Core OWIN OpenID Connect vagy JwtBearerAuthentication middleware-t használja, akkor már rendelkezik a kulcs átváltásának automatikus kezelésére szolgáló szükséges logikával.

A következő kódrészletek bármelyikének megkeresésével ellenőrizheti, hogy az alkalmazás használja-e az alkalmazás Startup.cs vagy Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webalkalmazások/API-k az erőforrások védelme Node.js Passport használatával – Azure-ad modul
Ha az alkalmazás a Node.js Passport-ad modult használja, akkor már rendelkezik a kulcs átváltásának automatikus kezelésére szolgáló szükséges logikával.

A következő kódrészlet megkeresésével ellenőrizheti, hogy az alkalmazás Passport-ad-e az alkalmazás app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>A Visual Studio 2015-es vagy újabb verziójával létrehozott webalkalmazások és API-k az erőforrásokat védik
Ha az alkalmazás egy webalkalmazás-sablonnal lett létrehozva a Visual Studio 2015-as vagy újabb verziójában, és kiválasztotta a **munkahelyi vagy iskolai fiókokat** a **hitelesítés módosítása** menüből, akkor már rendelkezik a kulcs átváltásának automatikus kezeléséhez szükséges logikával. Ez a logika, amely az OpenID Connect-OWIN ágyazva van, beolvassa és gyorsítótárazza a kulcsokat az OpenID Connect Discovery-dokumentumból, és rendszeres időközönként frissíti őket.

Ha manuálisan adta meg a hitelesítést a megoldáshoz, előfordulhat, hogy az alkalmazás nem rendelkezik a szükséges kulcs-átváltási logikával. Saját magának kell megírnia, vagy követnie kell a [webalkalmazások/API-k lépéseit más könyvtárak használatával vagy a támogatott protokollok manuális megvalósításával](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Az erőforrásokat védő és a Visual Studio 2013-mel létrehozott webalkalmazások
Ha az alkalmazás egy webalkalmazás-sablonnal lett létrehozva a Visual Studio 2013-ben, és kiválasztotta a **szervezeti fiókokat** a **change Authentication (hitelesítés módosítása** ) menüből, akkor már rendelkezik a Key rollover automatikus kezeléséhez szükséges logikával. Ez a logika tárolja a szervezet egyedi azonosítóját és az aláíró kulcs információit a projekthez társított két adatbázis-táblában. A projekt Web.config fájljában található adatbázishoz tartozó kapcsolatok karakterlánca megtalálható.

Ha manuálisan adta meg a hitelesítést a megoldáshoz, előfordulhat, hogy az alkalmazás nem rendelkezik a szükséges kulcs-átváltási logikával. Saját magának kell megírnia, vagy követnie kell a [webalkalmazások/API-k lépéseit más könyvtárak használatával vagy a támogatott protokollok manuális megvalósításával.](#other)

A következő lépések segítségével ellenőrizheti, hogy a logika megfelelően működik-e az alkalmazásban.

1. A Visual Studio 2013-ben nyissa meg a megoldást, majd a jobb oldali ablakban kattintson a **Server Explorer** fülre.
2. Bontsa ki **az adatkapcsolatok**, a **DefaultConnection**, majd a **táblák** elemet. Keresse meg a **IssuingAuthorityKeys** táblázatot, kattintson rá a jobb gombbal, majd kattintson a **táblázat adatai megjelenítése** elemre.
3. A **IssuingAuthorityKeys** táblában legalább egy sor jelenik meg, amely a kulcs ujjlenyomat-értékének felel meg. Törölje a tábla bármely sorát.
4. Kattintson a jobb gombbal a **bérlők** táblára, majd kattintson a **tábla adatai megjelenítése** elemre.
5. A **bérlők** táblában legalább egy sor lesz, amely egy egyedi címtár-bérlői azonosítónak felel meg. Törölje a tábla bármely sorát. Ha nem törli a **bérlők** tábla és a **IssuingAuthorityKeys** tábla sorait, a rendszer futásidőben hibaüzenetet kap.
6. Hozza létre és futtassa az alkalmazást. Miután bejelentkezett a fiókjába, leállíthatja az alkalmazást.
7. Térjen vissza a **Server Explorerrel** , és tekintse meg a **IssuingAuthorityKeys** és a **bérlők** tábla értékeit. Megfigyelheti, hogy automatikusan újra lettek-e feltöltve a megfelelő információkkal az összevonási metaadat-dokumentumból.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Az erőforrásokat védő webes API-k és a Visual Studio 2013
Ha webes API-alkalmazást hozott létre a Visual Studio 2013-ben a webes API-sablonnal, majd a **hitelesítés módosítása** menüből kiválasztotta a **szervezeti fiókok** lehetőséget, akkor már rendelkezik a szükséges logikával az alkalmazásban.

Ha a hitelesítést manuálisan konfigurálta, kövesse az alábbi utasításokat, amelyből megtudhatja, hogyan konfigurálhatja a webes API-t a legfontosabb információk automatikus frissítéséhez.

A következő kódrészlet bemutatja, hogyan kérheti le a legújabb kulcsokat az összevonási metaadat-dokumentumból, majd az [JWT jogkivonat-kezelővel](/previous-versions/dotnet/framework/security/json-web-token-handler) érvényesítheti a jogkivonatot. A kódrészlet feltételezi, hogy saját gyorsítótárazási mechanizmust használ a kulcs megőrzéséhez a Microsoft Identity platform jövőbeli jogkivonatának érvényesítéséhez, függetlenül attól, hogy az adatbázis, a konfigurációs fájl vagy máshol található.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Az erőforrásokat védő és a Visual Studio 2012-mel létrehozott webalkalmazások
Ha az alkalmazása a Visual Studio 2012-ben készült, valószínűleg az identitás-és elérési eszközt használta az alkalmazás konfigurálásához. Az is valószínű, hogy a [kibocsátói név érvényesítése (VINR) bejegyzést](/previous-versions/dotnet/framework/security/validating-issuer-name-registry)használja. A VINR feladata a megbízható identitás-szolgáltatók (Microsoft Identity platform) adatainak és az általuk kiállított jogkivonatok ellenőrzéséhez használt kulcsok fenntartásáért felelős. A VINR emellett megkönnyíti a Web.config fájlban tárolt kulcsok automatikus frissítését a címtárhoz társított legújabb összevonási metaadat-dokumentum letöltésével, amely ellenőrzi, hogy a konfiguráció elavult-e a legújabb dokumentummal, és szükség szerint frissítse az alkalmazást az új kulcs használatára.

Ha az alkalmazást a Microsoft által biztosított bármely kód-minta vagy bemutató-dokumentáció használatával hozta létre, akkor a projektben már szerepel a kulcs átváltási logikája. Megfigyelheti, hogy az alábbi kód már létezik a projektben. Ha az alkalmazás még nem rendelkezik ezzel a logikával, kövesse az alábbi lépéseket a hozzáadásához és annak ellenőrzéséhez, hogy megfelelően működik-e.

1. A **Megoldáskezelőban** adjon hozzá egy hivatkozást a **System. IdentityModel** szerelvényhez a megfelelő projekthez.
2. Nyissa meg a **Global.asax.cs** fájlt, és adja hozzá a következő utasításokat a használatával:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adja hozzá a következő metódust a **Global.asax.cs** -fájlhoz:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Hívja meg a **RefreshValidationSettings ()** metódust a Global.asax.cs **()** metódusban Application_Start a  -ben az alábbiak szerint:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Ha követte ezeket a lépéseket, az alkalmazás Web.config frissülni fog az összevonási metaadatokat tartalmazó dokumentum legfrissebb adataival, beleértve a legújabb kulcsokat is. Ez a frissítés minden alkalommal bekövetkezik, amikor az alkalmazáskészlet újraindul az IIS-ben. Alapértelmezés szerint az IIS 29 óránként úgy van beállítva, hogy újrahasznosítsa az alkalmazásokat.

Kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a kulcs átváltási logikája működik-e.

1. Miután meggyőződött róla, hogy az alkalmazás a fenti kódot használja, nyissa meg a **Web.config** fájlt, és navigáljon a **\<issuerNameRegistry>** blokkhoz, amely kifejezetten a következő néhány sort keresi:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. A **\<add thumbprint="">** beállításban módosítsa az ujjlenyomat értékét úgy, hogy az egyik karaktert egy másikra cseréli. Mentse a **Web.config** fájlt.
3. Hozza létre az alkalmazást, majd futtassa. Ha elvégezheti a bejelentkezési folyamatot, az alkalmazás sikeresen frissíti a kulcsot, ha letölti a szükséges információkat a címtár összevonási metaadat-dokumentumból. Ha problémák merülnek fel, ellenőrizze, hogy helyesek-e az alkalmazás változásai, ha a [Microsoft Identity platform használatával beolvassa a Sign-On hozzáadása a webalkalmazáshoz](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) című cikket, vagy letölti és megvizsgálja a következő mintakód-mintát: [több-bérlős felhőalapú alkalmazás a Azure Active Directoryhoz](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Az erőforrásokat védő webalkalmazások és a Visual Studio 2008 vagy 2010 és a Windows Identity Foundation (WIF) 1.0-s verziójának használata a .NET 3,5-hoz
Ha létrehoz egy alkalmazást a WIF 1.0-s verziójában, nincs megadva olyan mechanizmus, amely automatikusan frissíti az alkalmazás konfigurációját egy új kulcs használatára.

* *Legegyszerűbb módszer* Használja a WIF SDK-ban található FedUtil-eszközt, amely beolvashatja a legfrissebb metaadat-dokumentumot, és frissítheti a konfigurációt.
* Frissítse alkalmazását a .NET 4,5-es verzióra, amely tartalmazza a System névtérben található WIF legújabb verzióját. Ezután használhatja az [érvényesítési kiállítói név beállításjegyzékét (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) az alkalmazás konfigurációjának automatikus frissítéséhez.
* Hajtson végre manuális átváltást az útmutató dokumentum végén található utasítások alapján.

Útmutató a FedUtil a konfiguráció frissítéséhez való használatához:

1. Ellenőrizze, hogy telepítve van-e a WIF v 1.0 SDK a Visual Studio 2008-es vagy 2010-es verziójának fejlesztői gépére. [Innen letöltheti](https://www.softpedia.com/get/Programming/Other-Programming-Files/Windows-Identity-Foundation-SDK.shtml) innen, ha még nem telepítette.
2. A Visual Studióban nyissa meg a megoldást, majd kattintson a jobb gombbal a megfelelő projektre, és válassza az **összevonási metaadatok frissítése** lehetőséget. Ha ez a beállítás nem érhető el, a FedUtil és/vagy a WIF v 1.0 SDK nincs telepítve.
3. A parancssorból válassza a **frissítés** lehetőséget az összevonási metaadatok frissítésének megkezdéséhez. Ha rendelkezik hozzáféréssel az alkalmazást futtató kiszolgálói környezethez, igény szerint használhatja a FedUtil [automatikus metaadat-frissítési ütemező](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))eszközét is.
4. A frissítési folyamat befejezéséhez kattintson a **Befejezés** gombra.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webalkalmazások/API-k, amelyek bármely más kódtár használatával védik az erőforrásokat, vagy manuálisan implementálják a támogatott protokollokat
Ha más függvénytárat használ, vagy a támogatott protokollok bármelyikét manuálisan implementálja, akkor ellenőriznie kell a könyvtárat vagy a megvalósítást, hogy a kulcs beolvasása az OpenID Connect Discovery-dokumentumból vagy az összevonási metaadat-dokumentumból történjen. Ennek egyik módja, ha a kód vagy a könyvtár kódja alapján keres meg egy, az OpenID-felderítési dokumentumba vagy az összevonási metaadat-dokumentumba irányuló hívásokat.

Ha a kulcs tárolása valahol vagy hardcoded történik az alkalmazásban, manuálisan is lekérheti a kulcsot, és ennek megfelelően frissítheti azt, ha az útmutatást tartalmazó dokumentum végén található utasítások alapján manuális átváltást végez. **Nyomatékosan javasoljuk, hogy az alkalmazás** a jelen cikk bármely megközelítési vázlatának használatával támogassa az automatikus átváltást, hogy elkerülje a jövőbeli fennakadásokat és a terhelést, ha a Microsoft Identity platform növeli az átváltási ritmusát, vagy vészhelyzeti sávon kívüli rollovert használ.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Az alkalmazás tesztelése annak megállapítására, hogy az érintett lesz-e
Ellenőrizheti, hogy az alkalmazás támogatja-e az automatikus kulcsok átváltását a parancsfájlok letöltésével és a [GitHub-tárházban](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) található utasítások követésével.

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Manuális váltás végrehajtása, ha az alkalmazás nem támogatja az automatikus átváltást
Ha az alkalmazás **nem** támogatja az automatikus átváltást, akkor létre kell hoznia egy folyamatot, amely rendszeres időközönként figyeli a Microsoft Identity platform aláíró kulcsait, és ennek megfelelően manuális átváltást végez. [Ez a GitHub-adattár](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) parancsfájlokat és útmutatást tartalmaz.