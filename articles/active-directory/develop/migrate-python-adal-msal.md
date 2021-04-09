---
title: Python-ADAL a MSAL áttelepítési útmutatóhoz | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan telepítheti át a Azure Active Directory Authentication Library (ADAL) Python-alkalmazást a Pythonhoz készült Microsoft Authentication Library (MSAL) szolgáltatásba.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578838"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL a MSAL áttelepítési útmutatója a Pythonhoz

Ez a cikk azokat a módosításokat mutatja be, amelyeket a Azure Active Directory hitelesítési függvénytárat (ADAL) használó alkalmazások áttelepíteni kell a Microsoft Authentication Library (MSAL) használatára.

## <a name="difference-highlights"></a>Különbségi csúcsfények

A ADAL együttműködik a Azure Active Directory (Azure AD) 1.0-s verziójának végpontján. A Microsoft Authentication Library (MSAL) a Microsoft Identity platformmal működik – korábbi nevén Azure Active Directory v 2.0-végpont. A Microsoft Identity platform különbözik az Azure AD 1.0-s verziótól:

Támogatja
  - Munkahelyi és iskolai fiókok (Azure AD-kiépített fiókok)
  - Személyes fiókok (például Outlook.com vagy Hotmail.com)
  - A saját e-mail-címét vagy közösségi identitását (például LinkedIn, Facebook, Google) használó ügyfelei az Azure AD B2C ajánlaton keresztül

- A szabványokkal kompatibilis szabványok:
  - OAuth 2.0-s verzió
  - OpenID Connect (OIDC)

További részletekért lásd: [Mi a különbség a Microsoft Identity platformmal kapcsolatban?](../azuread-dev/azure-ad-endpoint-comparison.md) .

### <a name="scopes-not-resources"></a>Hatókörök nem erőforrásai

A ADAL Python jogkivonatokat vásárol az erőforrásokhoz, de a MSAL Python jogkivonatokat vásárol a hatókörökhöz. A MSAL Python API-felülete már nem rendelkezik erőforrás-paraméterrel. A hatóköröket olyan karakterláncok listájában kell megadnia, amelyek deklarálják a kívánt engedélyeket és a kért erőforrásokat. A hatókörökkel kapcsolatos néhány példa: [Microsoft Graph hatókörei](/graph/permissions-reference).

A `/.default` hatókör utótagját hozzáadhatja az erőforráshoz, hogy az alkalmazásokat a 1.0-s verziójú végpontról (ADAL) a Microsoft Identity platformra (MSAL) telepítse. A (z) erőforrás értékének `https://graph.microsoft.com` megfelelő hatókör értéke például a következő: `https://graph.microsoft.com/.default` .  Ha az erőforrás nem szerepel az URL-címben, de az űrlap erőforrás-azonosítója `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , továbbra is használhatja a hatókör értékét `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

A különböző típusú hatókörökkel kapcsolatos további részletekért tekintse [meg a Microsoft Identity platform engedélyeit és](./v2-permissions-and-consent.md) a hozzájuk tartozó jogosultságokat, valamint a webes API-k 1.0-s verzióinak [elfogadására vonatkozó](./msal-v1-app-scopes.md) cikkeket.

### <a name="error-handling"></a>Hibakezelés

A Pythonhoz készült Azure Active Directory Authentication Library (ADAL) a kivétel `AdalError` alapján jelzi, hogy probléma merült fel. A MSAL for Python jellemzően hibakódokat használ. További információ: [MSAL for Python hibakezelés](msal-error-handling-python.md).

### <a name="api-changes"></a>API-változások

A következő táblázat a Pythonhoz készült ADAL API-t, a MSAL for Python esetében pedig a helyét tartalmazza.

| ADAL Python API-hoz  | MSAL Python API-hoz |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication vagy ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) és [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) és [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | Gyorsítótár az adatmegőrzéssel, elérhető a [MSAL-bővítmények](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) közül |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Meglévő frissítési tokenek migrálása a MSAL Pythonhoz

A Microsoft Authentication Library (MSAL) elvonta a frissítési tokenek fogalmát. A MSAL Python alapértelmezés szerint a memóriában tárolt jogkivonat-gyorsítótárat biztosít, így nincs szükség a frissítési tokenek tárolására, keresésére vagy frissítésére. A felhasználók kevesebb bejelentkezési kérést is láthatnak, mivel a frissítési tokenek általában felhasználói beavatkozás nélkül frissíthetők. További információ a jogkivonat-gyorsítótárról: [Egyéni jogkivonat-gyorsítótár szerializálása a MSAL-ben a Pythonhoz](msal-python-token-cache-serialization.md).

A következő kód segít áttelepíteni a más OAuth2-függvénytár által kezelt frissítési tokeneket (beleértve a ADAL Pythont is), amelyet a MSAL for Python felügyel. A frissítési tokenek áttelepítésének egyik oka annak megakadályozása, hogy a meglévő felhasználók újra be kelljen jelentkezniük az alkalmazás MSAL for Python-ba való áttelepítésekor.

A frissítési token áttelepítésének módszere a MSAL for Python használata az előző frissítési token használatával új hozzáférési jogkivonat beszerzéséhez. Ha az új frissítési tokent adja vissza, a Python MSAL a gyorsítótárban fogja tárolni.
Mivel a MSAL Python 1.3.0, a MSAL-on belül egy API-t biztosítunk erre a célra.
Tekintse meg a következő kódrészletet, [amelyet a frissítési tokenek áttelepítése a MSAL Python használatával című befejezett mintából](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67) idézett

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a [1.0-s és a 2.0-s verzió összehasonlítását](../azuread-dev/azure-ad-endpoint-comparison.md)ismertető témakört.
