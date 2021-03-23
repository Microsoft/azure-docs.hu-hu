---
title: Azure Migrate értékelésének testreszabása | Microsoft Docs
description: A Azure Migrate használatával létrehozott értékelések testreszabását ismerteti.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785206"
---
# <a name="customize-an-assessment"></a>Értékelés testreszabása

Ez a cikk bemutatja, hogyan szabhatja testre a Azure Migrate felderítési és értékelési eszköz által létrehozott értékeléseket.

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

A Azure Migrate felderítési és értékelési eszközzel felméréseket hozhat létre a helyszíni VMware virtuális gépekhez és a Hyper-V virtuális gépekhez az Azure-ba való Migrálás előkészítése során. A felderítési és értékelési eszköz értékeli a helyszíni kiszolgálókat az Azure IaaS Virtual Machines és az Azure VMware Solution (AVS) szolgáltatásba való áttelepítéshez. 

## <a name="about-assessments"></a>Az értékelések ismertetése

A felderítési és értékelési eszközzel létrehozott felmérések az adatok időpontra vonatkozó pillanatképei. Kétféle értékelést hozhat létre Azure Migrate használatával: felderítés és Értékelés.

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V virtuális gépeket](how-to-set-up-appliance-hyper-v.md)és a [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) felhasználhatja az Azure-ba való áttelepítéshez ezzel az értékelési típussal. (concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> Az Azure VMware Solutionbe (AVS-be) történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md).[További információ](concepts-azure-vmware-solution-assessment-calculation.md)

Méretezési feltételek beállításai Azure Migrate értékelésekben:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | **Azure-beli virtuális gépek kiértékelése**: A virtuális gép méretére vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.<br/><br/> A lemeztípusra vonatkozó javaslatok (standard HDD/SSD vagy prémium felügyelt lemez) a helyszíni lemezek IOPS értékén és átviteli sebességén alapulnak.<br/><br/>**Azure SQL Assessment**: az Azure SQL-konfiguráció az SQL-példányok és-adatbázisok teljesítményadatait tartalmazza, beleértve a processzor kihasználtságát, a memória kihasználtságát, a IOPS (adatok és naplófájlok), az IO-műveletek átviteli sebességét és késését.<br/><br/>**Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.
**Módosítás nélküli helyszíni** | A javaslatok létrehozásához teljesítményadatokat nem használó kiértékelések. | **Azure-beli virtuális gépek kiértékelése**: A virtuálisgép-méretre vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak<br/><br> A lemeztípus-ajánlás azon alapul, hogy mit választ ki a kiértékeléshez a tárolótípus beállításai között.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak.

## <a name="how-is-an-assessment-done"></a>Hogyan történik az értékelés?

