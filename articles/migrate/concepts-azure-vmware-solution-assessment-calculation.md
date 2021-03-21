---
title: AVS Assessment-számítások a Azure Migrateban | Microsoft Docs
description: Áttekintést nyújt az AVS Assessment számításokról a Azure Migrate szolgáltatásban.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: b3975d30fca1f7f542f27742ef8408b1feecc146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727192"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Értékelés áttekintése (Migrálás az Azure VMware-megoldásba)

[Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és munkaterhelések felderítésének, értékelésének és áttelepítésének nyomon követéséhez. Emellett nyomon követi a privát és a nyilvános Felhőbeli példányokat az Azure-ban. Az elosztó Azure Migrate eszközöket kínál az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatait.

A Server Assessment olyan Azure Migrate eszköz, amely a helyszíni kiszolgálókat az Azure IaaS Virtual Machines és az Azure VMware Solution (AVS) szolgáltatásba való áttelepítésre értékeli. Ez a cikk arról nyújt tájékoztatást, hogyan számítják ki az Azure VMware-megoldás (AVS) értékelését.

> [!NOTE]
> Az Azure VMware-megoldás (AVS) értékelését csak VMware virtuális gépekhez lehet létrehozni.

## <a name="types-of-assessments"></a>Az értékelések típusai

Az Azure Migrateekkel létrehozott értékelések az adatok időponthoz kapcsolódó pillanatképei. Kétféle értékelést hozhat létre a Azure Migrate használatával:

| **Kiértékelés típusa** | **Részletek** |
| - | - |
| **Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. A[VMware](how-to-set-up-appliance-vmware.md) vagy a [Hyper-V](how-to-set-up-appliance-hyper-v.md) környezetben futó helyszíni kiszolgálókat, valamint az Azure-ba való áttelepítéshez használható [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) értékelheti ki. [További információ](concepts-assessment-calculation.md) |
| **Azure VMware Solution (AVS)** | A helyszíni VMware virtuális gépek vagy kiszolgálók[ Azure VMware-megoldásba (AVS)](../azure-vmware/introduction.md)való átköltöztetésének felmérése.  A [VMware](how-to-set-up-appliance-vmware.md) -környezetben futó helyszíni kiszolgálókat felmérhető módon áttelepítheti az Azure VMware-megoldásba (AVS) az értékelés típusának használatával. [További információ](concepts-azure-vmware-solution-assessment-calculation.md) |

Az Azure VMware-megoldás (AVS) értékelése két méretezési feltétel beállítását biztosítja:

| **Értékelés** | **Részletek** | **Adatok** |
| - | - | - |
| **Teljesítményalapú** | A helyszíni virtuális gépek összegyűjtött teljesítményadatok alapján végzett értékelések. | **Ajánlott csomópont mérete**: a CPU és a memória kihasználtsági adatai, valamint a csomópont típusa, a tárolási típus és a pénzügyi tranzakciós beállítás alapján, amelyet az értékeléshez választott. |
| **Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott csomópont mérete**: a helyszíni virtuális gép méretétől, valamint a csomópont típusától, a tárolási típustól és az értékeléshez kiválasztott tranzakciós beállítástól függően. |

## <a name="how-do-i-run-an-assessment"></a>Hogyan egy értékelést?

Több módon is futtathat értékeléseket.

- A gépeket egy egyszerű Azure Migrate berendezés által gyűjtött kiszolgálói metaadatok használatával értékelheti ki. A készülék felfedi a helyszíni gépeket. Ezután a számítógép metaadatainak és teljesítményadatokat küld Azure Migrate. Ez nagyobb pontosságot tesz lehetővé.
- A gépeket a vesszővel tagolt (CSV) formátumban importált kiszolgálói metaadatok használatával értékelheti ki.

## <a name="how-do-i-assess-with-the-appliance"></a>Hogyan az értékelést a berendezéssel?

Ha Azure Migrate berendezést helyez üzembe a helyszíni kiszolgálók felderítése érdekében, hajtsa végre a következő lépéseket:

1. Az Azure és a helyszíni környezet beállítása a Azure Migrate való együttműködéshez.
2. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a kiszolgáló-értékelési eszközt.
3. Egyszerűsített Azure Migrate berendezés üzembe helyezése. A készülék folyamatosan felfedi a helyszíni gépeket, és számítógép-metaadatokat és teljesítményadatokat küld Azure Migrate. Telepítse a készüléket virtuális gépre. Semmit nem kell telepítenie az értékelni kívánt gépekre.

Miután a készülék megkezdte a számítógép-felderítést, összegyűjtheti azokat a gépeket, amelyeket fel szeretne mérni egy csoportba, és értékelést kell futtatnia a csoportnak az **Azure VMware Solution (AVS)** típusú értékeléssel.

Hozza létre az első Azure VMware-megoldás (AVS) értékelését az [itt](how-to-create-azure-vmware-solution-assessment.md)leírt lépéseket követve.

## <a name="how-do-i-assess-with-imported-data"></a>Hogyan az importált adattal való értékelést?

Ha egy CSV-fájl használatával értékeli a kiszolgálókat, nincs szüksége berendezésre. Ehelyett hajtsa végre a következő lépéseket:

1. Állítsa be az Azure-t, hogy működjön együtt Azure Migrateokkal.
2. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a kiszolgáló-értékelési eszközt.
3. Töltsön le egy CSV-sablont, és adja hozzá a kiszolgálói adatfájlokat.
4. Importálja a sablont Azure Migrateba.
5. Az importálással hozzáadott kiszolgálók felderítése, összegyűjtése egy csoportba, és értékelés futtatása a csoportnak az **Azure VMware Solution (AVS)** típusú értékeléssel.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Ha a Azure Migrate berendezést értékelésre használja, ismerkedjen meg a [VMware](migrate-appliance.md#collected-data---vmware)-hez gyűjtött metaadatokkal és teljesítményadatokat.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hogyan számítja ki a készülék a teljesítményadatokat?

Ha a készüléket a felderítéshez használja, a következő lépésekkel gyűjt teljesítményadatokat a számítási beállításokhoz:

1. A készülék valós idejű mintavételi pontot gyűjt.

   - **VMWare virtuális gépek**: a rendszer 20 másodpercenként gyűjt egy mintavételi pontot.
2. A készülék 10 percenként egyesíti a mintavételi pontokat, hogy egyetlen adatpontot hozzon létre. Az adatpont létrehozásához a készülék kiválasztja az összes minta csúcsérték-értékeit. Ezután elküldi az adatpontot az Azure-nak.
3. A Azure Migrate az elmúlt hónap 10 percenkénti adatpontjait tárolja.
4. Az értékelés létrehozásakor az értékelés azonosítja a megadásában használandó megfelelő adatpontot. Az azonosítás a *teljesítmény előzményeinek* és a *percentilis kihasználtságának* százalékos értékein alapul.

   - Ha például a teljesítmény előzményei egy hét, a percentilis kihasználtsága pedig a 95. percentilis, az értékelés az elmúlt hét 10 perces mintavételi pontjait rendezi. Növekvő sorrendben rendezi őket, és kiválasztja a 95. percentilis értékét a megadásában.
   - A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiválasztotta a esetek 99% percentilis értékét.
   - Ha ki szeretné választani az időszakhoz tartozó csúcsérték-használatot, és nem szeretne kiugró értékeket kihagyni, válassza a esetek 99% percentilis értéket a percentilis kihasználtsága mezőben.
5. Ennek az értéknek a megszorozza a komforttal, hogy a készülék által összegyűjtött mérőszámok tényleges teljesítmény-kihasználtsági adatai meglegyenek:

   - Processzorhasználat
   - RAM kihasználtsága

A rendszer a következő teljesítményadatokat gyűjti, de nem használja az AVS-értékelések méretezési javaslataiban:

- A lemez a virtuális géphez csatolt minden lemez IOPS és adatátviteli adatokkal rendelkezik.
- A hálózati I/O-t a virtuális géphez csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

## <a name="how-are-avs-assessments-calculated"></a>Hogyan történik az AVS-értékelések kiszámítása?

Az AVS Assessment a helyszíni gépek metaadatait és teljesítményadatait használja az értékelések kiszámításához. Ha telepíti a Azure Migrate készüléket, az értékelés a készülék által gyűjtött adatokat használja. Ha azonban egy CSV-fájllal importált értékelést futtat, akkor megadja a számítás metaadatait.

A számítások a következő három szakaszban történnek:

1. **Azure VMware-megoldás (AVS) készültségének kiszámítása**: azt határozza meg, hogy a helyszíni virtuális gépek alkalmasak-e az Azure VMware-megoldásra (AVS) való áttelepítésre.
2. Az **AVS-csomópontok számának és a csomópontok közötti kihasználtságának kiszámítása**: a VMWare virtuális gépek futtatásához szükséges AVS-csomópontok becsült száma, valamint a processzor, a memória és a tárhely kihasználtsága az összes csomóponton.
3. **Havi költségbecslés**: a helyszíni virtuális gépeket futtató összes Azure VMware Solution (AVS) csomópont becsült havi költségei.

A számítások az előző sorrendben vannak. A számítógép-kiszolgálók csak akkor mozdulnak el egy későbbi fázisra, ha az előzőt átadja. Ha például egy kiszolgáló meghibásodik az AVS készültségi fázisában, az az Azure számára nem megfelelőként van megjelölve. A méretezési és költségszámítási műveletek nem lettek végrehajtva az adott kiszolgálón

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Mi az Azure VMware-megoldás (AVS) értékelése?

A következőkben szerepel az AVS assessment:

| **Tulajdonság** | **Részletek** |
| - | - |
| **Célhely** | Megadja azt az AVS saját Felhőbeli helyet, amelyre az áttelepítést szeretné végezni. |
| **Tárolási típus** | Megadja az AVS-ben használni kívánt tárolási motort. Az AVS jelenleg csak az alapértelmezett vSAN támogatja, a tárolási lehetőségek pedig ütemterv szerint lesznek elérhetők. |
| **Fenntartott példányok (RIs)** | Ennek a tulajdonságnak a segítségével megadhatja a fenntartott példányokat az AVS-ben, ha megvásárolta és a fenntartott példány időtartamát A költségek kiszámításához használatos. |
| **Csomópont típusa** | Az Azure-ban használni kívánt [AVS-csomópont típusát](../azure-vmware/concepts-private-clouds-clusters.md) adja meg. Az alapértelmezett csomópont típusa AV36. A jövőben további csomópont-típusok is elérhetők lehetnek.  Azure Migrate a virtuális gépek AVS-re való áttelepítéséhez szükséges csomópontok számát javasolja. |
| **TRANZAKCIÓs beállítás, RAID-szint** | Meghatározza a hibák és a RAID-kombinációk érvényes kombinációját. A kiválasztott TRANZAKCIÓs lehetőség RAID-szinttel és a helyszíni virtuálisgép-lemezzel kapcsolatos követelménye határozza meg az AVS-ben szükséges teljes vSAN-tárterületet. Az összes rendelkezésre álló tárterület a számítások után is tartalmaz egy, a felügyeleti objektumok, például a vCenter és a b) számára fenntartott területet, amely a vSAN műveletekhez szükséges 25%-os tárolási tartalékidő. |
| **Méretezési feltétel** | Megadja az AVS-csomópontok memória-, CPU-és tárolási követelményeinek meghatározásához használandó feltételeket. A teljesítmény *-alapú* méretezést vagy *a helyszínen* is dönthet úgy, hogy figyelembe venné a teljesítménnyel kapcsolatos előzményeket. Ha egyszerűen fel szeretné emelni és váltani szeretne, válassza a helyszíni lehetőséget. A használaton alapuló méretezés beszerzéséhez válassza a teljesítmény-alapú lehetőséget. |
| **Teljesítményelőzmények** | Beállítja a gépek teljesítményi adatai kiértékeléséhez szükséges időtartamot. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*. |
| **Százalékos kihasználtság** | Meghatározza a jobb méretezéshez beállított teljesítményi minta százalékos értékét. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés teljesítmény-alapú. |
| **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). A rendszer ezt a puffert alkalmazza a virtuális gépek (CPU, memória és lemez) gépi kihasználtsági adataira. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése. Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. |
| **Ajánlat** | Megjeleníti a regisztrált [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) . Az Azure Migrate ez alapján becsüli meg a költségeket. |
| **Pénznem** | A fiók számlázási pénznemét jeleníti meg. |
| **Kedvezmény (%)** | Felsorolja az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%. |
| **Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Bár a node-alapú díjszabás miatt nem befolyásolja az Azure VMware-megoldások díjszabását, az ügyfelek továbbra is alkalmazhatják az AVS-ben a helyszíni operációs rendszert vagy az SQL-licenceket (Microsoft-alapú) az Azure Hybrid Benefits használatával. Más szoftveres operációs rendszer-gyártóknak meg kell adniuk a saját licencelési feltételeit, például a RHEL. |
| **vCPU-előfizetések** | Megadja a virtuális magok számának az AVS-csomópontban egy fizikai maghoz kötött arányát. A számítások alapértelmezett értéke 4 vCPU: 1 fizikai mag az AVS-ben. Az API-felhasználók egész számként állíthatja be ezt az értéket. Vegye figyelembe, hogy a vCPU-előfizetések > 4:1 a CPU-használattól függően befolyásolhatják a számítási feladatokat. A méretezés során mindig feltételezzük a kiválasztott magok 100%-os kihasználtságát. |
| **Memória túlérvényesítésének tényezője** | Meghatározza, hogy a rendszer milyen arányban adja meg a memóriát a fürtön. Az 1 érték a 100%-os memória-használatot jelöli, a 0,5 például a 50%, a 2 pedig a rendelkezésre álló memória 200%-át használja. 0,5 és 10 közötti értékeket csak egy tizedesjegyre adhat hozzá. |
| **Dedupe és tömörítési tényező** | Meghatározza a számítási feladatok várható kibontási és tömörítési tényezőjét. A tényleges értéket a helyszíni vSAN vagy a Storage-konfigurációból lehet beolvasni. Ezek a munkaterhelés szerint változnak. A 3 érték azt jelenti, hogy a 300GB lemez csak a 100 GB tárolót használja. Az 1 érték a dedupe vagy a Compression értéket sem fogja jelenteni. Csak 1 és 10 közötti értékeket adhat hozzá egy tizedes tört értékhez. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware-megoldás (AVS) megfelelőségi elemzés

