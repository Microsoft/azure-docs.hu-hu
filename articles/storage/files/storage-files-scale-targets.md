---
title: Az Azure Files méretezhetőségi és teljesítménycéljai
description: Ismerje meg az átviteli sebesség skálázhatósági és teljesítménycéljait Azure Files beleértve a kapacitást, a kérelmek sebességét, valamint a bejövő és kimenő sávszélesség korlátait.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bdcee6b8ced45dba34309724e5a634cbb60a6d37
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818817"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure Files méretezhetőségi és teljesítménycéljai
[Azure Files](storage-files-introduction.md) szolgáltatás teljes körűen felügyelt felhőbeli fájlmegosztásokat kínál, amelyek az SMB- és NFS-fájlrendszer protokolljain keresztül érhetők el. Ez a cikk a méretezési és teljesítménycélokat ismerteti a Azure Files és Azure File Sync.

Az itt felsorolt skálázhatósági és teljesítménycélok magas szintű célok, de az üzemelő példány más változói is hatással lehetnek rá. Előfordulhat például, hogy egy fájl átviteli sebességét a rendelkezésre álló hálózati sávszélesség is korlátozza, nem csak az Azure-fájlmegosztásokat üzemeltető kiszolgálók. Erősen ajánlott a használati minta tesztelése annak megállapításához, hogy a Azure Files skálázhatósága és teljesítménye megfelel-e a követelményeknek. Emellett elkötelezettek vagyunk a korlátok időveli növelése mellett. 

## <a name="azure-files-scale-targets"></a>Azure Files – skálázási célok
Az Azure-fájlmegosztások tárfiókban vannak üzembe osztva, amelyek a tárolók közös készletét képviselő legfelső szintű objektumok. Ez a tárolókészlet több fájlmegosztás üzembe helyezésére is használható. Ezért három kategóriát érdemes figyelembe venni: tárfiókok, Azure-fájlmegosztások és fájlok.

### <a name="storage-account-scale-targets"></a>Tárfiókok méretezési céljai
Az Azure többféle tárfióktípust támogat az ügyfelek különböző tárolási forgatókönyvei esetében, de a tárfiókok két fő típusa Azure Files. A létrehozni kívánt tárfiók típusa attól függ, hogy standard vagy prémium szintű fájlmegosztást szeretne létrehozni: 

- **Általános célú 2- (GPv2-)** tárfiókok: A GPv2-tárfiókok lehetővé teszik az Azure-fájlmegosztások standard/merevlemez-alapú (HDD-alapú) hardveren való üzembe helyezését. Az Azure-fájlmegosztások tárolása mellett a GPv2-tárfiókok más tárolási erőforrásokat, például blobtárolókat, üzenetsorokat vagy táblákat is tárolnak. A fájlmegosztások üzembe helyezhetők a tranzakcióoptimalált (alapértelmezett), a forró vagy a hűtési szinten.

- **FileStorage** tárfiókok: A FileStorage tárfiókok lehetővé teszik azure-fájlmegosztások prémium/SSD-alapú (SSD-alapú) hardveren való üzembe helyezését. A FileStorage-fiókok csak Azure-fájlmegosztások tárolására használhatók; A FileStorage-fiókban nem helyezhetők üzembe más tárolási erőforrások (blobtárolók, üzenetsorok, táblák stb.).

