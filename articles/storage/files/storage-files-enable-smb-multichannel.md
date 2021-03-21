---
title: Többcsatornás SMB engedélyezése
description: Ismerje meg, hogyan engedélyezheti a többcsatornás SMB-t az Azure Premium-fájlmegosztás esetében.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732538"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Többcsatornás SMB engedélyezése FileStorage-fiókban (előzetes verzió) 

Az Azure FileStorage-fiókok támogatják a többcsatornás SMB-t (előzetes verzió), ami növeli az SMB 3. x ügyfél teljesítményét úgy, hogy több hálózati kapcsolatot létesít a prémium szintű fájlmegosztás számára. Ez a cikk részletes útmutatást nyújt a többcsatornás SMB egy meglévő Storage-fiókban való engedélyezéséhez. Azure Files többcsatornás SMB-vel kapcsolatos részletes információkért lásd: többcsatornás SMB-teljesítmény.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy FileStorage-fiókot](./storage-how-to-create-file-share.md).
- Ha a Azure PowerShell modult szeretné használni, [telepítse a modul 3.0.1-Preview verzióját](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Első lépések

Nyisson meg egy PowerShell-ablakot, és jelentkezzen be az Azure-előfizetésbe. Innen regisztrálhat a többcsatornás SMB előzetes verziójára az alábbi parancsokkal.

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
> A regisztráció akár egy óráig is eltarthat.

### <a name="verify-that-feature-registration-is-complete"></a>A szolgáltatás regisztrációjának ellenőrzése befejeződött

Mivel akár egy órát is igénybe vehet, hogy engedélyezze a szolgáltatást a Storage-fiókjában, a következő paranccsal ellenőrizheti, hogy regisztrálva van-e az előfizetéséhez:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Többcsatornás SMB engedélyezése 
Miután létrehozott egy FileStorage-fiókot, kövesse az utasításokat a Storage-fiókhoz tartozó többcsatornás SMB-beállítások frissítéséhez.

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be a Azure Portalba, és navigáljon ahhoz a FileStorage Storage-fiókhoz, amelyen be szeretné állítani a többcsatornás SMB-t.
1. Válassza **a fájlmegosztás lehetőséget** a **Fájlszolgáltatások** területen, majd válassza a **fájlmegosztás beállításai** lehetőséget.
1. Állítsa be a **többcsatornás SMB** -t **a be** (vagy **ki** a letiltáshoz), majd válassza a **Mentés** lehetőséget.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Képernyőfelvétel a Storage-fiókról: a többcsatornás SMB bekapcsolva.":::

Ha a többcsatornás SMB lehetőség nem látható a **fájlmegosztás beállításai** között, vagy ha nem sikerül frissíteni a beállítási hibát a konfiguráció frissítése közben, győződjön meg arról, hogy az előfizetés regisztrálva van, és a fiókja az egyik [támogatott régióban](#regional-availability) támogatott fióktípus és replikálás.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a többcsatornás SMB-t a Azure PowerShell modul használatával szeretné engedélyezni, [telepítenie kell a modul 3.0.1-Preview verzióját](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) .

Adja meg a változókat `$resourceGroupName` és az `$storageAccountName` erőforráscsoportot és a Storage-fiókot a PowerShell-parancsok futtatása előtt.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az Azure CLI még nem támogatja a többcsatornás SMB konfigurálását. Tekintse meg a portál utasításait a többcsatornás SMB konfigurálásához a Storage-fiókban.

---

> [!NOTE]
> A többcsatornás SMB konfigurációs beállításainak módosítása a Storage-fiókban lévő összes fájlmegosztás esetében érvényes lesz. A módosítások életbe léptetéséhez újra kell csatlakoztatnia a megosztást az ügyfélen.


## <a name="next-steps"></a>Következő lépések 

- [Csatlakoztassa újra a fájlmegosztást](storage-how-to-use-files-windows.md) , hogy kihasználhassa a többcsatornás SMB előnyeit.
- A [többcsatornás SMB-vel kapcsolatos problémák elhárítása](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- További információ a tökéletesítésekről: [többcsatornás SMB-teljesítmény](storage-files-smb-multichannel-performance.md)
 - További információ a Windows SMB többcsatornás szolgáltatásáról: az [SMB-Mulitchannel kezelése](/azure-stack/hci/manage/manage-smb-multichannel).