Az AVS-értékelések a számítógép tulajdonságainak áttekintésével értékelik az egyes helyszíni virtuális gépeket az AVS megfelelőségének érdekében. Emellett az egyes mért gépeket a következő alkalmassági kategóriák valamelyikébe rendeli:

- **AVS-re kész**: a gép áttelepíthető az Azure-ba (AVS) módosítás nélkül. A teljes AVS-támogatással indul az AVS-ben.
- Felmerülő **feltételek**: a virtuális gép kompatibilitási problémákba ütközhet a jelenlegi vSphere-verzióval, valamint az esetlegesen vmware-eszközök és egyéb beállítások megkövetelésével, mielőtt a virtuális gép teljes funkcionalitása elérhető legyen az AVS-ben.
- **Nem áll készen az AVS-re**: a virtuális gép nem indul el az AVS-ben. Ha például a helyszíni VMware virtuális gép rendelkezik külső eszközzel, például CD-ROM-meghajtóval, akkor a VMware VMotion művelet meghiúsul (ha VMware VMotion használ).
- **Felkészültség ismeretlen**: Azure Migrate nem tudta megállapítani a gép készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

Az értékelés megvizsgálja a számítógép tulajdonságait, hogy meghatározza a helyszíni gép Azure-készültségét.

### <a name="machine-properties"></a>Számítógép tulajdonságai

