---
title: Azure-fájlmegosztás használata Windowson | Microsoft Docs
description: Az Azure-fájlmegosztások használata Windowson és Windows Serveren.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 62b3445ba841a87f04dbe8c867411814b849be07
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682442"
---
# <a name="use-an-azure-file-share-with-windows"></a>Azure-fájlmegosztás használata Windowson
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások zökkenőmentesen használhatóak Windowson és Windows Serveren. Ebben a cikkben az Azure-fájlmegosztások Windowson és Windows Serveren való használatának szempontjairól olvashat.

Ha egy Azure-fájlmegosztást az üzemeltető Azure-régión kívül kíván alkalmazni, például a helyszínen vagy más Azure-régióban, az operációs rendszernek támogatnia kell az SMB 3.0-s verziót. 

Azure-fájlmegosztásokat az Azure-beli virtuális gépeken vagy helyszínen futó Windows-telepítéseken használhat. A következő táblázatban látható, hogy melyik operációsrendszer-verzió melyik környezetekben támogatja a fájlmegosztások elérését:

| Windows-verzió        | SMB-verzió | Azure-beli virtuális gépeken csatlakoztatható | Helyszíni üzembe helyezhető |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Igen | Igen |
| Windows 10<sup>1</sup> | SMB 3.0 | Igen | Igen |
| Windows Server féléves csatorna<sup>2</sup> | SMB 3.0 | Igen | Igen |
| Windows Server 2016 | SMB 3.0 | Igen | Igen |
| Windows 8.1 | SMB 3.0 | Igen | Igen |
| Windows Server 2012 R2 | SMB 3.0 | Igen | Igen |
| Windows Server 2012 | SMB 3.0 | Igen | Igen |
| Windows 7<sup>3</sup> | SMB 2.1 | Igen | Nem |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Igen | Nem |

<sup>1</sup> Windows 10, 1507, 1607, 1709, 1803, 1809, 1903 és 1909.  
<sup>2</sup> Windows Server, 1809, 1903 és 1909 verzió.  
<sup>3</sup> A Microsoft a Windows 7 és a Windows Server 2008 R2 rendszerhez készült rendszeres támogatás befejeződött. A biztonsági frissítések további támogatását csak a [bővített biztonsági frissítés (EUME) programon](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)keresztül lehet megvásárolni. Erősen ajánlott áttelepíteni ezeket az operációs rendszereket.

> [!Note]  
> Javasoljuk, hogy mindig a Windows-verziójához legutóbb kiadott frissítést használja.

## <a name="prerequisites"></a>Előfeltételek 
* **Storage-fiók neve**: egy Azure-fájlmegosztás csatlakoztatásához szüksége lesz a Storage-fiók nevére.

