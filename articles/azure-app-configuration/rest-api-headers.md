---
title: Azure app Configuration REST API – fejlécek
description: Az Azure-alkalmazás konfigurációjában használt fejlécek hivatkozási lapja REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932574"
---
# <a name="headers"></a>Fejlécek

Ez a cikk az Azure-alkalmazás konfigurációs REST API használt fejlécekre mutató hivatkozásokat tartalmaz.

## <a name="request-headers"></a>Kérések fejlécei

Az alábbi táblázat az Azure-alkalmazások konfigurációjában használt általános kérelmek fejléceit ismerteti.

| Fejléc | Leírás | Példa |
|--|--|--|
| **Engedélyezés** | A szolgáltatáshoz való kérelem [hitelesítéséhez](./rest-api-authentication-index.md) használatos. Lásd: [14,8. szakasz](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Elfogadás** | Arról tájékoztatja a kiszolgálót, hogy az ügyfél milyen adathordozó-típust fogad el egy HTTP-válaszban. Lásd: [14,1. szakasz](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Elfogadás – datetime** | Azt kéri, hogy a kiszolgáló visszaadja a tartalmát a korábbi állapotának megjelenítéséhez. Ennek a fejlécnek az értéke ennek az állapotnak a kért dátum/idő. Lásd: [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | A HTTP-kérés törzsében lévő tartalom adathordozó-típusát tartalmazza. Lásd: [14,17. szakasz](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Date** | A HTTP-kérés kiadásának dátuma. Ez a fejléc a [HMAC-hitelesítésben](./rest-api-authentication-hmac.md)használatos. Lásd: [14,18. szakasz](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Állomás** | Meghatározza azt a bérlőt, amelyhez a kérést kiállították. Ez a fejléc a [HMAC-hitelesítésben](./rest-api-authentication-hmac.md)használatos. Lásd: [14,23. szakasz](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | HTTP-kérelem feltételes elvégzésére szolgál. Ez a kérelem csak akkor lehet sikeres, ha a célként megadott erőforrás ETag megegyezik ennek a fejlécnek az értékével. A "*" érték minden ETag megfelel. Lásd: [14,24. szakasz](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | HTTP-kérelem feltételes elvégzésére szolgál. Ez a kérelem csak akkor lehet sikeres, ha a célként megadott erőforrás ETag nem felel meg a fejléc értékének. A "*" érték minden ETag megfelel. Lásd: [14,26. szakasz](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Szinkronizáló-token** | A kérelmek sorozatában a valós idejű konzisztencia engedélyezésére szolgál. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-MS-Client-Request-ID** | Az ügyfél által a kérelem kerekítésének nyomon követésére használt egyedi azonosító. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-Content-sha256** | A HTTP-kérés törzsének sha256-kivonata. Ez a fejléc a [HMAC-hitelesítésben](./rest-api-authentication-hmac.md)használatos. | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-MS-Date** | Ezt a fejlécet lehet beállítani és használni a fejléc helyett, `Date` Ha a dátum fejléce nem érhető el. Ez a fejléc a [HMAC-hitelesítésben](./rest-api-authentication-hmac.md)használatos. | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-MS-Return-Client-Request-ID** | A fejléctel együtt használatos `x-ms-client-request-id` . Ha a fejléc értéke "true" (igaz), akkor a rendszer arra utasítja a kiszolgálót, hogy adja vissza a `x-ms-client-request-id` kérelem fejlécének értékét. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Válaszfejlécek

A kiszolgáló a válaszában a következő HTTP-fejléceket is tartalmazhatja.

| Fejléc | Leírás | Példa |
|--|--|--|
| **Content-Type** | A HTTP-válasz törzsében lévő tartalom adathordozó-típusát tartalmazza. Lásd: [14,17. szakasz](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | Egy olyan átlátszatlan jogkivonat, amely egy adott erőforrás állapotát jelképezi. Feltételes műveletekben is használható. Lásd: [14,19. szakasz](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Utolsó módosítás** | A kért erőforrás utolsó módosításának leírása. [Http-dátumként](https://tools.ietf.org/html/rfc2616#section-3.3.1)formázva. Lásd: [14,29. szakasz](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Hivatkozás** | A válaszhoz kapcsolódó erőforrásokra mutató hivatkozásokat tartalmaz. Ezt a fejlécet használja a rendszer a lapozáshoz a _következő_ hivatkozás használatával. Lásd: [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Emlék – datetime** | Azt jelzi, hogy a válaszban szereplő tartalom korábbi állapotot jelöl. A fejléc értéke ennek az állapotnak a DateTime értéke. Lásd: [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **újrapróbálkozás után – MS** | Egy javasolt időszak (ezredmásodpercben), ameddig az ügyfélnek várnia kell a sikertelen kérések újrapróbálkozása előtt. | `retry-after-ms: 10` |
| **x-MS-Request-ID** | A kiszolgáló által a kérelem nyomon követésére szolgáló egyedi azonosító. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-hitelesítés** | Az ügyfelek hitelesítésre való kitámadására szolgál, és a hitelesítési kísérlet okának okát adja meg. Lásd: [14,47. szakasz](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
