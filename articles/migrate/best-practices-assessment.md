---
title: Az Azure Migrate Server Assessment bevált eljárásainak értékelése
description: Tippek az értékelések létrehozásához Azure Migrate Server Assessment alapján.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c1fff5b5b7f6450ad8d1977e55a1f6b255f3d668
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754317"
---
# <a name="best-practices-for-creating-assessments"></a>Ajánlott eljárások az értékelések létrehozásához

[Azure Migrate](./migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.

Ez a cikk Az értékelések Azure Migrate Server Assessment eszközzel történő létrehozásával kapcsolatos ajánlott eljárásokat foglalja össze.

## <a name="about-assessments"></a>Az értékelések ismertetése

Az Azure Migrate Server Assessmenttel létrehozott értékelések az adatok időpontra vonatkozó pillanatképei. Kétféle értékelést hozhat létre Azure Migrate használatával: Server assessment:

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> Az Azure-ba történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V rendszerű virtuális gépeket](how-to-set-up-appliance-hyper-v.md) és [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md). [További információ](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) az értékelés típusának használatával értékelheti az Azure VMware-megoldásba (AVS) való áttelepítésre. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Méretezési feltételek
A kiszolgálóértékelési szolgáltatásban a következő két méretezési feltétel adható meg:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | **Azure-beli virtuális gépek kiértékelése**: A virtuális gép méretére vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.<br/><br/> A lemeztípusra vonatkozó javaslatok (standard HDD/SSD vagy prémium felügyelt lemez) a helyszíni lemezek IOPS értékén és átviteli sebességén alapulnak.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.
**Módosítás nélküli helyszíni** | A javaslatok létrehozásához teljesítményadatokat nem használó kiértékelések. | **Azure-beli virtuális gépek kiértékelése**: A virtuálisgép-méretre vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak<br/><br> A lemeztípus-ajánlás azon alapul, hogy mit választ ki a kiértékeléshez a tárolótípus beállításai között.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak.

#### <a name="example"></a>Példa
Ha például egy helyszíni virtuális gép négy maggal rendelkezik 20%-os kihasználtsággal, és 8 GB memóriát használ 10%-os kihasználtsággal, az Azure-beli virtuális gép értékelése a következő lesz:

- **Teljesítmény-alapú értékelés**:
    - A hatékony magok és memória azonosítása a mag (4 x 0,20 = 0,8) és a memória (8 GB x 0,10 = 0,8) kihasználtsága alapján.
    - Az értékelés tulajdonságaiban megadott kényelmi tényezőt alkalmazza (tegyük fel, hogy 1,3 x) a méretezéshez használandó értékek lekéréséhez. 
    - Az Azure-ban a legközelebbi virtuálisgép-méretet javasolja, amely támogatja a ~ 1,04 mag (0,8 x 1,3) és a ~ 1,04 GB (0,8 x 1,3) memóriát.

- A **(helyszíni) felmérés**:
    -  Négy maggal rendelkező virtuális gépet javasol; 8 GB memória.


## <a name="best-practices-for-creating-assessments"></a>Ajánlott eljárások az értékelések létrehozásához

Az Azure Migrate készülék folyamatosan felméri a helyszíni környezetet, és metaadatokat és teljesítményadatokat küld az Azure-nak. Kövesse az alábbi ajánlott eljárásokat a berendezések használatával felderített kiszolgálók értékeléséhez:

- **Create as-is assessments**: a (z)-as értékeléseket azonnal létrehozhatja, amint a gépek megjelennek a Azure Migrate portálon.
- **Teljesítmény-alapú Értékelés létrehozása**: Miután beállította a felderítést, javasoljuk, hogy várjon legalább egy nappal a teljesítmény-alapú értékelés futtatása előtt:
    - A teljesítményadatok gyűjtése időt vesz igénybe. Legalább egy nap várakozásával gondoskodhat arról, hogy az értékelés futtatása előtt elegendő teljesítményadatok legyenek.
    - Ha teljesítmény-alapú értékeléseket futtat, győződjön meg arról, hogy a környezete az értékelés időtartamára van feldolgozva. Ha például egy hétre állítja össze az értékelést, akkor a felderítés megkezdése után legalább egy hétig meg kell várnia az összes összegyűjtött adatpontot. Ha nem, az értékelés nem kap öt csillagos minősítést.
- **Értékelések újraszámítása**: mivel az értékelések időponthoz kötődő Pillanatképek, nem frissülnek automatikusan a legújabb adattal. Ha frissíteni szeretne egy értékelést a legújabb adattal, újra kell számítania.

Kövesse az alábbi ajánlott eljárásokat a Azure Migrate-ba importált kiszolgálók értékeléséhez. CSV-fájl:

- **Create as-is assessments**: a (z)-as értékeléseket azonnal létrehozhatja, amint a gépek megjelennek a Azure Migrate portálon.
- **Teljesítmény-alapú Értékelés létrehozása**: ez segít a jobb költségbecslés megszerzésében, különösen akkor, ha a helyszíni kiszolgáló kapacitása túl van kiépítve. A teljesítmény-alapú értékelés pontossága azonban a kiszolgálók által megadott teljesítményadatokat függ. 
- **Értékelések újraszámítása**: mivel az értékelések időponthoz kötődő Pillanatképek, nem frissülnek automatikusan a legújabb adattal. Ha frissíteni szeretne egy értékelést a legújabb importált adattal, újra kell számítania.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>Az adó-méretezési paraméterek az AVS-értékelésekhez

