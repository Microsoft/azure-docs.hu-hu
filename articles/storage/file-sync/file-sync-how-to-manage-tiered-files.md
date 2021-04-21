---
title: Rétegzett Azure File Sync fájlok kezelése | Microsoft Docs
description: Tippek és PowerShell-parancsmagok a rétegzett fájlok kezeléséhez
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796872"
---
# <a name="how-to-manage-tiered-files"></a>Rétegzett fájlok kezelése

Ez a cikk útmutatást nyújt a rétegzett fájlok kezelésével kapcsolatos kérdéseit kezelő felhasználóknak. A felhőbeli rétegezéssel kapcsolatos fogalmi kérdésekért tekintse meg a [gyakori Azure Files.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Annak ellenőrzése, hogy a fájlok rétegzettek-e

A rendszer óránként egyszer kiértékeli, hogy a fájlokat a beállított szabályzatok szerint kell-e rétegelni. Új kiszolgálóvégpont létrehozásakor két helyzet áll elő:

Amikor először ad hozzá egy új kiszolgálóvégpontot, a fájlok gyakran ezen a kiszolgálón vannak. A felhőbeli rétegezés megkezdése előtt fel kell tölteni őket. A kötet szabad területére vonatkozó szabályzat nem kezdi meg a munkát, amíg az összes fájl kezdeti feltöltése be nem fejeződik. A választható dátum szabályzat azonban a fájl feltöltése után, egyéni fájl alapján kezd működni. Az egyórás időköz itt is érvényes. 

Új kiszolgálóvégpont hozzáadásakor lehetséges, hogy egy üres kiszolgálóhelyet csatlakoztat egy Azure-fájlmegosztáshoz, benne az adataival. Ha úgy dönt, hogy letölti a névteret, és visszahívja a tartalmat a kiszolgálóra való kezdeti letöltés során, akkor a névtér leomlása után a rendszer a legutóbbi módosítás időbélyege alapján visszahívja a fájlokat, amíg el nem érik a kötet szabad területére vonatkozó szabályzatot és a választható dátum-szabályzatkorlátokat.

Több módon is ellenőrizheti, hogy egy fájl rétegzett-e az Azure-fájlmegosztásra:
    
   *  **Ellenőrizze a fájl attribútumát.**
     Kattintson a jobb gombbal egy fájlra, válassza a **Részletek** menüt, majd görgessen le az **Attributes (Attribútumok) tulajdonsághoz.** A rétegzett fájlokhoz a következő attribútumok vannak beállítva:     
        
        | Attribútumbetű | Attribútum | Definíció |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájl biztonsági mentését biztonsági mentési szoftvernek kell készítenie. Ez az attribútum mindig be van állítva, függetlenül attól, hogy a fájl rétegzett vagy teljesen lemezen van tárolva. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl ritka fájl. A ritka fájl egy speciális fájltípus, amely hatékony használatot biztosít az NTFS számára, ha a lemezstreamen lévő fájl többnyire üres. Azure File Sync ritka fájlokat használ, mert a fájlok teljesen rétegzettek vagy részlegesen vannak visszahívva. A teljes rétegzett fájlban a fájlstream a felhőben van tárolva. Egy részlegesen visszahívott fájlban a fájlnak ez a része már lemezen van. Ez akkor fordulhat elő, ha a fájlokat részlegesen beolvasják az olyan alkalmazások, mint a multimédia-lejátszók vagy a zip-segédprogramok. Ha egy fájl teljesen lemezre van visszahívva, Azure File Sync konvertálja azt egy ritka fájlból egy normál fájlba. Ez az attribútum csak a Windows Server 2016-os és régebbi verziókhoz van beállítva.|
        | M | Felidézi az adatelérést | Azt jelzi, hogy a fájl adatai nem teljes mértékben jelen vannak a helyi tárolón. A fájl beolvasása azt fogja okozhatni, hogy legalább a fájltartalom egy része olyan Azure-fájlmegosztásból lesz lekérve, amelyhez a kiszolgálóvégpont csatlakozik. Ez az attribútum csak Windows Server 2019 rendszeren van beállítva. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájl újraelemzési ponttal rendelkezik. Az újraelemzési pont egy speciális mutató, amelyet a fájlrendszerszűrők használhatnak. Azure File Sync újraelemzési pontok segítségével határozza meg Azure File Sync fájlrendszerszűrőt (StorageSync.sys) a felhőben, ahol a fájl tárolva van. Ez támogatja a zökkenőmentes hozzáférést. A felhasználóknak nem kell tudják, hogy Azure File Sync van használatban, vagy hogyan férhetnek hozzá az Azure-fájlmegosztásban található fájlhoz. Egy fájl teljes visszahívása után a Azure File Sync eltávolítja az újraelemzési pontot a fájlból. |
        | O | Offline | Azt jelzi, hogy a fájl tartalmának egy része vagy egésze nincs lemezen tárolva. Egy fájl teljes visszahívása után a Azure File Sync eltávolítja ezt az attribútumot. |

        ![Egy fájl Tulajdonságok párbeszédpanele, a Részletek lap kijelölve](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > A mappa összes fájljának attribútumát úgy láthatja, hogy hozzáadja az **Attributes** (Attribútumok) mezőt a mappa Fájlkezelő. Ehhez kattintson a jobb gombbal egy meglévő oszlopra (például **Méret),** válassza a **További** lehetőséget, majd válassza az **Attribútumok** lehetőséget a legördülő listából.
        
        > [!NOTE]
        > Ezek az attribútumok mind láthatók lesznek a részlegesen visszahívott fájloknál is.
        
   * **A `fsutil` használatával ellenőrizheti, hogy vannak-e újraelemzési pontok egy fájlban.**
       Ahogy azt az előző lehetőség is ismerteti, a rétegzett fájlokhoz mindig be van állítva újraelemzési pont. Az újraelemzési pont lehetővé teszi, Azure File Sync fájlrendszerszűrő-illesztőprogram (StorageSync.sys) lekérje a tartalmat a kiszolgálón helyileg nem tárolt Azure-fájlmegosztásból. 

       Annak ellenőrzéshez, hogy egy fájl rendelkezik-e újraelemzési ponttal, futtassa a segédprogramot egy emelt szintű parancssorban vagy `fsutil` PowerShell-ablakban:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Ha a fájl újraelemzési ponttal rendelkezik, a következőt **láthatja: Újraelemzési címke értéke: 0x8000001e.** Ez a hexadecimális érték az újraelemzési pont értéke, amely a Azure File Sync. A kimenet azokat az újraelemzési adatokat is tartalmazza, amelyek az Azure-fájlmegosztáson található fájl elérési útját jelölik.
        
        > [!WARNING]  
        > A segédprogram parancs az újraelemzési pont `fsutil reparsepoint` törlésére is lehetőséget ad. Csak akkor hajtsa végre ezt a parancsot, Azure File Sync mérnöki csapat erre kéri. A parancs futtatása adatvesztést eredményezhet. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Alkalmazások kizárása a felhőbeli rétegezés utolsó hozzáférési ideje nyomon követéséből

A Azure File Sync 11.1-es verziójával kizárhatja az alkalmazásokat a legutóbbi hozzáférés-követésből. Amikor egy alkalmazás hozzáfér egy fájlhoz, a fájl utolsó hozzáférési ideje frissül a felhőbeli rétegzett adatbázisban. A fájlrendszert, például víruskeresőt beolvasó alkalmazások miatt minden fájlnak ugyanaz az utolsó hozzáférési ideje, ami hatással van a fájlok rétegzett rétegzére.

Ha ki szeretne zárni alkalmazásokat az utolsó hozzáférés-követésből, adja hozzá a folyamat nevét a HeatTrackingProcessNameExclusionList beállításjegyzék-beállításhoz, amely a következő helyen HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Például: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> Az adatdeduplikáció és Resource Manager (FSRM) folyamatok alapértelmezés szerint ki vannak zárva. A rendszer 5 percenként tiszteletben tartja a folyamatkizárási lista módosításait.

## <a name="how-to-access-the-heat-store"></a>Az hőtár elérése

A felhőbeli rétegezés a fájlok utolsó hozzáférési idejét és hozzáférési gyakoriságát használja annak meghatározásához, hogy mely fájlokat kell rétegezésre. A felhőbeli rétegezés szűrőillesztője (storagesync.sys) nyomon követi az utolsó hozzáférés idejét, és naplózza az információkat a felhőbeli rétegzett hőtárban. Az hőtárat lekérheti, és egy CSV-fájlba mentheti egy kiszolgáló helyi PowerShell-parancsmag használatával.

Egyetlen hőtároló található az ugyanazon köteten lévő összes fájlhoz. A hőtároló nagyon nagy lehet. Ha csak a "leglátványosabb" számú elemet kell lekérni, használja a -Limit és egy számot, és fontolja meg az alútvonal és a kötetgyökér alapján való szűrést is.

- Importálja a PowerShell-modult:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- KÖTET SZABAD TERÜLET: A kötet szabad terület házirendje segítségével a fájlok rétegzett sorrendjének lekért sorrendje:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DATE POLICY: A dátum házirend használatával rétegzett fájlok sorrendjének lehívása:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Keresse meg egy adott fájl hőtároló-adatait:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Az összes fájlt az utolsó hozzáférés ideje szerint csökkenő sorrendben láthatja:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Tekintse meg a sorrendet, amely alapján a rétegzett fájlokat visszahívja a háttérben futó visszahívás vagy az igény szerinti visszahívás a PowerShell használatával:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Fájl vagy könyvtár rétegzettre kényszerítve

> [!NOTE]
> Ha egy rétegzett könyvtárat választ ki, csak a könyvtárban aktuálisan lévő fájlok rétege lesz rétegzett. Az ezt követően létrehozott fájlok nem kerülnek automatikusan rétegbe.

Ha a felhőbeli rétegezés engedélyezve van, a felhőbeli rétegezés automatikusan rétegezést hoz létre a fájlokhoz az utolsó hozzáférés alapján, és módosítja az időt, hogy elérje a felhővégponton megadott szabad kötetterület százalékos arányát. Előfordulhat azonban, hogy manuálisan szeretne rétegbe kényszeríteni egy fájlt. Ez akkor lehet hasznos, ha olyan nagy méretű fájlt ment, amely hosszú ideig nem lesz használatban, és azt szeretné, hogy a köteten lévő szabad terület más fájlokhoz és mappákhoz is használható legyen. A rétegezést az alábbi PowerShell-parancsokkal kényszerítheti:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Rétegzett fájl visszahívása lemezre

A fájlok lemezre való visszahívásának legegyszerűbb módja a fájl megnyitása. A Azure File Sync fájlrendszerszűrő (StorageSync.sys) zökkenőmentesen letölti a fájlt az Azure-fájlmegosztásból anélkül, hogy bármilyen munkát adne. A részlegesen olvasható vagy streamelható fájltípusok, például multimédia- vagy .zip-fájlok esetén a fájlok egyszerű megnyitása nem biztosítja a teljes fájl letöltését.

Annak biztosításához, hogy egy fájl teljes mértékben letöltve legyen a helyi lemezre, a PowerShell használatával kényszerítenie kell a fájlok teljes visszahívát. Ez a lehetőség akkor is hasznos lehet, ha egyszerre több fájlt szeretne visszahívni, például egy mappa összes fájlját. Nyisson meg egy PowerShell-munkamenetet arra a kiszolgáló-csomópontra, Azure File Sync telepítve van, majd futtassa a következő PowerShell-parancsokat:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Választható paraméterek:
* `-Order CloudTieringPolicy` először a legutóbb módosított vagy elért fájlokat fogja visszahívni, és az aktuális rétegezés házirend által engedélyezett. 
    * Ha a kötet szabad területére vonatkozó házirend konfigurálva van, a rendszer visszahívja a fájlokat, amíg el nem érik a kötet szabad területére vonatkozó házirend-beállítást. Ha például a kötet ingyenes házirend-beállítása 20%, a visszahívás leáll, amint a kötet szabad területének eléri a 20%-ot.  
    * Ha a köteten elérhető szabad terület és a dátum házirend konfigurálva van, a rendszer visszahívja a fájlokat, amíg el nem érik a kötet szabad területére vagy dátumra vonatkozó házirend-beállítását. Ha például a kötet ingyenes házirend-beállítása 20%, és a dátum házirend 7 nap, a felidézés le fog állni, ha a kötet szabad területének mérete eléri a 20%-ot, vagy a 7 napon belül elért vagy módosított összes fájl helyi.
* `-ThreadCount` A azt határozza meg, hogy hány fájl visszahívható párhuzamosan.
* `-PerFileRetryCount`A azt határozza meg, hogy a rendszer milyen gyakran kísérelje meg a jelenleg blokkolt fájl visszahívási kísérletét.
* `-PerFileRetryDelaySeconds`A meghatározza az újrapróbálkozási kísérletek közötti időt másodpercben, és mindig az előző paraméterrel együtt kell használni.

Példa:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Ha a kiszolgálót üzemeltető helyi köteten nincs elég szabad hely az összes rétegzett adat visszahívása érdekében, a `Invoke-StorageSyncFileRecall` parancsmag sikertelen lesz.  

> [!Note]
> A rétegzett fájlok visszahívása érdekében a hálózati sávszélességnek legalább 1 Mb/s sebességűnek kell lennie. Ha a hálózati sávszélesség kisebb, mint 1 Mbps, előfordulhat, hogy a fájlok időtúllépési hiba miatt nem lesznek visszahívásra.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Filesszal kapcsolatos gyakori kérdések (GYIK)](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)