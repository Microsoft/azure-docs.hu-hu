---
title: Monitor Azure File Sync | Microsoft Docs
description: Tekintse át, hogyan figyelheti a Azure File Sync a Azure Monitor, a Storage Sync Service és a Windows Server használatával.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8e9fbd5fd8fc90681432ee8403b6dd139d02a5a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796997"
---
# <a name="monitor-azure-file-sync"></a>Az Azure File Sync monitorozása

A Azure File Sync központosíthatja a szervezet fájlmegosztását a Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). A világ minden táján annyi gyorsítótárral lehet, amennyire csak szüksége van.

Ez a cikk azt ismerteti, hogyan figyelheti a Azure File Sync a Azure Monitor, a Storage Sync Service és a Windows Server használatával.

Ez az útmutató a következő forgatókönyveket tartalmazza: 
- Tekintse Azure File Sync metrikákat a Azure Monitor.
- Hozzon létre riasztásokat a Azure Monitor, hogy proaktívan értesítse Önt a kritikus állapotról.
- Tekintse meg a Azure File Sync állapotát a Azure Portal.
- Az eseménynaplók és teljesítményszámlálók használata a Windows-kiszolgálókon az üzemelő példány állapotának Azure File Sync figyelése érdekében. 

## <a name="azure-monitor"></a>Azure Monitor

A [Azure Monitor](../../azure-monitor/overview.md) megtekintheti a metrikákat, és konfigurálhatja a szinkronizálási, felhőbeli rétegezésre és kiszolgálói kapcsolatra vonatkozó riasztásokat.  

### <a name="metrics"></a>Mérőszámok

A Azure File Sync metrikák alapértelmezés szerint engedélyezve vannak, és 15 percenként Azure Monitor rendszernek küldi el őket.

**A metrikák Azure File Sync megtekintése a Azure Monitor**
1. A tárfiókban válassza a **Társzinkronizálási** **szolgáltatás Azure Portal** **Metrikák gombra.**
2. Kattintson a **Metrika** legördülő gombra, és válassza ki a megtekinteni kívánt metrikát.