Az értékelés a helyszíni virtuális gép következő tulajdonságát vizsgálja meg annak megállapításához, hogy futtatható-e az Azure VMware-megoldáson (AVS).

| **Tulajdonság** | **Részletek** | **AVS-készültségi állapot** |
| - | - | - |
| **Internet Protocol** | Az Azure jelenleg nem támogatja a végpontok közötti IPv6-alapú internetes címzést. Ha a rendszer azt észleli, hogy a gép IPv6-ot használ, forduljon a helyi MSFT AVS GBB-csapathoz segítségért. | Feltételesen kész Internet Protocol |

### <a name="guest-operating-system"></a>Vendég operációs rendszer

Az AVS-értékelések jelenleg nem tekintik át az operációs rendszert a megfelelőségi elemzés részeként. A helyszíni virtuális gépeken futó összes operációs rendszer valószínűleg Azure VMware-megoldáson (AVS) fut.

A virtuális gép tulajdonságai mellett az értékelés a gépek vendég operációs rendszerét vizsgálja, hogy az az Azure-on is futtatható-e.

## <a name="sizing"></a>Méretezés

Miután egy gép az AVS-re készként van megjelölve, az AVS Assessment lehetővé teszi a csomópontok méretezését, amely magában foglalja a megfelelő helyszíni virtuálisgép-követelmények azonosítását és a szükséges AVS-csomópontok teljes számának megállapítását. Ezek az ajánlások a megadott értékelési tulajdonságokkal függően változnak.

