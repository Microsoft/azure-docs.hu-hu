---
title: Többcsatornás SMB engedélyezése
description: Megtudhatja, hogyan engedélyezheti a többcsatornás SMB-t prémium szintű Azure-fájlmegosztások esetében.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da4e1a58aef28e5c47100a0311ff81a5af04a918
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718977"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Többcsatornás SMB engedélyezése fileStorage-fiókon (előzetes verzió) 

Az Azure FileStorage-fiókok támogatják a többcsatornás SMB -t (előzetes verzió), amely növeli a teljesítményt egy SMB 3.x-ügyféltől azáltal, hogy több hálózati kapcsolatot hoz létre a prémium fájlmegosztásokkal. Ez a cikk részletes útmutatást nyújt a többcsatornás SMB meglévő tárfiókon való engedélyezéséhez. A többcsatornás SMB Azure Files részletes információkért lásd: Többcsatornás SMB teljesítménye.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy FileStorage fiókot.](./storage-how-to-create-file-share.md)
- Ha a Azure PowerShell modult szeretné használni, telepítse a modul [3.0.1-es](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview)előzetes verzióját.

## <a name="getting-started"></a>Első lépések

Nyisson meg egy PowerShell-ablakot, és jelentkezzen be Az Azure-előfizetésbe. Innen regisztrálhat a többcsatornás SMB előzetes verzióra az alábbi parancsokkal.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> A regisztráció akár egy órát is előfordulhat.

### <a name="verify-that-feature-registration-is-complete"></a>A szolgáltatásregisztráció befejezésének ellenőrzése

Mivel a szolgáltatás tárfiókon való engedélyezése akár egy órát is el is vehet, a következő paranccsal ellenőrizheti, hogy regisztrálva van-e az előfizetésében:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Többcsatornás SMB engedélyezése 
Miután létrehozott egy FileStorage-fiókot, kövesse az utasításokat a tárfiók többcsatornás SMB-beállításainak frissítéséhez.

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be a Azure Portal, és lépjen arra a FileStorage tárfiókra, amelybe a többcsatornás SMB-t konfigurálni szeretné.
1. A **Fájlszolgáltatás alatt** válassza a **Fájlmegosztások** lehetőséget, majd a **Fájlmegosztás beállításai lehetőséget.**
1. Kapcsolja be a **Többcsatornás SMB-t** **(vagy** kapcsolja ki a letiltásához), majd válassza a **Mentés lehetőséget.** 

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Képernyőkép a tárfiókról, amely bekapcsolja a többcsatornás SMB-t."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Ha a Többcsatornás SMB beállítás  nem látható a Fájlmegosztás beállításai között, vagy a konfiguráció frissítése közben sikertelenül frissülő beállítási [](#regional-availability) hibát kap, győződjön meg arról, hogy az előfizetés regisztrálva van, és a fiókja a támogatott fióktípussal és replikációval támogatott régiók valamelyikében van.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a többcsatornás SMB-t a Azure PowerShell modullal szeretné engedélyezni, telepítenie kell a modul [3.0.1-es](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) előzetes verzióját.

A PowerShell-parancsok futtatása előtt állítsa be az és a változót az erőforráscsoportra és a `$resourceGroupName` `$storageAccountName` tárfiókra.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az Azure CLI még nem támogatja a többcsatornás SMB konfigurálását. A portál utasításait követve konfigurálhatja a többcsatornás SMB-t a tárfiókon.

---

> [!NOTE]
> A többcsatornás SMB konfigurációs beállításainak bármilyen módosítása a tárfiók alatt található összes fájlmegosztásra érvényes lesz. Ahhoz azonban, hogy a módosítások életbe lépnek, újra le kell szerelni a megosztást az ügyfélen.


## <a name="next-steps"></a>Következő lépések 

- [A többcsatornás](storage-how-to-use-files-windows.md) SMB előnyeinek kihasználása után újra kell csatolnia a fájlmegosztást.
- Hárítsa el a többcsatornás [SMB-hez kapcsolódó problémákat.](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)
- A fejlesztésekkel kapcsolatos további információkért lásd: [Többcsatornás SMB-teljesítmény](storage-files-smb-multichannel-performance.md)
 - További információ a Többcsatornás Windows SMB szolgáltatásról: [Manage SMB Mulitchannel ( Többcsatornás SMB kezelése).](/azure-stack/hci/manage/manage-smb-multichannel)