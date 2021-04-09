---
title: Natív ügyfélalkalmazások közzététele – Azure AD | Microsoft Docs
description: Ismerteti, hogyan engedélyezhető a natív ügyfélalkalmazások az Azure AD Application Proxy-összekötővel való kommunikációhoz a helyszíni alkalmazások biztonságos távoli elérésének biztosításához.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258662"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz

A webalkalmazások közzétételéhez Azure Active Directory (Azure AD) alkalmazásproxy használható, de a Microsoft Authentication Library (MSAL) szolgáltatással konfigurált natív ügyfélalkalmazások is közzétehető. A natív ügyfélalkalmazások eltérnek a webalkalmazások, mert azok egy eszközre vannak telepítve, míg a Web Apps böngészőn keresztül érhető el.

A natív ügyfélalkalmazások támogatásához az alkalmazásproxy fogadja az Azure AD által kiadott, a fejlécben eljuttatott jogkivonatokat. Az alkalmazásproxy szolgáltatás a felhasználók hitelesítését végzi. Ez a megoldás nem használ alkalmazás-jogkivonatokat a hitelesítéshez.

![A végfelhasználók, az Azure AD és a közzétett alkalmazások közötti kapcsolat](./media/application-proxy-configure-native-client-application/richclientflow.png)

Natív alkalmazások közzétételéhez használja a Microsoft hitelesítési függvénytárát, amely gondoskodik a hitelesítésről, és számos ügyfél-környezetet támogat. Az alkalmazásproxy illeszkedik az [asztali alkalmazáshoz, amely egy webes API-t hív meg egy bejelentkezett felhasználói](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) forgatókönyv nevében.

Ez a cikk végigvezeti a natív alkalmazások alkalmazásproxy és az Azure AD Authentication Library használatával történő közzétételének négy lépésén.

## <a name="step-1-publish-your-proxy-application"></a>1. lépés: a proxy alkalmazás közzététele

Tegye közzé a proxykiszolgálót úgy, mint bármely más alkalmazást, és rendelje hozzá a felhasználókat az alkalmazáshoz való hozzáféréshez. További információ: [alkalmazások közzététele az alkalmazásproxy](application-proxy-add-on-premises-application.md)-ben.

## <a name="step-2-register-your-native-application"></a>2. lépés: a natív alkalmazás regisztrálása

Most regisztrálnia kell az alkalmazást az Azure AD-ben az alábbiak szerint:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/). Megjelenik a **Azure Active Directory felügyeleti központ** **irányítópultja** .
1. Az oldalsávon válassza a **Azure Active Directory** lehetőséget. Megjelenik a **Azure Active Directory** áttekintés oldal.
1. Az Azure AD áttekintő oldalsávján válassza a **Alkalmazásregisztrációk** lehetőséget. Megjelenik az összes alkalmazás-regisztráció listája.
1. Válassza az **új regisztráció** lehetőséget. Megjelenik az **alkalmazás regisztrálása** lap.

   ![Új alkalmazás regisztrációjának létrehozása a Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. A **név** fejlécben adja meg az alkalmazás felhasználó által megjelenített nevét.
1. A **támogatott fióktípus** fejléc alatt válassza ki a hozzáférési szintet a következő irányelvek használatával:

   - Ha csak a szervezeten belüli fiókokat kívánja megcélozni, **csak a szervezeti címtárban** válassza a fiókok lehetőséget.
   - Ha csak üzleti vagy oktatási ügyfeleket szeretne megcélozni, válassza a **fiókok lehetőséget bármely szervezeti címtárban**.
   - A Microsoft-identitások legszélesebb körének megcélzásához válassza a fiókok lehetőséget a **szervezeti címtárban és a személyes Microsoft-fiókokban**.
1. Az **átirányítási URI** területen válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget, majd írja be az alkalmazás átirányítási URI-ját `https://login.microsoftonline.com/common/oauth2/nativeclient` .
1. Válassza ki és olvassa el a **Microsoft-Platform házirendjeit**, majd kattintson a **regisztráció** elemre. Létrejön és megjelenik az új alkalmazás regisztrálásának áttekintő lapja.

Az új alkalmazás-regisztráció létrehozásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>3. lépés: hozzáférés biztosítása a proxy alkalmazáshoz

Most, hogy regisztrálta a natív alkalmazását, hozzáférést biztosíthat más alkalmazásokhoz a címtárban, ebben az esetben a alkalmazásproxy eléréséhez. Annak engedélyezése, hogy a natív alkalmazás elérhető legyen a proxy alkalmazás számára:

1. Az új alkalmazás regisztrálása lap oldalsávján válassza az **API-engedélyek** lehetőséget. Megjelenik az új alkalmazás-regisztráció **API-engedélyek** lapja.
1. Válassza **az engedély hozzáadása** lehetőséget. Megjelenik a **kérelem API-engedélyek** lap.
1. Az **API kiválasztása** beállításnál válassza a **szervezetem által használt API**-k elemet. Megjelenik egy lista, amely tartalmazza az API-kat közzétevő címtárban lévő alkalmazásokat.
1. Írja be a keresőmezőbe, vagy görgessen az [1. lépés: a proxy alkalmazás közzététele](#step-1-publish-your-proxy-application), majd a proxykiszolgáló kiválasztásához közzétett alkalmazásproxy megkereséséhez.
1. Az **alkalmazás milyen típusú engedélyeket igényel?** fejlécben válassza ki az engedély típusát. Ha a natív alkalmazásnak bejelentkezett felhasználóként kell hozzáférnie a proxy Application API-hoz, válassza a **delegált engedélyek** lehetőséget.
1. Az **engedélyek kiválasztása** fejlécben válassza ki a kívánt engedélyt, majd válassza az **engedélyek hozzáadása** lehetőséget. A natív alkalmazás **API-engedélyeinek** lapja mostantól megjeleníti a hozzáadott alkalmazásproxy-alkalmazást és az engedélyek API-ját.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>4. lépés: a Microsoft hitelesítési függvénytár hozzáadása a kódhoz (.NET C# minta)

Szerkessze a natív alkalmazás kódját a Microsoft Authentication Library (MSAL) hitelesítési környezetében, hogy tartalmazza a következő szöveget: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

A mintakód szükséges adatai az Azure AD portálon találhatók, a következőképpen:

| Szükséges információk | Az Azure AD-portálon való keresés |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **Tulajdonságok**  >  **Könyvtár azonosítója** |
| \<App ID of the Native app> | **Alkalmazás regisztrálása**  >  *natív alkalmazása*  >  **Áttekintés**  >  **Alkalmazás azonosítója** |
| \<Scope> | **Alkalmazás regisztrálása**  >  *natív alkalmazása*  >  **API-engedélyek** > kattintson az engedély API-ra (user_impersonation) > egy panel, amely a felirat **user_impersonation** jelenik meg a jobb oldalon. > a hatókör a szerkesztési mezőben lévő URL-cím.
| \<Proxy App Url> | az API külső URL-címe és elérési útja

Miután módosította a MSAL kódot ezekkel a paraméterekkel, a felhasználók akkor is hitelesíthetők a natív ügyfélalkalmazások számára, ha a vállalati hálózaton kívül esnek.

## <a name="next-steps"></a>Következő lépések

További információ a natív alkalmazás folyamatáról: [natív alkalmazások Azure Active Directoryban](../azuread-dev/native-app.md).

Ismerje meg, hogyan állíthat be [egyszeri bejelentkezést az alkalmazásokba Azure Active Directory-ben](sso-options.md#choosing-a-single-sign-on-method).