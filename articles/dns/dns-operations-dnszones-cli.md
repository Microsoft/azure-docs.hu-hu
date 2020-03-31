---
title: DNS-zónák kezelése az Azure DNS-ben - Azure CLI | Microsoft dokumentumok
description: A DNS-zónák at az Azure CLI használatával kezelheti. Ez a cikk bemutatja, hogyan frissítheti, törölheti és hozhat létre DNS-zónákat az Azure DNS-en.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: rohink
ms.openlocfilehash: 413c2ab3ee04249c2bb52bf42ca6a31a58fb9082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936932"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Dns-zónák kezelése az Azure DNS-ben az Azure CLI használatával

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


Ez az útmutató bemutatja, hogyan kezelheti a DNS-zónák segítségével a platformfüggetlen Azure CLI, amely elérhető a Windows, Mac és Linux. A DNS-zónák is kezelheti az [Azure PowerShell](dns-operations-dnszones.md) vagy az Azure Portal használatával.

Ez az útmutató kifejezetten a nyilvános DNS-zónákkal foglalkozik. Az Azure CLI azure-beli DNS-zónák kezeléséhez való használatáról az [Azure DNS-t használó magánzónák használatának első lépései](private-dns-getstarted-cli.md)című témakörben talál.

## <a name="introduction"></a>Bevezetés

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Az Azure parancssori felület (CLI) beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Telepítse az Azure parancssori felület (CLI) legújabb verzióját, amely Windows, Linux és Mac platformon is elérhető. További információt az [Azure parancssori felület (CLI) telepítése](https://docs.microsoft.com/cli/azure/install-az-cli2) című cikkben olvashat.

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyisson meg egy konzolablakot, adja meg a saját hitelesítő adatait. További információt az [Azure parancssori felületből (CLI) Azure-ba történő bejelentkezést ismertető cikkben](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) talál.

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

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Nem kötelező: Az Azure DNS-alapú személyes zónák szolgáltatástelepítése/használata
Az Azure DNS-beli privát zóna szolgáltatás az Azure CLI kiterjesztésén keresztül érhető el. A „dns” Azure CLI-bővítmény telepítése 
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

Az Azure DNS-hez kapcsolódó összes `az network dns`Azure CLI-parancs a . A súgó minden parancshoz `--help` elérhető a `-h`(rövid űrlap) segítségével.  Példa:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `az network dns zone create` parancs használatával hozható létre. További segítségért lásd: `az network dns zone create -h`.

A következő példa létrehoz egy *contoso.com* nevű DNS-zónát a *MyResourceGroup*nevű erőforráscsoportban:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>DNS-zóna létrehozása címkékkel

A következő példa bemutatja, hogyan hozhat létre DNS-zónát két [Azure Resource Manager címkével](dns-zones-records.md#tags), projekt = *demo* és *env = teszt*, a `--tags` paraméter használatával (rövid űrlap): `-t`

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>DNS-zóna beszerezni

DNS-zóna beolvasásához `az network dns zone show`használja a használatát. További segítségért lásd: `az network dns zone show --help`.

A következő példa a DNS-zónát *contoso.com* és a hozzá tartozó adatokat adja vissza a *MyResourceGroup*erőforráscsoportból. 

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

Ez a parancs nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd: [DNS-rekordok kezelése](dns-operations-recordsets-cli.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Ezek a tulajdonságok jelenleg az [Azure Resource Manager "címkék"](dns-zones-records.md#tags) a zóna erőforrás.

A következő példa bemutatja, hogyan frissítheti a címkéket egy DNS-zónában. A meglévő címkéket a megadott érték váltja fel.

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

A következő példa bemutatja, hogyan törölheti a zóna *contoso.com* a *MyResourceGroup*erőforráscsoportból.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>További lépések

További információ a dns-zónában lévő [rekordkészletek és rekordok kezeléséről.](dns-getstarted-create-recordset-cli.md)

Ismerje meg, hogyan [delegálhatja tartományát az Azure DNS-re.](dns-domain-delegation.md)