- Ha az értékelés *teljesítmény-alapú méretezést* használ, Azure Migrate figyelembe veszi a gép teljesítmény-előzményeit, hogy az AVS-re vonatkozó megfelelő méretezési javaslatok legyenek. Ez a módszer különösen akkor hasznos, ha túlterhelte a helyszíni virtuális gépet, de a kihasználtság alacsony, és a virtuális gépet az AVS-ben szeretné megtakarítani a költségek megtakarítása érdekében. Ez a módszer segít optimalizálni a méreteket az áttelepítés során.
- Ha nem szeretné figyelembe venni a virtuális gép méretezéséhez szükséges teljesítményadatokat, és a helyszíni gépeket az AVS-be szeretné állítani, akkor a méretezési feltételeket * a helyszíni * értékre állíthatja. Ezután az értékelés a helyszíni konfiguráció alapján méretezi a virtuális gépeket a kihasználtsági adatmennyiség figyelembevétele nélkül.

### <a name="ftt-sizing-parameters"></a>Pénzügyi tranzakció méretezési paraméterei

Az AVS-ben használt vSAN. a vSAN tárolási házirendjei a virtuális gépek tárolási követelményeit határozzák meg. Ezek a szabályzatok garantálják a virtuális gépek szükséges szolgáltatásszintjét, mivel meghatározzák, hogy a tároló hogyan legyen lefoglalva a virtuális gépnek. Az elérhető FTT-Raid kombinációk a következők:

