---
title: Azure File Sync központi telepítésének tervezése | Microsoft Docs
description: Tervezze meg a Azure File Sync-vel történő telepítést, amely lehetővé teszi egy számú Azure-fájlmegosztás gyorsítótárba helyezését egy helyszíni Windows Server-vagy felhőalapú virtuális gépen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 28b7419dfc889d1149765b884f1a5990a1e28f03
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898104"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Az Azure File Sync üzembe helyezésének megtervezése

:::row:::
    :::column:::
        [![Interjú és bemutató bemutató Azure File Sync – kattintson a lejátszáshoz!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync egy olyan szolgáltatás, amely lehetővé teszi számos Azure-fájlmegosztás gyorsítótárazását egy helyszíni Windows Server-vagy felhőalapú virtuális gépen. 
        
        Ez a cikk bemutatja, hogyan Azure File Sync fogalmakat és szolgáltatásokat. Ha már ismeri a Azure File Synct, vegye figyelembe a [Azure file Sync üzembe helyezési útmutatót](storage-sync-files-deployment-guide.md) a szolgáltatás kipróbálásához.        
    :::column-end:::
:::row-end:::

A fájlok tárolása a felhőben történik az [Azure-fájlmegosztás](storage-files-introduction.md)során. Az Azure-fájlmegosztás kétféleképpen használható: a kiszolgáló nélküli Azure-fájlmegosztás (SMB) közvetlen csatlakoztatásával vagy az Azure-fájlmegosztás helyszíni gyorsítótárazásával Azure File Sync használatával. Melyik központi telepítési beállítással kell megadnia, hogy milyen szempontokat kell figyelembe vennie az üzemelő példány tervezésekor. 

- **Azure-fájlmegosztás közvetlen csatlakoztatása**: mivel Azure Files az SMB-hozzáférést biztosít, a Windows, MacOS és Linux rendszeren elérhető szabványos SMB-ügyféllel csatlakoztathatja az Azure-fájlmegosztást a helyszínen vagy a felhőben. Mivel az Azure-fájlmegosztás kiszolgáló nélküli, az éles környezetekben való üzembe helyezéshez nem szükséges fájlkiszolgáló vagy NAS-eszköz kezelése. Ez azt jelenti, hogy nem kell szoftverfrissítéseket alkalmaznia vagy fizikai lemezeket cserélnie. 

- **A helyszíni Azure-fájlmegosztás gyorsítótárazása a Azure file Sync** használatával: a Azure file Sync lehetővé teszi a szervezete fájlmegosztás megszervezését Azure Filesban, miközben megtartja a helyszíni fájlkiszolgáló rugalmasságát, teljesítményét és kompatibilitását. Azure File Sync átalakítja a helyszíni (vagy Felhőbeli) Windows Servert az Azure-fájlmegosztás gyors gyorsítótárba. 

## <a name="management-concepts"></a>Felügyeleti fogalmak
Egy Azure File Sync üzemelő példány három alapvető felügyeleti objektummal rendelkezik:

- **Azure-fájlmegosztás**: az Azure-fájlmegosztás egy kiszolgáló nélküli felhőalapú fájlmegosztás, amely egy Azure file Sync szinkronizálási kapcsolat *Felhőbeli végpontját* adja meg. Az Azure-fájlmegosztás fájljai közvetlenül is elérhetők az SMB-vel vagy a kiszolgált protokollal, de javasoljuk, hogy elsősorban a Windows Server-gyorsítótáron keresztül férjen hozzá a fájlokhoz, ha az Azure-fájlmegosztás Azure File Sync használatával van használatban. Ennek az az oka, hogy Azure Files ma nem rendelkezik olyan hatékony változás-észlelési mechanizmussal, mint a Windows Server, ezért az Azure-fájlmegosztás közvetlen módosítása időt vesz igénybe, hogy a kiszolgáló-végpontokra terjesszen vissza.
- **Kiszolgálói végpont**: az Azure-fájlmegosztás felé szinkronizált Windows Server elérési útja. Ez lehet egy adott kötet vagy a kötet gyökerének egy adott mappája. Ugyanazon a köteten több kiszolgálói végpont is létezhet, ha a névterek nem fedik át egymást.
- **Szinkronizálási csoport**: a **Felhőbeli végpont** vagy az Azure-fájlmegosztás, valamint egy kiszolgálói végpont közötti szinkronizálási kapcsolatot meghatározó objektum. A szinkronizálási csoporton belüli végpontokat a rendszer szinkronban tartja egymással. Ha például két különálló fájllal szeretne felügyelni Azure File Sync, két szinkronizálási csoportot kell létrehoznia, és különböző végpontokat kell hozzáadnia az egyes szinkronizálási csoportokhoz.

### <a name="azure-file-share-management-concepts"></a>Az Azure fájlmegosztás-felügyeleti fogalmak
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync felügyeleti fogalmak
A szinkronizálási csoportok a **Storage Sync Services szolgáltatásba** vannak telepítve, amelyek olyan legfelső szintű objektumok, amelyek a kiszolgálókat regisztrálják Azure file Sync és tartalmazzák a szinkronizálási csoport kapcsolatait. A Storage Sync szolgáltatás erőforrása a Storage-fiók erőforrásának egyik társa, és hasonló módon telepíthető Azure-erőforráscsoportok számára is. A Storage Sync szolgáltatás olyan szinkronizálási csoportokat hozhat létre, amelyek több Storage-fiókban és több regisztrált Windows-kiszolgálón is tartalmaznak Azure-fájlmegosztást.

Ahhoz, hogy szinkronizált csoportot hozzon létre egy Storage Sync szolgáltatásban, először regisztrálnia kell egy Windows-kiszolgálót a Storage Sync szolgáltatással. Ez létrehoz egy **regisztrált kiszolgáló** objektumot, amely megbízhatósági kapcsolatot képvisel a kiszolgáló vagy a fürt és a Storage Sync szolgáltatás között. A Storage Sync szolgáltatás regisztrálásához először telepítenie kell a Azure File Sync ügynököt a kiszolgálóra. Egy adott kiszolgáló vagy fürt egyszerre csak egy Storage Sync szolgáltatással regisztrálható.

A szinkronizálási csoport egy Felhőbeli végpontot, vagy egy Azure-fájlmegosztást, valamint legalább egy kiszolgálói végpontot tartalmaz. A kiszolgálói végpont objektum azokat a beállításokat tartalmazza, amelyek az Azure File Sync gyorsítótárazási funkcióját biztosítják a **felhő** -előállítók számára. Az Azure-fájlmegosztás használatával történő szinkronizáláshoz az Azure-fájlmegosztást tartalmazó Storage-fióknak ugyanabban az Azure-régióban kell lennie, mint a Storage Sync szolgáltatásnak.

> [!Important]  
> A szinkronizálási csoportban bármilyen Felhőbeli végpontot vagy kiszolgálói végpontot módosíthat, és a fájlok szinkronizálva vannak a szinkronizálási csoport többi végpontján. Ha közvetlenül módosítja a Felhőbeli végpontot (Azure-fájlmegosztás), a módosításokat először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A változás-észlelési feladatok csak 24 óránként egyszer indíthatók el egy Felhőbeli végponton. További információ: [Azure Files gyakori kérdések](storage-files-faq.md#afs-change-detection).

### <a name="management-guidance"></a>Kezelési útmutató
Azure File Sync telepítésekor a következőket javasoljuk:

- Azure-fájlmegosztás 1:1-es telepítése Windows-fájlmegosztás esetén. A kiszolgálói végpont objektum nagyszerű rugalmasságot biztosít a szinkronizálási topológia beállításához a szinkronizálási kapcsolat kiszolgálóoldali oldalán. A felügyelet egyszerűbbé tételéhez adja meg a kiszolgálói végpont elérési útját a Windows-fájlmegosztás elérési útjának megfelelően. 

- A lehető legkevesebb tárolási szinkronizációs szolgáltatást használja. Ez leegyszerűsíti a felügyeletet, ha olyan szinkronizálási csoportokkal rendelkezik, amelyek több kiszolgálói végpontot tartalmaznak, mivel a Windows Servert egyszerre csak egy Storage Sync szolgáltatásban lehet regisztrálni. 

- Ügyeljen arra, hogy a Storage-fiók IOPS korlátozásait az Azure-fájlmegosztás telepítésekor. Ideális esetben a Storage-fiókokkal rendelkező 1:1-es fájlmegosztás leképezhető, azonban a különböző korlátok és korlátozások miatt a szervezettől és az Azure-tól is nem mindig lehetséges. Ha egyetlen Storage-fiókban nem lehet egyetlen fájlmegosztást telepíteni, gondolja át, hogy mely megosztások lesznek aktívak, és hogy mely megosztások kevésbé lesznek aktívak, hogy a legforróbb fájlmegosztás ne legyen ugyanazon a Storage-fiókban.

## <a name="windows-file-server-considerations"></a>Windows-fájlkiszolgáló szempontjai
A Windows Server szinkronizálási funkciójának engedélyezéséhez telepítenie kell a Azure File Sync letölthető ügynököt. A Azure File Sync ügynök két fő összetevőt biztosít: `FileSyncSvc.exe` a háttérben futó Windows-szolgáltatást, amely a kiszolgáló-végpontok változásainak figyelésére és a szinkronizálási munkamenetek kezdeményezésére, valamint `StorageSync.sys` a felhőalapú rétegek és a gyors vész-helyreállítást lehetővé tevő fájlrendszer-szűrőre szolgál.  

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények
A Azure File Sync a Windows Server következő verzióiban támogatott:

| Verzió | Támogatott SKU-i | Támogatott üzembe helyezési lehetőségek |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, standard és IoT | Teljes és alapvető |
| Windows Server 2016 | Datacenter, standard és Storage Server | Teljes és alapvető |
| Windows Server 2012 R2 | Datacenter, standard és Storage Server | Teljes és alapvető |

A rendszer a Windows Server jövőbeli verzióit adja hozzá a kiadásuk után.

> [!Important]  
> Azt javasoljuk, hogy az összes olyan kiszolgálót, amelyet a Azure File Sync naprakészen használ, Windows Update legújabb frissítéseivel. 

### <a name="minimum-system-resources"></a>Minimális rendszererőforrások
A Azure File Synchoz fizikai vagy virtuális kiszolgáló szükséges, legalább egy PROCESSZORral és legalább 2 GiB memóriával.

> [!Important]  
> Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.

A legtöbb éles számítási feladathoz nem ajánlott olyan Azure File Sync szinkronizáló kiszolgálót konfigurálni, amely kizárólag a minimális követelményekkel rendelkezik. További információért lásd: [ajánlott rendszererőforrások](#recommended-system-resources) .

### <a name="recommended-system-resources"></a>Ajánlott rendszererőforrások
A kiszolgálói szolgáltatásokhoz vagy alkalmazásokhoz hasonlóan a Azure File Sync rendszererőforrás-követelményeit az üzemelő példány skálázása határozza meg; a kiszolgálók nagyobb központi telepítései nagyobb rendszererőforrást igényelnek. Azure File Sync esetében a méretezést a kiszolgálói végpontok és az adatkészletben lévő objektumok száma határozza meg. Egyetlen kiszolgáló több szinkronizálási csoportban is rendelkezhet kiszolgálói végpontokkal, valamint a következő táblázatban szereplő objektumok számával ahhoz a teljes névtérhez, amelyhez a kiszolgáló csatlakozik. 

Például az A kiszolgáló-végpont, 10 000 000 objektum + kiszolgáló-végpont B, 10 000 000 objektum = 20 000 000 objektummal. Ebben a példában az üzembe helyezéshez 8 processzor, 16 GiB memória szükséges a stabil állapothoz, és (ha lehetséges) 48 GiB memória a kezdeti áttelepítéshez.
 
A névtér adatai a memóriában vannak tárolva teljesítménybeli okokból. Ennek következtében a nagyobb méretű névterek több memóriát igényelnek a jó teljesítmény fenntartása érdekében, és több adatforgalom szükséges a PROCESSZORok feldolgozásához. 
 
A következő táblázatban a névtér méretét, valamint a tipikus általános célú fájlmegosztás kapacitásának átalakítását is biztosítottuk, ahol az átlagos fájlméret 512 KiB. Ha a fájlméret kisebb, akkor vegyen fel további memóriát ugyanahhoz a kapacitáshoz. A memória konfigurációjának alapja a névtér mérete alapján.

| Névtér mérete – fájlok & könyvtárak (millió)  | Jellemző kapacitás (TiB)  | CPU-magok  | Ajánlott memória (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (kezdeti szinkronizálás)/2 (jellemző adatforgalom)      |
| 5        | 2.3     | 2        | 16 (kezdeti szinkronizálás)/4 (jellemző adatforgalom)    |
| 10       | 4.7     | 4        | 32 (kezdeti szinkronizálás)/8 (jellemző adatforgalom)   |
| 30       | 14,0    | 8        | 48 (kezdeti szinkronizálás)/16 (jellemző forgalom)   |
| 50       | 23,3    | 16       | 64 (kezdeti szinkronizálás)/32 (jellemző forgalom)  |
| 100 *     | 46,6    | 32       | 128 (kezdeti szinkronizálás)/32 (jellemző forgalom)  |

\*Jelenleg több mint 100 000 000 fájlt szinkronizál & a címtárak jelenleg nem ajánlottak. A tesztelt küszöbértékek alapján ez a határérték nem kötelező. További információ: [Azure Files skálázhatósági és teljesítménybeli célok](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> A névtér kezdeti szinkronizálása intenzív művelet, és javasoljuk, hogy a kezdeti szinkronizálás befejezéséig több memóriát foglaljon le. Ez nem kötelező, de felgyorsíthatja a kezdeti szinkronizálást. 
> 
> A tipikus adatforgalom napi változása a névtér 0,5%-a. Magasabb szintű adatforgalom esetén érdemes lehet további CPU-t hozzáadni. 

- Az NTFS fájlrendszerrel formázott, helyileg csatolt kötet.

### <a name="evaluation-cmdlet"></a>Próbaverziós parancsmag
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós parancsmag használatával. Ez a parancsmag ellenőrzi a fájlrendszerrel és az adatkészlettel kapcsolatos lehetséges problémákat, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. Az ellenőrzések a legtöbb alább említett funkciót lefedik; Javasoljuk, hogy alaposan olvassa el a szakasz további részeit, és győződjön meg arról, hogy az üzembe helyezés zökkenőmentesen működik. 

A kiértékelési parancsmag az az PowerShell-modul telepítésével telepíthető, amely az alábbi utasításokat követve telepíthető: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Használat  
A kiértékelési eszközt többféleképpen is meghívhatja: elvégezheti a rendszer-ellenőrzéseket, az adatkészlet-ellenőrzéseket vagy mindkettőt. A rendszerek és az adatkészlet ellenőrzésének elvégzéséhez tegye a következőket: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Csak az adatkészlet tesztelése:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Csak a rendszerkövetelmények tesztelése:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Az eredmények CSV-ben való megjelenítéséhez:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Fájlrendszer kompatibilitása
A Azure File Sync csak közvetlenül csatlakoztatott, NTFS-kötetek esetén támogatott. A Windows Serveren a közvetlenül csatlakoztatott tárolók vagy a DAS-kiszolgálók azt jelzik, hogy a Windows Server operációs rendszer a fájlrendszert birtokolja. A DAS a lemezeket fizikailag csatolja a fájlkiszolgálón, és virtuális lemezeket csatlakoztat egy fájlkiszolgáló virtuális géphez (például egy Hyper-V által üzemeltetett virtuális géphez), vagy akár ISCSI-n keresztül is.

Csak NTFS-kötetek támogatottak; A ReFS, a FAT, a FAT32 és más fájlrendszerek nem támogatottak.

Az alábbi táblázat az NTFS fájlrendszer szolgáltatásainak együttműködési állapotát mutatja be: 

| Szolgáltatás | Támogatás állapota | Jegyzetek |
|---------|----------------|-------|
| Hozzáférés-vezérlési lista (ACL-ek) | Teljes mértékben támogatott | A Windows-stílusú tulajdonosi hozzáférés-vezérlési listát a Azure File Sync őrzi meg, és a Windows Server a kiszolgálói végpontokon kényszeríti ki. Az ACL-ek az Azure-fájlmegosztás közvetlen csatlakoztatása esetén is kikényszeríthető, azonban ez további konfigurálást igényel. További információért tekintse meg az [Identity szakaszt](#identity) . |
| Rögzített hivatkozások | Kimarad | |
| Szimbolikus hivatkozások | Kimarad | |
| Csatlakoztatási pontok | Részlegesen támogatott | A csatlakoztatási pontok lehetnek a kiszolgálói végpontok gyökeré, de a rendszer kihagyja őket, ha egy kiszolgálói végpont névterében szerepelnek. |
| Csomópontok | Kimarad | Például elosztott fájlrendszer DfrsrPrivate és DFSRoots mappákat. |
| Újraelemzési pontok | Kimarad | |
| NTFS-tömörítés | Teljes mértékben támogatott | |
| Ritka fájlok | Teljes mértékben támogatott | A ritka fájlok szinkronizálása (nincs letiltva), de teljes fájlként szinkronizálja őket a felhőbe. Ha a fájl tartalma megváltozik a felhőben (vagy egy másik kiszolgálón), a fájl már nem ritka, amikor a módosítás le van töltve. |
| Alternatív adatfolyamok (ADS) | Megőrzött, de nincs szinkronizálva | A fájl besorolási infrastruktúrája által létrehozott besorolási címkék például nem szinkronizálhatók. Az egyes kiszolgálói végpontokon található fájlokra vonatkozó meglévő besorolási címkék érintetlenek maradnak. |

<a id="files-skipped"></a>A Azure File Sync bizonyos ideiglenes fájlokat és rendszermappákat is kihagy:

| Fájl/mappa | Megjegyzés |
|-|-|
| pagefile.sys | A rendszerfájlra vonatkozó fájl |
| Desktop.ini | A rendszerfájlra vonatkozó fájl |
| Thumbs. db | Ideiglenes fájl a miniatűrökhöz |
| ehthumbs. db | Ideiglenes fájl a média bélyegképei számára |
| ~$\*.\* | Ideiglenes Office-fájl |
| \*. tmp | Ideiglenes fájl |
| \*.laccdb | ADATBÁZIS-zárolási fájl elérése|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Belső szinkronizáló fájl|
| \\Rendszerkötet adatai | A kötethez tartozó mappa |
| $RECYCLE. BIN| Mappa |
| \\SyncShareState | A szinkronizáláshoz használt mappa |

### <a name="failover-clustering"></a>Feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatást Azure File Sync támogatja a "fájlkiszolgáló az általános használatra" telepítési lehetőség. A feladatátvételi fürtszolgáltatás nem támogatott az alkalmazásadatok Kibővíthető fájlkiszolgáló (SOFS) vagy fürtözött megosztott köteteken (CSV).

> [!Note]  
> A Azure File Sync ügynöknek telepítve kell lennie a feladatátvevő fürt minden csomópontján a szinkronizálás megfelelő működéséhez.

### <a name="data-deduplication"></a>Adatdeduplikáció
**Windows Server 2016 és Windows Server 2019**   
Az deduplikálás nem támogatott, függetlenül attól, hogy a Windows Server 2016 és a Windows Server 2019 rendszerű köteten engedélyezve van-e a felhőalapú réteg, vagy le van-e tiltva egy vagy több kiszolgálói végponton. Ha engedélyezi az deduplikálás szolgáltatást egy olyan köteten, amelyen engedélyezve van a felhőalapú réteg, a további tárhelyek kiépítésének hiányában további fájlokat is gyorsítótárazhat a helyszínen. 

Ha az adatok deduplikálása engedélyezve van egy olyan köteten, amelyen engedélyezve van a felhőalapú rétegek használata, a deduplikáció optimalizált fájlokat a rendszer a felhőre vonatkozó házirend-beállítások alapján a normál fájlhoz hasonló módon fogja összeállítani. Ha a deduplikáció optimalizált fájlok lettek kiválasztva, az adatok deduplikálása adatgyűjtési feladatsor automatikusan lefuttatja a lemezterület felszabadítását a köteten lévő más fájlok által már nem hivatkozott felesleges adattömbök eltávolításával.

Vegye figyelembe, hogy a mennyiségi megtakarítás csak a kiszolgálón érvényes. Az Azure-fájlmegosztás adatait nem törli a rendszer.

> [!Note]  
> Ha engedélyezni szeretné a Windows Server 2019-es verzión engedélyezve lévő, felhőalapú rétegekből származó kötetek adatmásolásának támogatását, telepítenie kell a Windows Update [KB4520062](https://support.microsoft.com/help/4520062) , és Azure file Sync ügynök 9.0.0.0 vagy újabb verziójára van szükség.

**Windows Server 2012 R2**  
A Azure file Sync nem támogatja az deduplikálás és a felhőalapú rétegek használatát ugyanarra a kötetre a Windows Server 2012 R2 rendszeren. Ha egy köteten engedélyezve van az deduplikálás, a Felhőbeli rétegek letiltását le kell tiltani. 

**Megjegyzések**
- Ha a Azure file Sync ügynök telepítése előtt telepíti az deduplikálás szolgáltatást, a rendszer újraindítást igényel az deduplikálás és a felhőalapú rétegek egyazon köteten történő támogatásához.
- Ha az adatok deduplikálása engedélyezve van egy köteten a felhőalapú rétegek engedélyezése után, a kezdeti deduplikálás optimalizálási feladata optimalizálja a köteten még nem használt fájlokat, és a következő hatással lesz a Felhőbeli rétegek használatára:
    - A szabad terület házirendje a hő használatával továbbra is a köteten lévő szabad területtel rendelkező fájlokat fogja használni.
    - A Date Policy kihagyja a fájlokhoz hozzáférő, a deduplikálás optimalizálási feladata miatt esetlegesen felhasználható fájlok leválasztását.
- A folyamatban lévő deduplikálás optimalizációs feladatokhoz a dátummal ellátott felhő-előfizetések késleltetve lesznek az adatok deduplikálása [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume?view=win10-ps) beállítással, ha a fájl még nincs feltömörítve. 
    - Példa: Ha a MinimumFileAgeDays-beállítás hét nap, a felhő-előállítási dátum házirendje pedig 30 nap, a dátum-és 37 nap után a rendszer a fájlokat is felveszi.
    - Megjegyzés: Ha egy fájlt Azure File Sync, akkor a deduplikálás optimalizálási feladata kihagyja a fájlt.
- Ha a telepített Azure file Sync ügynökkel rendelkező Windows Server 2012 R2 rendszert futtató kiszolgáló a Windows Server 2016 vagy a Windows Server 2019 rendszerre frissül, a következő lépéseket kell végrehajtani az deduplikálás és a felhőalapú rétegek egyazon köteten történő támogatásához:  
    - Távolítsa el a Windows Server 2012 R2 Azure File Sync Agent ügynököt, és indítsa újra a kiszolgálót.
    - Töltse le az Azure File Sync-ügynököt az új kiszolgáló operációs rendszerének verziójára (Windows Server 2016 vagy Windows Server 2019).
    - Telepítse a Azure File Sync ügynököt, és indítsa újra a kiszolgálót.  
    
    Megjegyzés: az ügynök eltávolítása és újratelepítése után a rendszer megőrzi a Azure File Sync konfigurációs beállításait a kiszolgálón.

### <a name="distributed-file-system-dfs"></a>Elosztott fájlrendszer (DFS)
Azure File Sync támogatja az elosztott fájlrendszerbeli névterek (DFS-N) és a Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) közötti együttműködési lehetőséget.

**Elosztott fájlrendszerbeli névterek (DFS-n)**: a Azure file Sync teljes mértékben támogatott a DFS-n kiszolgálókon. Az Azure File Sync-ügynököt telepítheti egy vagy több elosztott fájlrendszerbeli tagon, hogy szinkronizálja az adatkapcsolatot a kiszolgálói végpontok és a felhő végpontja között. További információ: az [elosztott fájlrendszerbeli névterek áttekintése](/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Elosztott fájlrendszer replikációs szolgáltatása (DFS-r)**: mivel a DFS-r és a Azure file Sync egyaránt replikációs megoldás, a legtöbb esetben azt javasoljuk, hogy a DFS-r-t Azure file Sync-re cserélje. A DFS-R és a Azure File Sync együttes használata azonban több esetben is lehetséges:

- Egy DFS-R üzemelő példányról telepít át egy Azure File Sync központi telepítésre. További információ: [elosztott fájlrendszer replikációs szolgáltatása (DFS-R) központi telepítésének áttelepítése Azure file Syncre](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem minden olyan helyszíni kiszolgálónak, amelyhez a fájl adatai másolata szükséges, közvetlenül kapcsolódhat az internethez.
- A fiókirodai kiszolgálók egyetlen központi kiszolgálóra összesítik az adategységeket, amelyekhez a Azure File Sync-t szeretné használni.

Azure File Sync és DFS-R esetében:

1. A DFS-R replikált mappákkal rendelkező köteteken le kell tiltani Azure File Sync a Felhőbeli rétegeket.
2. A kiszolgálói végpontokat nem kell konfigurálni az elosztott fájlrendszer-R Írásvédett replikációs mappáin.

További információ: [elosztott fájlrendszer replikációs szolgáltatása Overview (áttekintés](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11))).

### <a name="sysprep"></a>Sysprep
Nem támogatott a Sysprep használata olyan kiszolgálón, amelyen telepítve van a Azure File Sync ügynök, és nem várt eredményekhez vezethet. Az ügynök telepítése és a kiszolgáló regisztrálása a kiszolgáló rendszerképének telepítése és a Sysprep mini-Setup befejezése után történik.

### <a name="windows-search"></a>Windows Search
Ha a felhő-rétegek engedélyezve vannak egy kiszolgálói végponton, a rendszer kihagyja a többszintes fájlokat, és nem indexeli a Windows Search. A nem rétegű fájlok indexelése megfelelő.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Egyéb hierarchikus tárolási kezelési (HSM) megoldások
Nem kell más HSM-megoldásokat használni a Azure File Synchoz.

## <a name="identity"></a>Identitás
A Azure File Sync a szokásos AD-alapú identitással működik, anélkül, hogy a szinkronizálás beállítását meghaladó beállítást kellene beállítania. Azure File Sync használatakor az általános elvárás, hogy a legtöbb hozzáférés a Azure File Sync gyorsítótárazási kiszolgálókon halad át az Azure-fájlmegosztás helyett. Mivel a kiszolgálói végpontok a Windows Serveren találhatók, és a Windows Server hosszú ideje támogatja az AD és a Windows stílusú ACL-eket, nincs szükség arra, hogy a Storage Sync szolgáltatásban regisztrált Windows-fájlkiszolgálók ne legyenek tartományhoz csatlakoztatva. Azure File Sync fogja tárolni az ACL-eket az Azure-fájlmegosztás fájljain, és replikálja azokat az összes kiszolgálói végpontra.

Bár a közvetlenül az Azure-fájlmegosztást érintő módosítások hosszabb ideig tartanak a szinkronizálási csoportban lévő kiszolgálói végpontokkal való szinkronizálásra, érdemes lehet biztosítani, hogy az AD-engedélyeket a fájlmegosztás közvetlenül a felhőben is kényszerítse. Ehhez a Storage-fiókját a helyszíni AD-hez kell csatlakoztatnia, ugyanúgy, mint a Windows-fájlkiszolgálók tartományhoz csatlakoztatása. Ha többet szeretne megtudni a Storage-fiók ügyfél által birtokolt Active Directoryhoz való csatlakoztatásáról, tekintse meg a [Azure Files Active Directory áttekintése](storage-files-active-directory-overview.md)című témakört.

> [!Important]  
> A Storage-fiók Active Directoryhoz való csatlakoztatása nem szükséges a Azure File Sync sikeres telepítéséhez. Ez egy szigorúan opcionális lépés, amely lehetővé teszi az Azure-fájlmegosztás számára a helyszíni ACL-ek kényszerítését, amikor a felhasználók közvetlenül csatlakoztatják az Azure-fájlmegosztást.

## <a name="networking"></a>Hálózatkezelés
A Azure File Sync ügynök kommunikál a Storage Sync szolgáltatással és az Azure-fájlmegosztás használatával a Azure File Sync REST protokoll és a legtitkosítási protokoll segítségével, amely mindkét esetben HTTPS protokollt használ a 443-as porton keresztül. Az SMB soha nem használja fel az adatok feltöltését és letöltését a Windows Server és az Azure-fájlmegosztás között. Mivel a legtöbb szervezet engedélyezi a HTTPS-forgalmat az 443-as porton keresztül, a legtöbb webhely felkeresésének követelménye, a speciális hálózati konfiguráció általában nem szükséges a Azure File Sync telepítéséhez.

A szervezet házirendje vagy az egyedi szabályozási követelmények alapján több korlátozó kommunikációra lehet szükség az Azure-ban, így a Azure File Sync számos mechanizmust biztosít a hálózatkezelés konfigurálásához. A követelmények alapján a következőket teheti:

- Az alagút szinkronizálása és a fájlok feltöltése/letöltése a ExpressRoute vagy az Azure VPN-en keresztül történik. 
- A Azure Files és az Azure hálózati szolgáltatásai, például a szolgáltatási végpontok és a magánhálózati végpontok használata.
- Konfigurálja Azure File Sync a proxy támogatásához a környezetben.
- Hálózati tevékenység szabályozása Azure File Syncból.

Ha többet szeretne megtudni a Azure File Sync és a hálózatkezelésről, tekintse meg a [Azure file Sync hálózati megfontolások](storage-sync-files-networking-overview.md)című témakört.

## <a name="encryption"></a>Titkosítás
Azure File Sync használatakor három különböző titkosítási réteg létezik: titkosítás a Windows Server nyugalmi tárolóján, a Azure File Sync-ügynök és az Azure közötti adatátviteli titkosítás, valamint az Azure-fájlmegosztás adatain kívüli titkosítás. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server titkosítás inaktív állapotban 
Két olyan stratégia létezik, amely az adattitkosítást a Windows Serveren, amely általában a Azure File Sync: titkosítást használja a fájlrendszer alatt, például a fájlrendszert és az ahhoz írt összes adatmennyiséget, és a fájl formátumát is titkosítja. Ezek a módszerek nem zárják ki egymást. Ezek együtt használhatók, ha szükséges, mivel a titkosítás célja eltér.

A fájlrendszer alatti titkosítás biztosításához a Windows Server a BitLocker beérkezett fájlok mappáját biztosítja. A BitLocker teljes mértékben átlátható Azure File Sync. A titkosítási mechanizmusok (például a BitLocker) használatának elsődleges oka, hogy megakadályozza a helyszíni adatközpontból származó adatok fizikai kiszűrése a lemezek lopásával, és megakadályozza, hogy a jogosulatlan olvasási/írási műveletek elvégzése érdekében közvetlen telepítési az adatokat. A BitLocker szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [BitLocker áttekintése](/windows/security/information-protection/bitlocker/bitlocker-overview)című témakört.

Harmadik féltől származó, a BitLockerhez hasonlóan működő termékek, amelyekben az NTFS-kötet alatt ülnek, hasonlóan kell teljes mértékben transzparens módon működniük Azure File Sync. 

Az adattitkosítás másik fő módja a fájl adatfolyamának titkosítása, amikor az alkalmazás menti a fájlt. Néhány alkalmazás natív módon is elvégezhető, de ez általában nem igaz. A fájl adatfolyamának titkosítására szolgáló módszer például Azure Information Protection (/Azure) Rights Management Services (Azure RMS) aktivál Directory RMS. A titkosítási mechanizmusok, például az kiszűrése és az RMS használatának elsődleges oka, hogy megakadályozza az adatok megosztását a fájlmegosztás más helyekre való másolásával, például a Flash meghajtóval, vagy e-mailben egy jogosulatlan személynek. Ha a fájl formátumának részeként titkosítva van egy fájl adatfolyama, a fájl továbbra is titkosítva lesz az Azure-fájlmegosztás esetében. 

A Azure File Sync nem működik együtt az NTFS fájlrendszerrel titkosított fájlrendszerrel (NTFS EFS) vagy a fájlrendszer felett lévő, a fájl adatfolyama alatt lévő külső titkosítási megoldásokkal. 

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

> [!NOTE]
> A Azure File Sync szolgáltatás a 2020-es augusztus 1-től a TLS 1.0 és a 1,1 támogatását fogja eltávolítani. Alapértelmezés szerint az összes támogatott Azure File Sync ügynök verziója már használja a TLS 1.2-es verzióját. A TLS korábbi verziójának használata akkor fordulhat elő, ha a TLS 1.2 le lett tiltva a kiszolgálón, vagy a proxy használatban van. Ha proxyt használ, javasoljuk, hogy ellenőrizze a proxy konfigurációját. Azure File Sync a 5/1/2020-es után hozzáadott szolgáltatási régiók csak a TLS 1.2-es és a TLS 1.0-s és a 1,1-es támogatást fogják eltávolítani a meglévő régiókból, 2020. augusztus 1-től.  További információ: [hibaelhárítási útmutató](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

Azure File Sync ügynök kommunikál a Storage Sync szolgáltatással és az Azure-fájlmegosztás használatával a Azure File Sync REST protokoll és a legtitkosítási protokoll segítségével, amely mindkét esetben HTTPS protokollt használ a 443-as porton keresztül. Azure File Sync nem küld titkosítatlan kérelmeket HTTP-n keresztül. 

Az Azure Storage-fiókok olyan kapcsolót tartalmaznak, amely a titkosítás átvitelét igényli, amely alapértelmezés szerint engedélyezve van. Még ha a tárolási fiók szintjén is le van tiltva a kapcsoló, ami azt jelenti, hogy az Azure-fájlmegosztás nem titkosított kapcsolatai lehetségesek, Azure File Sync továbbra is csak a titkosított csatornákat fogja használni a fájlmegosztás eléréséhez.

A Storage-fiók titkosításának letiltásának elsődleges oka, hogy olyan örökölt alkalmazást támogasson, amelyet régebbi operációs rendszeren kell futtatni, például Windows Server 2008 R2 vagy régebbi Linux-disztribúció, közvetlenül egy Azure-fájlmegosztás használatával. Ha az örökölt alkalmazás a fájlmegosztás Windows Server-gyorsítótárára mutat, a beállítás nem lesz hatással. 

Javasoljuk, hogy engedélyezze a továbbítást a forgalomban lévő adatátvitel titkosításának biztosításához.

További információ az átvitel közbeni titkosításról: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Azure-fájlmegosztás titkosítása nyugalmi állapotban
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Tárolási szintek
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>A standard fájlmegosztás engedélyezése akár 100 TiB-re is terjedhet
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Az Azure file Sync régió rendelkezésre állása
Azure File Sync a következő régiókban érhető el:

| Azure-felhő | Földrajzi régió | Azure-régió | Régiókód |
|-------------|-------------------|--------------|-------------|
| Nyilvános | Ázsia | Kelet-Ázsia | `eastasia` |
| Nyilvános | Ázsia | Délkelet-Ázsia | `southeastasia` |
| Nyilvános | Ausztrália | Kelet-Ausztrália | `australiaeast` |
| Nyilvános | Ausztrália | Délkelet-Ausztrália | `australiasoutheast` |
| Nyilvános | Brazília | Dél-Brazília | `brazilsouth` |
| Nyilvános | Kanada | Közép-Kanada | `canadacentral` |
| Nyilvános | Kanada | Kelet-Kanada | `canadaeast` |
| Nyilvános | Európa | Észak-Európa | `northeurope` |
| Nyilvános | Európa | Nyugat-Európa | `westeurope` |
| Nyilvános | Franciaország | Közép-Franciaország | `francecentral` |
| Nyilvános | Franciaország | Dél-Franciaország * | `francesouth` |
| Nyilvános | India | Közép-India | `centralindia` |
| Nyilvános | India | Dél-India | `southindia` |
| Nyilvános | Japán | Kelet-Japán | `japaneast` |
| Nyilvános | Japán | Nyugat-Japán | `japanwest` |
| Nyilvános | Dél-Korea | Dél-Korea középső régiója | `koreacentral` |
| Nyilvános | Dél-Korea | Dél-Korea déli régiója | `koreasouth` |
| Nyilvános | Dél-afrikai Köztársaság | Dél-Afrika északi régiója | `southafricanorth` |
| Nyilvános | Dél-afrikai Köztársaság | Dél-Afrika nyugati régiója * | `southafricawest` |
| Nyilvános | EAE | Az Egyesült Arab Emírségek középső régiója * | `uaecentral` |
| Nyilvános | EAE | Észak-Egyesült Arab | `uaenorth` |
| Nyilvános | Egyesült Királyság | Az Egyesült Királyság déli régiója | `uksouth` |
| Nyilvános | Egyesült Királyság | Az Egyesült Királyság nyugati régiója | `ukwest` |
| Nyilvános | USA | Az USA középső régiója | `centralus` |
| Nyilvános | USA | USA keleti régiója | `eastus` |
| Nyilvános | USA | USA 2. keleti régiója | `eastus2` |
| Nyilvános | USA | USA északi középső régiója | `northcentralus` |
| Nyilvános | USA | USA déli középső régiója | `southcentralus` |
| Nyilvános | USA | USA nyugati középső régiója | `westcentralus` |
| Nyilvános | USA | USA nyugati régiója | `westus` |
| Nyilvános | USA | USA 2. nyugati régiója | `westus2` |
| US Gov | USA | USA-beli államigazgatás – Arizona | `usgovarizona` |
| US Gov | USA | USA-beli államigazgatás – Texas | `usgovtexas` |
| US Gov | USA | USA-beli államigazgatás – Virginia | `usgovvirginia` |

A Azure File Sync csak olyan Azure-fájlmegosztás szinkronizálását támogatja, amely ugyanabban a régióban található, mint a Storage Sync szolgáltatás.

A csillaggal jelölt régiók esetében kapcsolatba kell lépnie az Azure támogatási szolgálatával, hogy az ezekben a régiókban hozzáférést kérjen az Azure Storage-hoz. A folyamat [ebben a dokumentumban](https://azure.microsoft.com/global-infrastructure/geographies/)található.

## <a name="redundancy"></a>Redundancia
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> A Geo-redundáns és a Geo-zónás redundáns tárolás lehetővé tenné a tárolók manuális feladatátvételét a másodlagos régióba. Azt javasoljuk, hogy az adatvesztés nagyobb valószínűsége miatt ne hajtsa végre ezt a katasztrófán kívül, ha Azure File Sync használ. Abban az esetben, ha a tároló manuális feladatátvételét szeretné elindítani, meg kell nyitnia egy támogatási esetet a Microsofttal, hogy Azure File Sync a másodlagos végponttal való szinkronizálás folytatásához.

## <a name="migration"></a>Áttelepítés
Ha van meglévő Windows-fájlkiszolgáló, Azure File Sync közvetlenül is telepíthető, anélkül, hogy át kellene helyeznie az adatátvitelt egy új kiszolgálóra. Ha a Azure File Sync bevezetésének részeként új Windows-fájlkiszolgálón kíván áttelepítést végezni, több lehetséges módszer áll rendelkezésre az adatáthelyezéshez:

- Hozzon létre kiszolgálói végpontokat a régi fájlmegosztás és az új fájlmegosztás számára, és hagyja, hogy Azure File Sync szinkronizálja az adatokat a kiszolgálói végpontok között. Ennek a megközelítésnek az az előnye, hogy nagyon egyszerűen előfizethet az új fájlkiszolgálón lévő tárterületre, mivel Azure File Sync a Felhőbeli rétegek számára. Ha elkészült, a végfelhasználókat átvágja az új kiszolgálón található fájlmegosztás fölé, és eltávolíthatja a régi fájlmegosztás kiszolgálói végpontját.

- Csak az új fájlkiszolgálón hozzon létre egy kiszolgálói végpontot, és másolja át az adatait a régi fájlmegosztást a használatával `robocopy` . Attól függően, hogy az új kiszolgálón lévő fájlmegosztás topológiája (hány megosztás van az egyes köteteken, hogyan szabadítható fel az egyes kötetek, stb.), ideiglenesen további tárhelyet kell kiosztania, mivel a `robocopy` régi kiszolgálóról a helyszíni adatközpontban lévő új kiszolgálóra való várakozás során az adatok az Azure-ba való áthelyezésének folyamata gyorsabb lesz, mint Azure file Sync.

A Data Box segítségével áttelepítheti az adatAzure File Sync üzembe helyezési szolgáltatásait is. Az idő nagy részében, amikor az ügyfelek a Data Box az adatok betöltéséhez szeretnék használni, azért, mert úgy gondolják, hogy növeli az üzembe helyezés sebességét, vagy azért, mert ez segít a korlátozott sávszélesség-forgatókönyvek esetén. Habár igaz, hogy a Azure File Sync üzembe helyezése során az adatok betöltésére szolgáló Data Box használata csökkenti a sávszélesség-kihasználtságot, valószínűleg gyorsabb lesz a legtöbb forgatókönyv esetén, hogy online adatfeltöltés történjen a fent ismertetett módszerek egyikével. Ha többet szeretne megtudni arról, hogyan használhatók a Data Box az adatAzure File Sync üzembe helyezéséhez, olvassa el a következő témakört: az [adatáttelepítés a Azure file Syncba Azure Data Box](storage-sync-offline-data-transfer.md).

Gyakori hiba, hogy az ügyfelek az új Azure File Sync üzembe helyezéskor az Adatmásolást közvetlenül az Azure-fájlmegosztásba másolják, nem pedig a Windows-fájlkiszolgálók. Bár a Azure File Sync azonosítja az Azure-fájlmegosztás összes új fájlját, és szinkronizálja azokat a Windows-fájlmegosztás számára, ez általában jóval lassabb, mint a Windows-fájlkiszolgálón való betöltés. Az Azure Copy-eszközök, például a AzCopy használata esetén fontos, hogy a legújabb verziót használja. Tekintse át a fájlmásolás- [eszközök táblázatát](storage-files-migration-overview.md#file-copy-tools) , és tekintse át az Azure másolási eszközeit, és győződjön meg arról, hogy a fájl összes fontos metaadatát, például az időbélyegeket és az ACL-eket is másolja.

## <a name="antivirus"></a>Vírusvédelem
Mivel a víruskereső úgy működik, hogy a fájlokat az ismert kártékony kódok vizsgálatával végzi, a víruskereső termék a rétegekből származó fájlok visszahívását okozhatja, ami magas kimenő költségekkel jár. A Azure File Sync ügynök 4,0-es és újabb verzióiban a többplatformos fájlok a biztonságos Windows-attribútumot FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS beállítani. Javasoljuk, hogy a szoftver gyártójával való tanácsadással megtudja, hogyan konfigurálhatja a megoldását úgy, hogy kihagyja a fájlok olvasását ezzel az attribútummal (sok minden automatikusan). 

A Microsoft belső vírusvédelmi megoldásai, a Windows Defender és a System Center Endpoint Protection (SCEP) egyaránt automatikusan kihagyják az ezzel az attribútummal rendelkező fájlok olvasását. Teszteltük őket, és azonosított egy kisebb problémát: amikor hozzáad egy kiszolgálót egy meglévő szinkronizálási csoporthoz, a 800 bájtnál kisebb fájlokat a rendszer visszahívja (letölti) az új kiszolgálón. Ezek a fájlok az új kiszolgálón maradnak, és nem lesznek feldolgozva, mivel nem felelnek meg a rétegek méretére vonatkozó követelménynek (>64 kb).

> [!Note]  
> A víruskereső-szállítók ellenőrizhetik a termékük és a Azure File Sync közötti kompatibilitást a [Azure file Sync Antivirus Compatibility test Suite](https://www.microsoft.com/download/details.aspx?id=58322)használatával, amely letölthető a Microsoft letöltőközpontból.

## <a name="backup"></a>Backup 
Ha a felhő-rétegek engedélyezve vannak, akkor a kiszolgáló-végpontról vagy a kiszolgálói végpontot tartalmazó virtuális gépről közvetlenül biztonsági mentést biztosító megoldások nem használhatók. A felhő-rétegek a kiszolgálói végponton tárolják az adatokat, és az Azure-fájlmegosztás teljes adatkészletét használják. A használt biztonsági mentési megoldástól függően a rendszer kihagyja a rétegekből származó fájlokat, és nem készít biztonsági mentést (mert a FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attribútum van beállítva), vagy a rendszer a lemezre hívja őket, ami magas kimenő költségekkel jár. Javasoljuk, hogy a felhőalapú biztonsági mentési megoldás használatával készítsen biztonsági másolatot az Azure-fájlmegosztás közvetlen biztonsági mentéséhez. További információkért lásd: [Az Azure-fájlmegosztás biztonsági mentésének ismertetése](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) , vagy a biztonsági mentési szolgáltatótól kérheti, hogy támogatják-e az Azure-fájlmegosztás biztonsági mentését.

Ha helyszíni biztonsági mentési megoldást szeretne használni, akkor a biztonsági mentéseket a szinkronizálási csoporton belül olyan kiszolgálón kell végrehajtani, amelyen le van tiltva a Felhőbeli réteg. Ha visszaállítást végez, használja a kötet szintű vagy a fájl szintű visszaállítási lehetőségeket. A fájl szintű visszaállítási lehetőséggel visszaállított fájlok a szinkronizálási csoport összes végpontján szinkronizálva lesznek, és a meglévő fájlok a biztonsági másolatból visszaállított verzióra lesznek lecserélve.  A mennyiségi szintű visszaállítások nem cserélik le az újabb verziójú fájlokat az Azure-fájlmegosztás vagy más kiszolgálói végpontok között.

> [!Note]  
> Az operációs rendszer nélküli (BMR) visszaállítás váratlan eredményekhez vezethet, és jelenleg nem támogatott.

> [!Note]  
> A Azure File Sync ügynök 9-es verziójával a VSS-Pillanatképek (beleértve a korábbi verziók lapot) mostantól támogatottak azokon a köteteken, amelyeken engedélyezve van a felhőalapú rétegek használata. A PowerShell használatával azonban engedélyeznie kell a korábbi verziók kompatibilitását. [Ismerje meg, hogyan](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Következő lépések
* [A tűzfal és a proxy beállításainak megfontolása](storage-sync-files-firewall-and-proxy.md)
* [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
* [Az Azure Files üzembe helyezése](./storage-how-to-create-file-share.md)
* [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
* [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
