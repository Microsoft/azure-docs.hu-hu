---
title: Gyakori paraméterek és fejlécek
description: Az erőforrásokhoz kapcsolódó összes műveletben közös paraméterek és fejlécek Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 616b6061b08258d465b09902556de6903b873199
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749869"
---
# <a name="common-parameters-and-headers"></a>Gyakori paraméterek és fejlécek

A következő információk az összes olyan műveletre, amely a Key Vault kapcsolatosak:

- A `Host` HTTP-fejlécnek mindig jelen kell lennie, és meg kell adnia a tároló állomásnevét. Példa: `Host: contoso.vault.azure.net`. Vegye figyelembe, hogy a legtöbb ügyfél-technológia feltölti `Host` a fejlécet az URI-ból. A például `GET https://contoso.vault.azure.net/secrets/mysecret{...}` a következőre lesz `Host` `contoso.vault.azure.net` beállítva: . Ez azt jelenti, hogy ha nyers IP Key Vault (például ) használatával fér hozzá a kulcshoz, a fejléc automatikus értéke helytelen lesz, és manuálisan kell arról beállítania, hogy a fejléc tartalmazza-e a tároló `GET https://10.0.0.23/secrets/mysecret{...}` `Host` `Host` állomásnevét.
- Cserélje `{api-version}` le a helyére az api-version helyére az URI-ban.
- Cserélje `{subscription-id}` le a helyére az előfizetés azonosítóját az URI-ban
- Cserélje `{resource-group-name}` le a helyére az erőforráscsoportot. További információ: Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.
- Cserélje `{vault-name}` le a helyére a kulcstartó nevét az URI-ban.
- Állítsa a Content-Type fejlécet application/json (alkalmazás/json) fejlécre.
- Állítsa az Engedélyezés fejlécet egy olyan JSON-webtoken, amely a Azure Active Directory (AAD) alapján szerezhető be. További információ: [Hitelesítő adatok](authentication-requests-and-responses.md) Azure Resource Manager kérelmekhez.

## <a name="common-error-response"></a>Gyakori hibaválasz
A szolgáltatás HTTP-állapotkódokat használ a sikeres vagy sikertelenség jelzésére. Emellett a hibák a következő formátumban tartalmaznak választ:

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
| code | sztring | Az észlelt hiba típusa.|
| message | sztring | A hiba okának leírása. |



## <a name="see-also"></a>Lásd még:
 [Azure Key Vault REST API referencia](/rest/api/keyvault/)
