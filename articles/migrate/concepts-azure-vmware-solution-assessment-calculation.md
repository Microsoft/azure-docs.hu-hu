---
title: AVS Assessment-számítások a Azure Migrateban | Microsoft Docs
description: Áttekintést nyújt az AVS Assessment számításokról a Azure Migrate szolgáltatásban.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: a489902372b0b88a0ffec07fa685ee745ec59c50
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880457"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>A kiszolgáló értékelésének áttekintése (Migrálás az Azure VMware-megoldásba)

[Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és munkaterhelések felderítésének, értékelésének és áttelepítésének nyomon követéséhez. Emellett nyomon követi a privát és a nyilvános Felhőbeli példányokat az Azure-ban. Az elosztó Azure Migrate eszközöket kínál az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatait.

A Server Assessment olyan Azure Migrate eszköz, amely a helyszíni kiszolgálókat az Azure IaaS Virtual Machines és az Azure VMware Solution (AVS) szolgáltatásba való áttelepítésre értékeli. Ez a cikk arról nyújt tájékoztatást, hogyan számítják ki az Azure VMware-megoldás (AVS) értékelését.

> [!NOTE]
> Az Azure VMware Solution (AVS) felmérés jelenleg előzetes verzióban érhető el, és csak VMware virtuális gépekhez hozható létre.

## <a name="types-of-assessments"></a>Az értékelések típusai

A kiszolgáló-értékeléssel létrehozott értékelések az adatok időpontra vonatkozó pillanatképei. A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V virtuális gépeket](how-to-set-up-appliance-hyper-v.md)és a [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) felhasználhatja az Azure-ba való áttelepítéshez ezzel az értékelési típussal. [További információ](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> Az Azure VMware Solutionbe (AVS-be) történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md).[További információ](concepts-azure-vmware-solution-assessment-calculation.md)

Az Azure VMware Solution (AVS) értékelése a kiszolgáló értékelése során két méretezési feltétel közül választhat:

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A helyszíni virtuális gépek összegyűjtött teljesítményadatok alapján végzett értékelések. | **Ajánlott csomópont mérete**: a CPU és a memória kihasználtsági adatai, valamint a csomópont típusa, a tárolási típus és a pénzügyi tranzakciós beállítás alapján, amelyet az értékeléshez választott.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott csomópont mérete**: a helyszíni virtuális gép méretétől, valamint a csomópont típusától, a tárolási típustól és az értékeléshez kiválasztott tranzakciós beállítástól függően.

## <a name="how-do-i-run-an-assessment"></a>Hogyan egy értékelést?

Több módon is futtathat értékeléseket.

- A gépeket egy egyszerű Azure Migrate berendezés által gyűjtött kiszolgálói metaadatok használatával értékelheti ki. A készülék felfedi a helyszíni gépeket. Ezután a számítógép metaadatainak és teljesítményadatokat küld Azure Migrate.
- A gépeket a vesszővel tagolt (CSV) formátumban importált kiszolgálói metaadatok használatával értékelheti ki.

## <a name="how-do-i-assess-with-the-appliance"></a>Hogyan az értékelést a berendezéssel?

Ha Azure Migrate berendezést helyez üzembe a helyszíni kiszolgálók felderítése érdekében, hajtsa végre a következő lépéseket:

1. Az Azure és a helyszíni környezet beállítása a kiszolgáló-értékeléssel való együttműködéshez.
2. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a kiszolgáló-értékelési eszközt.
3. Egyszerűsített Azure Migrate berendezés üzembe helyezése. A készülék folyamatosan felfedi a helyszíni gépeket, és számítógép-metaadatokat és teljesítményadatokat küld Azure Migrate. Telepítse a készüléket virtuális gépre. Semmit nem kell telepítenie az értékelni kívánt gépekre.

Miután a készülék megkezdte a számítógép-felderítést, összegyűjtheti azokat a gépeket, amelyeket fel szeretne mérni egy csoportba, és értékelést kell futtatnia a csoportnak az **Azure VMware Solution (AVS)** típusú értékeléssel.

