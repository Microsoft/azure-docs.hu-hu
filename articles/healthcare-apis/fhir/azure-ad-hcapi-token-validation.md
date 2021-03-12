---
title: Azure API a FHIR hozzáférési token érvényesítéséhez
description: Végigvezeti a jogkivonat-ellenőrzésen, és tippeket ad a hozzáférési problémák elhárításához
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019128"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API a FHIR hozzáférési token érvényesítéséhez

A FHIR készült Azure API ellenőrzi, hogy a hozzáférési token a megvalósítástól és a konfigurációjától függ-e. Ebben a cikkben áttekintjük az ellenőrzési lépéseket, amelyek hasznosak lehetnek a hozzáférési problémák elhárítása során.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Az érvényesítési jogkivonat nem tartalmaz problémát az Identity providerben

A jogkivonat-érvényesítés első lépése annak ellenőrzése, hogy a jogkivonatot a megfelelő identitás-szolgáltató bocsátotta-e ki, és hogy nem módosították-e. A FHIR-kiszolgáló úgy lesz konfigurálva, hogy egy adott identitás-szolgáltatót használjon `Authority` . A FHIR-kiszolgáló lekéri az identitás-szolgáltató adatait a `/.well-known/openid-configuration` végpontról. Az Azure AD használatakor a teljes URL-cím a következő lesz:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

ahol az `<TENANT-ID>` adott Azure ad-bérlő (vagy egy bérlői azonosító vagy egy tartománynév).

Az Azure AD egy, az alábbihoz hasonló dokumentumot ad vissza a FHIR-kiszolgálóhoz.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
A FHIR-kiszolgáló fontos tulajdonságai olyanok `jwks_uri` , amelyek közlik a kiszolgálóval, hogy hol kell beolvasni a jogkivonat-aláírás érvényesítéséhez szükséges titkosítási kulcsokat, és `issuer` amely megadja a kiszolgálónak, hogy mi lesz a `iss` kiszolgáló által kiállított jogkivonatok kiállítói jogcíme (). A FHIR-kiszolgáló ezzel ellenőrizheti, hogy a rendszer hiteles tokent kap-e.

## <a name="validate-claims-of-the-token"></a>A jogkivonat jogcímeinek érvényesítése

Miután a kiszolgáló ellenőrizte a jogkivonat hitelességét, a FHIR-kiszolgáló ellenőrzi, hogy az ügyfél rendelkezik-e a jogkivonathoz való hozzáféréshez szükséges jogcímeket.

Ha a FHIR készült Azure API-t használja, a kiszolgáló érvényesíti az alábbiakat:

1. A jogkivonat jogosult `Audience` ( `aud` jogcím).
1. A jogkivonatot kiállító felhasználó vagy tag jogosult a FHIR-kiszolgáló adatsíkja elérésére. A `oid` jogkivonat jogcíme tartalmaz egy azonosító objektum azonosítóját, amely egyedileg azonosítja a felhasználót vagy a rendszerbiztonsági azonosítót.

Javasoljuk, hogy a FHIR szolgáltatás az [Azure RBAC használatára legyen konfigurálva](configure-azure-rbac.md) az adatsík szerepkör-hozzárendelések kezeléséhez. A helyi RBAC azonban akkor is [konfigurálhatja](configure-local-rbac.md) , ha a FHIR szolgáltatás külső vagy másodlagos Azure Active Directory bérlőt használ. 

Az OSS Microsoft FHIR-kiszolgáló Azure-hoz való használatakor a kiszolgáló ellenőrzi a következőket:

1. A jogkivonat jogosult `Audience` ( `aud` jogcím).
1. A jogkivonat szerepkört tartalmaz a `roles` jogcímben, amely jogosult a FHIR-kiszolgálóhoz való hozzáférésre.

A szerepkörök a FHIR- [kiszolgálón való definiálásának](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)részletes ismertetését lásd:.

A FHIR-kiszolgálók azt is ellenőrizhetik, hogy egy hozzáférési jogkivonat rendelkezik-e hatókörrel (jogkivonat-jogcímben `scp` ) a FHIR API azon részéhez való hozzáféréshez, amelyet az ügyfél megkísérel elérni. Jelenleg a FHIR-hez készült Azure API és az Azure-hoz készült FHIR-kiszolgáló nem érvényesíti a jogkivonat-hatóköröket.

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, hogyan hajthatja végre a jogkivonat-érvényesítést, elvégezheti az oktatóanyagot egy JavaScript-alkalmazás létrehozásához és a FHIR-információk olvasásához.

>[!div class="nextstepaction"]
>[Webalkalmazás-oktatóanyag](tutorial-web-app-fhir-server.md)