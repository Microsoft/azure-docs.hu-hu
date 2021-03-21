---
title: Ajánlott eljárások a Azure Data Lake Storage Gen2 használatához | Microsoft Docs
description: Ismerje meg az adatfeldolgozással, a dátummal és a biztonsággal kapcsolatos ajánlott eljárásokat, valamint a Azure Data Lake Storage Gen2 (korábbi nevén Azure Data Lake Store) használatához kapcsolódó teljesítményt
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 0efcdfd1b14479edf84dc1892e7e1d9afabd5a81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95913555"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Ajánlott eljárások Azure Data Lake Storage Gen2 használatához

Ebben a cikkben megismerheti a Azure Data Lake Storage Gen2 használatának ajánlott eljárásait és szempontjait. Ez a cikk a Data Lake Storage Gen2 biztonságával, teljesítményével, rugalmasságával és figyelésével kapcsolatos információkat tartalmaz. Data Lake Storage Gen2 előtt a valóban big data olyan szolgáltatásokkal, mint az Azure HDInsight, összetett volt. Több blob Storage-fiókba kellett átméreteznie az adatmennyiséget, hogy a petabyte tárolás és az optimális teljesítmény legyen elérhető. Data Lake Storage Gen2 támogatja az egyéni fájlméretet olyan magas szintű 5TB, amely a teljesítményre vonatkozó nagy mennyiségű korlátozást eltávolította. Azonban továbbra is vannak olyan megfontolások, amelyeket ez a cikk ismertet, hogy a lehető legjobb teljesítményt kapja Data Lake Storage Gen2.

## <a name="security-considerations"></a>Biztonsági szempontok

A Azure Data Lake Storage Gen2 a Azure Active Directory (Azure AD) felhasználók, csoportok és egyszerű szolgáltatások POSIX-hozzáférés-vezérlését kínálja. Ezek a hozzáférés-vezérlések megadhatók meglévő fájlokhoz és könyvtárakhoz. A hozzáférés-vezérléssel olyan alapértelmezett engedélyek is létrehozhatók, amelyek automatikusan alkalmazhatók az új fájlokra vagy címtárakra. Data Lake Storage Gen2 hozzáférés-vezérlési listákkal kapcsolatos további részletek a [Azure Data Lake Storage Gen2 hozzáférés-vezérlésében](./data-lake-storage-access-control.md)érhetők el.

### <a name="use-security-groups-versus-individual-users"></a>Biztonsági csoportok és egyéni felhasználók együttes használata

Ha Data Lake Storage Gen2 big data dolgozik, valószínű, hogy egy egyszerű szolgáltatásnév lehetővé teszi az olyan szolgáltatások használatát, mint az Azure HDInsight az adatkezeléshez. Előfordulhatnak azonban olyan esetek, amikor az egyes felhasználóknak is szükségük van az adathozzáférésre. Az egyes felhasználókat a címtárakhoz és fájlokhoz való hozzárendelés helyett minden esetben erősen érdemes Azure Active Directory [biztonsági csoportokat](../common/storage-auth-aad.md) használni.

Ha a biztonsági csoporthoz engedélyek vannak rendelve, a csoportba tartozó felhasználók hozzáadására vagy eltávolítására nincs szükség frissítésre Data Lake Storage Gen2. Ez azt is lehetővé teszi, hogy ne lépje túl a hozzáférés-vezérlési bejegyzések maximális számát (ACL). Ez a szám jelenleg 32 (beleértve a négy POSIX-stílusú ACL-eket, amelyek mindig társítva vannak minden fájlhoz és könyvtárhoz): a tulajdonos felhasználó, a tulajdonos csoport, a maszk és más. Minden címtár rendelkezhet két hozzáférés-vezérlési listával, a hozzáférési ACL-vel és az alapértelmezett ACL-vel, összesen 64 hozzáférés-vezérlési bejegyzés esetén. További információ ezekről az ACL-ekkel: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Csoportok biztonsága

Ha vagy a felhasználóknak olyan Storage-fiókban lévő adatelérésre van szükségük, amelyen engedélyezve van a hierarchikus névtér, érdemes Azure Active Directory biztonsági csoportokat használni. Előfordulhat, hogy egyes ajánlott csoportok a tároló gyökeréhez **ReadOnlyUsers**, **WriteAccessUsers** és **FullAccessUsers** , és a fő alkönyvtárak esetében is elkülönítik azokat. Ha a felhasználók más várható felhasználói csoportjai is hozzáadhatók, de még nincsenek azonosítva, érdemes lehet olyan dummy biztonsági csoportokat létrehozni, amelyek bizonyos mappákhoz férnek hozzá. A biztonsági csoport használatával gondoskodhat arról, hogy a hosszú feldolgozási idő elkerülhető legyen, amikor új engedélyeket rendel több ezer fájlhoz.