| Attribútum | GPv2-tárfiókok (standard) | FileStorage tárfiókok (prémium) |
|-|-|-|
| Tárfiókok száma régiónként előfizetésenként | 250 | 250 |
| Tárfiók maximális kapacitása | 5 PiB<sup>1</sup> | 100 TiB (kiépítve) |
| Fájlmegosztások maximális száma | Korlátlan | Korlátlan, az összes megosztás teljes kiépített méretének a tárfiók maximális kapacitásábanál kisebbnek kell lennie a maximális értéknél |
| Egyidejű kérelmek maximális száma | 20 000 IOPS<sup>1</sup> | 100 000 IOPS |
| Maximális bejövő forgalom | <ul><li>USA/Európa: 9536 MiB/s<sup>1</sup></li><li>Egyéb régiók (LRS/ZRS): 9536 MiB/mp<sup>1</sup></li><li>Egyéb régiók (GRS): 4768 GiB/mp<sup>1</sup></li></ul> | 4136 MiB/s |
| Maximális bejövő forgalom | 47 683 MiB/s<sup>1</sup> | 6204 MiB/s |
| Virtuális hálózati szabályok maximális száma | 200 | 200 |
| IP-címekre vonatkozó szabályok maximális száma | 200 | 200 |
| Felügyeleti olvasási műveletek | 5 percenként 800 | 5 percenként 800 |
| Felügyeleti írási műveletek | Másodpercenként 10/1200 óránként | Másodpercenként 10/1200 óránként |
| Felügyeletilista-műveletek | 5 percenként 100 | 5 percenként 100 |

