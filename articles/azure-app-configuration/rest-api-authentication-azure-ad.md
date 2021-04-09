---
title: Azure Active Directory REST API – hitelesítés
description: A Azure Active Directory használatával hitelesítheti az Azure-alkalmazások konfigurációját a REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930517"
---
# <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

A HTTP-kéréseket a hitelesítési séma használatával hitelesítheti `Bearer` Azure Active Directory (Azure ad) által beszerzett jogkivonattal. Ezeket a kérelmeket Transport Layer Security (TLS) protokollon keresztül kell továbbítania.

## <a name="prerequisites"></a>Előfeltételek

Hozzá kell rendelnie a rendszerbiztonsági tag, amely az Azure AD-token igénylésére szolgál az [Azure-alkalmazás megfelelő konfigurációs szerepköreinek](./rest-api-authorization-azure-ad.md)egyikéhez.

Adja meg az összes kérelmet a hitelesítéshez szükséges összes HTTP-fejléctel. A minimális követelmény a következő:

|  Kérelem fejléce | Leírás  |
| --------------- | ------------ |
| `Authorization` | A séma által igényelt hitelesítési adatok `Bearer` . |

**Példa**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD-jogkivonat beszerzése

Az Azure AD-token beszerzése előtt meg kell határoznia, hogy milyen felhasználót szeretne hitelesíteni, milyen célközönséget kér a jogkivonat számára, és milyen Azure AD-végpontot (Authority) kell használnia.

### <a name="audience"></a>Célközönség

Kérje le az Azure AD-tokent egy megfelelő célközönségre. Az Azure-alkalmazások konfigurálásához használja az alábbi célközönségek egyikét. A célközönség a jogkivonat által kért *erőforrásra* is utalhat.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> Ha a célközönséget kérte `{configurationStoreName}.azconfig.io` , pontosan meg kell egyeznie a kérelem `Host` küldéséhez használt kérelem fejlécével (a kis-és nagybetűk megkülönböztetésével).

### <a name="azure-ad-authority"></a>Azure AD-szolgáltató

Az Azure AD-szolgáltató az Azure AD-token beszerzéséhez használt végpont. Ez a formáját képezi `https://login.microsoftonline.com/{tenantId}` . A `{tenantId}` szegmens azon Azure ad-bérlői azonosítóra hivatkozik, amelyhez a hitelesíteni próbált felhasználó vagy alkalmazás tartozik.

### <a name="authentication-libraries"></a>Hitelesítési tárak

Az Azure olyan kódtárakat biztosít, amelyek Azure Active Directory hitelesítési könyvtárakkal rendelkeznek, így leegyszerűsíthető egy Azure AD-jogkivonat beszerzésének folyamata. Az Azure több nyelvhez is létrehozza ezeket a kódtárakat. További információért lásd a [dokumentációt](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Hibák

A következő hibák merülhetnek fel.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**OK:** Nem biztosította az engedélyezési kérelem fejlécét a `Bearer` sémával.

**Megoldás:** Adjon meg egy érvényes `Authorization` http-kérelem fejlécét.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**OK:** Az Azure AD-jogkivonat érvénytelen.

**Megoldás:** Szerezze be az Azure AD-tokent az Azure AD-szolgáltatótól, és győződjön meg arról, hogy a megfelelő célközönséget használta.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**OK:** Az Azure AD-jogkivonat érvénytelen.

**Megoldás:** Azure AD-jogkivonat beszerzése az Azure AD-szolgáltatótól. Győződjön meg arról, hogy az Azure AD-bérlő a konfigurációs tárolóhoz tartozó előfizetéshez társítva van. Ez a hiba akkor jelenhet meg, ha a résztvevő több Azure AD-bérlőhöz tartozik.