---
title: Üzembe Azure File Sync | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe Azure File Sync-t az elsőtől a végéig a Azure Portal, a PowerShell vagy az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 989bcbb7e509b9b7692f067af2989fcad94b6ad1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796884"
---
# <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése
A Azure File Sync központosíthatja a szervezet fájlmegosztását a Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). A világ minden táján annyi gyorsítótárral lehet, amennyire csak szüksége van.

Határozottan javasoljuk, hogy olvassa el a [Planning for an Azure Files deployment](../files/storage-files-planning.md) and Planning for an Azure File Sync [deployment](file-sync-planning.md) before you complete the steps in this article.

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Egy Azure-fájlmegosztás ugyanabban a régióban, ahol üzembe Azure File Sync. További információ:
    - [Régiónkénti](file-sync-planning.md#azure-file-sync-region-availability) Azure File Sync.
    - [Hozzon létre egy](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) fájlmegosztást a fájlmegosztások létrehozási folyamatának részletes leírására.
1. Legalább egy támogatott Windows Server- vagy Windows Server-fürtpéldány szinkronizálása a Azure File Sync. További információ a Windows Server támogatott verzióiról és az ajánlott rendszererőforrásokról: [A Windows-fájlkiszolgálóval kapcsolatos szempontok.](file-sync-planning.md#windows-file-server-considerations)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Egy Azure-fájlmegosztás ugyanabban a régióban, ahol üzembe Azure File Sync. További információ:
    - [Régiónkénti](file-sync-planning.md#azure-file-sync-region-availability) Azure File Sync.
    - [Hozzon létre egy](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) fájlmegosztást a fájlmegosztások létrehozási folyamatának részletes leírására.
1. Legalább egy támogatott Windows Server- vagy Windows Server-fürtpéldány szinkronizálása a Azure File Sync. További információ a Windows Server támogatott verzióiról és az ajánlott rendszererőforrásokról: [A Windows-fájlkiszolgálóval kapcsolatos szempontok.](file-sync-planning.md#windows-file-server-considerations)

1. Az Az PowerShell-modul a PowerShell 5.1-es vagy a PowerShell 6-os vagy további parancsával is használható. Az Az PowerShell-modult használhatja az Azure File Sync-hez bármely támogatott rendszeren, beleértve a nem Windows rendszereket is, azonban a kiszolgálóregisztrációs parancsmagot mindig futtatni kell a regisztráló Windows Server-példányon (ez közvetlenül vagy PowerShell-alapú eltárolással használhatja). A Windows Server 2012 R2-ben ellenőrizheti, hogy legalább a PowerShell 5.1-et futtatja-e. \* a következő objektum **PSVersion** **tulajdonságának $PSVersionTable** meg:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Ha a **PSVersion** értéke kisebb, mint 5.1. , ahogyan a Windows Server 2012 R2 legtöbb friss telepítése esetében, a \* Windows Management Framework [(WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)letöltésével és telepítésével egyszerűen frissíthet. A Windows Server 2012 R2-höz a **Win8.1AndW2K12R2-KB \* \* \* \* \* \* \* -x64.msu** szükséges. 

    A PowerShell 6+ bármilyen támogatott rendszerrel használható, és a [GitHub-oldalán keresztül tölthető le.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Ha nem közvetlenül a PowerShellből, hanem a Kiszolgálóregisztráció felhasználói felületet tervezi használni, a PowerShell 5.1-et kell használnia.

1. Ha a PowerShell 5.1 használatát választotta, győződjön meg arról, hogy legalább a .NET 4.7.2-es vagy azt telepítette. További információ [a .NET-keretrendszer és függőségeiről.](/dotnet/framework/migration-guide/versions-and-dependencies)

    > [!Important]  
    > Ha a Windows Server Core-on telepíti a .NET 4.7.2 vagy többes verziókat, a és a jelzővel kell telepítenie, különben a `quiet` `norestart` telepítés sikertelen lesz. Ha például a .NET 4.8-at telepíti, a parancs a következő lesz:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az Az PowerShell-modul, amely az itt található utasítások szerint telepíthető: [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az Az.StorageSync modul automatikusan telepítve van az Az PowerShell-modul telepítésekor.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Egy Azure-fájlmegosztás ugyanabban a régióban, ahol üzembe Azure File Sync. További információ:
    - [Régiónkénti](file-sync-planning.md#azure-file-sync-region-availability) Azure File Sync.
    - [Hozzon létre egy fájlmegosztást](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) a fájlmegosztások létrehozásához szükséges részletes leíráshoz.
1. Legalább egy támogatott Windows Server- vagy Windows Server-fürtpéldány szinkronizálása a Azure File Sync. További információ a Windows Server támogatott verzióiról és az ajánlott rendszererőforrásokról: [A Windows-fájlkiszolgálóval kapcsolatos szempontok.](file-sync-planning.md#windows-file-server-considerations)

1. [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)

   Ha szeretné, a Azure Cloud Shell is használhatja az oktatóanyag lépéseit.  Azure Cloud Shell egy interaktív felületi környezet, amely a böngészőben használható.  Kezdje Cloud Shell a következő módszerek egyikével:

   - Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában. **Próbálja meg** megnyitni Azure Cloud Shell, de nem másolja automatikusan a kódot a Cloud Shell.

   - Nyissa Cloud Shell következőt: [https://shell.azure.com](https://shell.azure.com)

   - Kattintson **a Cloud Shell** jobb felső sarkában található menüsáv [](https://portal.azure.com) Azure Portal

1. Jelentkezzen be.

   Jelentkezzen be az [az login](/cli/azure/reference-index#az_login) paranccsal, ha a CLI helyi telepítését használja.

   ```azurecli
   az login
   ```

    A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

1. Telepítse az [az filesync](/cli/azure/ext/storagesync/storagesync) Azure CLI-bővítményt.

   ```azurecli
   az extension add --name storagesync
   ```

   A **storagesync** bővítményhivatkozás telepítése után a következő figyelmeztetés jelenik meg.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>A Windows Server előkészítése az Azure File Sync használatára
Tiltsa le az összes olyan kiszolgálót, Azure File Sync a feladatátvevő fürtben található összes kiszolgálócsomópontot is beleértve, Internet Explorer fokozott **biztonsági beállításokat.** Ez csak a kezdeti kiszolgálóregisztrációhoz szükséges. A kiszolgáló regisztrációja után újra engedélyezheti.

# <a name="portal"></a>[Portál](#tab/azure-portal)
> [!Note]  
> Ezt a lépést kihagyhatja, ha Windows Server Core-Azure File Sync telepíti a virtuális gépeket.

1. Nyissa meg a Kiszolgálókezelőt.
2. Kattintson **a Helyi kiszolgáló elemre:**  
    ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. A **Tulajdonságok** lapon, válassza ki az **Internet Explorer fokozott biztonsági beállításai** hivatkozást.  
    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. A Fokozott **Internet Explorer** konfigurálása párbeszédpanelen válassza a Ki beállítást **a** Rendszergazdák és **felhasználók** **számára:**  
    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A biztonsági Internet Explorer letiltásához hajtsa végre a következőt egy emelt szintű PowerShell-munkamenetből:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kövesse a Azure Portal Vagy a PowerShell utasításait.

---

## <a name="deploy-the-storage-sync-service"></a>A Társzinkronizálási szolgáltatás üzembe helyezése 
A Azure File Sync a **Storage Sync Service-erőforrás** a kiválasztott előfizetés erőforráscsoportba való elhelyezésével kezdődik. Javasoljuk, hogy csak néhányat kiépítsen ezek közül. Megbízhatósági kapcsolatot fog létrehozni a kiszolgálók és az erőforrás között, és egy kiszolgáló csak egy Szinkronizálási szolgáltatásban regisztrálható. Ezért ajánlott annyi szinkronizálási szolgáltatást üzembe helyezni, amennyit a kiszolgálók csoportjainak külön-külön kell használnia. Ne feledje, hogy a különböző tárolószinkronizálási szolgáltatások kiszolgálói nem szinkronizálhatóak egymással.

> [!Note]
> A társzinkronizálási szolgáltatás örökli a hozzáférési engedélyeket az előfizetéstől és az üzembe helyezett erőforráscsoporttól. Javasoljuk, hogy gondosan ellenőrizze, hogy ki férhet hozzá. Az írási hozzáféréssel rendelkezik entitások új fájlkészleteket szinkronizálnak a társzinkronizálási szolgáltatásban regisztrált kiszolgálókról, és az adatok a számukra elérhető Azure-tárolóba áramlnak.

# <a name="portal"></a>[Portál](#tab/azure-portal)
A Társzinkronizálási szolgáltatás üzembe helyezéséhez keresse meg a [Azure Portal,](https://portal.azure.com/)kattintson *az Erőforrás* létrehozása elemre, majd keresse meg a Azure File Sync. A keresési eredmények között válassza **a** Azure File Sync, majd a Létrehozás lehetőséget a **Tárolószinkronizálás üzembe helyezése lap megnyitásához.** 

A megnyíló panelen adja meg a következőket:

- **Név:** A Társzinkronizálási szolgáltatás egyedi neve (régiónként).
- **Előfizetés:** Az az előfizetés, amelyben létre szeretné hozni a szinkronizálási szolgáltatást. A szervezet konfigurációs stratégiájától függően egy vagy több előfizetéshez is hozzáférhet. Az Azure-előfizetés az egyes felhőszolgáltatások (például a szolgáltatások) számlázásának Azure Files.
- **Erőforráscsoport:** Az erőforráscsoport Azure-erőforrások logikai csoportja, például egy tárfiók vagy egy szinkronizálási szolgáltatás. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt a Azure File Sync. (Javasoljuk, hogy az erőforráscsoportokat tárolókként használja a szervezet erőforrásainak logikai elkülönítéséhez, például a HR-erőforrások vagy -erőforrások csoportosítása egy adott projekthez.)
- **Hely:** Az a régió, amelyben üzembe szeretné helyezni Azure File Sync. Ebben a listában csak a támogatott régiók érhetők el.

Ha elkészült, válassza a Létrehozás **lehetőséget** a Társzinkronizálási szolgáltatás üzembe helyezéséhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Cserélje le a , és értékeket a saját értékeire, majd a következő parancsokkal hozzon létre és `<Az_Region>` `<RG_Name>` `<my_storage_sync_service>` telepítsen egy Storage Sync Service-t:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kövesse az utasításokat a Azure Portal PowerShellben.

---

## <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az ügynököt a [Microsoft letöltőközpontból töltheti le.](https://go.microsoft.com/fwlink/?linkid=858257) Ha a letöltés befejeződött, kattintson duplán az MSI-csomagra a Azure File Sync telepítéséhez.

> [!Important]  
> Ha feladatátvevő fürthöz Azure File Sync használni, a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell. A fürt minden csomópontját regisztrálni kell a Azure File Sync.

javasoljuk, hogy tegye a következőket:
- Hagyja meg az alapértelmezett telepítési útvonalat (C:\Program Files\Azure\StorageSyncAgent), hogy egyszerűbb legyen a hibaelhárítás és a kiszolgáló karbantartása.
- Engedélyezze Microsoft Update, hogy Azure File Sync naprakész maradjon. A szolgáltatásügynök minden Azure File Sync, beleértve a szolgáltatásfrissítéseket és a gyorsjavításokat is, a Microsoft Update. Javasoljuk, hogy telepítse a legújabb frissítést a Azure File Sync. További információ: Azure File Sync [szabályzat frissítése.](file-sync-planning.md#azure-file-sync-agent-update-policy)

Amikor a Azure File Sync ügynök telepítése befejeződött, a Kiszolgálóregisztráció felhasználói felület automatikusan megnyílik. A regisztráció előtt a Storage Sync Service-nek kell lennie; tekintse meg a Storage Sync Service létrehozásáról a következő szakaszt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Hajtsa végre a következő PowerShell-kódot a Azure File Sync operációs rendszerének megfelelő verziójának letöltéséhez és a rendszerre való telepítéséhez.

> [!Important]  
> Ha feladatátvevő fürthöz Azure File Sync használni, a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell. A fürt minden csomópontjának regisztrálva kell lennie a Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kövesse a Azure Portal Vagy a PowerShell utasításait.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatással
A Windows Server regisztrálásával a Társzinkronizálási szolgáltatásra megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Társzinkronizálási szolgáltatás között. A kiszolgáló csak egy Társzinkronizálási szolgáltatásra regisztrálható és az ugyanahhoz a Társzinkronizálási szolgáltatáshoz társított kiszolgálókkal és Azure-fájlmegosztásokkal képes szinkronizálni.

> [!Note]
> A kiszolgálóregisztráció az Ön Azure-beli hitelesítő adataival hoz létre megbízhatósági kapcsolatot a Társzinkronizálási szolgáltatás és a Windows Server között, ezt követően azonban a kiszolgáló létrehozza és használja a saját identitását, amely addig érvényes, amíg a kiszolgáló regisztrálva marad, és a jelenlegi közös hozzáférésű jogosultságkódot (Storage SAS) érvényes. A kiszolgáló regisztrációjának megszüntetése után nem lehet új SAS-jogkivonatot kiadni a kiszolgálónak, így a kiszolgáló nem férhet hozzá az Azure-fájlmegosztáshoz, és nem tudja leállítani a szinkronizálást.

A kiszolgálót regisztráló rendszergazdának a Tulajdonos  vagy Közreműködő felügyeleti szerepkör tagja kell, hogy legyen **az** adott Szinkronizálási szolgáltatáshoz. Ez a Társzinkronizálási szolgáltatás Access Control **(IAM)** Azure Portal konfigurálható.

Emellett meg lehet különböztetni azokat a rendszergazdákat, akik regisztrálhatják a kiszolgálókat azok közül, akik számára engedélyezett a szinkronizálás konfigurálása a szinkronizálási szolgáltatásban. Ehhez létre kell hoznia egy egyéni szerepkört, amelyben felsorolhatja azokat a rendszergazdákat, akik csak a kiszolgálók regisztrálásához engedélyezettek, és meg kell adni az egyéni szerepkörnek a következő engedélyeket:

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[Portál](#tab/azure-portal)
A kiszolgálóregisztrációs ügynök telepítése után a kiszolgálóregisztrációs felhasználói felületnek automatikusan meg kell Azure File Sync meg. Ha nem, akkor manuálisan is megnyithatja, a fájl helye: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a Kiszolgálóregisztráció felhasználói felület, **válassza a Bejelentkezés lehetőséget** a kezdéshez.

A bejelentkezés után a rendszer a következő adatokat kéri:

![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés:** A Társzinkronizálási szolgáltatást tartalmazó előfizetés [(lásd: A társzinkronizálási szolgáltatás üzembe helyezése).](#deploy-the-storage-sync-service) 
- **Erőforráscsoport:** A Társzinkronizálási szolgáltatást tartalmazó erőforráscsoport.
- **Társzinkronizálási** szolgáltatás: Annak a társzinkronizálási szolgáltatásnak a neve, amellyel regisztrálni szeretne.

Miután kiválasztotta a megfelelő adatokat, válassza a **Regisztráció lehetőséget** a kiszolgáló regisztrációjának befejezéséhez. A regisztrációs folyamat részeként a rendszer újabb bejelentkezésre kéri fel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kövesse az utasításokat a Azure Portal PowerShellben.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Szinkronizálási csoport és felhő végpont létrehozása
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. A szinkronizálási csoportnak tartalmaznia kell egy felhőbeli végpontot, amely Azure-fájlmegosztást és egy vagy több kiszolgáló végpontot jelöl. A kiszolgálóvégpont egy elérési utat képvisel egy regisztrált kiszolgálón. Egy kiszolgáló több szinkronizálási csoportban is lehet kiszolgálóvégponttal. Annyi szinkronizálási csoportot hozhat létre, amennyire szüksége van a kívánt szinkronizálási topológia megfelelő leírására.

A felhőbeli végpont egy Azure-fájlmegosztásra mutató mutató. Minden kiszolgálóvégpont szinkronizálva lesz egy felhőbeli végponttal, így a felhőbeli végpont lesz a központ. Az Azure-fájlmegosztás tárfiókja és a szinkronizálási szolgáltatás ugyanabban a régióban kell lennie. A teljes Azure-fájlmegosztás szinkronizálva lesz, egyetlen kivétellel: Egy speciális mappa lesz kiépítve, amely hasonlít az NTFS-kötet rejtett "Rendszerkötet-információk" mappájába. Ennek a könyvtárnak a neve " . SystemShareInformation". Fontos szinkronizálási metaadatokat tartalmaz, amelyek nem szinkronizálódnak más végpontokkal. Ne használja és ne törölje!

> [!Important]  
> A szinkronizálási csoport bármely felhőbeli végpontját vagy kiszolgálóvégpontját módosíthatja, és szinkronizálhatja a fájlokat a szinkronizálási csoport többi végpontjára. Ha közvetlenül módosítja a felhővégpontot (Azure-fájlmegosztást), a módosításokat először egy módosításészlelési Azure File Sync kell észlelni. A rendszer 24 óránként csak egyszer kezdeményez változásészlelési feladatot egy felhőbeli végponton. További információt a gyakori [Azure Files tartalmaz.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)

A felhőbeli végpontot létrehozó rendszergazdának  a tulajdonos felügyeleti szerepkör tagja kell, hogy legyen ahhoz a tárfiókhoz, amely tartalmazza azt az Azure-fájlmegosztást, amelyre a felhőbeli végpont mutat. Ez a tárfiók Access Control **(IAM)** Azure Portal konfigurálható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Szinkronizálási csoport létrehozásához [](https://portal.azure.com/)a Azure Portal a Társzinkronizálási szolgáltatáshoz, majd válassza a **+ Szinkronizálási csoport lehetőséget:**

![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

A megnyíló panelen adja meg a következő információkat a szinkronizálási csoport létrehozásához egy felhőbeli végponttal:

- **Szinkronizálási csoport neve:** A létrehozni fog szinkronizálási csoport neve. A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név.
- **Előfizetés:** Az az előfizetés, amelyben üzembe helyezett egy szinkronizálási szolgáltatást [a Tárolószinkronizálási szolgáltatás üzembe helyezésecímű alatt.](#deploy-the-storage-sync-service)
- **Tárfiók:** Ha a Tárfiók kiválasztása lehetőséget **választja,** megjelenik egy újabb panel, amelyen kiválaszthatja azt a tárfiókot, amely rendelkezik a szinkronizálni kívánt Azure-fájlmegosztással.
- **Azure-fájlmegosztás:** Annak az Azure-fájlmegosztásnak a neve, amellyel szinkronizálni szeretne.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A szinkronizálási csoport létrehozásához hajtsa végre a következő PowerShellt. Ne felejtse el `<my-sync-group>` lecserélni a helyére a szinkronizálási csoport kívánt nevét.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

A szinkronizálási csoport sikeres létrehozása után létrehozhatja a felhőbeli végpontot. Ne feledjük a `<my-storage-account>` és `<my-file-share>` a értékeket a várt értékekkel.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hozzon létre egy új szinkronizálási csoportot az [az storagesync sync-group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) paranccsal.  Ha az összes CLI-parancshoz alapértelmezett erőforráscsoportot használ, használja [az az configure parancsot.](/cli/azure/reference-index#az_configure)

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Új [felhővégpont létrehozásához használja az az storagesync sync-group cloud-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) parancsot.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Kiszolgálói végpont létrehozása
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten. A kiszolgálóvégpontnak egy regisztrált kiszolgálón (és nem csatlakoztatott megosztáson) található elérési útnak kell lennie, és a felhőbeli rétegezéshez az elérési útnak nem rendszerköteten kell lennie. A hálózati tároló (NAS) nem támogatott.

> [!NOTE]
> Az elérési út vagy a meghajtó betűjelének módosítása a kiszolgálóvégpont köteten való létrejötte után nem támogatott. Győződjön meg arról, hogy a regisztrált kiszolgálón a végső elérési utat használja.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Kiszolgálóvégpont hozzáadásához válassza az újonnan létrehozott szinkronizálási csoportot, majd válassza a **Kiszolgálói végpont hozzáadása lehetőséget.**

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-deployment-guide/create-sync-group-part-2.png)

A **Kiszolgálói végpont felvétele** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

- **Regisztrált kiszolgáló:** Annak a kiszolgálónak vagy fürtnek a neve, ahol létre szeretné hozni a kiszolgálóvégpontot.
- **Elérési** út: A szinkronizálási csoport részeként szinkronizálni kell a Windows Server elérési útja.
- **Felhőbeli rétegezés:** A felhőbeli rétegezés engedélyezésére vagy letiltására való váltás. A felhőbeli rétegezéssel a ritkán használt vagy elért fájlok rétegezése is Azure Files.
- **Szabad kötetterület:** A kiszolgálóvégpontot tároló köteten lefoglalni szükséges szabad terület. Ha például a kötet szabad területének 50%-a van beállítva egy olyan köteten, amely egyetlen kiszolgálóvégponttal rendelkezik, akkor az adatok mennyiségének körülbelül fele 50%-ra van Azure Files. Függetlenül attól, hogy a felhőbeli rétegezés engedélyezve van-e, az Azure-fájlmegosztás mindig rendelkezik a szinkronizálási csoportban található adatok teljes másolattal.
- **Kezdeti letöltési mód:** Ez egy választható lehetőség, az ügynök 11-es verziójától kezdve, amely hasznos lehet, ha fájlok vannak az Azure-fájlmegosztásban, de a kiszolgálón nem. Ilyen helyzet lehet például, ha olyan kiszolgálóvégpontot hoz létre, amely egy másik fiókiroda-kiszolgálót ad hozzá egy szinkronizálási csoporthoz, vagy ha vészhelyreállítást vezet be egy meghibásodott kiszolgálóra. Ha a felhőbeli rétegezés engedélyezve van, az alapértelmezett beállítás az, hogy csak a névteret hívja vissza, és kezdetben nincs fájltartalom. Ez akkor hasznos, ha úgy véli, hogy a felhasználói hozzáférési kérelmeknek kell eldönteniük, hogy milyen fájltartalmak vannak visszahívásra a kiszolgálóra. Ha a felhőbeli rétegezés le van tiltva, az alapértelmezett beállítás az, hogy először a névtér lesz letöltve, majd a fájlok az utolsó módosítás időbélyege alapján lesznek visszahívva, amíg el nem érik a helyi kapacitást. A kezdeti letöltési módot azonban módosíthatja csak névtérre. Harmadik mód csak akkor használható, ha a felhőbeli rétegezés le van tiltva ehhez a kiszolgálóvégponthoz. Ez a mód elkerüli a névtér előhívását. A fájlok csak akkor jelennek meg a helyi kiszolgálón, ha volt lehetőségük a teljes letöltésre. Ez a mód akkor hasznos, ha például egy alkalmazás teljes fájlokat igényel, és nem tolerálja a rétegzett fájlokat a névterében.

A kiszolgálóvégpont hozzáadásához válassza a **Létrehozás lehetőséget.** A fájlok mostantól szinkronban maradnak az Azure-fájlmegosztás és a Windows Server között. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Hajtsa végre a következő PowerShell-parancsokat a kiszolgálóvégpont létrehozásához, és cserélje le a és a helyére a kívánt értékeket, és ellenőrizze a választható kezdeti letöltési `<your-server-endpoint-path>` `<your-volume-free-space>` házirendet.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Új [kiszolgálóvégpont létrehozásához használja az az storagesync sync-group server-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) parancsot.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Tűzfal- és virtuális hálózati beállítások konfigurálása

### <a name="portal"></a>Portál
Ha az Azure File Syncet tűzfal- és virtuális hálózati beállításokkal való használatra szeretné konfigurálni, tegye a következőket:

1. A Azure Portal nyissa meg a biztonságossá kívánt tárfiókot.
1. A **bal oldali menüben** válassza a Hálózat lehetőséget.
1. A **Kijelölt hálózatok alatt,** a **Hozzáférés engedélyezése innen: alatt.**
1. Ellenőrizze, hogy a kiszolgálók IP-címe vagy virtuális hálózata szerepel-e a **Címtartomány szakaszban.**
1. Győződjön meg **arról, Microsoft-szolgáltatások A tárfiókhoz való** hozzáférés engedélyezése megbízható felhasználók számára be van jelölve.
1. A **beállítások mentéséhez** válassza a Mentés lehetőséget.

    ![Tűzfal- és virtuális hálózati beállítások konfigurálása az Azure File Sync-beli használathoz](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Be- és Azure File Sync
A szolgáltatásba való Azure File Sync javasolt lépései nulla állásidővel, a teljes fájlhűség és hozzáférés-vezérlési lista (ACL) megőrzése mellett a következők:
 
1. Tárolószinkronizálási szolgáltatás üzembe helyezése.
1. Hozzon létre egy szinkronizálási csoportot.
1. Telepítse Azure File Sync ügynököt a kiszolgálóra a teljes adatkészlettel.
1. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálóvégpontot a megosztáson. 
1. Hagyja, hogy a sync teljes feltöltést az Azure-fájlmegosztásba (felhővégpontba) töltsön fel.  
1. A kezdeti feltöltés befejezése után telepítse Azure File Sync ügynököt az összes fennmaradó kiszolgálóra.
1. Hozzon létre új fájlmegosztásokat az összes többi kiszolgálón.
1. Ha szükséges, hozzon létre kiszolgálóvégpontokat az új fájlmegosztásokra a felhőbeli rétegezés szabályzatával. (Ehhez a lépéshez további tárterületre van szükség a kezdeti beállításhoz.)
1. Hagyja Azure File Sync, hogy az ügynök gyorsan visszaállítsa a teljes névteret a tényleges adatátvitel nélkül. A teljes névtér-szinkronizálás után a szinkronizálási motor kitölti a helyi lemezterületet a kiszolgálóvégpont felhőbeli rétegzási szabályzata alapján. 
1. Győződjön meg arról, hogy a szinkronizálás befejeződött, és szükség szerint tesztelje a topológiát. 
1. Felhasználók és alkalmazások átirányítása erre az új megosztásra.
1. Igény szerint törölheti a kiszolgálókon található duplikált megosztásokat.
 
Ha nem rendelkezik további tárhellyel a kezdeti bevetéshez, és szeretné csatolni a meglévő megosztásokat, előre átveheti az adatokat az Azure-fájlmegosztások között. Ez a megközelítés akkor és csak akkor javasolt, ha elfogadja az állásidőt, és egyáltalán nem garantálja a kiszolgálómegosztások adatváltozását a kezdeti betöltés során. 
 
1. Győződjön meg arról, hogy a kiszolgálókon található adatok nem változnak az adatbehozás folyamata során.
1. Az Azure-fájlmegosztások előzetes átvitele a kiszolgálói adatokkal bármilyen adatátviteli eszköz használatával az SMB-n keresztül. Például Robocopy. Rest-hez is használhatja az AzCopyt. Az AzCopyt mindenképpen a megfelelő kapcsolók használatával használja az ACL-időbélyegek és -attribútumok megőrzéséhez.
1. Hozzon Azure File Sync a meglévő megosztásokra mutasson a kívánt kiszolgálóvégpontokkal.
1. Hagyja, hogy a szinkronizálás befejezze az egyeztetési folyamatot az összes végponton. 
1. Az egyeztetés befejezése után megnyithatja a megosztásokat a módosításokhoz.
 
Az előzetes vetésnek jelenleg van néhány korlátja: 
- A kiszolgálón a szinkronizálási topológia teljes körű és futó futtatása előtti adatváltozások ütközéseket okozhatnak a kiszolgálóvégponton.  
- A felhőbeli végpont létrehozása után a Azure File Sync futtat egy folyamatot, amely észleli a felhőben lévő fájlokat a kezdeti szinkronizálás megkezdése előtt. A folyamat befejezéséhez szükséges idő a különböző tényezőktől függ, például a hálózati sebességtől, a rendelkezésre álló sávszélességtől, valamint a fájlok és mappák számától. Az előzetes kiadás hozzávetőleges becsléséhez az észlelési folyamat körülbelül 10 fájl/s értéken fut.  Ezért még ha az előzetes adatokat gyorsan is le lehet edni, a teljes rendszer teljes futási ideje jelentősen hosszabb lehet, ha az adatok előre feltöltve lesznek a felhőben.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Önkiszolgáló visszaállítás a korábbi verziók és a VSS segítségével (Kötet árnyékmásolata szolgáltatás)

> [!IMPORTANT]
> Az alábbi információk csak a storage sync agent 9-es (vagy újabb) verziójával használhatók. A 9-nél alacsonyabb verziók nem fogják a StorageSyncSelfService parancsmagokat.

A Korábbi verziók olyan Windows-szolgáltatás, amely lehetővé teszi, hogy egy kötet kiszolgálóoldali VSS-pillanatképei segítségével visszaállítható verziókat jelenítsen meg egy SMB-ügyfélnek.
Ez egy hatékony, gyakran önkiszolgáló visszaállításnak is nevezik a forgatókönyvet, amely nem a rendszergazdai visszaállítástól függ, hanem közvetlenül az információkkal dolgozó munkatársak számára.

A VSS-pillanatképek és az előző verziók a Azure File Sync. A felhőbeli rétegezést azonban kompatibilis módra kell állítani. Számos Azure File Sync kiszolgálóvégpont létezhet ugyanazon a köteten. Az alábbi PowerShell-hívást kötetenként kell elvégze, amely akár egy olyan kiszolgálóvégponttal is rendelkezik, ahol felhőbeli rétegezést tervez vagy használ.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

A VSS-pillanatképek egy teljes kötetről vannak készítve. Alapértelmezés szerint egy adott kötethez legfeljebb 64 pillanatkép készíthető, mivel elegendő hely áll rendelkezésre a pillanatképek tárolására. A VSS ezt automatikusan kezeli. Az alapértelmezett pillanatkép-ütemezés naponta két pillanatképet készít, hétfőtől péntekig. Ez az ütemezés egy ütemezett Windows-feladattal konfigurálható. A fenti PowerShell-parancsmag két dolgot tesz:
1. Úgy konfigurálja az Azure File Syncs felhőbeli rétegezését a megadott köteten, hogy kompatibilis legyen a korábbi verziókkal, és garantálja, hogy a fájlok visszaállíthatóak legyenek egy korábbi verzióból, még akkor is, ha a kiszolgálón a felhőbe vannak rétegzve. 
1. Engedélyezi az alapértelmezett VSS-ütemezést. Ezt követően dönthet úgy, hogy később módosítja. 

> [!Note]  
> Két fontos dolgot kell megjegyeznie:
>- Ha a -Force paramétert használja, és a VSS jelenleg engedélyezve van, akkor felülírja a VSS aktuális pillanatkép-ütemezését, és lecseréli az alapértelmezett ütemezésre. A parancsmag futtatása előtt mentse az egyéni konfigurációt.
> - Ha ezt a parancsmagot egy fürtcsomóponton használja, akkor a fürt összes többi csomópontján is futtatnia kell. 

A következő parancsmag futtatásával ellenőrizze, hogy engedélyezve van-e az önkiszolgáló visszaállítás kompatibilitása.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Felsorolja a kiszolgálón található összes kötetet, valamint az egyes felhőbeli rétegezésekkel kompatibilis napok számát. Ezt a számot a rendszer automatikusan kiszámítja a kötetenkénti maximálisan lehetséges pillanatképek és az alapértelmezett pillanatkép-ütemezés alapján. Így alapértelmezés szerint az információ-feldolgozónak bemutatott összes korábbi verzió használható a visszaállításhoz. Ugyanez igaz, ha úgy módosítja az alapértelmezett ütemezést, hogy több pillanatképet készítsen.
Ha azonban úgy módosítja az ütemezést, hogy az elérhető pillanatképet eredményez a kompatibilis nap értéknél régebbi köteten, akkor a felhasználók nem fogják tudni használni ezt a régebbi pillanatképet (az előző verziót) a visszaállításhoz.

> [!Note]
> Az önkiszolgáló visszaállítás engedélyezése hatással lehet az Azure Storage-használatra és a számlázásra. Ez a hatás a kiszolgálón jelenleg rétegzett fájlokra korlátozódik. A funkció engedélyezése biztosítja, hogy a felhőben elérhető egy fájlverzió, amely egy korábbi verzió (VSS-pillanatkép) bejegyzésen keresztül hivatkozható.
>
> Ha letiltja a funkciót, az Azure-tárterület-használat lassan csökken a kompatibilis napok időkeretének lejártáig. Ezt nem lehet felgyorsítni. 

A KÖTETENKÉNTI VSS-pillanatképek alapértelmezett maximális száma (64) és az alapértelmezett ütemezés szerint ezek a pillanatképek a köteten tárolható VSS-pillanatképek számától függően legfeljebb 45 nap korábbi verzióból állíthatók vissza.

Ha kötetenként legfeljebb 64 VSS-pillanatkép nem a megfelelő beállítás, ezt az értéket egy beállításkulcs [segítségével módosíthatja.](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)
Ahhoz, hogy az új korlátozás életbe lép, újra futtatnia kell a parancsmagot, hogy engedélyezze a korábbi verziók kompatibilitását minden korábban engedélyezett köteten, a -Force jelzővel pedig vegye figyelembe a VSS-pillanatképek kötetenkénti új maximális számát. Ez a kompatibilis napok újonnan kiszámított számát eredményezi. Vegye figyelembe, hogy ez a módosítás csak az újonnan rétegzett fájlokra lesz hatással, és felülírja a VSS-ütemezés testreszabását.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Új és módosított fájlok proaktív visszahívása egy Azure-fájlmegosztásból

A 11-es ügynökverzióval új mód válik elérhetővé a kiszolgálóvégponton. Ez a mód lehetővé teszi a globálisan elosztott vállalatok számára, hogy a kiszolgáló gyorsítótárát egy távoli régióban előre feltöltik még azelőtt, hogy a helyi felhasználók hozzáférnek a fájlokhoz. Ha engedélyezve van egy kiszolgálóvégponton, akkor a kiszolgáló visszahívja az Azure-fájlmegosztásban létrehozott vagy módosított fájlokat.

### <a name="scenario"></a>Eset

Egy globálisan elosztott vállalat rendelkezik fiókirodákkal az Egyesült Államok és India területén. Reggel (amerikai idő szerint) az információs dolgozók létrehoznak egy új mappát és új fájlokat egy teljesen új projekthez, és egész nap ezen dolgoznak. Azure File Sync azure-fájlmegosztásba (felhőbeli végpont) szinkronizálja a mappát és a fájlokat. Az indiai adatmunkások az időzónában folytatják a munkát a projekten. Amikor reggel érkeznek, az indiai helyi Azure File Sync-kompatibilis kiszolgálónak helyileg kell elérhetővé tennie ezeket az új fájlokat, hogy az indiai csapat hatékonyan dolgoztata ki a helyi gyorsítótárat. Ennek a módnak a engedélyezése megakadályozza, hogy a kezdeti fájlelérés az igény szerinti visszahívás miatt lassabb legyen, és lehetővé teszi, hogy a kiszolgáló proaktívan visszahívja a fájlokat, amint azok módosultak vagy létrejöttek az Azure-fájlmegosztásban.

> [!IMPORTANT]
> Fontos tudni, hogy az Azure-fájlmegosztás kiszolgálón történt változásainak nyomon követése növelheti az Azure-ból származó bejövő forgalmat és számlát. Ha a kiszolgálóra visszahívott fájlokra nincs szükség helyileg, a szükségtelen kiszolgáló-visszahívás negatív következményekkel járhat. Akkor használja ezt a módot, ha tudja, hogy a gyorsítótár a kiszolgálón a legutóbbi felhőbeli módosításokkal való előzetes felhasználásával pozitív hatással lesz a kiszolgálón lévő fájlokat használó felhasználókra vagy alkalmazásokra.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Kiszolgálóvégpont engedélyezése az Azure-fájlmegosztások módosulásának proaktív visszahívása érdekében

# <a name="portal"></a>[Portál](#tab/proactive-portal)

1. A [Azure Portal](https://portal.azure.com/)a Társzinkronizálási szolgáltatáshoz, válassza ki a megfelelő szinkronizálási csoportot, majd azonosítsa azt a kiszolgálóvégpontot, amelyhez szorosan nyomon szeretné követni az Azure-fájlmegosztás (felhőbeli végpont) változásait.
1. A felhőbeli rétegezés szakaszban keresse meg az "Azure-fájlmegosztás letöltése" témakört. Használhatja a jelenleg kiválasztott módot, és módosíthatja az Azure-fájlmegosztások változásainak pontosabb nyomon követéséhez, és proaktív módon visszahívhatja őket a kiszolgálóra.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Kép a jelenleg érvényben lévő kiszolgálóvégpont Azure-fájlmegosztási letöltési viselkedéséről, valamint egy olyan gombról, amely lehetővé teszi a módosításhoz használt menü megnyitását.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

A PowerShellben a kiszolgálóvégpont tulajdonságait a [Set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) parancsmaggal módosíthatja.

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Központi telepítés Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) áttelepítése Azure File Sync
DFS-R üzemelő példány áttelepítése a Azure File Sync:

1. Hozzon létre egy szinkronizálási csoportot, amely a lecserélt DFS-R topológiát képviseli.
1. Kezdjen azzal a kiszolgálóval, amely az áttelepítéshez szükséges DFS-R topológiában található összes adatot tartalmaz. Telepítse Azure File Sync a kiszolgálóra.
1. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálóvégpontot az első migrálható kiszolgálóhoz. Ne engedélyezze a felhőbeli rétegezést.
1. Hagyja, hogy az összes adat szinkronizálva legyen az Azure-fájlmegosztással (felhőbeli végponttal).
1. Telepítse és regisztrálja a Azure File Sync ügynököt az összes többi DFS-R-kiszolgálón.
1. Tiltsa le a DFS-R-t. 
1. Hozzon létre egy kiszolgálóvégpontot az egyes DFS-R kiszolgálókon. Ne engedélyezze a felhőbeli rétegezést.
1. Győződjön meg arról, hogy a szinkronizálás befejeződött, és szükség szerint tesztelje a topológiát.
1. A DFS-R kivezetve.
1. A felhőbeli rétegezés most már bármilyen kiszolgálóvégponton engedélyezhető.

További információ: Azure File Sync [(DFS) elosztott fájlrendszer.](file-sync-planning.md#distributed-file-system-dfs)

## <a name="next-steps"></a>Következő lépések
- [Kiszolgálóvégpont hozzáadása Azure File Sync eltávolítása](file-sync-server-endpoint.md)
- [Kiszolgáló regisztrálása vagy regisztrációjának Azure File Sync](file-sync-server-registration.md)
- [Az Azure File Sync monitorozása](file-sync-monitoring.md)