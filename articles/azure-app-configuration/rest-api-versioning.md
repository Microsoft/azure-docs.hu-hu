---
title: Azure app Configuration REST API – verziószámozás
description: Az Azure app Configuration REST API használatával történő verziószámozásra szolgáló lapok
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 90d131cdc7c496853f2520951c95b9903d69f8fb
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424264"
---
# <a name="versioning"></a>Verziókezelés

API-Version: 1,0

Minden ügyfél-kérelemnek explicit API-verziót kell megadnia lekérdezési karakterlánc paraméterként. Például: `https://{myconfig}.azconfig.io/kv?api-version=1.0`

`api-version` SemVer (főverzió. alverzió) formátumban van kifejezve. A tartomány vagy a verzió egyeztetése nem támogatott.

## <a name="error-response"></a>Hiba válasza

A következő összefoglalja a kiszolgáló által visszaadott lehetséges hibaüzenetek összegzését, ha a kért API-verzió nem egyezik.

### <a name="api-version-unspecified"></a>Nem meghatározott API-verzió

Akkor következik be, amikor az ügyfél egy API-verzió megadása nélkül kezdeményezi a kérést.

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

### <a name="unsupported-api-version"></a>Nem támogatott API-verzió

Akkor következik be, amikor az ügyfél által kért API-verzió nem felel meg a kiszolgáló által támogatott API-verzióknak.

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

### <a name="invalid-api-version"></a>Érvénytelen API-verzió

Akkor következik be, amikor az ügyfél egy API-verzióval kezdeményezi a kérést, de az érték hibás, vagy a kiszolgáló nem tudja értelmezni.

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

### <a name="ambiguous-api-version"></a>Nem egyértelmű API-verzió

Akkor következik be, amikor egy ügyfél olyan API-verziót kér, amely nem egyértelmű a kiszolgálón. Például több különböző érték.

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
