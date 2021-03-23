---
title: Azure HPC-gyorsítótár létrehozása
description: Azure HPC cache-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 63a179f7123f088c9c60fbfb8996a5b21d1c704d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773266"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC-gyorsítótár létrehozása

A gyorsítótár létrehozásához használja a Azure Portal vagy az Azure CLI-t.

![képernyőkép a gyorsítótár áttekintéséről Azure Portalban, a létrehozás gombbal alul](media/hpc-cache-home-page.png)

Az alábbi képre kattintva megtekintheti a gyorsítótár létrehozásának és a tárolási cél hozzáadásának [bemutató videóját](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) .

[![videó miniatűrje: Azure HPC cache: Setup (kattintson ide a videó oldal megtekintéséhez)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portál](#tab/azure-portal)

## <a name="define-basic-details"></a>Alapszintű részletek meghatározása

![képernyőkép a Project details lapról Azure Portal](media/hpc-cache-create-basics.png)

A **projekt részletei** területen válassza ki azt az előfizetést és erőforráscsoportot, amely a gyorsítótárat fogja tárolni.

A **szolgáltatás részletei** területen adja meg a gyorsítótár nevét és a többi attribútumot:

* Hely – válasszon egy [támogatott régiót](hpc-cache-overview.md#region-availability).
* Virtuális hálózat – választhat egy meglévőt, vagy létrehozhat egy új virtuális hálózatot.
* Alhálózat – válasszon ki vagy hozzon létre legalább 64 IP-címmel rendelkező alhálózatot (/24). Ezt az alhálózatot csak ehhez az Azure HPC cache-példányhoz kell használni.

## <a name="set-cache-capacity"></a>Gyorsítótár kapacitásának beállítása
<!-- referenced from GUI - update aka.ms link if you change this header text -->

A **gyorsítótár** lapon be kell állítania a gyorsítótár kapacitását. Az itt megadott értékek határozzák meg, hogy a gyorsítótár milyen mennyiségű adattal rendelkezhet, és hogy milyen gyorsan lehet az ügyfelek kéréseinek kiszolgálására.

A kapacitás a gyorsítótár költségeit is befolyásolja.

Válassza ki a kapacitást a következő két érték beállításával:

* A gyorsítótár (átviteli sebesség) maximális adatátviteli sebessége GB/másodpercben
* A gyorsítótárazott adathoz lefoglalt tárterület mennyisége (TB)

Válassza ki az elérhető átviteli sebesség és a gyorsítótár tárolási méretének egyikét.

Ne feledje, hogy a tényleges adatátviteli sebesség a munkaterhelés, a hálózati sebesség és a tárolási célok típusától függ. A megadott értékek a teljes gyorsítótárrendszer maximális átviteli sebességét határozzák meg, de ezek közül néhányat a rendszer a terhelési feladatokhoz használ. Ha például egy ügyfél olyan fájlt kér, amely még nem található meg a gyorsítótárban, vagy ha a fájl elavultként van megjelölve, a gyorsítótár a saját átviteli sebességét használja a háttérbeli tárolóból való beolvasáshoz.

Az Azure HPC gyorsítótára felügyeli, hogy mely fájlok vannak gyorsítótárazva és előre betöltve a gyorsítótár találati arányának maximalizálása érdekében. A gyorsítótár tartalmának ellenőrzése folyamatosan történik, és a fájlok átkerülnek a hosszú távú tárolásba, ha ritkábban férnek hozzá. Válasszon egy olyan gyorsítótár-tárolási méretet, amely kényelmesen tárolhatja a munkafájlok aktív készletét, valamint további lemezterületet biztosít a metaadatok és egyéb terhelések számára.

![a gyorsítótár-méretezés oldalának képernyőképe](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault titkosítás engedélyezése (nem kötelező)

A **lemez titkosítási kulcsainak** lapja a **gyorsítótár** és a **címkék** lapok között jelenik meg.<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Ha szeretné kezelni a gyorsítótár-tárolóhoz használt titkosítási kulcsokat, adja meg a Azure Key Vault adatait a **lemez titkosítási kulcsainak** oldalán. A kulcstárolónak ugyanabban a régióban és ugyanabban az előfizetésben kell lennie, mint a gyorsítótárnak.

Ezt a szakaszt kihagyhatja, ha nincs szüksége az ügyfél által felügyelt kulcsokra. Alapértelmezés szerint az Azure a Microsoft által felügyelt kulcsokkal titkosítja az adataikat. További információért olvassa el az [Azure Storage-titkosítást](../storage/common/storage-service-encryption.md) ismertető témakört.

> [!NOTE]
>
> * A gyorsítótár létrehozása után nem válthat a Microsoft által felügyelt kulcsok és az ügyfél által felügyelt kulcsok között.
> * A gyorsítótár létrehozása után engedélyeznie kell, hogy hozzáférjen a kulcstartóhoz. Kattintson a titkosítás **engedélyezése** gombra a gyorsítótár **Áttekintés** lapján a titkosítás bekapcsolásához. Ezt a lépést a gyorsítótár létrehozása 90 percen belül elvégezheti.
> * A gyorsítótár-lemezek az engedélyezés után jönnek létre. Ez azt jelenti, hogy a kezdeti gyorsítótár-létrehozási idő rövid, de a gyorsítótár nem áll készen a hozzáférés engedélyezése után tíz vagy több percig.

Az ügyfél által felügyelt kulcs titkosítási folyamat teljes körű ismertetését az [ügyfél által felügyelt titkosítási kulcsok használata az Azure HPC cache-hez című cikk](customer-keys.md)ismerteti.

![a titkosítási kulcsok oldalának képernyőképe a "felhasználó által felügyelt" beállítással és a Key Vault mezőinek megjelenítésével](media/create-encryption.png)

Válassza a **felügyelt ügyfél** lehetőséget az ügyfél által felügyelt kulcs titkosításának kiválasztásához. Megjelenik a Key Vault specifikáció mezői. Válassza ki a használni kívánt Azure Key Vault, majd válassza ki a gyorsítótárhoz használni kívánt kulcsot és verziót. A kulcsnak 2048 bites RSA-kulcsnak kell lennie. Ezen a lapon létrehozhat egy új kulcstartót, kulcsot vagy kulcsot tartalmazó verziót.

A gyorsítótár létrehozása után engedélyeznie kell azt a Key Vault szolgáltatás használatához. További részletek: [Azure Key Vault titkosítás engedélyezése a gyorsítótárból](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Erőforrás-Címkék hozzáadása (nem kötelező)

A **címkék** lapon hozzáadhat erőforrás- [CÍMKÉKET](../azure-resource-manager/management/tag-resources.md) az Azure HPC cache-példányhoz.

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson a **felülvizsgálat + létrehozás** fülre. A portál ellenőrzi a beállításokat, és lehetővé teszi a lehetőségek áttekintését. Ha minden helyes, kattintson a **Létrehozás** gombra.

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. A folyamat nyomon követhető a Azure Portal értesítések paneljén.

![képernyőkép a gyorsítótár létrehozásáról "üzembe helyezés folyamatban" és "értesítések" oldalain a portálon](media/hpc-cache-deploy-status.png)

A létrehozás befejeződése után egy értesítés jelenik meg az új Azure HPC cache-példányra mutató hivatkozással, a gyorsítótár pedig megjelenik az előfizetés **erőforrások** listájában.

![képernyőkép az Azure HPC cache-példányról Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Ha a gyorsítótár ügyfél által felügyelt titkosítási kulcsokat használ, előfordulhat, hogy a gyorsítótár megjelenik az erőforrások listájában, mielőtt a központi telepítés állapota befejeződik. Amint a gyorsítótár állapota a **kulcsra vár** , [engedélyezheti](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a kulcstároló használatát.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>A gyorsítótár létrehozása az Azure CLI-vel

[Állítsa be az Azure CLI-t az Azure HPC cache-hez](./az-cli-prerequisites.md).

> [!NOTE]
> Az Azure CLI jelenleg nem támogatja az ügyfél által felügyelt titkosítási kulcsokkal rendelkező gyorsítótár létrehozását. Használja az Azure Portalt.

Hozzon létre egy új Azure HPC-gyorsítótárat az az [HPC-cache Create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) paranccsal.

Adja meg a következő értékeket:

* Gyorsítótár-erőforráscsoport neve
* Gyorsítótár neve
* Azure-régió
* Gyorsítótár-alhálózat, ebben a formátumban:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  A gyorsítótár-alhálózatnak legalább 64 IP-címet (/24) kell tartalmaznia, és semmilyen más erőforrást nem tud elhelyezni.

* Gyorsítótár kapacitása. Két érték az Azure HPC-gyorsítótár maximális átviteli sebességét állítja be:

  * A gyorsítótár mérete (GB)
  * A gyorsítótár-infrastruktúrában használt virtuális gépek SKU-jának

  az [HPC-cache SKUs List](/cli/azure/ext/hpc-cache/hpc-cache/skus) megjeleníti az elérhető SKU-ket és a gyorsítótár méretének érvényes beállításait. A gyorsítótár méretének beállításai 3 TB és 48 TB közé esnek, de csak néhány érték támogatott.

  Ez a diagram azt jeleníti meg, hogy mely gyorsítótár-méret és SKU-kombináció érvényes a dokumentum előkészítésének időpontjában (július 2020).

  | Gyorsítótár mérete | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | igen         | nem          | nem          |
  | 6144 GB    | igen         | igen         | nem          |
  | 12288 GB   | igen         | igen         | igen         |
  | 24576 GB   | nem          | igen         | igen         |
  | 49152 GB   | nem          | nem          | igen         |

  Olvassa el a **gyorsítótár kapacitásának beállítása** szakaszt a portál utasítások lapján, amely fontos információkat tartalmaz a díjszabásról, az átviteli sebességről és a gyorsítótárnak a munkafolyamathoz való megfelelő méretezéséről.

Gyorsítótár-létrehozási példa:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

A gyorsítótár létrehozása több percet vesz igénybe. Sikeres művelet esetén a Create parancs a következőhöz hasonló kimenetet ad vissza:

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

Az üzenet tartalmaz néhány hasznos információt, beleértve az alábbi elemeket is:

* Ügyfél-csatlakoztatási címek – ezeket az IP-címeket akkor használja, ha készen áll az ügyfelek csatlakoztatására a gyorsítótárhoz. További információért olvassa el [Az Azure HPC cache csatlakoztatása](hpc-cache-mount.md) című témakört.
* Frissítési állapot – a szoftverfrissítés kiadása után ez az üzenet módosul. A [gyorsítótárazási szoftvert manuálisan is frissítheti](hpc-cache-manage.md#upgrade-cache-software) , de néhány nap múlva automatikusan alkalmazza.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az az. HPCCache PowerShell-modul jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés. Az előzetes verzió használata NEM javasolt éles számítási feladatok esetén. Előfordulhat, hogy egyes funkciók nem támogatottak, vagy korlátozott képességekkel rendelkeznek. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps). Ha a Cloud Shell használatát választja, további információt [a Azure Cloud Shell áttekintése](../cloud-shell/overview.md) című témakörben talál.

> [!IMPORTANT]
> Míg az az **. HPCCache** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután a PowerShell-modul általánosan elérhetővé válik, az a PowerShell-modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>A gyorsítótár létrehozása Azure PowerShell

> [!NOTE]
> Azure PowerShell jelenleg nem támogatja az ügyfél által felügyelt titkosítási kulcsokkal rendelkező gyorsítótár létrehozását. Használja az Azure Portalt.

Új Azure HPC-gyorsítótár létrehozásához használja a [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) parancsmagot.

Adja meg az alábbi értékeket:

* Gyorsítótár-erőforráscsoport neve
* Gyorsítótár neve
* Azure-régió
* Gyorsítótár-alhálózat, ebben a formátumban:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  A gyorsítótár-alhálózatnak legalább 64 IP-címet (/24) kell tartalmaznia, és semmilyen más erőforrást nem tud elhelyezni.

* Gyorsítótár kapacitása. Két érték az Azure HPC-gyorsítótár maximális átviteli sebességét állítja be:

  * A gyorsítótár mérete (GB)
  * A gyorsítótár-infrastruktúrában használt virtuális gépek SKU-jának

  A [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) megjeleníti a rendelkezésre álló SKU-ket és a gyorsítótár méretének érvényes beállításait. A gyorsítótár méretének beállításai 3 TB és 48 TB közé esnek, de csak néhány érték támogatott.

  Ez a diagram azt jeleníti meg, hogy mely gyorsítótár-méret és SKU-kombináció érvényes a dokumentum előkészítésének időpontjában (július 2020).

  | Gyorsítótár mérete | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | igen         | nem          | nem          |
  | 6144 GB    | igen         | igen         | nem          |
  | 12 288 GB   | igen         | igen         | igen         |
  | 24 576 GB   | nem          | igen         | igen         |
  | 49 152 GB   | nem          | nem          | igen         |

  Olvassa el a **gyorsítótár kapacitásának beállítása** szakaszt a portál utasítások lapján, amely fontos információkat tartalmaz a díjszabásról, az átviteli sebességről és a gyorsítótárnak a munkafolyamathoz való megfelelő méretezéséről.

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

A gyorsítótár létrehozása több percet vesz igénybe. Sikeres művelet esetén a Create parancs a következő kimenetet adja vissza:

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

Az üzenet tartalmaz néhány hasznos információt, beleértve az alábbi elemeket is:

* Ügyfél-csatlakoztatási címek – ezeket az IP-címeket akkor használja, ha készen áll az ügyfelek csatlakoztatására a gyorsítótárhoz. További információért olvassa el [Az Azure HPC cache csatlakoztatása](hpc-cache-mount.md) című témakört.
* Frissítési állapot – a szoftverfrissítés megjelenésekor ez az üzenet módosul. A [gyorsítótárazási szoftvereket manuálisan is frissítheti](hpc-cache-manage.md#upgrade-cache-software) , de néhány nap múlva automatikusan alkalmazza.

---

## <a name="next-steps"></a>Következő lépések

Miután a gyorsítótár megjelenik az **erőforrások** listájában, átléphet a következő lépésre.

* [Adja meg a tárolási célokat](hpc-cache-add-storage.md) , hogy a gyorsítótár hozzáférjen az adatforrásokhoz.
* Ha ügyfél által felügyelt titkosítási kulcsokat használ, engedélyeznie kell [Azure Key Vault titkosítást](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a gyorsítótár áttekintés lapján a gyorsítótár telepítésének befejezéséhez. Ezt a lépést a tárterület hozzáadása előtt kell végrehajtania. A részletekért olvassa el az [ügyfél által felügyelt titkosítási kulcsok használata](customer-keys.md) című leírást.
