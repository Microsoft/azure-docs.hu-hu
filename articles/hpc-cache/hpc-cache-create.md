---
title: Hozzon létre egy Azure HPC Cache
description: Új Azure HPC Cache létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 02934a1943ef37d282dd2a2e7862c5695bbd6ecb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862704"
---
# <a name="create-an-azure-hpc-cache"></a>Hozzon létre egy Azure HPC Cache

A gyorsítótár Azure Portal vagy az Azure CLI használatával hozhatja létre.

![képernyőkép a gyorsítótár áttekintési Azure Portal lap alján található Létrehozás gombbal](media/hpc-cache-home-page.png)

Az alábbi képre kattintva tekintse meg [a](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) gyorsítótár létrehozását és a tárolási cél hozzáadását bemutató videót.

[![video thumbnail: Azure HPC Cache: Beállítás (kattintson ide a videó oldalának megjelenítéséhez)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portál](#tab/azure-portal)

## <a name="define-basic-details"></a>Alapvető részletek meghatározása

![képernyőkép a projekt részletei lapról a Azure Portal](media/hpc-cache-create-basics.png)

A **Project Details (Projekt részletei) mezőben** válassza ki a gyorsítótárat tároló előfizetést és erőforráscsoportot.

A **Szolgáltatás adatai alatt** adja meg a gyorsítótár nevét és az alábbi attribútumokat:

* Hely – Válassza ki az egyik [támogatott régiót.](hpc-cache-overview.md#region-availability)
* Virtuális hálózat – Kiválaszthat egy meglévőt, vagy létrehozhat egy új virtuális hálózatot.
* Alhálózat – Válasszon ki vagy hozzon létre egy alhálózatot legalább 64 IP-címmel (/24). Ezt az alhálózatot csak ehhez a Azure HPC Cache kell használni.

## <a name="set-cache-capacity"></a>Gyorsítótár-kapacitás beállítása
<!-- referenced from GUI - update aka.ms link if you change this header text -->

A Gyorsítótár **lapon** be kell állítania a gyorsítótár kapacitását. Az itt beállított értékek határozzák meg, hogy a gyorsítótár mennyi adatot képes tartalmazni, és milyen gyorsan képes az ügyfélkérések kiszolgálására.

A kapacitás a gyorsítótár költségeit is befolyásolja.

Válassza ki a kapacitást a következő két érték beállításával:

* A gyorsítótár maximális adatátviteli sebessége (átviteli sebesség) GB/másodpercben
* A gyorsítótárazott adatokhoz lefoglalt tárterület mennyisége TB-ban

Válassza ki az elérhető átviteli sebességértékek és gyorsítótár-tárolóméretek valamelyikét.

Ne feledje, hogy a tényleges adatátviteli sebesség a számítási feladatoktól, a hálózati sebességtől és a céltárolók típusától függ. A választott értékek a teljes gyorsítótárrendszer maximális átviteli sebességét állítják, de ezek némelyike többletfeladatok esetén használatos. Ha például egy ügyfél olyan fájlt kér, amely még nincs tárolva a gyorsítótárban, vagy ha a fájl elavultként van megjelölve, a gyorsítótár az átviteli sebesség egy része alapján lekéri azt a háttértárból.

Azure HPC Cache a gyorsítótárazott és előre betöltött fájlokat kezeli a gyorsítótár találati arányának maximalizálása érdekében. A rendszer folyamatosan értékeli a gyorsítótár tartalmát, és a fájlokat a rendszer áthelyezi a hosszú távú tárolóba, amikor ritkábban férnek hozzá. Olyan gyorsítótár-tárolóméretet válasszon, amely kényelmesen képes a munkafájlok aktív készletének tárolására, valamint további helyet biztosít a metaadatoknak és egyéb többletterhelésnek.

![a gyorsítótár méretezési oldalának képernyőképe](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Titkosítás Azure Key Vault engedélyezése (nem kötelező)

A **Lemeztitkosítási** kulcsok  lap a Gyorsítótár és a Címkék **lap között jelenik** meg.<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Ha kezelni szeretné a gyorsítótár-tárolóhoz használt titkosítási kulcsokat, Azure Key Vault a Lemeztitkosítási kulcsok **lapon.** A kulcstartónak ugyanabban a régióban és előfizetésben kell lennie, mint a gyorsítótárnak.

Ha nincs szüksége ügyfél által kezelt kulcsokra, kihagyhatja ezt a szakaszt. Az Azure alapértelmezés szerint a Microsoft által kezelt kulcsokkal titkosítja az adatokat. További információért olvassa el [az Azure Storage-titkosítást.](../storage/common/storage-service-encryption.md)

> [!NOTE]
>
> * A Gyorsítótár létrehozása után a Microsoft által kezelt és az ügyfél által kezelt kulcsok között nem lehet módosítani.
> * A gyorsítótár létrehozása után engedélyeznie kell a kulcstartóhoz való hozzáférést. Kattintson a **Titkosítás engedélyezése** gombra a gyorsítótár Áttekintés **lapján** a titkosítás bekapcsoláshoz. Ezt a lépést a gyorsítótár létrehozása után 90 percen belül kell meghozni.
> * A gyorsítótárlemezek az engedélyezés után létrejönnek. Ez azt jelenti, hogy a kezdeti gyorsítótár-létrehozási idő rövid, de a hozzáférés-jogosultságot követően tíz vagy több percig nem áll készen a használatra.

Az ügyfél által felügyelt kulcstitkosítási folyamat teljes magyarázatát az [Ügyfél](customer-keys.md)által felügyelt titkosítási kulcsok használata a Azure HPC Cache.

![képernyőkép a titkosítási kulcsok oldalról, az "ügyfél által felügyelt" kiválasztva és a Kulcstartó mezőkkel](media/create-encryption.png)

Válassza **az Ügyfél által felügyelt** lehetőséget az ügyfél által felügyelt kulcstitkosítás kiválasztásához. Megjelennek a Key Vault specifikációi mezői. Válassza ki Azure Key Vault használni kívánt kulcsot, majd válassza ki a gyorsítótárhoz használni kívánt kulcsot és verziót. A kulcsnak 2048 bites RSA-kulcsnak kell lennie. Ezen az oldalon hozhat létre új kulcstartót, kulcsot vagy kulcsverziót.

A gyorsítótár létrehozása után engedélyeznie kell számára a Key Vault szolgáltatás használatát. Részletekért [olvassa Azure Key Vault titkosítás gyorsítótárból való titkosításának engedélyét.](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)

## <a name="add-resource-tags-optional"></a>Erőforráscímkék hozzáadása (nem kötelező)

A **Címkék** lapon [erőforráscímkéket adhat a](../azure-resource-manager/management/tag-resources.md) Azure HPC Cache példányhoz.

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson az Áttekintés **és létrehozás fülre.** A portál ellenőrzi a választásokat, és lehetővé teszi a választási lehetőségek áttekintését. Ha minden rendben van, kattintson a **Létrehozás gombra.**

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. A folyamat előrehaladását az értesítési Azure Portal panelen követheti nyomon.

![képernyőkép a gyorsítótár-létrehozás "üzembe helyezés folyamatban" és az "értesítések" oldalról a portálon](media/hpc-cache-deploy-status.png)

Amikor a létrehozás befejeződik, megjelenik egy értesítés az új Azure HPC Cache-példányra mutató hivatkozással, és a gyorsítótár megjelenik az előfizetés Erőforrások **listájában.**

![képernyőkép az Azure HPC Cache-példányról Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Ha a gyorsítótár ügyfél által felügyelt titkosítási kulcsokat használ, előfordulhat, hogy a gyorsítótár megjelenik az erőforrások listájában, mielőtt az üzembe helyezés állapota megváltozik. Amint a gyorsítótár Állapota **Kulcsra** vár, [](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) engedélyezheti a kulcstartó használatát.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Gyorsítótár létrehozása az Azure CLI használatával

[Az Azure CLI beállítása a Azure HPC Cache.](./az-cli-prerequisites.md)

> [!NOTE]
> Az Azure CLI jelenleg nem támogatja a gyorsítótár ügyfél által felügyelt titkosítási kulcsokkal való létrehozását. Használja az Azure Portalt.

Az [az hpc-cache create paranccsal](/cli/azure/hpc-cache#az_hpc_cache_create) hozzon létre egy új Azure HPC Cache.

A következő értékeket kell megszabadni:

* Gyorsítótár erőforráscsoport-neve
* Gyorsítótár neve
* Azure-régió
* Gyorsítótárazza az alhálózatot a következő formátumban:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  A gyorsítótár alhálózatának legalább 64 IP-címre (/24) van szüksége, és nem tud más erőforrásokat eltárni.

* Gyorsítótár-kapacitás. Két érték adja meg a maximális átviteli sebességet a Azure HPC Cache:

  * A gyorsítótár mérete (GB-ban)
  * A gyorsítótár-infrastruktúrában használt virtuális gépek termékváltozata

  [Az az hpc-cache skus list](/cli/azure/hpc-cache/skus) az elérhető termékkódokat és az egyes gyorsítótárak érvényes méretbeállításokat jeleníti meg. A gyorsítótár méretének beállításai 3 TB és 48 TB között vannak, de csak néhány érték támogatott.

  Ezen a diagramon látható, hogy mely gyorsítótárméret- és termékváltozat-kombinációk érvényesek a dokumentum elkészítésekor (2020. július).

  | Gyorsítótár mérete | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | igen         | nem          | nem          |
  | 6144 GB    | igen         | igen         | nem          |
  | 12288 GB   | igen         | igen         | igen         |
  | 24576 GB   | nem          | igen         | igen         |
  | 49152 GB   | nem          | nem          | igen         |

  Olvassa **el** a Portál utasításai lap Gyorsítótár-kapacitás beállítása szakaszát a díjszabással, az átviteli sebességtel és a gyorsítótár munkafolyamatnak megfelelő méretezésével kapcsolatos fontos információkért.

Gyorsítótár-létrehozási példa:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

A gyorsítótár létrehozása eltarthat néhány percig. Sikeres létrehozás esetén a create parancs a következő kimenetet adja vissza:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Az üzenet hasznos információkat tartalmaz, többek között az alábbiakat:

* Ügyfél csatlakoztatási címei – Akkor használja ezeket az IP-címeket, ha készen áll az ügyfelek gyorsítótárhoz csatlakoztatására. További [információ: Azure HPC Cache](hpc-cache-mount.md) csatlakoztatása.
* Frissítési állapot – A szoftverfrissítések kiadott állapotában ez az üzenet megváltozik. A [gyorsítótárszoftvereket](hpc-cache-manage.md#upgrade-cache-software) frissítheti manuálisan, megfelelő időpontban, vagy több nap után automatikusan alkalmazva lesz.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az Az.HPCache PowerShell-modul jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés. Az előzetes verzió használata NEM javasolt éles számítási feladatok esetén. Előfordulhat, hogy egyes funkciók nem támogatottak, vagy korlátozott képességekkel rendelkeznek. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az Az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. További információ az Az PowerShell-modul telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps). Ha úgy dönt, hogy a Cloud Shell használja, további Azure Cloud Shell [áttekintését.](../cloud-shell/overview.md)

> [!IMPORTANT]
> Bár az **Az.HPCCache** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával. Miután ez a PowerShell-modul általánosan elérhetővé válik, a későbbi Az PowerShell-modulok kiadásának része lesz, és natív módon érhető el a Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Hozza létre a gyorsítótárat Azure PowerShell

> [!NOTE]
> Azure PowerShell jelenleg nem támogatja a gyorsítótár ügyfél által felügyelt titkosítási kulcsokkal való létrehozását. Használja az Azure Portalt.

A [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) parancsmag használatával hozzon létre egy új Azure HPC Cache.

Adja meg a következő értékeket:

* Gyorsítótár erőforráscsoport-neve
* Gyorsítótár neve
* Azure-régió
* Gyorsítótárazza az alhálózatot a következő formátumban:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  A gyorsítótár alhálózatának legalább 64 IP-címre (/24) van szüksége, és nem tud más erőforrásokat eltárni.

* Gyorsítótár-kapacitás. Két érték adja meg a maximális átviteli sebességet a Azure HPC Cache:

  * A gyorsítótár mérete (GB-ban)
  * A gyorsítótár-infrastruktúrában használt virtuális gépek termékváltozata

  [A Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) megjeleníti az elérhető termékváltozatokat és az egyes gyorsítótárak érvényes méretének beállításait. A gyorsítótár méretének beállításai 3 TB és 48 TB között vannak, de csak néhány érték támogatott.

  Ezen a diagramon látható, hogy a dokumentum elkészítésekor (2020. július) mely gyorsítótárméret- és termékváltozat-kombinációk érvényesek.

  | Gyorsítótár mérete | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | igen         | nem          | nem          |
  | 6144 GB    | igen         | igen         | nem          |
  | 12 288 GB   | igen         | igen         | igen         |
  | 24 576 GB   | nem          | igen         | igen         |
  | 49 152 GB   | nem          | nem          | igen         |

  Olvassa **el** a Portál utasításai lap Gyorsítótár-kapacitás beállítása szakaszát a díjszabással, az átviteli sebességtel és a gyorsítótár munkafolyamatnak megfelelő méretezésével kapcsolatos fontos információkért.

Gyorsítótár-létrehozási példa:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

A gyorsítótár létrehozása eltarthat néhány percig. Sikeres létrehozás esetén a create parancs a következő kimenetet adja vissza:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Az üzenet hasznos információkat tartalmaz, többek között az alábbiakat:

* Ügyfél csatlakoztatási címei – Akkor használja ezeket az IP-címeket, ha készen áll az ügyfelek gyorsítótárhoz csatlakoztatására. További [információ: Azure HPC Cache](hpc-cache-mount.md) csatlakoztatása.
* Frissítés állapota – A szoftverfrissítések kiadott állapotában ez az üzenet megváltozik. A [gyorsítótárszoftvereket](hpc-cache-manage.md#upgrade-cache-software) frissítheti manuálisan, kényelmes időpontban, vagy több nap után automatikusan is alkalmazható.

---

## <a name="next-steps"></a>Következő lépések

Miután a gyorsítótár megjelenik az **Erőforrások listában,** továbbléphet a következő lépésre.

* [A céltárolók meghatározásával](hpc-cache-add-storage.md) hozzáférést adhat a gyorsítótárnak az adatforráshoz.
* Ha ügyfél által felügyelt titkosítási kulcsokat használ, engedélyeznie kell Azure Key Vault titkosítást a gyorsítótár áttekintési oldalán a gyorsítótár beállításának befejezéséhez. [](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) Ezt a lépést a tároló hozzáadása előtt kell megtennie. További [információ: Ügyfél által felügyelt titkosítási](customer-keys.md) kulcsok használata.
