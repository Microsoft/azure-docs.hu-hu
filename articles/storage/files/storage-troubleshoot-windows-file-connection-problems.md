---
title: Azure Files-problémák hibaelhárítása Windowson
description: Hibaelhárítás Azure Files Windowsban. Tekintse meg a Windows-ügyfelekről Azure Files problémák gyakori problémáit és a lehetséges megoldásokat. Csak SMB-megosztások esetén
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 115c083a75adab96e416fc200bf7db287a99ff4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788420"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Windows Azure Files (SMB) hibaelhárítása

Ez a cikk azokat a gyakori problémákat sorolja fel, amelyek a Microsoft Azure-fájlokkal kapcsolatosak, amikor Windows-ügyfelekről csatlakozik. Emellett lehetséges okokat és megoldásokat is biztosít ezekre a problémákra. A cikkben található hibaelhárítási lépéseken kívül az [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) használatával is biztosíthatja, hogy a Windows-ügyfélkörnyezet megfelelő előfeltételeket biztosít. Az AzFileDiagnostics automatizálja a cikkben említett legtöbb tünet észlelését, és segít a környezet beállításában az optimális teljesítmény elérése érdekében.

> [!IMPORTANT]
> A cikk tartalma csak az SMB-megosztások esetében érvényes. Az NFS-megosztásokkal kapcsolatos részletekért lásd: [Azure NFS-fájlmegosztások hibaelhárítása.](storage-troubleshooting-files-nfs.md)

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>5-ös hiba azure-fájlmegosztás csatlakoztatásakor

Amikor megpróbál csatlakoztatni egy fájlmegosztást, a következő hibaüzenet jelenhet meg:

- Az 5-ös rendszerhiba fordult elő. A hozzáférés megtagadva.

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Titkosítatlan kommunikációs csatorna

Biztonsági okokból az Azure-fájlmegosztásokhoz való kapcsolódás le van tiltva, ha a kommunikációs csatorna nincsen titkosítva, vagy ha a csatlakozási kísérlet nem ugyanabból az adatközpontból történik, ahol az Azure-fájlmegosztások találhatók. Az ugyanazon adatközponton belüli titkosítatlan kapcsolatokat akkor is blokkolhatja a rendszer, ha a tárfiókban engedélyezve van a [Biztonságos átvitelre van szükség](../common/storage-require-secure-transfer.md) beállítás. Csak akkor biztosítható titkosított kommunikációs csatorna, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítást.

A Windows 8, a Windows Server 2012 és újabb verzióik olyan kéréseket egyeztetnek, amelyek magukban foglalják a titkosítást támogató SMB 3.0-t.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Csatlakozás SMB-titkosítást (Windows 8, Windows Server 2012 vagy újabb) támogató ügyfélről, vagy egy, az Azure-fájlmegosztáshoz használt Azure-tárfiókkal azonos adatközpontban található virtuális gépről.
2. Ellenőrizze, [hogy a Biztonságos átvitelre](../common/storage-require-secure-transfer.md) van szükség beállítás le van-e tiltva a tárfiókon, ha az ügyfél nem támogatja az SMB-titkosítást.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>2. ok: Virtuális hálózati vagy tűzfalszabályok vannak engedélyezve a tárfiókhoz 

Ha virtuális hálózati (VNET) és tűzfalszabályok vannak konfigurálva a tárfiókhoz, a hálózati forgalom számára blokkolva lesz a hozzáférés, kivéve, ha az ügyfél IP-címe vagy a virtuális hálózat hozzáférést kapott.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>3. ok: Identitásalapú hitelesítés használata esetén a megosztásszintű engedélyek helytelenek

Ha a felhasználók Active Directory(AD) vagy Azure Active Directory Domain Services (Azure AD DS) hitelesítéssel férnek hozzá az Azure-fájlmegosztáshoz, a fájlmegosztáshoz való hozzáférés "Hozzáférés megtagadva" hibaüzenettel meghiúsul, ha a megosztási szintű engedélyek helytelenek. 

