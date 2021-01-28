---
title: Az Apache HBase teljesítményproblémáinak elhárítása az Azure HDInsightban
description: Különböző Apache HBase Performance tuning-irányelvek és tippek az optimális teljesítmény az Azure HDInsight-on való beszerzéséhez.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942892"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Az Apache HBase teljesítményproblémáinak elhárítása az Azure HDInsightban

Ez a cikk a különböző Apache HBase teljesítmény-hangolási irányelveit és az Azure HDInsight optimális teljesítményének megkezdésére vonatkozó tippeket ismerteti. Ezek a tippek sok esetben az adott számítási feladattól, valamint az írási/olvasási/beolvasási mintáktól függenek. Mielőtt éles környezetben alkalmazza a konfigurációs módosításokat, alaposan tesztelje azokat.

## <a name="hbase-performance-insights"></a>HBase teljesítmény-felismerés

A legtöbb HBase-számítási feladat leggyakoribb szűk keresztmetszete az írási előre látható napló (WAL). Ez súlyosan befolyásolja az írási teljesítményt. A HDInsight HBase egy elkülönített tárolási modellel rendelkezik. Az Azure Storage-ban a rendszer távolról tárolja az adattárolást, bár a virtuális gépek üzemeltetik a régió kiszolgálóit. A legutóbb a WAL az Azure Storage-ba is lett írva. A HDInsight ez a szűk keresztmetszetet fokozza. A [gyorsított írási](./apache-hbase-accelerated-writes.md) funkció a probléma megoldására szolgál. A WAL-t az Azure prémium SSD által felügyelt lemezekre írja. Ez jelentősen hozzájárul az írási teljesítményhez, és számos olyan problémát nyújt, amely a nagy írási igényű munkaterhelések esetében is hasznos.

Az olvasási műveletek jelentős javításához használja a [Premium Block blob Storage fiókot](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) a távoli tárolóként. Ez a beállítás csak akkor lehetséges, ha a WAL funkció engedélyezve van.

## <a name="compaction"></a>Tömörítés

A tömörítés egy másik potenciális szűk keresztmetszet, amely alapvetően a Közösségben van elfogadva. Alapértelmezés szerint a nagyobb tömörítés le van tiltva a HDInsight HBase-fürtökön. A tömörítés le van tiltva, mert annak ellenére, hogy erőforrás-igényes folyamat, az ügyfelek teljes rugalmasságot biztosítanak a számítási feladatoknak megfelelően. Előfordulhat például, hogy a munkaidőn kívüli órákban ütemezhetik. Emellett az adatterület nem aggodalomra ad okot, mert a tárterület távoli (az Azure Storage által támogatott) helyett egy helyi Hadoop elosztott fájlrendszer (HDFS).

Az ügyfeleknek érdemes a nagy tömörítést ütemezniük. Ha nem végzik el a karbantartást, a tömörítés hosszú távon negatív hatással lesz az olvasási teljesítményre.

A vizsgálati műveletek esetében aggodalomra ad okot, hogy az 100 ezredmásodpercnél jóval nagyobb késést jelent. Ellenőrizze, hogy a fő tömörítés ütemezése pontosan megtörtént-e.

## <a name="apache-phoenix-workload"></a>Apache Phoenix munkaterhelés

A következő kérdések megválaszolásával jobban megismerheti az Apache Phoenix munkaterhelést:

* A rendszer az összes "olvasást" lefordítja a vizsgálatokba?
    * Ha igen, Mik a vizsgálatok jellemzői?
    * Optimalizálta a Phoenix Table sémáját ezekhez a vizsgálatokhoz, beleértve a megfelelő indexelést is?
* Használta az `EXPLAIN` utasítást, hogy megértse a lekérdezési terveket az "olvasások" létrehozásakor?
* Az írásai "upsert – kiválasztja"?
    * Ha igen, akkor is végeznek vizsgálatokat. A vizsgálatok átlaga körülbelül 100 milliszekundum, a HBase pedig 10 ezredmásodpercnél kevesebb időt vesz igénybe.  

## <a name="test-methodology-and-metrics-monitoring"></a>Tesztelési módszertan és metrikák figyelése

Ha olyan viszonyítási pontokat használ, mint például a Yahoo! A teljesítmény teszteléséhez és finomhangolásához szükséges teljesítményteszt-, JMeter-vagy Pherf-alapú felhő:

- Az ügyfélgépek nem lesznek szűk keresztmetszetek. Ehhez keresse meg a CPU-használatot az ügyfélszámítógépeken.

- Az ügyféloldali konfigurációk, például a szálak száma, megfelelően vannak hangolva az ügyfelek sávszélességének csökkentése érdekében.

- A tesztek eredményei pontosan és szisztematikusan vannak rögzítve.

Ha a lekérdezések hirtelen megkezdése sokkal rosszabb, mint korábban, ellenőrizze a lehetséges hibákat az alkalmazás kódjában. Hirtelen nagy mennyiségű érvénytelen adattal generál? Ha igen, növelheti az olvasási késéseket.

## <a name="migration-issues"></a>Migrálási problémák

Ha az Azure HDInsight-ba végez áttelepítést, győződjön meg arról, hogy a Migrálás szisztematikusan és pontosan, lehetőleg automatizáláson keresztül történik. Kerülje a manuális áttelepítést. Ügyeljen a következőkre:

- A tábla attribútumai pontosan települnek át. Az attribútumok tartalmazhatnak tömörítést, virágzási szűrőket stb.

- A Phoenix-táblák sós beállításai megfelelően vannak leképezve az új fürt méretére. Például a sós gyűjtők számának a fürtben lévő munkavégző csomópontok számának többszörösének kell lennie. A gyakori bepecsételés mértékének faktora pedig több is lehet.

## <a name="server-side-configuration-tunings"></a>Kiszolgálóoldali konfiguráció-hangolások

A HDInsight HBase a HFiles a távoli tárolóban tárolódnak. Gyorsítótár-hiány esetén a beolvasások díja magasabb, mint a helyszíni rendszerek esetében, mert a helyszíni rendszerekre vonatkozó adatokat a helyi HDFS támogatja. A legtöbb esetben a HBase cache-gyorsítótárak intelligens használata (a gyorsítótár és a gyűjtő gyorsítótárának blokkolása) úgy lett kialakítva, hogy kikerülje ezt a problémát. Abban az esetben, ha a probléma nem kerül kijátszásra, a prémium szintű blokk blob-fiók használatával segíthet a probléma megoldásában. A Windows Azure Storage Blob illesztőprogram bizonyos tulajdonságokra támaszkodik, például az `fs.azure.read.request.size` adatblokkokban tárolt adatokat az olvasási mód (szekvenciális és véletlenszerű) alapján, így előfordulhat, hogy továbbra is magasabb késésű példányok találhatók az olvasásokkal. A empirikus kísérletek segítségével úgy találtuk, hogy az olvasási kérelmek blokkjának mérete ( `fs.azure.read.request.size` ) 512 kb-ra van állítva, és a HBase táblák blokk-méretének megfelelő mérettel egyező méretben állítja elő a legjobb eredményt a gyakorlatban.

A legtöbb nagy méretű csomópontos fürtök esetében a HDInsight HBase a `bucketcache` virtuális géphez csatolt helyi prémium SSD fájlját tartalmazza `regionservers` . Előfordulhat, hogy a kikapcsolt halom gyorsítótár használata némi javulást eredményezhet. Ez a megkerülő megoldás a rendelkezésre álló memória korlátozásával, illetve a fájl-alapú gyorsítótárnál kisebb mérettel rendelkezik, így előfordulhat, hogy nem mindig a legjobb választás.

Az alábbiakban néhány, az általunk hangolt paraméterek közül néhányat ismertetünk, amelyek többek között a következőkhöz nyújtanak segítséget:

- Növelje `memstore` a méretet az alapértelmezett 128 MB és 256 MB között. Ez a beállítás általában nagy írási helyzetekben ajánlott.

- Növelje a tömörítéshez dedikált szálak számát az alapértelmezett **1** és **4** közötti értékkel. Ez a beállítás akkor fontos, ha betartjuk a gyakori kisebb tömörítést.

- Ne blokkolja `memstore` a kiürítést a tároló korlátja miatt. A puffer megadásához növelje a `Hbase.hstore.blockingStoreFiles` beállítást **100**-re.

- A kiürítések szabályozásához használja az alábbi beállításokat:

    - `Hbase.regionserver.maxlogs`: **140** (a a Wal-korlátok miatt elkerüli a kiürítést)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-specifikus konfigurációk a szál-készlet hangolásához:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Egyéb Phoenix-specifikus konfigurációk:

    - `Phoenix.rpc.index.handler.count`: **50** (nagy vagy sok indexelési keresés esetén)

    - `Phoenix.stats.updateFrequency`: **1 óra**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 óra**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC-időtúllépések: **3 perc**

   - Az RPC-időtúllépések közé tartozik a HBase RPC-időtúllépés, a HBase-ügyfél képolvasó-időtúllépése és a Phoenix-lekérdezés időtúllépése. 
   - Győződjön meg arról, hogy a `hbase.client.scanner.caching` paraméter ugyanarra az értékre van beállítva a kiszolgáló végén és az ügyfél végén is. Ha ezek nem azonosak, ez a beállítás a szolgáltatáshoz kapcsolódó ügyféloldali hibákhoz vezet `OutOfOrderScannerException` . Ezt a beállítást kis értékre kell állítani a nagyméretű keresések esetében. Ezt az értéket **100**-re állítjuk be.

## <a name="other-considerations"></a>További szempontok

A következő további paramétereket érdemes megfontolni a hangoláshoz:

- `Hbase.rs.cacheblocksonwrite` – Alapértelmezés szerint a HDI esetében ez a beállítás **igaz** értékre van állítva.

- Beállítások, amelyek lehetővé teszik a kisebb tömörítés késleltetését később.

- A kísérleti beállítások, például az olvasási és írási kérelmek számára fenntartott várólisták százalékos arányának módosítása.

## <a name="next-steps"></a>Következő lépések

Ha a probléma továbbra is megoldatlan marad, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) . Ez a hivatalos Microsoft Azure fiók a felhasználói élmény javításához. Összekapcsolja az Azure-Közösséget a megfelelő erőforrásokkal: válaszokkal, támogatással és szakértőkkel.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. A Microsoft Azure-előfizetés az előfizetés-kezeléshez és a számlázási támogatáshoz biztosít hozzáférést, a technikai támogatás pedig az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.