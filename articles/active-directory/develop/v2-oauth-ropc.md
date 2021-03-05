---
title: Jelentkezzen be az erőforrás-tulajdonos jelszava hitelesítő adataival | Azure
titleSuffix: Microsoft identity platform
description: Támogatja a böngésző nélküli hitelesítési folyamatokat az erőforrás-tulajdonos jelszavas hitelesítő adatai (ROPC) megadása esetén.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bf469b79fa532978e904a54f32c80280706ee7cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174580"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft Identity platform és OAuth 2,0 erőforrás-tulajdonosi jelszó hitelesítő adatai

A Microsoft Identity platform támogatja a [OAuth 2,0 erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), amely lehetővé teszi, hogy az alkalmazás közvetlenül a jelszavának kezelésével jelentkezzen be a felhasználóba.  Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

> [!WARNING]
> A Microsoft azt javasolja, hogy _ne használja a_ ROPC folyamatot. A legtöbb esetben biztonságosabb alternatívák érhetők el és ajánlottak. Ennek a folyamatnak nagyon nagyfokú megbízhatóságra van szüksége az alkalmazásban, és olyan kockázatokat hordoz, amelyek nem jelennek meg más folyamatokban. Ezt a folyamatot csak akkor használja, ha a többi biztonságosabb folyamat nem használható.

> [!IMPORTANT]
>
> * A Microsoft Identity platform csak az Azure AD-bérlők ROPC támogatja, a személyes fiókokat nem. Ez azt jelenti, hogy a bérlő-specifikus végpontot ( `https://login.microsoftonline.com/{TenantId_or_Name}` ) vagy a `organizations` végpontot kell használnia.
> * Az Azure AD-bérlők számára meghívott személyes fiókok nem használhatják a ROPC.
> * Azok a fiókok, amelyeknek nincsenek jelszavai, nem jelentkezhetnek be a ROPC-on keresztül. Ennél a forgatókönyvnél azt javasoljuk, hogy ehelyett használjon egy másik folyamatot az alkalmazáshoz.
> * Ha a felhasználóknak [többtényezős hitelesítést (MFA)](../authentication/concept-mfa-howitworks.md) kell használniuk az alkalmazásba való bejelentkezéshez, a rendszer letiltja a helyet.
> * A ROPC nem támogatott [hibrid identitás-összevonási](../hybrid/whatis-fed.md) forgatókönyvekben (például az Azure ad és a helyszíni fiókok hitelesítéséhez használt ADFS). Ha a felhasználók teljes körűen átirányítják a helyszíni identitás-szolgáltatókat, az Azure AD nem tudja tesztelni a felhasználónevet és a jelszót az identitás-szolgáltatón. Az [átmenő hitelesítés](../hybrid/how-to-connect-pta.md) a ROPC esetében is támogatott.
> * A hibrid identitás-összevonási forgatókönyvek alól kivételt képeznek a következők: a AllowCloudPasswordValidation TRUE értékkel rendelkező Home Realm Discovery-házirend lehetővé teszi, hogy a ROPC folyamat az összevont felhasználók számára is elérhető legyen, amikor a helyszíni jelszó szinkronizálva van a felhőbe. További információ: az [összevont felhasználók közvetlen ROPC hitelesítésének engedélyezése örökölt alkalmazásokhoz](../manage-apps/configure-authentication-for-federated-users-portal.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications).

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán a ROPC folyamat látható.