| **Megengedhető hibák (FTT)** | **RAID-konfiguráció** | **Minimálisan szükséges gazdagépek száma** | **Méretezési szempontok** |
| - | - | - | - |
| 1 | RAID-1 (tükrözés) | 3 | Egy 100 GB-os virtuális gép 200 GB-ot használna fel. |
| 1 | RAID-5 (törléskódolás) | 4 | Egy 100 GB-os virtuális gép 133,33 GB-ot használna fel. |
| 2 | RAID-1 (tükrözés) | 5 | Egy 100 GB-os virtuális gép 300 GB-ot használna fel. |
| 2 | RAID-6 (törléskódolás) | 6 | Egy 100 GB-os virtuális gép 150 GB-ot használna fel. |
| 3 | RAID-1 (tükrözés) | 7 | Egy 100 GB-os virtuális gép 400 GB-ot használna fel. |

### <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezés

A teljesítmény-alapú méretezéshez Azure Migrate készülék profilja a helyszíni környezet a CPU, a memória és a lemez teljesítményadatait gyűjti. Így az AVS teljesítmény-alapú méretezése figyelembe veszi a lefoglalt lemezterületet, és a memória és a processzor kiválasztott százalékos kihasználtságát használja. Ha például egy virtuális gép 4vCores van lefoglalva, de csak 25%-ot használ, akkor az AVS az adott virtuális gép 1vCore méretét fogja használni.

