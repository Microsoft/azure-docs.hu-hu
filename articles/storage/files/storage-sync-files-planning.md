---
title: Központi telepítési Azure File Sync tervezése | Microsoft Docs
description: Tervezze meg az üzembe helyezést a Azure File Sync szolgáltatással, amely lehetővé teszi több Azure-fájlmegosztás gyorsítótárazása egy helyszíni Windows Serveren vagy felhőalapú virtuális gépen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 9382feeb16f2f6a82b946c05a6b4780866fdda5c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600148"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Az Azure File Sync üzembe helyezésének megtervezése

:::row:::
    :::column:::
        [![Állásinterjú és bemutató a Azure File Sync – kattintson a lejátszáshoz!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync szolgáltatás lehetővé teszi több Azure-fájlmegosztás gyorsítótárazása egy helyszíni Windows Server- vagy felhőalapú virtuális gépen. 
        
        Ez a cikk bemutatja a Azure File Sync alapfogalmait és funkcióit. Ha már ismeri a Azure File Sync, érdemes lehet Azure File Sync üzembe [helyezési útmutatót](storage-sync-files-deployment-guide.md) követnie, hogy kipróbálja ezt a szolgáltatást.        
    :::column-end:::
:::row-end:::

A fájlok a felhőben lesznek tárolva az [Azure-fájlmegosztások között.](storage-files-introduction.md) Az Azure-fájlmegosztások kétféleképpen használhatók: közvetlenül a kiszolgáló nélküli Azure-fájlmegosztások (SMB) csatlakoztatásával, vagy az Azure-fájlmegosztások helyszíni gyorsítótárazása a Azure File Sync. A választott üzembe helyezési lehetőség módosítja az üzembe helyezéshez megfontolni kívánt szempontokat. 

- **Azure-fájlmegosztás** közvetlen csatlakoztatása: Mivel az Azure Files SMB-hozzáférést biztosít, az Azure-fájlmegosztások a helyszínen vagy a felhőben is csatlakoztathatóak a Windows, macOS és Linux rendszereken elérhető standard SMB-ügyfél használatával. Mivel az Azure-fájlmegosztások kiszolgáló nélküliek, az éles környezetben való üzembe helyezéshez nincs szükség fájlkiszolgáló vagy NAS-eszköz kezelésére. Ez azt jelenti, hogy nem kell szoftverjavításokat alkalmaznia vagy fizikai lemezeket cserélnie. 

- Gyorsítótárazza a helyszíni Azure-fájlmegosztást a **Azure File Sync:** Azure File Sync segítségével központosíthatja a szervezet fájlmegosztását az Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Azure File Sync helyszíni (vagy felhőbeli) Windows Servert alakít át az Azure-fájlmegosztás gyors gyorsítótárába. 

## <a name="management-concepts"></a>Felügyeleti fogalmak
A Azure File Sync központi telepítése három alapvető felügyeleti objektumból rendelkezik:

- **Azure-fájlmegosztás:** Az Azure-fájlmegosztások kiszolgáló nélküli felhőbeli fájlmegosztások, amelyek egy szinkronizálási kapcsolat felhőbeli végpontját Azure File Sync biztosítják.  Az Azure-fájlmegosztásban lévő fájlok közvetlenül az SMB-val vagy a FileREST protokollal érhetők el, azonban javasoljuk, hogy elsősorban a Windows Server gyorsítótárán keresztül fér hozzá a fájlokhoz, amikor az Azure-fájlmegosztást a Azure File Sync. Ennek az az oka Azure Files hogy a windowsos kiszolgálóhoz hasonló hatékony változásészlelési mechanizmus jelenleg nem működik, így az Azure-fájlmegosztás közvetlen módosítása időt fog igénybe venni a kiszolgálóvégpontra való visszaterjesztéshez.
- **Kiszolgálóvégpont:** Az Azure-fájlmegosztással szinkronizált Windows Server elérési útja. Ez lehet egy adott mappa egy köteten vagy a kötet gyökerében. Több kiszolgálóvégpont is létezhet ugyanazon a köteten, ha a névtereik nincsenek átfedésben egymással.
- **Szinkronizálási csoport:** A felhőbeli végpont , az Azure-fájlmegosztás és **a** kiszolgálóvégpont közötti szinkronizálási kapcsolatot meghatározó objektum. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. Ha például két különálló fájlkészletet szeretne kezelni a Azure File Sync-val, akkor két szinkronizálási csoportot kell létrehoznia, és mindegyikhez különböző végpontokat kell hozzáadnia.

### <a name="azure-file-share-management-concepts"></a>Az Azure-fájlmegosztások kezelésével kapcsolatos fogalmak
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync felügyeleti fogalmak
A szinkronizálási csoportok a Társzinkronizálási szolgáltatásokban vannak telepítve, amelyek legfelső szintű objektumok, amelyek regisztrálják a kiszolgálókat a Azure File Sync és tartalmazzák a szinkronizálási csoport kapcsolatait. A Társviszony-szinkronizálási szolgáltatás erőforrás a tárfiók-erőforrás társviszonya, és hasonlóképpen helyezhető üzembe Azure-erőforráscsoportokban. A Storage Sync Service olyan szinkronizálási csoportokat hozhat létre, amelyek több tárfiók és több regisztrált Windows-kiszolgáló Azure-fájlmegosztását tartalmazzák.

Mielőtt létrehozhat egy szinkronizálási csoportot egy Storage Sync Service-szolgáltatásban, először regisztrálnia kell egy Windows Servert a társzinkronizálási szolgáltatással. Ez létrehoz egy **regisztrált kiszolgálóobjektumot,** amely megbízhatósági kapcsolatot jelent a kiszolgáló vagy fürt és a Storage Sync Service között. A Storage Sync Service regisztráláshoz először telepítenie kell a Azure File Sync ügynököt a kiszolgálóra. Egy különálló kiszolgáló vagy fürt egyszerre csak egy szinkronizálási szolgáltatással regisztrálható.

A szinkronizálási csoportok egy felhőbeli végpontot vagy Azure-fájlmegosztást és legalább egy kiszolgálóvégpontot tartalmaznak. A kiszolgálóvégpont objektum tartalmazza  azokat a beállításokat, amelyek konfigurálják a felhőbeli rétegezés képességet, ami a felhőbeli rétegezés gyorsítótárazása Azure File Sync. Az Azure-fájlmegosztással való szinkronizáláshoz az Azure-fájlmegosztást tartalmazó tárfióknak ugyanabban az Azure-régióban kell lennie, mint a Storage Sync Service.

> [!Important]  
> A szinkronizálási csoport bármely felhőbeli végpontjának vagy kiszolgálóvégpontjának névterét módosíthatja, és szinkronizálhatja a fájlokat a szinkronizálási csoport többi végpontjára. Ha közvetlenül módosítja a felhővégpontot (Azure-fájlmegosztást), a módosításokat először egy módosításészlelési feladatnak kell Azure File Sync észlelni. A rendszer 24 óránként csak egyszer kezdeményez változásészlelési feladatot egy felhőbeli végponton. További információt a gyakori [Azure Files tartalmaz.](storage-files-faq.md#afs-change-detection)

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Vegye figyelembe a szükséges Szinkronizálási szolgáltatások számát
Az előző szakasz a tárfiókhoz konfigurálni szükséges alapvető erőforrást ismerteti Azure File Sync: a *Storage Sync Service.* A Windows Server csak egy Szinkronizálási szolgáltatásban regisztrálható. Ezért gyakran az a legjobb, ha csak egyetlen szinkronizálási szolgáltatást helyez üzembe, és regisztrálja az összes kiszolgálót. 

Több szinkronizálási szolgáltatást csak akkor hozzon létre, ha:
* különálló kiszolgálókészletek, amelyek soha nem cserélnek adatokat egymással. Ebben az esetben úgy szeretné tervezni a rendszert, hogy kizárja azokat a kiszolgálókat, amelyek szinkronizálva vannak egy olyan Azure-fájlmegosztással, amely már használatban van felhővégpontként egy másik Szinkronizálási szolgáltatás szinkronizálási csoportjában. Ezt úgy is meg lehet nézni, hogy a különböző tárolószinkronizálási szolgáltatásban regisztrált Windows-kiszolgálók nem szinkronizálhatóak ugyanazokkal az Azure-fájlmegosztásokkal.
* több regisztrált kiszolgálóra vagy szinkronizálási csoportra van szükség, mint amennyit egyetlen Storage Sync-szolgáltatás támogatni tud. További [részletekért Azure File Sync tekintse át a méretezési](storage-files-scale-targets.md#azure-file-sync-scale-targets) célokat ismertető ismertetőt.

## <a name="plan-for-balanced-sync-topologies"></a>Kiegyensúlyozott szinkronizálási topológiák megterve
Az erőforrások üzembe helyezése előtt fontos, hogy megterveze, mit fog szinkronizálni egy helyi kiszolgálón, melyik Azure-fájlmegosztással. A csomag megkészítésével meghatározhatja, hogy hány tárfiókra, Azure-fájlmegosztásra és szinkronizálási erőforrásra lesz szüksége. Ezek a szempontok akkor is relevánsak, ha az adatok jelenleg nem Windows Serveren vagy a hosszú távú használatra kijelölt kiszolgálón találhatók. A [migrálási szakasz](#migration) segítségével meghatározhatja az adott helyzetnek megfelelő migrálási útvonalakat.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>A Windows-fájlkiszolgálóval kapcsolatos szempontok
A Windows Server szinkronizálási funkciójának engedélyezéséhez telepítenie kell a Azure File Sync ügynököt. A Azure File Sync-ügynök két fő összetevőt biztosít: a háttérbeli Windows-szolgáltatást, amely a kiszolgálóvégponton végrehajtott változások monitorozásáért és a szinkronizálási munkamenetek kezdeményezéséért felelős, valamint a fájlrendszerszűrőt, amely lehetővé teszi a felhőbeli rétegezést és a gyors `FileSyncSvc.exe` vészhelyreállítást. `StorageSync.sys`  

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények
Azure File Sync a Windows Server következő verziói támogatják:

| Verzió | Támogatott SKUS-k | Támogatott üzembe helyezési lehetőségek |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard és IoT | Teljes és Core |
| Windows Server 2016 | Adatközpont, Standard és Storage Server | Teljes és Core |
| Windows Server 2012 R2 | Adatközpont, Standard és Storage Server | Teljes és Core |

A Windows Server jövőbeli verziói a adva lesznek hozzáadva.

> [!Important]  
> Javasoljuk, hogy a Azure File Sync használt összes kiszolgálót mindig naprakészen tartsa a Windows Update. 

### <a name="minimum-system-resources"></a>Minimális rendszererőforrások
Azure File Sync fizikai vagy virtuális kiszolgálóra van szükség, legalább egy CPU-val és legalább 2 GiB memóriával.

> [!Important]  
> Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.

A legtöbb éles számítási feladat esetében nem javasoljuk, hogy csak a minimális követelményekkel konfiguráljon Azure File Sync szinkronizálási kiszolgálót. További [információ:](#recommended-system-resources) Ajánlott rendszererőforrások.

### <a name="recommended-system-resources"></a>Ajánlott rendszererőforrások
A kiszolgálói szolgáltatásokhoz és alkalmazásokhoz Azure File Sync az üzembe helyezés mérete határozza meg a rendszererőforrás-követelményeket; A kiszolgálón nagyobb üzemelő példányok nagyobb rendszererőforrásokat igényelnek. A Azure File Sync a méretezést a kiszolgálóvégponton található objektumok száma és az adatváltozás határozza meg. Egy kiszolgáló több szinkronizálási csoportban is lehet kiszolgálóvégponttal, és az alábbi táblázatban felsorolt objektumok száma a kiszolgálóhoz csatolt teljes névtérhez. 

Például az A kiszolgálóvégpont 10 millió objektummal és a B kiszolgálóvégponttal, 10 millió objektummal = 20 millió objektummal. Ebben a példában 8 PROCESSZORt, 16 GiB memóriát ajánlunk az állandó állapot érdekében, és (ha lehetséges) 48 GiB memóriát a kezdeti migráláshoz.
 
A névtéradatok teljesítménybeli okokból a memóriában vannak tárolva. Emiatt a nagyobb névterek több memóriát igényelnek a jó teljesítmény fenntartásához, a nagyobb adatváltozáshoz pedig több processzorra van szükség a feldolgozáshoz. 
 
Az alábbi táblázatban a névtér méretét és a tipikus általános célú fájlmegosztások kapacitására való átalakítást is megmérték, ahol az átlagos fájlméret 512 KiB. Ha a fájlméretek kisebbek, érdemes lehet további memóriát hozzáadni ugyanennek a kapacitásnak. A memóriakonfigurációt a névtér méretére alapozva.

| Névtér mérete – a & (millió)  | Jellemző kapacitás (TiB)  | PROCESSZORmagok  | Ajánlott memória (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (kezdeti szinkronizálás)/ 2 (jellemző adatváltozás)      |
| 5        | 2.3     | 2        | 16 (kezdeti szinkronizálás)/ 4 (jellemző adatváltozás)    |
| 10       | 4.7     | 4        | 32 (kezdeti szinkronizálás)/ 8 (jellemző adatváltozás)   |
| 30       | 14,0    | 8        | 48 (kezdeti szinkronizálás)/ 16 (jellemző adatváltozás)   |
| 50       | 23,3    | 16       | 64 (kezdeti szinkronizálás)/ 32 (jellemző adatváltozás)  |
| 100*     | 46.6    | 32       | 128 (kezdeti szinkronizálás)/ 32 (jellemző adatváltozás)  |

\*Jelenleg nem ajánlott 100 milliónál több fájlt & könyvtárakba szinkronizálni. Ez a tesztelt küszöbértékek alapján meghatározott soft limit. További információ: Azure Files [teljesítménycélok.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> A névtér kezdeti szinkronizálása intenzív művelet, ezért javasoljuk, hogy a kezdeti szinkronizálás befejezéséig foglaljon le több memóriát. Ez nem kötelező, de felgyorsíthatja a kezdeti szinkronizálást. 
> 
> A jellemző változás a névtér napi 0,5%-a. Magasabb szintű adatváltozás esetén érdemes lehet további processzort hozzáadni. 

- Helyileg csatlakoztatott, NTFS fájlrendszerrel formázott kötet.

### <a name="evaluation-cmdlet"></a>Kiértékelési parancsmag
A Azure File Sync előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync kiértékelési parancsmaggal. Ez a parancsmag ellenőrzi a fájlrendszerrel és az adatkészletkel kapcsolatos esetleges problémákat, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. Az ellenőrzések a legtöbb, de nem az alább említett funkciókat lefedik; Javasoljuk, hogy alaposan olvassa át a szakasz további részét, hogy az üzembe helyezés zökkenőmentes legyen. 

A kiértékelési parancsmag az Az PowerShell-modul telepítésével telepíthető, amely az alábbi utasítások szerint [telepíthető: Az](/powershell/azure/install-Az-ps)Azure PowerShell.

#### <a name="usage"></a>Használat  
A kiértékelési eszközt többféleképpen is meghívhatja: elvégezheti a rendszerellenőrzéseket, az adatkészlet-ellenőrzéseket, vagy mindkettőt. A rendszer- és az adatkészlet-ellenőrzések végrehajtása: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Csak az adatkészlet teszteléséhez:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Csak a rendszerkövetelmények teszteléséhez:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Az eredmények megjelenítése CSV-fájlban:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Fájlrendszer-kompatibilitás
Azure File Sync csak közvetlenül csatlakoztatott NTFS-köteteken támogatott. A Windows Serveren a közvetlenül csatlakoztatott tároló vagy DAS azt jelenti, hogy a fájlrendszer a Windows Server operációs rendszer tulajdonában van. A DAS a lemezek fájlkiszolgálóhoz való fizikai csatolásán, virtuális lemezek fájlkiszolgáló virtuális géphez (például Hyper-V által üzemeltetett virtuális géphez) vagy akár ISCSI-n keresztüli csatlakoztatásán keresztül is biztosítható.

Csak az NTFS-kötetek támogatottak; Az ReFS, FAT, FAT32 és más fájlrendszerek nem támogatottak.

Az alábbi táblázat az NTFS fájlrendszer szolgáltatásainak interop-állapotát mutatja be: 

| Szolgáltatás | Támogatás állapota | Jegyzetek |
|---------|----------------|-------|
| Hozzáférés-vezérlési lista (ACL-ek) | Teljes mértékben támogatott | A Windows-stílusú belátás szerinti hozzáférés-vezérlési listákat a Azure File Sync megőrzi, és a Windows Server kényszeríti a kiszolgálóvégponton. Az ACL-ek az Azure-fájlmegosztás közvetlen csatlakoztatásakor is kényszeríthetőek, ez azonban további konfigurációt igényel. További [információért tekintse meg az Identity](#identity) (Identitás) szakaszt. |
| Rögzített hivatkozások | Kihagyva | |
| Szimbolikus hivatkozások | Kihagyva | |
| Csatlakoztatási pontok | Részlegesen támogatott | A csatlakoztatási pontok lehetnek a kiszolgálóvégpontok gyökerei, de a rendszer kihagyja őket, ha egy kiszolgálóvégpont névterében vannak. |
| Csomópontok | Kihagyva | Például: DfrsrPrivate elosztott fájlrendszer DFSRoots mappák. |
| Újraelemzési pontok | Kihagyva | |
| NTFS-tömörítés | Teljes mértékben támogatott | |
| Ritka fájlok | Teljes mértékben támogatott | A ritka fájlok szinkronizálása (nincs letiltva), de teljes fájlként szinkronizálnak a felhőbe. Ha a fájl tartalma megváltozik a felhőben (vagy egy másik kiszolgálón), a fájl már nem ritka a módosítás letöltésekor. |
| Alternatív adatstreamek (ADS) | Megőrzött, de nincs szinkronizálva | A fájlbesorolási infrastruktúra által létrehozott besorolási címkék például nincsenek szinkronizálva. Az egyes kiszolgálóvégponton található fájlok meglévő besorolási címkéi érintetlenek maradnak. |

<a id="files-skipped"></a>Azure File Sync egyes ideiglenes fájlokat és rendszermappákat is kihagy:

| Fájl/mappa | Megjegyzés |
|-|-|
| pagefile.sys | Rendszerspecifikus fájl |
| Desktop.ini | Rendszerspecifikus fájl |
| Remek | Ideiglenes fájl a miniatűrök számára |
| ehthumbs.db | Ideiglenes fájl média-miniatűrök számára |
| ~$\*.\* | Office ideiglenes fájl |
| \*.tmp | Ideiglenes fájl |
| \*.laccdb | Adatbázis-zárolási fájl elérése|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Belső szinkronizálási fájl|
| \\Rendszerkötet adatai | Kötetspecifikus mappa |
| $RECYCLE. Bin| Mappa |
| \\SyncShareState | Szinkronizálási mappa |

### <a name="failover-clustering"></a>Feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatást Azure File Sync "Általános felhasználású fájlkiszolgáló" telepítési lehetőség támogatja. A feladatátvételi fürtszolgáltatás nem támogatott az "Kibővíthető fájlkiszolgáló alkalmazásadatokhoz" (SOFS) és a fürtözött megosztott kötetek (CSV-k) esetén.

> [!Note]  
> A Azure File Sync ügynöknek a feladatátvevő fürt minden csomópontján telepítve kell lennie, hogy a szinkronizálás megfelelően működjön.

### <a name="data-deduplication"></a>Adatdeduplikáció
**Windows Server 2016 és Windows Server 2019**   
Az adatdeduplikáció attól függetlenül támogatott, hogy a felhőbeli rétegezés engedélyezve van-e vagy le van-e tiltva a Windows Server 2016 és a Windows Server 2019 kötetének egy vagy több kiszolgálóvégpontjában. Ha engedélyezi az adatdeduplikációt egy olyan köteten, amelynél engedélyezve van a felhőbeli rétegezés, több fájl gyorsítótárazható a helyszínen anélkül, hogy további tárhelyet létesítenének. 

Ha az Adatdeduplikáció engedélyezve van egy olyan köteten, ahol engedélyezve van a felhőbeli rétegezés, a deduplikált fájlok a kiszolgálóvégpont helye alatt a felhőbeli rétegezés házirend-beállításai alapján a normál fájlokhoz hasonlóan lesznek rétegzve. Miután a deduplikált fájlok rétegzve vannak, az adatdeduplikáció szemétgyűjtési feladata automatikusan lefut a lemezterület felszabadítása érdekében a köteten lévő többi fájl által már nem hivatkozott felesleges adattömbök eltávolításával.

Vegye figyelembe, hogy a kötetmegtakarítások csak a kiszolgálóra vonatkoznak; az Azure-fájlmegosztásban található adatok nem dedupl lesznek deduplánsak.

> [!Note]  
> Az adatdeduplikáció Windows Server 201 Azure File Sync 9-en engedélyezett felhőbeli rétegezéssel rendelkező köteten való támogatásához a [KB4520062 – 2019.](https://support.microsoft.com/help/4520062) október vagy újabb, havi összesítési frissítést kell telepíteni, és egy 12.0.0.0-s vagy újabb verziójú ügynökre van szükség.

**Windows Server 2012 R2**  
Azure File Sync nem támogatja az adatdeduplikációt és a felhőbeli rétegezést ugyanazon a köteten a Windows Server 2012 R2 rendszeren. Ha az Adatdeduplikáció engedélyezve van egy köteten, a felhőbeli rétegezést le kell tiltani. 

**Megjegyzések**
- Ha az adatdeduplikáció az Azure File Sync-ügynök telepítése előtt van telepítve, újraindítás szükséges az adatdeduplikáció és a felhőbeli rétegezés támogatásához ugyanazon a köteten.
- Ha az Adatdeduplikáció engedélyezve van egy köteten a felhőbeli rétegezés engedélyezése után, a kezdeti deduplikációoptimalizálási feladat optimalizálja a még nem rétegzett köteten lévő fájlokat, és a következő hatással lesz a felhőbeli rétegezésre:
    - A szabad terület szabályzata továbbra is rétegzett fájlokat fog tartalmazni a kötet szabad területének alapján az hőtérkép használatával.
    - A dátum szabályzat kihagyja az olyan fájlok rétegezését, amelyek egyébként jogosultak voltak a rétegezésre, mert a deduplikáció optimalizálási feladata hozzáfért a fájlokhoz.
- A folyamatban lévő deduplikációoptimalizálási feladatok esetében a dátum szabályzattal a felhőbeli rétegezést késlelteti az Adatdeduplikáció [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume) beállítása, ha a fájl még nincs rétegzve. 
    - Példa: Ha a MinimumFileAgeDays beállítás hét nap, a felhőbeli rétegezés dátumának szabályzata pedig 30 nap, akkor a dátum házirend 37 nap után rétege fogja tartalmazni a fájlokat.
    - Megjegyzés: Miután egy fájl rétegzett egy Azure File Sync, a deduplikáció optimalizálási feladata kihagyja a fájlt.
- Ha egy Windows Server 2012 R2 rendszert futtató kiszolgáló, amelyre telepítve van a Azure File Sync-ügynök, Windows Server 2016-ra vagy Windows Server 2019-re van frissítve, a következő lépéseket kell végrehajtani az adatdeduplikáció és a felhőrétegezés támogatásához ugyanazon a köteten:  
    - Távolítsa el a Azure File Sync a Windows Server 2012 R2-höz, és indítsa újra a kiszolgálót.
    - Töltse le Azure File Sync-ügynököt az új kiszolgálói operációs rendszer verziójához (Windows Server 2016 vagy Windows Server 2019).
    - Telepítse a Azure File Sync, majd indítsa újra a kiszolgálót.  
    
    Megjegyzés: a Azure File Sync konfigurációs beállításai megmaradnak az ügynök eltávolításakor és újratelepítésekor.

### <a name="distributed-file-system-dfs"></a>elosztott fájlrendszer (DFS)
Azure File Sync támogatja az elosztott fájlrendszerbeli névterekkel (DFS-N) és a Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) való összekapcsolásokat.

**Elosztott fájlrendszerbeli névterek (DFS-N):** Azure File Sync DFS-N kiszolgálókon teljes mértékben támogatott. A Azure File Sync egy vagy több DFS-N tagra is telepítheti, hogy szinkronizálja az adatokat a kiszolgálóvégpont és a felhőbeli végpont között. További információ: [Elosztott fájlrendszerbeli névterek áttekintése.](/windows-server/storage/dfs-namespaces/dfs-overview)
 
**Elosztott fájlrendszer replikációs szolgáltatása (DFS-R):** Mivel a DFS-R és a Azure File Sync egyaránt replikációs megoldás, a legtöbb esetben javasoljuk, hogy cserélje le a DFS-R-t Azure File Sync. A DFS-R-t és a DFS-R-t több forgatókönyv is Azure File Sync együtt használni:

- Egy DFS-R üzemelő példányról egy központi telepítésre Azure File Sync áttelepítést. További információ: [Elosztott fájlrendszer replikációs szolgáltatása (DFS-R)](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)telepítése a Azure File Sync.
- Nem lehet közvetlenül az internethez csatlakoztatni minden olyan helyszíni kiszolgálót, amely a fájladatok másolatát szükséges.
- Az ágkiszolgálók egyetlen központi kiszolgálón egyesítik az adatokat, amelyekhez az adatokat Azure File Sync.

A Azure File Sync DFS-R és a DFS-R egymás mellett való munkához:

1. Azure File Sync felhőbeli rétegezést le kell tiltani az elosztott fájlrendszer-replikációs szolgáltatás replikált mappáit tartalmazó kötetek esetében.
2. A kiszolgálóvégpontokat nem szabad konfigurálni a csak olvasható DFS-R replikációs mappákban.

További információkért lásd a Elosztott fájlrendszer replikációs szolgáltatása [áttekintését.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11))

### <a name="sysprep"></a>Sysprep
A sysprep nem támogatott olyan kiszolgálón, Azure File Sync ügynök telepítve van, és váratlan eredményekhez vezethet. Az ügynöktelepítésnek és a kiszolgálóregisztrációnak a kiszolgálói lemezkép telepítése és a sysprep minibeállítás befejezése után kell befejeződni.

### <a name="windows-search"></a>Windows Search
Ha a felhőbeli rétegezés engedélyezve van egy kiszolgálóvégponton, a rendszer kihagyja a rétegzett fájlokat, és nem indexeli őket a Windows Search. A nem rétegzett fájlok indexelése megfelelő.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolókezelési (HSM) megoldások
Más HSM-megoldás nem használható Azure File Sync.

## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Mivel a Azure File Sync-ügynök az Azure-fájlmegosztáshoz csatlakozó Windows Server-gépen fut, a hatékony szinkronizálási teljesítmény az infrastruktúrában számos tényezőtől függ: a Windows Server és a mögöttes lemezkonfiguráció, a kiszolgáló és az Azure Storage közötti hálózati sávszélességtől, a fájlmérettől, az adatkészlet teljes méretétől és az adatkészleten végzett tevékenységtől. Mivel Azure File Sync fájlszinten működik, a Azure File Sync-alapú megoldások teljesítményjellemzőit jobban mérik a másodpercenként feldolgozott objektumok (fájlok és könyvtárak) számában.

Az Azure-fájlmegosztáson az Azure Portal vagy SMB használatával végrehajtott módosításokat a rendszer nem észleli és replikálja azonnal, például a kiszolgálóvégpont módosításait. Azure Files még nem rendelkezik módosítási értesítésekkel vagy naplózással, így nem lehet automatikusan szinkronizálási munkamenetet kezdeményezni fájlok módosításakor. Windows Serveren a Azure File Sync [Windows USN-naplózást](/windows/win32/fileio/change-journals) használ a szinkronizálási munkamenet automatikus kezdeményezéséhez fájlok módosításakor

Az Azure-fájlmegosztás változásainak észleléséhez Azure File Sync egy módosításészlelési feladat nevű ütemezett feladat. A módosításészlelési feladat számba veszi a fájlmegosztásban található összes fájlt, majd összehasonlítja azt a fájl szinkronizálási verziójával. Amikor a módosításészlelési feladat megállapítja, hogy a fájlok megváltoztak, a Azure File Sync kezdeményez szinkronizálási munkamenetet. A módosításészlelési feladat 24 óránként indul el. Mivel a módosításészlelési feladat az Azure-fájlmegosztásban található összes fájl számbavétele miatt a módosításészlelés hosszabb időt vesz igénybe nagyobb névterek között, mint a kisebb névterekben. Nagyméretű névtereknél 24 óránként több időt is igénybe vehet annak meghatározása, hogy mely fájlok módosulnak.

További információ: teljesítménymetrikák [Azure File Sync méretezési](storage-files-scale-targets.md#azure-file-sync-performance-metrics) [Azure File Sync skálázása](storage-files-scale-targets.md#azure-file-sync-scale-targets)

## <a name="identity"></a>Identitás
Azure File Sync AD-alapú identitással a szinkronizálás beállításán túl speciális beállítások nélkül is együttműködik. A virtuális Azure File Sync általános elvárás az, hogy a legtöbb hozzáférés a Azure File Sync gyorsítótárazó kiszolgálókon, és nem az Azure-fájlmegosztáson keresztül történik. Mivel a kiszolgálói végpontok a Windows Serveren találhatók, és a Windows Server már régóta támogatja az AD- és Windows-stílusú ACL-eket, nincs másra szükség, mint biztosítani, hogy a Társzinkronizálási szolgáltatásban regisztrált Windows-fájlkiszolgálók tartományhoz legyenek csatlakozva. Azure File Sync ACL-eket fog tárolni az Azure-fájlmegosztásban lévő fájlokon, és replikálja őket az összes kiszolgálóvégpontra.

Annak ellenére, hogy a közvetlenül az Azure-fájlmegosztáson végrehajtott módosítások szinkronizálása hosszabb időt fog igénybe venni a szinkronizálási csoportban található kiszolgálóvégpontokkal, előfordulhat, hogy az AD-engedélyeket közvetlenül a felhőben is kikényszeríteni tudja a fájlmegosztáson. Ehhez tartományhoz kell csatlakoznia a tárfiókhoz a helyszíni AD-hez, a Windows-fájlkiszolgálók tartományhoz való csatlakozásának mikéntjéhez hasonlóan. Ha többet szeretne megtudni arról, hogy a tartomány hogyan csatlakozik a tárfiókhoz egy ügyfél tulajdonában lévő Active Directory, tekintse meg a [Azure Files Active Directory áttekintését.](storage-files-active-directory-overview.md)

> [!Important]  
> A tárfiókhoz a Active Directory való csatlakozása nem szükséges a tárfiók sikeres Azure File Sync. Ez egy szigorúan nem kötelező lépés, amely lehetővé teszi az Azure-fájlmegosztás számára a helyszíni ACL-ek kényszerítét, amikor a felhasználók közvetlenül csatlakoztatják az Azure-fájlmegosztást.

## <a name="networking"></a>Hálózatkezelés
A Azure File Sync-ügynök a Azure File Sync REST protokoll és a FileREST protokoll használatával kommunikál a Storage Sync Service-sel és az Azure-fájlmegosztással, és mindkettő mindig HTTPS-t használ a 443-as porton keresztül. Az SMB soha nem használatos adatok feltöltésére vagy letöltésére a Windows Server és az Azure-fájlmegosztás között. Mivel a legtöbb szervezet engedélyezi a HTTPS-forgalmat a 443-as porton keresztül, a legtöbb webhely felkereséséhez szükséges követelmény, a hálózati konfigurációk telepítése általában nem Azure File Sync.

A szervezet szabályzata vagy egyedi szabályozási követelményei alapján szigorúbb kommunikációra lehet szükség az Azure-ral, ezért a Azure File Sync számos hálózati mechanizmust kínál. A követelmények alapján a következőre van képes:

- Alagútszinkronizálás és fájlfeltöltési/-letöltési forgalom ExpressRoute- vagy Azure VPN-en keresztül. 
- Használja ki a Azure Files és Azure-hálózatkezelés, például a szolgáltatásvégpontokat és a privát végpontokat.
- Konfigurálja Azure File Sync, hogy támogassa a proxyt a környezetében.
- Hálózati tevékenység Azure File Sync.

A hálózati Azure File Sync és hálózattal kapcsolatos további információkért tekintse meg a hálózati [Azure File Sync kapcsolatos további tudnivalókat.](storage-sync-files-networking-overview.md)

## <a name="encryption"></a>Titkosítás
A Azure File Sync használata esetén három különböző titkosítási réteget kell figyelembe venni: titkosítást a Windows Serveren, az Azure File Sync-ügynök és az Azure közötti átvitel során, valamint az adatok többi részét az Azure-fájlmegosztásban. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server titkosítása 
A Windows Serveren az adatok titkosításának két stratégiája működik általában a Azure File Sync-rel: a fájlrendszer alatti titkosítás, hogy a fájlrendszer és az összes rá írt adat titkosítva, a titkosítás pedig magában a fájlformátumban történik. Ezek a metódusok nem zárja ki egymást; szükség esetén együtt is használhatók, mivel a titkosítás célja eltérő.

A fájlrendszer alatti titkosításhoz a Windows Server biztosítja a BitLocker-postaládát. A BitLocker teljesen transzparens a Azure File Sync. A BitLockerhez hasonló titkosítási mechanizmus használatának elsődleges célja, hogy megakadályozza az adatok fizikai kiszivárgását a helyszíni adatközpontból azáltal, hogy valaki ellopja a lemezeket, és meggátolja, hogy jogosulatlan operációs rendszerekkel illetéktelen olvasási/írási műveleteket hajtson végre az adatokon. További információ a BitLockerről: [A BitLocker áttekintése.](/windows/security/information-protection/bitlocker/bitlocker-overview)

A BitLockerhez hasonlóan működő, harmadik féltől származó termékeknek, amelyek az NTFS-kötet alatt találhatóak, hasonlóan teljes mértékben transzparens módon működnek a Azure File Sync. 

Az adatok titkosításának másik fő módja a fájl adatfolyamának titkosítása, amikor az alkalmazás menti a fájlt. Egyes alkalmazások natív módon ezt is meg is okozhatják, de ez általában nem így van. A fájl adatfolyamának titkosítására jó példa az Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Az olyan titkosítási mechanizmusok használatának elsődleges célja, mint az AIP/RMS, hogy megakadályozza az adatok kiszivárgását a fájlmegosztásból azáltal, hogy a fájlokat más helyre, például flash meghajtóra másolják, vagy jogosulatlan személynek küldik el e-mailben. Ha egy fájl adatfolyama a fájlformátum részeként van titkosítva, ez a fájl továbbra is titkosítva lesz az Azure-fájlmegosztáson. 

Azure File Sync nem működik együtt az NTFS titkosított fájlrendszerrel (NTFS EFS) vagy a fájlrendszer fölött, de a fájl adatfolyama alatt található, harmadik féltől származó titkosítási megoldásokkal. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

> [!NOTE]
> Azure File Sync szolgáltatás 2020. augusztus 1-én megszünteti a TLS 1.0 és 1.1 támogatását. A támogatott Azure File Sync verziója alapértelmezés szerint már használja a TLS1.2-t. A TLS korábbi verziójának használata akkor fordulhat elő, ha a TLS1.2 le lett tiltva a kiszolgálón, vagy proxyt használ. Ha proxyt használ, javasoljuk, hogy ellenőrizze a proxy konfigurációját. Azure File Sync 2020. augusztus 1. után hozzáadott Azure File Sync szolgáltatási régiók csak a TLS 1.2-t támogatják, és a TLS1.0 és 1.1 támogatása 2020. augusztus 1-jére el lesz távolítva a meglévő régiókból.  További információért tekintse meg a [hibaelhárítási útmutatót.](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)

Azure File Sync ügynök a Azure File Sync REST protokoll és a FileREST protokoll használatával kommunikál a Storage Sync Service-sel és az Azure-fájlmegosztással, és mindkettő mindig HTTPS-t használ a 443-as porton keresztül. Azure File Sync nem küld titkosítatlan kéréseket HTTP-protokollon keresztül. 

Az Azure Storage-fiókok tartalmaznak egy kapcsolót, amely megköveteli a titkosítást az átvitel során, ami alapértelmezés szerint engedélyezve van. Még ha a tárfiók szintjén le is van tiltva a kapcsoló, ami azt jelenti, hogy az Azure-fájlmegosztások titkosítatlan kapcsolatai lehetségesek, az Azure File Sync továbbra is csak titkosított csatornákat használ a fájlmegosztás eléréséhez.

A tárfiók átvitel közbeni titkosításának elsődleges oka egy régebbi alkalmazás támogatása, amely egy régebbi operációs rendszeren, például Windows Server 2008 R2 vagy régebbi Linux-disztribúción fut, és közvetlenül egy Azure-fájlmegosztással beszélget. Ha az örökölt alkalmazás a fájlmegosztás Windows Server-gyorsítótárával folytatja a beszédét, ennek a beállításnak a váltása nem lesz hatással. 

Határozottan javasoljuk, hogy az átvitel közbeni adatok titkosítása engedélyezve legyen.

További információ az átvitel közbeni titkosításról: Biztonságos [átvitel megkövetelve az Azure Storage-ban.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-file-share-encryption-at-rest"></a>Azure-fájlmegosztás titkosítása az azure-ban
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Tárolási szintek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standard fájlmegosztások engedélyezése legfeljebb 100 TiB-ra
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionális elérhetőség
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Az Azure File Sync-régió rendelkezésre állása

A régiónkénti rendelkezésre állásért lásd: [Régiónként elérhető termékek.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

A következő régiók megkövetelik, hogy hozzáférést kérjen az Azure Storage-hoz, mielőtt Azure File Sync használni őket:

- Dél-Franciaország
- Dél-Afrika nyugati régiója
- Egyesült Arab Emírségek középső

A régiókhoz való hozzáférés kéréséhez kövesse az ebben a dokumentumban [található folyamatot.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> A georedundáns és a geozónaredundáns tárolás képes manuális feladatátvételt elvégezni a másodlagos régióba. Javasoljuk, hogy ezt ne vészhelyreállításon kívül tegye, ha Azure File Sync adatvesztés nagyobb valószínűsége miatt. Olyan katasztrófa esetén, amikor manuális feladatátvételt szeretne kezdeményezni a tárterületen, meg kell nyitnia egy támogatási esetet a Microsoftnál, hogy a Azure File Sync folytatja a szinkronizálást a másodlagos végponttal.

## <a name="migration"></a>Áttelepítés
Ha már van 2012R2-es vagy újabb Windows-fájlkiszolgálója, a Azure File Sync közvetlenül a helyére is telepíthető anélkül, hogy új kiszolgálóra kellene áthelyezni az adatokat. Ha az Azure File Sync használatának részeként új Windows-fájlkiszolgálóra tervez áttelepítést, vagy ha az adatok jelenleg hálózati tárolón (NAS) találhatók, az Azure File Sync-t többféle módon is lehet migrálni. A választott migrálási módszer attól függ, hogy az adatok jelenleg hol találhatók. 

Tekintse meg a Azure File Sync azure-fájlmegosztások migrálását áttekintő cikket, amelyben részletes útmutatást talál a forgatókönyvéhez. [](storage-files-migration-overview.md)

## <a name="antivirus"></a>Vírusvédelem
Mivel a víruskereső úgy működik, hogy ismert kártevő kódot keres a fájlokban, egy víruskereső termék rétegzett fájlok visszahívást okozhat, ami magas bejövő forgalommal kapcsolatos költségeket eredményezhet. A Azure File Sync ügynök 4.0-s és újabb verzióiban a rétegzett fájlok biztonságos Windows-FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS vannak beállítva. Javasoljuk, hogy a szoftver gyártójával egyeztetve ismerje meg, hogyan konfigurálhatja úgy a megoldását, hogy kihagyja az ilyen attribútumkészletben található fájlok olvasását (sok automatikusan megtörténik). 

A Microsoft saját vírusvédelmi megoldásai, a Windows Defender és System Center Endpoint Protection (SCEP) egyaránt automatikusan kihagyják az ilyen attribútummal rendelkező fájlok olvasását. Teszteltük őket, és azonosítottunk egy kisebb problémát: amikor hozzáad egy kiszolgálót egy meglévő szinkronizálási csoporthoz, a rendszer a 800 bájtnál kisebb fájlokat visszahívja (letölti) az új kiszolgálón. Ezek a fájlok az új kiszolgálón maradnak, és nem lesznek rétegzettek, mivel nem felelnie meg a rétegezés méretkövetelményének (>64 kb).

> [!Note]  
> A víruskeresők a Microsoft letöltőközpontból letölthető [Azure File Sync Víruskereső](https://www.microsoft.com/download/details.aspx?id=58322)kompatibilitási tesztcsomag Azure File Sync ellenőrizhetik a termékük és az alkalmazásuk kompatibilitását.

## <a name="backup"></a>Backup 
Ha a felhőbeli rétegezés engedélyezve van, nem használhatók olyan megoldások, amelyek közvetlenül a kiszolgálóvégpontról vagy a kiszolgálói végpontot kiszolgálóvégpontot kiszolgálóvégponttal rendelkező virtuális gépről biztonságimentik. A felhőbeli rétegezés hatására az adatoknak csak egy részkészlete lesz tárolva a kiszolgálóvégponton, és a teljes adatkészlet az Azure-fájlmegosztásban található. A használt biztonsági mentési megoldástól függően a rétegzett fájlokat a rendszer kihagyja, és nem készít biztonsági másolatot (mert az FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútumkészlettel vannak beállítva), vagy a lemezre fogja őket visszahívni, ami magas kiesési díjakat eredményez. Javasoljuk, hogy az Azure-fájlmegosztás közvetlen biztonsági mentéséhez egy felhőalapú biztonsági mentési megoldást használ. További információ: [](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) Az Azure-fájlmegosztások biztonsági mentése, vagy lépjen kapcsolatba a biztonsági mentési szolgáltatóval, és nézze meg, hogy támogatják-e az Azure-fájlmegosztások biztonsági mentését.

Ha inkább helyszíni biztonsági mentési megoldást szeretne használni, a biztonsági mentéseket a szinkronizálási csoport egy olyan kiszolgálójának kell elvégeznie, amely esetében a felhőbeli rétegezés le van tiltva. Visszaállításkor használja a kötet- vagy fájlszintű visszaállítási beállításokat. A fájlszintű visszaállítási beállítással visszaállított fájlok szinkronizálva lesznek a szinkronizálási csoport összes végpontjára, a meglévő fájlokat pedig a biztonsági másolatból visszaállított verzióra cseréli a rendszer.  A kötetszintű visszaállítás nem helyettesíti az Azure-fájlmegosztásban vagy más kiszolgálóvégpontban található újabb fájlverziókat.

> [!WARNING]
> Ha a Robocopy /B ügynököt egy forrás- vagy célkiszolgálón futó Azure File Sync-ügynökkel kell használnia, frissítsen az Azure File Sync 12.0-s vagy újabb verziójára. A Robocopy /B 12.0-s verziónál régebbi ügynökverzióval való használata a rétegzett fájlok sérüléséhez vezet a másolás során.

> [!Note]  
> Az operációs rendszer nélküli (BMR) visszaállítás váratlan eredményeket okozhat, és jelenleg nem támogatott.

> [!Note]  
> A Azure File Sync ügynök 9-es verziójával a VSS-pillanatképek (beleértve az Előző verziók lapot is) már támogatottak az olyan kötetek esetében, amelyeken engedélyezve van a felhőbeli rétegezés. Azonban engedélyeznie kell a korábbi verziók kompatibilitását a PowerShell használatával. [További információ:](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-classification"></a>Adatbesorolás
Ha adatbesorolási szoftver van telepítve, a felhőbeli rétegezés engedélyezése két okból is megnövekedett költségeket eredményezhet:

1. Ha engedélyezve van a felhőbeli rétegezés, a legkedveltebb fájlok helyileg vannak gyorsítótárazva, a leghűbb fájlok pedig a felhőben lévő Azure-fájlmegosztásba vannak rétegazva. Ha az adatbesorolás rendszeresen átvizsgálja a fájlmegosztás összes fájlját, a felhőbe rétegzett fájlokat minden vizsgálatkor vissza kell idézni. 

2. Ha az adatbesorolási szoftver egy fájl adatfolyamában található metaadatokat használja, a fájlt teljesen vissza kell idézni ahhoz, hogy a szoftver számára látható legyen a besorolás. 

Ezek a visszahívások számának és a visszahívott adatok mennyiségének növekedése is megnövelheti a költségeket.

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Következő lépések
* [Fontolja meg a tűzfal- és proxybeállítások használatát](storage-sync-files-firewall-and-proxy.md)
* [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
* [Az Azure Files üzembe helyezése](./storage-how-to-create-file-share.md)
* [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
* [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
