---
title: DNS-zónák kezelése a Azure DNSban – Azure CLI | Microsoft Docs
description: A DNS-zónák az Azure CLI használatával kezelhetők. Ez a cikk bemutatja, hogyan frissíthet, törölhet és hozhat létre DNS-zónákat Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d902e0172a048527ce8f2fa9e22c5fc9bf22e0b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203623"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Azure DNS DNS-zónák kezelése az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


Ez az útmutató bemutatja, hogyan kezelheti a DNS-zónákat a platformfüggetlen Azure CLI használatával, amely Windows, Mac és Linux rendszereken is elérhető. A DNS-zónákat [Azure PowerShell](dns-operations-dnszones.md) vagy a Azure Portal használatával is kezelheti.

Ez az útmutató kifejezetten a nyilvános DNS-zónákat tárgyalja. További információ az Azure CLI Azure DNS-beli privát zónák kezeléséhez való használatáról: Ismerkedés [a Azure DNS Private Zones az Azure CLI használatával](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Bevezetés

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Az Azure parancssori felület (CLI) beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Telepítse az Azure parancssori felület (CLI) legújabb verzióját, amely Windows, Linux és Mac platformon is elérhető. További információt az [Azure parancssori felület (CLI) telepítése](/cli/azure/install-az-cli2) című cikkben olvashat.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyisson meg egy konzolablakot, adja meg a saját hitelesítő adatait. További információt az [Azure parancssori felületből (CLI) Azure-ba történő bejelentkezést ismertető cikkben](/cli/azure/authenticate-azure-cli) talál.

```
az login
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést

Keresse meg a fiókot az előfizetésekben.

```
az account list
```

Válassza ki, hogy melyek Azure-előfizetését használja.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Választható lehetőség: Azure DNS Private Zones funkció telepítése/használata
Az Azure DNS privát zóna funkció az Azure CLI-n keresztül érhető el egy bővítménnyel. A „dns” Azure CLI-bővítmény telepítése 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Segítség kérése

A Azure DNS kapcsolatos összes Azure CLI-parancs a következővel kezdődik: `az network dns` . A súgó minden parancshoz elérhető a `--help` következő lehetőség használatával: (rövid űrlap `-h` ).  Például:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. További segítségért lásd: `az network dns zone create -h`.

A következő példa egy *contoso.com* nevű DNS-zónát hoz létre az *MyResourceGroup* nevű erőforráscsoporthoz:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>DNS-zóna létrehozása címkékkel

Az alábbi példa bemutatja, hogyan hozhat létre egy DNS-zónát két [Azure Resource Manager címkével](dns-zones-records.md#tags), a *Project = bemutatóval* és az *env = testtel* a `--tags` paraméter (rövid formátum `-t` ) használatával:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>DNS-zóna beszerzése

DNS-zóna lekéréséhez használja a következőt: `az network dns zone show` . További segítségért lásd: `az network dns zone show --help`.

A következő példa a DNS-zóna *contoso.com* és a hozzá tartozó, az erőforráscsoport *MyResourceGroup* tartozó adatait adja vissza. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

A következő példa a válasz.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Vegye figyelembe, hogy az `az network dns zone show` nem ad vissza DNS-rekordokat. A DNS-rekordok listázásához az `az network dns record-set list` használható.


## <a name="list-dns-zones"></a>DNS-zónák listázása

A DNS-zónák felsorolásához használja az `az network dns zone list` parancsot. További segítségért lásd: `az network dns zone list --help`.

Az erőforráscsoport megadásakor a rendszer csak az adott csoporton belüli zónákat sorolja fel:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Az erőforráscsoport kihagyásakor a rendszer az előfizetésben található összes zónát felsorolja:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS-zóna frissítése

A DNS-zóna erőforrásai az `az network dns zone update` paranccsal módosíthatók. További segítségért lásd: `az network dns zone update --help`.

Ez a parancs nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd: [DNS-rekordok kezelése](dns-operations-recordsets-cli.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Ezek a tulajdonságok jelenleg a zóna-erőforráshoz tartozó ["címkék" Azure Resource Manager](dns-zones-records.md#tags) korlátozódnak.

Az alábbi példa bemutatja, hogyan frissítheti a címkéket egy DNS-zónában. A meglévő címkéket a megadott érték váltja fel.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

A DNS-zónák törléséhez használja az `az network dns zone delete` parancsot. További segítségért lásd: `az network dns zone delete --help`.

> [!NOTE]
> A DNS-zónák törlésével a zónában található összes DNS-rekord is törlődni fog. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zónát használó szolgáltatások futtatása meghiúsul a zóna törlésekor.
>
>A zónák véletlen törlése elleni védelemről további információt talál a [DNS-zónák és -rekordok védelme](dns-protect-zones-recordsets.md) című szakaszban.

Ez a parancs megerősítést kér. A választható `--yes` kapcsoló kiiktatja a megerősítés kérését.

Az alábbi példa bemutatja, hogyan törölheti a zóna *contoso.com* az erőforráscsoport *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti a rekordhalmazokat [és rekordokat](./dns-getstarted-cli.md) a DNS-zónában.

Ismerje meg, hogyan [delegálhatja a tartományt Azure DNSre](dns-domain-delegation.md).