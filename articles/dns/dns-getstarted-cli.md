---
title: 'Rövid útmutató: Azure DNS zóna és rekord létrehozása – Azure CLI'
titleSuffix: Azure DNS
description: Rövid útmutató – A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure CLI használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1929cd512d18d7fd234aff1f55814c423455e63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94561369"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Rövid útmutató: Azure DNS-zóna és -rekord létrehozása az Azure CLI használatával

Ez a cikk bemutatja az első DNS-zóna és -rekord létrehozásának lépéseit az Azure CLI használatával, amely Windows, Mac és Linux platformokon is elérhető. Ezeket a lépéseket az [Azure Portal](dns-getstarted-portal.md) vagy az [Azure PowerShell](dns-getstarted-powershell.md) használatával is elvégezheti.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

A Azure DNS támogatja a magánhálózati DNS-zónákat is. További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md). További információt a privát DNS-zónák létrehozásáról [az Azure DNS privát zónái CLI segítségével történő használatának első lépéseit](./private-dns-getstarted-cli.md) ismertető cikkben olvashat.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Mielőtt létrehozná a DNS-zónát, hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. A paranccsal kapcsolatos súgó megtekintéséhez írja be a következőt: `az network dns zone create -h`.

A következő példa egy *contoso.xyz* nevű DNS-zónát hoz létre az erőforráscsoport *MyResourceGroup*. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

DNS-rekordokat az `az network dns record-set [record type] add-record` paranccsal lehet létrehozni. Az A-rekordokkal kapcsolatos segítségért lásd: `azure network dns record-set A add-record -h`.

Az alábbi példa egy "www" relatív névvel rendelkező rekordot hoz létre a "MyResourceGroup" erőforráscsoport "contoso.xyz" DNS-zónájában. A rekord teljes neve "www.contoso.xyz". A rekord típusa "A", az IP-cím "10.10.10.10", és az alapértelmezett élettartam 3600 másodperc (1 óra).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy rendelkezik egy "A" teszttel rendelkező DNS-zónával, tesztelheti a névfeloldást egy *nslookup* nevű eszközzel. 

**DNS-névfeloldás tesztelése:**

1. Futtassa a következő parancsmagot a zóna névkiszolgálók listájának lekéréséhez:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Másolja az egyik névkiszolgálói nevet az előző lépésben megadott kimenetből.

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Például:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   A következő képernyőhöz hasonlóan kell megjelennie:

   ![Képernyőfelvétel: a parancssori ablak n s keresési paranccsal, valamint a kiszolgáló, a címe, a név és a címe értékeit jeleníti meg.](media/dns-getstarted-portal/nslookup.PNG)

A **www \. contoso.xyz** állomásnév a **10.10.10.10** hasonlóan oldódik meg, ugyanúgy, ahogy konfigurálta. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, a rövid útmutatóban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta első DNS-zónáját és -rekordját az Azure CLI használatával, létrehozhat webalkalmazás-rekordokat is egyéni tartományokban.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
