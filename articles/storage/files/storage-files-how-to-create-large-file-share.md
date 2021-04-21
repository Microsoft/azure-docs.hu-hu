---
title: Nagyméretű fájlmegosztások engedélyezése és létrehozása – Azure Files
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és hozhat létre nagyméretű fájlmegosztásokat.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818259"
---
# <a name="enable-and-create-large-file-shares"></a>Nagyméretű fájlmegosztások engedélyezése és létrehozása
Az Azure-fájlmegosztások akár 100 TiB-ra is méretezhetők, miután engedélyezi a nagyméretű fájlmegosztásokat a tárfiókban. Ha engedélyezi a nagyméretű fájlmegosztásokat, az megnövelheti a fájlmegosztás IOPS- és átviteli sebességkorlátját is. A méretezést meglévő és új fájlmegosztások meglévő tárfiókja esetében is engedélyezheti. A teljesítménybeli különbségekkel kapcsolatos részletekért lásd: [fájlmegosztási és fájlskálacélok.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót.](/cli/azure/install-azure-cli)
- Ha az új modult szeretné Azure PowerShell, [telepítse a legújabb verziót.](/powershell/azure/install-az-ps)

## <a name="restrictions"></a>Korlátozások
Jelenleg csak helyileg redundáns tárolást (LRS) vagy zónaredundáns tárolást (ZRS) használhat olyan tárfiókok esetében, amelyeken engedélyezve van a nagy fájlmegosztások használata. Nem használhat georedundáns tárolást (GZRS), georedundáns tárolást (GRS), íráselérésű georedundáns tárolást (RA-GRS) vagy írási hozzáférésű geo-zónaredundáns tárolást (RA-GZRS).

A nagyméretű fájlmegosztások fiókon való engedélyezése visszafordíthatatlan folyamat. Az engedélyezés után nem tudja majd GZRS, GRS, RA-GRS vagy RA-GZRS formátumra konvertálni a fiókját.

## <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal válassza a **Minden szolgáltatás lehetőséget.** 
1. Az erőforrások listájában adja meg a **Tárfiókok listában a következőt:**. A begépelkedő lista a bemenet alapján szűr. Válassza a **Tárfiókok lehetőséget.**
1. A megjelenő **Tárfiókok** panelen válassza az **+ Új lehetőséget.**
1. Az Alapvető beállítások panelen töltse ki a kijelöléseket, ahogy szeretné.
1. Győződjön meg arról, **hogy a Teljesítmény** beállítása **Standard.**
1. Állítsa **a Redundancy (Redundancia)** beállítását helyileg redundáns **tárolásra** vagy **zónaredundáns tárolásra.**
1. Válassza a **Speciális panelt,** majd válassza a Nagy fájlmegosztások jobb oldalon található Engedélyezve **lehetőséget.** 
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A nagyméretű fájlmegosztásokat engedélyező tárfiók létrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` le a , és `<yourResourceGroup>` `<yourDesiredRegion>` helyére a saját adatait.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A nagyméretű fájlmegosztásokat engedélyező tárfiók létrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` le a , és `<yourResourceGroup>` `<yourDesiredRegion>` helyére a saját adatait.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Miután létrehozott egy nagyméretű fájlmegosztást engedélyező tárfiókot, létrehozhat egy olyan fájlmegosztást, amely kihasználja a nagy kapacitás és a méretkorlátok előnyeit. További információ a tárfiókok és az Azure-fájlmegosztások létrehozásáról: [Azure-fájlmegosztás létrehozása.](storage-how-to-create-file-share.md)

## <a name="enable-large-files-shares-on-an-existing-account"></a>Nagyméretű fájlmegosztások engedélyezése meglévő fiókon
A meglévő LRS- és ZRS-tárfiókok esetében is engedélyezheti a nagyméretű fájlmegosztásokat. Ha GRS-, GZRS-, RA-GRS- vagy RA-GZRS-fiókja van, a folytatás előtt át kell alakítania azt LRS-fiókká.

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Nyissa meg [a Azure Portal,](https://portal.azure.com)és keresse meg azt a tárfiókot, ahol engedélyezni szeretné a nagyméretű fájlmegosztásokat.
1. Nyissa meg a tárfiókot, és válassza a **Fájlmegosztások lehetőséget.**
1. Válassza **az Engedélyezve a** nagyméretű **fájlmegosztások esetében lehetőséget,** majd válassza a **Mentés lehetőséget.**
1. Válassza **az Áttekintés,** majd a **Frissítés lehetőséget.**
1. Válassza a **Kapacitás megosztása,** majd a **100 TiB és a** Mentés **lehetőséget.**

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Képernyőkép az Azure Storage-fiók, fájlmegosztások panelről, kiemelt 100 tib megosztással.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A nagyméretű fájlmegosztások meglévő fiókon való engedélyezéséhez használja a következő parancsot. Cserélje le `<yourStorageAccountName>` a és `<yourResourceGroup>` a helyére a saját adatait.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A nagyméretű fájlmegosztások meglévő fiókon való engedélyezéséhez használja a következő parancsot. Cserélje le `<yourStorageAccountName>` a és `<yourResourceGroup>` a helyére a saját adatait.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

Engedélyezte a nagyméretű fájlmegosztásokat a tárfiókon. Ezután frissítenie kell [a meglévő megosztások kvótáját,](#expand-existing-file-shares) hogy kihasználja a nagyobb kapacitást és méretezést. 

> [!Important]  
> A meglévő fájlmegosztások nem skálázhatóak fel a nagyméretű fájlmegosztások meghirdetett korlátaira, hacsak nem módosították a kvótát.

## <a name="expand-existing-file-shares"></a>Meglévő fájlmegosztások kibontása
Miután engedélyezte a nagyméretű fájlmegosztásokat a tárfiókon, ki kell bonta a tárfiók meglévő fájlmegosztását, hogy kihasználja a nagyobb kapacitást és méretezést. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. A tárfiókban válassza a **Fájlmegosztások lehetőséget.**
1. Kattintson a jobb gombbal a fájlmegosztásra, majd válassza a **Kvóta lehetőséget.**
1. Adja meg a kívánt új méretet, majd kattintson az **OK gombra.**

![A Azure Portal felhasználói felülete meglévő fájlmegosztások kvótával](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A kvóta maximális méretre való beállítását a következő paranccsal használhatja. Cserélje `<YourResourceGroupName>` le a , és `<YourStorageAccountName>` `<YourStorageAccountFileShareName>` adatokat az adataira.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A kvóta maximális méretre való beállítását a következő paranccsal használhatja. Cserélje `<yourResourceGroupName>` le a , és `<yourStorageAccountName>` `<yourFileShareName>` adatokat az adataira.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Következő lépések
* [Fájlmegosztás csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása macOS rendszeren](storage-how-to-use-files-mac.md)