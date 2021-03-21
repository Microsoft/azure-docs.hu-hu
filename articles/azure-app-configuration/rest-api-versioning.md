---
title: Azure app Configuration REST API – verziószámozás
description: Az Azure app Configuration REST API használatával történő verziószámozásra szolgáló lapok
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932421"
---
# <a name="versioning"></a>Verziókezelés

Minden ügyfél-kérelemnek explicit API-verziót kell megadnia lekérdezési karakterlánc paraméterként. Példa: `https://{myconfig}.azconfig.io/kv?api-version=1.0`.

`api-version` SemVer (főverzió. alverzió) formátumban van kifejezve. A tartomány vagy a verzió egyeztetése nem támogatott.

Ez a cikk a 1,0-es API-verzióra vonatkozik.

A következő összefoglalja a kiszolgáló által visszaadott lehetséges hibaüzenetek összegzését, ha a kért API-verzió nem egyezik.

## <a name="api-version-unspecified"></a>Nem meghatározott API-verzió

Ez a hiba akkor fordul elő, amikor egy ügyfél egy API-verzió megadása nélkül kezdeményezi a kérést.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

## <a name="unsupported-api-version"></a>Nem támogatott API-verzió

Ez a hiba akkor fordul elő, ha az ügyfél által kért API-verzió nem felel meg a kiszolgáló által támogatott API-verzióknak.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="invalid-api-version"></a>Érvénytelen API-verzió

Ez a hiba akkor fordul elő, amikor az ügyfél egy API-verzióval rendelkező kérelmet küld, de az érték helytelen formátumú, vagy a kiszolgáló nem tudja elemezni.

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="ambiguous-api-version"></a>Nem egyértelmű API-verzió

Ez a hiba akkor fordul elő, amikor az ügyfél olyan API-verziót kér, amely nem egyértelmű a kiszolgálón (például több különböző érték).

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
