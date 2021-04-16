---
title: 'Rövid útmutató: Azure DNS zóna és rekord létrehozása – Azure PowerShell'
titleSuffix: Azure DNS
description: A cikkből megtudhatja, hogyan hozhat létre DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti rövid útmutató, amellyel az Azure PowerShell használatával létrehozhatja és kezelheti az első DNS-zónáját és -rekordját.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c457b435ca17ce9dc86e1a83a4d1a56d9623b17
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537875"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Rövid útmutató: Azure DNS-zóna és -rekord létrehozása az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezzel a rövid útmutatóval létrehozza első DNS-zónáját és -rekordját az Azure PowerShell használatával. Ezek a lépések az [Azure Portal](dns-getstarted-portal.md) vagy az [Azure CLI](dns-getstarted-cli.md) használatával is elvégezhetők. 

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Végül a DNS-zóna interneten való közzétételéhez konfigurálnia kell a tartomány névkiszolgálóit. Az egyes lépéseket az alábbiakban ismertetjük.

Az Azure DNS saját tartományok létrehozását is támogatja. Ha részletes útmutatót keres az első saját DNS-zóna és -rekord létrehozásához, lásd: [Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel](private-dns-getstarted-powershell.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell helyileg vagy helyileg telepített Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Mielőtt létrehozná a DNS-zónát, hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzDnsZone` parancsmag használatával hozható létre. A következő példában létrehozunk egy contoso.xyz nevű DNS-zónát  a *MyResourceGroup erőforráscsoportban.* A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

Rekordhalmazt a `New-AzDnsRecordSet` parancsmag használatával hozhat létre. Az alábbi példa egy "www" relatív nevű rekordot hoz létre a "contoso.xyz" DNS-zónában, a "MyResourceGroup" erőforráscsoportban. A rekordhalmaz teljes neve "www.contoso.xyz". A rekord típusa "A", az IP-címe "10.10.10.10", az TTL pedig 3600 másodperc.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy már van egy "A" tesztrekordot tesztelési DNS-zónája, tesztelheti a névfeloldást egy *nslookup nevű eszközzel.* 

**A DNS-névfeloldás tesztelése:**

1. Futtassa a következő parancsmagot a zóna névkiszolgálói listájának lekértéhez:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Másolja ki a névkiszolgálók egyik nevét az előző lépés kimenetből.

1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Például:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Az alábbi képernyőhöz hasonlónak kell lennie:

   ![Képernyőkép egy parancssori ablakról egy n s keresési paranccsal, valamint a Kiszolgáló, Cím, Név és Cím értékekkel.](media/dns-getstarted-portal/nslookup.PNG)

A **www \. contoso.xyz** **10.10.10.10-re** oldódik fel, ahogyan ön konfigurálta. Ez az eredmény ellenőrzi, hogy a névfeloldás megfelelően működik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, a rövid útmutatóban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta első DNS-zónáját és -rekordját az Azure PowerShell használatával, létrehozhat webalkalmazás-rekordokat is egyéni tartományokban.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
