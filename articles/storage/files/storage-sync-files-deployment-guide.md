---
title: Azure File Sync üzembe helyezése | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe a Azure File Synct, az elejétől a végéig, a Azure Portal, a PowerShell vagy az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4814a12a870d5317ad91c3514327ba0daad7ed69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225370"
---
# <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése
A Azure File Sync segítségével központilag kezelheti a szervezete fájlmegosztást Azure Filesban, miközben megőrizheti a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Tetszőleges számú gyorsítótárral rendelkezhet a világ minden tájáról.

Javasoljuk, hogy olvassa el a [Azure Files központi telepítésének megtervezését](storage-files-planning.md) és a [Azure file Sync központi telepítés megtervezését](storage-sync-files-planning.md) , mielőtt elvégezte a jelen cikkben ismertetett lépéseket.

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Egy Azure-fájlmegosztás abban a régióban, amelyet telepíteni kíván Azure File Sync. További információt a következő témakörben talál:
    - A [régió rendelkezésre állása](storage-sync-files-planning.md#azure-file-sync-region-availability) Azure file Sync számára.
    - [Hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md) a fájlmegosztás létrehozásának lépésenkénti leírásához.
1. A Windows Server vagy a Windows Server-fürt legalább egy támogatott példánya Azure File Sync-vel való szinkronizálásra. A Windows Server támogatott verzióival és az ajánlott Rendszererőforrásokkal kapcsolatos további információkért lásd a [Windows-fájlkiszolgáló szempontjait](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Egy Azure-fájlmegosztás abban a régióban, amelyet telepíteni kíván Azure File Sync. További információt a következő témakörben talál:
    - A [régió rendelkezésre állása](storage-sync-files-planning.md#azure-file-sync-region-availability) Azure file Sync számára.
    - [Hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md) a fájlmegosztás létrehozásának lépésenkénti leírásához.
1. A Windows Server vagy a Windows Server-fürt legalább egy támogatott példánya Azure File Sync-vel való szinkronizálásra. A Windows Server támogatott verzióival és az ajánlott Rendszererőforrásokkal kapcsolatos további információkért lásd a [Windows-fájlkiszolgáló szempontjait](storage-sync-files-planning.md#windows-file-server-considerations).

1. Az az PowerShell-modul a PowerShell 5,1 vagy a PowerShell 6 + használatával is használható. Használhatja az az PowerShell-modult a Azure File Synchoz bármely támogatott rendszeren, beleértve a nem Windows rendszerű rendszereket is, azonban a kiszolgáló regisztrációs parancsmagját mindig futtatni kell a regisztrálni kívánt Windows Server-példányon (ezt közvetlenül vagy a PowerShell-távelérésen keresztül teheti meg). Windows Server 2012 R2 rendszeren ellenőrizheti, hogy legalább a PowerShell 5,1-et futtatja-e. \* a **$PSVersionTable** objektum **PSVersion** tulajdonságának értékét tekinti meg:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Ha a **PSVersion** értéke kisebb, mint 5,1 \* , akkor a Windows Server 2012 R2 legtöbb friss telepítése esetén a [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616)letöltésével és telepítésével egyszerűen frissíthető. A Windows Server 2012 R2-hoz letölthető és telepíthető megfelelő csomag a **Win 8.1 andw2k12r2-kb \* \* \* \* \* \* \* -x64. msu**. 

    A PowerShell 6 + bármilyen támogatott rendszerrel használható, és a [GitHub-oldalán](https://github.com/PowerShell/PowerShell#get-powershell)is letölthető. 

    > [!Important]  
    > Ha azt tervezi, hogy a kiszolgáló regisztrációjának felhasználói felületét használja, és nem közvetlenül a PowerShellből regisztrálja, akkor a PowerShell 5,1-et kell használnia.

1. Ha a PowerShell 5,1 használatát választotta, győződjön meg arról, hogy legalább .NET 4.7.2 van telepítve. További információ a [.NET-keretrendszer verzióiról és függőségeiről](/dotnet/framework/migration-guide/versions-and-dependencies) a rendszeren.

    > [!Important]  
    > Ha a .NET 4.7.2 + rendszert a Windows Server Core verzióra telepíti, a és a jelzőket kell telepítenie, `quiet` `norestart` vagy a telepítés sikertelen lesz. Ha például a .NET 4,8-et telepíti, a parancs a következőhöz hasonlóan fog kinézni:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az az PowerShell-modul, amely a következő utasításokat követve telepíthető: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az az. StorageSync modul mostantól automatikusan települ az az PowerShell modul telepítésekor.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Egy Azure-fájlmegosztás abban a régióban, amelyet telepíteni kíván Azure File Sync. További információt a következő témakörben talál:
    - A [régió rendelkezésre állása](storage-sync-files-planning.md#azure-file-sync-region-availability) Azure file Sync számára.
    - [Hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md) a fájlmegosztás létrehozásának lépésenkénti leírásához.
1. A Windows Server vagy a Windows Server-fürt legalább egy támogatott példánya Azure File Sync-vel való szinkronizálásra. A Windows Server támogatott verzióival és az ajánlott Rendszererőforrásokkal kapcsolatos további információkért lásd a [Windows-fájlkiszolgáló szempontjait](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)

   Ha szeretné, az oktatóanyag lépéseinek elvégzéséhez Azure Cloud Shell is használhatja.  A Azure Cloud Shell egy interaktív rendszerhéj-környezet, amelyet a böngészőben használhat.  Cloud Shell elindítása a következő módszerek egyikével:

   - Kattintson a **Kipróbálás** elemre egy kódblokk jobb felső sarkában. **Próbálja** meg megnyitni Azure Cloud shell, de nem másolja automatikusan a kódot Cloud shellre.

   - Nyissa meg Cloud Shell [https://shell.azure.com](https://shell.azure.com)

   - A [Azure Portal](https://portal.azure.com) jobb felső sarkában lévő menüsorban kattintson a **Cloud Shell** gombra

1. Jelentkezzen be.

   Jelentkezzen be az [az login](/cli/azure/reference-index#az-login) paranccsal, ha a CLI helyi telepítését használja.

   ```azurecli
   az login
   ```

    A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

1. Telepítse az az [filesync](/cli/azure/ext/storagesync/storagesync) Azure CLI bővítményt.

   ```azurecli
   az extension add --name storagesync
   ```

   A **storagesync** -bővítmény hivatkozásának telepítése után a következő figyelmeztetés jelenik meg.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>A Windows Server előkészítése az Azure File Sync használatára
Az **Internet Explorer fokozott biztonsági beállításainak** letiltásával minden olyan kiszolgáló esetében, amelyet Azure file Synchoz kíván használni, beleértve a feladatátvevő fürt minden egyes kiszolgálói csomópontját is. Erre csak a kiszolgáló kezdeti regisztrációja esetén van szükség. A kiszolgáló regisztrációja után újra engedélyezheti.

# <a name="portal"></a>[Portál](#tab/azure-portal)
> [!Note]  
> Ezt a lépést kihagyhatja, ha a Windows Server Core-on telepíti Azure File Sync.

1. Nyissa meg a Kiszolgálókezelőt.
2. Kattintson a **helyi kiszolgáló** elemre:  
    ![A "Helyi kiszolgáló" a Kiszolgálókezelő felhasználói felület bal oldalán](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. A **Tulajdonságok** lapon, válassza ki az **Internet Explorer fokozott biztonsági beállításai** hivatkozást.  
    ![Az "Internet Explorer – fokozott biztonsági beállítások" panel a Kiszolgálókezelőben](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Az **Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen válassza ki a **ki** lehetőséget a **rendszergazdák** és a **felhasználók** számára:  
    ![Az Internet Explorer fokozott biztonsági beállítások felugró ablak a "Kikapcsolva" beállítással](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az Internet Explorer fokozott biztonsági beállításainak letiltásához futtassa a következőt egy emelt szintű PowerShell-munkamenetből:

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

Kövesse a Azure Portal vagy a PowerShell utasításait.

---

## <a name="deploy-the-storage-sync-service"></a>A Társzinkronizálási szolgáltatás üzembe helyezése 
A Azure File Sync üzembe helyezése a **Storage Sync szolgáltatás** erőforrásának a kiválasztott előfizetéshez tartozó erőforráscsoporthoz való elhelyezésével kezdődik. Javasoljuk, hogy a lehető legtöbbet kiépítse igény szerint. Létre fog hozni egy megbízhatósági kapcsolatot a kiszolgálók és az erőforrás között, és a kiszolgáló csak egy Storage Sync szolgáltatásban regisztrálható. Ezért javasoljuk, hogy több tárolási szinkronizálási szolgáltatást telepítsen a kiszolgálók különböző csoportjaihoz. Ne feledje, hogy a különböző Storage Sync Services-kiszolgálók nem tudnak szinkronizálni egymással.

> [!Note]
> A Storage Sync szolgáltatás örökli a hozzáférési engedélyeket az előfizetésből és az erőforráscsoporthoz telepített erőforrás-csoportból. Javasoljuk, hogy alaposan vizsgálja meg, ki férhet hozzá. Az írási hozzáféréssel rendelkező entitások megkezdhetik a Storage Sync szolgáltatásban regisztrált kiszolgálók új fájljainak szinkronizálását, és az adatok a számukra elérhető Azure Storage-ba való áthaladását okozzák.

# <a name="portal"></a>[Portál](#tab/azure-portal)
A Storage Sync szolgáltatás üzembe helyezéséhez lépjen a [Azure Portal](https://portal.azure.com/), kattintson az *erőforrás létrehozása* elemre, majd keresse meg Azure file Sync. A keresési eredmények között válassza a **Azure file Sync** lehetőséget, majd a **Létrehozás** elemre kattintva nyissa meg a **Storage Sync telepítése** lapot.

A megnyíló panelen adja meg a következőket:

- **Name (név**): a Storage Sync szolgáltatás egyedi neve (régiónként).
- **Előfizetés**: az előfizetés, amelyben létre szeretné hozni a Storage Sync szolgáltatást. A szervezet konfigurációs stratégiájától függően előfordulhat, hogy egy vagy több előfizetéshez fér hozzá. Az Azure-előfizetés az egyes felhőalapú szolgáltatások (például a Azure Files) számlázásának legalapvetőbb tárolója.
- **Erőforráscsoport**: az erőforráscsoport az Azure-erőforrások logikai csoportja, például egy Storage-fiók vagy egy Storage Sync szolgáltatás. Létrehozhat egy új erőforráscsoportot, vagy használhat meglévő erőforráscsoportot Azure File Synchoz. (Az erőforráscsoportok tárolóként való használatát javasoljuk, hogy a szervezet számára logikailag elkülönítse az erőforrásokat, például a HR-erőforrások vagy egy adott projekt erőforrásainak csoportosítását.)
- **Hely**: az a régió, amelyben a Azure file Sync központilag telepíteni kívánja. Ebben a listában csak a támogatott régiók érhetők el.

Ha elkészült, válassza a **Létrehozás** lehetőséget a Storage Sync szolgáltatás telepítéséhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Cserélje le a `<Az_Region>` `<RG_Name>` és a értéket a `<my_storage_sync_service>` saját értékeire, majd használja a következő parancsokat a Storage Sync szolgáltatás létrehozásához és telepítéséhez:

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

Kövesse a Azure Portal vagy a PowerShell utasításait.

---

## <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az ügynököt a [Microsoft letöltőközpontból](https://go.microsoft.com/fwlink/?linkid=858257)töltheti le. Ha a letöltés befejeződött, kattintson duplán az MSI-csomagra a Azure File Sync-ügynök telepítésének elindításához.

> [!Important]  
> Ha a Azure File Sync feladatátvevő fürttel kívánja használni, akkor a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell. A fürt minden csomópontjának regisztrálva kell lennie a Azure File Sync használatához.

javasoljuk, hogy tegye a következőket:
- A hibaelhárítás és a kiszolgáló karbantartásának leegyszerűsítése érdekében hagyja meg az alapértelmezett telepítési útvonalat (C:\Program Files\Azure\StorageSyncAgent).
- Microsoft Update engedélyezése a Azure File Sync naprakészen tartása érdekében. A Azure File Sync ügynök összes frissítése, beleértve a szolgáltatás frissítéseit és gyorsjavításait is, Microsoft Update. Javasoljuk, hogy a legújabb frissítést a Azure File Sync telepítéséhez telepítse. További információ: [Azure file Sync frissítési szabályzat](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Ha a Azure File Sync-ügynök telepítése befejeződött, a kiszolgáló regisztrációs FELÜLETe automatikusan megnyílik. A regisztrálás előtt rendelkeznie kell egy Storage Sync szolgáltatással; Tekintse meg a Storage Sync szolgáltatás létrehozásának következő szakaszát.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-kód futtatásával töltse le az operációs rendszer Azure File Sync ügynökének megfelelő verzióját, és telepítse azt a rendszeren.

> [!Important]  
> Ha a Azure File Sync feladatátvevő fürttel kívánja használni, akkor a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell. A fürt minden egyes csomópontjának regisztrálnia kell a Azure File Syncsal való együttműködéshez.

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

Kövesse a Azure Portal vagy a PowerShell utasításait.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatással
A Windows Server regisztrálásával a Társzinkronizálási szolgáltatásra megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Társzinkronizálási szolgáltatás között. A kiszolgáló csak egy Társzinkronizálási szolgáltatásra regisztrálható és az ugyanahhoz a Társzinkronizálási szolgáltatáshoz társított kiszolgálókkal és Azure-fájlmegosztásokkal képes szinkronizálni.

> [!Note]
> A kiszolgáló regisztrálása az Azure-beli hitelesítő adataival megbízhatósági kapcsolatot hoz létre a Storage Sync szolgáltatás és a Windows Server között, azonban ezt követően a kiszolgáló létrehozza és felhasználja a saját identitását, amely mindaddig érvényes, amíg a kiszolgáló regisztrálva marad, és az aktuális közös hozzáférésű aláírási jogkivonat (Storage SAS) érvényes. A kiszolgáló regisztrációjának törlése után nem lehet új SAS-jogkivonatot kiállítani a kiszolgálónak, így a kiszolgáló nem férhet hozzá az Azure-fájlmegosztás eléréséhez, és megszüntetheti a szinkronizálást.

A kiszolgálót regisztráló rendszergazdának a megadott Storage Sync szolgáltatás felügyeleti szerepkörök **tulajdonosa** vagy **közreműködője** tagjának kell lennie. Ez a Storage Sync szolgáltatás Azure Portal **Access Control (iam)** alatt konfigurálható.

Az is előfordulhat, hogy a rendszergazdák számára lehetővé teszi a kiszolgálók regisztrálását, amelyek lehetővé teszik, hogy a szinkronizálást a Storage Sync szolgáltatásban is konfigurálják. Ahhoz, hogy létre kellene hoznia egy egyéni szerepkört, amely felsorolja azokat a rendszergazdákat, akik csak a kiszolgálók regisztrálására jogosultak, és az egyéni szerepkört a következő engedélyekkel ruházhatja fel:

* "Microsoft. StorageSync/storageSyncServices/registeredServers/Write"
* "Microsoft. StorageSync/storageSyncServices/Read"
* "Microsoft. StorageSync/storageSyncServices/munkafolyamatok/olvasás"
* "Microsoft. StorageSync/storageSyncServices/munkafolyamatok/műveletek/olvasás"

# <a name="portal"></a>[Portál](#tab/azure-portal)
A kiszolgáló regisztrációjának felhasználói felületének automatikusan meg kell nyílnia a Azure File Sync ügynök telepítése után. Ha nem, akkor manuálisan is megnyithatja, a fájl helye: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a kiszolgáló regisztrációjának felhasználói felülete, a kezdéshez válassza a **Bejelentkezés** lehetőséget.

A bejelentkezést követően a rendszer a következő információk megadását kéri:

![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: a Storage Sync szolgáltatást tartalmazó előfizetés (lásd: [a Storage Sync szolgáltatás üzembe helyezése](#deploy-the-storage-sync-service)). 
- **Erőforráscsoport**: a Storage Sync szolgáltatást tartalmazó erőforráscsoport.
- **Storage Sync szolgáltatás**: annak a Storage Sync szolgáltatásnak a neve, amellyel regisztrálni kíván.

Miután kiválasztotta a megfelelő adatokat, válassza a **regisztráció** lehetőséget a kiszolgáló regisztrációjának befejezéséhez. A regisztrációs folyamat részeként a rendszer újabb bejelentkezésre kéri fel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kövesse a Azure Portal vagy a PowerShell utasításait.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Szinkronizálási csoport és felhő végpont létrehozása
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. A szinkronizálási csoportnak tartalmaznia kell egy felhőbeli végpontot, amely Azure-fájlmegosztást és egy vagy több kiszolgáló végpontot jelöl. A kiszolgálói végpont egy regisztrált kiszolgáló elérési útját jelöli. A kiszolgálók több szinkronizálási csoportban is rendelkezhetnek kiszolgálói végpontokkal. Annyi szinkronizálási csoportot hozhat létre, amennyi szükséges a kívánt szinkronizálási topológia megfelelő leírásához.

A Felhőbeli végpont egy Azure-fájlmegosztás mutatója. Minden kiszolgálói végpont szinkronizálva lesz egy Felhőbeli végponttal, így a felhő végpontja a középpontba kerül. Az Azure-fájlmegosztás tárolási fiókjának ugyanabban a régióban kell lennie, mint a Storage Sync szolgáltatásnak. Az Azure-fájlmegosztás teljes egészében szinkronizálva lesz egy kivétel: egy speciális mappa, amely az NTFS-kötet rejtett "rendszerkötet-információ" mappájához hasonlít. Ennek a könyvtárnak a neve ". SystemShareInformation". Fontos szinkronizálási metaadatokat tartalmaz, amelyek nem szinkronizálhatók más végpontokkal. Ne használja vagy törölje!

> [!Important]  
> A szinkronizálási csoportban bármilyen Felhőbeli végpontot vagy kiszolgálói végpontot módosíthat, és a fájlok szinkronizálva vannak a szinkronizálási csoport többi végpontján. Ha közvetlenül módosítja a Felhőbeli végpontot (Azure-fájlmegosztás), a módosításokat először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A változás-észlelési feladatok csak 24 óránként egyszer indíthatók el egy Felhőbeli végponton. További információ: [Azure Files gyakori kérdések](storage-files-faq.md#afs-change-detection).

A Felhőbeli végpontot létrehozó rendszergazdának a felügyeleti szerepkör **tulajdonosának** kell lennie ahhoz a Storage-fiókhoz, amely tartalmazza azt az Azure-fájlmegosztást, amelyre a felhő végpontja mutat. Ez a Storage-fiókhoz tartozó Azure Portal **Access Control (iam)** lehetőséggel konfigurálható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Egy szinkronizálási csoport létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/)a Storage Sync Service-t, majd válassza a **+ szinkronizálás csoport**:

![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

A megnyíló panelen adja meg a következő információkat a szinkronizálási csoport létrehozásához egy felhőbeli végponttal:

- **Szinkronizálási csoport neve**: a létrehozandó szinkronizálási csoport neve. A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név.
- **Előfizetés**: az előfizetés, amelyben üzembe helyezte a Storage Sync szolgáltatást a [Storage Sync szolgáltatás üzembe helyezése](#deploy-the-storage-sync-service)során.
- **Storage-fiók**: Ha a **Storage-fiók kiválasztása** lehetőséget választja, egy másik panel jelenik meg, ahol kiválaszthatja azt a Storage-fiókot, amelyhez a szinkronizálni kívánt Azure-fájlmegosztás tartozik.
- **Azure-fájlmegosztás**: annak az Azure-fájlmegosztásnak a neve, amellyel szinkronizálni kíván.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A szinkronizálási csoport létrehozásához hajtsa végre a következő PowerShellt. Ne felejtse el lecserélni a `<my-sync-group>` szinkronizálási csoport kívánt nevére.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

A szinkronizálási csoport sikeres létrehozása után létrehozhatja a Felhőbeli végpontot. Ügyeljen arra, hogy `<my-storage-account>` `<my-file-share>` a és a értékét a várt értékekkel helyettesítse.

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

Hozzon létre egy új szinkronizálási csoportot az az [storagesync Sync-Group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) paranccsal.  Ha az összes CLI-parancshoz alapértelmezett erőforráscsoportot szeretne használni, használja [az az configure](/cli/azure/reference-index#az-configure)parancsot.

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Hozzon létre egy új Felhőbeli végpontot az az [storagesync Sync-Group Cloud-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) paranccsal.

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
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten. A kiszolgálói végpontnak egy regisztrált kiszolgálón (nem pedig csatlakoztatott megosztáson) található elérési útnak kell lennie, és a felhőalapú rétegek használatához az elérési útnak nem rendszerköteten kell lennie. A hálózati csatolású tároló (NAS) nem támogatott.

> [!NOTE]
> Az elérési út vagy a meghajtóbetűjel módosítása a kiszolgálói végpont köteten való létrehozása után nem támogatott. Győződjön meg arról, hogy a regisztrált kiszolgálón a végső elérési utat használja.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Kiszolgálói végpont hozzáadásához nyissa meg az újonnan létrehozott szinkronizálási csoportot, majd válassza a **kiszolgáló-végpont hozzáadása** elemet.

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

A **Kiszolgálói végpont felvétele** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

- **Regisztrált kiszolgáló**: annak a kiszolgálónak vagy fürtnek a neve, amelyben létre kívánja hozni a kiszolgálói végpontot.
- **Elérési út**: a szinkronizálási csoport részeként szinkronizálandó Windows Server-elérési út.
- **Felhőbeli rétegek**: a felhőalapú rétegek engedélyezésére vagy letiltására szolgáló kapcsoló. A felhő-előállítók esetében a ritkán használt vagy a hozzáfért fájlok Azure Files.
- **Kötet szabad területe**: a kiszolgáló végpontját tartalmazó köteten foglalható szabad terület. Ha például a kötet szabad területe 50%-ra van állítva egy olyan köteten, amely egyetlen kiszolgálói végponttal rendelkezik, az adatmennyiség nagyjából fele lesz a Azure Files. Függetlenül attól, hogy engedélyezve van-e a felhőalapú rétegek használata, az Azure-fájlmegosztás mindig a szinkronizálási csoportban lévő összes adattal rendelkezik.
- **Kezdeti letöltési mód**: ez egy opcionális lehetőség, amely az ügynök 11-es verziójától kezdve hasznos lehet, ha az Azure-fájlmegosztás fájljai vannak, de a kiszolgálón nem. Ilyen helyzet létezhet például, ha létrehoz egy kiszolgálói végpontot egy másik fiókirodai kiszolgáló egy szinkronizálási csoportba való felvételéhez, vagy egy hibás kiszolgáló meghibásodása esetén. Ha a felhő-rétegek engedélyezve vannak, az alapértelmezés szerint csak a névteret kell visszahívnia, kezdetben nincs fájl tartalma. Ez akkor hasznos, ha úgy gondolja, hogy a felhasználói hozzáférési kérelmeknek el kell dönteniük, hogy milyen fájlokra hívja fel a rendszer a kiszolgálót. Ha a felhő-réteg le van tiltva, az alapértelmezett érték az, hogy a rendszer először letölti a névteret, majd a rendszer visszahívja a fájlokat a legutóbbi módosítás időbélyegzője alapján, amíg el nem éri a helyi kapacitást. A kezdeti letöltési mód azonban csak a névtérre módosítható. A harmadik mód csak akkor használható, ha a Felhőbeli rétegek letiltva ezen a kiszolgálói végponton. Ez a mód elkerüli a névtér visszahívását. A fájlok csak akkor jelennek meg a helyi kiszolgálón, ha teljes mértékben le lettek letöltve. Ez a mód akkor lehet hasznos, ha például egy alkalmazásnak teljes fájlokra van szüksége, és nem tudja elviselni a rétegű fájlokat a névtérben.

A kiszolgálói végpont hozzáadásához válassza a **Létrehozás** lehetőséget. A fájlok most már szinkronban vannak az Azure-fájlmegosztás és a Windows Server között. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Hajtsa végre a következő PowerShell-parancsokat a kiszolgálói végpont létrehozásához, és `<your-server-endpoint-path>` `<your-volume-free-space>` a kívánt értékekkel, majd ellenőrizze a választható kezdeti letöltési szabályzat választható beállítását.

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

Hozzon létre egy új kiszolgálói végpontot az az [storagesync Sync-Group Server-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) paranccsal.

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
Ha úgy szeretné konfigurálni az Azure-fájlok szinkronizálását, hogy a tűzfal és a virtuális hálózat beállításait is működjön, tegye a következőket:

1. A Azure Portal navigáljon a védeni kívánt Storage-fiókhoz.
1. Válassza a **tűzfalak és virtuális hálózatok** gombot a bal oldali menüben.
1. Válassza a **kiválasztott hálózatok** lehetőséget **a hozzáférés engedélyezése lehetőségnél**.
1. Győződjön meg arról, hogy a kiszolgálók IP-címe vagy virtuális hálózata szerepel a megfelelő szakaszban.
1. Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a Storage-fiókhoz** jelölőnégyzet be van jelölve.
1. A beállítások mentéséhez kattintson a **Mentés** gombra.

![A tűzfal és a virtuális hálózat beállításainak konfigurálása az Azure file Sync szolgáltatással való együttműködéshez](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Előkészítés Azure File Sync
A Azure File Sync első alkalommal történő bevezetésének ajánlott lépései a nulla állásidővel, miközben a teljes fájl-hűség és a hozzáférés-vezérlési lista (ACL) megőrzése a következő:
 
1. Helyezzen üzembe egy Storage Sync szolgáltatást.
1. Hozzon létre egy szinkronizálási csoportot.
1. Telepítse Azure File Sync-ügynököt a kiszolgálóra a teljes adatkészlettel.
1. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálói végpontot a megosztáson. 
1. A szinkronizálás az Azure-fájlmegosztás (Felhőbeli végpont) teljes feltöltését teszi lehetővé.  
1. A kezdeti feltöltés befejezése után telepítse Azure File Sync ügynököt a többi kiszolgálóra.
1. Hozzon létre új fájlmegosztást a többi kiszolgálón.
1. Szükség esetén hozzon létre kiszolgálói végpontokat az új fájlmegosztás és a felhőalapú adatmegosztási szabályzat alapján. (Ehhez a lépéshez további tárterület szükséges a kezdeti beállításhoz.)
1. Lehetővé teszi Azure File Sync ügynöknek a tényleges adatátvitel nélkül a teljes névtér gyors visszaállítását. A névtér teljes szinkronizálása után a szinkronizálási motor kitölti a helyi lemezterületet a kiszolgálói végponthoz tartozó felhőalapú rétegű házirend alapján. 
1. Győződjön meg arról, hogy a szinkronizálás befejeződik, és szükség szerint tesztelje a topológiát. 
1. Átirányíthatja a felhasználókat és az alkalmazásokat az új megosztásra.
1. Lehetősége van a kiszolgálókon lévő duplikált megosztások törlésére is.
 
Ha nem rendelkezik további tárterülettel a bevezetéshez, és szeretné csatolni a meglévő megosztásokhoz, az Azure Files-megosztásokban lévő adatmagot is megadhatja. Ez a megközelítés akkor javasolt, ha és csak akkor, ha az állásidőt el tudja fogadni, és a kezdeti bevezetési folyamat során egyáltalán nem módosítja a kiszolgálói megosztások adatváltozását. 
 
1. A bevezetési folyamat során győződjön meg arról, hogy a kiszolgálók egyikén sem változhatnak az adatmennyiség.
1. Az Azure-fájlmegosztás előzetes használata a kiszolgálói adatokkal az SMB protokollon keresztül bármely adatátviteli eszközzel. Robocopy, például:. A REST-AzCopy is használhatja. Ügyeljen arra, hogy a AzCopy a megfelelő kapcsolók használatával őrizze meg az ACL-ek időbélyegeit és attribútumait.
1. Hozzon létre Azure File Sync topológiát a meglévő megosztásokra mutató kívánt kiszolgálói végpontokkal.
1. Az összes végponton engedélyezze a szinkronizálás befejezési egyeztetési folyamatát. 
1. Az egyeztetés befejeződése után megnyithatja a megosztásokat a változásokhoz.
 
Jelenleg a kivetés előtti megközelítés néhány korlátozással rendelkezik – 
- Az adatváltozások a kiszolgálón a szinkronizálási topológia teljes körű és futása előtt ütközéseket okozhatnak a kiszolgálói végpontokon.  
- A Felhőbeli végpont létrehozása után Azure File Sync futtat egy folyamatot a felhőben lévő fájlok észleléséhez a kezdeti szinkronizálás megkezdése előtt. A folyamat befejezéséhez szükséges idő a különböző tényezőktől, például a hálózati sebességtől, a rendelkezésre álló sávszélességtől és a fájlok és mappák számától függ. Az előzetes kiadásban felmerülő durva becslés esetében az észlelési folyamat körülbelül 10 fájlt/s-t futtat.  Ezért még akkor is, ha az előkészítési folyamat gyorsan fut, a teljes körűen futó rendszerek teljes ideje jelentősen meghaladhatja a felhőben tárolt adatmennyiséget.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Önkiszolgáló visszaállítás az előző verziók és a VSS használatával (Kötet árnyékmásolata szolgáltatás)

> [!IMPORTANT]
> A következő információk csak a Storage-szinkronizáló ügynök 9-es vagy újabb verziójával használhatók. A 9-es verziónál korábbi verziók nem rendelkeznek a StorageSyncSelfService-parancsmagokkal.

A korábbi verziók egy Windows-szolgáltatás, amely lehetővé teszi, hogy egy kötet kiszolgálóoldali VSS-pillanatképeit kihasználva egy fájl helyreállítható verzióit egy SMB-ügyfélnek mutassa.
Ez egy hatékony forgatókönyv, amelyet általában önkiszolgáló visszaállításnak nevezünk, közvetlenül az informatikai szakemberek számára, a visszaállítástól függően.

A VSS-Pillanatképek és a korábbi verziók a Azure File Synctól függetlenül működnek. A Felhőbeli rétegek megadását azonban kompatibilis módra kell beállítani. Több Azure File Sync kiszolgálói végpont is létezhet ugyanazon a köteten. A következő PowerShell-hívást olyan köteten kell elvégeznie, amely még egy olyan kiszolgálói végponttal rendelkezik, ahol a felhő-rétegre tervez vagy használ.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

A VSS-Pillanatképek egy teljes kötetről készültek. Alapértelmezés szerint akár 64 pillanatkép is létezhet egy adott köteten, mivel a pillanatképek tárolására elegendő hely áll rendelkezésre. A VSS automatikusan kezeli ezt. Az alapértelmezett pillanatkép-ütemterv naponta két pillanatképet vesz igénybe, hétfőtől péntekig. Ez az ütemezés egy Windows ütemezett feladaton keresztül konfigurálható. A fenti PowerShell-parancsmag két dolgot tesz:
1. Azt konfigurálja, hogy az Azure-fájl szinkronizálja a Felhőbeli adatmennyiséget a megadott köteten, hogy kompatibilis legyen az előző verziókkal, és garantálja, hogy a fájlok egy korábbi verzióból is visszaállíthatók, még akkor is, ha a kiszolgálón a felhőbe lett bontva. 
1. Ez lehetővé teszi az alapértelmezett VSS-ütemtervet. Ezután később is dönthet úgy, hogy módosítja. 

> [!Note]  
> Két fontos szempontot kell figyelembe venni:
>- Ha a-Force paramétert használja, és a VSS jelenleg engedélyezve van, akkor a rendszer felülírja az aktuális VSS-pillanatkép-ütemtervet, és lecseréli az alapértelmezett ütemtervre. A parancsmag futtatása előtt győződjön meg arról, hogy az egyéni konfigurációt menti.
> - Ha ezt a parancsmagot egy fürtcsomóponton használja, azt a fürt összes többi csomópontjára is futtatnia kell. 

Ha szeretné megtekinteni, hogy az önkiszolgáló visszaállítási kompatibilitás engedélyezve van-e, futtathatja a következő parancsmagot.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Felsorolja a kiszolgálón lévő összes kötetet, valamint a Felhőbeli rétegek egymással kompatibilis napjainak számát. Ezt a számot a rendszer automatikusan kiszámítja a köteten a lehetséges Pillanatképek maximális száma és az alapértelmezett pillanatkép-ütemterv alapján. Ezért alapértelmezés szerint az Information Worker szolgáltatásban bemutatott összes korábbi verzió a-ból való visszaállításra használható. Ugyanez igaz, ha úgy módosítja az alapértelmezett ütemtervet, hogy további pillanatképeket lehessen készíteni.
Ha azonban úgy módosítja az ütemtervet, hogy az a kompatibilis nap értéknél régebbi köteten elérhető pillanatképet fog eredményezni, akkor a felhasználók nem fogják tudni használni ezt a régebbi pillanatképet (korábbi verzió) a visszaállításhoz.

> [!Note]
> Az önkiszolgáló visszaállítás engedélyezése hatással lehet az Azure Storage-felhasználásra és a számlázásra. Ez a hatás a kiszolgálón jelenleg felépülő fájlokra korlátozódik. A funkció engedélyezésével biztosítható, hogy a felhőben elérhető egy olyan fájlverzió, amely egy korábbi verzió (VSS-pillanatkép) bejegyzésen keresztül hivatkozni tud rá.
>
> Ha letiltja a szolgáltatást, az Azure Storage-felhasználás lassan csökken, amíg a kompatibilis napok ablaka nem lett átadva. Ez a megoldás nem gyorsítható fel. 

A köteten tárolt VSS-Pillanatképek alapértelmezett maximális száma (64), valamint az alapértelmezett ütemterv, amely azt eredményezi, hogy a köteten tárolni kívánt VSS-Pillanatképek számától függően a rendszer legfeljebb 45 nappal korábbi verziót tud visszaállítani.

Ha a max. 64 VSS-Pillanatképek mennyisége nem a megfelelő beállítás, akkor az [értéket egy beállításkulcs segítségével módosíthatja](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Az új korlát érvénybe léptetéséhez újra kell futtatnia a parancsmagot a korábbi verziók kompatibilitásának engedélyezéséhez minden korábban engedélyezett köteten, a-Force jelzővel, hogy a köteten lévő VSS-Pillanatképek maximális számát figyelembe vegye. Ennek eredményeképpen egy újonnan számított számú kompatibilis nap fog megjelenni. Vegye figyelembe, hogy ez a változás csak az újonnan létrehozott fájlokra lép érvénybe, és felülírja az esetlegesen elvégzett VSS-ütemterv testreszabásait.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Új és módosított fájlok proaktív felidézése egy Azure-fájlmegosztás alapján

Az ügynök 11-es verziójában az új mód elérhetővé válik egy kiszolgálói végponton. Ez a mód lehetővé teszi a globálisan elosztott vállalatok számára, hogy a kiszolgálói gyorsítótárat egy előre kitöltött távoli régióban is használják, még mielőtt a helyi felhasználók hozzáférjenek a fájlokhoz. Ha engedélyezve van egy kiszolgálói végponton, ez a mód azt eredményezi, hogy a kiszolgáló felidézi az Azure-fájlmegosztás során létrehozott vagy módosított fájlokat.

### <a name="scenario"></a>Eset

A globálisan elosztott vállalatok fiókirodái az Egyesült Államokban és Indiában találhatóak. A délelőtti (amerikai idő) információkkal dolgozó szakemberek új mappát és új fájlokat hoznak létre egy új projekthez, és egész nap dolgozhatnak hozzájuk. Azure File Sync fogja szinkronizálni a mappát és a fájlokat az Azure-fájlmegosztás (Felhőbeli végpont) számára. Az indiai információs szakemberek továbbra is a projekten dolgoznak a saját időzónájában. Amikor reggel érkeznek, az indiai helyi Azure File Sync-kiszolgáló számára elérhetővé kell tennie ezeket az új fájlokat helyileg, így az indiai csapat hatékonyan dolgozhat ki helyi gyorsítótárból. Ha engedélyezi ezt a módot, azzal megakadályozza, hogy az igény szerinti visszahívás miatt a kezdeti fájlhoz való hozzáférés lassabb legyen, és lehetővé teszi a kiszolgáló számára, hogy az Azure-fájlmegosztás módosításakor vagy létrehozásakor proaktív módon felidézze a fájlokat.

> [!IMPORTANT]
> Fontos tisztában lenni azzal, hogy az Azure-fájlmegosztás azon változásainak nyomon követése, amelyek szorosan a kiszolgálón vannak, növelheti a kimenő forgalmat és az Azure-ból származó számlát. Ha a fájlok visszahívása a kiszolgálóra nincs ténylegesen szükség helyileg, akkor a kiszolgálóra való szükségtelen visszahívás negatív következményekkel járhat. Akkor használja ezt a módot, ha tudja, hogy a felhőben lévő legutóbbi módosításokkal rendelkező kiszolgáló gyorsítótárának előzetes kitöltése pozitív hatással lesz a kiszolgálón található fájlokat használó felhasználókra vagy alkalmazásokra.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Kiszolgálói végpont engedélyezése az Azure-fájlmegosztás változásának proaktív felidézésére

# <a name="portal"></a>[Portál](#tab/proactive-portal)

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Storage Sync szolgáltatást, válassza ki a megfelelő szinkronizálási csoportot, majd azonosítsa azt a kiszolgálói végpontot, amelyhez szorosan nyomon szeretné követni az Azure-fájlmegosztás (Felhőbeli végpont) módosításait.
1. A felhő-rétegek szakaszban keresse meg az "Azure fájlmegosztás letöltése" témakört. Ekkor megjelenik a jelenleg kijelölt mód, és módosíthatja azt az Azure-fájlmegosztás változásainak alaposabb nyomon követéséhez, és proaktív módon felhívhatja őket a kiszolgálóra.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Egy olyan kép, amely az Azure fájlmegosztás letöltési viselkedését mutatja be egy jelenleg érvényben lévő kiszolgálói végponton, és egy gombot egy olyan menü megnyitásához, amely lehetővé teszi a módosítását.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

A kiszolgálói végpont tulajdonságait a PowerShellben módosíthatja a [set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) parancsmag használatával.

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) telepítésének áttelepítése Azure File Syncre
DFS-R központi telepítésének áttelepítése Azure File Syncre:

1. Hozzon létre egy szinkronizálási csoportot, amely a cserélni kívánt DFS-R topológiát jelöli.
2. Indítsa el az áttelepíteni kívánt DFS-R topológiában található összes adattal rendelkező kiszolgálót. Telepítse a Azure File Synct az adott kiszolgálón.
3. Regisztrálja a kiszolgálót, és hozzon létre egy kiszolgálói végpontot az áttelepítendő első kiszolgáló számára. Ne engedélyezze a Felhőbeli rétegek engedélyezését.
4. Lehetővé teszi az összes adatszinkronizálást az Azure-fájlmegosztás (Felhőbeli végpont) számára.
5. Telepítse és regisztrálja a Azure File Sync-ügynököt a többi DFS-R-kiszolgálón.
6. Tiltsa le a DFS-R-t. 
7. Hozzon létre egy kiszolgálói végpontot az egyes DFS-R-kiszolgálókon. Ne engedélyezze a Felhőbeli rétegek engedélyezését.
8. Győződjön meg arról, hogy a szinkronizálás befejeződik, és szükség szerint tesztelje a topológiát.
9. A DFS-R kivonása.
10. A felhő-rétegek mostantól a kívánt kiszolgálói végponton is engedélyezhetők.

További információ: [Azure file Sync együttműködés elosztott fájlrendszer (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs)szolgáltatással.

## <a name="next-steps"></a>Következő lépések
- [Azure File Sync kiszolgáló-végpont hozzáadása vagy eltávolítása](storage-sync-files-server-endpoint.md)
- [Kiszolgáló regisztrálása vagy törlése Azure File Sync](storage-sync-files-server-registration.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
