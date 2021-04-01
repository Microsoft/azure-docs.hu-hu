---
title: Szolgáltatás-hozzáférési jogkivonatok lekérése
description: Leírja, hogyan hozhatók létre tokenek az ARR REST API-khoz való hozzáféréshez
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97830700"
---
# <a name="get-service-access-tokens"></a>Szolgáltatás-hozzáférési jogkivonatok lekérése

Az ARR REST API-khoz való hozzáférés csak a jogosult felhasználók számára engedélyezett. Az engedélyezés bizonyításához el kell küldenie egy *hozzáférési jogkivonatot* a REST-kérelmekkel együtt. Ezeket a jogkivonatokat a *biztonságos jogkivonat szolgáltatás* (STS) adja ki az Exchange-fiók kulcsaként. A tokenek **élettartama 24 óra** , így a felhasználók számára a szolgáltatás teljes hozzáférése nélkül is kiállíthatók.

Ez a cikk bemutatja, hogyan hozhat létre ilyen hozzáférési tokent.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy ARR-fiókot](create-an-account.md), ha még nem rendelkezik ilyennel.

## <a name="token-service-rest-api"></a>Jogkivonat-szolgáltatás REST API

Hozzáférési tokenek létrehozásához a *biztonságos jogkivonat szolgáltatás* egyetlen REST API biztosít. Az STS szolgáltatás URL-címe a távoli renderelési fiók fiókjának tartományából függ. Ez az űrlap https://sts . [ fiók tartománya], például: `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>"Jogkivonat beolvasása" kérés

| URI | Metódus |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Fejléc | Érték |
|--------|:------|
| Engedélyezés | "Tulajdonos **accountId**:**accountKey**" |

Cserélje le a *accountId* és a *accountKey* a megfelelő adataira.

### <a name="get-token-response"></a>"Jogkivonat beolvasása" válasz

| Állapotkód | JSON-adattartalom | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | AccessToken: karakterlánc | Siker |

| Fejléc | Cél |
|--------|:------|
| MS-CV | Ezzel az értékkel lehet nyomon követni a hívást a szolgáltatáson belül |

## <a name="getting-a-token-using-powershell"></a>Jogkivonat beszerzése a PowerShell használatával

Az alábbi PowerShell-kód azt mutatja be, hogyan kell elküldeni a szükséges REST-kérést az STS-nek. Ezután kinyomtatja a tokent a PowerShell-parancssorba.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

A szkript egyszerűen kinyomtatja a tokent a kimenetre, ahonnan másolhatja & beillesztheti. Valós projekt esetén automatizálja ezt a folyamatot.

## <a name="next-steps"></a>Következő lépések

* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
* [Azure frontend API-k](../how-tos/frontend-apis.md)
* [Munkamenet-felügyeleti REST API](../how-tos/session-rest-api.md)
