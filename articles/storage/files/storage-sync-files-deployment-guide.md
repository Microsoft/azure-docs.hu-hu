---
title: Az Azure File Sync üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure File Sync elejétől a végéig.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 12fd1b03e58d1c62157c6652ce96d8f0172dadb2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606109"
---
# <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése
Az Azure File Sync használatával fájlmegosztásainak a szervezet az Azure Files között, miközben gondoskodik a rugalmasságát, teljesítményét és kompatibilitását a helyszíni fájlkiszolgálók. Az Azure File Sync Windows Server az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. Helyileg, az adatok eléréséhez a Windows Serveren elérhető bármely protokollt használhatja, beleértve az SMB, NFS és FTPS. Tetszőleges számú gyorsítótárak világszerte igény szerint is rendelkezhet.

Javasoljuk, hogy olvasási [Azure Files üzembe helyezésének megtervezése](storage-files-planning.md) és [Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md) a jelen cikkben ismertetett lépések végrehajtása előtt.

## <a name="prerequisites"></a>Előfeltételek
* Egy Azure-fájlmegosztás ugyanabban a régióban, hogy szeretné-e az Azure File Sync üzembe helyezése. További információkért lásd:
    - [Régiónkénti elérhetőség](storage-sync-files-planning.md#region-availability) Azure File Sync.
    - [Fájlmegosztás létrehozása](storage-how-to-create-file-share.md) részletes leírása a fájlmegosztás létrehozása.
* Legalább egy támogatott példány szinkronizálása az Azure File Sync használatával Windows Server vagy Windows Server-fürt. A Windows Server támogatott verzióival kapcsolatos további információkért lásd: [együttműködés a Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Az Az PowerShell-modul vagy a PowerShell 5.1-es, vagy a PowerShell 6 + is használhatók. Előfordulhat, hogy az Az PowerShell modul használata az Azure File Sync bármely támogatott rendszerben, beleértve a nem Windows rendszerek, azonban a kiszolgáló regisztrációs parancsmagot kell mindig futtatni a Windows Server-példányon, is (ezt megteheti közvetlenül vagy a Powershellen keresztül regisztrálása a táveléréssel). A Windows Server 2012 R2, ellenőrizheti, hogy futtatja vagy újabb PowerShell 5.1. \* megnézzük az értékét a **PSVersion** tulajdonságát a **$PSVersionTable** objektum:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Ha a PSVersion érték kisebb, mint az 5.1-es. \*, mint az eset áll fenn a legtöbb újonnan telepített Windows Server 2012 R2, letöltése és telepítése által könnyedén frissíthet [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). A megfelelő csomag letöltése és telepítése a Windows Server 2012 R2 **Win8.1AndW2K12R2 KB-os\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6 + bármely támogatott rendszerrel használható, és keresztül letölthető a [GitHub-oldalon](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Ha azt tervezi, hogy a kiszolgáló regisztrációs felhasználói felületén ahelyett, hogy regisztrálja közvetlenül a PowerShell, PowerShell 5.1 kell használnia.

* Ha Ön úgy döntött, hogy a PowerShell 5.1 használni, győződjön meg arról, hogy legalább .NET 4.7.2 telepítve van. Tudjon meg többet [.NET-keretrendszer-verziókat és a függőségek](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) a rendszeren.

    > [!Important]  
    > Ha .NET 4.7.2+ telepíti a Windows Server Core-on, telepíteni kell a `quiet` és `norestart` jelzők vagy a telepítés sikertelen lesz. Például ha telepíti a .NET 4.8, a parancs lenne a következőhöz hasonló:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Az Az PowerShell modult, amely az itt leírt utasításokat követve telepíthető: [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > A Az.StorageSync modul most már automatikusan települ a Az PowerShell-modul telepítésekor.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>A Windows Server előkészítése az Azure File Sync használatára
Tiltsa le minden olyan kiszolgálón, az Azure File Sync, többek között az egyes kiszolgáló-csomópont egy feladatátvevő fürtben használni kívánt **az Internet Explorer fokozott biztonsági beállításai**. Ez azért szükséges, csak a kezdeti kiszolgálói regisztrációhoz. A kiszolgáló regisztrációja után újra engedélyezheti.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
> [!Note]  
> Ezt a lépést kihagyhatja, ha a Windows Server Core-on Azure File Sync telepíti.

1. Nyissa meg a Kiszolgálókezelőt.
2. Kattintson a **helyi kiszolgáló**:  
    ![A Server Manager felhasználói felületén bal oldalán a "helyi kiszolgáló"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. A **Tulajdonságok** lapon, válassza ki az **Internet Explorer fokozott biztonsági beállításai** hivatkozást.  
    ![A Server Manager felhasználói felületén a "Internet Explorer – fokozott biztonsági beállítások" panelen](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Az a **az Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen jelölje ki **ki** a **rendszergazdák** és **felhasználók**:  
    ![Az Internet Explorer fokozott biztonsági beállításai pop-ablak "Kikapcsolva" a kijelölt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Az Internet Explorer fokozott biztonsági beállítások letiltása, egy rendszergazda jogú PowerShell-munkamenetben hajtsa végre az alábbiakat:

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

---

## <a name="deploy-the-storage-sync-service"></a>A Társzinkronizálási szolgáltatás üzembe helyezése 
Az üzembe helyezés az Azure File Sync kezdődik elhelyezése egy **Társzinkronizálási szolgáltatás** erőforrás egy erőforrás-csoportba, a kijelölt előfizetésben. Azt javasoljuk, ahogyan few ezek kiépítése igény szerint. A kiszolgálók és az erőforrás közötti megbízhatósági kapcsolat fog létrehozni, és csak egy kiszolgálót is regisztrálható egy Társzinkronizálási szolgáltatást. Emiatt javasoljuk, hogy annyi társzinkronizálási szolgáltatás szerint kiszolgálócsoportok szét kell telepíteni. Ne feledje, hogy a kiszolgálók a társzinkronizálási szolgáltatás különböző nem tudják szinkronizálni a egymással.

> [!Note]
> A Storage Sync Service a hozzáférési engedélyek öröklődnek az előfizetés és erőforráscsoport rendelkezik lett üzembe helyezve. Azt javasoljuk, hogy alaposan ellenőrizze ki férhet hozzá. Írási hozzáféréssel rendelkező entitások megkezdheti a kiszolgálók regisztrálva új fájlkészleteket szinkronizálása ezt a tárolót a szinkronizálási szolgáltatás és flow az Azure storage, amely hozzáférhető annak azokat az adatokat.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
A Storage Sync Service telepítése, keresse fel a [az Azure portal](https://portal.azure.com/), kattintson a *erőforrás létrehozása* , és keressen rá az Azure File Sync. A keresési eredmények között, válassza ki a **Azure File Sync**, majd válassza ki **létrehozása** megnyitásához a **Társzinkronizálási üzembe helyezése** fülre.

A megnyíló panelen adja meg a következőket:

- **Név**: A Társzinkronizálási szolgáltatás egyedi neve (előfizetésenként).
- **Előfizetés**: Az előfizetés, amelyben szeretné létrehozni a Storage Sync Service. A szervezet konfigurációs stratégia, attól függően előfordulhat, hogy rendelkezik egy vagy több előfizetés hozzáférést. Azure-előfizetés az a lehető legegyszerűbb tároló, számoljuk el (például az Azure Files) minden felhőszolgáltatás.
- **Erőforráscsoport**: Egy erőforráscsoport olyan Azure-erőforrások, például a storage-fiók vagy a Storage Sync Service logikai csoportja. Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az Azure File Sync. (Javasoljuk erőforráscsoportok tárolókként logikailag a szervezetben, például a HR-erőforrások vagy egy adott projekthez tartozó erőforrások csoportosítása erőforrások elkülönítése.)
- **Hely**: A régió, amelyben meg szeretné az Azure File Sync üzembe helyezése. Ebben a listában csak a támogatott régiók érhetők el.

Ha elkészült, válassza ki a **létrehozás** a Storage Sync Service telepítése.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Cserélje le **< Az_Region >** , **< RG_Name >** , és **< my_storage_sync_service >** a saját értékeire, majd használja az alábbi parancsok létrehozása és üzembe helyezése egy Társzinkronizálási szolgáltatás:

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

---

## <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
Az Azure File Sync ügynök egy letölthető csomag, amely lehetővé teszi a Windows Server szinkronizálását Azure-fájlmegosztással. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Az ügynököt a letöltheti a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Ha a letöltés befejeződött, kattintson duplán az MSI-csomag az Azure File Sync ügynök telepítésének indításához.

> [!Important]  
> Szeretne használni az Azure File Sync a feladatátvevő fürttel, ha az Azure File Sync ügynök telepítve kell lennie a fürt minden csomópontján. A fürt egyes csomópontjaihoz regisztrálva kell az Azure File Sync használatával működnek.

Javasoljuk, hogy tegye a következőket:
- Hagyja bejelölve az alapértelmezett telepítési útvonaltól (C:\Program Files\Azure\StorageSyncAgent), a hibaelhárítás és a kiszolgáló karbantartás leegyszerűsítésében.
- Engedélyezheti a Microsoft Update, hogy az Azure File Sync mindig naprakészek. Minden frissítés az Azure File Sync ügynök, a szolgáltatás-frissítéseket és gyorsjavításokat, beleértve a fordulhat elő, a Microsoft Update webhelyről. Azt javasoljuk, hogy a legújabb frissítés telepítése az Azure File Sync. További információkért lásd: [Azure File Sync-frissítési szabályzat](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Az Azure File Sync ügynök telepítésének befejezését követően a kiszolgáló regisztrációs felhasználói felület automatikusan megnyílik. Mielőtt regisztrálná; rendelkeznie kell a Társzinkronizálási szolgáltatás Tekintse meg a következő szakaszban a Társzinkronizálási szolgáltatás létrehozása.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Futtassa a következő PowerShell-kódot az operációs rendszer az Azure File Sync ügynök megfelelő verzióját töltse le és telepítse azt a rendszer.

> [!Important]  
> Szeretne használni az Azure File Sync a feladatátvevő fürttel, ha az Azure File Sync ügynök telepítve kell lennie a fürt minden csomópontján. A fürt egyes csomópontjaihoz regisztrálva kell az Azure File Sync használatával működnek.

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

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows-kiszolgáló regisztrálása a Társzinkronizálási szolgáltatással
A Windows Server regisztrálásával a Társzinkronizálási szolgáltatásra megbízhatósági kapcsolatot hoz létre a kiszolgáló (vagy fürt) és a Társzinkronizálási szolgáltatás között. A kiszolgáló csak egy Társzinkronizálási szolgáltatásra regisztrálható és az ugyanahhoz a Társzinkronizálási szolgáltatáshoz társított kiszolgálókkal és Azure-fájlmegosztásokkal képes szinkronizálni.

> [!Note]
> Kiszolgáló regisztrálása Azure hitelesítő adatait használja a Storage Sync Service és a Windows Server, de ezt követően a kiszolgáló hoz létre, illetve a saját identitás, amely érvényes mindaddig, amíg a kiszolgáló marad regisztrált közötti megbízhatósági kapcsolat létrehozásához és a aktuális közös hozzáférésű Jogosultságkód (SAS-tároló) token érvényes. Egy új SAS-jogkivonat nem lehet kiadni a kiszolgálón a kiszolgáló regisztrációjának törlése, így az a kiszolgáló elérését az Azure-fájlmegosztások, bármely szinkronizálás leállítása eltávolítása után.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
A kiszolgáló regisztrációs felhasználói felület automatikusan megnyitja az Azure File Sync ügynök telepítése után. Ha nem, manuálisan is megnyithatja, a fájlok helyéről: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a kiszolgáló regisztrációs felhasználói felületén, válassza ki a **bejelentkezési** megkezdéséhez.

Miután bejelentkezett, a rendszer felszólítja a következő információkat:

![A kiszolgáló regisztrációs felhasználói felület képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: Az előfizetés, amely tartalmazza a Storage Sync Service (lásd: [a Storage Sync Service telepítése](#deploy-the-storage-sync-service)). 
- **Erőforráscsoport**: A Storage Sync Service tartalmazó erőforráscsoportot.
- **Társzinkronizálási szolgáltatás**: A Társzinkronizálási szolgáltatás, amellyel a regisztrálni kívánt neve.

Miután kiválasztotta a megfelelő információkat, válassza ki a **regisztrálása** a kiszolgáló regisztráció befejezéséhez. A regisztrációs folyamat részeként a rendszer újabb bejelentkezésre kéri fel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Szinkronizálási csoport és felhő végpont létrehozása
A szinkronizálási csoport határozza meg fájlok egy halmazára a szinkronizálási topológiát. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. A szinkronizálási csoportnak tartalmaznia kell egy felhőbeli végpontot, amely Azure-fájlmegosztást és egy vagy több kiszolgáló végpontot jelöl. Kiszolgálói végpont egy regisztrált kiszolgáló elérési útját jelöli. Egy kiszolgáló több szinkronizálási csoporttal is lehet kiszolgálói végpontot. Tetszőleges számú szinkronizálási csoportok megfelelően írja le a kívánt szinkronizálási topológia igény szerint hozhat létre.

Felhőbeli végpont az Azure-fájlmegosztások mutató. Összes kiszolgálói végpontot a felhőbeli végpont, így a felhőbeli végpont a hub szinkronizálódnak. Az Azure-fájlmegosztást a storage-fiókját és a Storage Sync Service ugyanabban a régióban kell elhelyezkednie. A teljes Azure-fájlmegosztásba való szinkronizálása megtörténik, egy kivétellel: Az NTFS-köteten található mappa rejtett "System Volume Information" hasonlítható speciális mappa lesznek üzembe helyezve. Ez a könyvtár neve ". SystemShareInformation". Nem szinkronizálódnak más végpontok szinkronizálási fontos metaadatokat tartalmaz. Ne használja, és törölje azt!

> [!Important]  
> Felhőbeli végpont vagy a szinkronizálási csoportban lévő kiszolgálói végpont módosításokat végezheti el, és a fájlok szinkronizálta már a szinkronizálási csoport végpontja. Ha módosítja a felhőbeli végponthoz (Azure-fájlmegosztás) közvetlenül, módosítások először az Azure File Sync módosítás észlelési feladat deríteniük. A módosítás észlelési feladat csak egyszer 24 óránként indul a felhőbeli végpont. További információkért lásd: [– gyakori kérdések az Azure Files](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
A szinkronizálási csoport létrehozása a [az Azure portal](https://portal.azure.com/)nyissa meg a Storage Sync Service, és válassza ki **+ szinkronizálási csoport**:

![Új szinkronizálási csoport létrehozása az Azure portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

A megnyíló panelen adja meg a következő információkat a szinkronizálási csoport létrehozásához egy felhőbeli végponttal:

- **Szinkronizálási csoport neve**: Neve a szinkronizálási csoport létrejön. A Társzinkronizálási szolgáltatáson belül egyedinek kell lennie a névnek, de lehet bármilyen Ön számára logikus név.
- **Előfizetés**: Az előfizetés, amelybe telepítette a Storage Sync Service a [a Storage Sync Service telepítése](#deploy-the-storage-sync-service).
- **Storage-fiók**: Ha **válassza ki a tárfiókot**, egy másik ablaktáblán jelenik meg, amelyben kiválaszthatja a tárfiók, amely rendelkezik az Azure-fájlmegosztást, amelyet szeretne szinkronizálni.
- **Azure-fájlmegosztás**: Az Azure-, amellyel szinkronizálni kívánt fájlmegosztás neve.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
A szinkronizálási csoport létrehozásához hajtsa végre az alábbi PowerShell-lel. Ne felejtse el kicserélni `<my-sync-group>` a szinkronizálási csoport kívánt nevét.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Ha a szinkronizálási csoport sikeresen létrejött, a felhőbeli végpont is létrehozhat. Ne felejtse el `<my-storage-account>` és `<my-file-share>` a várt értékkel.

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

---

## <a name="create-a-server-endpoint"></a>Kiszolgálói végpont létrehozása
A kiszolgálói végpont a regisztrált kiszolgálón egy konkrét helyet jelöl, például egy mappát egy kiszolgálói köteten. Kiszolgálói végpont egy regisztrált kiszolgáló (ahelyett, hogy egy csatlakoztatott megosztás) elérési útnak kell lennie, és a felhőbeli rétegezést használni, az elérési út egy nem rendszer-köteten kell lennie. Csatlakoztatott hálózati tárolóeszközök (NAS) nem támogatott.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Kiszolgálói végpont hozzáadásához nyissa meg a szinkronizálási újonnan létrehozott csoportot, majd **kiszolgálói végpont felvétele**.

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

A **Kiszolgálói végpont felvétele** panelen adja meg a következő információkat a kiszolgálói végpont létrehozásához:

- **Regisztrált kiszolgáló**: A kiszolgáló vagy fürt, ahol szeretné létrehozni a kiszolgálói végpont neve.
- **Elérési út**: A Windows Server elérési útját a szinkronizálási csoport részeként szinkronizálható.
- **Felhőbeli Rétegezés**: Egy kapcsoló engedélyezheti vagy tilthatja le a felhőbeli rétegezés. A felhőbeli rétegezés, ritkán használt vagy használt fájlokat a is az Azure Files lesz rétegzett.
- **Szabad terület a köteten**: A szabad terület a köteten, amelyen a kiszolgálói végpont is található lefoglalása mennyisége. Például ha egy köteten, amelyen egyetlen kiszolgálói végpont 50 % szabad terület a köteten van beállítva, körülbelül felét adatmennyiség többszintű az Azure Files. Függetlenül attól, hogy felhőbeli rétegezés engedélyezve van, az Azure-fájlmegosztást az adatok teljes másolata mindig szerepel a szinkronizálási csoport.

A kiszolgálói végpont hozzáadásához válassza **létrehozás**. Vannak, a fájlok mostantól szinkronban tartani az Azure-fájlmegosztást és a Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Hajtsa végre a következő PowerShell-parancsokat a kiszolgálói végpont létrehozása, és ne felejtse el `<your-server-endpoint-path>` és `<your-volume-free-space>` a kívánt értékekkel.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Bevezetés az Azure File Sync használatával
A javasolt lépéseket üzembe helyezni az Azure File Sync az első az állásidő nulla megőrzése a teljes fájl minőségben és hozzáférés-vezérlési lista (ACL) a következők:
 
1. A Storage Sync Service telepítése.
2. Hozzon létre egy szinkronizálási csoportot.
3. Az Azure File Sync-ügynök telepítése a kiszolgálón, a teljes adatkészlet.
4. Regisztrálja a kiszolgálót, és a kiszolgálói végpont létrehozása a megosztáson. 
5. Hajtsa végre a teljes feltöltése az Azure-fájlmegosztást (felhőbeli végpont) szinkronizálás segítségével.  
6. A kezdeti feltöltés befejeződése után telepítse az Azure File Sync ügynök összes többi kiszolgálóját.
7. A többi kiszolgáló minden egyes új fájlmegosztásokat hozhat létre.
8. Hozzon létre kiszolgálóvégpontok új fájlmegosztások felhőalapú rétegzési házirendet, ha szükséges. (Ebben a lépésben a kezdeti beállítás elérhető további tárterületet igényel.)
9. Lehetővé teszik az Azure File Sync ügynök ehhez a gyors visszaállítás, a teljes névtér a tényleges adatátviteli nélkül. A névtér teljes szinkronizálást követően a szinkronizálási motor betelik a helyi lemezterületet a felhő a kiszolgálói végpontot a rétegzési szabályzat alapján. 
10. Győződjön meg arról, szinkronizálása befejeződött, és tesztelje a topológia igény szerint. 
11. Átirányítja a felhasználókat és alkalmazásokat az új megosztásra.
12. Ismétlődő megosztásaitól, a kiszolgálókon szükség esetén törölheti.
 
Ha nem rendelkezik a kezdeti bevezetési extra tárterület, és szeretné csatlakoztatni a meglévő megosztások, előre, is feltöltheti az adatokat az Azure-fájlmegosztások. Ez a módszer javasolt, ha csak akkor fogadja el az állásidő és a feltétlenül garantálják az adatok nem változnak, a kiszolgálómegosztásokon a kezdeti bevezetési folyamat során. 
 
1. Győződjön meg arról, hogy bármely kiszolgálón lévő adatok a bevezetési folyamat során nem módosítható.
2. Előre vetőmag Azure-fájlmegosztások a kiszolgáló adatokkal eszközzel bármely adatok átvitele az SMB-n keresztül például Robocopy, a közvetlen SMB-példányt. Mivel az AzCopy nem tölt fel adatokat az SMB-n keresztül így előre beültetés esetében nem használható.
3. Hozzon létre az Azure File Sync-topológia a kívánt kiszolgálói végpontot a meglévő megosztások mutat.
4. Lehetővé teszik a szinkronizálás minden végponton egyeztetés folyamat befejezéséhez. 
5. Egyeztetés befejeződése után megnyithatja a megosztások változásait.
 
Felhívjuk a figyelmét arra, hogy jelenleg vonatkozik néhány korlátozás - előre beültetés megközelítés rendelkezik 
- Nem őrződik meg a fájlok teljes visszaadása. Például a fájlok ACL-EK és időbélyegek elvesznek.
- Adatok módosítása a előtt szinkronizálási topológia teljes körűen fel- és futó ütközések hatására a kiszolgáló végpontokon.  
- A felhőbeli végpont létrehozása után az Azure File Sync egy folyamat észleli a fájlokat a felhőben, a kezdeti szinkronizálás indítása előtt futtatja. Ez a folyamat befejezéséhez szükséges idő például a hálózati sebesség, a rendelkezésre álló sávszélességet és a fájlok és mappák száma különböző tényezőktől függően változik. Az előzetes kiadásban az Hozzávetőleges becslés az észlelési folyamat körülbelül 10 fájlokat/mp-ben található futtatja.  Ezért még akkor is, ha előre beültetés gyorsan lefut, beolvasni egy teljes mértékben működő rendszerben teljes időtartama sokkal hosszabb Ha adatokat nem lehet előzetesen a felhőben.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Áttelepítheti egy elosztott fájlrendszer replikációs szolgáltatása (DFS-R) környezetet az Azure File Sync
A DFS-R telepítési áttelepítése Azure File Sync:

1. Lecseréli a DFS-R-topológia ábrázolására szinkronizálási csoport létrehozása.
2. Indítsa el a kiszolgálón, amelyen az adatok teljes készlete a DFS-R topológia áttelepíteni. Telepítse az Azure File Sync az adott kiszolgálón.
3. Regisztrálja a kiszolgálót, és az első kiszolgáló kell áttelepíteni a kiszolgálói végpont létrehozásához. Ne engedélyezze a felhőbeli rétegezés.
4. Lehetővé teszik az összes az adatszinkronizálás az Azure-megosztás (felhőbeli végpont).
5. Telepíti és regisztrálja az Azure File Sync ügynök minden fennmaradó DFS-R-kiszolgálót.
6. Tiltsa le a DFS-R. 
7. A DFS-R Server-kiszolgálók mindegyike a kiszolgálói végpont létrehozásához. Ne engedélyezze a felhőbeli rétegezés.
8. Győződjön meg arról, szinkronizálása befejeződött, és tesztelje a topológia igény szerint.
9. A DFS-r kivonása
10. A felhő rétegezési május most már igény szerint bármely kiszolgálói végpont engedélyezhető.

További információkért lásd: [Azure File Sync együttműködési az elosztott fájlrendszer (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>További lépések
- [És egy Azure File Sync kiszolgálói végpont törlése](storage-sync-files-server-endpoint.md)
- [Regisztráljon vagy az Azure File Sync-kiszolgáló regisztrációjának törlése](storage-sync-files-server-registration.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