Az AVS-ben használt vSAN. A vSAN tárolási szabályzatai határozzák meg a virtuális gépek tárolási követelményeit. Ezek a szabályzatok garantálják a virtuális gépek szükséges szolgáltatásszintjét, mivel meghatározzák, hogy a tároló hogyan legyen lefoglalva a virtuális gépnek. Az elérhető FTT-RAID-kombinációk a következők: 

**Megengedhető hibák (FTT)** | **RAID-konfiguráció** | **Minimálisan szükséges gazdagépek száma** | **Méretezési szempontok**
--- | --- | --- | --- 
1 | RAID-1 (tükrözés) | 3 | Egy 100 GB-os virtuális gép 200 GB-ot használna fel.
1 | RAID-5 (törléskódolás) | 4 | Egy 100 GB-os virtuális gép 133,33 GB-ot használna fel.
2 | RAID-1 (tükrözés) | 5 | Egy 100 GB-os virtuális gép 300 GB-ot használna fel.
2 | RAID-6 (törléskódolás) | 6 | Egy 100 GB-os virtuális gép 150 GB-ot használna fel.
3 | RAID-1 (tükrözés) | 7 | Egy 100 GB-os virtuális gép 400 GB-ot használna fel.


## <a name="best-practices-for-confidence-ratings"></a>Ajánlott eljárások a megbízhatósági minősítéshez

A teljesítmény-alapú értékelések futtatásakor a rendszer az értékeléshez az 1 csillagos (legalacsonyabb) és az 5 csillag közötti megbízhatósági minősítést adja meg. Megbízhatósági minősítések hatékony használata:
- Azure Migrate kiszolgáló értékeléséhez a virtuális gép PROCESSZORának/memóriájának kihasználtsági adatai szükségesek.
- A helyszíni virtuális géphez csatlakoztatott minden lemezhez szükség van az írási/olvasási IOPS/adatátviteli adatokra.
- A virtuális géphez csatlakoztatott minden hálózati adapter esetében a hálózat be-és kimenő adatelérésére van szükség.

A kiválasztott időtartamhoz elérhető adatpontok százalékos arányának függvényében az értékelés megbízhatósági minősítése az alábbi táblázat szerint Összefoglalva lesz.

   **Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag


## <a name="common-assessment-issues"></a>Gyakori értékelési problémák

Az értékeléseket érintő gyakori környezeti problémák megoldásához.

###  <a name="out-of-sync-assessments"></a>Nem szinkronizált értékelések

Ha gépeket ad hozzá vagy távolít el egy csoportból egy Értékelés létrehozása után, a létrehozott értékelést a rendszer **szinkronként** jelöli meg. Futtassa újra az értékelést (**újraszámítva**), hogy tükrözze a csoport módosításait.

### <a name="outdated-assessments"></a>Elavult értékelések

Ha olyan helyszíni módosításokat végeznek a virtuális gépeken, amelyek egy értékelt csoportban vannak, az értékelés **elavultként** van megjelölve. Az alábbi tulajdonságok egy vagy több módosítása miatt az értékelés "elavultként" jelölhető meg:

- Processzor-magok száma
- Lefoglalt memória
- Rendszerindítási típus vagy belső vezérlőprogram
- Operációs rendszer neve, verziója és architektúrája
- Lemezek száma
- Hálózati adapterek száma
- Lemez méretének változása (GB lefoglalt)
- A hálózati adapter tulajdonságainak frissítése. Például: Mac-cím módosítása, IP-cím hozzáadása stb.

Futtassa újra az értékelést (**újraszámítva**), hogy tükrözze a módosításokat.

### <a name="low-confidence-rating"></a>Alacsony megbízhatósági minősítés

Előfordulhat, hogy az értékelés számos okból nem rendelkezik az összes adatponttal:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például egy hétre beállított teljesítmény *-alapú értékelést* hoz létre, akkor legalább egy hétig várnia kell, miután elindította az összes adatpont felderítését. Az **újraszámítások** lehetőségre kattintva megtekintheti a legújabb alkalmazható megbízhatósági minősítést. A megbízhatósági minősítés csak akkor alkalmazható, ha *teljesítmény-alapú* értékelést hoz létre.

- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha egyes virtuális gépek egy ideig ki lettek kapcsolva, a Server Assessment nem fog tudni teljesítményadatokat gyűjteni az adott időszakra vonatkozóan.

- Kevés virtuális gép lett létrehozva a felderítés indítását követően a Server Assessmentben. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépek teljesítményadatai a teljes időtartamra vonatkozóan nem lesznek elérhetőek, és a megbízhatósági minősítés alacsony lesz.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Áttelepítési eszköz – útmutató az AVS-értékelésekhez

Az Azure VMware Solution- (AVS-) értékelés Azure-kompatibilitási jelentésében a következő ajánlott eszközök szerepelnek: 
- **VMware HCX vagy Enterprise**: VMware-es gépek esetén a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Ismeretlen**: A CSV-fájllal importált gépek esetében az alapértelmezett migrálási eszköz ismeretlen. A VMware-gépek esetében azonban ajánlott a VMware Hybrid Cloud Extension (HCX) megoldás használata.


## <a name="next-steps"></a>Következő lépések

- [További információ](concepts-assessment-calculation.md) az értékelések kiszámításáról.
- [Megtudhatja](how-to-modify-assessment.md) , hogyan szabhatja testre az értékeléseket.
