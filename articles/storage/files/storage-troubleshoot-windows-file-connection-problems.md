---
title: Azure Files-problémák hibaelhárítása Windowson
description: Hibaelhárítás Azure Files problémák a Windows rendszerben. A Windows-ügyfelekről való csatlakozáskor Azure Files kapcsolódó gyakori problémák és a lehetséges megoldások megtekintése. Csak SMB-megosztások esetén
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 242c0819e916f3ea7912d4d57b7d3e338152e4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878510"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>A Windows (SMB) Azure Files problémáinak elhárítása

Ez a cikk a Windows-ügyfelekről való csatlakozáskor Microsoft Azure fájlokkal kapcsolatos gyakori problémákat sorolja fel. Emellett a problémák lehetséges okait és megoldásait is tartalmazza. A cikkben található hibaelhárítási lépések mellett a [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) is használható annak biztosítására, hogy a Windows-ügyfél környezete megfelelő előfeltételekkel rendelkezik. A AzFileDiagnostics automatizálja a jelen cikkben említett legtöbb tünet észlelését, és segít az optimális teljesítmény érdekében a környezet beállításában.

> [!IMPORTANT]
> A cikk tartalma csak az SMB-megosztásokra vonatkozik. Az NFS-megosztásokkal kapcsolatos részletekért lásd: az [Azure NFS-fájlmegosztás hibáinak megoldása](storage-troubleshooting-files-nfs.md).

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>5. hiba az Azure-fájlmegosztás csatlakoztatásakor

Amikor megpróbál csatlakoztatni egy fájlmegosztást, a következő hibaüzenet jelenhet meg:

- Az 5-ös rendszerhiba fordult elő. A hozzáférés megtagadva.

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: titkosítatlan kommunikációs csatorna

Biztonsági okokból az Azure-fájlmegosztásokhoz való kapcsolódás le van tiltva, ha a kommunikációs csatorna nincsen titkosítva, vagy ha a csatlakozási kísérlet nem ugyanabból az adatközpontból történik, ahol az Azure-fájlmegosztások találhatók. Az ugyanazon adatközponton belüli titkosítatlan kapcsolatokat akkor is blokkolhatja a rendszer, ha a tárfiókban engedélyezve van a [Biztonságos átvitelre van szükség](../common/storage-require-secure-transfer.md) beállítás. Csak akkor biztosítható titkosított kommunikációs csatorna, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítást.

A Windows 8, a Windows Server 2012 és újabb verzióik olyan kéréseket egyeztetnek, amelyek magukban foglalják a titkosítást támogató SMB 3.0-t.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Kapcsolódjon egy olyan ügyfélhez, amely támogatja az SMB-titkosítást (Windows 8, Windows Server 2012 vagy újabb), vagy kapcsolódjon egy olyan virtuális gépről, amely az Azure-fájlmegosztás által használt Azure Storage-fiókkal azonos adatközpontban található.
2. Győződjön meg arról, hogy a [biztonságos átvitel szükséges](../common/storage-require-secure-transfer.md) beállítás le van tiltva a Storage-fiókban, ha az ügyfél nem támogatja az SMB-titkosítást.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: a virtuális hálózat vagy a tűzfalszabályok engedélyezve vannak a Storage-fiókon. 

Ha virtuális hálózati (VNET) és tűzfalszabályok vannak konfigurálva a tárfiókhoz, a hálózati forgalom számára blokkolva lesz a hozzáférés, kivéve, ha az ügyfél IP-címe vagy a virtuális hálózat hozzáférést kapott.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>3. ok: a megosztási szintű engedélyek helytelenek az identitás-alapú hitelesítés használatakor

Ha a felhasználók Active Directory (AD) vagy Azure Active Directory Domain Services (Azure AD DS) hitelesítés használatával érik el az Azure-fájlmegosztást, akkor a fájlmegosztás hozzáférése sikertelen lesz, ha a megosztási szintű engedélyek helytelenek. 

