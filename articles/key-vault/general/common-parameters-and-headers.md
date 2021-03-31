---
title: Gyakori paraméterek és fejlécek
description: A Key Vault erőforrásokkal kapcsolatos összes művelethez közös paraméterek és fejlécek.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83005812"
---
# <a name="common-parameters-and-headers"></a>Gyakori paraméterek és fejlécek

Az alábbi információk a Key Vault erőforrásokhoz kapcsolódó összes művelet esetében közösek:

- A HTTP `Host` -fejlécnek mindig jelen kell lennie, és meg kell adnia a tároló állomásnevét. Példa: `Host: contoso.vault.azure.net`. Vegye figyelembe, hogy a legtöbb ügyfél-technológia feltölti a `Host` fejlécet az URI-ból. Például a következőt `GET https://contoso.vault.azure.net/secrets/mysecret{...}` fogja beállítani `Host` : `contoso.vault.azure.net` . Ez azt jelenti, hogy ha a Key Vault nyers IP-cím használatával fér hozzá `GET https://10.0.0.23/secrets/mysecret{...}` , a fejléc automatikus értéke helytelen lesz, `Host` és manuálisan kell biztosítania, hogy a `Host` fejléc tartalmazza a tároló állomásnevét.
- Cserélje le az `{api-version}` elemet az API-verzióra az URI-ban.
- Cserélje le az `{subscription-id}` elemet az előfizetés azonosítójával az URI-ban
- Cserélje le `{resource-group-name}` az csoportot az erőforráscsoporthoz. További információ: erőforráscsoportok használata az Azure-erőforrások kezeléséhez.
- Cserélje le a `{vault-name}` elemet a Key Vault nevére az URI-ban.
- Állítsa be a Content-type fejlécet az Application/JSON értékre.
- Állítsa be az engedélyezési fejlécet olyan JSON Web Tokenre, amelyet a Azure Active Directorytól (HRE) szerez be. További információ: Azure Resource Manager kérelmek [hitelesítése](authentication-requests-and-responses.md) .

## <a name="common-error-response"></a>Gyakori hiba a válaszban
A szolgáltatás HTTP-állapotkódot használ a sikeres vagy sikertelen művelet jelzésére. Továbbá a hibák a következő formátumban választ tartalmaznak:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elem neve | Típus | Description |
|---|---|---|
| code | sztring | A bekövetkezett hiba típusa.|
| message | sztring | Annak leírása, hogy mi okozta a hibát. |



## <a name="see-also"></a>Lásd még:
 [Azure Key Vault REST API-hivatkozás](/rest/api/keyvault/)