* **Storage-fiók kulcsa**: az Azure-fájlmegosztás csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárolási kulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, hogy a 445-ös port nyitva van**: Az SMB protokollhoz szükséges, hogy a 445-ös TCP port nyitva legyen; a csatlakozás nem sikerül, ha a 445-ös port blokkolva van. Ellenőrizze, hogy a tűzfal nem blokkolja-e a 445-ös portot a `Test-NetConnection` parancsmaggal. Az [445-es blokkolt port megkerülő megoldásának különböző módjairól itt](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)olvashat.

    A következő PowerShell-kód feltételezi, hogy telepítve van a Azure PowerShell modul, további információért lásd: [Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps) . Ne felejtse el kicserélni a `<your-storage-account-name>` és a `<your-resource-group-name>` elemet a tárfiók vonatkozó neveivel.

    ```powershell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Sikeres csatlakozás esetén a következő kimenetet kell látnia:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > A fenti parancs visszaadja a tárfiók aktuális IP-címét. Nem garantált, hogy ez az IP-cím ugyanaz marad, és bármikor megváltozhat. Ne rögzítse szoftveresen az IP-címet egy szkriptben vagy egy tűzfal-konfigurációban sem. 

## <a name="using-an-azure-file-share-with-windows"></a>Az Azure-fájlmegosztások használata Windowson
Az Azure-fájlmegosztások Windowson való használatához csatlakoztatnia kell azokat, azaz hozzájuk kell rendelnie egy meghajtó betűjelét vagy egy csatlakoztatási pont elérési útját, vagy pedig az [UNC-útvonalukon](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx) keresztül érheti el azokat. 

Ez a cikk a Storage-fiók kulcsát használja a fájlmegosztás eléréséhez. A Storage-fiók kulcsa a Storage-fiókhoz tartozó rendszergazdai kulcs, beleértve a megosztott fájlmegosztás összes fájljának és mappájának rendszergazdai engedélyeit, valamint a Storage-fiókban található összes fájlmegosztást és egyéb tárolási erőforrást (Blobok, várólisták, táblák stb.). Ha ez nem elegendő a munkaterheléshez, [Azure file Sync](storage-sync-files-planning.md) lehet használni, vagy az [SMB protokollon alapuló identitás-alapú hitelesítést](storage-files-active-directory-overview.md)is használhat.

Az SMB-fájlmegosztást váró üzletági (LOB) alkalmazások Azure-ba való áthelyezése esetén gyakori megoldás az Azure-fájlmegosztások használata a dedikált Windows-fájlkiszolgálók Azure-beli virtuális gépeken történő futtatása helyett. Az üzletági alkalmazások egy Azure-fájlmegosztás használatára való sikeres migrálása érdekében fontos figyelembe venni, hogy számos üzletági alkalmazás, egy korlátozott rendszerengedélyekkel rendelkező dedikált szolgáltatásfiók környezetében fut a virtuális gép rendszergazdai fiókja helyett. Ezért győződjön meg róla, hogy az Azure-fájlmegosztáshoz szükséges hitelesítő adatokat a szolgáltatásfiók helyett a rendszergazdai fiókon keresztül csatlakoztatja/menti.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Az Azure-fájlmegosztások hitelesítő adatainak megőrzése Windowson  
A [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) segédprogram lehetővé teszi a tárfiókok hitelesítő adatainak tárolását Windowson. Ez azt jelenti, hogy nem szükséges megadnia a hitelesítő adatokat az Azure-fájlmegosztások csatlakoztatáskor vagy UNC-útvonalon keresztül történő elérésekor. A tárfiók hitelesítő adatainak mentéséhez futtassa a következő PowerShell-parancsokat, és cserélje le értelemszerűen a `<your-storage-account-name>` és `<your-resource-group-name>` elemeket.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

A list paraméter segítségével ellenőrizheti, hogy a cmdkey segédprogram tárolta-e a tárfiók hitelesítő adatait:

```powershell
cmdkey /list
```

Ha a rendszer sikeresen tárolta az Azure-fájlmegosztáshoz tartozó hitelesítő adatokat, a következő kimenetnek kell megjelennie (előfordulhat, hogy további kulcsok is tárolva vannak a listában):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Most már további hitelesítő adatok megadása nélkül is tudnia kell csatlakozni vagy hozzáférni a megosztáshoz.

#### <a name="advanced-cmdkey-scenarios"></a>Speciális cmdkey-forgatókönyvek
Két további esetet kell figyelembe venni a cmdkey segédprogrammal kapcsolatban: a hitelesítő adatok tárolása egy másik felhasználó számára a gépen, például egy szolgáltatásfiók esetében, és a hitelesítő adatok tárolása távoli gépen PowerShell távoli eljáráshívással.

Egy másik felhasználó hitelesítő adatainak tárolása a gépen egyszerű: Ha bejelentkezett a fiókjába, egyszerűen hajtsa végre a következő PowerShell-parancsot:

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Ekkor megnyílik egy új PowerShell-ablak a szolgáltatásfiók (vagy felhasználói fiók) felhasználói környezetében. Ezután használhatja a cmdkey segédprogramot a [fenti](#persisting-azure-file-share-credentials-in-windows) leírás szerint.

A hitelesítő adatok tárolása egy távoli gépen a PowerShell távoli eljáráshívás használatával azonban nem lehetséges, mivel a cmdkey nem engedélyezi a hozzáférést a hitelesítőadat-tárolóhoz még hozzáadás esetére sem, amikor a felhasználó PowerShell távoli eljáráshíváson keresztül van bejelentkezve. Javasoljuk a [Távoli asztallal](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows) történő bejelentkezést a gépre.

### <a name="mount-the-azure-file-share-with-powershell"></a>Az Azure-fájlmegosztás csatlakoztatása a PowerShell-lel
Futtassa az alábbi parancsokat egy normál (nem emelt szintű) PowerShell-munkamenetből az Azure-fájlmegosztás csatlakoztatásához. Ne felejtse el kicserélni a `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` és `<desired-drive-letter>` elemeket a vonatkozó információval.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> A `-Persist` opció a `New-PSDrive` parancsmagban csak akkor teszi lehetővé a fájlmegosztás újracsatlakoztatását a rendszerindításkor, ha a hitelesítő adatok mentve vannak. A hitelesítő adatokat a [korábbiakban leírtak](#persisting-azure-file-share-credentials-in-windows) szerint tudja menteni a cmdkey segédprogram használatával. 

A következő PowerShell-parancsmag használatával szükség esetén leválasztható az Azure-fájlmegosztás.

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Az Azure-fájlmegosztás csatlakoztatása a Fájlkezelővel
> [!Note]  
> Vegye figyelembe, hogy a következő példák a Windows 10-re vonatkoznak, és a régebbi kiadásokban eltérhetnek. 

1. Nyissa meg a Fájlkezelőt. Ezt a Start menüből vagy a Win+E billentyűkombináció lenyomásával teheti meg.

1. Keresse meg az **Ez a gép** elemet az ablak bal oldalán. Ez módosítja a szalagon elérhető menüket. A Számítógép menüben válassza a **Hálózati meghajtó csatlakoztatása** elemet.
    
    ![A Hálózati meghajtó csatlakoztatása legördülő menü képernyőképe](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Válassza ki a meghajtóbetűjelet, és adja meg az UNC elérési utat, az UNC elérési út formátuma `<storageAccountName>.file.core.windows.net/<fileShareName>` . Példa: `anexampleaccountname.file.core.windows.net/example-share-name`.
    
    ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Felhasználónévként használja a tárfiók `AZURE\` előtaggal kiegészített nevét, jelszóként pedig egy tárfiókkulcsot.
    
    ![A hálózati hitelesítő adatok párbeszédpanelének képernyőképe](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Használja az Azure-fájlmegosztást igény szerint.
    
    ![Az Azure-fájlmegosztás most már csatlakoztatva van](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Amikor készen áll az Azure-fájlmegosztás leválasztására, kattintson a jobb gombbal a megosztás bejegyzésére a Fájlkezelő **Hálózati helyek** területén, és válassza a **Leválasztás** parancsot.

### <a name="accessing-share-snapshots-from-windows"></a>Megosztási pillanatképek elérése a Windowsban
A valamely szkript vagy szolgáltatás, például az Azure Backup használatával manuálisan vagy automatikusan létrehozott megosztási pillanatképek segítségével megtekintheti a megosztások, a könyvtárak, illetve a fájlmegosztásokban vagy a Windows rendszeren lévő adott fájlok korábbi verzióit. A [Azure Portal](storage-how-to-use-files-portal.md), [Azure PowerShell](storage-how-to-use-files-powershell.md)és az [Azure parancssori](storage-how-to-use-files-cli.md)felületről is készíthet megosztási pillanatképet.

#### <a name="list-previous-versions"></a>Előző verziók listázása
Tallózással keresse meg a visszaállítani kívánt elemet vagy szülőelemet. Duplán rákattintva lépjen a kívánt könyvtárra. Kattintson a jobb gombbal, majd válassza a menü **Tulajdonságok** elemét.

![Kijelölt könyvtár helyi menüje](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához. A lista betöltése a hálózat sebességétől és a könyvtárban lévő megosztási pillanatképek számától függően néhány másodpercet is igénybe vehet.

![Előző verziók lap](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

A **Megnyitás** elemre kattintva nyithatja meg az egyes pillanatképeket. 

![Megnyitott pillanatkép](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról
A **Visszaállítás** elemre kattintva visszaállíthatja a teljes könyvtár tartalmát az eredeti helyre a megosztási pillanatkép készítésének időpontjában érvényes állapotra.

 ![Visszaállítás gomb a figyelmeztető üzenetben](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>A Windows/Windows Server védelme
Az Azure-fájlmegosztás Windowson való csatlakoztatásához a 445-ös portnak elérhetőnek kell lennie. Számos szervezet blokkolja a 445-ös portot az SMB 1 eredendő biztonsági kockázatai miatt. Az SMB-1, más néven CIFS (Common Internet File System), egy korábbi fájlrendszerprotokoll, amelyet a Windows és a Windows Server tartalmaz. Az SMB-1 egy elavult, nem hatékony és legfőképpen nem biztonságos protokoll. A jó hír az, hogy az Azure Files nem támogatja az SMB 1-et, és a Windows és Windows Server összes támogatott verziója lehetővé teszi az SMB 1 eltávolítását vagy letiltását. Mindig [erősen ajánlott](https://aka.ms/stopusingsmb1) az SMB 1 ügyfél és kiszolgáló eltávolítása vagy letiltása a Windowsban az Azure-fájlmegosztások használata előtt.

A következő táblázat részletes leírást ad az SMB 1 állapotáról minden Windows-verzión:

| Windows-verzió                           | SMB 1 alapértelmezett állapota | Letiltási/eltávolítási módszer       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Letiltva             | Eltávolítás Windows-funkcióval |
| Windows Server, 1709+ verziók            | Letiltva             | Eltávolítás Windows-funkcióval |
| Windows 10, 1709+ verziók                | Letiltva             | Eltávolítás Windows-funkcióval |
| Windows Server 2016                       | Engedélyezve              | Eltávolítás Windows-funkcióval |
| Windows 10, 1507-es, 1607-es és 1703-as verziók | Engedélyezve              | Eltávolítás Windows-funkcióval |
| Windows Server 2012 R2                    | Engedélyezve              | Eltávolítás Windows-funkcióval | 
| Windows 8.1                               | Engedélyezve              | Eltávolítás Windows-funkcióval | 
| Windows Server 2012                       | Engedélyezve              | Letiltás a beállításjegyzékkel       | 
| Windows Server 2008 R2                    | Engedélyezve              | Letiltás a beállításjegyzékkel       |
| Windows 7                                 | Engedélyezve              | Letiltás a beállításjegyzékkel       | 

### <a name="auditing-smb-1-usage"></a>Az SMB 1 használatának naplózása
> A Windows Server 2019, a Windows Server féléves csatornára (1709 és 1803 verziók), a Windows Server 2016, a Windows 10 (Versions 1507, 1607, 1703, 1709 és 1803), a Windows Server 2012 R2 és a Windows 8,1 alkalmazásra vonatkozik

Mielőtt eltávolítja az SMB 1-et a környezetből, naplózhatja az SMB 1 használatát, hogy nyomon tudja követni, károsodik-e bármelyik ügyfél a változtatás miatt. Ha kérelem merül fel az SMB 1-gyel rendelkező SMB-megosztásokkal kapcsolatban, a rendszer egy naplózási eseményt rögzít az eseménynaplóba az `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` útvonalon. 

> [!Note]  
> A Windows Server 2012 R2-es és a Windows 8.1-es verzión való naplózási támogatás engedélyezéséhez telepítse legalább a [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) frissítést.

A naplózás engedélyezéséhez hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Az SMB 1 eltávolítása a Windows Serverről
> A Windows Server 2019, a Windows Server féléves csatornára (1709 és 1803 verziók), a Windows Server 2016, a Windows Server 2012 R2 verzióra vonatkozik.

Az SMB 1 Windows Server-példányról történő eltávolításához hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Az eltávolítási folyamat befejezéséhez indítsa újra a kiszolgálót. 

> [!Note]  
> A Windows 10 és a Windows Server 1709-es verziótól kezdődően az SMB 1 alapértelmezés szerint nincs telepítve, és az SMB 1 ügyfél és az SMB 1 kiszolgáló különálló Windows-funkciókkal rendelkezik. Azt javasoljuk, hogy ne telepítse az SMB 1 kiszolgálót (`FS-SMB1-SERVER`) és az SMB 1 ügyfelet (`FS-SMB1-CLIENT`).

### <a name="removing-smb-1-from-windows-client"></a>Az SMB 1 eltávolítása a Windows-ügyfélről
> Érintett kiadások: Windows 10 (1507-es, 1607-es, 1703-as, 1709-es és 1803-as verziók) és Windows 8.1

Az SMB 1 Windows-ügyfélről történő eltávolításához hajtsa végre a következő parancsmagot egy emelt szintű PowerShell-munkamenetből:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Az eltávolítási folyamat befejezéséhez indítsa újra a számítógépet.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Az SMB 1 letiltása a Windows/Windows Server korábbi verzióin
> Érintett kiadások: Windows Server 2012, Windows Server 2008 R2 és Windows 7

Az SMB 1 nem távolítható el teljesen a Windows/Windows Server korábbi verzióin, a beállításjegyzék segítségével azonban letiltható. Az SMB-1 letiltásához hozzon létre egy új, `DWORD` típusú `SMB1` beállításkulcsot `0` értékkel a `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` útvonalon.

Ezt a következő PowerShell-parancsmaggal egyszerűen megteheti:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Miután létrehozta a beállításkulcsot, indítsa újra a kiszolgálót az SMB 1 letiltásához.

### <a name="smb-resources"></a>SMB-erőforrások
- [Az SMB 1 használatának leállítása](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 termék adategyeztető központ](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Az SMB 1 felfedezése a környezetben DSCEA használatával](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [Az SMB 1 letiltása csoportházirend használatával](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Gyakori kérdések](../storage-files-faq.md)
- [Hibaelhárítás a Windows rendszerben](storage-troubleshoot-windows-file-connection-problems.md)      
