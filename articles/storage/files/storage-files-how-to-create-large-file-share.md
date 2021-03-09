---
title: Nagyméretű fájlmegosztás engedélyezése és létrehozása – Azure Files
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és hozhatja létre a nagyméretű fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c22b3f3164cbb7c1a7ed150d093f77777c7b1023
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501294"
---
# <a name="enable-and-create-large-file-shares"></a>Nagyméretű fájlmegosztás engedélyezése és létrehozása

Ha engedélyezi a nagyméretű fájlmegosztást a Storage-fiókjában, a fájlmegosztás akár 100 TiB-ra is méretezhető, miközben a standard megosztások IOPS és átviteli korlátait is növeli. Azt is engedélyezheti, hogy ez a skálázás a meglévő fájlmegosztás meglévő Storage-fiókjain is engedélyezve legyen. Részletekért lásd a [fájlmegosztás és a fájlok méretezése célokat](storage-files-scale-targets.md#azure-files-scale-targets) ismertető részt. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](/cli/azure/install-azure-cli).
- Ha a Azure PowerShell modult szeretné használni, [telepítse a legújabb verziót](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Korlátozások

Egyelőre csak helyileg redundáns tárolást (LRS) vagy zóna-redundáns tárolást (ZRS) használhat a nagyméretű fájlmegosztás – engedélyezve fiókok esetében. Nem használhatja a Geo-Zone-redundáns tárolást (GZRS), a Geo-redundáns tárolást (GRS), a Read-Access geo-redundáns tárolást (RA-GRS), vagy a Read-Access geo-Zone-redundáns tárolást (RA-GZRS).

A nagyméretű fájlmegosztás egy fiókban való engedélyezése visszafordíthatatlan folyamat. Miután engedélyezte, nem fogja tudni átalakítani a fiókját GZRS, GRS, RA-GRS vagy RA-GZRS.

## <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. 
1. Az erőforrások listájában adja meg a **Storage-fiókokat**. A beíráskor a rendszer a bemenet alapján szűri a listákat. Válassza a **Storage-fiókok** lehetőséget.
1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki a Storage-fiók létrehozásához használni kívánt előfizetést.
1. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Adja meg az új erőforráscsoport nevét.

    ![Erőforráscsoport portálon való létrehozását bemutató képernyőkép](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ezután adja meg a tárfiók nevét. A névnek egyedinek kell lennie az Azure-ban. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokból és kisbetűkből állhat.
1. Válasszon egy helyet a Storage-fiókhoz.
1. Állítsa a replikálást **helyileg redundáns tárterületre** vagy a **zóna redundáns tárolására**.
1. Hagyja meg a következő mezőket az alapértelmezett értékeken:

   |Mező  |Érték  |
   |---------|---------|
   |Üzembehelyezési modell     |Resource Manager         |
   |Teljesítmény     |Standard         |
   |Fiók altípusa     |StorageV2 (általános célú v2)         |
   |Hozzáférési szint     |Gyakori         |

1. Válassza a **speciális** lehetőséget, majd a **nagyméretű fájlmegosztás** jobb oldalán kattintson az **engedélyezve** lehetőségre.
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

    ![Képernyőfelvétel az "enabled" (engedélyezve) gombra a Azure Portal új Storage-fiókjában](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Válassza a **Létrehozás** lehetőséget.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először [telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) , hogy lehetővé váljon a nagyméretű fájlmegosztás engedélyezése.

Nagyméretű fájlmegosztást engedélyező Storage-fiók létrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` le `<yourResourceGroup>` a, a és `<yourDesiredRegion>` az adatait az adataira.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Először [telepítse a PowerShell legújabb verzióját](/powershell/azure/install-az-ps) , hogy lehetővé váljon a nagyméretű fájlmegosztás engedélyezése.

Nagyméretű fájlmegosztást engedélyező Storage-fiók létrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` le `<yourResourceGroup>` a, a és `<yourDesiredRegion>` az adatait az adataira.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Nagyméretű fájlok megosztásának engedélyezése egy meglévő fiókon

A nagyméretű fájlmegosztást is engedélyezheti a meglévő fiókokon. Nagyméretű fájlmegosztás engedélyezése esetén a GZRS, GRS, RA-GRS vagy RA-GZRS formátumra nem lehet konvertálni. A nagyméretű fájlmegosztás engedélyezése visszafordíthatatlan ezen a Storage-fiókon.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Nyissa meg a [Azure Portalt](https://portal.azure.com), és keresse meg azt a Storage-fiókot, ahol engedélyezni szeretné a nagyméretű fájlmegosztást.
1. Nyissa meg a Storage-fiókot, és válassza a **konfiguráció** lehetőséget.
1. A **nagyméretű fájlmegosztás** esetében válassza az **engedélyezve** lehetőséget, majd kattintson a **Mentés** gombra.
1. Válassza az **Áttekintés** lehetőséget, és válassza a **frissítés** lehetőséget.

![Ha a Azure Portal meglévő Storage-fiókjában az Engedélyezve lehetőségre kattint](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Ezzel engedélyezte a nagyméretű fájlmegosztás használatát a Storage-fiókban. Ezt követően [frissítenie kell a meglévő megosztás kvótáját](#expand-existing-file-shares) , hogy kihasználhassa a megnövekedett kapacitást és méretezést.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha nagy fájlmegosztást szeretne engedélyezni a meglévő fiókjában, használja a következő parancsot. Cserélje `<yourStorageAccountName>` le `<yourResourceGroup>` a és az adatait.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Ezzel engedélyezte a nagyméretű fájlmegosztás használatát a Storage-fiókban. Ezt követően [frissítenie kell a meglévő megosztás kvótáját](#expand-existing-file-shares) , hogy kihasználhassa a megnövekedett kapacitást és méretezést.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha nagy fájlmegosztást szeretne engedélyezni a meglévő fiókjában, használja a következő parancsot. Cserélje `<yourStorageAccountName>` le `<yourResourceGroup>` a és az adatait.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Ezzel engedélyezte a nagyméretű fájlmegosztás használatát a Storage-fiókban. Ezt követően [frissítenie kell a meglévő megosztás kvótáját](#expand-existing-file-shares) , hogy kihasználhassa a megnövekedett kapacitást és méretezést.

---

## <a name="create-a-large-file-share"></a>Nagyméretű fájlmegosztás létrehozása

Miután engedélyezte a nagyméretű fájlmegosztást a Storage-fiókjában, a fiókban létrehozhat egy magasabb kvótával rendelkező fájlmegosztást. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

A nagyméretű fájlmegosztás létrehozása majdnem azonos a szabványos fájlmegosztás létrehozásával. A fő különbség az, hogy a kvótát akár 100 TiB-re is beállíthatja.

1. A Storage-fiókban válassza a **fájlmegosztás** lehetőséget.
1. Válassza a **+ Fájlmegosztás** lehetőséget.
1. Adja meg a fájlmegosztás nevét. Azt is megteheti, hogy a kvóta mérete (akár 100 TiB) is megadható. Ezután kattintson a **Létrehozás** elemre. 

![A név és a kvóta mezőket megjelenítő Azure Portal felhasználói felület](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nagyméretű fájlmegosztás létrehozásához használja a következő parancsot. Cserélje `<yourStorageAccountName>` le `<yourStorageAccountKey>` a, a és `<yourFileShareName>` az adatait az adataira.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nagyméretű fájlmegosztás létrehozásához használja a következő parancsot. Cserélje `<YourStorageAccountName>` le `<YourStorageAccountKey>` a, a és `<YourStorageAccountFileShareName>` az adatait az adataira.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Meglévő fájlmegosztás kibontása

Miután engedélyezte a nagyméretű fájlmegosztást a Storage-fiókon, a meglévő fájlmegosztást is kiterjesztheti az adott fiókban a magasabb kvótára. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A Storage-fiókban válassza a **fájlmegosztás** lehetőséget.
1. Kattintson a jobb gombbal a fájlmegosztás elemre, majd válassza a **kvóta** lehetőséget.
1. Adja meg a kívánt új méretet, majd kattintson **az OK gombra**.

![A meglévő fájlmegosztás kvótáját tartalmazó Azure Portal felhasználói felület](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha a kvótát a maximális méretre szeretné beállítani, használja a következő parancsot. Cserélje `<yourStorageAccountName>` le `<yourStorageAccountKey>` a, a és `<yourFileShareName>` az adatait az adataira.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a kvótát a maximális méretre szeretné beállítani, használja a következő parancsot. Cserélje `<YourStorageAccountName>` le `<YourStorageAccountKey>` a, a és `<YourStorageAccountFileShareName>` az adatait az adataira.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Következő lépések

* [Fájlmegosztás csatlakoztatása és csatlakoztatása Windows rendszeren](storage-how-to-use-files-windows.md)
* [Fájlmegosztás csatlakoztatása és csatlakoztatása Linux rendszeren](storage-how-to-use-files-linux.md)
* [Fájlmegosztás csatlakoztatása és csatlakoztatása macOS rendszeren](storage-how-to-use-files-mac.md)