Hozza létre az első Azure VMware-megoldás (AVS) értékelését az [itt](how-to-create-azure-vmware-solution-assessment.md)leírt lépéseket követve.

## <a name="how-do-i-assess-with-imported-data"></a>Hogyan az importált adattal való értékelést?

Ha egy CSV-fájl használatával értékeli a kiszolgálókat, nincs szüksége berendezésre. Ehelyett hajtsa végre a következő lépéseket:

1. Állítsa be az Azure-t, hogy működjön a kiszolgáló értékelésével.
2. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a kiszolgáló-értékelési eszközt.
3. Töltsön le egy CSV-sablont, és adja hozzá a kiszolgálói adatfájlokat.
4. Importálja a sablont a kiszolgálói felmérésbe.
5. Az importálással hozzáadott kiszolgálók felderítése, összegyűjtése egy csoportba, és értékelés futtatása a csoportnak az **Azure VMware Solution (AVS)** típusú értékeléssel.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Ha a Azure Migrate berendezést értékelésre használja, ismerkedjen meg a [VMware](migrate-appliance.md#collected-data---vmware)-hez gyűjtött metaadatokkal és teljesítményadatokat.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hogyan számítja ki a készülék a teljesítményadatokat?

Ha a készüléket a felderítéshez használja, a következő lépésekkel gyűjt teljesítményadatokat a számítási beállításokhoz:

1. A készülék valós idejű mintavételi pontot gyűjt.

    - **VMWare virtuális gépek**: a rendszer 20 másodpercenként gyűjt egy mintavételi pontot.

2. A készülék 10 percenként egyesíti a mintavételi pontokat, hogy egyetlen adatpontot hozzon létre. Az adatpont létrehozásához a készülék kiválasztja az összes minta csúcsérték-értékeit. Ezután elküldi az adatpontot az Azure-nak.
3. A kiszolgáló értékelése az elmúlt hónapban az összes 10 perces adatpontot tárolja.
4. Értékelés létrehozásakor a kiszolgáló értékelése azonosítja a megadásában használandó megfelelő adatpontot. Az azonosítás a *teljesítmény előzményeinek* és a *percentilis kihasználtságának* százalékos értékein alapul.

    - Ha például a teljesítmény előzményei egy hét, a percentilis kihasználtsága pedig a 95. percentilis, a kiszolgáló értékelése az elmúlt hét 10 perces mintavételi pontjait rendezi. Növekvő sorrendben rendezi őket, és kiválasztja a 95. percentilis értékét a megadásában.
    - A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiválasztotta a esetek 99% percentilis értékét.
    - Ha ki szeretné választani az időszakhoz tartozó csúcsérték-használatot, és nem szeretne kiugró értékeket kihagyni, válassza a esetek 99% percentilis értéket a percentilis kihasználtsága mezőben.

5. Ennek az értéknek a megszorozza a komforttal, hogy a készülék által összegyűjtött mérőszámok tényleges teljesítmény-kihasználtsági adatai meglegyenek:

    - Processzorhasználat
    - RAM kihasználtsága
    - Lemez IOPS (olvasás és írás)
    - Lemez átviteli sebessége (olvasás és írás)
    - Hálózati átviteli sebesség (be és ki)

A rendszer a következő teljesítményadatokat gyűjti, de nem használja az AVS-értékelések méretezési javaslataiban:
  - A lemez a virtuális géphez csatolt minden lemez IOPS és adatátviteli adatokkal rendelkezik.
  - A hálózati I/O-t a virtuális géphez csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

## <a name="how-are-avs-assessments-calculated"></a>Hogyan történik az AVS-értékelések kiszámítása?

A kiszolgáló értékelése a helyszíni gépek metaadatait és teljesítményadatait használja az értékelések kiszámításához. Ha telepíti a Azure Migrate készüléket, az értékelés a készülék által gyűjtött adatokat használja. Ha azonban egy CSV-fájllal importált értékelést futtat, akkor megadja a számítás metaadatait.

A számítások a következő három szakaszban történnek:

1. **Azure VMware-megoldás (AVS) készültségének kiszámítása**: azt határozza meg, hogy a helyszíni virtuális gépek alkalmasak-e az Azure VMware-megoldásra (AVS) való áttelepítésre.
2. Az **AVS-csomópontok és a kihasználtságok számának kiszámítása a csomópontok között**: a virtuális gépek és a tervezett CPU-, memória-és tárterület-használat összes csomóponton történő futtatásához szükséges AVS-csomópontok becsült száma.
3. **Havi költségbecslés**: a helyszíni virtuális gépeket futtató összes Azure VMware Solution (AVS) csomópont becsült havi költségei.

A számítások az előző sorrendben vannak. A számítógép-kiszolgálók csak akkor mozdulnak el egy későbbi fázisra, ha az előzőt átadja. Ha például egy kiszolgáló meghibásodik az AVS készültségi fázisában, az az Azure számára nem megfelelőként van megjelölve. A méretezési és költségszámítási műveletek nem lettek végrehajtva az adott kiszolgálón

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Mi az Azure VMware-megoldás (AVS) értékelése?

A kiszolgáló értékelése során a következő, az AVS-értékelés részét képezi:


| **Tulajdonság** | **Részletek** 
| - | - 
| **Célhely** | Megadja azt az AVS saját Felhőbeli helyet, amelyre az áttelepítést szeretné végezni.<br/><br/> A Server Assessment szolgáltatásban az AVS Assessment jelenleg a következő célcsoportokat támogatja: USA keleti régiója, Nyugat-Európa, USA nyugati régiója. 
| **Tárolási típus** | Megadja az AVS-ben használni kívánt tárolási motort.<br/><br/> Az AVS-értékelések csak a vSAN támogatják alapértelmezett tárolási típusként. 
**Fenntartott példányok (RIs)** | Ez a tulajdonság segít a fenntartott példányok megadásában az AVS-ben. A RIs jelenleg nem támogatott az AVS-csomópontok esetében. 
**Csomópont típusa** | Megadja a helyszíni virtuális gépek leképezéséhez használt [AVS-csomópont típusát](../azure-vmware/concepts-private-clouds-clusters.md) . Az alapértelmezett csomópont típusa AV36. <br/><br/> Azure Migrate a virtuális gépek AVS-re való áttelepítéséhez szükséges csomópontok számát javasolja. 
**TRANZAKCIÓs beállítás, RAID-szint** | Meghatározza a megfelelő meghibásodást az eltűriés és a RAID-kombinációk esetében. A helyszíni virtuálisgép-lemezre vonatkozó követelményekkel együtt a kiválasztott TRANZAKCIÓs beállítás határozza meg az AVS-ben szükséges teljes vSAN-tárolót. 
**Méretezési feltétel** | Az AVS *-hez megfelelő méretű* virtuális gépekhez használandó feltételek beállítása. A teljesítmény *-alapú* méretezést vagy *a helyszínen* is dönthet úgy, hogy figyelembe venné a teljesítménnyel kapcsolatos előzményeket. 
**Teljesítményelőzmények** | Beállítja a gépek teljesítményi adatai kiértékeléséhez szükséges időtartamot. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*. 
**Százalékos kihasználtság** | Meghatározza a jobb méretezéshez beállított teljesítményi minta százalékos értékét. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés teljesítmény-alapú.
**Kényelmi faktor** | Azure Migrate kiszolgáló értékelése az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. 
**Ajánlat** | Megjeleníti a regisztrált [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) . Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A fiók számlázási pénznemét jeleníti meg. 
**Kedvezmény (%)** | Felsorolja az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%. 
**Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Bár a node-alapú díjszabás miatt nem befolyásolja az Azure VMware-megoldások díjszabását, az ügyfelek továbbra is alkalmazhatják a helyszíni OS-licenceket (Microsoft-alapú) az AVS-ben az Azure Hybrid Benefits használatával. Más szoftveres operációs rendszer-gyártóknak meg kell adniuk a saját licencelési feltételeit, például a RHEL. 
**vCPU-előfizetések** | Megadja a virtuális magok számának az AVS-csomópontban egy fizikai maghoz kötött arányát. A számítások alapértelmezett értéke 4 vCPU: 1 fizikai mag az AVS-ben. <br/><br/> Az API-felhasználók egész számként állíthatja be ezt az értéket. Vegye figyelembe, hogy a vCPU-előfizetések > 4:1 a CPU-használattól függően befolyásolhatják a számítási feladatokat. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware-megoldás (AVS) megfelelőségi elemzés

A kiszolgáló értékelésével kapcsolatos AVS-értékelések a számítógép tulajdonságainak áttekintésével értékelik az egyes helyszíni virtuális gépeket az AVS-megfelelőség szempontjából. Emellett az egyes mért gépeket a következő alkalmassági kategóriák valamelyikébe rendeli:

- **AVS-re kész**: a gép áttelepíthető az Azure-ba (AVS) módosítás nélkül. A teljes AVS-támogatással indul az AVS-ben.
- Felmerülő **feltételek**: a virtuális gép kompatibilitási problémákba ütközhet a jelenlegi vSphere-verzióval, valamint az esetlegesen vmware-eszközök és egyéb beállítások megkövetelésével, mielőtt a virtuális gép teljes funkcionalitása elérhető legyen az AVS-ben.
- **Nem áll készen az AVS-re**: a virtuális gép nem indul el az AVS-ben. Ha például a helyszíni VMware virtuális gép rendelkezik külső eszközzel, például CD-ROM-meghajtóval, akkor a VMware VMotion művelet meghiúsul (ha VMware VMotion használ).
- **Felkészültség ismeretlen**: Azure Migrate nem tudta megállapítani a gép készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

A kiszolgáló értékelése ellenőrzi a számítógép tulajdonságait, hogy meghatározza a helyszíni gép Azure-készültségét.

### <a name="machine-properties"></a>Számítógép tulajdonságai

A kiszolgáló értékelése a helyszíni virtuális gép következő tulajdonságát vizsgálja meg annak megállapításához, hogy futtatható-e az Azure VMware-megoldáson (AVS).


| **Tulajdonság** | **Részletek** | **AVS-készültségi állapot** 
| - | - | - 
| **Internet Protocol** | Az AVS jelenleg nem támogatja az IPv6-alapú internetcímeket.<br/><br/> Ha a rendszer azt észleli, hogy a gép IPv6-ot használ, forduljon a helyi MSFT AVS GBB-csapathoz segítségért.| Feltételesen kész Internet Protocol


### <a name="guest-operating-system"></a>Vendég operációs rendszer

Az AVS-értékelések jelenleg nem tekintik át az operációs rendszert a megfelelőségi elemzés részeként. A helyszíni virtuális gépeken futó összes operációs rendszer valószínűleg Azure VMware-megoldáson (AVS) fut.

A virtuális gép tulajdonságaival együtt a kiszolgáló értékelése a gépek vendég operációs rendszerét vizsgálja annak megállapítására, hogy futtatható-e az Azure-ban.


## <a name="sizing"></a>Méretezés

Miután egy gép az AVS-re készként van megjelölve, a kiszolgáló értékelése során az AVS Assessment a csomópontok méretezésére vonatkozó javaslatokat tartalmaz, amely magában foglalja a megfelelő helyszíni virtuálisgép-követelmények azonosítását és a szükséges AVS-csomópontok teljes számának megállapítását. Ezek az ajánlások a megadott értékelési tulajdonságokkal függően változnak.

- Ha az értékelés *teljesítmény-alapú méretezést* használ, Azure Migrate figyelembe veszi a gép teljesítmény-előzményeit, hogy az AVS-re vonatkozó megfelelő méretezési javaslatok legyenek. Ez a módszer különösen akkor hasznos, ha túlterhelte a helyszíni virtuális gépet, de a kihasználtság alacsony, és a virtuális gépet az AVS-ben szeretné megtakarítani a költségek megtakarítása érdekében. Ez a módszer segít optimalizálni a méreteket az áttelepítés során.
- Ha nem szeretné figyelembe venni a virtuális gép méretezésére szolgáló teljesítményadatokat, és a helyszíni gépeket az AVS-re szeretné használni, beállíthatja a méretezési feltételeket *a helyszíni értékre.* Ezt követően a kiszolgáló értékelése a helyszíni konfiguráció alapján méretezi a virtuális gépeket a kihasználtsági adatmennyiség figyelembevétele nélkül. 


### <a name="ftt-sizing-parameters"></a>Pénzügyi tranzakció méretezési paraméterei

Az AVS-ben használt vSAN. a vSAN tárolási házirendjei a virtuális gépek tárolási követelményeit határozzák meg. Ezek a szabályzatok garantálják a virtuális gépek szükséges szolgáltatásszintjét, mivel meghatározzák, hogy a tároló hogyan legyen lefoglalva a virtuális gépnek. Az elérhető FTT-Raid kombinációk a következők: 

**Megengedhető hibák (FTT)** | **RAID-konfiguráció** | **Minimálisan szükséges gazdagépek száma** | **Méretezési szempontok**
--- | --- | --- | --- 
1 | RAID-1 (tükrözés) | 3 | Egy 100 GB-os virtuális gép 200 GB-ot használna fel.
1 | RAID-5 (törléskódolás) | 4 | Egy 100 GB-os virtuális gép 133,33 GB-ot használna fel.
2 | RAID-1 (tükrözés) | 5 | Egy 100 GB-os virtuális gép 300 GB-ot használna fel.
2 | RAID-6 (törléskódolás) | 6 | Egy 100 GB-os virtuális gép 150 GB-ot használna fel.
3 | RAID-1 (tükrözés) | 7 | Egy 100 GB-os virtuális gép 400 GB-ot használna fel.

### <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezés

A teljesítmény-alapú méretezés érdekében a kiszolgáló értékelése a virtuális géphez csatlakoztatott lemezekkel kezdődik, majd a hálózati adapterek. Ezután leképezi a virtuális gép követelményeit az AVS megfelelő számú csomópontjára. A Azure Migrate berendezés a helyszíni környezetet a CPU, a memória, a lemezek és a hálózati adapter teljesítményadatait gyűjti.

**Teljesítményadatok gyűjtésének lépései:**

1. A VMware virtuális gépek esetében a Azure Migrate készülék minden 20 másodperces intervallumban gyűjt valós idejű mintavételi pontot. 
2. A készülék 10 percenként gyűjti össze a mintavételi pontokat, és az elmúlt 10 percben a kiszolgáló értékeléséhez elküldi a maximális értéket.
3. A kiszolgáló értékelése az elmúlt egy hónapban az összes 10 perces mintavételi pontot tárolja. Ezután a *teljesítmény előzményeihez* és a *percentilis kihasználtságához* megadott értékelési tulajdonságoktól függően azonosítja a megfelelő adatpontot, amelyet a jobb méretezés érdekében használ. Ha például a teljesítmény-előzmények értéke 1 nap, a percentilis kihasználtsága pedig a 95. percentilis, a Server Assessment az elmúlt egy nap 10 perces mintavételi pontját használja, növekvő sorrendbe rendezi őket, és kiválasztja a 95. percentilis értékét a jobb méretezés érdekében.
4. Ennek az értéknek a megszorozza a komforttal, hogy az egyes mérőszámok teljesítménybeli kihasználtsági adatait (CPU-kihasználtság, memória kihasználtsága, lemez IOPS (olvasás és írás), a lemez átviteli sebességét (olvasási és írási), valamint a készülék által gyűjtött hálózati átviteli sebességet (be és ki) kell beolvasni.

A tényleges kihasználtsági érték meghatározása után a tároló, a hálózat és a számítási méret a következőképpen lesz kezelve.

**Tárolási méretezés**: Azure Migrate a helyszíni virtuális gép teljes lemezterületét használja számítási paraméterként az AVS vSAN Storage-követelmények meghatározásához az ügyfél által választott tranzakciós beállítás mellett. Pénzügyi adó – az elviselni kívánt hibák, valamint a minimálisan szükséges csomópontok száma a TRANZAKCIÓs szolgáltatásokra vonatkozóan beállítás határozza meg a virtuálisgép-lemezre vonatkozó követelménysel összevont teljes vSAN-tárolást.

**Hálózat méretezése**: A Server Assessment jelenleg nem veszi figyelembe a hálózati beállításokat az AVS-értékelések során.

**Számítási méretezés**: a tárolási követelmények kiszámítását követően a kiszolgáló értékelése a processzor-és memória-követelmények alapján határozza meg, hogy hány csomópont szükséges az AVS-hez a csomópont típusa szerint.

- A méretezési feltételek alapján a kiszolgáló értékelése vagy a teljesítmény-alapú virtuálisgép-vagy a helyszíni virtuális gép konfigurációja szerint néz ki. A Comfort Factor beállítás lehetővé teszi a fürt növekedési tényezőjének megadását. A hiperszálkezelés jelenleg alapértelmezés szerint engedélyezett, így a 36 maggal rendelkező csomópontok 72 virtuális magot fognak tartalmazni. A rendszer fizikai magonként 4 virtuális magot használ a fürtönkénti processzor-küszöbértékek meghatározására a VMware követelményének használatával, amely szerint a kihasználtság nem haladhatja meg a 80%-ot, hogy lehetővé tegye a karbantartást vagy a hibák kezelését a fürt rendelkezésre állásának veszélyeztetése nélkül. Jelenleg nem érhető el felülbírálás a túljegyzési értékek módosításához, és lehetséges, hogy ez a későbbi verziókban van.

### <a name="as-on-premises-sizing"></a>Helyszíni méretezés

Ha helyszíni *méretezést* használ, a kiszolgáló értékelése nem veszi figyelembe a virtuális gépek és lemezek teljesítményének előzményeit. Ehelyett az AVS-csomópontokat a helyszínen lefoglalt méret alapján foglalja le. Az alapértelmezett tárolási típus a vSAN az AVS-ben.

[További](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) információ az Azure VMware-megoldás értékelésének áttekintéséről.

## <a name="confidence-ratings"></a>Megbízhatósági minősítések

Azure Migrate minden teljesítmény-alapú értékelése egy olyan megbízhatósági minősítéssel van társítva, amely egy (legalacsonyabb) és öt csillag közötti (a legmagasabb) tartományba esik.

- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítések nem alkalmazhatók *a* helyszíni értékelésekhez.
- A teljesítmény-alapú méretezés érdekében a kiszolgáló-értékeléshez tartozó AVS-értékeléseknek szüksége van a processzor és a virtuális gép memóriájának kihasználtsági adataira. A rendszer a következő adatokat gyűjti össze, de nem használja az AVS méretezési javaslataiban:
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

Vegye figyelembe, hogy mivel az Azure VMware-megoldás (AVS) előzetes verzióban érhető el, az értékelésben szereplő csomóponti árak előzetes árak. Útmutatásért forduljon a helyi MSFT AVS GBB csapatához.

## <a name="migration-tool-guidance"></a>Áttelepítési eszköz – útmutató

Az Azure VMware Solution- (AVS-) értékelés Azure-kompatibilitási jelentésében a következő ajánlott eszközök szerepelnek: 
- **VMware HCX vagy Enterprise**: VMware-es gépek esetén a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Ismeretlen**: A CSV-fájllal importált gépek esetében az alapértelmezett migrálási eszköz ismeretlen. A VMware-es gépek esetében azonban ajánlott a VMware Hybrid Cloud Extension (HCX) megoldás használata.

## <a name="next-steps"></a>További lépések

Hozzon létre egy értékelést az [AVS VMWare virtuális gépekhez](how-to-create-azure-vmware-solution-assessment.md).