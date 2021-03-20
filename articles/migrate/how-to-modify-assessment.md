---
title: Azure Migrate Server Assessment értékelésének testreszabása | Microsoft Docs
description: A Azure Migrate Server Assessment használatával létrehozott értékelések testreszabásának ismertetése
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: de90cffe8760a55aafebf079678d139c6f6f99e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751104"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

Ez a cikk a Azure Migrate Server Assessment által létrehozott értékelések testreszabását ismerteti.

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

Az Azure-ba való Migrálás előkészítése során a Azure Migrate Server Assessment Tool eszközzel hozhat létre értékeléseket a helyszíni VMware virtuális gépekhez és a Hyper-V virtuális gépekhez. A kiszolgáló-értékelési eszköz értékeli a helyszíni kiszolgálókat az Azure IaaS Virtual Machines és az Azure VMware Solution (AVS) szolgáltatásba való áttelepítéshez. 

## <a name="about-assessments"></a>Az értékelések ismertetése

A kiszolgáló-értékeléssel létrehozott értékelések az adatok időpontra vonatkozó pillanatképei. A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V virtuális gépeket](how-to-set-up-appliance-hyper-v.md)és a [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) felhasználhatja az Azure-ba való áttelepítéshez ezzel az értékelési típussal. (concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> Az Azure VMware Solutionbe (AVS-be) történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md).[További információ](concepts-azure-vmware-solution-assessment-calculation.md)

Az Azure-beli virtuális gépek értékelése a kiszolgálók értékelése során két méretezési feltétel közül választhat:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | **Azure-beli virtuális gépek kiértékelése**: A virtuális gép méretére vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.<br/><br/> A lemeztípusra vonatkozó javaslatok (standard HDD/SSD vagy prémium felügyelt lemez) a helyszíni lemezek IOPS értékén és átviteli sebességén alapulnak.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.
**Módosítás nélküli helyszíni** | A javaslatok létrehozásához teljesítményadatokat nem használó kiértékelések. | **Azure-beli virtuális gépek kiértékelése**: A virtuálisgép-méretre vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak<br/><br> A lemeztípus-ajánlás azon alapul, hogy mit választ ki a kiértékeléshez a tárolótípus beállításai között.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak.


## <a name="how-is-an-assessment-done"></a>Hogyan történik az értékelés?