![Az erőforrás-tulajdonos jelszava hitelesítő folyamatát ábrázoló diagram](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Engedélyezési kérelem

A ROPC folyamat egyetlen kérelem: elküldi az ügyfél-azonosítót és a felhasználó hitelesítő adatait a IDENTITÁSSZOLGÁLTATÓ, majd visszaküldi a jogkivonatokat. Az ügyfélnek meg kell adnia a felhasználó e-mail-címét (UPN) és a jelszót. A sikeres kérelem után az ügyfélnek biztonságosan fel kell szabadítania a felhasználó hitelesítő adatait a memóriából. Soha nem kell mentenie őket.

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán!
> [![Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az a címtár-bérlő, amelybe be szeretné jelentkezni a felhasználót. Ez lehet a GUID vagy a felhasználóbarát név formátuma. Ez a paraméter nem állítható be `common` vagy értékre `consumers` , de beállítható a következőre: `organizations` . |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldal alkalmazás-(ügyfél-) azonosítója. |
| `grant_type` | Kötelező | Értékre kell állítani `password` . |
| `username` | Kötelező | A felhasználó e-mail címe. |
| `password` | Kötelező | A felhasználó jelszava. |
| `scope` | Ajánlott | Az alkalmazás által igényelt [hatókörök](v2-permissions-and-consent.md)vagy engedélyek szóközzel tagolt listája. Egy interaktív folyamat során a rendszergazdának vagy a felhasználónak meg kell adnia a hatókörét az idő előtt. |
| `client_secret`| Néha szükséges | Ha az alkalmazás nyilvános ügyfél, akkor a vagy a `client_secret` `client_assertion` nem vehető fel.  Ha az alkalmazás bizalmas ügyfél, akkor azt is tartalmaznia kell. |
| `client_assertion` | Néha szükséges | Egy másik formája `client_secret` , amely egy tanúsítvány használatával lett létrehozva.  További részletekért tekintse meg a [tanúsítvány hitelesítő adatait](active-directory-certificate-credentials.md) . |

### <a name="successful-authentication-response"></a>Sikeres hitelesítési válasz

A következő példa egy sikeres jogkivonat-választ mutat be:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Paraméter | Formátum | Leírás |
| --------- | ------ | ----------- |
| `token_type` | Sztring | Mindig állítsa be a következőt: `Bearer` . |
| `scope` | Szóközzel tagolt karakterláncok | Ha egy hozzáférési jogkivonatot adott vissza, akkor ez a paraméter felsorolja azokat a hatóköröket, amelyekhez a hozzáférési jogkivonat érvényes. |
| `expires_in`| int | Azon másodpercek száma, ameddig a tartalmazott hozzáférési jogkivonat érvényes. |
| `access_token`| Átlátszatlan karakterlánc | A kért [hatókörökhöz](v2-permissions-and-consent.md) lett kiállítva. |
| `id_token` | JWT | Kiadva, ha az eredeti `scope` paraméter tartalmazza a `openid` hatókört. |
| `refresh_token` | Átlátszatlan karakterlánc | Kiadva, ha az eredeti `scope` paraméter szerepel `offline_access` . |

A frissítési jogkivonattal új hozzáférési jogkivonatok szerezhetők be, és a tokenek frissítése a [OAuth-programkód dokumentációjában](v2-oauth2-auth-code-flow.md#refresh-the-access-token)ismertetett folyamattal megegyező folyamat használatával végezhető el.

### <a name="error-response"></a>Hiba válasza

Ha a felhasználó nem adta meg a helyes felhasználónevet vagy jelszót, vagy az ügyfél nem kapta meg a kért beleegyezett, a hitelesítés sikertelen lesz.

| Hiba | Leírás | Ügyfél művelete |
|------ | ----------- | -------------|
| `invalid_grant` | A hitelesítés sikertelen | A hitelesítő adatok helytelenek voltak, vagy az ügyfél nem rendelkezik beleegyezik a kért hatókörökkel. Ha a hatókörök nem lettek megadva, a rendszer `consent_required` hibaüzenetet küld. Ha ez történik, az ügyfélnek egy interaktív üzenetbe kell küldenie a felhasználót egy webnézet vagy egy böngésző használatával. |
| `invalid_request` | A kérés nem megfelelően lett kiépítve | A támogatás típusa nem támogatott a `/common` vagy a `/consumers` hitelesítési környezetekben.  `/organizations`Ehelyett használjon vagy egy bérlői azonosítót. |

## <a name="learn-more"></a>Tudjon meg többet

A ROPC használatára vonatkozó példát a következő témakörben talál: a [.net Core Console alkalmazás](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) kódjának mintája a githubon.
