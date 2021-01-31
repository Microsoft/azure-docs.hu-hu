---
title: Microsoft Identity platform hitelesítési kódtárak | Azure
description: A Microsoft Identity platformmal kompatibilis ügyféloldali kódtárak és köztes szoftverek listája. Ezekkel a tárakkal a felhasználói bejelentkezés (hitelesítés) és a védett webes API-hozzáférés (Engedélyezés) támogatását veheti igénybe az alkalmazásaihoz.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218279"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft Identity platform hitelesítési kódtárak

Az alábbi táblázatok a Microsoft hitelesítési függvénytárának támogatását mutatják be több alkalmazás típusához. Ide tartoznak a könyvtár forráskódra mutató hivatkozások, ahol lekérheti az alkalmazás projektjeinek csomagját, valamint azt, hogy a könyvtár támogatja-e a felhasználói bejelentkezést (hitelesítést), a védett webes API-khoz (engedélyezéshez) való hozzáférést vagy mindkettőt.

A Microsoft Identity platformot az OpenID Foundation tanúsította [hitelesített OpenID-szolgáltatóként](https://openid.net/certification/). Ha a Microsoft Authentication Library (MSAL) vagy más, a Microsoft által támogatott tár helyett inkább egy könyvtárat szeretne használni, válasszon egy [hitelesített OpenID Connect-implementációt](https://openid.net/developers/certified/).

Ha úgy dönt, hogy a [OAuth 2,0 vagy az OpenID Connect 1,0](active-directory-v2-protocols.md)saját protokoll szintű implementációját adja meg, ügyeljen arra, hogy az egyes szabványokra vonatkozó előírásokban Kiemelt figyelmet szenteljenek a biztonsági szempontoknak, és a [Microsoft SDL][Microsoft-SDL]-hez hasonló, a szoftverfejlesztői életciklussal (SDL) kapcsolatos módszertant kell követnie.

## <a name="single-page-application-spa"></a>Egyoldalas alkalmazás (SPA)

Egy egyoldalas alkalmazás teljes egészében a böngésző felületén fut, és dinamikusan vagy alkalmazás betöltési idején kéri le az oldal (HTML, CSS és JavaScript) adatmennyiségét. Meghívhatja a webes API-kat a háttérbeli adatforrásokkal való kommunikációra.

Mivel a SPA kódja teljes egészében a böngészőben fut, olyan *nyilvános ügyfélnek* minősül, amely nem tudja biztonságosan tárolni a titkokat.

| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                                                    | Csomag                                                                      | Szerzés<br/>lépések                             | Bejelentkezési felhasználók                                         | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL szögletes 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| Angular              | [MSAL szögletes](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Oktatóanyag](tutorial-v2-angular.md)              | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Oktatóanyag](tutorial-v2-javascript-auth-code.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| React                | [MSAL reagál](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

## <a name="web-application"></a>Webalkalmazás

A webalkalmazások kódot futtatnak egy olyan kiszolgálón, amely HTML-, CSS-és JavaScript-adatokat hoz létre és küld a felhasználó webböngészőjébe. A felhasználó identitását a rendszer a felhasználó böngészője (az előtér) és a webkiszolgáló (a háttér) közötti munkamenetként tartja karban.

Mivel a webalkalmazás kódja a webkiszolgálón fut, egy olyan *bizalmas ügyfélnek* tekintendő, amely biztonságosan képes tárolni a titkokat.

| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                                     | Csomag                                                                                                    | Szerzés<br/>lépések                               | Bejelentkezési felhasználók                                            | Hozzáférés a webes API-khoz                                                    | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y]    | FE                                                           |
| ASP.NET Core         | [ASP.NET biztonság](/aspnet/core/security/)                                                                | [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y]    | ![A függvénytár nem kérhet hozzáférési jogkivonatokat a védett webes API-khoz.][n] | FE                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y]    | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y]    | FE                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Gyors útmutató](quickstart-v2-java-webapp.md)        | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y]    | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y]    | FE                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal – csomópont](https://www.npmjs.com/package/@azure/msal-node)                                                | [Gyors útmutató](quickstart-v2-nodejs-webapp-msal.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y]    | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y]    | Nyilvános előzetes verzió                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport – Azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [Gyors útmutató](quickstart-v2-nodejs-webapp.md)      | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y]    | ![A függvénytár nem kérhet hozzáférési jogkivonatokat a védett webes API-khoz.][n] | FE                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Gyors útmutató](quickstart-v2-python-webapp.md)      | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y]    | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y]    | FE                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

## <a name="desktop-application"></a>Asztali alkalmazás

Az asztali alkalmazások általában bináris (lefordított) kódok, amelyek felfedik a felhasználói felületet, és a felhasználó asztalán futnak.

Mivel egy asztali alkalmazás a felhasználó asztalán fut, olyan *nyilvános ügyfélnek* minősül, amely nem tudja biztonságosan tárolni a titkokat.

| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                                     | Csomag                                                                               | Szerzés<br/>lépések                        | Bejelentkezési felhasználók                                         | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| macOS (Swift/obj-C)  | [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Oktatóanyag](tutorial-v2-ios.md)             | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Oktatóanyag](tutorial-v2-windows-uwp.md)     | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Oktatóanyag](tutorial-v2-windows-desktop.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

## <a name="mobile-application"></a>Mobilalkalmazás

A mobil alkalmazások általában bináris (lefordított) kódok, amelyek felfedik a felhasználói felületet, és egy felhasználó mobileszközön futnak.

Mivel a mobil alkalmazások a felhasználó mobileszközön futnak, olyan *nyilvános ügyfélnek* számít, amely nem tudja biztonságosan tárolni a titkokat.

| Platform          | Projekt bekapcsolva<br/>GitHub                                                                          | Csomag                                                                               | Szerzés<br/>lépések                    | Bejelentkezési felhasználók                                         | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Gyors útmutató](quickstart-v2-android.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Android (Kotlin)  | [MSAL Android rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| iOS (Swift/obj-C) | [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Oktatóanyag](tutorial-v2-ios.md)         | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

## <a name="service--daemon"></a>Szolgáltatás/démon

A szolgáltatások és démonok általában a kiszolgálók közötti és a felügyelet nélküli (más néven *fej*-alapú) kommunikációhoz használatosak. Mivel nincs felhasználó a billentyűzeten a hitelesítő adatok megadásához vagy az erőforrás-hozzáféréshez való belefoglaláshoz, ezek az alkalmazások önmagukban hitelesítik magukat, nem pedig felhasználóként, amikor a rendszer jogosult hozzáférést kér a webes API erőforrásaihoz.

Egy kiszolgálón futó szolgáltatás vagy démon *bizalmas ügyfélnek* minősül, amely biztonságosan tárolhatja a titkos kulcsait.

| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                 | Csomag                                                                                | Szerzés<br/>lépések                           | Bejelentkezési felhasználók                                            | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Gyors útmutató](quickstart-v2-netcore-daemon.md) | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal – Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Gyors útmutató](quickstart-v2-python-daemon.md)  | ![A könyvtár nem kérhet azonosító jogkivonatot a felhasználói bejelentkezéshez.][n] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

## <a name="next-steps"></a>Következő lépések

További információ a Microsoft hitelesítési könyvtáráról: a [Microsoft Authentication Library (MSAL) áttekintése](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