Azure Migrate felderítés és értékelés során elvégzett értékelés három szakaszból áll. Az értékelés megfelelőségi elemzéssel kezdődik, majd a méretezés és végül a havi költségbecslés. A gépek csak akkor haladnak át egy későbbi fázisban, ha az előzőre kerül. Ha például egy gép meghibásodik az Azure alkalmassági ellenőrzésének, az az Azure-nak nem megfelelőként van megjelölve, és a méretezés és a költségszámítás nem lesz végrehajtva. [Részletek](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Mi az Azure-beli virtuális gépek felmérése?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/> Az Azure VM Assessment jelenleg a következő célcsoportokat támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Közép-India, Közép-USA, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Kelet-RÉGIÓJA, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, északi középső régió, Észak-Európa, az USA déli középső régiója Egyesült Királyság déli régiója , US Gov Texas, US Gov Virginia, az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és Nyugat-RÉGIÓJA.
**Tárolási típus** | Ezzel a tulajdonsággal adhatja meg, hogy milyen típusú lemezeket szeretne áthelyezni az Azure-ban.<br/><br/> Helyszíni méretezés esetén a célként megadott tárolási típust prémium szintű felügyelt lemezként, standard SSD felügyelt lemezként vagy standard HDD által felügyelt lemezként is megadhatja. A teljesítmény-alapú méretezéshez megadhatja a céllemez típusát automatikus, prémium szintű felügyelt lemezként, standard HDD felügyelt lemezként vagy standard SSD által felügyelt lemezként.<br/><br/> Ha a tárolási típust automatikusként adja meg, a lemezre vonatkozó javaslat a lemezek teljesítményi adatai (IOPS és átviteli sebesség) alapján történik. Ha a tárterületet prémium/standard szintűként adja meg, akkor az értékelés a kiválasztott tárolási típuson belül egy lemez SKU-t javasol. Ha az Egypéldányos VM 99,9%-os SLA-t kívánja elérni, érdemes lehet a tárolási típust prémium szintű felügyelt lemezként megadni. Ez biztosítja, hogy az értékelés összes lemeze prémium szintű felügyelt lemezként legyen ajánlott. Azure
**Fenntartott példányok (RI)** | Ennek a tulajdonságnak a segítségével megadhatja, hogy az Azure-ban [foglalt példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/) rendelkeznek-e, majd az értékelésben szereplő KÖLTSÉGBECSLÉS az ri-kedvezményekbe kerül. A fenntartott példányok jelenleg csak az utólagos elszámolású ajánlatokhoz használhatók Azure Migrateban.
**Méretezési feltétel** | Az Azure-hoz megfelelő méretű virtuális gépekhez használandó feltétel. A teljesítmény *-alapú* méretezés vagy a virtuális gépek mérete a *helyszínen* is végezhető, a teljesítmény előzményeinek figyelembevétele nélkül.
**Teljesítményelőzmények** | A gépek teljesítményi adatai értékelésének időtartama. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*.
**Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés *teljesítmény-alapú*.
**Virtuálisgép-sorozatok** |     Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Ha például olyan éles környezettel rendelkezik, amelyet nem szeretne áttelepíteni az Azure-beli sorozatú virtuális gépekre, kizárhatja a-sorozatokat a listából vagy adatsorozatból, és a jobb oldali méretezés csak a kiválasztott adatsorozatban végezhető el.
**Kényelmi faktor** | Az Azure VM Assessment az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha a virtuális gépek nem fognak nonstop futni az Azure-ban, megadhatja az időtartamot (a havonta megjelenő napok számát és a napi óraszámot), és ennek megfelelően elvégezheti a költségbecslést.<br/> Az alapértelmezett érték havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadhatja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. Az alapértelmezett érték az Igen.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Mi az Azure VMware-megoldás (AVS) értékelése?

A következőkben szerepel az AVS assessment:


| **Tulajdonság** | **Részletek** |
| - | - |
| **Célhely** | Megadja azt az AVS saját Felhőbeli helyet, amelyre az áttelepítést szeretné végezni.<br/><br/> Az AVS Assessment jelenleg a következő célcsoportokat támogatja: USA keleti régiója, Nyugat-Európa, USA nyugati régiója. |
| **Tárolási típus** | Megadja az AVS-ben használni kívánt tárolási motort.<br/><br/> Vegye figyelembe, hogy az AVS-értékelések csak a vSAN alapértelmezett tárolási típusként támogatják. |
**Fenntartott példányok (RIs)** | Ez a tulajdonság segít a fenntartott példányok megadásában az AVS-ben. A RIs jelenleg nem támogatott az AVS-csomópontok esetében. |
**Csomópont típusa** | Megadja a helyszíni virtuális gépek leképezéséhez használt [AVS-csomópont típusát](../azure-vmware/concepts-private-clouds-clusters.md) . Vegye figyelembe, hogy az alapértelmezett csomópont típusa AV36. <br/><br/> Azure Migrate a virtuális gépek AVS-re való áttelepítéséhez szükséges csomópontok számát javasolja. |
**TRANZAKCIÓs beállítás, RAID-szint** | Meghatározza a megfelelő meghibásodást az eltűriés és a RAID-kombinációk esetében. A helyszíni virtuálisgép-lemezre vonatkozó követelményekkel együtt a kiválasztott TRANZAKCIÓs beállítás határozza meg az AVS-ben szükséges teljes vSAN-tárolót. |
**Méretezési feltétel** | Az AVS _-hez megfelelő méretű_ virtuális gépekhez használandó feltételek beállítása. A teljesítmény _-alapú_ méretezést vagy _a helyszínen_ is dönthet úgy, hogy figyelembe venné a teljesítménnyel kapcsolatos előzményeket. |
**Teljesítményelőzmények** | Beállítja a gépek teljesítményi adatai kiértékeléséhez szükséges időtartamot. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel _teljesítmény-alapú_. |
**Százalékos kihasználtság** | Meghatározza a jobb méretezéshez beállított teljesítményi minta százalékos értékét. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés teljesítmény-alapú.|
**Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. |
**Ajánlat** | Megjeleníti a regisztrált [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) . Az Azure Migrate ez alapján becsüli meg a költségeket.|
**Pénznem** | A fiók számlázási pénznemét jeleníti meg. |
**Kedvezmény (%)** | Felsorolja az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%. |
**Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Bár ez nem befolyásolja az Azure VMware Solutions díjszabását a csomópont-alapú árak miatt, az ügyfelek továbbra is alkalmazhatják a helyszíni OS-licenceket (Microsoft-alapú) az AVS-ben az Azure Hybrid Benefits használatával. Más szoftveres operációs rendszer-gyártóknak meg kell adniuk a saját licencelési feltételeit, például a RHEL. |
**vCPU-előfizetések** | Meghatározza az 1 fizikai mag-hoz tartozó virtuális magok számának arányát az AVS-csomópontban. A számítások alapértelmezett értéke 4 vCPU: 1 fizikai mag az AVS-ben. <br/><br/> Az API-felhasználók egész számként állíthatja be ezt az értéket. Vegye figyelembe, hogy a vCPU előfizetése > 4:1-es verziójában a teljesítmény romlása is megkezdődhet, de a webkiszolgáló típusú számítási feladatokhoz használható. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Milyen tulajdonságokat kell használni az Azure SQL-felmérések létrehozásához és testreszabásához?

Itt látható az Azure SQL Assessment tulajdonságai:

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az az Azure-régió, amelyre az áttelepíteni kívánja. Az Azure SQL-konfiguráció és a Cost-javaslatok a megadott helyen alapulnak.
**Cél központi telepítési típus** | A cél központi telepítési típus, amelynek értékelését futtatni szeretné: <br/><br/> Válassza az **ajánlott** lehetőséget, ha azt szeretné, hogy a Azure Migrate felmérje az SQL-kiszolgálók készültségét az Azure SQL mi és az Azure SQL Database szolgáltatásba való áttelepítéshez, és javasolja a legmegfelelőbb célként megadott üzembe helyezési lehetőséget, a célként megadott szintet, az Azure SQL-konfigurációt és a havi becsléseket.<br/><br/>Válassza az **Azure SQL db** lehetőséget, ha az SQL-kiszolgálókat csak az Azure SQL Database-adatbázisok áttelepítésére szeretné felmérni, és tekintse át a célként megadott szintet, az Azure SQL db konfigurációját és a havi becsléseket.<br/><br/>Válassza az **Azure SQL mi** lehetőséget, ha szeretné felmérni az SQL-kiszolgálókat csak az Azure SQL Database-adatbázisok áttelepítésére, és tekintse át a célként megadott szintet, az Azure SQL mi konfigurációját és a havi becsléseket.
**Fenntartott kapacitás** | Meghatározza a fenntartott kapacitást, hogy az értékelésben szereplő költségbecslés figyelembe vegye azokat.<br/><br/> Ha a fenntartott kapacitás lehetőséget választja, nem adhatja meg a "kedvezmény (%)" beállítást.
**Méretezési feltételek** | Ez a tulajdonság az Azure SQL konfigurációjának jobb méretezésére szolgál. <br/><br/> Alapértelmezés szerint a **teljesítmény-alapú** , ami azt jelenti, hogy az értékelés összegyűjti a SQL Server példányok és adatbázisok teljesítménymutatóit, hogy az optimális méretű Azure SQL felügyelt példányt és/vagy Azure SQL Database réteg/konfiguráció javaslatot javasolja.
**Teljesítményelőzmények** | A teljesítmény előzményei a teljesítményadatok kiértékeléséhez használt időtartamot határozzák meg.
**Százalékos kihasználtság** | A percentilis kihasználtsága meghatározza a megadásában használt teljesítmény mintájának százalékos értékét.
**Kényelmi faktor** | Az értékelés során használt puffer. A szolgáltatás olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítménybeli előzmények és a jövőbeli használat valószínű növekedése.<br/><br/> Például a 20%-os kihasználtságú 10 Magos példány általában egy kétmagos példányt eredményez. A 2,0-es kényelmi tényezővel az eredmény egy négy Magos példány.
**Ajánlat/licencelési program** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/) , amelyben regisztrálva van. Jelenleg csak az utólagos elszámolású és az utólagos elszámolású fejlesztési/tesztelési lehetőség közül választhat. Vegye figyelembe, hogy további kedvezményt is igénybe vehet, ha foglalt kapacitást alkalmaz, és az utólagos elszámolású ajánlaton Azure Hybrid Benefit.
**Szolgáltatási szint** | A legmegfelelőbb szolgáltatási szintű lehetőség a Azure SQL Database és/vagy az Azure SQL felügyelt példányának áttelepítéséhez szükséges üzleti igények kielégítésére:<br/><br/>**Ajánlott** , ha azt szeretné, hogy a Azure Migrate javasolja a kiszolgálók számára legmegfelelőbb szolgáltatási szintet. Ez lehet általános célú vagy üzleti szempontból kritikus. <br/><br/> **Általános célú** Ha olyan Azure SQL-konfigurációt szeretne, amely költségvetés-alapú számítási feladatokhoz készült. [További információ](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Üzletileg kritikus** Ha olyan Azure SQL-konfigurációt szeretne használni, amely kis késleltetésű számítási feladatokhoz készült, nagy rugalmassággal és gyors feladatátvételsel. [További információ](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Pénznem** | A fiók számlázási pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott előfizetés-specifikus kedvezmények. Az alapértelmezett beállítás 0%.
**Azure Hybrid Benefit** | Megadja, hogy már rendelkezik-e SQL Server licenccel. <br/><br/> Ha így tesz, és a SQL Server-előfizetések aktív frissítési garanciája van érvényben, akkor a licencek az Azure-ba való beszerzése után a Azure Hybrid Benefit is alkalmazható.

## <a name="edit-assessment-properties"></a>Értékelési Tulajdonságok szerkesztése

Értékelés létrehozása után az értékelés tulajdonságainak szerkesztéséhez tegye a következőket:

1. A Azure Migrate projektben kattintson a **kiszolgálók** elemre.
2. **Azure Migrate: felderítés és értékelés**, kattintson az értékelések száma elemre.
3. Az **értékelés** területen kattintson a megfelelő értékelés > **Szerkesztés tulajdonságok** elemre.
5. Szabja testre az értékelési tulajdonságokat a fenti táblázatoknak megfelelően.
6. Az értékelés frissítéséhez kattintson a **Save (Mentés** ) gombra.


Értékelés létrehozásakor is szerkesztheti az értékelési tulajdonságokat.


## <a name="next-steps"></a>Következő lépések

- [További](concepts-assessment-calculation.md) információ az Azure-beli virtuális gépek értékelésének kiszámításáról.
- [További](concepts-azure-sql-assessment-calculation.md) információ az Azure SQL-értékelések kiszámításáról.
- [További](concepts-azure-vmware-solution-assessment-calculation.md) információ az AVS-értékelések kiszámításáról.