**Teljesítményadatok gyűjtésének lépései:**

1. A VMware virtuális gépek esetében a Azure Migrate készülék minden 20 másodperces intervallumban gyűjt valós idejű mintavételi pontot.
2. A készülék 10 percenként gyűjti össze a mintavételi pontokat, és az elmúlt 10 percben a maximális értéket küldi el Azure Migrate.
3. A Azure Migrate az elmúlt egy hónapban az összes 10 perces mintavételi pontot tárolja. Ezután a *teljesítmény előzményeihez* és a *percentilis kihasználtságához* megadott értékelési tulajdonságoktól függően azonosítja a megfelelő adatpontot, amelyet a jobb méretezés érdekében használ. Ha például a teljesítmény előzményeinek értéke 1 nap, a percentilis kihasználtsága pedig a 95. percentilis, a Azure Migrate az elmúlt egy nap 10 perces mintavételi pontját használja, növekvő sorrendbe rendezi őket, és kiválasztja a 95. percentilis értékét a jobb méretezés érdekében.
4. Ennek az értéknek a megszorozza a komforttal, hogy a készülék által gyűjtött egyes mérőszámok (CPU-kihasználtság és memóriahasználat) tényleges teljesítmény-kihasználtsági adatai meglegyenek.

A tényleges kihasználtsági érték meghatározása után a tároló, a hálózat és a számítási méret a következőképpen lesz kezelve.

**Tárolási méretezés**: Azure Migrate a helyszíni virtuális gép teljes lemezterületét használja számítási paraméterként az AVS vSAN Storage-követelmények meghatározásához az ügyfél által választott tranzakciós beállítás mellett. Pénzügyi adó – az elviselni kívánt hibák, valamint a minimálisan szükséges csomópontok száma a TRANZAKCIÓs szolgáltatásokra vonatkozóan beállítás határozza meg a virtuálisgép-lemezre vonatkozó követelménysel összevont teljes vSAN-tárolást. A tárterület kihasználtságát jelenleg nem vesszük figyelembe, és a logika csak a lefoglalt tárterületet vizsgálja virtuális gépenként.

**Hálózati méretezés**: az AVS-értékelések jelenleg nem tesznek figyelembe semmilyen hálózati beállítást.

**Számítási méretezés**: a tárolási követelmények kiszámítását követően az AVS ASSESSMENT a processzor-és memória-követelményeket úgy vizsgálja, hogy meghatározza a csomópont típusa alapján az AVS számára szükséges csomópontok számát.

- A méretezési feltételek alapján az AVS Assessment a teljesítmény-alapú virtuális gép vagy a helyszíni virtuális gép konfigurációját vizsgálja. A Comfort Factor beállítás lehetővé teszi a fürt növekedési tényezőjének megadását. A hiperszálkezelés jelenleg alapértelmezés szerint engedélyezett, így a 36 maggal rendelkező csomópontok 72 virtuális magot fognak tartalmazni. A rendszer fizikai magonként 4 virtuális magot használ a fürtönkénti processzor-küszöbértékek meghatározására a VMware követelményének használatával, amely szerint a kihasználtság nem haladhatja meg a 80%-ot, hogy lehetővé tegye a karbantartást vagy a hibák kezelését a fürt rendelkezésre állásának veszélyeztetése nélkül. Jelenleg nem érhető el felülbírálás a túljegyzési értékek módosításához, és lehetséges, hogy ez a későbbi verziókban van.