![Képernyőkép a Azure File Sync metrikákról](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

A következő metrikák Azure File Sync a következő Azure Monitor:

| Metrika neve | Description |
|-|-|
| Szinkronizált bájtok | Az átvitt adatok mérete (feltöltés és letöltés).<br><br>Egység: bájtok<br>Összesítés típusa: Összeg<br>Alkalmazható dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Szinkronizálási csoport neve |
| Felhőbeli rétegezés visszahívása | A visszahívott adatok mérete.<br><br>**Megjegyzés:** Ez a metrika a jövőben el lesz távolítva. A felhőbeli rétegezés visszahívási méretmetrikát a visszahívott adatok méretének monitorozására használhatja.<br><br>Egység: bájtok<br>Összesítés típusa: Összeg<br>Alkalmazható dimenzió: Kiszolgáló neve |
| Felhőbeli rétegezés visszahívási mérete | A visszahívott adatok mérete.<br><br>Egység: bájtok<br>Összesítés típusa: Összeg<br>Alkalmazható dimenziók: Kiszolgálónév, Szinkronizálási csoport neve |
| Felhőbeli rétegezés visszahívási mérete alkalmazás szerint | Az alkalmazás által visszahívott adatok mérete.<br><br>Egység: bájtok<br>Összesítés típusa: Összeg<br>Alkalmazható dimenziók: Alkalmazásnév, Kiszolgálónév, Szinkronizálási csoport neve |
| Felhőbeli rétegezés visszahívási átviteli sebesség | Az adatvisszahívás átviteli sebességének mérete.<br><br>Egység: bájtok<br>Összesítés típusa: Összeg<br>Alkalmazható dimenziók: Kiszolgálónév, Szinkronizálási csoport neve |
| A fájlok nem szinkronizálódnak | A szinkronizálás sikertelen fájljainak száma.<br><br>Egység: Darabszám<br>Összesítési típusok: Átlag, Összeg<br>Alkalmazható dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Szinkronizálási csoport neve |
| Szinkronizált fájlok | Átvitt fájlok száma (feltöltés és letöltés).<br><br>Egység: Darabszám<br>Összesítés típusa: Összeg<br>Alkalmazható dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Szinkronizálási csoport neve |
| Kiszolgáló online állapota | A kiszolgálótól fogadott szívverések száma.<br><br>Egység: Darabszám<br>Összesítés típusa: Maximum<br>Alkalmazható dimenzió: Kiszolgálónév |
| Szinkronizálási munkamenet eredménye | Szinkronizálási munkamenet eredménye (1=sikeres szinkronizálási munkamenet; 0=sikertelen szinkronizálási munkamenet)<br><br>Egység: Darabszám<br>Összesítési típusok: Maximum<br>Alkalmazható dimenziók: Kiszolgálóvégpont neve, Szinkronizálás iránya, Szinkronizálási csoport neve |

### <a name="alerts"></a>Riasztások

A riasztások proaktívan értesítik, ha fontos feltételek találhatók a monitorozási adatokban. További információ a riasztások konfigurálásról a Azure Monitor: A riasztások áttekintése [a Microsoft Azure.](../../azure-monitor/alerts/alerts-overview.md)

**Riasztások létrehozása a Azure File Sync**

1. A **Társzinkronizálási szolgáltatáshoz a** következő **Azure Portal.** 
2. Kattintson **a Figyelés szakaszban** a Riasztások elemre, majd kattintson az + Új **riasztási szabály elemre.**
3. Kattintson **a Feltétel kiválasztása** elemre, és adja meg a következő információkat a riasztáshoz: 
    - **Metrika**
    - **Dimenzió neve**
    - **Riasztási logika**
4. Kattintson **a Műveletcsoport kiválasztása elemre,** és adjon hozzá egy műveletcsoportot (e-mail, SMS stb.) a riasztáshoz egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával.
5. Adja meg a Riasztás **részleteit,** például **a Riasztási szabály neve,** **Leírás és** **Súlyosság adatokat.**
6. Kattintson **a Riasztási szabály létrehozása elemre** a riasztás létrehozásához.  

Az alábbi táblázat felsorol néhány figyelendő példaforgatókönyvet és a riasztáshoz megfelelő metrikát:

| Eset | Riasztáshoz használt metrika |
|-|-|
| A kiszolgálóvégpont állapota hibát jelez a portálon | Szinkronizálási munkamenet eredménye |
| A fájlok szinkronizálása nem sikerül egy kiszolgálóval vagy felhőbeli végponttal | A fájlok nem szinkronizálódnak |
| A regisztrált kiszolgáló nem tud kommunikálni a Társzinkronizálási szolgáltatással | Kiszolgáló online állapota |
| A felhőbeli rétegezés visszahívási mérete egy nap alatt meghaladta az 500GiB-t  | Felhőbeli rétegezés visszahívási mérete |

Az ilyen forgatókönyvekhez használható riasztások létrehozásáról a Riasztáspélák [című szakaszban](#alert-examples) talál útmutatást.

## <a name="storage-sync-service"></a>Társzinkronizálási szolgáltatás

A tárfiók üzemelő példányának Azure File Sync megtekintéséhez lépjen a **Azure Portal** a Társzinkronizálási szolgáltatáshoz, és a következő információk érhetők el: 

- Regisztrált kiszolgáló állapota
- Kiszolgálóvégpont állapota
    - A fájlok nem szinkronizálódnak
    - Szinkronizálási tevékenység
    - Felhőbeli rétegezés hatékonysága
    - A fájlok nem rétegezése
    - Előhívási hibák
- Mérőszámok

### <a name="registered-server-health"></a>Regisztrált kiszolgáló állapota

A regisztrált **kiszolgáló állapotának** a portálon  való megtekintéséhez lépjen a Társzinkronizálási szolgáltatás Regisztrált kiszolgálók **szakaszára.**

![Képernyőkép a regisztrált kiszolgálók állapotáról](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Ha **a Regisztrált kiszolgáló állapota** **Online,** a kiszolgáló sikeresen kommunikál a szolgáltatással.
- Ha a Regisztrált  **kiszolgáló** állapota Offline, a Tárolószinkronizálási figyelő (AzureStorageSyncMonitor.exe) nem fut, vagy a kiszolgáló nem fér hozzá a Azure File Sync szolgáltatáshoz. Útmutatásért tekintse meg [a hibaelhárítási](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) dokumentációt.

### <a name="server-endpoint-health"></a>Kiszolgálóvégpont állapota

A kiszolgálóvégpont állapotának **megtekintéséhez** a portálon lépjen a Szinkronizálási csoportok szakaszra a **Társzinkronizálási** szolgáltatásban, és válasszon ki egy **szinkronizálási csoportot.** 

![Képernyőkép a kiszolgálóvégpont állapotáról](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- A **kiszolgálóvégpont állapota** **és** szinkronizálási tevékenysége a portálon a kiszolgáló Telemetria eseménynaplójában naplózott szinkronizálási eseményeken alapul (9102-es és 9302-es azonosító). Ha egy szinkronizálási munkamenet átmeneti hiba ( például megszakítva) miatt meghiúsul,  a kiszolgálóvégpont állapota továbbra is kifogástalan marad a portálon, amíg az aktuális szinkronizálási munkamenet folyamatban van (fájlok lesznek alkalmazva). A 9302-es eseményazonosító a szinkronizálási folyamat eseménye, a 9102-es eseményazonosító pedig a szinkronizálási munkamenet befejezése után lesz naplózva.  További információ: Szinkronizálás állapota [és](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) [szinkronizálási folyamat.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session) Ha a kiszolgálóvégpont állapota Hiba **vagy** **Nincs** tevékenység, tekintse meg a [hibaelhárítási dokumentációt.](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors)
- A **portálon a** nem szinkronizált fájlok száma a 9121-es eseményazonosítón alapul, amely a kiszolgáló telemetriai eseménynaplójában van naplózva. A szinkronizálási munkamenet befejezése után a program minden elemenkénti hiba esetén naplózza ezt az eseményt. Az elemenkénti hibák elhárításához tekintse meg a Hogyan, hogy vannak-e olyan fájlok vagy mappák, amelyek [nem szinkronizálnak?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- A felhőbeli **rétegezés hatékonyságának** a portálon való megtekintéséhez lépjen a Kiszolgálói végpont tulajdonságai elemre, és lépjen a Felhőbeli  **rétegezés szakaszra.** A felhőbeli rétegezés hatékonyságának növelése érdekében megadott adatok a 9071-es eseményazonosítón alapulnak, amely a kiszolgáló telemetriai eseménynaplójában van naplózva. További információ: [Felhőbeli rétegezés monitorozása.](file-sync-monitor-cloud-tiering.md)
- Ha meg szeretne tekinteni  **olyan fájlokat,** amelyek  nem rétegzettek, és a portálon előhívási hibákat szeretne megtekinteni, lépjen a Kiszolgálóvégpont tulajdonságai lapra, és lépjen a Felhőbeli **rétegezés szakaszra.** **A nem rétegzett** fájlok a 9003-as eseményazonosítón alapulnak, amely  a kiszolgáló Telemetria eseménynaplójában van naplózva, a visszahívási hibák pedig a 9006-os eseményazonosítón alapulnak. A nem rétegzett vagy előhívási [](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) fájlok vizsgálatáról a Nem rétegzett fájlok hibaelhárítása és a Nem visszahívható fájlok hibaelhárítása [témakörben található.](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)

### <a name="metric-charts"></a>Metrikadiagramok

- Az alábbi metrikadiagramok a Szinkronizálási szolgáltatás portálján hetők meg:

  | Metrika neve | Description | Panel neve |
  |-|-|-|
  | Szinkronizált bájtok | Az átvitt adatok mérete (feltöltés és letöltés) | Szinkronizálási csoport, kiszolgálóvégpont |
  | Felhőbeli rétegezés visszahívása | A visszahívott adatok mérete | Regisztrált kiszolgálók |
  | A fájlok nem szinkronizálódnak | A szinkronizálás sikertelen fájljainak száma | Kiszolgálóvégpont |
  | Szinkronizált fájlok | Átvitt fájlok száma (feltöltés és letöltés) | Szinkronizálási csoport, kiszolgálóvégpont |
  | Kiszolgáló online állapota | A kiszolgálótól fogadott szívverések száma | Regisztrált kiszolgálók |

- További információ: [Azure Monitor.](#azure-monitor)

  > [!Note]  
  > A Társzinkronizálási szolgáltatás portálján a diagramok időtartománya 24 óra. A különböző időtartományok vagy dimenziók megtekintéséhez használja a Azure Monitor.

## <a name="windows-server"></a>Windows Server

A **windowsos kiszolgálón,** Azure File Sync ügynök telepítve van, az eseménynaplók és a  teljesítményszámlálók segítségével megtekintheti a kiszolgálón található kiszolgálóvégpontokat. 

### <a name="event-logs"></a>Eseménynaplók

A kiszolgálón a telemetriai eseménynaplóval monitorozással figyelheti a regisztrált kiszolgálót, a szinkronizálást és a felhőbeli rétegezés állapotát. A Telemetria eseménynaplója az alkalmazások és szolgáltatások *Eseménynapló\Microsoft\FileSync\Agent* mappában található.

Szinkronizálás állapota

- A szinkronizálási munkamenet befejezése után a rendszer a 9102-es eseményazonosítót naplózza. Az esemény használatával megállapíthatja, hogy sikeresek-e a szinkronizálási munkamenetek (**HResult = 0**), és hogy vannak-e elemenkénti szinkronizálási hibák **(PerItemErrorCount).** További információért tekintse meg a [szinkronizálási állapot](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) és [az elemenkénti hibák dokumentációját.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Előfordulhat, hogy a szinkronizálási munkamenetek összességében meghiúsulnak, vagy nem nulla PerItemErrorCount értékük van. Azonban továbbra is haladnak előre, és egyes fájlok szinkronizálása sikeresen megtörtént. Ezt az Alkalmazott mezőkben láthatja, például AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes. Ezek a mezők azt mutatjak, hogy a munkamenet mekkora része volt sikeres. Ha azt látja, hogy több szinkronizálási munkamenet meghiúsul egy sorban, és azok száma egyre nő, a támogatási jegy megnyitása előtt adjon időt a szinkronizálásnak, hogy újra megpróbáljon.

- A 9121-es eseményazonosítót a rendszer minden elemenkénti hiba esetén naplózza, amint a szinkronizálási munkamenet befejeződik. Ez az esemény határozza meg a hibával nem szinkronizálható fájlok számát **(PersistentCount** és **TransientCount).** Az elemenkénti állandó hibákat meg kell vizsgálni, Hogyan tekintse meg, hogy vannak-e olyan konkrét fájlok vagy mappák, amelyek [nem szinkronizálódnak?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- A rendszer 5–10 percenként naplózza a 9302-es eseményazonosítót, ha aktív szinkronizálási munkamenet van. Ez az esemény határozza meg a szinkronizálni kívánt elemek számát **(TotalItemCount),** az eddig szinkronizált elemek számát **(AppliedItemCount**) és az elemenkénti hiba miatt sikertelenül szinkronizált elemek számát (**PerItemErrorCount**). Ha a szinkronizálás nem halad **(AppliedItemCount=0),** a szinkronizálási munkamenet végül sikertelen lesz, és a 9102-es eseményazonosító lesz naplózva a hibával. További információt a szinkronizálási [folyamat dokumentációjában talál.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Regisztrált kiszolgáló állapota

- A rendszer 30 másodpercenként naplózza a 9301-es eseményazonosítót, amikor egy kiszolgáló feladatokat keres a szolgáltatásban. Ha a GetNextJob **0** állapottal ér véget, a kiszolgáló képes kommunikálni a szolgáltatással. Ha a GetNextJob hibával fejezi be a munkát, útmutatásért tekintse meg [a hibaelhárítási](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) dokumentációt.

Felhőbeli rétegezés állapota

- A kiszolgáló rétegzett tevékenységének figyelése érdekében használja a 9003-as, a 9016-os és a 9029-es eseményazonosítót a Telemetria eseménynaplóban, amely a Eseménynapló Applications *and Services\Microsoft\FileSync\Agent* mappában található.

  - A 9003-as eseményazonosító hibaeloszlást biztosít a kiszolgálóvégponthoz. Például: Hibák teljes száma és Hibakód. Hibakódonként egy esemény naplózása történik.
  - A 9016-os eseményazonosító egy kötethez biztosít szellemkép-eredményeket. Például: A szabad terület százalékos aránya, a munkamenetben szellemben lévő fájlok száma, és a fájlok száma nem sikerült.
  - A 9029-es eseményazonosító a kiszolgálóvégpontra vonatkozó szellem-munkamenet-információkat biztosít. Például: A munkamenetben megkísérelt fájlok száma, a munkamenetben rétegzett fájlok száma és a már rétegzett fájlok száma.

- A kiszolgáló előhívási tevékenységének figyelése érdekében használja a 9005, 9006, 9009, 9059 és 9071 eseményazonosítót a telemetriai eseménynaplóban, amely a Eseménynapló Alkalmazások és *szolgáltatások\Microsoft\FileSync\Agent* mappában található.

  - A 9005-ös eseményazonosító a kiszolgálóvégpont visszahívási megbízhatóságát biztosítja. Például: Az elért egyedi fájlok teljes száma és a Sikertelen hozzáférésű egyedi fájlok teljes száma.
  - A 9006-os eseményazonosító visszahívási hibák eloszlását biztosítja a kiszolgálóvégponthoz. Például: Összes sikertelen kérelem és Hibakód. Hibakódonként egy esemény naplózása történik.
  - A 9009-es eseményazonosító visszahívási munkamenet-információkat biztosít a kiszolgálóvégponthoz. Például: DurationSeconds, CountFilesRecallSucceeded és CountFilesRecallFailed.
  - A 9059-es eseményazonosító alkalmazás-visszahívási elosztást biztosít a kiszolgálóvégponthoz. Például: ShareId, Application Name és TotalEgressNetworkBytes.
  - A 9071-es eseményazonosító felhőbeli rétegezést biztosít a kiszolgálóvégpont számára. Például: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheBytes és TotalCacheMissBytes.

### <a name="performance-counters"></a>Teljesítményszámlálók

A szinkronizálási Azure File Sync a kiszolgáló teljesítményszámlálóit.

A Azure File Sync teljesítményszámlálók megtekintéséhez nyissa meg a Teljesítményfigyelőt (Perfmon.exe). A számlálókat az **Átvitt AFS** bájtok és az **AFS-szinkronizálási műveletek objektumok alatt találja.**

Az alábbi teljesítményszámlálók érhetők Azure File Sync teljesítményfigyelőben:

| Teljesítményobjektum\Számláló neve | Description |
|-|-|
| Átvitt AFS-bájtok\Letöltött bájtok száma másodpercenként | A másodpercenként letöltött bájtok száma. |
| Átvitt AFS bájtok\Feltöltött bájtok száma másodpercenként | A másodpercenként feltöltött bájtok száma. |
| Átvitt AFS-bájtok\Összes bájt/s | Összes bájt/másodperc (feltöltés és letöltés). |
| AFS-szinkronizálási műveletek\Letöltött szinkronizálási fájlok másodpercenként | A másodpercenként letöltött fájlok száma. |
| AFS-szinkronizálási műveletek\Feltöltött szinkronizálási fájlok másodpercenként | A másodpercenként feltöltött fájlok száma. |
| AFS-szinkronizálási műveletek\Fájlszinkronizálási műveletek összesen másodpercenként | A szinkronizált fájlok teljes száma (feltöltés és letöltés). |

## <a name="alert-examples"></a>Riasztási példák
Ez a szakasz néhány példa riasztást biztosít a Azure File Sync.

  > [!Note]  
  > Ha létrehoz egy riasztást, és az túl zajos, módosítsa a küszöbértéket és a riasztási logikát.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Riasztás létrehozása, ha a kiszolgálóvégpont állapota hibát jelez a portálon

1. **A(Azure Portal** keresse meg a **megfelelő Szinkronizálási társzolgáltatást.** 
2. A Figyelés **szakaszban** kattintson a **Riasztások elemre.** 
3. Új **riasztási szabály létrehozásához kattintson** az + Új riasztási szabály elemre. 
4. Konfigurálja a feltételt a **Feltétel kiválasztása gombra kattintva.**
5. A **Jellogika konfigurálása panelen** kattintson a **Szinkronizálási munkamenet eredménye elemre a** jel neve alatt.  
6. Válassza ki a következő dimenziókonfigurációt: 
     - Dimenzió neve: **Kiszolgálói végpont neve**  
     - Üzemeltető: **=** 
     - Dimenzióértékek: **Minden jelenlegi és jövőbeli érték**  
7. Lépjen a **Riasztási logika lapra,** és töltse ki a következőket: 
     - A küszöbérték statikusra **van állítva** 
     - Operátor: **Kisebb, mint** 
     - Összesítés típusa: **Maximum**  
     - Küszöbérték: **1** 
     - A kiértékelése a következő alapján történik: Összesítés részletessége = **24** óra | Értékelés gyakorisága = **Óránként** 
     - Kattintson a **Kész gombra.** 
8. Kattintson **a Műveletcsoport kiválasztása** elemre egy műveletcsoport (e-mail, SMS stb.) a riasztáshoz való hozzáadásához egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával.
9. Adja meg a Riasztás **részleteit,** például **a Riasztási szabály neve,** **Leírás és** **Súlyosság adatokat.**
10. Kattintson a **Riasztási szabály létrehozása** lehetőségre. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Riasztás létrehozása, ha a fájlok szinkronizálása nem sikerül egy kiszolgálóval vagy felhőbeli végponttal

1. A **Azure Portal** a megfelelő **Szinkronizálási szolgáltatáshoz.** 
2. Válassza a **Figyelés szakaszt,** és kattintson a **Riasztások elemre.** 
3. Új **riasztási szabály létrehozásához kattintson** az + Új riasztási szabály elemre. 
4. Konfigurálja a feltételt a **Feltétel kiválasztása gombra kattintva.**
5. A **Jellogika konfigurálása panelen** kattintson a **Nem szinkronizálható fájlok** elemre a jel neve alatt.  
6. Válassza ki a következő dimenziókonfigurációt: 
     - Dimenzió neve: **Kiszolgálói végpont neve**  
     - Üzemeltető: **=** 
     - Dimenzióértékek: **Minden aktuális és jövőbeli érték**  
7. Lépjen a **Riasztási logika lapra,** és töltse ki a következőket: 
     - A küszöbérték statikusra **van állítva** 
     - Operátor: **Nagyobb, mint** 
     - Összesítés típusa: **Átlag**  
     - Küszöbérték: **100** 
     - A kiértékelése a következő alapján történik: Összesítés részletessége = **5 perc** | Értékelés gyakorisága = **5 percenként** 
     - Kattintson a **Kész gombra.** 
8. Kattintson a **Műveletcsoport kiválasztása** elemre egy műveletcsoport (e-mail, SMS stb.) a riasztáshoz való hozzáadásához egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával.
9. Adja meg a **Riasztás részleteit,** például **a riasztási szabály nevét,** **leírását** és **súlyosságát.**
10. Kattintson a **Riasztási szabály létrehozása** lehetőségre. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Riasztás létrehozása, ha egy regisztrált kiszolgáló nem tud kommunikálni a Társzinkronizálási szolgáltatással

1. **A(Azure Portal** keresse meg a **megfelelő Szinkronizálási társzolgáltatást.** 
2. A Figyelés **szakaszban** kattintson a **Riasztások elemre.** 
3. Új **riasztási szabály létrehozásához kattintson** az + Új riasztási szabály elemre. 
4. Konfigurálja a feltételt a **Feltétel kiválasztása gombra kattintva.**
5. A **Jellogika konfigurálása panelen** kattintson a **Kiszolgáló online állapota elemre a** jel neve alatt.  
6. Válassza ki a következő dimenziókonfigurációt: 
     - Dimenzió neve: **Kiszolgálónév**  
     - Üzemeltető: **=** 
     - Dimenzióértékek: **Minden jelenlegi és jövőbeli érték**  
7. Lépjen a **Riasztási logika lapra,** és töltse ki a következőket: 
     - A küszöbérték statikusra **van állítva** 
     - Operátor: **Kisebb, mint** 
     - Összesítés típusa: **Maximum**  
     - Küszöbérték (bájt): **1** 
     - A kiértékelése a következő alapján történik: Összesítés részletessége = **1 óra** | Értékelés gyakorisága = **30 percenként** 
         - Vegye figyelembe, hogy a rendszer 15 Azure Monitor 20 percenként küldi el a metrikákat. Ne állítsa a Kiértékelési **gyakoriságot** 30 percnél rövidebbre (ez téves riasztásokat hoz létre).
     - Kattintson a **Kész gombra.** 
8. Kattintson **a Műveletcsoport kiválasztása** elemre egy műveletcsoport (e-mail, SMS stb.) a riasztáshoz való hozzáadásához egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával.
9. Adja meg a Riasztás **részleteit,** például **a Riasztási szabály neve,** **Leírás és** **Súlyosság adatokat.**
10. Kattintson a **Riasztási szabály létrehozása** lehetőségre. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Riasztás létrehozása, ha a felhőbeli rétegezés visszahívási mérete egy nap alatt meghaladta az 500GiB-t

1. A **Azure Portal** a megfelelő **Szinkronizálási szolgáltatáshoz.** 
2. Válassza a **Figyelés szakaszt,** és kattintson a **Riasztások elemre.** 
3. Új **riasztási szabály létrehozásához kattintson** az + Új riasztási szabály elemre. 
4. Konfigurálja a feltételt a **Feltétel kiválasztása gombra kattintva.**
5. A **Jellogika konfigurálása panelen** kattintson a **Felhőbeli rétegezés visszahívási méretére** a jel neve alatt.  
6. Válassza ki a következő dimenziókonfigurációt: 
     - Dimenzió neve: **Kiszolgálónév**  
     - Üzemeltető: **=** 
     - Dimenzióértékek: **Minden aktuális és jövőbeli érték**  
7. Lépjen a **Riasztási logika lapra,** és töltse ki a következőket: 
     - A küszöbérték statikusra **van állítva** 
     - Operátor: **Nagyobb, mint** 
     - Összesítés típusa: **Összesen**  
     - Küszöbérték (bájt): **67108864000** 
     - A kiértékelése a következő alapján történik: Összesítés részletessége = **24** óra | Értékelés gyakorisága = **Óránként** 
     - Kattintson a **Kész gombra.** 
8. Kattintson a **Műveletcsoport kiválasztása** elemre egy műveletcsoport (e-mail, SMS stb.) a riasztáshoz való hozzáadásához egy meglévő műveletcsoport kiválasztásával vagy új műveletcsoport létrehozásával.
9. Adja meg a **Riasztás részleteit,** például **a riasztási szabály nevét,** **leírását** és **súlyosságát.**
10. Kattintson a **Riasztási szabály létrehozása** lehetőségre. 

## <a name="next-steps"></a>Következő lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
- [Fontolja meg a tűzfal- és proxybeállítások használatát](file-sync-firewall-and-proxy.md)
- [Azure File Sync – üzembe helyezés](file-sync-deployment-guide.md)
- [Azure File Sync – hibaelhárítás](file-sync-troubleshoot.md)