### <a name="solution-for-cause-3"></a>3. ok megoldás

Ellenőrizze, hogy az engedélyek helyesen vannak-e konfigurálva:

- **Active Directory (ad)** lásd: [megosztási szintű engedélyek társítása identitáshoz](./storage-files-identity-ad-ds-assign-permissions.md).

    A megosztási szintű engedélyek hozzárendelései olyan csoportok és felhasználók számára támogatottak, amelyek a Active Directory (AD) szolgáltatásból Azure Active Directory (Azure AD) Azure AD Connect használatával szinkronizálhatók.  Győződjön meg arról, hogy a csoportoknak és a megosztott megosztási szintű engedélyekkel rendelkező felhasználók nem támogatják a "csak felhőalapú" csoportokat.
- **Azure Active Directory Domain Services (Azure AD DS)** lásd: [hozzáférési engedélyek kiosztása identitáshoz](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53 hiba, hiba 67 vagy hiba 87 az Azure-fájlmegosztás csatlakoztatásakor vagy leválasztásakor

Amikor a helyi vagy egy másik adatközpontból próbál meg fájlmegosztást csatlakoztatni, a következő hibaüzenetek jelenhetnek meg:

- Az 53-as rendszerhiba fordult elő. A hálózati elérési út nem található.
- A 67-es rendszerhiba fordult elő. A hálózatnév nem található.
- A 87-es rendszerhiba fordult elő. A paraméter helytelen.

### <a name="cause-1-port-445-is-blocked"></a>1. ok: a 445-es port blokkolva van

Rendszerhiba 53 vagy a 67 rendszerhiba akkor fordulhat elő, ha a port 445 Azure Files adatközpontba való kimenő kommunikációja le van tiltva. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Annak vizsgálatához, hogy a tűzfal vagy az INTERNETSZOLGÁLTATÓ blokkolja-e a 445-es portot, használja a [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) eszközt vagy a `Test-NetConnection` parancsmagot. 

A parancsmag használatához `Test-NetConnection` telepíteni kell a Azure PowerShell modult. További információért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps) . Ne felejtse el kicserélni a `<your-storage-account-name>` és a `<your-resource-group-name>` elemet a tárfiók vonatkozó neveivel.

   
```azurepowershell
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
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> A fenti parancs visszaadja a tárfiók aktuális IP-címét. Nem garantált, hogy ez az IP-cím ugyanaz marad, és bármikor megváltozhat. Ne rögzítse szoftveresen az IP-címet egy szkriptben vagy egy tűzfal-konfigurációban sem.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

#### <a name="solution-1---use-azure-file-sync"></a>1. megoldás – az Azure File Sync használata
Azure File Sync átalakíthatja a helyszíni Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Az Azure File Sync a 443-as porton keresztül működik, ezért megkerülő megoldásként használható az Azure Files eléréséhez a letiltott 445-ös porttal rendelkező ügyfelekről. [Útmutató a Azure file Sync telepítéséhez](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>2. megoldás – VPN használata
A VPN az adott Storage-fiókhoz való beállításával a forgalom egy biztonságos alagúton halad át, szemben az interneten keresztül. Ha Windowsból szeretné elérni az Azure Filest, kövesse [a VPN beállításához kapcsolódó utasításokat](storage-files-configure-p2s-vpn-windows.md).

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3. megoldás – A 445-ös port blokkolásának feloldása az internetszolgáltató vagy a rendszergazda segítségével
Az IT-részleggel vagy az INTERNETSZOLGÁLTATÓval együttműködve nyissa meg az 445-es portot az [Azure IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4. megoldás – REST API-alapú eszközök, például a Storage Explorer és a Powershell használata
A Azure Files az SMB mellett a REST-t is támogatja. A REST-hozzáférés a 443-as (szabványos TCP-) porton keresztül működik. Számos olyan REST API-val írt eszköz létezik, amely gazdag felhasználói felületi élmény megvalósítását teszi lehetővé. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) az egyikük. [Töltse le és telepítse a Storage Explorert](https://azure.microsoft.com/features/storage-explorer/), és csatlakozzon az Azure Files-támogatással rendelkező fájlmegosztáshoz. Használhatja a [PowerShellt](./storage-how-to-use-files-powershell.md) is, amely a felhasználó REST API is.

### <a name="cause-2-ntlmv1-is-enabled"></a>2. ok: a NTLMv1 engedélyezve van

Rendszerhiba 53 vagy a 87 rendszerhiba akkor fordulhat elő, ha a NTLMv1-kommunikáció engedélyezve van az ügyfélen. Az Azure Files kizárólag az NTLMv2-hitelesítést támogatja. Az NTLMv1 engedélyezése csökkenti az ügyfél biztonságát. Ezért kerül sor az Azure Files kommunikációjának blokkolására. 

Annak megállapításához, hogy ez okozza-e a hibát, ellenőrizze, hogy a következő beállításkulcs értéke nem 3 kisebb értékre van-e állítva:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információért tekintse meg a TechNet [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) témakörét.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza az **LmCompatibilityLevel** értékét az alapértelmezett 3-ra a következő beállításjegyzékbeli alkulcsban:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>1816-es hiba – nem áll rendelkezésre elegendő kvóta a parancs feldolgozásához

### <a name="cause"></a>Ok

1816-es hiba történik, amikor eléri az Azure-fájlmegosztás fájljának vagy könyvtárának felső határát. További információ: [Azure Files – skálázási célok](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Megoldás

Csökkentse az egyidejű megnyitott fogópontok számát néhány leíró bezárásával, majd próbálkozzon újra. További információ: [Microsoft Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlista](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Egy fájlmegosztás, könyvtár vagy fájl nyitott leíróinak megtekintéséhez használja a [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmagot.  

Egy fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak bezárásához használja a [AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-parancsmagot.

> [!Note]  
> A Get-AzStorageFileHandle és Close-AzStorageFileHandle parancsmagok az az PowerShell-modul 2,4-es vagy újabb verziójában szerepelnek. A legújabb az PowerShell modul telepítéséhez lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Hiba: "nincs hozzáférés", amikor megpróbál hozzáférni vagy törölni egy Azure-fájlmegosztást  
Amikor megpróbál hozzáférni vagy törölni egy Azure-fájlmegosztást a portálon, a következő hibaüzenetet kaphatja:

Nincs hozzáférés  
Hibakód: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>1. ok: a virtuális hálózat vagy a tűzfalszabályok engedélyezve vannak a Storage-fiókon.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>2. ok: a felhasználói fióknak nincs hozzáférése a Storage-fiókhoz

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Keresse meg azt a Storage-fiókot, ahol az Azure-fájlmegosztás található, kattintson a **hozzáférés-vezérlés (iam)** elemre, és ellenőrizze, hogy a felhasználói fiókja rendelkezik-e hozzáféréssel a Storage-fiókhoz. További információt a [Storage-fiók biztonságossá tétele az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../blobs/security-recommendations.md#data-protection)című témakörben talál.

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Egy fájl vagy könyvtár nem módosítható, nem helyezhető át, nem nevezhető át vagy nem törölhető.
A fájlmegosztás egyik fő célja, hogy több felhasználó és alkalmazás is dolgozhat egyidejűleg a megosztásban található fájlokkal és címtárakkal. Az interakció támogatásához a fájlmegosztás számos módszert biztosít a fájlok és könyvtárak hozzáférésének közvetítésére.

Amikor egy csatlakoztatott Azure-fájlmegosztás SMB-kapcsolaton keresztül nyit meg egy fájlt, az alkalmazás/operációs rendszer egy fájlleíró-fájlt kér le, amely a fájlra mutató hivatkozás. Többek között az alkalmazás egy fájlmegosztási módot is megad, amikor egy fájlkezelőt kér, amely meghatározza, hogy a Azure Files által érvényesített fájlhoz való hozzáférése milyen mértékben van kikényszerítve: 

- `None`: kizárólagos hozzáférése van. 
- `Read`: mások is elolvashatják a fájlt, amíg nyitva van.
- `Write`: mások a fájl megnyitásakor írhatnak a fájlba. 
- `ReadWrite`: a `Read` és a `Write` megosztási mód kombinációja.
- `Delete`: mások is törölhetik a fájlt, amíg nyitva van. 

Bár az állapot nélküli protokoll, a kiosztott protokoll nem rendelkezik a fájlleíró fogalmával, hasonló mechanizmust biztosít a parancsfájlok, alkalmazások vagy szolgáltatások által használt fájlokhoz és mappákhoz való hozzáférés biztosításához: a fájlok bérletét. Egy fájl bérletének megadásakor a rendszer egyenértékűként kezeli azt egy fájlmegosztási móddal `None` . 

Bár a fájlkezelők és a bérletek fontos célt szolgálnak, időnként előfordulhat, hogy a fájlok és a bérletek árvaok. Ha ez történik, ez problémákat okozhat a fájlok módosításában vagy törlésében. A következőhöz hasonló hibaüzenetek jelenhetnek meg:

- A folyamat nem éri el a fájlt, mert egy másik folyamat használja.
- A művelet nem hajtható végre, mert a fájl egy másik programban van megnyitva.
- A dokumentum zárolva van egy másik felhasználó általi szerkesztésre.
- Egy SMB-ügyfél törlésre jelölte meg a megadott erőforrást.

A probléma megoldása attól függ, hogy ezt egy árva fájlkezelő vagy bérlet okozza-e. 

### <a name="cause-1"></a>1\. ok
A fájlleíró megakadályozza, hogy egy fájl vagy könyvtár módosítható vagy törölve legyen. A [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmag használatával megtekintheti a nyitott leírókat. 

Ha az összes SMB-ügyfél lezárta a megnyitott kezelőket egy fájlra vagy könyvtárra, és a probléma továbbra is fennáll, akkor kényszerítheti a fájlleíró bezárását.

### <a name="solution-1"></a>1\. megoldás
A fájlkezelő bezárásának kényszerítéséhez használja a [Close-AzStorageFileHandle PowerShell-](/powershell/module/az.storage/close-azstoragefilehandle) parancsmagot. 

> [!Note]  
> A Get-AzStorageFileHandle és Close-AzStorageFileHandle parancsmagok az az PowerShell-modul 2,4-es vagy újabb verziójában szerepelnek. A legújabb az PowerShell modul telepítéséhez lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>2\. ok
A fájlbérlet megakadályozza a fájlok módosítását vagy törlését. Azt is megteheti, hogy egy fájl rendelkezik-e a következő PowerShell-lel, amely a `<resource-group>` `<storage-account>` `<file-share>` `<path-to-file>` környezet megfelelő értékeit helyettesíti:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Ha egy fájl bérlettel rendelkezik, a visszaadott objektumnak a következő tulajdonságokat kell tartalmaznia:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>2\. megoldás
Ha el szeretne távolítani egy bérletet egy fájlból, felszabadíthatja vagy megszakíthatja a bérletet. A bérlet felszabadításához szüksége lesz a bérlet létrehozásakor beállított bérletazonosítóra. A bérlet megszakításához nincs szükséges a bérletazonosítóra.

Az alábbi példa bemutatja, hogyan szakíthatja meg a 2. ok által jelzett fájl bérletét (ez a példa a 2. ok PowerShell-változóit folytatja):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>A fájlok az Azure Files szolgáltatásba vagy onnan máshová történő lassú másolása Windows rendszeren

Előfordulhat, hogy a lassú teljesítmény jelenik meg, amikor fájlokat próbál továbbítani az Azure file Service-be.

- Ha nem rendelkezik meghatározott minimális I/O-mérettel, javasoljuk, hogy az optimális teljesítmény érdekében az 1 MiB-t használja az I/O-mérethez.
-   Ha ismeri az írásokkal kiterjeszthető fájl végső méretét, és a szoftver nem rendelkezik kompatibilitási problémákkal, ha a fájl íratlan farka nulla értéket tartalmaz, a fájl méretét előre állítsa be ahelyett, hogy minden írási kibővítést ír.
-   Használja a megfelelő másolási módszert:
    -   Használjon [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a két fájlmegosztás közötti átvitelhez.
    -   Egy helyszíni számítógépen lévő fájlmegosztás esetén használja a [Robocopy](./storage-how-to-create-file-share.md) szolgáltatást.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>A Windows 8,1 vagy a Windows Server 2012 R2 szempontjai

Windows 8,1 vagy Windows Server 2012 R2 rendszerű ügyfelek esetén győződjön meg arról, hogy a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás telepítve van. Ez a gyorsjavítás javítja a létrehozási és a bezárási fogópontok teljesítményét.

A következő parancsfájl futtatásával ellenőrizhető, hogy telepítve van-e a gyorsjavítás:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha a gyorsjavítás telepítve van, a következő kimenet jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> A Windows Server 2012 R2 rendszerképek az Azure Marketplace-en alapértelmezés szerint a gyorsjavítások KB3114025 települnek, a 2015-es verziótól kezdődően.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nincs "Sajátgép" vagy "Ez a számítógép" betűjelű mappa

Ha egy Azure-fájlmegosztást rendszergazdaként rendel a net use használatával, akkor a megosztás hiányzik.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows file Explorer nem rendszergazdaként fut. Ha a net használatát rendszergazdai parancssorból futtatja, a hálózati meghajtót rendszergazdaként kell leképezni. Mivel a csatlakoztatott meghajtók felhasználó-központú, a bejelentkezett felhasználói fiók nem jeleníti meg a meghajtókat, ha egy másik felhasználói fiókhoz vannak csatlakoztatva.

### <a name="solution"></a>Megoldás
Csatlakoztassa a megosztást egy nem rendszergazdai parancssorból. Azt is megteheti, hogy [ezt a TechNet-témakört](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) követve konfigurálja a **EnableLinkedConnections** beállításazonosító értékét.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>A net use parancs végrehajtása meghiúsul, ha a Storage-fiók perjelet tartalmaz

### <a name="cause"></a>Ok

A net use parancs parancssori kapcsolóként értelmezi a továbbítási perjelet (/). Ha a felhasználói fiók neve egy továbbítási perjeltel kezdődik, a meghajtó megfeleltetése sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megkerüléséhez a következő lépések bármelyikét használhatja:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A Batch-fájlból a következő módon futtathatja a parancsot:

  `Echo new-smbMapping ... | powershell -command –`

- A probléma megkerüléséhez dupla idézőjelek közé kell tenni a kulcsokat – kivéve, ha a továbbítás perjele az első karakter. Ha igen, használja az interaktív módot, és adja meg a jelszót külön vagy a kulcsok újragenerálása egy olyan kulcs lekéréséhez, amely nem a továbbítás perjelével kezdődik.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Az alkalmazás vagy szolgáltatás nem fér hozzá csatlakoztatott Azure Files meghajtóhoz

### <a name="cause"></a>Ok

A meghajtók felhasználónként vannak csatlakoztatva. Ha az alkalmazás vagy szolgáltatás egy másik felhasználói fiók alatt fut, mint amelyik a meghajtót csatlakoztatta, az alkalmazás nem fogja látni a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások egyikét:

-   Csatlakoztassa a meghajtót ugyanahhoz a felhasználói fiókhoz, amely tartalmazza az alkalmazást. Használhat olyan eszközt is, mint például a PsExec.
- Adja át a Storage-fiók nevét és kulcsát a net use parancs Felhasználónév és jelszó paraméterében.
- A cmdkey parancs használatával adja hozzá a hitelesítő adatokat a hitelesítőadat-kezelőhöz. Ezt egy interaktív bejelentkezéssel vagy a használatával hajtsa végre a szolgáltatásfiók környezetében `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Rendelje hozzá a megosztást közvetlenül a csatlakoztatott meghajtóbetűjel használata nélkül. Előfordulhat, hogy egyes alkalmazások nem csatlakoznak megfelelően a meghajtóbetűjelhez, így a teljes UNC elérési út megbízhatóbb lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Miután elvégezte ezeket az utasításokat, a következő hibaüzenet jelenhet meg, amikor a rendszer-/hálózati szolgáltatás fiókjának net-használatát futtatja: "a 1312-es hiba történt. Egy megadott bejelentkezési munkamenet nem létezik. Lehetséges, hogy már meg lett szakítva. " Ha ez történik, győződjön meg arról, hogy a net use szolgáltatásnak átadott Felhasználónév tartományi adatokat tartalmaz (például: "[Storage Account name]. file. Core. Windows. net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Hiba: "a fájl másolása olyan célhelyre történik, amely nem támogatja a titkosítást"

Ha egy fájlt a hálózaton keresztül másol a rendszer, a rendszer visszafejti a fájlt a forrásszámítógépen, amely egyszerű szöveges formátumban lesz továbbítva és újra titkosítva van a célhelyen. A titkosított fájlok másolására tett kísérlet során azonban a következő hibaüzenet jelenhet meg: "a fájl másolása olyan célhelyre történik, amely nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha titkosított fájlrendszert (EFS) használ. A BitLocker által titkosított fájlok átmásolhatók Azure Filesba. A Azure Files azonban nem támogatja az NTFS EFS használatát.

### <a name="workaround"></a>Áthidaló megoldás
Ha egy fájlt hálózaton keresztül szeretne másolni, először vissza kell fejtenie azt. Használja az alábbi módszerek egyikét:

- Használja a **copy/d** parancsot. Lehetővé teszi a titkosított fájlok mentését visszafejtett fájlként a célhelyen.
- Adja meg a következő beállításkulcsot:
  - Elérési út = HKLM\Software\Policies\Microsoft\Windows\System
  - Értéktípus = DWORD
  - Név: CopyFileAllowDecryptedRemoteDestination
  - Érték: 1

Vegye figyelembe, hogy a beállításkulcs beállítása hatással van a hálózati megosztásokon végrehajtott összes másolási műveletre.

## <a name="slow-enumeration-of-files-and-folders"></a>Fájlok és mappák lassú számbavétele

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha nincs elegendő gyorsítótár az ügyfélszámítógépen a nagyméretű könyvtárakhoz.

### <a name="solution"></a>Megoldás

A probléma megoldásához módosítsa a **DirectoryCacheEntrySizeMax** beállításazonosító értékét, hogy engedélyezze a nagyobb könyvtár-listák gyorsítótárazását az ügyfélszámítógépen:

- Hely: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Érték Mane: DirectoryCacheEntrySizeMax 
- Érték típusa: DWORD
 
 
Beállíthatja például a 0x100000, és megtekintheti, hogy a teljesítmény jobb legyen.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Hiba történt a (z) Azure Active Directory tartományi szolgáltatás (Azure AD DS) hitelesítésének engedélyezésekor a (z) Azure Files "nem találja a (z) HRE bérlői azonosítóval rendelkező aktív bérlőket" AadDsTenantNotFound

### <a name="cause"></a>Ok

Hiba történt a AadDsTenantNotFound, amikor az Azure [AD tartományi szolgáltatást (azure AD DS)](../../active-directory-domain-services/overview.md) nem a társított előfizetés Azure ad-bérlője számára való [Azure Active Directory Domain Services (Azure AD DS) hitelesítésre próbálja engedélyezni Azure Filesn](storage-files-identity-auth-active-directory-domain-service-enable.md) .  

### <a name="solution"></a>Megoldás

Engedélyezze az Azure AD DS azon előfizetés Azure AD-bérlőn, amelyre a Storage-fiókja telepítve van. Felügyelt tartomány létrehozásához rendszergazdai jogosultsággal kell rendelkeznie az Azure AD-bérlőben. Ha nem az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse az [Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához](../../active-directory-domain-services/tutorial-create-instance.md)szükséges lépésenkénti útmutatót.

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Nem sikerült csatlakoztatni az AD hitelesítő adatokkal rendelkező Azure Files 

### <a name="self-diagnostics-steps"></a>Saját diagnosztika lépései
Először is győződjön meg arról, hogy követte az [Azure Files ad-hitelesítés engedélyezéséhez](./storage-files-identity-auth-active-directory-enable.md)szükséges négy lépést.

Másodszor, próbálja meg [csatlakoztatni az Azure-fájlmegosztást a Storage-fiók kulcsaként](./storage-how-to-use-files-windows.md). Ha nem sikerült csatlakoztatni, töltse le [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) , hogy segítsen ellenőrizni a környezetet futtató ügyfelet, és felismeri a nem kompatibilis ügyfél-konfigurációt, amely a Azure Files hozzáférési hibáját okozhatja, az önjavításra és a diagnosztikai Nyomkövetések gyűjtésére szolgáló részletes útmutatást nyújt.

Harmadszor, a Debug-AzStorageAccountAuth parancsmag futtatásával elvégezheti az Active Directory-konfiguráción végzett alapszintű ellenőrzéseket a bejelentkezett AD-felhasználó használatával. Az [AzFilesHybrid v0.1.2+ verziója](https://github.com/Azure-Samples/azure-files-samples/releases) támogatja ezt a parancsmagot. A parancsmagot egy olyan AD-felhasználóval kell futtatnia, aki tulajdonosi engedéllyel rendelkezik a cél tárfiókon.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
A parancsmag az alábbi ellenőrzéseket hajtja végre egymás után, és útmutatást nyújt a hibákhoz:
1. CheckADObjectPasswordIsCorrect: Győződjön meg arról, hogy a Storage-fiókot jelképező AD-identitáson konfigurált jelszó megegyezik a kerb1 vagy a kerb2 kulcsával. Ha a jelszó helytelen, az [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) futtatásával állíthatja alaphelyzetbe a jelszót. 
2. CheckADObject: Ellenőrizze, hogy van-e olyan objektum a Active Directoryban, amely a Storage-fiókot jelöli, és rendelkezik a megfelelő SPN-vel (egyszerű szolgáltatásnév). Ha az egyszerű szolgáltatásnév nem megfelelően van beállítva, futtassa a Debug parancsmagban visszaadott set-AD parancsmagot az egyszerű szolgáltatásnév konfigurálásához.
3. CheckDomainJoined: ellenőrzi, hogy az ügyfélszámítógép tartományhoz van-e csatlakoztatva az AD-hez. Ha a számítógép nincs tartományhoz csatlakoztatva az AD-hez, tekintse meg ezt a [cikket](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) a tartományhoz való csatlakozással kapcsolatos útmutatásért.
4. CheckPort445Connectivity: Győződjön meg arról, hogy a 445-es port az SMB-kapcsolatok számára meg van nyitva. Ha a szükséges port nincs megnyitva, tekintse meg a hibaelhárítási eszközt [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) a Azure Files kapcsolódási problémáinak elhárításához.
5. CheckSidHasAadUser: Győződjön meg róla, hogy a bejelentkezett AD-felhasználó szinkronizálva van az Azure AD-vel. Ha szeretné megkeresni, hogy egy adott AD-felhasználó szinkronizálva van-e az Azure AD-val, megadhatja a-UserName és a-domain paramétert a bemeneti paraméterekben. 
6. CheckGetKerberosTicket: a Storage-fiókhoz való kapcsolódásra irányuló Kerberos-jegy beszerzésére tett kísérlet. Ha nincs érvényes Kerberos-jogkivonat, futtassa a klist Get CIFS/Storage-Account-Name. file. Core. Windows. net parancsmagot, és vizsgálja meg a hibakódot a fő – a jegy lekérési hibája miatt.
7. CheckStorageAccountDomainJoined: Ellenőrizze, hogy az AD-hitelesítés engedélyezve van-e, és hogy a fiók AD-tulajdonságainak feltöltése megtörtént-e. Ha nem, tekintse meg a AD DS hitelesítés engedélyezése Azure Fileson című [témakör utasításait](./storage-files-identity-ad-ds-enable.md) . 
8. CheckUserRbacAssignment: Ellenőrizze, hogy az AD-felhasználó rendelkezik-e a megfelelő RBAC-szerepkör-hozzárendeléssel, hogy hozzáférést biztosítson a megosztási szinten a Azure Files eléréséhez Ha nem, tekintse meg az [itt](./storage-files-identity-ad-ds-assign-permissions.md) található utasításokat a megosztási szint engedélyének konfigurálásához. (Támogatott a AzFilesHybrid v 0.2.3 + verziójában)
9. CheckUserFileAccess: Ellenőrizze, hogy az AD-felhasználó rendelkezik-e a megfelelő könyvtár/fájl engedéllyel (Windows ACL) a Azure Files eléréséhez. Ha nem, tekintse meg az [itt](./storage-files-identity-ad-ds-configure-permissions.md) található utasításokat a könyvtár/fájl szintű engedély konfigurálásához. (Támogatott a AzFilesHybrid v 0.2.3 + verziójában)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Nem lehet konfigurálni a címtár/fájl szintű engedélyeket (Windows ACL) a Windows fájlkezelővel

### <a name="symptom"></a>Hibajelenség

Előfordulhat, hogy az alábbiakban ismertetett tüneteket tapasztalja, amikor a Windows ACL-eket egy csatlakoztatott fájlmegosztás fájlkezelője alapján kísérli meg konfigurálni:
- Miután a biztonság lapon a Szerkesztés engedélyre kattint, az engedély varázsló nem töltődik be. 
- Amikor új felhasználót vagy csoportot próbál kijelölni, a tartomány helye nem jelenik meg a megfelelő AD DS tartományon. 

### <a name="solution"></a>Megoldás

Javasoljuk, hogy a mappa/fájl szintű engedélyek megkerülő megoldásként való konfigurálásához használja a [icacls eszközt](/windows-server/administration/windows-commands/icacls) . 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Hibák Join-AzStorageAccountForAuth parancsmag futtatásakor

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Hiba: "a címtárszolgáltatás nem tudott relatív azonosítót lefoglalni"

Ez a hiba akkor fordulhat elő, ha a RID-főkiszolgáló FSMO-szerepkört birtokló tartományvezérlő nem érhető el, vagy eltávolította a tartományból, és visszaállította a biztonsági mentésből.  Győződjön meg arról, hogy az összes tartományvezérlő fut és elérhető.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Hiba: „Nem köthetők a pozicionálás paraméterek, mert nincsenek nevek megadva.”

Ezt a hibát valószínűleg a Join-AzStorageAccountforAuth parancsban lévő szintaktikai hiba váltja ki.  Ellenőrizze a hibás helyesírású vagy szintaktikai hibákat a parancsban, és ellenőrizze, hogy a AzFilesHybrid modul legújabb verziója https://github.com/Azure-Samples/azure-files-samples/releases) van-e telepítve.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files helyszíni AD DS hitelesítés támogatása AES 256 Kerberos-titkosításhoz

Az AES 256 Kerberos-titkosítási támogatást bevezetett Azure Files helyszíni AD DS hitelesítéshez a [AzFilesHybrid modul v 0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases). Ha engedélyezte a AD DS hitelesítést a v 0.2.2 alacsonyabb verziójú modulnál, le kell töltenie a legújabb AzFilesHybrid-modult (v 0.2.2 +), és az alábbi PowerShellt kell futtatnia. Ha még nem engedélyezte AD DS hitelesítését a Storage-fiókjában, ezt az [útmutatót](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) az engedélyezéshez követheti. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és kérje meg a probléma gyors megoldását.