### <a name="as-on-premises-sizing"></a>Helyszíni méretezés

Ha helyszíni *méretezést* használ, az AVS Assessment nem veszi figyelembe a virtuális gépek és lemezek teljesítményének előzményeit. Ehelyett az AVS-csomópontokat a helyszínen lefoglalt méret alapján foglalja le. Az alapértelmezett tárolási típus a vSAN az AVS-ben.

[További](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) információ az Azure VMware-megoldás értékelésének áttekintéséről.

### <a name="cpu-utilization-on-avs-nodes"></a>CPU-használat AVS-csomópontokon

A CPU-kihasználtság az elérhető magok 100%-os használatát feltételezi. A szükséges csomópontok számának csökkentése érdekében az 4:1 6:1-es túllépést a számítási feladatok jellemzői és a helyszíni tapasztalatok alapján növelheti. A lemeztől eltérően az AVS nem korlátozza a CPU-kihasználtságot, hanem az ügyfelek számára, hogy gondoskodjon a fürt optimális működéséről, így ha a "Running Hot" beállítás megfelelően van szükség. A növekedés növeléséhez csökkentse az előfizetést, vagy növelje a növekedési faktor értékét.

A CPU-használat már a vCenter, a NSX Manager és más kisebb erőforrások felügyeletének terhelését is magában foglalja.

### <a name="memory-utilization-on-avs-nodes"></a>Memóriahasználat az AVS-csomópontokon

A memóriahasználat az összes csomópont és a kiszolgáló vagy a munkaterhelések követelményeinek teljes memóriáját mutatja. A memória felhasználható az előfizetésben, és az AVS nem korlátozza a korlátot, és az ügyfél az optimális szektorcsoport-teljesítményt futtatja a számítási feladatokhoz.

A memória kihasználtsága már a vCenter, a NSX Manager és más kisebb erőforrások felügyeletének terhelését is magában foglalja.

### <a name="storage-utilization-on-avs-nodes"></a>Tárterület-használat AVS-csomópontokon

A tárterület kihasználtsága a következő szakasz alapján számítható ki:

1. A virtuális gépekhez szükséges méret (vagy a kiosztott vagy a teljesítmény-alapú felhasznált terület)
2. A növekedési tényező alkalmazása, ha van ilyen
3. Felügyeleti terhelés hozzáadása és a TRANZAKCIÓs arány alkalmazása
4. Dedupe és tömörítési tényező alkalmazása
5. Szükséges 25%-os tartalékidő alkalmazása a vSAN
6. A teljes tárterületen kívüli virtuális gépek számára elérhető tárterület, beleértve a felügyeleti terhelést is.

A 3 csomópontos fürtön elérhető tárterület a RAID-1 alapértelmezett tárolási házirenden alapul, amely sűrű kiosztást használ. Ha például kiszámítja a törlési vagy RAID-5 kódolást, legalább 4 csomópontra van szükség. Vegye figyelembe, hogy az AVS-ben a tárolási házirendet a rendszergazdának kell módosítania, hogy több helyet engedélyezzen.

## <a name="confidence-ratings"></a>Megbízhatósági minősítések

Azure Migrate minden teljesítmény-alapú értékelése egy olyan megbízhatósági minősítéssel van társítva, amely egy (legalacsonyabb) és öt csillag közötti (a legmagasabb) tartományba esik.

- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítések nem alkalmazhatók *a* helyszíni értékelésekhez.
- A teljesítmény-alapú méretezés érdekében az AVS-értékelésekhez a processzor és a virtuális gép memóriájának kihasználtsági adatai szükségesek. A rendszer a következő adatokat gyűjti össze, de nem használja az AVS méretezési javaslataiban:

  - A lemez a virtuális géphez csatolt minden lemez IOPS és adatátviteli adatokkal rendelkezik.
  - A hálózati I/O-t a virtuális géphez csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

  Ha ezek a kihasználtsági számok nem érhetők el vCenter Serverban, előfordulhat, hogy a méretre vonatkozó javaslat nem megbízható.

Az elérhető adatpontok százalékos arányának függvényében az értékelés megbízhatósági minősítése az alábbiak szerint megy végbe.

| **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés** |
| - | - |
| 0-20% | 1 csillag |
| 21-40% | 2 csillag |
| 41-60% | 3 csillag |
| 61-80% | 4 csillag |
| 81-100% | 5 csillag |

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítések

Íme néhány ok, amiért egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem tudta felmérni a környezetét arra az időtartamra, amelyhez az értékelést létrehozza. Ha például az értékelést egy napra állítja be, akkor az összes adatpont felderítésének megkezdése után legalább egy napot várnia kell a begyűjtéshez.
- Az értékelés nem tudja összegyűjteni az értékelési időszakban egy vagy több virtuális gép teljesítményadatait. Magas megbízhatósági minősítés esetén ügyeljen a következőre:

  - A virtuális gépek az értékelés időtartamára vannak bekapcsolva
  - A 443-es portokon engedélyezett kimenő kapcsolatok engedélyezettek
  - Hyper-V virtuális gépek dinamikus memóriájának engedélyezése

  Számítsa újra az értékelést, hogy tükrözze a megbízhatósági minősítés legújabb módosításait.
- Néhány virtuális gép az értékelés kiszámításának ideje alatt lett létrehozva. Tegyük fel például, hogy létrehozta az előző hónap teljesítmény-előzményeinek értékelését, de néhány virtuális gép csak egy hetet hozott létre. Ilyen esetekben az új virtuális gépek teljesítményadatai a teljes időtartamra vonatkozóan nem lesznek elérhetőek, és a megbízhatósági minősítés alacsony lesz.

> [!NOTE]
> Ha az értékelések megbízhatósági minősítése kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék felkeresi a környezetet, majd számítsa ki újra az értékelést. Ha nem, akkor előfordulhat, hogy a teljesítmény-alapú méretezés nem megbízható. Ebben az esetben javasoljuk, hogy az értékelést a helyszíni méretezésre állítsa át.

## <a name="monthly-cost-estimation"></a>Havi költségbecslés

A méretezési javaslatok befejezését követően a Azure Migrate kiszámítja a helyszíni számítási feladatok az AVS-ben való futtatásának teljes költségét a csomóponti ár által igényelt AVS-csomópontok számának szorzatával. A virtuális gépekre vonatkozó költségeket úgy számítjuk ki, hogy a teljes költségeket az értékelésben szereplő virtuális gépek száma alapján osztjuk fel.

- A számítás a szükséges csomópontok számát, a csomópont típusát és helyét veszi figyelembe.
- A teljes havi költség kiszámításához összesíti a költségeket az összes csomóponton.
- A költségek az értékelési beállításokban megadott pénznemben jelennek meg.

Mivel az Azure VMware-megoldás (AVS) díjszabása csomópontos, a teljes költség nem rendelkezik számítási költséggel és a tárolási költség eloszlásával. [További információ](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Áttelepítési eszköz – útmutató

Az Azure VMware Solution- (AVS-) értékelés Azure-kompatibilitási jelentésében a következő ajánlott eszközök szerepelnek:

- **VMware HCX vagy Enterprise**: VMware-es gépek esetén a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Ismeretlen**: A CSV-fájllal importált gépek esetében az alapértelmezett migrálási eszköz ismeretlen. A VMware-es gépek esetében azonban ajánlott a VMware Hybrid Cloud Extension (HCX) megoldás használata.

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy értékelést az [AVS VMWare virtuális gépekhez](how-to-create-azure-vmware-solution-assessment.md).