### <a name="solution-for-cause-3"></a>Megoldás a 3. okra

Ellenőrizze, hogy az engedélyek megfelelően vannak-e konfigurálva:

- **Active Directory (AD) lásd:** Megosztásszintű engedélyek [hozzárendelése egy identitáshoz.](./storage-files-identity-ad-ds-assign-permissions.md)

    A megosztásszintű engedély-hozzárendelések olyan csoportok és felhasználók esetében támogatottak, akik az Active Directory (AD) szolgáltatásból az Azure Active Directory -be (Azure AD) szinkronizáltak Azure AD Connect.  Győződjön meg arról, hogy a megosztási szintű engedélyekhez rendelt csoportok és felhasználók nem támogatottak a "csak felhőalapú" csoportok.
- **Azure Active Directory Domain Services (Azure AD DS) lásd:** Hozzáférési engedélyek [hozzárendelése egy identitáshoz.](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity)

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53-as, 67-es vagy 87-es hiba egy Azure-fájlmegosztás csatlakoztatása vagy leválasztása során

Amikor helyszíni vagy más adatközpontból próbál fájlmegosztást csatlakoztatni, a következő hibákat kaphatja:

- Az 53-as rendszerhiba fordult elő. A hálózati elérési út nem található.
- A 67-es rendszerhiba fordult elő. A hálózatnév nem található.
- A 87-es rendszerhiba fordult elő. A paraméter helytelen.

### <a name="cause-1-port-445-is-blocked"></a>1. ok: A 445-ös port blokkolva van

Az 53-as vagy a 67-es rendszerhiba akkor fordulhat elő, ha a 445-ös porton blokkolva van a Azure Files felé irányuló kimenő kommunikáció. A [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) összefoglalja, hogy mely internetszolgáltatók engedélyezik vagy tiltják a 445-ös porton keresztüli hozzáférést.

Annak ellenőrzéshez, hogy a tűzfal vagy az internetszolgáltató blokkolja-e a 445-ös portot, használja az [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) eszközt vagy `Test-NetConnection` parancsmagot. 

A `Test-NetConnection` parancsmag használatának érdekében telepítenie kell Azure PowerShell modult: [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Az Azure PowerShell telepítése). Ne felejtse el kicserélni a `<your-storage-account-name>` és a `<your-resource-group-name>` elemet a tárfiók vonatkozó neveivel.

   
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
Azure File Sync helyszíni Windows Servert az Azure-fájlmegosztás gyors gyorsítótárába tudja alakítani. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Az Azure File Sync a 443-as porton keresztül működik, ezért megkerülő megoldásként használható az Azure Files eléréséhez a letiltott 445-ös porttal rendelkező ügyfelekről. [Ismerje meg, hogyan kell beállítani a Azure File Sync.](../file-sync/file-sync-extend-servers.md)

