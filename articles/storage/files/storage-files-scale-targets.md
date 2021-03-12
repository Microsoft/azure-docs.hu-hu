---
title: Az Azure Files méretezhetőségi és teljesítménycéljai
description: Ismerje meg a Azure Files méretezhetőségét és teljesítményét, beleértve a kapacitást, a kérelmek sebességét, valamint a bejövő és kimenő sávszélesség korlátozásait.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: aa24989103cca5bb7031a21ca106b93ada0c3904
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149460"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure Files méretezhetőségi és teljesítménycéljai
A [Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az SMB-és NFS fájlrendszer-protokollok használatával érhető el. Ez a cikk a Azure Files és Azure File Sync méretezhetőségi és teljesítménybeli céljait ismerteti.

Az itt felsorolt skálázhatósági és teljesítményi célok magas végpontok, de a telepítés más változói is befolyásolhatják. Előfordulhat például, hogy a fájl átviteli sebességét a rendelkezésre álló hálózati sávszélesség korlátozza, nem csak az Azure-fájlmegosztást üzemeltető kiszolgálók. Nyomatékosan javasoljuk a használati minta tesztelését annak meghatározására, hogy a Azure Files méretezhetősége és teljesítménye megfelel-e a követelményeknek. Emellett elkötelezettek vagyunk a korlátok időbeli változásának növelésére. 

## <a name="azure-files-scale-targets"></a>Azure Files – skálázási célok
Az Azure-fájlmegosztás tárolási fiókokra van telepítve, amelyek a tárolók megosztott készletét képviselő legfelső szintű objektumok. Ez a tárterület több fájlmegosztás üzembe helyezésére is használható. A következő három kategóriát érdemes figyelembe venni: Storage-fiókok, Azure-fájlmegosztás és-fájlok.

### <a name="storage-account-scale-targets"></a>Storage-fiók méretezési céljai
Az Azure több típusú Storage-fiókot támogat a különböző tárolási forgatókönyvekhez, de a Azure Files két fő típusa van. A létrehozandó Storage-fiók típusa attól függ, hogy szabványos fájlmegosztást vagy prémium szintű fájlmegosztást szeretne-e létrehozni: 

- **Általános célú 2-es verziójú (GPv2) Storage-fiókok**: a GPv2 Storage-fiókok lehetővé teszik az Azure-fájlmegosztás szabványos/merevlemez-alapú (HDD-alapú) hardveren való üzembe helyezését. Az Azure-fájlmegosztás tárolásán kívül a GPv2 más tárolási erőforrásokat is tárolhat, például blob-tárolókat, várólistákat vagy táblákat. A fájlmegosztás üzembe helyezhető a tranzakcióra optimalizált (alapértelmezett), a gyors vagy a lassú elérésű szinteken.

- **FileStorage Storage-fiókok**: a FileStorage Storage-fiókok lehetővé teszik az Azure-fájlmegosztás prémium/SSD-alapú (SSD-alapú) hardveren való üzembe helyezését. FileStorage-fiókokat csak az Azure-fájlmegosztás tárolására lehet használni; egy FileStorage-fiókban nem helyezhetők üzembe más tárolási erőforrások (blob-tárolók, várólisták, táblák stb.).

| Attribútum | GPv2 Storage-fiókok (standard) | FileStorage Storage-fiókok (prémium) |
|-|-|-|
| Storage-fiókok száma régiónként/előfizetéssel | 250 | 250 |
| Storage-fiók maximális kapacitása | 5 PiB<sup>1</sup> | 100 TiB (kiépítve) |
| Fájlmegosztás maximális száma | Korlátlan | Korlátlan számú, az összes megosztás teljes mérete nem haladhatja meg a maximális Storage-fiók kapacitását. |
| Egyidejű kérelmek maximális száma | 20 000 IOPS<sup>1</sup> | 100 000 IOPS |
| Bejövő forgalom maximális száma | <ul><li>USA/Európa: 10 GBP/mp<sup>1</sup></li><li>Egyéb régiók (LRS/ZRS): 10 GBP/mp<sup>1</sup></li><li>Egyéb régiók (GRS): 5 GBP/mp<sup>1</sup></li></ul> | 4 136 MiB/mp |
| Kimenő forgalom maximális száma | 50 GBP/mp<sup>1</sup> | 6 204 MiB/mp |
| Virtuális hálózati szabályok maximális száma | 200 | 200 |
| IP-címekre vonatkozó szabályok maximális száma | 200 | 200 |
| Kezelési olvasási műveletek | 800/5 perc | 800/5 perc |
| Kezelési írási műveletek | 10/másodperc/1200/óra | 10/másodperc/1200/óra |
| Felügyeleti lista műveletei | 100/5 perc | 100/5 perc |

<sup>1</sup> az általános célú 2. verziójú Storage-fiókok magasabb kapacitási korlátokat és magasabb korlátot támogatnak a bejövő forgalomra vonatkozóan. A fiók korlátozásának növeléséhez forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Azure-fájlmegosztás méretezési céljai
| Attribútum | Standard fájlmegosztás<sup>1</sup> | Prémium fájlmegosztás |
|-|-|-|
| Fájlmegosztás minimális mérete | Nincs minimum | 100 GiB (kiépítve) |
| Kiépített méret növelése/csökkentése egység | N/A | 1 GiB |
| Fájlmegosztás maximális mérete | <ul><li>100 TiB, nagyméretű fájlmegosztás funkció engedélyezve<sup>2</sup></li><li>5 TiB, alapértelmezett</li></ul> | 100 TiB |
| Fájlmegosztás fájljainak maximális száma | Korlátlan | Korlátlan |
| Kérelmek maximális száma (max. IOPS) | <ul><li>10 000, nagyméretű fájlmegosztás funkció engedélyezve<sup>2</sup></li><li>1 000 vagy 100 kérelem/100 MS, alapértelmezett</li></ul> | <ul><li>Alapterv IOPS: 400 + 1 IOPS/GiB, legfeljebb 100 000</li><li>IOPS-kitörés: Max (4000, 3x IOPS/GiB), akár 100 000</li></ul> |
| Egyetlen fájlmegosztás maximális bejövő száma | <ul><li>Akár 300 MiB/s, nagyméretű fájlmegosztás funkcióval (<sup>2</sup> )</li><li>Akár 60 MiB/mp, alapértelmezett</li></ul> | 40 MiB/s + 0,04 * kiépített GiB |
| Maximális kimenő forgalom egyetlen fájlmegosztás esetén | <ul><li>Akár 300 MiB/s, nagyméretű fájlmegosztás funkcióval (<sup>2</sup> )</li><li>Akár 60 MiB/mp, alapértelmezett</li></ul> | 60 MiB/s + 0,06 * kiépített GiB |
| Megosztási Pillanatképek maximális száma | 200 Pillanatképek | 200 Pillanatképek |
| A maximális objektum (könyvtárak és fájlok) nevének hossza | 2 048 karakter | 2 048 karakter |
| A maximális elérésiút-összetevő (az elérési út \A\B\C\D, minden betű egy összetevő) | 255 karakter | 255 karakter |
| Rögzített hivatkozás korlátja (csak NFS esetén) | N/A | 178 |
| Többcsatornás SMB-csatornák maximális száma | N.A. | 4 |
| Tárolt hozzáférési szabályzatok maximális száma fájlmegosztás esetén | 5 | 5 |

<sup>1</sup> a standard fájlmegosztás korlátai a standard fájlmegosztás számára elérhető mindhárom rétegre vonatkoznak: a tranzakció optimalizált, a gyors és a lassú.

<sup>2</sup> a standard fájlmegosztás alapértelmezett értéke 5 TiB. További információ: [nagy fájlmegosztás engedélyezése és létrehozása](./storage-files-how-to-create-large-file-share.md) a szabványos fájlmegosztás növelésének részleteiről 100 TiB-ra.

### <a name="file-scale-targets"></a>Fájlok méretezési céljai
| Attribútum | A standard fájlmegosztás fájljai  | A prémium fájlmegosztás fájljai  |
|-|-|-|
| Maximális fájlméret | 4 TiB | 4 TiB |
| Egyidejű kérelmek maximális száma | 1 000 IOPS | Akár 8 000<sup>1</sup> |
| Egy fájl maximális bejövő értékeit | 60 MiB/mp | 200 MiB/mp (legfeljebb 1 GiB/s SMB többcsatornás előzetes verzióval)<sup>2</sup>|
| A kimenő fájlok maximális száma | 60 MiB/mp | 300 MiB/mp (legfeljebb 1 GiB/s SMB többcsatornás előzetes verzióval)<sup>2</sup> |
| Egyidejű fogópontok maximális száma | 2 000-kezelők | 2 000-kezelők  |

<sup>1 az olvasási és írási iOS-re vonatkozik (jellemzően a 64 KiB-nál kisebb vagy azzal egyenlő kis i/o-méretek). Az olvasási és írási műveletektől eltérő metaadat-műveletek lehetnek alacsonyabbak.</sup> 
 <sup>2 a számítógép hálózati korlátai, a rendelkezésre álló sávszélesség, az i/o-méretek, a várólista mélysége és egyéb tényezők. További részletek: [többcsatornás SMB-teljesítmény](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync skálázási céljai
Az alábbi táblázat a Microsoft tesztelésének határait mutatja be, és azt is jelzi, hogy mely célok rögzített határértékek:

| Erőforrás | Cél | Rögzített korlát |
|----------|--------------|------------|
| Tárolási szinkronizálási szolgáltatások régiónként | 100 Storage Sync Services | Yes |
| Szinkronizálási csoportok/Storage Sync Service | 200 szinkronizálási csoportok | Yes |
| Regisztrált kiszolgálók/Storage Sync Service | 99-kiszolgálók | Yes |
| Felhőbeli végpontok szinkronizálása csoportonként | 1 Felhőbeli végpont | Yes |
| Kiszolgálói végpontok szinkronizálási csoportonként | 100 kiszolgálói végpontok | Yes |
| Kiszolgálói végpontok száma kiszolgálónként | 30 kiszolgálói végpont | Yes |
| Fájlrendszerbeli objektumok (címtárak és fájlok) szinkronizálási csoportonként | 100 000 000 objektum | No |
| A fájlrendszer objektumainak maximális száma (címtárak és fájlok) a címtárban | 5 000 000 objektum | Yes |
| Maximális objektum (címtárak és fájlok) biztonsági leíró mérete | 64 KiB | Yes |
| Fájlméret | 100 GiB | No |
| A többszintű fájl minimális fájlmérete | V9-es és újabb verziók: fájlrendszerbeli fürt méretétől függően (dupla fájlrendszerű fürt mérete). Ha például a fájlrendszer fürtjének mérete 4 KiB, a minimális fájlméret 8 KiB lesz.<br> V8-as és régebbi: 64 KiB  | Yes |

> [!Note]  
> Egy Azure File Sync végpont akár egy Azure-fájlmegosztás méretére is méretezhető. Ha az Azure-fájlmegosztás mérete eléri a korlátot, a szinkronizálás nem fog működni.

### <a name="azure-file-sync-performance-metrics"></a>Az Azure File Sync teljesítménymetrikái
Mivel a Azure File Sync ügynök egy olyan Windows Server rendszerű gépen fut, amely az Azure-fájlmegosztás számára csatlakozik, a tényleges szinkronizálási teljesítmény számos tényezőtől függ az infrastruktúrában: a Windows Server és a mögöttes lemez konfigurációja, a kiszolgáló és az Azure Storage közötti hálózati sávszélesség, a fájlméret, a teljes adatkészlet mérete és az adatkészlet tevékenysége. Mivel Azure File Sync a fájl szintjén működik, a Azure File Sync-alapú megoldás teljesítménybeli jellemzői jobban mérhetők a másodpercenként feldolgozott objektumok (fájlok és könyvtárak) száma alapján.

Azure File Sync esetében a teljesítmény kritikus fontosságú két szakaszban:

1. **Kezdeti egyszeri kiépítés**: a teljesítmény optimalizálásához a kezdeti kiépítés során tekintse meg a bevezetést [Azure file Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) az optimális üzembe helyezési részletekért.
2. **Folyamatos szinkronizálás**: miután az Azure-fájlmegosztás először befejeződik az adatgyűjtés során, Azure file Sync több végpontot is megtart a szinkronizálásban.

Az alábbi szakaszokban megtervezheti az üzembe helyezést, és a belső tesztelés során megfigyelheti a konfigurációt a rendszeren.

| Rendszerkonfiguráció | Részletek |
|-|-|
| CPU | 64 virtuális magok a 64 MiB L3 gyorsítótárral |
| Memória | 128 GiB |
| Lemez | SAS-lemezek RAID 10 akkumulátorral rendelkező gyorsítótárral |
| Network (Hálózat) | 1 GB/s hálózat |
| Számítási feladat | általános célú fájlkiszolgáló|

| Egyszeri kiépítés kezdeti időpontja  | Részletek |
|-|-|
| Objektumok száma | 25 000 000 objektum |
| Adatkészlet mérete| ~ 4,7 TiB |
| Fájlméret átlagos mérete | ~ 200 KiB (legnagyobb fájl: 100 GiB) |
| A felhő kezdeti változásának számbavétele | 20 objektum másodpercenként  |
| Feltöltési sebesség | 20 objektum másodpercenként/szinkronizálási csoportonként |
| Névtér letöltési átviteli sebessége | 400 objektum/másodperc |

### <a name="initial-one-time-provisioning"></a>Egyszeri kiépítés kezdeti időpontja

**Kezdeti Felhőbeli módosítás enumerálása**: új szinkronizálási csoport létrehozásakor a kezdeti Felhőbeli változások számbavétele az első lépés, amelyet a rendszer végrehajt. Ebben a folyamatban a rendszer az Azure-fájlmegosztás összes elemét enumerálja. A folyamat során nem lesz szinkronizálási tevékenység, azaz egyetlen elem sem lesz letöltve a Felhőbeli végpontról a kiszolgálói végpontra, és egyetlen elem sem lesz feltöltve a kiszolgálói végpontról a Felhőbeli végpontra. A szinkronizálási tevékenység akkor folytatódik, amikor a kezdeti Felhőbeli változás enumerálása befejeződik.
A teljesítmény sebessége 20 objektum másodpercenként. Az ügyfelek a Felhőbeli megosztásban lévő elemek számának meghatározásával, valamint a következő képletek használatával tudják megbecsülni a Felhőbeli módosítások számbavételének befejezéséhez szükséges időt. 

   **Kezdeti Felhőbeli számbavétel időpontja (napokban) = (objektumok száma a Felhőbeli végpontban)/(20 * 60 * 60 * 24)**

A **Windows Serverről az Azure-ba irányuló adatok kezdeti szinkronizálása**: sok Azure file Sync üzemelő példány üres Azure-fájlmegosztás használatával kezdődik, mert az összes adatok a Windows Serveren vannak. Ezekben az esetekben a felhő kezdeti változásának számbavétele gyors, és a legtöbb időt a Windows-kiszolgálóról az Azure-fájlmegosztás (ok) ra való szinkronizálás során kell kitölteni. 

Míg a Sync adatok feltöltése az Azure-fájlmegosztásba, a helyi fájlkiszolgálón nincs leállás, és a rendszergazdák [hálózati korlátozásokat](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-registration#set-azure-file-sync-network-limits) állíthatnak be a háttérbeli adatok feltöltéséhez használt sávszélesség korlátozására.

A kezdeti szinkronizálást a szinkronizálási csoport másodpercenkénti 20 fájljának kezdeti feltöltési sebessége korlátozza. Az ügyfelek a következő képletekkel tudják megbecsülni az összes adat Azure-ba való feltöltésének időpontját napokban:  

   **A fájlok szinkronizálási csoportba való feltöltésének időpontja (napokban) = (objektumok száma a kiszolgálói végpontban)/(20 * 60 * 60 * 24)**

Az adatok több kiszolgálói végpontba és szinkronizálási csoportba való felosztása felgyorsíthatja ezt a kezdeti adatfeltöltést, mivel a feltöltés párhuzamosan hajtható végre több szinkronizálási csoport számára, másodpercenként 20 elemnél. Így a két szinkronizálási csoport a másodpercenként 40-as összesített sebességgel fut. A teljes befejezési idő az a szinkronizálási csoportra vonatkozó becsült idő, amelyet a legtöbb fájl szinkronizál.

**Névtér letöltési átviteli sebessége** Ha új kiszolgálói végpontot ad hozzá egy meglévő szinkronizálási csoporthoz, a Azure File Sync ügynök nem tölti le a fájl tartalmát a Felhőbeli végpontból. Először szinkronizálja a teljes névteret, majd elindítja a háttérben való visszahívást, hogy letöltse a fájlokat, akár teljes egészében, akár a felhőalapú rétegek engedélyezése esetén a kiszolgálói végponton beállított felhő-előállítási házirendhez.

| Folyamatban lévő szinkronizálás  | Részletek  |
|-|--|
| Szinkronizált objektumok száma| 125 000 objektum (~ 1%-os forgalom) |
| Adatkészlet mérete| 50 GiB |
| Fájlméret átlagos mérete | ~ 500 KiB |
| Feltöltési sebesség | 20 objektum másodpercenként/szinkronizálási csoportonként |
| Teljes letöltési teljesítmény * | 60 objektum/másodperc |

* Ha a felhő-rétegek engedélyezve vannak, a jobb teljesítmény várható, mivel csak néhány fájl töltődik le. Azure File Sync csak akkor tölti le a gyorsítótárazott fájlok mennyiségét, ha azok bármelyik végponton módosulnak. Bármely rétegű vagy újonnan létrehozott fájl esetében az ügynök nem tölti le a fájlokat, hanem csak az összes kiszolgálói végpontra szinkronizálja a névteret. Az ügynök támogatja a többplatformos fájlok részleges letöltését is, mivel azok a felhasználó számára érhetők el. 

> [!Note]  
> A fenti számok nem utalnak arra, hogy milyen teljesítményt fog tapasztalni. A tényleges teljesítmény több tényezőtől is függ, ahogy az a szakasz elején szerepel.

Az üzembe helyezésre vonatkozó általános útmutatóként vegye figyelembe a következőket:

- Az objektum átviteli sebessége körülbelül a kiszolgálón lévő szinkronizálási csoportok számával arányos. Az adatok több szinkronizálási csoportba való felosztása a kiszolgálókon jobb átviteli sebességet eredményez, amelyet a kiszolgáló és a hálózat is korlátoz.
- Az objektum átviteli sebessége fordítottan arányos a MiB másodpercenkénti átviteli sebességével. Kisebb fájlok esetében a másodpercenként feldolgozott objektumok száma, de a másodpercenkénti adatátviteli sebesség tekintetében nagyobb átviteli sebesség fog megjelenni. Ezzel szemben a nagyobb fájlok esetében kevesebb, másodpercenként feldolgozott objektum lesz feldolgozva, de másodpercenként nagyobb a MiB-átviteli sebesség. A MiB/másodperc átviteli sebességet a Azure Files méretezési célok korlátozzák.

## <a name="see-also"></a>Lásd még
- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
