---
title: NFS-megosztás létrehozása – Azure Files (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre olyan Azure-fájlmegosztást, amely a hálózati fájlrendszer protokollal csatlakoztatható.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521237"
---
# <a name="how-to-create-an-nfs-share"></a>NFS-megosztás létrehozása
Az Azure-fájlmegosztás teljes körűen felügyelt fájlmegosztás, amely a felhőben él. Ez a cikk az NFS protokollt használó fájlmegosztás létrehozását ismerteti. További információ mindkét protokollról: [Azure fájlmegosztás protokollok](storage-files-compare-protocols.md).

## <a name="limitations"></a>Korlátozások
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionális elérhetőség
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Előfeltételek
- Az NFS-megosztások csak megbízható hálózatokból érhetők el. Az NFS-megosztással létesített kapcsolatoknak az alábbi források egyikéből kell származnia:
    - [Hozzon létre egy privát végpontot](storage-files-networking-endpoints.md#create-a-private-endpoint) (ajánlott), vagy [korlátozza a hozzáférést a nyilvános végponthoz](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használathoz](storage-files-configure-p2s-vpn-linux.md).
    - [Helyek közötti VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md).
    - Konfigurálja a [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Az NFS 4,1 protokoll regisztrálása
Ha a Azure PowerShell modult vagy az Azure CLI-t használja, regisztrálja a szolgáltatást a következő parancsokkal:

# <a name="portal"></a>[Portál](#tab/azure-portal)
Használja az Azure PowerShell vagy az Azure CLI-t a Azure Files NFS 4,1 funkciójának regisztrálásához.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

A regisztráció jóváhagyása akár egy órát is igénybe vehet. A regisztráció befejezésének ellenőrzéséhez használja a következő parancsokat:

# <a name="portal"></a>[Portál](#tab/azure-portal)
Használja az Azure PowerShell vagy az Azure CLI-t a Azure Files NFS 4,1 szolgáltatásának regisztrációjának megadásához. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>FileStorage-fiók létrehozása
Jelenleg az NFS 4,1-megosztások csak a prémium szintű fájlmegosztás esetén érhetők el. A prémium fájlmegosztás NFS 4,1 protokoll támogatásával történő üzembe helyezéséhez először létre kell hoznia egy FileStorage-fiókot. A Storage-fiók egy olyan legfelső szintű objektum az Azure-ban, amely a tároló megosztott készletét jelöli, amely több Azure-fájlmegosztás üzembe helyezésére is használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
FileStorage-fiók létrehozásához navigáljon a Azure Portal.

1. A Azure Portal válassza a bal oldali menüben a **Storage-fiókok** lehetőséget.

    ![Azure Portal fő lap Storage-fiók kiválasztása](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
3. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
4. Válassza ki azt az erőforráscsoportot, amelyben létre szeretné hozni a Storage-fiókot

5. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
6. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
7. A **teljesítmény** beállításnál válassza a **prémium** lehetőséget.

    Ki kell választania a **prémium** szintű **FileStorage** , hogy elérhető legyen a **Fiók típusa** legördülő menüben.

8. Válassza a **Fiók típusa** lehetőséget, és válassza a **FileStorage** lehetőséget.
9. Hagyja meg a **replikálást** a **helyileg REDUNDÁNS tárolás (LRS)** alapértelmezett értékére.

    ![Storage-fiók létrehozása prémium fájlmegosztás esetén](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
11. Válassza a **Létrehozás** lehetőséget.

A Storage-fiók erőforrásának létrehozása után navigáljon hozzá.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
FileStorage-fiók létrehozásához nyisson meg egy PowerShell-parancssort, és hajtsa végre a következő parancsokat, és jegyezze fel a helyére, `<resource-group>` és `<storage-account>` a megfelelő értékeket adja meg a környezetéhez.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
FileStorage-fiók létrehozásához nyissa meg a terminált, és hajtsa végre a következő parancsokat, és ne feledje, hogy `<resource-group>` `<storage-account>` a megfelelő értékeket adja meg a környezetéhez.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>NFS-megosztás létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)

Most, hogy létrehozott egy FileStorage-fiókot, és konfigurálta a hálózatot, létrehozhat egy NFS-fájlmegosztást. A folyamat hasonló az SMB-megosztások létrehozásához, ha a megosztás létrehozásakor az **NFS** -t választja az **SMB** helyett.

1. Navigáljon a Storage-fiókjához, és válassza a **fájlmegosztás** lehetőséget.
1. Válassza a **+ fájlmegosztás** lehetőséget egy új fájlmegosztás létrehozásához.
1. Nevezze el a fájlmegosztást, válasszon ki egy kiosztott kapacitást.
1. A **protokollnál** válassza az **NFS (előzetes verzió)** lehetőséget.
1. A **legfelső szintű squash** kiválasztását teszi elérhetővé.

    - Root squash (alapértelmezett) – a távoli adminisztrátor (root) hozzáférése az UID (65534) és a GID (65534) számára van leképezve.
    - Nincs root deverés – a távoli adminisztrátor (root) a root-ként kapja meg a hozzáférést.
    - Minden squash – minden felhasználói hozzáférés az UID (65534) és a GID (65534) számára van leképezve.
    
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Képernyőfelvétel a fájlmegosztás létrehozási paneljéről":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Győződjön meg arról, hogy a .NET-keretrendszer telepítve van. Lásd: [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell `5.1` -verzió vagy magasabb-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Telepítse a PowershellGet modul legújabb verzióját.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Kattintson a Bezárás gombra, majd nyissa meg újra a PowerShell-konzolt.

1. Telepítse az az **. Storage** Preview modul **2.5.2-** es verziójának előzetes verzióját.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)
   
1. Ha prémium szintű fájlmegosztást szeretne létrehozni a Azure PowerShell modullal, használja a [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) parancsmagot.

    > [!NOTE]
    > A prémium fájlmegosztás számlázása kiépített modell használatával történik. A megosztás kiépített méretét az alábbiak határozzák meg `QuotaGiB` . További információ: [a kiépített modell](understanding-billing.md#provisioned-model) és a [Azure Files díjszabási oldalának](https://azure.microsoft.com/pricing/details/storage/files/)megismerése.

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Prémium fájlmegosztás Azure CLI-vel való létrehozásához használja az az [Storage Share Create](/cli/azure/storage/share-rm) parancsot.

> [!NOTE]
> A prémium fájlmegosztás számlázása kiépített modell használatával történik. A megosztás kiépített méretét az alábbiak határozzák meg `quota` . További információ: [a kiépített modell](understanding-billing.md#provisioned-model) és a [Azure Files díjszabási oldalának](https://azure.microsoft.com/pricing/details/storage/files/)megismerése.

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Következő lépések
Most, hogy létrehozott egy NFS-megosztást, a használatához csatlakoztatnia kell azt a Linux-ügyfélen. Részletekért lásd: [NFS-megosztás csatlakoztatása](storage-files-how-to-mount-nfs-shares.md).

Ha problémákat tapasztal, tekintse meg az [Azure NFS-fájlmegosztás hibaelhárítása](storage-troubleshooting-files-nfs.md)című témakört.