Azure Migrate kiszolgáló értékelésében elvégzett értékelés három szakaszból áll. Az értékelés megfelelőségi elemzéssel kezdődik, majd a méretezés és végül a havi költségbecslés. A gépek csak akkor haladnak át egy későbbi fázisban, ha az előzőre kerül. Ha például egy gép meghibásodik az Azure alkalmassági ellenőrzésének, az az Azure-nak nem megfelelőként van megjelölve, és a méretezés és a költségszámítás nem lesz végrehajtva. [Részletek](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Mi az Azure-beli virtuális gépek felmérése?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/> A kiszolgáló értékelése jelenleg a következő célcsoportokat támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Közép-India, Közép-USA, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Kelet-RÉGIÓJA, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, északi középső régió, észak-Európa, az USA déli középső régiója Egyesült Királyság déli régiója , US Gov Texas, US Gov Virginia, az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és Nyugat-RÉGIÓJA.
**Tárolási típus** | Ezzel a tulajdonsággal adhatja meg, hogy milyen típusú lemezeket szeretne áthelyezni az Azure-ban.<br/><br/> Helyszíni méretezés esetén a célként megadott tárolási típust prémium szintű felügyelt lemezként, standard SSD felügyelt lemezként vagy standard HDD által felügyelt lemezként is megadhatja. A teljesítmény-alapú méretezéshez megadhatja a céllemez típusát automatikus, prémium szintű felügyelt lemezként, standard HDD felügyelt lemezként vagy standard SSD által felügyelt lemezként.<br/><br/> Ha a tárolási típust automatikusként adja meg, a lemezre vonatkozó javaslat a lemezek teljesítményi adatai (IOPS és átviteli sebesség) alapján történik. Ha a tárterületet prémium/standard szintűként adja meg, akkor az értékelés a kiválasztott tárolási típuson belül egy lemez SKU-t javasol. Ha az Egypéldányos VM 99,9%-os SLA-t kívánja elérni, érdemes lehet a tárolási típust prémium szintű felügyelt lemezként megadni. Ez biztosítja, hogy az értékelés összes lemeze prémium szintű felügyelt lemezként legyen ajánlott. Azure
**Fenntartott példányok (RI)** | Ennek a tulajdonságnak a segítségével megadhatja, hogy az Azure-ban [foglalt példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/) rendelkeznek-e, majd az értékelésben szereplő KÖLTSÉGBECSLÉS az ri-kedvezményekbe kerül. A fenntartott példányok jelenleg csak az utólagos elszámolású ajánlatokhoz használhatók Azure Migrateban.
**Méretezési feltétel** | Az Azure-hoz megfelelő méretű virtuális gépekhez használandó feltétel. A teljesítmény *-alapú* méretezés vagy a virtuális gépek mérete a *helyszínen* is végezhető, a teljesítmény előzményeinek figyelembevétele nélkül.
**Teljesítményelőzmények** | A gépek teljesítményi adatai értékelésének időtartama. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*.
**Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés *teljesítmény-alapú*.
**Virtuálisgép-sorozatok** |     Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Ha például olyan éles környezettel rendelkezik, amelyet nem szeretne áttelepíteni az Azure-beli sorozatú virtuális gépekre, kizárhatja a-sorozatokat a listából vagy adatsorozatból, és a jobb oldali méretezés csak a kiválasztott adatsorozatban végezhető el.
**Kényelmi faktor** | Azure Migrate kiszolgáló értékelése az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha a virtuális gépek nem fognak nonstop futni az Azure-ban, megadhatja az időtartamot (a havonta megjelenő napok számát és a napi óraszámot), és ennek megfelelően elvégezheti a költségbecslést.<br/> Az alapértelmezett érték havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadhatja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. Az alapértelmezett érték az Igen.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Mi az Azure VMware-megoldás (AVS) értékelése?

A kiszolgáló értékelése során a következő, az AVS-értékelés részét képezi:


| **Tulajdonság** | **Részletek** |
| - | - |
| **Célhely** | Megadja azt az AVS saját Felhőbeli helyet, amelyre az áttelepítést szeretné végezni.<br/><br/> A Server Assessment szolgáltatásban az AVS Assessment jelenleg a következő célcsoportokat támogatja: USA keleti régiója, Nyugat-Európa, USA nyugati régiója. |
| **Tárolási típus** | Megadja az AVS-ben használni kívánt tárolási motort.<br/><br/> Vegye figyelembe, hogy az AVS-értékelések csak a vSAN alapértelmezett tárolási típusként támogatják. |
**Fenntartott példányok (RIs)** | Ez a tulajdonság segít a fenntartott példányok megadásában az AVS-ben. A RIs jelenleg nem támogatott az AVS-csomópontok esetében. |
**Csomópont típusa** | Megadja a helyszíni virtuális gépek leképezéséhez használt [AVS-csomópont típusát](../azure-vmware/concepts-private-clouds-clusters.md) . Vegye figyelembe, hogy az alapértelmezett csomópont típusa AV36. <br/><br/> Azure Migrate a virtuális gépek AVS-re való áttelepítéséhez szükséges csomópontok számát javasolja. |
**TRANZAKCIÓs beállítás, RAID-szint** | Meghatározza a megfelelő meghibásodást az eltűriés és a RAID-kombinációk esetében. A helyszíni virtuálisgép-lemezre vonatkozó követelményekkel együtt a kiválasztott TRANZAKCIÓs beállítás határozza meg az AVS-ben szükséges teljes vSAN-tárolót. |
**Méretezési feltétel** | Az AVS _-hez megfelelő méretű_ virtuális gépekhez használandó feltételek beállítása. A teljesítmény _-alapú_ méretezést vagy _a helyszínen_ is dönthet úgy, hogy figyelembe venné a teljesítménnyel kapcsolatos előzményeket. |
**Teljesítményelőzmények** | Beállítja a gépek teljesítményi adatai kiértékeléséhez szükséges időtartamot. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel _teljesítmény-alapú_. |
**Százalékos kihasználtság** | Meghatározza a jobb méretezéshez beállított teljesítményi minta százalékos értékét. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés teljesítmény-alapú.|
**Kényelmi faktor** | Azure Migrate kiszolgáló értékelése az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. |
**Ajánlat** | Megjeleníti a regisztrált [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) . Az Azure Migrate ez alapján becsüli meg a költségeket.|
**Pénznem** | A fiók számlázási pénznemét jeleníti meg. |
**Kedvezmény (%)** | Felsorolja az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%. |
**Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Bár ez a csomópont-alapú díjszabás miatt nem befolyásolja az Azure VMware-megoldások díjszabását, az ügyfelek az Azure Hybrid Benefits használatával továbbra is alkalmazhatják az AVS-t (Microsoft-alapú) az AVS-ben. Más szoftveres operációs rendszer-gyártóknak meg kell adniuk a saját licencelési feltételeit, például a RHEL. |
**vCPU-előfizetések** | Meghatározza az 1 fizikai mag-hoz tartozó virtuális magok számának arányát az AVS-csomópontban. A számítások alapértelmezett értéke 4 vCPU: 1 fizikai mag az AVS-ben. <br/><br/> Az API-felhasználók egész számként állíthatja be ezt az értéket. Vegye figyelembe, hogy a vCPU előfizetése > 4:1-es verziójában a teljesítmény romlása is megkezdődhet, de a webkiszolgáló típusú számítási feladatokhoz használható. |

## <a name="edit-assessment-properties"></a>Értékelési Tulajdonságok szerkesztése

Értékelés létrehozása után az értékelés tulajdonságainak szerkesztéséhez tegye a következőket:

1. A Azure Migrate projektben kattintson a **kiszolgálók** elemre.
2. **Azure Migrate: kiszolgáló értékelése** területen kattintson az értékelések száma elemre.
3. Az **értékelés** területen kattintson a megfelelő értékelés > **Szerkesztés tulajdonságok** elemre.
5. Szabja testre az értékelési tulajdonságokat a fenti táblázatoknak megfelelően.
6. Az értékelés frissítéséhez kattintson a **Save (Mentés** ) gombra.


Értékelés létrehozásakor is szerkesztheti az értékelési tulajdonságokat.


## <a name="next-steps"></a>Következő lépések

- [További](concepts-assessment-calculation.md) információ az Azure-beli virtuális gépek értékelésének kiszámításáról.
- [További](concepts-azure-vmware-solution-assessment-calculation.md) információ az AVS-értékelések kiszámításáról.