### <a name="security-for-service-principals"></a>Az egyszerű szolgáltatások biztonsága

Azure Active Directory egyszerű szolgáltatásokat általában olyan szolgáltatások használják, mint a Azure Databricks a Data Lake Storage Gen2 lévő adateléréshez. Sok ügyfél esetében előfordulhat, hogy egy Azure Active Directory egyszerű szolgáltatás megfelelő, és teljes körű engedélyekkel rendelkezik a Data Lake Storage Gen2 tároló gyökerében. Más ügyfeleknél több olyan fürtre lehet szükség, amely különböző egyszerű szolgáltatásokkal rendelkezik, ahol az egyik fürt teljes hozzáféréssel rendelkezik az adathoz, és egy másik fürt, amely csak olvasási hozzáféréssel rendelkezik. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>A Data Lake Storage Gen2 tűzfal engedélyezése az Azure-szolgáltatás elérésével

Data Lake Storage Gen2 támogatja a tűzfal bekapcsolásának lehetőségét, és csak az Azure-szolgáltatásokhoz való hozzáférést korlátozza, ami a külső támadások vektorának korlátozására ajánlott. A tűzfal engedélyezhető a Azure Portal lévő Storage-fiókon **a tűzfal**  >  **engedélyezése (bekapcsolva)**  >  beállítással az **Azure-szolgáltatások elérésének engedélyezése** lehetőséggel.

Ha Azure Databricks szeretné elérni a Storage-fiókját, telepítse Azure Databricks a virtuális hálózatra, majd adja hozzá a virtuális hálózatot a tűzfalhoz. Lásd: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Rugalmassággal kapcsolatos szempontok

Data Lake Storage Gen2 vagy bármely felhőalapú szolgáltatással rendelkező rendszer tervezésekor meg kell fontolnia a rendelkezésre állási követelményeket, valamint a szolgáltatás lehetséges megszakításait. Egy probléma honosítható az adott példányra, vagy akár régiónként is, így a terv is fontos. A helyreállítási időre vonatkozó célkitűzéstől és a számítási feladathoz tartozó helyreállítási időponttól függően a magas rendelkezésre állás és a vész-helyreállítás érdekében több vagy kevesebb agresszív stratégiát is választhat.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

A magas rendelkezésre állás (HA) és a vész-helyreállítási (DR) időnként kombinálható együtt, bár mindegyiknek kis mértékben eltérő stratégiája van, különösen akkor, ha az adatforgalomról van szó. Data Lake Storage Gen2 már a motorháztető alatti, a honosított hardverek elleni védelem elleni védelmet biztosító 3x-os replikálást kezeli. Emellett más replikációs beállítások, például a ZRS vagy a GZRS, javítják a HA-t, míg a GRS & RA-GRS fejleszti a DR-t. Ha egy csomag kiépítésekor a szolgáltatás megszakad, a munkaterhelésnek a lehető leggyorsabban hozzá kell férnie a legfrissebb adatokhoz, ha átvált egy külön replikált példányra helyileg vagy egy új régióban.

Egy DR stratégiában, amely arra készül, hogy felkészüljön egy adott régió katasztrofális meghibásodásának valószínűtlen esetére, fontos, hogy a GRS vagy RA-GRS replikációt használó más régióba replikált adatmennyiségeket is. Meg kell fontolnia az Edge-esetekre vonatkozó követelményeket is, például az adatsérülést, ahol előfordulhat, hogy rendszeres pillanatképeket szeretne készíteni, amelyekkel vissza lehet térni. Az adatmennyiségtől és méretétől függően az 1 – 6 és 24 órás időszakra vonatkozó, kockázati tűréshatárok alapján megjelenő különbözeti pillanatképeket kell figyelembe venni.

Az adatrugalmasság Data Lake Storage Gen2 esetén ajánlott geo-replikálni az adatait GRS vagy RA-GRS, amely megfelel a HA/DR követelményeinek. Emellett érdemes megfontolnia, hogy az alkalmazás a Data Lake Storage Gen2 használatával automatikusan átadja a feladatátvételt a másodlagos régiónak az eseményindítók figyelésével vagy a sikertelen kísérletek hosszával, vagy legalább értesítést küldjön a rendszergazdáknak manuális beavatkozásra. Ne feledje, hogy a szolgáltatás online állapotba helyezésére való várakozás során kompromisszum van a feladatátvétel során.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp használata két helyszín közötti adatáthelyezéshez