#### <a name="solution-2---use-vpn"></a>2. megoldás – VPN használata
Ha VPN-t hoz létre az adott tárfiókhoz, a forgalom egy biztonságos alagúton halad át, nem pedig az interneten keresztül. Ha Windowsból szeretné elérni az Azure Filest, kövesse [a VPN beállításához kapcsolódó utasításokat](storage-files-configure-p2s-vpn-windows.md).

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>3. megoldás – A 445-ös port blokkolásának feloldása az internetszolgáltató vagy a rendszergazda segítségével
Az IT-részleg vagy az internetszolgáltató segítségével nyissa meg a 445-ös kimenő portot az [Azure IP-címtartományai felé.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>4. megoldás – REST API-alapú eszközök, például a Storage Explorer és a Powershell használata
Azure Files SMB mellett a REST-et is támogatja. A REST-hozzáférés a 443-as (szabványos TCP-) porton keresztül működik. Számos olyan REST API-val írt eszköz létezik, amely gazdag felhasználói felületi élmény megvalósítását teszi lehetővé. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) az egyik ilyen. [Töltse le és telepítse a Storage Explorert](https://azure.microsoft.com/features/storage-explorer/), és csatlakozzon az Azure Files-támogatással rendelkező fájlmegosztáshoz. A [PowerShellt is használhatja,](./storage-how-to-use-files-powershell.md) amely szintén felhasználói REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>2. ok: Az NTLMv1 engedélyezve van

Az 53-as vagy a 87-es rendszerhiba akkor fordulhat elő, ha az ügyfélen engedélyezve van az NTLMv1 kommunikáció. Az Azure Files kizárólag az NTLMv2-hitelesítést támogatja. Az NTLMv1 engedélyezése csökkenti az ügyfél biztonságát. Ezért kerül sor az Azure Files kommunikációjának blokkolására. 

Annak megállapításához, hogy ez-e a hiba oka, ellenőrizze, hogy a beállításjegyzék következő alkulcsa nem 3-asnál kisebb értékre van-e állítva:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információért tekintse meg a TechNet [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) témakörét.

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza az **LmCompatibilityLevel** értékét az alapértelmezett 3-ra a következő beállításjegyzékbeli alkulcsban:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>1816-os hiba – Nincs elég kvóta a parancs feldolgozásához

### <a name="cause"></a>Ok

Az 1816-os hiba akkor fordul elő, ha eléri az Azure-fájlmegosztáson lévő fájlhoz vagy könyvtárhoz engedélyezett egyidejű nyitott leírók felső korlátját. További információ: [Azure Files – skálázási célok](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Megoldás

Csökkentse az egyidejű nyitott leírók számát néhány leíró bezárásával, majd próbálja meg újra. További információ: Microsoft Azure Storage [és skálázhatósági ellenőrzőlista.](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Egy fájlmegosztás, könyvtár vagy fájl megnyitott leíróinak megtekintéséhez használja a [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmagot.  

Egy fájlmegosztás, könyvtár vagy fájl megnyitott leíróit a [Close-AzStorageFileHandle PowerShell-parancsmag](/powershell/module/az.storage/close-azstoragefilehandle) használatával zárhatja be.

> [!Note]  
> A Get-AzStorageFileHandle és Close-AzStorageFileHandle parancsmagokat az Az PowerShell-modul 2.4-es vagy újabb verziója tartalmazza. A legújabb Az PowerShell-modul telepítéséhez lásd: Install the Azure PowerShell module (A [Azure PowerShell modul telepítése).](/powershell/azure/install-az-ps)

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>"Nincs hozzáférés" hiba az Azure-fájlmegosztások elérésekor vagy törlésekor  
Amikor megpróbál elérni vagy törölni egy Azure-fájlmegosztást a portálon, a következő hibaüzenet jelenhet meg:

Nincs hozzáférés  
Hibakód: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>1. ok: A virtuális hálózati vagy tűzfalszabályok engedélyezve vannak a tárfiókon

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

Ellenőrizze, hogy a virtuális hálózati és tűzfalszabályok megfelelően vannak-e konfigurálva a tárfiókhoz. Ha meg szeretne bizonyosodni arról, hogy a virtuális hálózati vagy a tűzfalszabályok okozzák a problémát, ideiglenesen módosítsa a tárfiók beállítását a következőre: **Hozzáférés engedélyezése minden hálózatról**. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>2. ok: A felhasználói fióknak nincs hozzáférése a tárfiókhoz

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Keresse meg azt a tárfiókot, ahol az Azure-fájlmegosztás található, kattintson a Hozzáférés-vezérlés **(IAM)** elemre, és ellenőrizze, hogy a felhasználói fiók hozzáfér-e a tárfiókhoz. További tudnivalókért [lásd: Tárfiók biztonságossá tere az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC).](../blobs/security-recommendations.md#data-protection)

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Nem lehet módosítani, áthelyezni/átnevezni vagy törölni egy fájlt vagy könyvtárat
A fájlmegosztások egyik fő célja, hogy egyszerre több felhasználó és alkalmazás is kommunikálhat a megosztásban lévő fájlokkal és könyvtárakkal. Ennek az interakciónak a támogatásában a fájlmegosztások számos módszert kínálnak a fájlokhoz és könyvtárakhoz való hozzáférés közvetítői módként való elérésére.

Amikor SMB-s használatával nyit meg egy fájlt egy csatlakoztatott Azure-fájlmegosztásból, az alkalmazás/operációs rendszer egy fájlkezelőt kér, amely a fájlra hivatkozik. Az alkalmazás többek között egy fájlmegosztási módot ad meg, amikor fájllefogópontot kér, amely a fájlhoz való hozzáférés a következő által kikényszeríteni kívánt Azure Files: 

- `None`: kizárólagos hozzáféréssel rendelkezik. 
- `Read`: előfordulhat, hogy mások olvassák a fájlt, amíg Ön megnyitja.
- `Write`: mások is írhatnak a fájlba, amíg Ön megnyitja. 
- `ReadWrite`: a és a megosztási `Read` `Write` mód kombinációja.
- `Delete`: mások törölhetik a fájlt, amíg Megnyitva van. 

Bár állapot nélküli protokollként a FileREST protokoll nem rendelkezik fájlleírókkal, hasonló mechanizmust biztosít a parancsfájl, alkalmazás vagy szolgáltatás által használt fájlokhoz és mappákhoz való hozzáféréshez: fájlbérleteket. A bérletes fájlokat a rendszer egyenértékűként kezeli a fájlmegosztási módú `None` fájlkezelővel. 

Bár a fájlkezelők és -bérletek fontos célt szolgálnak, a fájlkezelők és -bérletek néha árvaak lehetnek. Ebben az esetben ez problémákat okozhat a fájlok módosításával vagy törlésével kapcsolatban. A következő hibaüzenetek jelenhetnek meg:

- A folyamat nem éri el a fájlt, mert egy másik folyamat használja.
- A műveletet nem lehet befejezni, mert a fájl meg van nyitva egy másik programban.
- A dokumentumot egy másik felhasználó zárolta szerkesztésre.
- Egy SMB-ügyfél törlésre jelölte meg a megadott erőforrást.

A probléma megoldása attól függ, hogy ezt árva fájlkezelő vagy bérlet okozza-e. 

### <a name="cause-1"></a>1\. ok
A fájlkezelő megakadályozza egy fájl/könyvtár módosítását vagy törléset. A Megnyitott leírók megtekintéséhez használhatja a [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell-parancsmagot. 

Ha minden SMB-ügyfél bezárta a megnyitott leíróit egy fájlon/könyvtáron, és a probléma továbbra is fennáll, kényszerítheti a fájlkezelők bezárásat.

### <a name="solution-1"></a>1\. megoldás
Egy fájllefogópont bezáródásának kényszerítésénél használja a [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell-parancsmagot. 

> [!Note]  
> A Get-AzStorageFileHandle és Close-AzStorageFileHandle parancsmagokat az Az PowerShell-modul 2.4-es vagy újabb verziója tartalmazza. A legújabb Az PowerShell-modul telepítéséhez lásd: Install the Azure PowerShell module (Az [Azure PowerShell modul telepítése).](/powershell/azure/install-az-ps)

### <a name="cause-2"></a>2\. ok
A fájlbérlet megakadályozza a fájlok módosítását vagy törlését. A következő PowerShell használatával ellenőrizheti, hogy egy fájl rendelkezik-e fájlbérlettel, lecserélve a , , és értékeket a környezetének `<resource-group>` `<storage-account>` megfelelő `<file-share>` `<path-to-file>` értékekre:

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

Az alábbi példa bemutatja, hogyan lehet megszakítani a 2. okként jelzett fájl bérletét (ez a példa a 2. ok powershell-változóit folytatja):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>A fájlok az Azure Files szolgáltatásba vagy onnan máshová történő lassú másolása Windows rendszeren

Ha az Azure File szolgáltatásba próbál fájlokat áthelyezni, a teljesítmény lassú lehet.

- Ha nincs meg a minimális I/O-méretre vonatkozó követelmény, javasoljuk, hogy az optimális teljesítmény érdekében 1 MiB-t használjon I/O-méretként.
-   Ha ismeri az írásokkal kibővített fájl végső méretét, és a szoftver nem okoz kompatibilitási problémákat, amikor a fájl íratlan írási faroka nullákat tartalmaz, akkor előre állítsa be a fájlméretet ahelyett, hogy minden írást bővítő írási módra írna.
-   Használja a megfelelő másolási módszert:
    -   Két fájlmegosztás közötti átvitelhez használja az [AzCopyt.](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
    -   Használja a [Robocopyt](./storage-how-to-create-file-share.md) a helyszíni számítógépen található fájlmegosztások között.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Megfontolandó szempontok Windows 8.1 vagy Windows Server 2012 R2 esetén

A Windows 8.1 vagy Windows Server 2012 R2 rendszert futtató ügyfelek esetében győződjön meg arról, hogy telepítve van a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás. Ez a gyorsjavítás javítja a leírók létrehozása és bezárása teljesítményét.

A következő szkript futtatásával ellenőrizheti, hogy a gyorsjavítás telepítve van-e:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha telepítve van a gyorsjavítás, a következő kimenet jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> A Windows Server 2012 R2 Azure Marketplace 2015 decemberéig alapértelmezés szerint telepítve van a KB3114025 gyorsjavítás.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nincs meghajtóbetűjellel nem tartalmazó mappa a "Saját számítógép" vagy "Ez a számítógép" mappában

Ha egy Azure-fájlmegosztást net use használatával leképez rendszergazdaként, úgy tűnik, hogy a megosztás hiányzik.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows Fájlkezelő nem rendszergazdaként fut. Ha a net use parancsot rendszergazdai parancssorból futtatja, a hálózati meghajtót rendszergazdaként fogja leképezni. Mivel a leképezett meghajtók felhasználó-központúak, a bejelentkezett felhasználói fiók nem jeleníti meg a meghajtókat, ha egy másik felhasználói fiókhoz vannak csatlakoztatva.

### <a name="solution"></a>Megoldás
Csatlakoztassa a megosztást egy nem rendszergazdai parancssorból. Másik lehetőségként ezt a [TechNet-témakört is](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) használhatja az **EnableLinkedConnections** beállításazonosító konfiguráláshoz.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>A net use parancs meghiúsul, ha a tárfiók perjelet tartalmaz

### <a name="cause"></a>Ok

A net use parancs a perjelet (/) parancssori kapcsolóként értelmezi. Ha a felhasználói fiók neve perjellel kezdődik, a meghajtóleképezés sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megoldásához az alábbi lépések egyikét használhatja:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Kötegelt fájlból a parancsot a következő módon futtathatja:

  `Echo new-smbMapping ... | powershell -command –`

- A probléma megoldásához tegyen idézőjeleket a kulcs köré – kivéve, ha a perjel az első karakter. Ha igen, használja az interaktív módot, és adja meg a jelszót külön, vagy újragenerálja a kulcsokat, hogy olyan kulcsot kap, amely nem perjellel kezdődik.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Az alkalmazás vagy szolgáltatás nem tud hozzáférni a csatlakoztatott Azure Files meghajtóhoz

### <a name="cause"></a>Ok

A meghajtók felhasználónként vannak csatlakoztatva. Ha az alkalmazás vagy szolgáltatás a meghajtóhoz csatlakoztatotttól eltérő felhasználói fiókban fut, az alkalmazás nem fogja látni a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások egyikét:

-   Csatlakoztassa a meghajtót ugyanattól a felhasználói fióktól, amely az alkalmazást is tartalmazza. Használhatja például a PsExec eszközt.
- Adja át a tárfiók nevét és kulcsát a net use parancs felhasználónév- és jelszóparaméterében.
- A cmdkey paranccsal adja hozzá a hitelesítő adatokat a Hitelesítőadat-kezelő. Ezt egy parancssorból hajtsa végre a szolgáltatásfiók környezetében, interaktív bejelentkezéssel vagy a `runas` használatával.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Leképezheti a megosztást közvetlenül, leképezett meghajtóbetűjel használata nélkül. Előfordulhat, hogy egyes alkalmazások nem csatlakoznak újra megfelelően a meghajtó betűjeléhez, így a teljes UNC elérési út megbízhatóbb lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Miután követte ezeket az utasításokat, a rendszer-/hálózati szolgáltatásfiók net use parancsának futtatásakor a következő hibaüzenet jelenhet meg: "1312-es rendszerhiba történt. A megadott bejelentkezési munkamenet nem létezik. Lehet, hogy már le lett ásva." Ebben az esetben győződjön meg arról, hogy a net use szolgáltatásnak átadott felhasználónév tartalmazza a tartományadatokat (például: "[tárfiók neve].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>"A fájlt olyan célhelyre másolja, amely nem támogatja a titkosítást" hiba

Amikor egy fájlt a hálózaton keresztül másol, a rendszer visszafejti a fájlt a forrásszámítógépen, egyszerű szövegként továbbítja, majd újra titkosítja a célhelyen. A titkosított fájlok másolása során azonban a következő hibaüzenet jelenhet meg: "A fájlt olyan célhelyre másolja, amely nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha a titkosított fájlrendszer (EFS) használja. A BitLocker által titkosított fájlok átmásolhatók a Azure Files. A Azure Files azonban nem támogatja az NTFS EFS-t.

### <a name="workaround"></a>Áthidaló megoldás
Egy fájl hálózaton keresztüli másolásához először vissza kell fejtenie azt. Használja a következő módszerek egyikét:

- Használja a **copy /d** parancsot. Lehetővé teszi a titkosított fájlok visszafejtett fájlként való mentését a célhelyen.
- Adja meg a következő beállításkulcsot:
  - Elérési út = HKLM\Software\Policies\Microsoft\Windows\System
  - Érték típusa = DWORD
  - Név: CopyFileAllowDecryptedRemoteDestination
  - Érték: 1

Vegye figyelembe, hogy a beállításkulcs beállítása hatással van a hálózati megosztások összes másolási műveletére.

## <a name="slow-enumeration-of-files-and-folders"></a>Fájlok és mappák lassú számbavétele

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha nincs elegendő gyorsítótár az ügyfélszámítógépen a nagy méretű könyvtárak számára.

### <a name="solution"></a>Megoldás

A probléma megoldásához módosítsa a **DirectoryCacheEntrySizeMax** beállításazonosítót, hogy lehetővé tegye a nagyobb könyvtároldalak gyorsítótárazást az ügyfélszámítógépen:

- Hely: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Érték mane: DirectoryCacheEntrySizeMax 
- Érték típusa:DWORD
 
 
Beállíthatja például úgy, hogy 0x100000, és lássa, jobb lesz-e a teljesítmény.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>AadDsTenantNotFound hiba az Azure Active Directory Domain Service- (Azure AD DS-) hitelesítés engedélyezésével Azure Files "Nem sikerült megtalálni az aad-tenant-id bérlőazonosítóval működő aktív bérlőket"

### <a name="cause"></a>Ok

Az AadDsTenantNotFound hiba akkor fordul elő, ha Azure Active Directory Domain Services [(Azure AD DS)](storage-files-identity-auth-active-directory-domain-service-enable.md) hitelesítést próbál engedélyezni az Azure Files-n egy olyan tárfiókon, ahol az [Azure AD Domain Service(Azure AD DS)](../../active-directory-domain-services/overview.md) nem jött létre a társított előfizetés Azure AD-bérlőjén.  

### <a name="solution"></a>Megoldás

Engedélyezze Azure AD DS a tárfiókot üzembe helyező előfizetés Azure AD-bérlőjéhez. Felügyelt tartomány létrehozásához az Azure AD-bérlő rendszergazdai jogosultságaira van szükség. Ha nem Ön az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse a felügyelt tartomány létrehozásáról és konfigurálásról [Azure Active Directory Domain Services útmutatót.](../../active-directory-domain-services/tutorial-create-instance.md)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Nem lehet csatlakoztatni Azure Files AD hitelesítő adatokkal 

### <a name="self-diagnostics-steps"></a>Az öndiagnosztika lépései
Először is győződjön meg arról, hogy követte mind a négy lépést az [AD Azure Files engedélyezéséhez.](./storage-files-identity-auth-active-directory-enable.md)

Másodszor próbálja meg az [Azure-fájlmegosztást a tárfiókk kulccsal való csatlakoztatásával.](./storage-how-to-use-files-windows.md) Ha nem sikerült csatlakoztatnia, töltse le a [AzFileDiagnostics.ps1-t, ](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) amely segít az ügyfélen futó környezet ellenőrzésében, észleli a nem kompatibilis ügyfélkonfigurációt, amely hozzáférési hibát okozna a Azure Files-hez, előíró útmutatást nyújt az önjavításhoz, és begyűjti a diagnosztikai nyomkövetéseket.

Harmadikként futtathatja a Debug-AzStorageAccountAuth parancsmagot, hogy alapszintű ellenőrzéseket végezzen az AD-konfiguráción a bejelentkezett AD-felhasználóval. Az [AzFilesHybrid v0.1.2+ verziója](https://github.com/Azure-Samples/azure-files-samples/releases) támogatja ezt a parancsmagot. A parancsmagot egy olyan AD-felhasználóval kell futtatnia, aki tulajdonosi engedéllyel rendelkezik a cél tárfiókon.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
A parancsmag sorrendben végzi el ezeket az ellenőrzéseket, és útmutatást nyújt a hibákhoz:
1. CheckADObjectPasswordIsCorrect: Győződjön meg arról, hogy a tárfiókot képviselő AD-identitáson konfigurált jelszó megegyezik a kerb1 vagy kerb2 kulcs tárfiókhoz tartozó jelszóval. Ha a jelszó helytelen, az [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) parancs futtatásával állíthatja alaphelyzetbe a jelszót. 
2. CheckADObject: Győződjön meg arról, hogy a tárfióknak megfelelő objektum Active Directory a tárfiókot jelöli, és a megfelelő egyszerű szolgáltatásnévvel (egyszerű szolgáltatásnév) rendelkezik. Ha az spN nem megfelelően van beállítva, futtassa a hibakeresési parancsmagban visszaadott Set-AD parancsmagot az egyszerű szolgáltatás egyszerű szolgáltatásának konfigurálásához.
3. CheckDomainJoined: Ellenőrizze, hogy az ügyfélszámítógép csatlakozik-e az AD-hez. Ha a gépe nincs tartományhoz csatlakozva az AD-hez, ebben [a](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) cikkben talál útmutatást a tartományhoz való csatlakozáshoz.
4. CheckPort445Connectivity: Ellenőrizze, hogy a 445-ös port meg van-e nyitva az SMB-kapcsolathoz. Ha a szükséges port nincs megnyitva, tekintse meg a hibaelhárító eszközt, [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) problémákat a Azure Files.
5. CheckSidHasAadUser: Ellenőrizze, hogy a bejelentkezett AD-felhasználó szinkronizálva van-e az Azure AD-val. Ha azt szeretné, hogy egy adott AD-felhasználó szinkronizálva legyen-e az Azure AD-be, a bemeneti paraméterekben megadhatja a -UserName és a -Domain paramétereket. 
6. CheckGetKerberosTicket: Kerberos-jegy lekért száma a tárfiókhoz való csatlakozáshoz. Ha nincs érvényes Kerberos-jogkivonat, futtassa a get cifs/storage-account-name.file.core.windows.net parancsmag klistet, és vizsgálja meg a hibakódot a jegylekérési hiba kiváltó okának okához.
7. CheckStorageAccountDomainJoined: Ellenőrizze, hogy engedélyezve van-e az AD-hitelesítés, és hogy a fiók AD-tulajdonságai fel vannak-e töltve. Ha nem, tekintse meg az itt [leírtakat](./storage-files-identity-ad-ds-enable.md) a hitelesítés engedélyezéséhez AD DS a Azure Files. 
8. CheckUserRbacAssignment: Ellenőrizze, hogy az AD-felhasználó rendelkezik-e a megfelelő RBAC-szerepkör-hozzárendeléssel a hozzáférés megosztási szintű Azure Files. Ha nem, tekintse meg az itt [található](./storage-files-identity-ad-ds-assign-permissions.md) utasításokat a megosztási szint engedélyének konfigurálásán. (Az AzFilesHybrid 0.2.3-as vagy újabb verzióján támogatott)
9. CheckUserFileAccess: Ellenőrizze, hogy az AD-felhasználó rendelkezik-e a megfelelő könyvtár-/fájlengedélyekkel (Windows ACL-ekkel) a hozzáférés-Azure Files. Ha nem, tekintse meg az itt [található](./storage-files-identity-ad-ds-configure-permissions.md) utasításokat a könyvtár-/fájlszintű engedély konfigurálásához. (Az AzFilesHybrid 0.2.3-as vagy újabb verzióján támogatott)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>A könyvtár- és fájlszintű engedélyek (Windows ACL-ek) nem konfigurálhatóak a Windows Fájlkezelő

### <a name="symptom"></a>Hibajelenség

Az alábbiakban ismertetett tünetek bármelyikét tapasztalhatja, amikor windowsos ACL-eket próbál Fájlkezelő csatlakoztatott fájlmegosztáson:
- Miután rákattintott a Szerkesztés engedélyre a Biztonság lap, az Engedély varázsló nem töltődik be. 
- Amikor új felhasználót vagy csoportot próbál kiválasztani, a tartomány helye nem a megfelelő AD DS jelenik meg. 

### <a name="solution"></a>Megoldás

Áthidaló megoldásként javasoljuk, hogy [az icacls](/windows-server/administration/windows-commands/icacls) eszközzel konfigurálja a könyvtár-/fájlszintű engedélyeket. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Hibák a parancsmag Join-AzStorageAccountForAuth futtatásakor

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Hiba: "A címtárszolgáltatás nem tudott relatív azonosítót lefoglalni"

Ez a hiba akkor fordulhat elő, ha egy olyan tartományvezérlő, amely a rid-főkiszolgáló FSMO-szerepkörét tartalmazza, nem érhető el, vagy eltávolították a tartományból, és biztonsági másolatból lettek visszaállítva.  Győződjön meg arról, hogy minden tartományvezérlő fut és elérhető.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Hiba: „Nem köthetők a pozicionálás paraméterek, mert nincsenek nevek megadva.”

Ezt a hibát valószínűleg a Join-AzStorageAccountforAuth parancsban lévő szintaktikai hiba váltja ki.  Ellenőrizze a parancsban, hogy vannak-e helytelenül írt vagy szintaktikai hibák, és ellenőrizze, hogy telepítve van-e az AzFilesHybrid modul () https://github.com/Azure-Samples/azure-files-samples/releases) legújabb verziója.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files AES 256 Kerberos-titkosítás AD DS hitelesítés támogatása

Bevezettük az AES 256 Kerberos titkosítási támogatását Azure Files az [AzFilesHybrid modul 0.2.2-es AD DS-hitelesítéséhez.](https://github.com/Azure-Samples/azure-files-samples/releases) Ha a 0.2.2-es verziónál régebbi modulverzióval engedélyezte az AD DS-hitelesítést, le kell töltenie a legújabb AzFilesHybrid modult (v0.2.2+), és futtatnia kell az alábbi PowerShellt. Ha még nem engedélyezte AD DS hitelesítést a tárfiókon, [](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) az engedélyezéshez kövesse ezt az útmutatót. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.
Ha továbbra is segítségre van szüksége, vegye fel a [kapcsolatot](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálattal a probléma gyors megoldása érdekében.
