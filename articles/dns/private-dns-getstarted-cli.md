---
title: Rövid útmutató – Azure Private DNS-zóna létrehozása az Azure CLI használatával
description: Ebben a rövid útmutatóban egy privát DNS-zónát és-rekordot hoz létre és tesztel a Azure DNSban. Ez egy lépésenkénti útmutató, amellyel az Azure CLI használatával létrehozhatja és kezelheti az első saját DNS-zónáját és -rekordját.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 820641af00caea4ffca450be8aa81b5357ba1261
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652961"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Rövid útmutató: Azure Private DNS-zóna létrehozása az Azure CLI használatával

Ez a rövid útmutató végigvezeti az első saját DNS-zóna és-rekord Azure CLI-vel történő létrehozásának lépésein.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. Ha privát DNS-zónát szeretne közzétenni a virtuális hálózaton, meg kell adnia azon virtuális hálózatok listáját, amelyek feloldhatnak rekordokat a zónában.  Ezeket *csatolt* virtuális hálózatoknak nevezzük. Ha engedélyezve van az automatikus regisztráció, a Azure DNS a zóna rekordjait is frissíti, amikor létrejön egy virtuális gép, megváltoztatja az IP-címét, vagy törli azt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- A rövid útmutató a [Azure PowerShell](private-dns-getstarted-powershell.md)használatával is elvégezhető.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Először hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

A következő példa egy **myAzureVNet** nevű virtuális hálózatot hoz létre. Ezután létrehoz egy **Private.contoso.com** nevű DNS-zónát a **MyAzureResourceGroup** -erőforráscsoporthoz, összekapcsolja a DNS-zónát a **MyAzureVnet** virtuális hálózattal, és engedélyezi az automatikus regisztrációt.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Ha csak névfeloldáshoz szeretne zónát létrehozni (automatikus állomásnév-regisztráció nélkül), akkor használhatja a `-e false` paramétert.

### <a name="list-dns-private-zones"></a>Privát DNS-zónák listázása

A DNS-zónák felsorolásához használja az `az network private-dns zone list` parancsot. További segítségért lásd: `az network dns zone list --help`.

Az erőforráscsoport megadásakor a rendszer csak az adott csoporton belüli zónákat sorolja fel:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Az erőforráscsoport kihagyásakor a rendszer az előfizetésben található összes zónát felsorolja:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Ez eltarthat néhány percig.

## <a name="create-an-additional-dns-record"></a>További DNS-rekord létrehozása

DNS-rekordokat az `az network private-dns record-set [record type] add-record` paranccsal lehet létrehozni. Az A-rekordok hozzáadásával kapcsolatos segítségért például lásd: `az network private-dns record-set A add-record --help`.

 A következő példa létrehoz egy rekordot a relatív name **db** -vel a DNS-zóna **Private.contoso.com**, az erőforráscsoport **MyAzureResourceGroup**. A **db.Private.contoso.com** teljesen minősített neve. A rekord típusa „A”, az IP-címe pedig „10.2.0.4”.

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>A DNS-rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most tesztelheti a **Private.contoso.com** saját zónájának névfeloldását.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Virtuális gépek konfigurálása a befelé irányuló ICMP-forgalom engedélyezésére

A névfeloldás teszteléséhez használhatja a ping parancsot. Ehhez konfigurálja mindkét virtuális gépen a tűzfalat arra, hogy engedélyezze a bejövő ICMP-csomagokat.

1. Csatlakozzon a myVM01 virtuális géphez, és nyisson meg egy Windows PowerShell-ablakot rendszergazdai jogosultsággal.
2. Futtassa az alábbi parancsot:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Ismételje meg ezt a myVM02 gép esetében is.

### <a name="ping-the-vms-by-name"></a>Virtuális gépek pingelése név alapján

1. A myVM02 gép Windows PowerShell parancssorából pingelje meg a myVM01 gépet az automatikusan regisztrált gazdagépnév használatával:

   ```powershell
   ping myVM01.private.contoso.com
   ```

   A következőhöz hasonló kimenetnek kell megjelennie:

   ```output
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Most pingelje meg a korábban létrehozott **db** nevet:

   ```powershell
   ping db.private.contoso.com
   ```

   A következőhöz hasonló kimenetnek kell megjelennie:

   ```output
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a **MyAzureResourceGroup** erőforráscsoportot az ebben a rövid útmutatóban létrehozott erőforrások törléséhez.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure DNS privát zónák forgatókönyvei](private-dns-scenarios.md)

