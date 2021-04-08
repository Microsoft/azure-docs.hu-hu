---
title: Ajánlott eljárások a Azure Data Lake Storage Gen1 használatához | Microsoft Docs
description: Ismerje meg az adatfeldolgozással, a dátummal és a biztonsággal kapcsolatos ajánlott eljárásokat, valamint a Azure Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store) használatához kapcsolódó teljesítményt
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452219"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Ajánlott eljárások Azure Data Lake Storage Gen1 használatához

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Ebben a cikkben megismerheti a Azure Data Lake Storage Gen1 használatának ajánlott eljárásait és szempontjait. Ez a cikk a Data Lake Storage Gen1 biztonságával, teljesítményével, rugalmasságával és figyelésével kapcsolatos információkat tartalmaz. Data Lake Storage Gen1 előtt a valóban big data olyan szolgáltatásokkal, mint az Azure HDInsight, összetett volt. Több blob Storage-fiókba kellett átméreteznie az adatmennyiséget, hogy a petabyte tárolás és az optimális teljesítmény legyen elérhető. A Data Lake Storage Gen1 a méret és a teljesítmény nagy részét eltávolítja a rendszer. Azonban továbbra is vannak olyan megfontolások, amelyeket ez a cikk ismertet, hogy a lehető legjobb teljesítményt kapja Data Lake Storage Gen1.

## <a name="security-considerations"></a>Biztonsági szempontok

A Azure Data Lake Storage Gen1 a POSIX hozzáférés-vezérlést, valamint az Azure Active Directory (Azure AD) felhasználók, csoportok és egyszerű szolgáltatások részletes naplózását kínálja. Ezek a hozzáférés-vezérlések megadhatók meglévő fájlokhoz és mappákhoz. A hozzáférés-vezérléssel az új fájlokra vagy mappákra alkalmazható alapértelmezett beállítások is létrehozhatók. Ha az engedélyek meglévő mappákra és alárendelt objektumokra vannak beállítva, az engedélyeket minden objektumon rekurzív módon kell propagálni. Ha nagy számú fájl van, az engedélyek propagálása hosszú időt is igénybe vehet. Az igénybe vett idő a másodpercenként feldolgozott 30-50 objektum közötti tartományba esik. Ezért tervezze meg megfelelően a mappa felépítését és a felhasználói csoportokat. Ellenkező esetben előfordulhat, hogy az adataival való munka során nem várt késések és problémák merülnek fel.

