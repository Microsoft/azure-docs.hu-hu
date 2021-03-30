---
title: Titkosítási kulcs adatainak lekérése
titleSuffix: Azure Cognitive Search
description: Lekéri a titkosítási kulcs nevét és verzióját, amelyet egy indexben vagy szinonima-térképen használt, hogy a kulcs a Azure Key Vault kezelhető legyen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88932899"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Az ügyfelek által felügyelt legfontosabb információk beolvasása az indexekről és a szinonimák térképe

Az Azure Cognitive Searchban az ügyfél által felügyelt titkosítási kulcsok létrehozása, tárolása és kezelése Azure Key Vault-ben történik. Ha meg kell határoznia, hogy egy objektum titkosítva van-e, vagy a kulcs nevét vagy verzióját használta, használja a REST API vagy egy SDK-t, hogy lekérje a **encryptionKey** tulajdonságot egy indexből vagy szinonima Térkép definíciója alapján. 

Javasoljuk, hogy [engedélyezze a naplózást](../key-vault/general/logging.md) Key Vault, hogy nyomon követhesse a kulcshasználat használatát.

## <a name="get-the-admin-api-key"></a>A felügyeleti API-kulcs beszerzése

Ahhoz, hogy egy keresési szolgáltatás objektum-definícióit lekérje, rendszergazdai jogosultságokkal kell hitelesítenie magát. A felügyeleti API-kulcs beszerzésének legegyszerűbb módja a portálon keresztül.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és nyissa meg a keresési szolgáltatás áttekintése lapot.

1. A bal oldalon kattintson a **kulcsok** elemre, és másolja a felügyeleti API-t. Az indexhez és a szinonimák leképezésének lekéréséhez rendszergazdai kulcs szükséges.

A további lépésekért váltson át a PowerShellre és a REST APIra. A portál nem jeleníti meg a szinonimákat, sem az indexek titkosítási kulcsának tulajdonságait.

## <a name="use-powershell-and-rest"></a>A PowerShell és a REST használata

Futtassa a következő parancsokat a változók beállításához és az objektum-definíciók beolvasásához.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogy melyik titkosítási kulcsot és verziót használja a rendszer, a kulcsot Azure Key Vault vagy más konfigurációs beállításokat is megtekintheti.

+ [Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból a PowerShell használatával](../key-vault/secrets/quick-create-powershell.md)

+ [Ügyfél által felügyelt kulcsok konfigurálása az adattitkosításhoz az Azure-ban Cognitive Search](search-security-manage-encryption-keys.md)