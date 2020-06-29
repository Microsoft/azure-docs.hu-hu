---
title: API-megfontolások – Azure Marketplace
description: Verziószámozás, hibakezelés és engedélyezési problémák a Marketplace API-k használatakor.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 11ec6f9a38c5fe957eba922f3136f4fa0b2c4995
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85516290"
---
# <a name="api-considerations"></a>API-megfontolások

<a name="api-versioning"></a>API-verziószámozás
--------------

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

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
