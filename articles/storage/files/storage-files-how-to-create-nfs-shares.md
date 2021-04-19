---
title: NFS-megosztás létrehozása – Azure Files (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre olyan Azure-fájlmegosztást, amely a hálózati fájlrendszer protokollal csatlakoztatható.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b549c625f0a6ff0480eafc38f84d292e66350950
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717130"
---
# <a name="how-to-create-an-nfs-share"></a>NFS-megosztás létrehozása
Az Azure-fájlmegosztások teljes körűen felügyelt fájlmegosztások, amelyek a felhőben találhatóak. Ez a cikk az NFS protokollt használó fájlmegosztások létrehozását foglalja magában. További információ mindkét protokollról: [Azure-fájlmegosztási protokollok.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Korlátozások
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regionális elérhetőség
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Előfeltételek
- Az NFS-megosztások csak megbízható hálózatokról érhetők el. Az NFS-megosztáshoz való csatlakozásnak az alábbi források egyikével kell származni:
    - Hozzon [létre egy privát végpontot](storage-files-networking-endpoints.md#create-a-private-endpoint) (ajánlott), vagy korlátozza a [nyilvános végponthoz való hozzáférést.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Pont–hely (P2S) VPN konfigurálása Linux rendszeren](storage-files-configure-p2s-vpn-linux.md)az Azure Files.
    - [Konfigurálja a hely–hely VPN-t a](storage-files-configure-s2s-vpn.md)Azure Files.
    - Az [ExpressRoute konfigurálása.](../../expressroute/expressroute-introduction.md)

- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót.](/cli/azure/install-azure-cli)

## <a name="register-the-nfs-41-protocol"></a>Az NFS 4.1 protokoll regisztrálása
Ha az azure-Azure PowerShell vagy az Azure CLI-t használja, regisztrálja a funkciót a következő parancsokkal:

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az NFS 4.1 Azure PowerShell szolgáltatás regisztrálása az azure cli-Azure PowerShell vagy az Azure CLI használatával Azure Files.

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

A regisztráció jóváhagyása akár egy óráig is tart. A regisztráció befejezésének ellenőrzéséhez használja a következő parancsokat:

# <a name="portal"></a>[Portál](#tab/azure-portal)
A Azure PowerShell NFS 4.1 szolgáltatás regisztrációját az Azure CLI vagy a Azure Files. 

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

## <a name="create-a-filestorage-storage-account"></a>FileStorage tárfiók létrehozása
Az NFS 4.1-megosztások jelenleg csak prémium fájlmegosztásként érhetők el. Prémium fájlmegosztás NFS 4.1 protokolltámogatással való üzembe helyezéséhez először létre kell hoznia egy FileStorage tárfiókot. A tárfiók egy legfelső szintű objektum az Azure-ban, amely egy megosztott tárolókészletet képvisel, amely több Azure-fájlmegosztás üzembe helyezésére használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
A FileStorage tárfiók létrehozásához lépjen a Azure Portal.

1. A Azure Portal bal oldali **menüben válassza** a Tárfiókok lehetőséget.

    ![Azure Portal főoldalon válassza a tárfiók lehetőséget.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.
1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.
1. Válassza ki azt az erőforráscsoportot, amelyben a tárfiókot létre kell hoznia
1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.
1. A **Teljesítmény mezőben** válassza a **Prémium lehetőséget.**

    A **Fileshares** (Fájlmegosztások) legördülő menüben a Prémium lehetőséget kell választania, hogy elérhető legyen a  **Fióktípus** legördülő menüben.

1. A **Prémium fiók típusaként válassza** a **Fájlmegosztások lehetőséget.**

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Képernyőkép a kiválasztott prémium teljesítményről.":::

1. A **Replication (Replikáció)** beállítást hagyja meg az alapértelmezett helyileg redundáns tárolás **(LRS) értéken.**
1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget.

A tárfiók-erőforrás létrehozása után keresse meg azt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A FileStorage tárfiók létrehozásához nyisson meg egy PowerShell-parancssort, és hajtsa végre a következő parancsokat, és ne felejtse el lecserélni a és a értékeket a `<resource-group>` `<storage-account>` környezetének megfelelő értékekre.

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
A FileStorage tárfiók létrehozásához nyissa meg a terminált, és hajtsa végre a következő parancsokat, és ne felejtse el lecserélni a és a értékeket a `<resource-group>` `<storage-account>` környezetének megfelelő értékekre.

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

Most, hogy létrehozott egy FileStorage-fiókot és konfigurálta a hálózatot, létrehozhat egy NFS-fájlmegosztást. A folyamat hasonlít az SMB-megosztások létrehozásához. A megosztás létrehozásakor az **SMB** helyett az **NFS** lehetőséget kell választania.

1. Lépjen a tárfiókra, és válassza a **Fájlmegosztások lehetőséget.**
1. Új **fájlmegosztás létrehozásához** válassza a + Fájlmegosztás lehetőséget.
1. Nevezze el a fájlmegosztást, és válasszon ki egy kiépített kapacitást.
1. A **Protokoll beállításnál** válassza az **NFS (előzetes verzió) lehetőséget.**
1. A **Gyökércsomógyítvány** beállításnál tegye meg a megfelelőt.

    - Gyökérgyöker (alapértelmezett) – A távoli felügyelő (root) hozzáférése a UID (65534) és a GID (65534) elemekre van leképezve.
    - Nincs gyökérgyöker – A távoli superuser (root) gyökérként kap hozzáférést.
    - Minden érék – Minden felhasználói hozzáférés az UID -ra (65534) és a GID-re (65534) van leképezve.
    
1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Képernyőkép a fájlmegosztás létrehozási panelről.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Győződjön meg arról, hogy a .NET-keretrendszer telepítve van. Lásd: [Download .NET-keretrendszer](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Az alábbi paranccsal ellenőrizze, hogy a Telepített PowerShell-verzió vagy újabb `5.1` verziójú-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítését lásd: Meglévő verzió [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Telepítse a PowerShellGet-modul legújabb verzióját.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Zárja be, majd nyissa meg újra a PowerShell-konzolt.

1. Telepítse az **Az.Storage előzetes** verziójú modul **2.5.2-preview verzióját.**

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   A PowerShell-modulok telepítésére vonatkozó további információkért lásd: [Install the Azure PowerShell module](/powershell/azure/install-az-ps)
   
1. Ha prémium szintű fájlmegosztást hoz létre a Azure PowerShell modullal, használja a [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) parancsmagot.

    > [!NOTE]
    > A prémium szintű fájlmegosztások számlázása egy kiépített modell használatával van kiszámlázva. A megosztás kiépített méretét az alábbiakban adja `QuotaGiB` meg. További információkért lásd [a kiépített](understanding-billing.md#provisioned-model) modell és a Azure Files [díjszabását.](https://azure.microsoft.com/pricing/details/storage/files/)

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha prémium szintű fájlmegosztást hoz létre az Azure CLI-val, használja [az az storage share create](/cli/azure/storage/share-rm) parancsot.

> [!NOTE]
> A prémium szintű fájlmegosztások számlázása egy kiépített modell használatával van kiszámlázva. A megosztás kiépített méretét az alábbiakban `quota` adja meg. További információkért lásd [a kiépített modell](understanding-billing.md#provisioned-model) és a Azure Files [díjszabását.](https://azure.microsoft.com/pricing/details/storage/files/)

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
Most, hogy létrehozott egy NFS-megosztást, a használathoz csatlakoztatnia kell azt a Linux-ügyfélen. Részletekért lásd: [NFS-megosztás csatlakoztatása.](storage-files-how-to-mount-nfs-shares.md)

Ha bármilyen problémát tapasztal, tekintse meg [az Azure NFS-fájlmegosztások hibaelhárítását bemutató témakört.](storage-troubleshooting-files-nfs.md)