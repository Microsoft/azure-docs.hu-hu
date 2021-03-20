---
title: Alkalmazások és API-k migrálása a b2clogin.com-be
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használhatja a b2clogin.com-t a Azure Active Directory B2C átirányítási URL-címeiben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109062"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Átirányítási URL-címek beállítása b2clogin.com Azure Active Directory B2C

Ha a Azure Active Directory B2C (Azure AD B2C) alkalmazásban állít be egy identitás-szolgáltatót a regisztráláshoz és a bejelentkezéshez, meg kell adnia egy átirányítási URL-címet. Az alkalmazásokban és API-kkal már nem hivatkozhat *login.microsoftonline.com* a felhasználók hitelesítéséhez Azure ad B2C. Ehelyett használja a *b2clogin.com* az összes új alkalmazáshoz, és telepítse át a meglévő alkalmazásokat a *login.microsoftonline.com* -ről a *b2clogin.com*-re.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Login.microsoftonline.com elavulása

**Október 2020 frissítés:** Kiterjesztjük a türelmi időszakot olyan bérlők számára, akik nem tudják teljesíteni az eredetileg a 2020. december 04-én bejelentett elavult időpontot. A login.microsoftonline.com kivonása mostantól nem haladhatja meg a **2021. január 14** -én.

**Háttér**: a 2019. december 1-jétől a login.microsoftonline.com-támogatás ütemezett kivonulását eredetileg Azure AD B2C, 2020. december 1-jétől [jelentették be](https://azure.microsoft.com/updates/b2c-deprecate-msol/) . Ez a meglévő bérlők számára egy (1) évet biztosított a b2clogin.com való Migrálás során. A 2019. december 04. után létrehozott új bérlők nem fogadják el a login.microsoftonline.com érkező kéréseket. Az összes funkció változatlan marad a b2clogin.com-végponton.

A login.microsoftonline.com elavulása nem érinti Azure Active Directory bérlőket. Ez a módosítás csak Azure Active Directory B2C bérlőket érint.

## <a name="what-endpoints-does-this-apply-to"></a>Milyen végpontokra vonatkozik ez a
A b2clogin.com-re való áttérés csak olyan hitelesítési végpontokra vonatkozik, amelyek Azure AD B2C házirendeket (felhasználói folyamatokat vagy egyéni szabályzatokat) használnak a felhasználók hitelesítéséhez. Ezek a végpontok egy `<policy-name>` paraméterrel rendelkeznek, amely meghatározza, hogy mely házirendet Azure ad B2C kell használni. [További információ a Azure ad B2C szabályzatokról](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Ezek a végpontok a következőkhöz hasonlóak:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Azt is megteheti, hogy a `<policy-name>` lekérdezési paraméterként a következőket adta át:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> A "Policy" paramétert használó végpontokat frissíteni kell, valamint az [identitás-szolgáltató átirányítási URL-címeit](#change-identity-provider-redirect-urls).

Egyes Azure AD B2C ügyfelek az Azure AD Enterprise-bérlők megosztott funkcióit használják, például a OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamatát. Ezek a funkciók az Azure AD login.microsoftonline.com-végpontokkal érhetők el, *amelyek nem tartalmaznak házirend-paramétert*. __Ezeket a végpontokat a rendszer nem érinti__.

## <a name="benefits-of-b2clogincom"></a>A b2clogin.com előnyei

Ha a *b2clogin.com* -t használja átirányítási URL-címként:

* A Microsoft-szolgáltatások cookie-fejlécében felhasznált lemezterület csökken.
* Az átirányítási URL-címeknek már nem kell tartalmazniuk a Microsoftnak való hivatkozást.
* A JavaScript ügyféloldali kódja támogatott (jelenleg [előzetes](javascript-and-page-layout.md)verzióban) a testreszabott lapokon. Biztonsági korlátozások miatt a rendszer eltávolítja a JavaScript-kódot és a HTML-űrlapok elemeit az egyéni lapokról, ha *login.microsoftonline.com* használ.

## <a name="overview-of-required-changes"></a>A szükséges módosítások áttekintése

Az alkalmazások *b2clogin.com* való áttelepíteni több módosítást is szükségessé tehet:

* Módosítsa az átirányítási URL-címet az identitás-szolgáltató alkalmazásaiban a *b2clogin.com* hivatkozására.
* Frissítse Azure AD B2C alkalmazásait a *b2clogin.com* használatára a felhasználói folyamat és a jogkivonat-végpont hivatkozásaiban. Ilyen lehet például a Microsoft Authentication Library (MSAL) hitelesítési függvénytár használatának frissítése.
* Frissítse a [felhasználói felület testreszabására](customize-ui-with-html.md)vonatkozó CORS-beállításokban definiált összes **engedélyezett eredetet** .

Egy régi végpont a következőhöz hasonló lehet:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Egy megfelelő frissített végpont A következőképpen fog kinézni:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Identitás-szolgáltató átirányítási URL-címeinek módosítása

Minden identitás-szolgáltató webhelyén, amelyben létrehozott egy alkalmazást, módosítsa az összes megbízható URL-címet úgy, hogy átirányítsa a `your-tenant-name.b2clogin.com` *login.microsoftonline.com* helyett.

A b2clogin.com-átirányítási URL-címekhez két formátumot használhat. Az első lehetőséget biztosít arra, hogy a bérlői tartománynév helyett a bérlői azonosító (GUID) használatával a "Microsoft" ne jelenjen meg az URL-ben.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

A második lehetőség a bérlői tartománynevet használja a (z `your-tenant-name.onmicrosoft.com` ) formában. Például:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Mindkét formátum esetében:

* Cserélje le a helyére `{your-tenant-name}` a Azure ad B2C bérlő nevét.
* Távolítsa el, `/te` Ha az szerepel az URL-címben.

## <a name="update-your-applications-and-apis"></a>Alkalmazások és API-k frissítése

A Azure AD B2C-kompatibilis alkalmazások és API-k kódja `login.microsoftonline.com` több helyen is hivatkozhat. Előfordulhat például, hogy a kód a felhasználói folyamatokra és a jogkivonat-végpontokra mutató hivatkozásokat tartalmaz. Frissítse a következőt a hivatkozás helyett `your-tenant-name.b2clogin.com` :

* Engedélyezési végpont
* Jogkivonat-végpont
* Jogkivonat kiállítója

Például a contoso regisztrációs/bejelentkezési szabályzatának Authority végpontja a következő lesz:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

További információ a OWIN-alapú webalkalmazások b2clogin.com való áttelepítéséről: [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md).

A Azure AD B2C által védett Azure API Management API-k áttelepítéséhez tekintse meg az [azure API Management API biztonságossá tétele a Azure ad B2C](secure-api-management.md)használatával című rész az [áttelepítés b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) című szakaszát.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority tulajdonsága

Ha a [MSAL.net][msal-dotnet] v2 vagy a korábbi verzióját használja, állítsa az **ValidateAuthority** tulajdonságot az `false` ügyfél-példányra, hogy engedélyezze az átirányítást a *b2clogin.com*. Ennek az értéknek a beállítása `false` nem szükséges a MSAL.net v3 és újabb verziókhoz.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL a JavaScript validateAuthority tulajdonsághoz

Ha a MSAL-t a JavaScript v 1.2.2 vagy korábbi verzióra használja, állítsa a **validateAuthority** tulajdonságot a [következőre:][msal-js] `false` .

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Ha a `validateAuthority: true` MSAL.js 1.3.0 + (az alapértelmezett) értéket adja meg, akkor érvényes jogkivonat-kiállítót is meg kell adnia a következővel `knownAuthorities` :

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Következő lépések

További információ a OWIN-alapú webalkalmazások b2clogin.com való áttelepítéséről: [OWIN-alapú webes API áttelepítése b2clogin.com](multiple-token-endpoints.md).

A Azure AD B2C által védett Azure API Management API-k áttelepítéséhez tekintse meg az [azure API Management API biztonságossá tétele a Azure ad B2C](secure-api-management.md)használatával című rész az [áttelepítés b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) című szakaszát.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
