---
title: Azure File Sync rétegű fájlok kezelése | Microsoft Docs
description: Tippek és PowerShell-parancsmagok a többplatformos fájlok kezeléséhez
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204354"
---
# <a name="how-to-manage-tiered-files"></a>A többplatformos fájlok kezelése

Ez a cikk útmutatást nyújt azokhoz a felhasználókhoz, akik a többplatformos fájlok kezelésével kapcsolatos kérdésekkel rendelkeznek. A felhőalapú rétegek létrehozásával kapcsolatos fogalmakkal kapcsolatban tekintse meg [Azure Files gyakori](storage-files-faq.md)kérdések című témakört.

## <a name="how-to-check-if-your-files-are-being-tiered"></a>A fájlok lépcsőzetes kiválasztásának megkeresése

Azt határozza meg, hogy a fájlokat a beállított szabályzatok alapján kell-e kiértékelni, óránként egyszer. Új kiszolgálói végpont létrehozásakor két szituációt is elér:

Amikor először ad hozzá egy új kiszolgálói végpontot, gyakran előfordul, hogy a kiszolgálón található fájlok találhatók. A felhőalapú rétegek megkezdése előtt fel kell tölteni őket. A kötet szabad területének házirendje nem kezdi meg a munkáját, amíg az összes fájl kezdeti feltöltését befejezte. A választható dátumra vonatkozó házirend azonban egy adott fájl alapján fog működni, amint a fájl feltöltése megtörtént. Az egyórás időköz itt is érvényes. 

Új kiszolgálói végpont hozzáadásakor lehetséges, hogy egy üres kiszolgáló helyet csatlakoztat egy Azure-fájlmegosztás számára az adataival. Ha úgy dönt, hogy letölti a névteret, és felidézi a tartalmat a kiszolgáló kezdeti letöltése során, akkor a névtér leállása után a rendszer visszahívja a fájlokat az utolsó módosítás időbélyege alapján, amíg a kötet szabad területére vonatkozó házirend és a választható dátum-házirendi korlátok el nem jutnak.

