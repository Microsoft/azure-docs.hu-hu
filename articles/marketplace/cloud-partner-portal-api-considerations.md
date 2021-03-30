---
title: API-megfontolások – Azure Marketplace
description: Verziószámozás, hibakezelés és engedélyezési problémák a Marketplace API-k használatakor.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035280"
---
# <a name="api-considerations"></a>API-kkal kapcsolatos szempontok

<a name="api-versioning"></a>API-verziószámozás
--------------

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

Előfordulhat, hogy az API több verziója is elérhető. Az ügyfeleknek jelezniük kell a használni kívánt verziót, ha a `api-version` paramétert a lekérdezési karakterlánc részeként biztosítják.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Az ismeretlen vagy érvénytelen API-verzióra irányuló kérelemre adott válasz egy 400-es HTTP-kód. Ez a hiba a válasz törzsében lévő ismert API-verziók gyűjteményét adja vissza.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Hibák
------

Az API a hibákat a megfelelő HTTP-állapotkódok és opcionálisan a JSON-ként szerializált válaszban található további információk alapján válaszolja meg.
Ha hibaüzenetet kap, különösen a 400 osztályú hibát, ne próbálja megismételni a kérést az alapul szolgáló ok kijavítása előtt. A fenti mintában például javítsa ki az API Version paramétert a kérelem újraküldése előtt.

<a name="authorization-header"></a>Engedélyezési fejléc
--------------------

Az ebben a hivatkozásban található összes API esetében át kell adni az engedélyezési fejlécet a Azure Active Directory (Azure AD) által beszerzett tulajdonosi jogkivonattal együtt. Ez a fejléc érvényes válasz fogadásához szükséges. Ha nem található, a `401 Unauthorized` rendszer hibaüzenetet küld. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