Az elosztott másoláshoz a DistCp egy Linux parancssori eszköz, amely a Hadoop-hoz tartozik, és elosztott adatátvitelt biztosít két hely között. A két helyszín lehet Data Lake Storage Gen2, HDFS vagy S3. Ez az eszköz MapReduce-feladatokat használ egy Hadoop-fürtön (például HDInsight) az összes csomóponton történő skálázáshoz. A Distcp a leggyorsabb módja a big data speciális hálózati tömörítési berendezések nélküli áthelyezésének. A Distcp egy olyan lehetőséget is biztosít, amely csak két hely közötti különbözetek frissítését, az automatikus újrapróbálkozások kezelését, valamint a számítás dinamikus skálázását is lehetővé teszi. Ez a megközelítés hihetetlenül hatékony, ha olyan struktúrát vagy Spark-táblákat használ, amelyeknek számos nagy fájlja lehet egyetlen címtárban, és csak a módosított adatra szeretne másolni. Ezen okok miatt a Distcp a leginkább ajánlott eszköz az adatok big data tárolók közötti másolásához.

A másolási feladatok az Apache Oozie-munkafolyamatok által indíthatók el, gyakorisággal vagy adateseményindítókkal, valamint Linux cron-feladatokkal. Az intenzív replikációs feladatok esetében ajánlott egy különálló HDInsight Hadoop-fürt üzembe helyezése, amely kifejezetten a másolási feladatok számára állítható be és méretezhető. Ez biztosítja, hogy a másolási feladatok ne akadályozzák a kritikus feladatokat. Ha a replikációt elég nagy gyakorisággal futtatja, akkor a fürt az egyes feladatok között is elvégezhető. Ha a másodlagos régióba feladatátvételt végez, győződjön meg arról, hogy egy másik fürt is megpördült a másodlagos régióban, hogy a biztonsági mentés után visszareplikálja az új adatait az elsődleges Data Lake Storage Gen2 fiókba. A Distcp használatára vonatkozó Példákért lásd: az [Azure Storage-blobok és Data Lake Storage Gen2 közötti adatmásolások használata a Distcp használatával](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>A másolási feladatok ütemezett Azure Data Factory használata

A Azure Data Factory is használható a másolási feladatok másolási tevékenységgel való megadására, és a másolás varázsló segítségével is beállítható a gyakoriság. Ne feledje, hogy Azure Data Factory a Felhőbeli adatáthelyezési egységek (DMUs-EK) korlátja, és végül a nagyméretű adatszámítási feladatokhoz tartozó átviteli sebesség/számítás. Emellett Azure Data Factory jelenleg nem biztosít különbözeti frissítéseket Data Lake Storage Gen2 fiókok között, ezért a kaptár-táblákhoz a replikáláshoz teljes másolat szükséges. A Data Factory használatával történő másolással kapcsolatos további információkért tekintse meg a [adat-előállítót ismertető cikket](../../data-factory/load-azure-data-lake-storage-gen2.md) .

## <a name="monitoring-considerations"></a>Figyelési szempontok

A Data Lake Storage Gen2 metrikákat biztosít a Data Lake Storage Gen2 fiók és a Azure Monitor Azure Portal alatt. A Data Lake Storage Gen2 rendelkezésre állása megjelenik a Azure Portal. Egy Data Lake Storage Gen2 fiók legfrissebb elérhetőségének lekéréséhez saját szintetikus teszteket kell futtatnia a rendelkezésre állás ellenőrzéséhez. Más mérőszámok, mint például a teljes tárterület-kihasználtság, az olvasási/írási kérelmek és a bejövő/kimenő forgalom kihasználható a figyelési alkalmazások számára, és riasztásokat is indíthatnak, ha a küszöbértékek (például átlagos késés vagy a percenkénti hibák száma) túllépve.

## <a name="directory-layout-considerations"></a>A címtár-elrendezés szempontjai

Az adatközpontba való kirakodáskor fontos, hogy előre tervezze az adat szerkezetét, hogy a biztonság, a particionálás és a feldolgozás hatékonyan felhasználható legyen. A következő javaslatok közül számos alkalmazható az összes big data munkaterhelésre. Minden számítási feladat eltérő követelményeket támaszt az adatok felhasználásának módjával kapcsolatban, de az alábbiakban felsorolunk néhány általános elrendezést, amelyeket érdemes figyelembe venni a IoT és a Batch-forgatókönyvek használatakor.

### <a name="iot-structure"></a>IoT struktúra

A IoT számítási feladatokban nagy mennyiségű adat található az adattárban, amely számos termékre, eszközre, szervezetre és ügyfélre kiterjed. Fontos, hogy előzetesen tervezze meg a címtár-elrendezést a szervezet, a biztonság és a hatékony feldolgozás érdekében az adatátviteli sebességű felhasználók számára. A megfontolandó általános sablon a következő elrendezés lehet:

*{Region}/{SubjectMatter (s)}/{yyyy}/{MM}/{DD}/{hh}/*

Például az Egyesült királyságbeli repülőgép-hajtóművek leszállási telemetria az alábbi struktúrához hasonló lehet:

*Egyesült Királyság/repülőgépek/BA1293/Engine1/2017/08/11/12/*

Fontos, hogy a címtár struktúrájának végére helyezze a dátumot. Ha bizonyos régiókat vagy felhasználókat vagy csoportokat szeretne zárolni, a POSIX-engedélyekkel egyszerűen elvégezheti a zárolást. Ellenkező esetben, ha korlátozni kell egy bizonyos biztonsági csoportot, hogy csak az Egyesült királyságbeli adatok vagy bizonyos síkok megtekintésére legyen szükség, a dátum struktúra előtt külön engedélyre lesz szükség az óránkénti címtárban számos könyvtárhoz. Emellett a kezdeti dátum szerkezete exponenciálisan megnövelheti a könyvtárak számát az idő múlásával.

### <a name="batch-jobs-structure"></a>Batch-feladatok szerkezete

A Batch-feldolgozás általánosan használt megközelítése az adatok egy "in" könyvtárban való lerakása. Ezután az adatfeldolgozást követően helyezze az új adatelérési utat egy "out" könyvtárba az alsóbb rétegbeli folyamatok felhasználásához. Ezt a címtár-struktúrát időnként olyan feladatok esetében tekintjük meg, amelyek az egyes fájlokon végzett feldolgozást igénylik, és előfordulhat, hogy a nagyméretű adatkészletek esetében nem szükségesek a A fentiekben javasolt IoT struktúrához hasonlóan a megfelelő címtár-struktúra is rendelkezik a szülő szintű címtárakkal olyan dolgokhoz, mint a régió és a tárgyi ügyek (például szervezet, termék/gyártó). Ez a struktúra segítséget nyújt az adatok szervezeten belüli biztonságossá tételében és a számítási feladatokban található adatok hatékonyabb kezelésében. Emellett vegye figyelembe a szerkezet dátumát és idejét, hogy jobb szervezetet, szűrt keresést, biztonságot és automatizálást engedélyezzen a feldolgozásban. A dátum-struktúra részletességi szintjét az adatok feltöltésének vagy feldolgozásának intervalluma határozza meg, például óránként, naponta vagy akár havonta.

Az adatsérülés vagy váratlan formátumok miatt előfordulhat, hogy a fájlok feldolgozása nem sikerült. Ilyen esetekben a címtár szerkezete előnyt jelenthet a **/Bad** mappában, hogy további ellenőrzés céljából áthelyezze a fájlokat. Előfordulhat, hogy a Batch-feladatok manuális beavatkozás céljából is kezelhetik a *hibás* fájlok jelentéseit vagy értesítéseit. Vegye figyelembe a következő sablon struktúráját:

*{Region}/{SubjectMatter (s)}/in/{yyyy}/{MM}/{DD}/{hh}/*\
*{Region}/{SubjectMatter (s)}/out/{yyyy}/{MM}/{DD}/{hh}/*\
*{Region}/{SubjectMatter (s)}/Bad/{yyyy}/{MM}/{DD}/{hh}/*

A marketing vállalat például az ügyfelek frissítéseinek napi adatkivonatát fogadja Észak-Amerika. A feldolgozás előtt és után a következő kódrészlethez hasonlónak tűnhet:

*NA/szemelvények/ACMEPaperCo/in/2017/08/14/updates_08142017.csv*\
*NA/szemelvények/ACMEPaperCo/out/2017/08/14/processed_updates_08142017.csv*

A közvetlenül az adatbázisokba (például a kaptárba vagy a hagyományos SQL-adatbázisba) feldolgozott batch-adatokat nem kell **/in** vagy **/out** mappához adni, mivel a kimenet már egy különálló mappába kerül a kaptár-tábla vagy a külső adatbázis számára. Például az ügyfelek napi kinyerése a saját mappáiba kerül, és a Azure Data Factory, az Apache Oozie vagy az Apache légáram egy olyan napi struktúrát vagy Spark-feladatot indít el, amely feldolgozza és beírja az adatait egy kaptár-táblába.