Több módon is ellenőrizhető, hogy a fájl az Azure-fájlmegosztás szintjére lett-e osztva:
    
   *  **Keresse meg a fájl attribútumait a fájlban.**
     Kattintson a jobb gombbal egy fájlra, lépjen a **részletek** menüpontra, majd görgessen le az **attribútumok** tulajdonsághoz. A rétegű fájlok a következő attribútumokkal vannak beállítva:     
        
        | Attribútum betűjele | Attribútum | Definíció |
        |:----------------:|-----------|------------|
        | A | Archívum | Azt jelzi, hogy a fájlt biztonsági mentési szoftverrel kell biztonsági másolatot készíteni. Ez az attribútum mindig be van állítva, függetlenül attól, hogy a fájl többszintes vagy teljes mértékben a lemezen van-e tárolva. |
        | P | Ritka fájl | Azt jelzi, hogy a fájl ritka fájl. A ritka fájlok olyan speciális fájltípusok, amelyeket az NTFS biztosít a hatékony használatra, ha a lemezen lévő fájl többnyire üres. A Azure File Sync ritka fájlokat használ, mivel a fájlok teljes mértékben, vagy részben visszahívásra kerülnek. A teljes mértékben többrétegű fájlokban a fájl stream a felhőben tárolódik. Egy részben visszanevezett fájlban a fájl egy része már lemezen van. Ez akkor fordulhat elő, ha a fájlok részben olvashatók az olyan alkalmazások, mint például a multimédia lejátszók vagy a zip-segédprogramok. Ha egy fájl teljesen visszahívásra kerül a lemezre, Azure File Sync átalakítja egy ritka fájlból egy normál fájlba. Ez az attribútum csak a Windows Server 2016-es és régebbi verzióra van beállítva.|
        | M | Visszahívás az adateléréssel kapcsolatban | Azt jelzi, hogy a fájl nem teljes mértékben jelen van a helyi tárolóban. A fájl olvasása esetén a fájl legalább egy olyan Azure-fájlmegosztás lekérését eredményezi, amelyhez a kiszolgálói végpont csatlakozik. Ez az attribútum csak a Windows Server 2019 rendszerre van beállítva. |
        | L | Újraelemzési pont | Azt jelzi, hogy a fájl újraelemzési ponttal rendelkezik. Az újraelemzési pont egy speciális mutató, amely egy fájlrendszer-szűrő általi használatra szolgál. A Azure File Sync újraelemzési pontokat használ a Azure File Sync fájlrendszer-szűrő (StorageSync.sys) számára a fájl tárolására szolgáló Felhőbeli hely meghatározásához. Ez támogatja a zökkenőmentes hozzáférést. A felhasználóknak nem kell tudniuk, hogy a Azure File Sync használatban van, vagy hogyan érhetik el az Azure-fájlmegosztás fájlját. Ha egy fájl teljesen visszahívásra kerül, Azure File Sync eltávolítja az újraelemzési pontot a fájlból. |
        | O | Offline | Azt jelzi, hogy a fájl tartalma nem tárolódik a lemezen. Ha egy fájl teljesen visszahívásra kerül, Azure File Sync eltávolítja ezt az attribútumot. |

        ![A fájlhoz tartozó Tulajdonságok párbeszédpanel, ahol a Részletek lap van kijelölve](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > A mappában található összes fájl attribútumait úgy tekintheti meg, hogy hozzáadja az **attribútumok** mezőt a fájlkezelő tábla megjelenítéséhez. Ehhez kattintson a jobb gombbal egy meglévő oszlopra (például **méret**), válassza a **továbbiak** lehetőséget, majd válassza az **attribútumok** elemet a legördülő listából.
        
        > [!NOTE]
        > Az összes ilyen attribútum látható lesz a részben visszanevezett fájlok esetében is.
        
   * **`fsutil`Egy fájl újraelemzési pontjainak keresésére használható.**
       Az előző beállításban leírtaknak megfelelően a többplatformos fájlok esetében mindig van egy újraelemzési pont beállítása. Az újraelemzési pont lehetővé teszi, hogy az Azure File Sync fájlrendszer-szűrő illesztőprogramja (StorageSync.sys) olyan tartalmat kérjen le az Azure-fájlmegosztást, amely nem a kiszolgálón helyileg található. 

       Annak ellenőrzéséhez, hogy egy fájl újraelemzési ponttal rendelkezik-e, futtassa a következő parancsot egy rendszergazda jogú parancssorban vagy PowerShell-ablakban `fsutil` :

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Ha a fájl újraelemzési ponttal rendelkezik, várhatóan megtekintheti az **újraelemzési címke értékét: 0x8000001e**. Ez a hexadecimális érték a Azure File Sync tulajdonában lévő újraelemzési pont értéke. A kimenet tartalmazza azokat az újraelemzési adatokat is, amelyek az Azure-fájlmegosztás fájljának elérési útját jelölik.
        
        > [!WARNING]  
        > A `fsutil reparsepoint` segédprogram-parancs szintén képes törölni egy újraelemzési pontot. Ne hajtsa végre ezt a parancsot, kivéve, ha a Azure File Sync mérnöki csapat kéri. A parancs futtatása adatvesztést okozhat. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Alkalmazások kizárása a Felhőbeli rétegek utolsó hozzáférés időpontjának nyomon követésével

Az Azure File Sync Agent 11,1-es verziójának használatával mostantól kizárhatja a legutóbbi hozzáférés-követésből származó alkalmazásokat. Amikor egy alkalmazás hozzáfér egy fájlhoz, a rendszer a fájl utolsó elérési idejét frissíti a felhő-rétegű adatbázisban. Azok az alkalmazások, amelyek a fájlrendszert (például Anti-Virus) ellenőrzik, az összes fájlhoz ugyanazt az utolsó hozzáférési időt okozzák, ami hatással van a fájlok rétegbeli megadására.

Ha ki szeretné zárni az alkalmazásokat az utolsó hozzáférés időpontjának nyomon követéséhez, adja hozzá a folyamat nevét az HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync alatt található HeatTrackingProcessNameExclusionList beállításjegyzék-beállításhoz.

Például: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> A rendszer alapértelmezés szerint kizárja az deduplikálás és a Fájlkiszolgálói erőforrás-kezelő (FSRM) folyamatait. A folyamat kizárási listájának módosításait 5 percenként a rendszer tiszteletben tartja.

## <a name="how-to-access-the-heat-store"></a>A Heat Store elérése

A felhő-rétegek a legutóbbi hozzáférési időt és a fájl hozzáférési gyakoriságát használják annak meghatározására, hogy mely fájlokat kell a rétegekbe állítani. A felhő-előállítók szűrő-illesztőprogramja (storagesync.sys) nyomon követi a legutóbbi hozzáférési időt, és naplózza a Felhőbeli adattárolóban tárolt adatokat. Lekérheti a Heat Store-t, és mentheti egy CSV-fájlba egy helyi PowerShell-parancsmag használatával.

Ugyanazon a köteten található összes fájlhoz egyetlen Heat Store-t kell tárolni. A hőenergia nagy mennyiségű. Ha csak a "legmenőbb" elemek számát kell lekérnie, használja a-limit és a Number függvényt, és vegye fontolóra egy Alútvonal és a kötet gyökerének szűrését is.

- A PowerShell-modul importálása:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- KÖTET szabad TERÜLETe: annak a sorrendnek a megszerzéséhez, amelyben a fájlok a kötet szabad területére vonatkozó házirend alapján lesznek felhasználva:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DÁTUM házirend: annak a sorrendnek a megszerzéséhez, amelyben a fájlok a dátum házirend alapján lesznek feldolgozva:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Egy adott fájlhoz tartozó Heat Store-információk megkeresése:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Az összes fájl megjelenítése csökkenő sorrendben az utolsó hozzáférési idő szerint:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Tekintse meg, hogy a rendszer milyen sorrendben hívja fel a többrétegű fájlokat háttérbeli visszahívás vagy igény szerinti visszahívás használatával a PowerShellen keresztül:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Egy fájl vagy könyvtár lépcsőzetes kikényszerítése

> [!NOTE]
> Ha kijelöl egy lépcsőzetesen megjelenő könyvtárat, csak a címtárban jelenleg található fájlok vannak kiválasztva. Az ezen időpont után létrehozott fájlok nem kerülnek automatikusan lépcsőzetesen.

Ha engedélyezve van a felhő-előállítási funkció, a Felhőbeli rétegek automatikusan a legutóbbi hozzáférés és a módosítási idő alapján, a Felhőbeli végponton megadott mennyiségű szabad terület százalékos arányának eléréséhez. Időnként előfordulhat azonban, hogy manuálisan szeretné kényszeríteni a fájl a szintet. Ez akkor lehet hasznos, ha olyan nagyméretű fájlt ment, amelyet hosszú ideje nem kíván újra használni, és azt szeretné, hogy a köteten lévő szabad terület más fájlokhoz és mappákhoz is használható legyen. Az alábbi PowerShell-parancsokkal kényszerítheti a rétegek használatát:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Háromrétegű fájl felidézése lemezre

A fájlok lemezre való felidézésének legegyszerűbb módja a fájl megnyitása. A Azure File Sync fájlrendszer-szűrő (StorageSync.sys) zökkenőmentesen letölti a fájlt az Azure-fájlmegosztás alapján, anélkül, hogy az Ön részéről munkát kellene elosztania. A részben olvasható vagy továbbított fájltípusok, például a multimédia vagy a. zip fájlok esetében egyszerűen csak egy fájl megnyitása nem biztosítja a teljes fájl letöltését.

Annak biztosítása érdekében, hogy egy fájl teljes mértékben le legyen töltve a helyi lemezre, a PowerShell használatával kényszeríteni kell egy fájl teljes visszahívását. Ez a beállítás akkor is hasznos lehet, ha egyszerre több fájlt szeretne felidézni, például egy mappában lévő összes fájlt. Nyisson meg egy PowerShell-munkamenetet azon a kiszolgáló-csomóponton, ahol a Azure File Sync telepítve van, majd futtassa a következő PowerShell-parancsokat:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Választható paraméterek:
* `-Order CloudTieringPolicy` először a legutóbb módosított vagy elért fájlokat fogja felidézni, és az aktuális rétegű szabályzat engedélyezi. 
    * Ha a kötet szabad területére vonatkozó házirend be van állítva, a rendszer visszahívja a fájlokat, amíg el nem éri a kötet szabad területére vonatkozó házirend-beállítást. Ha például a kötet szabad házirend-beállítása 20%, akkor a visszahívás leáll, ha a kötet szabad területe eléri a 20%-ot.  
    * Ha a kötet szabad területe és a dátum házirendje konfigurálva van, a rendszer visszahívja a fájlokat, amíg el nem éri a kötet szabad területét vagy a dátum házirend-beállítását. Ha például a kötet szabad házirend-beállítása 20%, a dátum pedig 7 nap, a visszahívás leáll, ha a kötet szabad területe eléri a 20%-ot, vagy a 7 napon belül elért vagy módosított összes fájl helyi.
* `-ThreadCount` meghatározza, hogy hány fájlt lehet visszahívni párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy a rendszer milyen gyakran próbálkozzon a visszahívással egy jelenleg blokkolt fájlon.
* `-PerFileRetryDelaySeconds`meghatározza azt az időtartamot másodpercben, ameddig a rendszer újrahívja az újrapróbálkozási kísérleteket, és mindig az előző paraméterrel együtt kell használni őket.

Példa:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Ha a kiszolgálót üzemeltető helyi köteten nincs elég szabad hely az összes rétegű adat felidézéséhez, a `Invoke-StorageSyncFileRecall` parancsmag meghiúsul.  

> [!Note]
> A lépcsőzetesen beállított fájlok felidézéséhez a hálózati sávszélességnek legalább 1 MB/s-nak kell lennie. Ha a hálózati sávszélesség kevesebb, mint 1 Mbps, előfordulhat, hogy a fájlok időtúllépési hiba miatt nem tudnak felidézni.

## <a name="next-steps"></a>Következő lépések
* [Gyakori kérdések az Azure Files-szal kapcsolatban](storage-files-faq.md)