Tegyük fel, hogy van egy 100 000 gyermekobjektum nevű mappája. Ha a másodpercenként feldolgozott 30 objektum alsó határát veszi igénybe, a teljes mappa engedélyeinek frissítése akár egy órát is igénybe vehet. Data Lake Storage Gen1 hozzáférés-vezérlési listákkal kapcsolatos további részletek a [Azure Data Lake Storage Gen1 hozzáférés-vezérlésében](data-lake-store-access-control.md)érhetők el. Az ACL-ek rekurzív hozzárendelésével kapcsolatos jobb teljesítmény érdekében használhatja a Azure Data Lake Command-Line eszközt. Az eszköz több szálat és rekurzív navigációs logikát hoz létre, amellyel gyorsan alkalmazhatja az ACL-eket több millió fájlra. Az eszköz Linux és Windows rendszerekhez érhető el, és az eszköz [dokumentációja](https://github.com/Azure/data-lake-adlstool) és [letöltése](https://aka.ms/adlstool-download) megtalálható a githubon. Ugyanezek a teljesítménnyel kapcsolatos újítások a Data Lake Storage Gen1 [.net](data-lake-store-data-operations-net-sdk.md) -és [Java](data-lake-store-get-started-java-sdk.md) SDK-val írt saját eszközeivel is engedélyezhetők.

### <a name="use-security-groups-versus-individual-users"></a>Biztonsági csoportok és egyéni felhasználók együttes használata

A Data Lake Storage Gen1 big datajának használatakor a legvalószínűbb, hogy egy egyszerű szolgáltatásnév lehetővé teszi az olyan szolgáltatások használatát, mint például az Azure HDInsight az adatkezeléshez. Előfordulhatnak azonban olyan esetek, amikor az egyes felhasználóknak is szükségük van az adathozzáférésre. Ilyen esetekben Azure Active Directory [biztonsági csoportokat](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) kell használnia, ahelyett, hogy egyéni felhasználókat rendel hozzá a mappákhoz és fájlokhoz.

Ha a biztonsági csoporthoz engedélyek vannak rendelve, a csoportba tartozó felhasználók hozzáadására vagy eltávolítására nincs szükség frissítésre Data Lake Storage Gen1. Ez azt is lehetővé teszi, hogy ne lépje túl a [32-es és az alapértelmezett ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) -EK korlátját (Ez magában foglalja az összes fájlhoz és mappához tartozó négy POSIX-stílusú ACL-t: [a tulajdonos felhasználó](data-lake-store-access-control.md#the-owning-user), [a tulajdonos csoport](data-lake-store-access-control.md#the-owning-group), [a maszk](data-lake-store-access-control.md#the-mask)és más).

### <a name="security-for-groups"></a>Csoportok biztonsága

Ahogy azt már említettük, a felhasználóknak a Data Lake Storage Gen1hoz való hozzáférésre van szükségük, Azure Active Directory biztonsági csoportok használata ajánlott. Előfordulhat, hogy néhány javasolt csoport a **ReadOnlyUsers**, a **WriteAccessUsers** és a **FullAccessUsers** a fiók gyökeréhez, és akár külön is a legfontosabb almappákhoz. Ha a felhasználók más várható felhasználói csoportjai is hozzáadhatók, de még nincsenek azonosítva, érdemes lehet olyan dummy biztonsági csoportokat létrehozni, amelyek bizonyos mappákhoz férnek hozzá. A biztonsági csoport használatával biztosíthatja, hogy a későbbiekben ne kelljen hosszú feldolgozási időt igényelni új engedélyek több ezer fájlhoz való hozzárendeléséhez.

### <a name="security-for-service-principals"></a>Az egyszerű szolgáltatások biztonsága

Azure Active Directory egyszerű szolgáltatásokat általában olyan szolgáltatások használják, mint például az Azure HDInsight a Data Lake Storage Gen1ban lévő adateléréshez. A különböző számítási feladatokhoz tartozó hozzáférési követelményektől függően előfordulhat, hogy a szervezeten belüli és kívüli biztonság biztosításához néhány szempontot figyelembe kell venni. Sok ügyfél esetében előfordulhat, hogy egy Azure Active Directory egyszerű szolgáltatás megfelelő, és a Data Lake Storage Gen1 fiók gyökerében teljes jogosultsággal rendelkezik. Más ügyfeleknél több olyan fürtre lehet szükség, amely különböző egyszerű szolgáltatásokkal rendelkezik, ahol az egyik fürt teljes hozzáféréssel rendelkezik az adathoz, és egy másik fürt, amely csak olvasási hozzáféréssel rendelkezik. Csakúgy, mint a biztonsági csoportok esetében, érdemes lehet egyszerű szolgáltatásnevet létrehozni az egyes várható (olvasási, írási, teljes) forgatókönyvekhez Data Lake Storage Gen1 fiók létrehozása után.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>A Data Lake Storage Gen1 tűzfal engedélyezése az Azure-szolgáltatás elérésével

Data Lake Storage Gen1 támogatja a tűzfal bekapcsolásának lehetőségét, és csak az Azure-szolgáltatásokhoz való hozzáférést korlátozza, ami a kisebb támadási vektorok számára ajánlott. A tűzfal engedélyezhető a Azure Portal Data Lake Storage Gen1 fiókjában **a tűzfalon** a tűzfal  >  **engedélyezése (on)** az  >  **Azure-szolgáltatások elérésének engedélyezése** lehetőséggel.

![Tűzfalbeállítások a Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Tűzfalbeállítások a Data Lake Storage Gen1")

Ha a tűzfal engedélyezve van, csak az Azure-szolgáltatások (például a HDInsight, a Data Factory, az Azure szinapszis Analytics stb.) férhetnek hozzá a Data Lake Storage Gen1hoz. Az Azure által használt belső hálózati címfordítás miatt a Data Lake Storage Gen1 tűzfal nem támogatja az IP-címek korlátozását, és csak az Azure-on kívüli végpontokra, például a helyszíni szolgáltatásokra vonatkozik.

## <a name="performance-and-scale-considerations"></a>Teljesítmény-és méretezési szempontok

Data Lake Storage Gen1 egyik leghatékonyabb funkciója az, hogy eltávolítja az adatátviteli sebességre vonatkozó korlátokat. A korlátok eltávolítása lehetővé teszi, hogy az ügyfelek az adatméretük növelésével és a teljesítményre vonatkozó követelmények nélkül is növekednek. Data Lake Storage Gen1 teljesítmény optimalizálásának egyik legfontosabb szempontja az, hogy a lehető leghatékonyabban hajtja végre az adott párhuzamosságot.

### <a name="improve-throughput-with-parallelism"></a>Az átviteli sebesség növelése a párhuzamosságtal

A legoptimálisabb olvasási/írási átviteli sebesség érdekében érdemes 8-12 szálat adni egy mag számára. Ennek az az oka, hogy egyetlen szálon blokkolja az olvasási/írási műveleteket, és több szál is lehetővé teszi a nagyobb Egyidejűség használatát a virtuális gépen. Annak érdekében, hogy a szintek kifogástalanok legyenek, és a párhuzamosságok megnövelhető legyen, ügyeljen a virtuális gép CPU-kihasználtságának figyelésére.

### <a name="avoid-small-file-sizes"></a>Kisméretű fájlméret elkerülése

A POSIX-engedélyek és a Data Lake Storage Gen1 naplózása olyan terheléssel jár, amely nyilvánvalóvá válik, ha sok kis fájllal dolgozik. Az ajánlott eljárás az, ha az adatait nagyobb fájlokba kívánja osztani, és több ezer vagy több millió kis fájlt ír Data Lake Storage Gen1ba. A kisméretű fájlok méretének elkerüléséhez több előnye is van, például:

* A hitelesítési ellenőrzések csökkentése több fájl között
* Csökkentett nyitott fájlok kapcsolatai
* Gyorsabb másolás/replikálás
* Kevesebb fájl feldolgozása Data Lake Storage Gen1 POSIX-engedélyek frissítésekor

Attól függően, hogy a szolgáltatások és a munkaterhelések hogyan használják az adatmennyiséget, a fájlok megfelelő mérete 256 MB vagy nagyobb. Ha a fájlméretet nem lehet kötegbe állítani a Data Lake Storage Gen1be való leszálláskor, akkor egy különálló tömörítési feladatokkal is rendelkezhet, amelyekkel a fájlok nagyobb méretűek lehetnek. További információk és javaslatok a fájlméretekről és a Data Lake Storage Gen1 adatok rendszerezéséről: [az adathalmaz szerkezete](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Nagyméretű fájlméretek és lehetséges teljesítményre gyakorolt hatás

Bár a Data Lake Storage Gen1 a nagy méretű fájlokat támogatja akár petabájt is, az optimális teljesítmény érdekében, és az adat beolvasásának folyamata alapján előfordulhat, hogy nem ideális, hogy átlagosan 2 GB fölé ugorjon. Ha például a **Distcp** használatával másol adatokat a helyszínek vagy a különböző Storage-fiókok között, a fájlok a legfinomabb részletességi szintet használják a leképezési feladatok meghatározásához. Tehát, ha 10, egyenként 1 TB-os fájlt másol, a legtöbb 10 Mapper lefoglalása történik. Emellett, ha sok fájl van hozzárendelve hozzárendelésekkel, kezdetben a leképezések párhuzamosan működnek a nagyméretű fájlok áthelyezéséhez. Mivel azonban a feladatnak csak néhány leképezést kell kiosztania, és egy nagy fájlhoz hozzárendelt egyetlen leképezéssel is elakad. A Microsoft olyan fejlesztéseket küldött a Distcp, amelyek a probléma megoldásához szükségesek a jövőbeli Hadoop-verziókban.

Egy másik példa arra, hogy a Azure Data Lake Analytics Data Lake Storage Gen1 használatával való használata esetén érdemes megfontolni. A kivonó által végzett feldolgozástól függően előfordulhat, hogy a nem bontható fájlok (például az XML, a JSON) teljesítménye a 2 GB-nál nagyobb teljesítmény esetén is csökkenhet. Azokban az esetekben, ahol a fájlok kivonóval (például CSV) bonthatók, a nagyméretű fájlok használata javasolt.

### <a name="capacity-plan-for-your-workload"></a>Kapacitási csomag a számítási feladatokhoz

Azure Data Lake Storage Gen1 eltávolítja a blob Storage-fiókokra helyezett rögzített IO-szabályozási korlátokat. Azonban még mindig enyhe korlátokat kell figyelembe venni. Az alapértelmezett bejövő/kimenő sávszélesség-szabályozási korlátok megfelelnek a legtöbb forgatókönyv igényeinek. Ha a munkaterhelésnek nagyobb a korlátai, a Microsoft támogatási szolgálatával dolgozhat. Tekintse meg a korlátozásokat a megvalósíthatósági fázisban, hogy az i/o-szabályozási korlátok ne legyenek lenyomva az éles környezetben. Ha ez történik, előfordulhat, hogy várnia kell a Microsoft mérnöki csapatának manuális növelésére. Ha az IO-szabályozás bekövetkezik, a Azure Data Lake Storage Gen1 429 hibakódot ad vissza, és ideális esetben a megfelelő exponenciális leállítási házirenddel próbálkozik újra.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Az "írások" optimalizálása a Data Lake Storage Gen1 illesztőprogram-pufferrel

A teljesítmény optimalizálása és a IOPS csökkentése érdekében Data Lake Storage Gen1 Hadoop való írásakor hajtsa végre az írási műveleteket a Data Lake Storage Gen1 illesztőprogram-puffer méretének megfelelően. A kiürítés előtt ne lépje túl a puffer méretét, például Apache Storm vagy Spark streaming számítási feladatait használó adatfolyamként. A HDInsight/Hadoop-ből való Data Lake Storage Gen1 írásakor fontos tudni, hogy Data Lake Storage Gen1 4 MB-os pufferrel rendelkező illesztőprogrammal rendelkezik. A legtöbb fájlrendszer-illesztőprogramhoz hasonlóan ez a puffer manuálisan is kiüríthető, mielőtt eléri a 4 MB-os méretet. Ha nem, a rendszer azonnal kiüríti a tárolóba, ha a következő írás meghaladja a puffer maximális méretét. Ahol lehetséges, el kell kerülnie a puffer túllépését vagy jelentős kiürülését a szabályzatok szám-vagy időablak szerinti szinkronizálása/kiürítése során.

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok

Data Lake Storage Gen1 vagy bármely felhőalapú szolgáltatással rendelkező rendszer tervezésekor meg kell fontolnia a rendelkezésre állási követelményeket, valamint a szolgáltatás lehetséges megszakításait. Egy probléma honosítható az adott példányra, vagy akár régiónként is, így a terv is fontos. A **helyreállítási időre vonatkozó célkitűzéstől** és a számítási feladathoz tartozó **helyreállítási időponttól** függően a magas rendelkezésre állás és a vész-helyreállítás érdekében több vagy kevesebb agresszív stratégiát is választhat.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

A magas rendelkezésre állás (HA) és a vész-helyreállítási (DR) időnként kombinálható együtt, bár mindegyiknek kis mértékben eltérő stratégiája van, különösen akkor, ha az adatforgalomról van szó. Data Lake Storage Gen1 már a motorháztető alatti, a honosított hardverek elleni védelem elleni védelmet biztosító 3x-os replikálást kezeli. Mivel azonban a régiók közötti replikáció nem beépített, ezt saját magának kell kezelnie. Ha egy csomag kiépítésekor a szolgáltatás megszakad, a munkaterhelésnek a lehető leggyorsabban hozzá kell férnie a legfrissebb adatokhoz, ha átvált egy külön replikált példányra helyileg vagy egy új régióban.

Egy DR stratégiában, amely arra készül, hogy felkészüljön a régió katasztrofális meghibásodásának valószínűtlen esetére, az is fontos, hogy az adatmennyiséget egy másik régióba replikálja. Ezek az adatértékek kezdetben megegyeznek a replikált HA-adatértékekkel. Azonban figyelembe kell vennie az olyan Edge-esetekre vonatkozó követelményeket is, mint például az adatsérülés, ahol előfordulhat, hogy időszakos pillanatképeket szeretne létrehozni, amelyekkel vissza lehet térni. Az adatmennyiségtől és méretétől függően a kockázati tűréshatárok alapján érdemes lehet 1 – 6 – és 24 órás időszakra vonatkozó, a helyi és/vagy másodlagos tárolón lévő, a különbözeti eltéréseket figyelembe venni.

Data Lake Storage Gen1 esetén az adatrugalmasságot javasoljuk, hogy az adatait egy külön régióba replikálja, és olyan gyakorisággal, amely megfelel a HA/DR követelményeinek, ideális esetben óránként. A replikálás gyakorisága a nagy mennyiségű adatátvitelt is lekicsinyíti, amely a fő rendszer és a jobb helyreállítási pont célkitűzése (RPO) esetében versengő adatátviteli kapacitást igényel. Emellett érdemes megfontolnia, hogy az alkalmazás a Data Lake Storage Gen1 használatával automatikusan átadja a másodlagos fióknak a figyelési eseményindítók vagy a sikertelen kísérletek hosszát, vagy legalább értesítést küldjön a rendszergazdáknak manuális beavatkozásra. Ne feledje, hogy a szolgáltatás online állapotba helyezésére való várakozás során kompromisszum van a feladatátvétel során. Ha az adatai nem végeztek replikálást, a feladatátvétel lehetséges adatvesztést, inkonzisztenciát vagy összetett adategyesítést okozhat.

Alább láthatók az első három ajánlott lehetőség a Data Lake Storage Gen1-fiókok közötti replikáció összehangolása és az egyes számítógépek közötti fő különbségek tekintetében.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Skálázási korlátok**     | Munkavégző csomópontok kötik        | A Felhőbeli adatáthelyezési egységek maximális száma        | Elemzési egységek kötik        |
|**Támogatja a különbözetek másolását**     |   Igen      | Nem         | Nem         |
|**Beépített előkészítés**     |  Nem (Oozie légáram vagy cron-feladatok használata)       | Yes        | Nem (Azure Automation vagy Windows Feladatütemező használata)         |
|**Támogatott fájlrendszerek**     | ADL, HDFS, WASB, S3, GS, CFS        |Számos, lásd: [Összekötők](../data-factory/connector-azure-blob-storage.md).         | ADL – ADL, WASB – ADL (csak azonos régió)        |
|**Operációs rendszer támogatása**     |Minden Hadoop-t futtató operációs rendszer         | N/A          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp használata két helyszín közötti adatáthelyezéshez

Az elosztott másoláshoz a Distcp egy Linux parancssori eszköz, amely a Hadoop-hoz tartozik, és elosztott adatátvitelt biztosít két hely között. A két helyszín lehet Data Lake Storage Gen1, HDFS, WASB vagy S3. Ez az eszköz MapReduce-feladatokat használ egy Hadoop-fürtön (például HDInsight) az összes csomóponton történő skálázáshoz. A Distcp a leggyorsabb módja a big data speciális hálózati tömörítési berendezések nélküli áthelyezésének. A Distcp egy olyan lehetőséget is biztosít, amely csak két hely közötti különbözetek frissítését, az automatikus újrapróbálkozások kezelését, valamint a számítás dinamikus skálázását is lehetővé teszi. Ez a megközelítés hihetetlenül hatékony, ha olyan struktúrát vagy Spark-táblákat használ, amelyeknek számos nagy fájlja lehet egyetlen címtárban, és csak a módosított adatra szeretne másolni. Ezen okok miatt a Distcp a leginkább ajánlott eszköz az adatok big data tárolók közötti másolásához.

A másolási feladatok az Apache Oozie-munkafolyamatok által indíthatók el, gyakorisággal vagy adateseményindítókkal, valamint Linux cron-feladatokkal. Az intenzív replikációs feladatok esetében ajánlott egy különálló HDInsight Hadoop-fürt üzembe helyezése, amely kifejezetten a másolási feladatok számára állítható be és méretezhető. Ez biztosítja, hogy a másolási feladatok ne akadályozzák a kritikus feladatokat. Ha a replikációt elég nagy gyakorisággal futtatja, akkor a fürt az egyes feladatok között is elvégezhető. Ha a másodlagos régióba feladatátvételt végez, győződjön meg arról, hogy egy másik fürt is megpördült a másodlagos régióban, hogy a biztonsági mentés után visszareplikálja az új adatait az elsődleges Data Lake Storage Gen1 fiókba. A Distcp használatára vonatkozó Példákért lásd: az [Azure Storage-blobok és Data Lake Storage Gen1 közötti adatmásolások használata a Distcp használatával](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>A másolási feladatok ütemezett Azure Data Factory használata

A Azure Data Factory is használható a másolási feladatok **másolási tevékenységgel** való megadására, és a másolás **varázsló** segítségével is beállítható a gyakoriság. Ne feledje, hogy Azure Data Factory a Felhőbeli adatáthelyezési egységek (DMUs-EK) korlátja, és végül a nagyméretű adatszámítási feladatokhoz tartozó átviteli sebesség/számítás. Emellett Azure Data Factory jelenleg nem biztosít különbözeti frissítéseket Data Lake Storage Gen1 fiókok között, így a kaptár-táblákhoz a replikáláshoz teljes másolat szükséges. A Data Factory használatával történő másolással kapcsolatos további információkért tekintse meg a [másolási tevékenység hangolási útmutatóját](../data-factory/copy-activity-performance.md) .

### <a name="adlcopy"></a>AdlCopy

A AdlCopy egy Windows parancssori eszköz, amely lehetővé teszi az Adatmásolást két Data Lake Storage Gen1 fiók között, csak ugyanazon a régión belül. A AdlCopy eszköz önálló lehetőséget, vagy a másolási feladatok futtatására szolgáló Azure Data Lake Analytics fiók használatát teszi lehetővé. Bár eredetileg az igény szerinti másolásra lett létrehozva, szemben a robusztus replikációval, egy másik lehetőséget biztosít az elosztott másolásra ugyanazon a régión belül Data Lake Storage Gen1-fiókok között. A megbízhatóság érdekében javasoljuk, hogy minden éles számítási feladathoz használja a prémium Data Lake Analytics lehetőséget. Az önálló verzióban foglalt válaszok adhatók vissza, és korlátozott mértékben méretezhetők és figyelhetők.

Hasonlóan a Distcp-hoz, a AdlCopy-nek hasonlónak kell lennie, mint Azure Automation vagy a Windows Feladatütemező. A Data Factoryhoz hasonlóan a AdlCopy nem támogatja a csak a frissített fájlok másolását, hanem a meglévő fájlok újramásolását és felülírását. További információ és példák a AdlCopy használatára: [adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Figyelési szempontok

Data Lake Storage Gen1 részletes diagnosztikai naplókat és naplózást biztosít. A Data Lake Storage Gen1 a Data Lake Storage Gen1 fiók és a Azure Monitor alatt Azure Portal alapvető metrikákat tartalmaz. A Data Lake Storage Gen1 rendelkezésre állása megjelenik a Azure Portal. Ez a metrika azonban hét percenként frissül, és nyilvánosan elérhető API-n keresztül nem kérdezhető le. Egy Data Lake Storage Gen1 fiók legfrissebb elérhetőségének lekéréséhez saját szintetikus teszteket kell futtatnia a rendelkezésre állás ellenőrzéséhez. Egyéb mérőszámok, mint például a teljes tárterület-kihasználtság, az olvasási/írási kérelmek és a bejövő/kimenő forgalom akár 24 órát is igénybe vehet. Így a további naprakész metrikákat manuálisan kell kiszámítani a Hadoop parancssori eszközökkel vagy a napló adatainak összesítésével. A legutóbbi tárterület-kihasználtság leggyorsabban a HDFS parancs futtatásával végezhető el egy Hadoop fürtcsomópont (például a fő csomópont):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Data Lake Storage Gen1 diagnosztika exportálása

Az Data Lake Storage Gen1 a kereshető naplókhoz való hozzáférés egyik leggyorsabb módja, ha engedélyezi a napló szállítását **log Analytics** a Data Lake Storage Gen1 fiók **diagnosztika** paneljén. Ez azonnali hozzáférést biztosít a bejövő naplókhoz idő-és tartalmi szűrőkkel, valamint a riasztási beállításokkal (e-mailek/webhookok), amely 15 perces időközökben aktiválódik. Útmutatásért lásd: [a Azure Data Lake Storage Gen1 diagnosztikai naplóinak elérése](data-lake-store-diagnostic-logs.md).

Ha több valós idejű riasztásra van szükség, és többet szeretne vezérelni a naplók beküldésének helyétől, érdemes lehet olyan Azure-EventHub exportálni a naplókat, amelyekben a tartalom elemezhető önállóan vagy egy időablakban, hogy valós idejű értesítéseket lehessen elküldeni egy várólistába. Egy különálló alkalmazás, például egy [logikai alkalmazás](../connectors/connectors-create-api-azure-event-hubs.md) felhasználhatja és továbbíthatja a riasztásokat a megfelelő csatornához, valamint mérőszámokat küldhet a figyelési eszközökre, például a NewRelic, a datadoggal vagy a AppDynamics. Ha külső gyártótól származó eszközt (például ElasticSearch) használ, a naplókat exportálhatja Blob Storagere, és az [Azure Logstash beépülő](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) modullal felhasználhatja az adatait a ElasticSearch, a Kibana és a LOGSTASH (Elk) verembe.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Hibakeresési szintű naplózás bekapcsolása a HDInsight-ben

Ha Data Lake Storage Gen1 napló szállítása nincs bekapcsolva, az Azure HDInsight lehetővé teszi az [ügyféloldali naplózás](data-lake-store-performance-tuning-mapreduce.md) bekapcsolását a log4j-on keresztüli Data Lake Storage Gen1. A következő tulajdonságot kell megadnia a **Ambari**  >  **fonál**  >    >  **-konfiguráció Advanced fonal-log4j konfigurációjában**:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Ha a tulajdonság be van állítva, és a csomópontok újraindulnak, Data Lake Storage Gen1 diagnosztikát a rendszer a csomópontokon található/tmp/-naplókba ( \<user\> /yarn.log) írja, és fontos részleteket, például a hibákat vagy a szabályozást (HTTP 429 hibakód) figyelheti. Ugyanezek az információk Azure Monitor naplókban is megfigyelhetők, vagy ha a naplók a Data Lake Storage Gen1 fiók [diagnosztika](data-lake-store-diagnostic-logs.md) paneljén vannak elküldve. Azt javasoljuk, hogy legalább ügyféloldali naplózás be legyen kapcsolva, vagy használja a naplózási lehetőséget a Data Lake Storage Gen1 az operatív láthatóság és a könnyebb hibakeresés érdekében.

### <a name="run-synthetic-transactions"></a>Szintetikus tranzakciók futtatása

Jelenleg a Azure Portal Data Lake Storage Gen1 szolgáltatás rendelkezésre állási metrikája 7 perces frissítési időszakot tartalmaz. Azt is megteheti, hogy nyilvánosan elérhető API használatával nem tud lekérdezni. Ezért azt javasoljuk, hogy hozzon létre egy alapszintű alkalmazást, amely szintetikus tranzakciókat Data Lake Storage Gen1, amelyek akár a percek rendelkezésre állását is lehetővé teszik. Lehetséges például, hogy létrehoz egy Webjobs, egy logikai alkalmazást vagy egy Azure-függvényalkalmazást az olvasási, létrehozási és frissítési műveletek elvégzéséhez Data Lake Storage Gen1 és az eredményeket elküldheti a figyelési megoldásnak. A műveletek egy ideiglenes mappában hajthatók végre, majd a teszt után törölve lesznek, amely a követelményektől függően minden 30-60 másodpercenként futtatható.

## <a name="directory-layout-considerations"></a>A címtár-elrendezés szempontjai

Az adatközpontba való kirakodáskor fontos, hogy előre tervezze az adat szerkezetét, hogy a biztonság, a particionálás és a feldolgozás hatékonyan felhasználható legyen. A következő javaslatok közül számos használható, függetlenül attól, hogy Azure Data Lake Storage Gen1, Blob Storage vagy HDFS van-e. Minden számítási feladat eltérő követelményeket támaszt az adatok felhasználásának módjával kapcsolatban, de az alábbiakban felsorolunk néhány általános elrendezést, amelyeket érdemes figyelembe venni a IoT és a Batch-forgatókönyvek használatakor.

### <a name="iot-structure"></a>IoT struktúra

A IoT számítási feladatokban nagy mennyiségű adat található az adattárban, amely számos termékre, eszközre, szervezetre és ügyfélre kiterjed. Fontos, hogy előzetesen tervezze meg a címtár-elrendezést a szervezet, a biztonság és a hatékony feldolgozás érdekében az adatátviteli sebességű felhasználók számára. A megfontolandó általános sablon a következő elrendezés lehet:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Például az Egyesült királyságbeli repülőgép-hajtóművek leszállási telemetria az alábbi struktúrához hasonló lehet:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Fontos oka, hogy a mappa szerkezete végén a dátumot kell létrehozni. Ha bizonyos régiókat vagy felhasználókat vagy csoportokat szeretne zárolni, a POSIX-engedélyekkel egyszerűen elvégezheti a zárolást. Ellenkező esetben, ha korlátozni kell egy bizonyos biztonsági csoportot, hogy csak az Egyesült királyságbeli adatok vagy bizonyos síkok megtekintésére legyen szükség, a dátum struktúra előtt külön engedélyre van szükség az óránkénti mappa számos mappájának megkereséséhez. Emellett a kezdeti dátum szerkezete exponenciálisan növelheti a mappák számát az idő múlásával.

### <a name="batch-jobs-structure"></a>Batch-feladatok szerkezete

A Batch-feldolgozás általánosan használt megközelítése az adatok egy "in" mappában való lerakása. Az adatfeldolgozást követően az új "out" mappába helyezheti az új adatfeldolgozási folyamatokat. Ezt a címtár-struktúrát időnként olyan feladatok esetében tekintjük meg, amelyek az egyes fájlokon végzett feldolgozást igénylik, és előfordulhat, hogy a nagyméretű adatkészletek esetében nem szükségesek a A fentiekben javasolt IoT struktúrához hasonlóan a megfelelő címtár-struktúra is rendelkezik a szülő szintű mappákkal olyan dolgokhoz, mint a régió és a tárgy (például szervezet, termék/gyártó). Ez a struktúra segítséget nyújt az adatok szervezeten belüli biztonságossá tételében és a számítási feladatokban található adatok hatékonyabb kezelésében. Emellett vegye figyelembe a szerkezet dátumát és idejét, hogy jobb szervezetet, szűrt keresést, biztonságot és automatizálást engedélyezzen a feldolgozásban. A dátum-struktúra részletességi szintjét az adatok feltöltésének vagy feldolgozásának intervalluma határozza meg, például óránként, naponta vagy akár havonta.

Az adatsérülés vagy váratlan formátumok miatt előfordulhat, hogy a fájlok feldolgozása nem sikerült. Ilyen esetekben a címtár szerkezete előnyt jelenthet a **/Bad** mappában, hogy további ellenőrzés céljából áthelyezze a fájlokat. Előfordulhat, hogy a Batch-feladatok manuális beavatkozás céljából is kezelhetik a *hibás* fájlok jelentéseit vagy értesítéseit. Vegye figyelembe a következő sablon struktúráját:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

A marketing vállalat például az ügyfelek frissítéseinek napi adatkivonatát fogadja Észak-Amerika. A feldolgozás előtt és után a következő kódrészlethez hasonlónak tűnhet:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

A közvetlenül az adatbázisokba (például a kaptárba vagy a hagyományos SQL-adatbázisba) feldolgozott batch-adatokat nem kell **/in** vagy **/out** mappához adni, mivel a kimenet már egy különálló mappába kerül a kaptár-tábla vagy a külső adatbázis számára. Például az ügyfelek napi kinyerése a saját mappáiba kerül, és a Azure Data Factory, az Apache Oozie vagy az Apache légáram egy olyan napi struktúrát vagy Spark-feladatot indít el, amely feldolgozza és beírja az adatait egy kaptár-táblába.

## <a name="next-steps"></a>Következő lépések

* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Access Control a Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Biztonság a Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [A teljesítmény finomhangolása Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-guidance.md)
* [Teljesítmény-finomhangolási útmutató a HDInsight Spark és a Azure Data Lake Storage Gen1 használatával](data-lake-store-performance-tuning-spark.md)
* [Teljesítmény-finomhangolási útmutató a HDInsight-struktúra Azure Data Lake Storage Gen1 használatával való használatához](data-lake-store-performance-tuning-hive.md)
* [HDInsight-fürtök létrehozása Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