<sup>1</sup> Az általános célú 2. verziójú tárfiókok magasabb kapacitáskorlátokat és kérések szerint nagyobb bejövő forgalomkorlátokat támogatnak. A fiókkorlátok növelésének igénylése érdekében lépjen kapcsolatba a [Azure ügyfélszolgálata.](https://azure.microsoft.com/support/faq/)

### <a name="azure-file-share-scale-targets"></a>Azure-fájlmegosztások méretezési céljai
| Attribútum | Standard<sup>fájlmegosztások 1</sup> | Prémium fájlmegosztások |
|-|-|-|
| Fájlmegosztás minimális mérete | Nincs minimum | 100 GiB (kiépítve) |
| Kiépített méret növelése/csökkentése egység | N/A | 1 GiB |
| Fájlmegosztás maximális mérete | <ul><li>100 TiB, engedélyezett nagyméretű fájlmegosztási<sup>funkcióval 2</sup></li><li>5 TiB, alapértelmezett</li></ul> | 100 TiB |
| Fájlmegosztásban található fájlok maximális száma | Nincs korlát | Nincs korlát |
| Maximális kérelemarány (maximális IOPS) | <ul><li>10 000, engedélyezett nagy fájlmegosztási<sup>funkcióval 2</sup></li><li>1000 vagy 100 kérés/100 ms, alapértelmezés szerint</li></ul> | <ul><li>Alap IOPS: 400 + 1 IOPS GiB-onként, legfeljebb 100 000-ig</li><li>IOPS-megll-ing: Maximum (4000,3x IOPS GiB-nként), legfeljebb 100 000</li></ul> |
| Egyetlen fájlmegosztás maximális bejövő forgaloma | <ul><li>Legfeljebb 300 MiB/s, engedélyezett nagy fájlmegosztási funkcióval<sup>2</sup></li><li>Legfeljebb 60 MiB/s, alapértelmezés szerint</li></ul> | 40 MiB/s + 0,04 * kiépítve GiB |
| Egyetlen fájlmegosztás maximális bejövő forgaloma | <ul><li>Legfeljebb 300 MiB/s, engedélyezett nagy fájlmegosztási funkcióval<sup>2</sup></li><li>Legfeljebb 60 MiB/s, alapértelmezés szerint</li></ul> | 60 MiB/s + 0,06 * kiépítve GiB |
| Megosztási pillanatképek maximális száma | 200 pillanatkép | 200 pillanatkép |
| Objektum (könyvtárak és fájlok) nevének maximális hossza | 2048 karakter | 2048 karakter |
| Maximális elérésiút-név összetevő (az \A\B\C\D elérési úton minden betű egy összetevő) | 255 karakter | 255 karakter |
| Kapcsolati korlát (csak NFS) | N/A | 178 |
| Többcsatornás SMB-csatornák maximális száma | N.A. | 4 |
| Tárolt hozzáférési házirendek maximális száma fájlmegosztásonként | 5 | 5 |

<sup>1</sup> A standard fájlmegosztások korlátai mindhárom standard fájlmegosztáshoz elérhető szintre vonatkoznak: a tranzakcióoptimalált, a gyors és a hűtési szintre.

<sup>2</sup> A standard fájlmegosztások alapértelmezett értéke [](./storage-files-how-to-create-large-file-share.md) 5 TiB. A standard fájlmegosztások 100 TiB-ra való növelésével kapcsolatos részletekért lásd: Nagyméretű fájlmegosztások engedélyezése és létrehozása.

### <a name="file-scale-targets"></a>Fájlskálacélok
| Attribútum | Standard fájlmegosztásban lévő fájlok  | Prémium fájlmegosztások fájljai  |
|-|-|-|
| Maximális fájlméret | 4 TiB | 4 TiB |
| Egyidejű kérelmek maximális száma | 1000 IOPS | Legfeljebb 8000<sup>1</sup> |
| Fájl maximális bejövő forgaloma | 60 MiB/s | 200 MiB/s (akár 1 GiB/s többcsatornás SMB előzetes verzióval)<sup>2</sup>|
| Fájl maximális bejövő forgaloma | 60 MiB/s | 300 MiB/s (akár 1 GiB/s többcsatornás SMB előzetes verzióval)<sup>2</sup> |
| Egyidejű leírók maximális száma | 2000 leíró | 2000 leíró  |

<sup>1 Az olvasási és írási I/O-kra vonatkozik (általában kisebb I/O-méretek 64 KiB vagy kisebbek). Az olvasási és</sup> írási műveleteken kívül a metaadat-műveletek alacsonyabbak is lehetnek. 
 2 A gép hálózati korlátai, a rendelkezésre <sup>álló sávszélesség, az I/O-méretek, az üzenetsor mélysége és egyéb tényezők vonatkoznak rá. Részletekért lásd: [Többcsatornás SMB-teljesítmény.](./storage-files-smb-multichannel-performance.md)</sup>

## <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync skálázási céljai
Az alábbi táblázat a Microsoft tesztelésének határait, valamint azt is jelzi, hogy mely célok a nehéz korlátok:

| Erőforrás | Cél | Korlátozás |
|----------|--------------|------------|
| Társzinkronizálási szolgáltatások régiónként | 100 Társzinkronizálási szolgáltatás | Yes |
| Csoportok szinkronizálása Társzinkronizálási szolgáltatásonként | 200 szinkronizálási csoport | Yes |
| Regisztrált kiszolgálók társzinkronizálási szolgáltatásonként | 99 kiszolgáló | Yes |
| Felhőbeli végpontok szinkronizálási csoportonként | 1 felhőbeli végpont | Yes |
| Kiszolgálóvégpont szinkronizálási csoportonként | 100 kiszolgálóvégpont | Yes |
| Kiszolgálóvégpont kiszolgálónként | 30 kiszolgálóvégpont | Yes |
| Fájlrendszerobjektumok (könyvtárak és fájlok) szinkronizálási csoportonként | 100 millió objektum | No |
| Fájlrendszer-objektumok (könyvtárak és fájlok) maximális száma egy könyvtárban | 5 millió objektum | Yes |
| Objektum (könyvtárak és fájlok) maximális biztonsági leírómérete | 64 KiB | Yes |
| Fájlméret | 100 GiB | No |
| A rétegzett fájlok minimális mérete | V9 és újabb: A fájlrendszerfürt mérete (kettős fájlrendszerfürtméret) alapján. Ha például a fájlrendszerfürt mérete 4 KiB, a fájl minimális mérete 8 KiB lesz.<br> V8 és régebbi: 64 KiB  | Yes |

> [!Note]  
> A Azure File Sync végpontok egy Azure-fájlmegosztás méretének megfelelő méretre méretezhetők fel. Ha eléri az Azure-fájlmegosztás méretkorlátját, a szinkronizálás nem fog tudni működni.

### <a name="azure-file-sync-performance-metrics"></a>Az Azure File Sync teljesítménymetrikái
Mivel a Azure File Sync-ügynök az Azure-fájlmegosztáshoz csatlakozó Windows Server-gépen fut, a hatékony szinkronizálási teljesítmény az infrastruktúrában számos tényezőtől függ: a Windows Server és a mögöttes lemezkonfiguráció, a kiszolgáló és az Azure Storage közötti hálózati sávszélességtől, a fájlmérettől, az adatkészlet teljes méretétől és az adatkészleten végzett tevékenységtől. Mivel Azure File Sync a fájl szintjén működik, a Azure File Sync-alapú megoldások teljesítményjellemzőit jobban megmérjük a másodpercenként feldolgozott objektumok (fájlok és könyvtárak) számában.

A Azure File Sync két szakaszban kritikus fontosságú a teljesítmény:

1. **Kezdeti egyszeres üzembe helyezés:** A kezdeti kiépítés teljesítményének optimalizálásához tekintse meg az [Azure File Sync](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) üzembe helyezési adatokkal való bevezetést.
2. **Folyamatban lévő szinkronizálás:** Miután az adatokat először átküldte az Azure-fájlmegosztásokra, a Azure File Sync több végpontot is szinkronban tart.

Az üzembe helyezés tervezésének segítése érdekében az alábbiakban a konfigurációval üzemelő rendszer belső tesztelése során megfigyelt eredményeket olvashatja.

| Rendszerkonfiguráció | Részletek |
|-|-|
| CPU | 64 virtuális mag 64 MiB L3 gyorsítótárral |
| Memória | 128 GiB |
| Lemez | SAS-lemezek RAID 10-zel, akkumulátoros gyorsítótárral |
| Network (Hálózat) | 1 Gb/s-os hálózat |
| Számítási feladat | általános célú fájlkiszolgáló|

| Kezdeti egyszeres kiépítés  | Részletek |
|-|-|
| Objektumok száma | 25 millió objektum |
| Adatkészlet mérete| ~4,7 TiB |
| Átlagos fájlméret | ~200 KiB (legnagyobb fájl: 100 GiB) |
| Kezdeti felhőváltozás-enumerálás | Másodpercenként 20 objektum  |
| Átviteli sebesség feltöltése | 20 objektum másodpercenként szinkronizálási csoportonként |
| Névtér letöltési átviteli sebesség | Másodpercenként 400 objektum |

### <a name="initial-one-time-provisioning"></a>Kezdeti, egyszeres kiépítés

**Kezdeti felhőváltozás-enumerálás:** Új szinkronizálási csoport létrehozásakor a kezdeti felhőváltozás-enumerálás az első lépés, amely végre lesz hajtva. Ebben a folyamatban a rendszer számba veszi az Azure-fájlmegosztás összes elemét. A folyamat során nem lesz szinkronizálási tevékenység, azaz a rendszer nem tölt le elemeket a felhőbeli végpontról a kiszolgálóvégpontra, és nem tölt fel elemeket a kiszolgálóvégpontról a felhőbeli végpontra. A szinkronizálási tevékenység a kezdeti felhőváltozás-enumerálás befejezése után folytatódik.
A teljesítmény sebessége másodpercenként 20 objektum. Az ügyfelek megbecsülhetik a kezdeti felhőbeli változás enumerálásának befejezéséhez szükséges időt, ha meghatározzák a felhőalapú megosztás elemeinek számát, és a következő képlettel lekérték az időt napokban. 

   **A kezdeti felhőbeli enumerálás ideje (napokban) = (objektumok száma a felhőbeli végponton)/(20 * 60 * 60 * 24)**

**Adatok kezdeti szinkronizálása a Windows Serverről** az Azure-fájlmegosztásba: Számos Azure File Sync üzemelő példány egy üres Azure-fájlmegosztással kezdődik, mert minden adat a Windows Serveren található. Ezekben az esetekben a kezdeti felhőváltozás-enumerálás gyors, és az idő nagy részét a Windows Server változásainak Azure-fájlmegosztásokkal való szinkronizálása fogja el. 

Bár a szinkronizálás feltölti az adatokat az Azure-fájlmegosztásba, nincs [](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) állásidő a helyi fájlkiszolgálón, és a rendszergazdák hálózati korlátokat is beállíthatják a háttéradatok feltöltéséhez használt sávszélesség korlátozására.

A kezdeti szinkronizálást általában a szinkronizálási csoportonkénti 20 fájl kezdeti feltöltési sebessége korlátozza. Az ügyfelek a következő képlettel megbecsülheti az összes adat azure-ba való feltöltésének idejét, hogy a napokban meg tudja kapni az időt:  

   **Fájlok szinkronizálási csoportba való feltöltésének ideje (napokban) = (Kiszolgálóvégpont objektumszáma)/(20 * 60 * 60 * 24)**

Az adatok több kiszolgálóvégpontra és szinkronizálási csoportra való felosztása felgyorsíthatja a kezdeti adatfeltöltést, mivel a feltöltés párhuzamosan, több szinkronizálási csoport esetében másodpercenként 20 elemet tartalmaz. Tehát két szinkronizálási csoport 40 elem/másodperces kombinált sebességgel futna. A teljes idő a szinkronizálási csoport időbecslése, amely a legtöbb szinkronizálni szükséges fájlt tartalmazza.

**A névtér letöltési átviteli sebesség** Amikor új kiszolgálóvégpontot ad hozzá egy meglévő szinkronizálási csoporthoz, a Azure File Sync-ügynök nem tölt le semmilyen fájltartalmat a felhőbeli végpontról. Először szinkronizálja a teljes névteret, majd kiváltja a háttér-visszahívási funkciót a fájlok teljes letöltéséhez, vagy – ha a felhőbeli rétegezés engedélyezve van – a kiszolgálóvégponton beállított felhőbeli rétegezés házirendjére.

| Folyamatban lévő szinkronizálás  | Részletek  |
|-|--|
| Szinkronizált objektumok száma| 125 000 objektum (~1%-os adatváltozás) |
| Adatkészlet mérete| 50 GiB |
| Átlagos fájlméret | ~500 KiB |
| Átviteli sebesség feltöltése | 20 objektum másodpercenként szinkronizálási csoportonként |
| Teljes letöltési átviteli sebesség* | Másodpercenként 60 objektum |

*Ha a felhőbeli rétegezés engedélyezve van, valószínűleg jobb teljesítményt fog látni, mivel a rendszer csak néhány fájladatot tölt le. Azure File Sync a gyorsítótárazott fájlok adatait csak akkor tölti le, ha bármelyik végponton módosultak. A rétegzett vagy újonnan létrehozott fájlok esetében az ügynök nem letölti a fájladatokat, hanem csak szinkronizálja a névteret az összes kiszolgálóvégponttal. Az ügynök támogatja a rétegzett fájlok részleges letöltését is, mivel a felhasználó hozzáfér. 

> [!Note]  
> A fenti számok nem jelzik a teljesítményt. A tényleges teljesítmény több tényezőtől is függ, ahogyan azt a szakasz elején vázoljuk.

Az üzembe helyezés általános útmutatójaként érdemes szem előtt tartania néhány dolgot:

- Az objektum átviteli sebességének aránya a kiszolgálón található szinkronizálási csoportok számához arányosan skálázható. Az adatok több szinkronizálási csoportra való felosztása jobb átviteli sebességet eredményez, amelyet a kiszolgáló és a hálózat is korlátoz.
- Az objektum átviteli sebességének arányosnak kell lennie a Másodpercenkénti MiB átviteli sebességével. Kisebb fájlok esetén magasabb átviteli sebességet fog tapasztalni a másodpercenként feldolgozott objektumok száma, de a Másodpercenkénti MiB-érték alacsonyabb. Ezzel szemben nagyobb fájlok esetén másodpercenként kevesebb objektum lesz feldolgozva, a másodpercenkénti MiB-sebesség viszont magasabb. A MiB másodpercenkénti átviteli sebességét a skálázható Azure Files korlátozza.

## <a name="see-also"></a>Lásd még
- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](../file-sync/file-sync-planning.md)