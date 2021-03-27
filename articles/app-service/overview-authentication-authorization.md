---
title: Hitelesítés és engedélyezés
description: Ismerje meg a Azure App Service és Azure Functions beépített hitelesítési és engedélyezési támogatását, valamint azt, hogy miként védheti meg alkalmazásait a jogosulatlan hozzáférés ellen.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612755"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Hitelesítés és engedélyezés Azure App Service és Azure Functions

A Azure App Service beépített hitelesítési és engedélyezési képességeket (más néven "egyszerű hitelesítés") biztosít, így a felhasználók bejelentkezhetnek, és az adatok eléréséhez a webalkalmazás, a REST API és a mobil háttérrendszer minimális vagy egyetlen kódja, valamint [Azure functions](../azure-functions/functions-overview.md)is beírhatók. Ez a cikk azt ismerteti, hogyan egyszerűsíthető a App Service az alkalmazás hitelesítésének és engedélyezésének egyszerűsítése.

## <a name="why-use-the-built-in-authentication"></a>Miért érdemes a beépített hitelesítést használni?

A szolgáltatás használata nem kötelező a hitelesítéshez és az engedélyezéshez. Használhatja a választott webes keretrendszer csomagban található biztonsági funkcióit, vagy megírhatja saját segédprogramjait is. Azonban biztosítania kell, hogy a megoldás naprakész maradjon a legújabb biztonsági, protokoll-és böngésző-frissítésekkel.

A biztonságos hitelesítési megoldás (bejelentkezési felhasználók) és az engedélyezés (a biztonságos adathozzáférés biztosítása) megvalósítása jelentős erőfeszítést vehet igénybe. Győződjön meg arról, hogy követi az iparági ajánlott eljárásokat és szabványokat, és naprakészen tartja a megvalósítást. A App Service és Azure Functions beépített hitelesítési funkciója időt és fáradságot takaríthat meg azáltal, hogy az összevont identitás-szolgáltatókkal való használatra kész hitelesítést biztosít, így az alkalmazás többi részén koncentrálhat.

- A Azure App Service lehetővé teszi a különböző hitelesítési funkciók integrálását a webalkalmazásba vagy API-ba anélkül, hogy saját maga is implementálja őket.
- A szolgáltatás közvetlenül a platformra épül, és nem igényel semmilyen konkrét nyelvet, SDK-t, biztonsági szakértelmet vagy akár bármilyen kódot.
- Több bejelentkezési szolgáltatóval is integrálható. Például: Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Identitásszolgáltatók

