---
title: Ajánlott eljárások a Azure Migrate felderítési és értékelési eszközben
description: Tippek az értékelések létrehozásához Azure Migrate felderítési és értékelési eszközzel.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 1bf844dafe450e90213db2e447bb5392064eb245
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786770"
---
# <a name="best-practices-for-creating-assessments"></a>Ajánlott eljárások az értékelések létrehozásához

[Azure Migrate](./migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.

Ez a cikk Az értékelések Azure Migrate felderítési és értékelési eszközzel történő létrehozásával kapcsolatos ajánlott eljárásokat foglalja össze.

A Azure Migrate segítségével létrehozott értékelések: a felderítési és értékelési eszköz az adatok időponthoz tartozó pillanatképe. Háromféle értékelést hozhat létre Azure Migrate használatával: felderítés és Értékelés:

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> A helyszíni kiszolgálókat kiértékelheti a [VMware](how-to-set-up-appliance-vmware.md) és a [Hyper-V](how-to-set-up-appliance-hyper-v.md) környezetben, valamint az Azure-ba való áttelepítéshez használható [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) az értékelés típusának használatával. [További információ](concepts-assessment-calculation.md)
**Azure SQL** | A helyszíni SQL Server-kiszolgálók VMware-környezetből Azure SQL Database vagy az Azure SQL felügyelt példányba való átköltöztetésének értékelése. [További információ](concepts-azure-sql-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) az értékelés típusának használatával értékelheti az Azure VMware-megoldásba (AVS) való áttelepítésre. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Méretezési feltételek
Méretezési feltételek beállításai Azure Migrate értékelésekben:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | **Azure-beli virtuális gépek kiértékelése**: A virtuális gép méretére vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.<br/><br/> A lemeztípusra vonatkozó javaslatok (standard HDD/SSD vagy prémium felügyelt lemez) a helyszíni lemezek IOPS értékén és átviteli sebességén alapulnak.<br/><br/>**Azure SQL Assessment**: az Azure SQL-konfiguráció az SQL-példányok és-adatbázisok teljesítményadatait tartalmazza, beleértve a processzor kihasználtságát, a memória kihasználtságát, a IOPS (adatok és naplófájlok), az IO-műveletek átviteli sebességét és késését.<br/><br/>**Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.
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

- **Create as-is assessments (létrehozás másként)**: a (z) rendszer azonnal létrehozhatja az értékeléseket, amint a kiszolgálók megjelennek a Azure Migrate-portálon. Nem hozhat létre "helyszíni" méretezési feltételekkel rendelkező Azure SQL-értékelést.
- **Teljesítmény-alapú Értékelés létrehozása**: Miután beállította a felderítést, javasoljuk, hogy várjon legalább egy nappal a teljesítmény-alapú értékelés futtatása előtt:
    - A teljesítményadatok gyűjtése időt vesz igénybe. Legalább egy nap várakozásával gondoskodhat arról, hogy az értékelés futtatása előtt elegendő teljesítményadatok legyenek.
    - Ha teljesítmény-alapú értékeléseket futtat, győződjön meg arról, hogy a környezete az értékelés időtartamára van feldolgozva. Ha például egy hétre állítja össze az értékelést, akkor a felderítés megkezdése után legalább egy hétig meg kell várnia az összes összegyűjtött adatpontot. Ha nem, az értékelés nem kap öt csillagos minősítést.
- **Értékelések újraszámítása**: mivel az értékelések időponthoz kötődő Pillanatképek, nem frissülnek automatikusan a legújabb adattal. Ha frissíteni szeretne egy értékelést a legújabb adattal, újra kell számítania.

Kövesse az alábbi ajánlott eljárásokat a Azure Migrate-ba importált kiszolgálók értékeléséhez. CSV-fájl:

- **Create as-is assessments (létrehozás másként)**: a (z) rendszer azonnal létrehozhatja az értékeléseket, amint a kiszolgálók megjelennek a Azure Migrate-portálon.
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

- Az Azure-beli virtuális gépek és az AVS-értékelések a következők:
    - Az egyes kiszolgálók CPU-és memória-kihasználtsági adatai
    - A helyszíni kiszolgálóhoz csatolt minden lemez olvasási/írási IOPS/adatátviteli adatok
    - A kiszolgálóhoz csatlakozó összes hálózati adapterhez tartozó hálózati be-és kijelentkezési érték.
     
- Az Azure SQL-értékelésekhez szükség van a kiértékelt SQL-példányok és-adatbázisok teljesítményadatait, többek között a következőkre:
    - A processzor és a memória kihasználtsági adatai
    - Az adatok és naplófájlok olvasási/írási IOPS/adatátviteli adatok
    - Az i/o-műveletek késése

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

Ha egy Értékelés létrehozása után ad hozzá vagy távolít el kiszolgálókat a csoportból, a létrehozott értékelést a rendszer **szinkronként** jelöli meg. Futtassa újra az értékelést (**újraszámítva**), hogy tükrözze a csoport módosításait.

### <a name="outdated-assessments"></a>Elavult értékelések

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Azure VM Assessment és AVS Assessment
Ha olyan helyszíni kiszolgálókon végez módosításokat, amelyek egy értékelt csoportban vannak, az értékelés **elavultként** van megjelölve. Az alábbi tulajdonságok egy vagy több módosítása miatt az értékelés "elavultként" jelölhető meg:
- Processzor-magok száma
- Lefoglalt memória
- Rendszerindítási típus vagy belső vezérlőprogram
- Operációs rendszer neve, verziója és architektúrája
- Lemezek száma
- Hálózati adapterek száma
- Lemez méretének változása (GB lefoglalt)
- A hálózati adapter tulajdonságainak frissítése. Például: Mac-cím módosítása, IP-cím hozzáadása stb.
    
Futtassa újra az értékelést (**újraszámítva**), hogy tükrözze a módosításokat.
    
#### <a name="azure-sql-assessment"></a>Azure SQL-értékelés
Ha olyan helyszíni SQL-példányok és-adatbázisok módosulnak, amelyek egy értékelt csoportban vannak, az értékelés **elavultként** van megjelölve. Az értékelés a következő okok miatt lehet elavultként megjelölve:
- SQL-példányt adtak hozzá egy kiszolgálóhoz vagy távolítottak el róla
- SQL-adatbázist adtak hozzá egy SQL-példányhoz vagy távolítottak el róla
- Egy SQL-példány teljes adatbázismérete több mint 20%-kal változott
- Processzor-magok számának módosítása
- A lefoglalt memória módosítása        
  
    Futtassa újra az értékelést (**újraszámítva**), hogy tükrözze a módosításokat.

### <a name="low-confidence-rating"></a>Alacsony megbízhatósági minősítés

Előfordulhat, hogy az értékelés számos okból nem rendelkezik az összes adatponttal:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például egyhetes teljesítmény-időtartamú értékelést hoz létre, akkor a felderítés indítását követően legalább egy hetet várnia kell az összes adatpont összegyűjtésére. Ha nem tudja megvárni az időtartam végét, módosítsa a teljesítmény időtartamát egy kisebb időszakra, és számítsa újra az értékelést.
 
- Az értékelés nem tud teljesítményadatokat gyűjteni az értékelési időszakban néhány kiszolgálóról vagy egyik kiszolgálóról sem. Magas megbízhatósági minősítés esetén ügyeljen a következőre: 
    - A kiszolgálók az értékelés időtartamára vannak bekapcsolva
    - A 443-es portokon engedélyezett kimenő kapcsolatok engedélyezettek
    - Hyper-V kiszolgálók esetén a dinamikus memória engedélyezve van 
    - A Azure Migrate lévő ügynökök kapcsolati állapota "Connected", és az utolsó szívverést vizsgálja.
    - Az Azure SQL-értékelések esetében Azure Migrate kapcsolati állapota minden SQL-példányhoz "csatlakoztatva" a felderített SQL-példány panelen

    Számítsa újra az értékelést, hogy tükrözze a megbízhatósági minősítés legújabb módosításait.

- Az Azure-beli virtuális gépek és az AVS-értékelések esetében kevés kiszolgáló jött létre a felderítés elindítása után. Ha például az elmúlt egy hónap teljesítmény-előzményeire vonatkozó értékelést hoz létre, néhány kiszolgálót azonban csak egy héttel ezelőtt hoztak létre a környezetben. Ebben az esetben az új kiszolgálókhoz tartozó teljesítményadatok nem lesznek elérhetők a teljes időtartamra, és a megbízhatósági minősítés alacsony lenne.

- Azure SQL-értékelések esetén néhány SQL-példány vagy -adatbázis a felderítés elindítása után jött létre. Ha például az utolsó egy hónap teljesítmény-előzményeire vonatkozó értékelést hoz létre, néhány SQL-példány vagy-adatbázis csak egy héttel ezelőtt jött létre a környezetben. Ebben az esetben az új kiszolgálókhoz tartozó teljesítményadatok nem lesznek elérhetők a teljes időtartamra, és a megbízhatósági minősítés alacsony lenne.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Áttelepítési eszköz – útmutató az AVS-értékelésekhez

Az Azure VMware Solution- (AVS-) értékelés Azure-kompatibilitási jelentésében a következő ajánlott eszközök szerepelnek: 
- **VMware HCX vagy Enterprise**: VMware-kiszolgálókhoz a VMware Hybrid Cloud Extension (HCX) megoldás a javasolt áttelepítési eszköz, amellyel áttelepítheti a helyszíni számítási feladatokat az Azure VMware-megoldás (AVS) privát felhőbe. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Ismeretlen**: a CSV-fájlon keresztül importált kiszolgálók esetében az alapértelmezett áttelepítési eszköz ismeretlen. A VMware-környezetben található kiszolgálók esetében azonban ajánlott a VMware Hybrid Cloud Extension (HCX) megoldás használata.


## <a name="next-steps"></a>Következő lépések

- [További információ](concepts-assessment-calculation.md) az értékelések kiszámításáról.
- [Megtudhatja](how-to-modify-assessment.md) , hogyan szabhatja testre az értékeléseket.
