---
title: 'Gyors útmutató: Azure DNS zóna és rekord létrehozása Azure PowerShell'
titleSuffix: Azure DNS
description: A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti rövid útmutató, amellyel az Azure PowerShell használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e33f6fdba1a15032d76b94804d610e292f663d59
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937162"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Rövid útmutató: Azure DNS-zóna és -rekord létrehozása az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezzel a rövid útmutatóval létrehozza első DNS-zónáját és -rekordját az Azure PowerShell használatával. Ezek a lépések az [Azure Portal](dns-getstarted-portal.md) vagy az [Azure CLI](dns-getstarted-cli.md) használatával is elvégezhetők. 

A DNS-zóna egy adott tartomány DNS-rekordjainak üzemeltetésére szolgál. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

Az Azure DNS saját tartományok létrehozását is támogatja. Ha részletes útmutatót keres az első saját DNS-zóna és -rekord létrehozásához, lásd: [Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Mielőtt létrehozná a DNS-zónát, hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzDnsZone` parancsmag használatával hozható létre. Az alábbi példa egy *contoso. xyz* nevű DNS-zónát hoz létre a *MyResourceGroup*nevű erőforráscsoport-csoportban. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

Rekordhalmazt a `New-AzDnsRecordSet` parancsmag használatával hozhat létre. Az alábbi példa egy "www" relatív névvel rendelkező rekordot hoz létre a "contoso. xyz" DNS-zónában, a "MyResourceGroup" erőforráscsoport esetében. A rekord teljes neve "www. contoso. xyz". A rekord típusa "A", az IP-cím "10.10.10.10", az élettartam pedig 3600 másodperc.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy rendelkezik egy "A" teszttel rendelkező DNS-zónával, tesztelheti a névfeloldást egy *nslookup*nevű eszközzel. 

**DNS-névfeloldás tesztelése:**

1. Futtassa a következő parancsmagot a zóna névkiszolgálók listájának lekéréséhez:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Másolja az egyik névkiszolgálói nevet az előző lépésben megadott kimenetből.

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Példa:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   A következő képernyőhöz hasonlóan kell megjelennie:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

A (z) **www\.contoso. xyz** állomásnév a **10.10.10.10**-re van feloldva, ugyanúgy, ahogy konfigurálta. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ha már nincs rájuk szükség, a rövid útmutatóban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta első DNS-zónáját és -rekordját az Azure PowerShell használatával, létrehozhat webalkalmazás-rekordokat is egyéni tartományokban.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)