App Service [összevont identitást](https://en.wikipedia.org/wiki/Federated_identity)használ, amelyben egy harmadik féltől származó identitás-szolgáltató kezeli a felhasználói identitásokat és a hitelesítési folyamatot. Alapértelmezés szerint a következő identitás-szolgáltatók érhetők el:

| Szolgáltató | Bejelentkezési végpont | How-To útmutató |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [Azure AD-bejelentkezés App Service](configure-authentication-provider-aad.md) |
| [Microsoft-fiók](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [A Microsoft-fiók bejelentkezési App Service](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Facebook-Bejelentkezés App Service](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Google-bejelentkezés App Service](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [Twitter-bejelentkezés App Service](configure-authentication-provider-twitter.md) |
| Bármely [OpenID Connect](https://openid.net/connect/) -szolgáltató (előzetes verzió) | `/.auth/login/<providerName>` | [App Service OpenID Connect-bejelentkezés](configure-authentication-provider-openid-connect.md) |

Ha engedélyezi a hitelesítést és az engedélyezést ezen szolgáltatók egyikével, a bejelentkezési végpontja elérhetővé válik a felhasználói hitelesítéshez és a szolgáltatótól származó hitelesítési jogkivonatok érvényesítéséhez. A felhasználók számára tetszőleges számú bejelentkezési lehetőség adható meg.

## <a name="considerations-for-using-built-in-authentication"></a>A beépített hitelesítés használatának szempontjai

Ha engedélyezi ezt a funkciót, a rendszer az alkalmazásnak küldött összes kérést automatikusan átirányítja a HTTPS-re, függetlenül a HTTPS kényszerített App Service konfigurációs beállításától. Ezt letilthatja a  `requireHttps` v2-konfiguráció beállításával. Javasoljuk azonban a HTTPS használatát, és gondoskodjon arról, hogy ne legyenek továbbítva biztonsági tokenek a nem biztonságos HTTP-kapcsolatokon keresztül.

App Service a webhely tartalmához és API-khoz való hozzáférés korlátozásával vagy anélkül is használható a hitelesítéshez. Ha csak a hitelesített felhasználók számára szeretné korlátozni az alkalmazások elérését, állítsa be a végrehajtandó **műveletet, ha a kérés nincs hitelesítve** a konfigurált identitás-szolgáltatók egyikével való bejelentkezéshez. Ha hitelesítésre van szükség, de nem korlátozza a hozzáférést, állítsa be **a végrehajtandó műveletet, ha a kérés nincs hitelesítve** "névtelen kérelmek engedélyezése (nincs művelet)".

> [!NOTE]
> Minden alkalmazásnak regisztrálnia kell a saját engedélyeit és beleegyezését. Kerülje a környezetek közötti engedélyek megosztását külön alkalmazás-regisztrációk használatával külön üzembe helyezési pontokhoz. Az új kód tesztelésekor ez a gyakorlat segít megakadályozni az éles alkalmazást érintő problémákat.

## <a name="how-it-works"></a>Működés

[Szolgáltatások architektúrája Windows rendszeren (nem tároló üzembe helyezés)](#feature-architecture-on-windows-non-container-deployment))

[A Linux és a tárolók funkcióinak architektúrája](#feature-architecture-on-linux-and-containers)

[Hitelesítési folyamat](#authentication-flow)

[Engedélyezési viselkedés](#authorization-behavior)

[Felhasználói és alkalmazási jogcímek](#user-and-application-claims)

[Jogkivonat-tároló](#token-store)

[Naplózás és nyomkövetés](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Szolgáltatások architektúrája Windows rendszeren (nem tároló telepítés)

A hitelesítési és engedélyezési modul ugyanazon a Sandboxon fut, mint az alkalmazás kódja. Ha engedélyezve van, minden bejövő HTTP-kérelem áthalad az alkalmazás kódjának kezelése előtt.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Architektúra-diagram, amely azt mutatja, hogy egy folyamat által elfogott kérelmeket a rendszer a helyhez tartozó homokozóban, amely az identitás-szolgáltatókkal kommunikál, mielőtt engedélyezi a forgalmat az üzembe helyezett hely felé" lightbox="media/app-service-authentication-overview/architecture.png":::

Ez a modul több dolgot kezel az alkalmazásban:

- A felhasználók hitelesítése a megadott szolgáltatóval
- Jogkivonatok ellenőrzése, tárolása és frissítése
- Felügyeli a hitelesített munkamenetet.
- Azonosító adatokat szúr be a kérelem fejlécbe

A modul külön fut az alkalmazás kódjától, és az Alkalmazásbeállítások használatával van konfigurálva. Nem szükségesek SDK-k, meghatározott nyelvek vagy az alkalmazás kódjának módosítása. 

#### <a name="feature-architecture-on-linux-and-containers"></a>A Linux és a tárolók funkcióinak architektúrája

A hitelesítési és engedélyezési modul egy külön tárolóban fut, amely el van különítve az alkalmazás kódjától. A mi a nagykövet mintának nevezett [minta](/azure/architecture/patterns/ambassador)használatával a bejövő forgalom a Windows rendszeren hasonló funkciókat hajthat végre. Mivel nem fut a folyamaton belül, az adott nyelvi keretrendszerrel való közvetlen integráció nem lehetséges; az alkalmazás által igényelt releváns információk azonban az alább ismertetett kérelmek fejlécének használatával továbbítódnak.

#### <a name="authentication-flow"></a>Hitelesítési folyamat

A hitelesítési folyamat az összes szolgáltató esetében azonos, de attól függően különbözik, hogy be kíván-e jelentkezni a szolgáltató SDK-val:

- Szolgáltatói SDK nélkül: az alkalmazás delegálja az összevont bejelentkezést App Serviceba. Ez általában a böngésző alkalmazásai esetében fordul elő, amely bemutathatja a szolgáltató bejelentkezési lapját a felhasználó számára. A kiszolgálói kód kezeli a bejelentkezési folyamatot, ezért a _kiszolgáló által irányított_ folyamatnak vagy a _kiszolgálói_ folyamatnak is nevezik. Ez az eset a böngésző alkalmazásaira vonatkozik. Olyan natív alkalmazásokra is vonatkozik, amelyek a Mobile Apps Client SDK használatával írják alá a felhasználókat, mert az SDK webes nézetet nyit meg a felhasználók App Service hitelesítéssel való aláírásához.
- A Provider SDK használatával: az alkalmazás manuálisan aláírja a felhasználókat a szolgáltatóhoz, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service. Ez általában a böngésző nélküli alkalmazások esetében fordul elő, amely nem tudja bemutatni a szolgáltató bejelentkezési lapját a felhasználónak. Az alkalmazás kódja kezeli a bejelentkezési folyamatot, ezért az _ügyfél által irányított_ folyamatnak vagy az _ügyfél_ folyamatának is nevezik. Ez az eset a REST API-kra, a [Azure Functionsra](../azure-functions/functions-overview.md)és a JavaScript böngésző ügyfeleire, valamint a bejelentkezési folyamat nagyobb rugalmasságot igénylő böngésző-alkalmazásokra vonatkozik. Olyan natív Mobile apps-alkalmazásokra is vonatkozik, amelyek a szolgáltató SDK használatával írják alá a felhasználókat.

A App Service egy megbízható böngészőből kezdeményezett hívásokat a App Service vagy [Azure functions](../azure-functions/functions-overview.md) egy másik REST API a kiszolgáló által irányított folyamattal lehet hitelesíteni. További információ: [a hitelesítés és az engedélyezés testreszabása app Serviceban](app-service-authentication-how-to.md).

Az alábbi táblázat a hitelesítési folyamat lépéseit mutatja be.

| Lépés | Szolgáltatói SDK nélkül | Szolgáltatói SDK-val |
| - | - | - |
| 1. Jelentkezzen be a felhasználóba | Átirányítja az ügyfelet a következőre: `/.auth/login/<provider>` . | Az ügyfél kódja közvetlenül a szolgáltató SDK-val aláírja a felhasználót, és hitelesítési jogkivonatot kap. További információt a szolgáltató dokumentációjában talál. |
| 2. hitelesítés utáni | A szolgáltató átirányítja az ügyfelet a következőre: `/.auth/login/<provider>/callback` . | Az ügyfél kódja a [szolgáltatótól kapott jogkivonatot](app-service-authentication-how-to.md#validate-tokens-from-providers) `/.auth/login/<provider>` érvényesíti. |
| 3. hitelesített munkamenet létrehozása | App Service a hitelesített cookie-t adja hozzá válaszként. | App Service visszaadja a saját hitelesítési tokenjét az ügyfél kódjához. |
| 4. hitelesített tartalom kiszolgálása | Az ügyfél hitelesítési cookie-t is tartalmaz a következő kérelmekben (a böngésző automatikusan kezeli). | Az ügyfél kódja a `X-ZUMO-AUTH` (Mobile apps ügyféloldali SDK-k által automatikusan kezelt) fejlécben található hitelesítési tokent jeleníti meg. |

Az ügyféloldali böngészők esetében a App Service automatikusan irányíthatja az összes nem hitelesített felhasználót `/.auth/login/<provider>` . A felhasználók egy vagy több `/.auth/login/<provider>` hivatkozással is bejelentkezhetnek az alkalmazásba, ha az Ön által választott szolgáltatón keresztül jelentkeznek be.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Engedélyezési viselkedés

A [Azure Portalban](https://portal.azure.com)számos viselkedést konfigurálhat app Service engedélyezéshez, ha a bejövő kérelem nincs hitelesítve.

![A "végrehajtandó művelet, ha a kérés nem hitelesítve" legördülő lista](media/app-service-authentication-overview/authorization-flow.png)

A következő címsorok leírják a beállításokat.

**Névtelen kérelmek engedélyezése (nincs művelet)**

Ez a beállítás elhalasztja az alkalmazás kódjához való nem hitelesített forgalom engedélyezését. A hitelesített kérések esetében App Service a HTTP-fejlécekben is továbbítja a hitelesítési adatokat.

Ez a lehetőség nagyobb rugalmasságot biztosít a névtelen kérelmek kezelésére. Például lehetővé teszi, hogy [több bejelentkezési szolgáltatót nyújtson](app-service-authentication-how-to.md#use-multiple-sign-in-providers) be a felhasználók számára. Azonban kódot kell írnia.

**Csak hitelesített kérelmek engedélyezése**

A beállítás **bejelentkezik a szolgáltatással \<provider>**. App Service átirányítja az összes névtelen kérelmet `/.auth/login/<provider>` a kiválasztott szolgáltatóhoz. Ha a névtelen kérelem egy natív mobil alkalmazásból származik, a visszaadott válasz egy `HTTP 401 Unauthorized` .

Ezzel a beállítással nem kell bármilyen hitelesítési kódot írnia az alkalmazásban. A felhasználó jogcímeinek vizsgálatával a finomabb engedélyezés, például a szerepkör-specifikus hitelesítés kezelhető (lásd: [hozzáférés a felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban.

> [!NOTE]
> Alapértelmezés szerint az Azure AD-bérlő bármely felhasználója igényelhet jogkivonatot az alkalmazáshoz az Azure AD-ből. [Az alkalmazást az Azure ad-ben is konfigurálhatja](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) , ha szeretné korlátozni az alkalmazáshoz való hozzáférést egy meghatározott felhasználói csoport számára.


#### <a name="user-and-application-claims"></a>Felhasználói és alkalmazási jogcímek

Az összes nyelvi keretrendszer esetében a App Service a bejövő jogcímek (azaz egy hitelesített végfelhasználó vagy ügyfélalkalmazás) által a kód számára elérhető jogcímeket a kérések fejlécére szúrja be. A ASP.NET 4,6 alkalmazások esetében App Service a hitelesített felhasználó jogcímeivel tölti fel a [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) értéket, így a szabványos .net-kód mintát is használhatja, beleértve az `[Authorize]` attribútumot is. Hasonlóképpen, a PHP-alkalmazások esetében App Service feltölti a `_SERVER['REMOTE_USER']` változót. Java-alkalmazások esetén a jogcímek a [tomcat servletből érhetők el](configure-language-java.md#authenticate-users-easy-auth).

[Azure functions](../azure-functions/functions-overview.md)esetén a `ClaimsPrincipal.Current` .net-kód nem töltődik fel, de továbbra is megkeresheti a felhasználói jogcímeket a kérések fejlécében, vagy lekérheti az `ClaimsPrincipal` objektumot a kérelem környezetében, vagy akár egy kötési paraméteren keresztül is. További információért lásd: [az ügyfél-identitások használata](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

További információ: hozzáférés a [felhasználói jogcímekhez](app-service-authentication-how-to.md#access-user-claims).

Ekkor a ASP.NET Core jelenleg nem támogatja az aktuális felhasználó feltöltését a hitelesítési/engedélyezési szolgáltatással. Azonban néhány [harmadik fél, a nyílt forráskódú middleware-összetevők léteznek,](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) amelyek segítenek a hézag kitöltésében.

#### <a name="token-store"></a>Jogkivonat-tároló

A App Service beépített jogkivonat-tárolót biztosít, amely a webalkalmazások, API-k vagy natív mobil alkalmazások felhasználóinak társított jogkivonatok tárháza. Ha bármely szolgáltatóval engedélyezi a hitelesítést, a jogkivonat-tároló azonnal elérhetővé válik az alkalmazás számára. Ha az alkalmazás kódjának a felhasználó nevében kell hozzáférnie a szolgáltatók adataihoz, például:

- közzététel a hitelesített felhasználó Facebook-idővonalán
- a felhasználó vállalati adatai beolvasása a Microsoft Graph API használatával

Általában kódot kell írnia a tokenek összegyűjtéséhez, tárolásához és frissítéséhez az alkalmazásban. A jogkivonat-tárolóval egyszerűen [lekérheti a jogkivonatokat](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) , amikor szüksége van rájuk, és [közli, hogy app Service,](app-service-authentication-how-to.md#refresh-identity-provider-tokens) ha érvénytelenné válnak. 

Az azonosító tokenek, a hozzáférési tokenek és a frissítési tokenek gyorsítótárazva vannak a hitelesített munkamenet számára, és csak a hozzárendelt felhasználó férhetnek hozzájuk.  

Ha nem kell jogkivonatokkal dolgoznia az alkalmazásban, akkor letilthatja a jogkivonat-tárolót az alkalmazás **hitelesítés/engedélyezés** lapján.

#### <a name="logging-and-tracing"></a>Naplózás és nyomkövetés

Ha [engedélyezi az alkalmazások naplózását](troubleshoot-diagnostic-logs.md), a rendszer közvetlenül a naplófájlokban fogja látni a hitelesítési és engedélyezési nyomkövetéseket. Ha olyan hitelesítési hiba jelenik meg, amelyet nem várt, a meglévő alkalmazás-naplók alapján kényelmesen megkeresheti az összes adatot. Ha engedélyezi a [Sikertelen kérelmek nyomkövetését](troubleshoot-diagnostic-logs.md), láthatja, hogy pontosan milyen szerepet játszott a hitelesítési és engedélyezési modul egy sikertelen kérelemben. A nyomkövetési naplók között keresse meg a nevű modulra mutató hivatkozásokat `EasyAuthModule_32/64` .

## <a name="more-resources"></a>További erőforrások

- [Útmutató: az App Service vagy Azure Functions alkalmazás konfigurálása az Azure AD-bejelentkezés használatára](configure-authentication-provider-aad.md)
- [A hitelesítés és az engedélyezés speciális használata Azure App Service](app-service-authentication-how-to.md)

Minták
- [Oktatóanyag: hitelesítés hozzáadása a Azure App Service-on futó webalkalmazáshoz](scenario-secure-app-authentication-app-service.md)
- [Oktatóanyag: Azure App Service teljes körű hitelesítése és engedélyezése a felhasználóknak (Windows vagy Linux)](tutorial-auth-aad.md)
- [Az Azure AppService EasyAuth .NET Core-integrációja (harmadik fél)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Azure App Service hitelesítés használata a .NET Core-ban (harmadik fél)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
