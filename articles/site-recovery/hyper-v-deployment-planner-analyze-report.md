---
title: A Hyper-V Deployment Planner jelentés elemzése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan elemezheti a Azure Site Recovery Deployment Planner által generált jelentéseket a Hyper-V virtuális gépek Azure-ba való vész-helyreállításához.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: f230445ecdb046c2b631e89567df71e1d09c3234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999229"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Az Azure Site Recovery Deployment Planner-jelentés elemzése
Ez a cikk az Azure Site Recovery Deployment Planner Hyper-V – Azure forgatókönyvére vonatkozó Excel-jelentés táblázatait ismerteti.

## <a name="on-premises-summary"></a>Helyszíni összefoglalás
A Helyszíni összefoglalás munkalap áttekintést nyújt a vizsgált Hyper-V-környezetről.

![Helyszíni összefoglalás](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Kezdési dátum** és **befejezési** dátum: a jelentéskészítési adatok kezdő és záró dátuma. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődik, a záró dátum pedig az a dátum, amikor a profilkészítés leáll. Ezek az adatok lehetnek a „StartDate” és „EndDate” értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Total number of profiling days** (Profilkészítés napjainak teljes száma): A jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma.

**Number of compatible virtual machines** (Kompatibilis virtuális gépek száma): Az olyan kompatibilis virtuális gépek teljes száma, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, valamint a szükséges tárfiókok és Azure-magok számát.

**Total number of disks across all compatible virtual machines** (Lemezek teljes száma az összes kompatibilis virtuális gépen): Az összes kompatibilis virtuális gépen található összes lemez.

**Average number of disks per compatible virtual machine** (Lemezek átlagos száma kompatibilis virtuális gépenként): Az összes kompatibilis virtuális gép alapján számított átlagos lemezszám.

**Average disk size (GB)** (Átlagos lemezméret (GB)): Az összes kompatibilis virtuális gép alapján számított átlagos lemezméret.

**Kívánt RPO (perc)**: vagy az alapértelmezett helyreállítási időkorlát, vagy a jelentés létrehozásakor a "DesiredRPO" paraméternek átadott érték a szükséges sávszélesség becslése érdekében.

**Desired bandwidth (Mbps)** (Kívánt sávszélesség (Mbps)): A jelentéskészítéskor az elérhető helyreállítási időkorlát (RPO) becslése érdekében a „Bandwidth” paraméter számára megadott érték.

**Observed typical data churn per day (GB)** (Megfigyelt átlagos napi adatváltozás (GB)): Az összes profilkészítési napon megfigyelt átlagos adatváltozás.

## <a name="recommendations"></a>Javaslatok 
A Hyper-V – Azure jelentés javaslati táblázatában a következő részletek találhatók (a kiválasztott RPO szerint):

![Hyper-V – Azure jelentéshez kapcsolódó javaslatok](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profiladatok
![Profiladatok](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Profiled data period** (Profilkészítési adatok létrehozásának időtartama): A profilkészítés futtatásának időtartama. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz. Ha használta a StartDate és az EndDate lehetőséget a jelentés létrehozásakor, a rendszer a meghatározott időszakra vonatkozóan hozza azt létre. 

**Number of Hyper-V servers profiled** (Vizsgált Hyper-V-kiszolgálók száma): Azon Hyper-V-kiszolgálók száma, amelyek virtuális gépeiről jelentés készül. Jelölje ki a számot a Hyper-V-kiszolgálók nevének megjelenítéséhez. Megnyílik a helyszíni tárolóra vonatkozó követelmények táblázata, amelyen minden kiszolgáló fel van sorolva a hozzájuk kapcsolódó tárolási követelményekkel. 

**Desired RPO** (Kívánt RPO): Az üzembe helyezés kívánt helyreállítási időkorlátja. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a DesiredRPOinMin paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a kívánt helyreállítási időkorlát eredményei között.

### <a name="profiling-overview"></a>Profilkészítés áttekintése
![Profilkészítés áttekintése](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Total Profiled Virtual Machines** (Minden virtuális gép, amelyről profil készült): Azon virtuális gépek teljes száma, amelyekről profil készült. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Compatible Virtual Machines** (Kompatibilis virtuális gépek): Azon virtuális gépek száma, amelyek az Azure-ban védhetők az Azure Site Recovery használatával. Az olyan kompatibilis virtuális gépek teljes száma, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, valamint a szükséges tárfiókok és Azure-magok számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban.

**Incompatible Virtual Machines** (Nem kompatibilis virtuális gépek): Azon virtuális gépek száma, amelyekről profil készült, és amelyek nem védhetők Site Recoveryvel. Az inkompatibilitás okait a „Nem kompatibilis virtuális gépek” című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az „Incompatible VMs” (Nem kompatibilis virtuális gépek) szakasz végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Desired RPO**(Kívánt RPO): A kívánt helyreállítási időkorlát, percben megadva. Az eszköz három helyreállítási időkorlát értékről készít jelentést: 15 (alapértelmezett érték), 30 és 60 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található **Desired RPO** (Kívánt RPO) legördülő listából. Ha egyéni értékű -DesiredRPO paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a **Desired RPO** (Kívánt RPO) legördülő listában.

### <a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)
![Szükséges hálózati sávszélesség](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**To meet RPO 100% of the time** (Az RPO betartása az idő 100%-ban): Az ajánlott sávszélesség (Mbps), amelyet ki kell osztania, ha mindig biztosan el kívánja érni a kívánt RPO-t. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**To meet RPO 90% of the time** (Az RPO betartása az idő 90%-ban): Előfordulhat, hogy a szélessávú kapcsolat díjszabása miatt vagy egyéb okból kifolyólag nincs lehetősége arra, hogy akkora sávszélességet állítson be, amellyel a kívánt RPO az idő 100 százalékában betartható. Ilyen esetben választhat egy alacsonyabb sávszélesség-beállítást, amellyel az idő 90 százalékában betarthatja a kívánt RPO-t. Az alacsonyabb sávszélesség-beállítás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Achieved Throughput** (Elért átviteli sebesség): Az átviteli sebesség a GetThroughput parancs futtatásához használt kiszolgáló és a tárfióknak helyet adó Azure-régió között. Ez az átvitelisebesség-érték azt a becsült szintet jelöli, amely akkor érhető el, ha a kompatibilis virtuális gépeket a Site Recoveryvel védi. A Hyper-V-kiszolgáló tárolási és hálózati jellemzőinek meg kell egyezniük azon kiszolgálóéval, amelyen az eszközt futtatja.

A Site Recovery minden vállalati üzemelő példánya esetében az [ExpressRoute](https://aka.ms/expressroute) használata javasolt.

### <a name="required-storage-accounts"></a>Szükséges tárfiókok
A következő diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) tárfiókok teljes számát mutatja. A „VM-storage placement” (Virtuálisgép-tároló elhelyezése) szakaszból megtudhatja, hogy melyik tárfiókot melyik virtuális géphez használhatja.

![Szükséges Azure Storage-fiókok](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Szükséges Azure-magok száma
Ez az eredmény az összes kompatibilis virtuális gép feladatátvétele vagy feladatátvételi tesztje előtt beállítandó magok teljes száma. Ha az előfizetés túl kevés maggal rendelkezik, a Site Recovery nem tudja létrehozni a virtuális gépeket a feladatátvételi teszt vagy a tényleges feladatátvétel alatt.

![Szükséges Azure-magok száma](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>A helyszíni tárolásra vonatkozó további követelmények

A Hyper-V-kiszolgálók sikeres replikációjához és a változásreplikációhoz szükséges teljes szabad tárterület, amelynek megléte esetén a virtuális gépek replikációja semmiképpen nem okozza a termelési alkalmazások nem kívánt leállását. Az egyes kötetkövetelményekkel kapcsolatban [A helyszíni tárolásra vonatkozó követelmények](#on-premises-storage-requirement) című szakaszban talál további információt. 

[A helyszíni tárolásra vonatkozó követelmények](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication) című szakaszban talál további információt arról, hogy a replikációhoz miért van szükség szabad helyre.

![A helyszíni tárolásra vonatkozó követelmények és másolási gyakoriság](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maximális másolási gyakoriság
Az ajánlott maximális másolási gyakoriságot be kell állítani ahhoz, hogy a replikáció elérje a kívánt RPO-t. Az alapértelmezett érték öt perc. A jobb RPO eléréséhez a másolási gyakoriságot 30 másodpercre is beállíthatja.

### <a name="what-if-analysis"></a>Lehetőségelemzés
![Lehetőségelemzés](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Ez az elemzés kiemeli, hogy hányszor szegné meg a helyreállítási időkorlátot a profilkészítés időtartama alatt, ha alacsonyabb sávszélességet állít be, amely az idő 90 százalékában biztosítaná a kívánt RPO betartását. A helyreállítási időkorlát túllépése bármelyik nap egy vagy több alkalommal is előfordulhat. Az ábra a napi RPO-csúcsértéket mutatja. Az elemzés alapján eldöntheti, hogy az RPO-túllépések teljes száma az összes nap alatt, valamint a napi RPO-csúcsérték elfogadható-e a megadott alacsonyabb sávszélesség esetén. Ha elfogadható, akkor lefoglalhatja az alacsonyabb sávszélességet a replikációhoz. Ha nem, akkor foglalja le a javasolt magasabb sávszélességet ahhoz, hogy az idő 100 százalékában betarthassa az RPO-t. 

### <a name="recommendation-for-successful-initial-replication"></a>Javaslatok a sikeres kezdeti replikációhoz
Ebben a szakaszban a virtuális gépeket védő kötegek mennyiségéről, valamint a kezdeti replikáció (IR) sikeres befejezéséhez szükséges minimális sávszélességről olvashat. 

![IR-kötegelés](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

A virtuális gépeket a megadott kötegelési sorrendben kell levédeni. Minden egyes köteg a virtuális gépek külön listáját tartalmazza. Az 1. kötegbe tartozó virtuális gépeket a 2. kötegbe tartozók előtt kell levédeni. A 2. kötegbe tartozó virtuális gépeket a 3. kötegbe tartozók előtt kell levédeni, és így tovább. Ha az 1. kötegbe tartozó virtuális gépek kezdeti replikációja befejeződött, engedélyezheti a 2. kötegbe tartozó virtuális gépekét. Hasonlóképpen, ha a 2. kötegbe tartozó virtuális gépek kezdeti replikációja befejeződött, ugyanígy engedélyezheti a 3. kötegbe tartozó virtuális gépekét, és így tovább. 

Ha nem követi a kötegelési sorrendet, előfordulhat, hogy nem fog rendelkezésre állni elegendő sávszélesség a később levédendő virtuális gépek kezdeti replikációjához. Ennek eredményeképpen a virtuális gépek nem tudják majd befejezni a kezdeti replikációt, vagy előfordulhat, hogy több védett virtuális gép újraszinkronizálási üzemmódba lép. A kiválasztott RPO-táblázatra vonatkozó IR-kötegelés részletes információkat tartalmaz arról, hogy mely virtuális gépeket kell belevenni a kötegekbe.

Az itt látható diagramon a kezdeti és változásreplikáció sávszélesség-elosztása látható kötegekre lebontva, a megadott kötegelési sorrend szerint. Az első kötegbe tartozó virtuális gépek levédésekor a kezdeti replikáció során a teljes sávszélesség rendelkezésre áll. Ha az első köteg kezdeti replikációja befejeződött, a sávszélesség egy részére szükség lesz a változásreplikációhoz. A fennmaradó sávszélesség fog rendelkezésre állni a második köteg virtuális gépeinek kezdeti replikációjához. 

A 2. köteg oszlopában az 1. köteg virtuális gépeinek változásreplikációjához szükséges sávszélesség, valamint a 2. köteg virtuális gépeinek kezdeti replikációjához elérhető sávszélesség látható. Ehhez hasonló módon a 3. köteg oszlopa a korábbi kötegek (1. és 2.) virtuális gépeinek változásreplikációjához szükséges sávszélességet, valamint a 3. köteg virtuális gépeinek kezdeti replikációjához rendelkezésre álló sávszélességet tartalmazza, és így tovább. Ha az összes köteg kezdeti replikációja befejeződött, az utolsó oszlopban az összes virtuális gép változásreplikációjához szükséges sávszélesség látható.

**Miért van szükség a kezdeti replikáció kötegelésére?**
A kezdeti replikáció befejezési ideje a virtuális gépek lemezeinek méretétől, a felhasznált lemezterülettől és az elérhető hálózati átviteli sebességtől függ. A részletek a kiválasztott RPO-táblázat IR-kötegelésében érhetők el.

### <a name="cost-estimation"></a>Költségbecslés
Az ábra az Azure-ba irányuló vészhelyreállítás (DR) becsült teljes költségének összefoglaló nézetét jeleníti meg a jelentéskészítéshez megadott pénznemben és célrégióra vonatkozóan.

![Költségbecslés összefoglalása](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Az összefoglalás segít megismerni a tárolás, számítás, hálózat és licencelés költségeit, amelyet akkor kell fizetnie, ha az Azure-ban lévő összes kompatibilis virtuális gépet a Site Recovery használatával védi. A költségek kiszámítása a kompatibilis virtuális gépek, nem pedig a profilkészítésben részt vevő virtuális gépek alapján történik. 
 
A költségeket havi vagy éves bontásban tekintheti meg. További információkat olvashat a [támogatott célrégiókról](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) és a [támogatott pénznemekről](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Összetevők szerinti költségek**: A vészhelyreállítás teljes költsége négy összetevőből áll: számítás, tárterület, hálózat és a Site Recovery licencköltségei. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a DR működési ideje során jelentkeznek. A számítás a számítási igény, a tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint a Site Recovery-licenc figyelembe vételével történik.

**Állapot szerinti költségek**: A vészhelyreállítás teljes költségeinek kategóriái két különböző állapoton alapulnak: replikáció és DR működése. 

**Replikáció költségei**: A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és a Site Recovery-licenc költségeit. 

**DR működés költségei**: A feladatátvételi tesztek során felmerülő költségek. A Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik. 

**Az Azure Storage tárterület éves/havi költségei**: Ez az oszlopdiagram a tárterület teljes költségeit mutatja, amelyek a replikációhoz és a DR működésekor használt prémium és standard tárterület esetén merülnek fel. A virtuális gépenkénti részletes költségelemzést a [Költségbecslés](hyper-v-deployment-planner-cost-estimation.md) táblázatban tekintheti meg.

### <a name="growth-factor-and-percentile-values-used"></a>A használt növekedési tényező és százalékértékek
A munkalap alján található szakasz a profilkészítésben részt vevő virtuális gépek összes teljesítményszámlálójához használt százalékértéket (az alapértelmezett érték a 95. percentilis), valamint az összes számításban használt növekedési tényezőt (az alapértelmezett érték 30 százalék) mutatja.

![A használt növekedési tényező és százalékértékek](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Javaslatok az elérhető sávszélesség bemenetként való megadásával
![Profilkészítés áttekintése sávszélesség-bevitellel](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Előfordulhat olyan helyzet, hogy legfeljebb x Mbps sávszélességet tud beállítani a Site Recovery replikációjára. Az eszközzel megadhatja a rendelkezésre álló sávszélességet (jelentéskészítés közben a -Bandwidth paraméterrel), és megkapja az elérhető RPO-t percben megadva. Az elérhető RPO-érték ismeretében eldöntheti, hogy szükséges-e további sávszélességet kiépítenie, vagy elfogadja a vészhelyreállítási megoldást ezzel az RPO-val.

![Elérhető RPO](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Javaslat a virtuálisgép-tároló elhelyezésére 
![Virtuálisgép-tároló elhelyezése](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Disk Storage Type** (Lemezes tárolás típusa): A **VMs to Place**(Elhelyezendő virtuális gépek) oszlopban említett összes virtuális gép replikációjához használt standard vagy prémium szintű tárfiók.

**Suggested Prefix** (Javasolt előtag): A tárfiók elnevezéséhez javasolt háromkarakteres előtag. Saját előtagot is használhat, de az eszköz által javasolt nevek követik a [tárfiókok partíció-elnevezési szabályait](/en-in/azure/storage/blobs/storage-performance-checklist).

**Suggested Account Name** (Javasolt fióknév): A Storage-fiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Log Storage Account**(Naplótárolási fiók): Minden replikációs naplót egy standard szintű tárfiók tárol. A prémium szintű tárfiókokba replikált virtuális gépek esetében be kell állítani egy további standard szintű tárfiókot a naplók tárolására. Egyetlen standard szintű naplótárolási fiókot több prémium szintű replikációs tárfiók is használhat. A standard szintű tárfiókokba replikált virtuális gépek ugyanazt a tárfiókot használják a naplókhoz is.

**Suggested Log Account Name** (Javasolt naplótárolási fióknév): A naplókat tároló tárfiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Placement Summary** (Elhelyezés összegzése): A tárfiókon található virtuális gépeknek a replikáció és feladatátvételi teszt vagy tényleges feladatátvétel során mért teljes terhelésének összegzése. Az összegzés a következőket tartalmazza:

* A tárfiókhoz rendelt virtuális gépek teljes számát. 
* A tárfiókban elhelyezni kívánt összes virtuális gép összesített olvasási/írási IOPS-értékét.
* Az összesített írási (replikációs) IOPS-értéket.
* Az összes lemez teljes beállítási méretét.
* A lemezek teljes számát.

**VMs to Place** (Elhelyezendő virtuális gépek): Az összes olyan virtuális gép listája, amelyet az optimális teljesítmény és használat érdekében az adott tárfiókon ajánlott elhelyezni.

## <a name="compatible-vms"></a>Kompatibilis virtuális gépek
A Site Recovery Deployment Planner által létrehozott Excel-jelentésben minden kompatibilis virtuális gépre vonatkozó részletet megtalál a „Compatible VMs” (Kompatibilis virtuális gépek) táblázatban.

![Kompatibilis virtuális gépek](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM Name** (Virtuális gép neve): Jelentés létrehozásakor, a VMListFile-ban használt virtuálisgép-név. Ez az oszlop a virtuális gépekhez csatolt lemezek (VHD-k) listáját is megjeleníti. A nevek tartalmazzák a Hyper-V kiszolgálóneveket, amelyeken az eszköz felfedezte a virtuális gépeket a profilkészítési időszakban.

**VM Compatibility** (Virtuálisgép-kompatibilitás): Az érték **Yes** (Igen) és **Yes**\* (Igen) lehet. **Igen** \* olyan példányok esetén, amelyekben a virtuális gép elfér a [prémium SSD](../virtual-machines/disks-types.md)-k számára. A profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a lemezhez leképezett méretet meghaladó prémium lemezméretnek. A tárfiók a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt: 
* 128 GB alatt P10.
* 128 GB és 256 GB között P15.
* 256 GB és 512 GB között P20.
* 512 GB és 1024 GB között P30.
* 1025 GB és 2048 GB között P40.
* 2049 GB és 4095 GB között P50.

Ha például egy lemez számítási feladatait a P20 vagy a P30 kategóriába helyezi, de a méret leképezi egy alacsonyabb Premium Storage-lemez típusra, akkor az eszköz **Igen értéket** jelöl a virtuális gép számára \* . Az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen az ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.

**Storage Type** (Tároló típusa): Standard vagy Premium.

**Suggested Prefix** (Javasolt előtag): A tárfiók három karakterből álló előtagja.

**Storage Account** (Tárfiók): A javasolt tárfiókelőtagot használó név.

**Peak R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS-csúcsérték (növekedési tényezővel)): A lemez írási/olvasási IOPS-csúcsértéke (alapértelmezés szerint a 95. percentilis) a későbbi növekedési tényezővel (az alapértelmezett érték: 30%). Egy virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. A virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn in MB/s (with Growth Factor)** (Maximális adatváltozás (MB/s) (növekedési tényezővel)): A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. percentilis) a későbbi növekedési tényezővel (alapértelmezés szerint: 30%). A virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes virtuálisgép-lemezek adatváltozásának összegével. A virtuális gép maximális adatváltozása az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozások legmagasabb összege.

**Azure VM Size** (Azure-beli virtuális gép mérete): Az Azure Cloud Services-beli hozzárendelt virtuális gép ideális mérete az adott helyszíni virtuális gép esetén. A hozzárendelés a helyszíni virtuális gép memóriáján, a lemezek/magok/hálózati adapterek számán és az olvasási/írási IOPS-értéken alapul. Mindig a legalacsonyabb, a helyszíni virtuális gép összes jellemzőjének megfelelő Azure-beli virtuálisgép-méretet ajánlott használni.

**Number of Disks** (Lemezek száma): A virtuális gép lemezeinek (VHD-inek) teljes száma.

**Disk size (GB)** (Lemez mérete (GB)): A virtuális gép összes lemezének teljes mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok): A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)): A virtuális gép fizikai memóriája (RAM).

**NICs** (Hálózati adapterek): A virtuális gép hálózati adaptereinek száma.

**Boot Type** (Rendszerindítás típusa): A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.

## <a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek
A Site Recovery Deployment Planner által létrehozott Excel-jelentésben az összes, nem kompatibilis virtuális gépre vonatkozó részletet megtalálja az „Incompatible VMs” (Nem kompatibilis virtuális gépek) táblázatban.

![Nem kompatibilis virtuális gépek](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM Name** (Virtuális gép neve): Jelentés létrehozásakor, a VMListFile-ban használt virtuálisgép-név. Ez az oszlop a virtuális gépekhez csatolt lemezek (VHD-k) listáját is megjeleníti. A nevek tartalmazzák a Hyper-V kiszolgálóneveket, amelyeken az eszköz felfedezte a virtuális gépeket a profilkészítési időszakban.

**VM Compatibility** (Virtuális gép kompatibilitása): Azt jelzi, hogy miért nem kompatibilis az adott virtuális gép a Site Recoveryvel való használattal. Az indokok a virtuális gép minden nem kompatibilis lemezénél vannak megadva, és a közzétett [tárhelykorlátok](/en-in/azure/storage/common/scalability-targets-standard-account) alapján a következők lehetnek:

* A lemez mérete nagyobb 4095 GB-nál. Az Azure Storage jelenleg nem támogatja a 4095 GB-nál nagyobb adatlemez-méretet.

* Az 1. generációs virtuális gép (BIOS rendszerindítás-típus) operációsrendszer-lemezének mérete nagyobb 2047 GB-nál. A Site Recovery jelenleg nem támogatja a 2047 GB-nál nagyobb méretű operációsrendszer-lemezeket 1. generációs virtuális gépek esetében.

* A 2. generációs virtuális gép (EFI rendszerindítás-típus) operációsrendszer-lemezének mérete nagyobb 300 GB-nál. A Site Recovery jelenleg nem támogatja a 300 GB-nál nagyobb méretű operációsrendszer-lemezeket 2. generációs virtuális gépek esetében.

* A virtuális gép neve nem tartalmazhatja a következő, nem támogatott karaktereket: “” [] `. Az eszköz nem tudja lekérni azon virtuális gépek profilkészítési adatait, amelyek tartalmazzák nevükben a fenti karaktereket. 

* A VHD-n kettő vagy több virtuális gép osztozik. Az Azure nem támogatja a közös VHD-vel rendelkező virtuális gépeket.

* A Virtuális Fiber Channel vezérlőt használó virtuális gépek nem támogatottak. A Site Recovery nem támogatja a Virtuális Fiber Channel vezérlőt használó virtuális gépeket.

* Egy Hyper-V-fürt nem tartalmaz replikációs közvetítőt. A Site Recovery nem támogatja egy Hyper-V-fürt virtuális gépét, ha a Hyper-V-replikaközvetítő nem lett konfigurálva a fürt számára.

* A virtuális gép nem magas rendelkezésre állású. A Site Recovery nem támogatja olyan Hyper-V-fürtcsomópont virtuális gépét, amelynek VHD lemezeit a helyi lemez tárolja, nem a fürtlemez. 

* A virtuális gépek teljes mérete (replikáció + feladatátvételi teszt) meghaladja a támogatott prémium tárfiók méretkorlátozását (35 TB). Ez a fajta inkompatibilitás általában akkor fordul elő, ha a virtuális gép egy lemezének egyik teljesítményjellemzője meghaladja a támogatott standard szintű tárolóra vonatkozó Azure- vagy Site Recovery-korlátozásokat. Egy ilyen példány a prémium szintű tárolózónába kényszeríti a virtuális gépet. A prémium szintű tárfiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. 

    Amikor a feladatátvételi teszt védett virtuális gépen megy végbe, és ha egy felügyelet nélküli lemez konfigurálva lett feladatátviteli tesztre, akkor a teszt ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ebben az esetben további, ugyanakkora mennyiségű tárterületre van szükség, mint a replikáció esetében. Ez biztosítja a replikáció lefolyását, valamint ezzel párhuzamosan a feladatátvételi teszt sikerét. Ha egy felügyelt lemez konfigurálva lett feladatátviteli tesztre, nincs szükség további terület kijelölésére azon virtuális gép esetén, amelyen a feladatátviteli teszt zajlik.

* A forrás IOPS-érték meghaladja a tároló lemezenkénti 7500-as IOPS-korlátját.

* A forrás IOPS-érték meghaladja a tároló virtuális gépenkénti 80 000-es IOPS-korlátját.

* A forrás virtuális gép átlagos adatváltozása meghaladja a támogatott Site Recovery adatváltozási korlátot 20 MB/s értéknél az átlagos I/O-mérethez.

* A forrás virtuális gép átlagos tényleges írási IOPS-értéke meghaladja a Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját.

* A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet.

**Peak R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS-csúcsérték (növekedési tényezővel)): A lemez IOPS-csúcsértéke (alapértelmezés szerint a 95. percentilis) a későbbi növekedési tényezővel (az alapértelmezett érték: 30%). A virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. A virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn (MB/s) (with Growth Factor)** (Maximális adatváltozás (MB/s) (növekedési tényezővel)): A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. percentilis) a későbbi növekedési tényezővel (alapértelmezés szerint: 30%). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes virtuálisgép-lemezek adatváltozásának összegével. A virtuális gép maximális adatváltozása az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozások legmagasabb összege.

**Number of Disks** (Lemezek száma): A virtuális gép VHD-inek teljes száma.

**Lemez mérete (GB)**: a virtuális gép összes lemezének teljes telepítési mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok): A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)): A virtuális gép fizikai memóriájának (RAM) mennyisége.

**NICs** (Hálózati adapterek): A virtuális gép hálózati adaptereinek száma.

**Boot Type** (Rendszerindítás típusa): A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.

## <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat a Site Recovery korlátait tartalmazza. Ezek a korlátok teszteken alapulnak, azonban nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. A legjobb eredmények érdekében, még az üzembe helyezés megtervezése után is alaposan tesztelje az alkalmazást feladatátvételi tesztek használatával, így valós képet kaphat az alkalmazás teljesítményéről.
 
**Replikáció tárolási célja** | **Forrás virtuális gép átlagos I/O-mérete** |**Forrás virtuális gép átlagos adatváltozása** | **Forrás virtuális gép teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | Virtuális gépenként 2 MB/s | Virtuális gépenként 168 GB
Prémium szintű Storage | 8 KB  | Virtuális gépenként 5 MB/s | Virtuális gépenként 421 GB
Prémium szintű Storage | 16 KB vagy több| 20 MB/s virtuális gépenként | 1684 GB/virtuális gépenként

Ezek átlagos határértékek, amelyek 30 százalékos I/O-átfedést feltételeznek. A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján. Az előbbi számok egy általános, körülbelül ötperces várólistát feltételeznek. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

## <a name="on-premises-storage-requirement"></a>A helyszíni tárolásra vonatkozó követelmények

A munkalapon szerepelnek a Hyper-V kiszolgálók (amelyeken a VHD-k megtalálhatók) köteteinek a sikeres kezdeti és változásreplikációhoz szükséges teljes szabad tárterületre vonatkozó követelményei. A replikáció engedélyezése előtt adja hozzá a szükséges tárterületet a kötetekhez, így biztosíthatja, hogy a replikáció során nem következik be nem kívánt leállás a termelési alkalmazásokban. 

  A Site Recovery Deployment Planner azonosítja az optimális tárterület-követelményeket a VHD mérete és a replikációhoz felhasznált hálózati sávszélesség alapján.

![A helyszíni tárolásra vonatkozó követelmények](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Miért van szükség szabad területre a Hyper-V kiszolgálón a replikációhoz?
* Amikor engedélyezi egy virtuális gép replikációját, a Site Recovery pillanatképet készít a virtuális gép egyes VHD-iről a kezdeti replikációhoz. A kezdeti replikáció során az alkalmazás új módosításokat ír a lemezekre. A Site Recovery a naplófájlokban követi nyomon ezeket a változásokat, ehhez pedig további tárterületre van szükség. A kezdeti replikáció befejezéséig a naplófájlokat a rendszer helyben tárolja. 

    Ha nincs elegendő hely a naplófájlok és a pillanatkép (AVHDX) számára, a replikáció átlép újraszinkronizálási üzemmódba, és soha nem fejeződik be. A legrosszabb esetben a VHD-méret 100%-ának megfelelő további szabad tárterületre van szükség a kezdeti replikációhoz.
* Ha a kezdeti replikáció befejeződik, elkezdődik a változásreplikáció. A Site Recovery a naplófájlokban követi nyomon ezeket a változásokat. Ezek ugyanazon a köteten találhatók, amelyen a virtuális gép VHD-i vannak. A naplófájlok replikálása az Azure-ba a konfigurált másolási gyakoriságnak megfelelően történik. Az elérhető hálózati sávszélességtől függően a naplófájlok Azure-ba történő replikációja némi időt vesz igénybe. 

    Ha nincs elegendő szabad terület a naplófájlok tárolásához, a replikáció szünetel, a virtuális gép replikációja pedig „újraszinkronizálás szükséges” állapotúra vált.
* Ha a hálózati sávszélesség nem elég a naplófájlok Azure-ba történő áthelyezéséhez, akkor azok a köteten halmozódnak fel. A legrosszabb esetben – amikor a naplófájlok mérete eléri a VHD méretének 50%-át – a virtuális gép replikációja „újraszinkronizálás szükséges” állapotúra változik. A legrosszabb esetben a VHD mérete 50%-ának megfelelő további szabad tárterületre van szükség a változásreplikációhoz.

**Hyper-V host** (Hyper-V gazdagép): A vizsgált Hyper-V kiszolgálók listája. Ha egy kiszolgáló egy Hyper-V fürt részét képezi, az összes fürtcsomópont csoportosítva lesz.

**Volume (VHD path)** (Kötet (VHD-útvonal)): A Hyper-V gazdagép minden olyan kötete, ahol VHD-k vagy VHDX-ek találhatók. 

**Free space available (GB)** (Elérhető szabad tárterület (GB)): A köteten lévő szabad tárterület.

**Total storage space required on the volume (GB)** (Teljes szükséges tárterület a köteten (GB)): A sikeres kezdeti és változásreplikációhoz szükséges, teljes szabad tárterület a köteten. 

**Total additional storage to be provisioned on the volume for successful replication (GB)** (A sikeres replikációhoz szükséges teljes kiépítendő tárterület a köteten (GB)): Javaslatot tesz a teljes további tárterületre, amelyet ki kell építeni a köteten a sikeres kezdeti és változásreplikációhoz.

## <a name="initial-replication-batching"></a>Kezdeti replikáció kötegelése 

### <a name="why-do-i-need-initial-replication-batching"></a>Miért van szükség a kezdeti replikáció kötegelésére?
Ha összes virtuális gép védelme egyszerre zajlik, a szabad tárterületre vonatkozó követelmény sokkal magasabb. Ha nem áll rendelkezésre elég tárterület, a virtuális gépek replikációja újraszinkronizálási üzemmódra vált. Emellett az összes virtuális gép együttes kezdeti replikációjának befejezéséhez szükséges hálózati sávszélesség is sokkal magasabb. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Kezdeti replikáció kötegelése a kiválasztott RPO esetében
Ezen a munkalapon az egyes kötegek részletes nézete található a kezdeti replikációhoz. A rendszer minden egyes RPO esetében külön IR-kötegelési táblázatot hoz létre. 

Ha minden kötet esetében megfelelt a javasolt helyszíni tárterület-követelményeknek, a replikálandó fő információ a párhuzamosan védhető virtuális gépek listája. Ezek a virtuális gépek egy kötegbe vannak csoportosítva, és több köteg is lehet. A virtuális gépeket a megadott kötegelési sorrendben kell levédeni. Először a 1. kötegbe tartozó virtuális gépeket védje le, majd ha befejeződött a kezdeti replikáció, védje le a 2. kötegbe tartozókat, és így tovább. A kötegek és a hozzájuk tartozó virtuális gépek listáját ebben a táblázatban találja. 

![Az IR-kötegelés részletei](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Az IR-kötegelés további részletei](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Minden kötegben az alábbi információk találhatók meg 
**Hyper-V host** (Hyper-V gazdagép): A levédendő virtuális gép Hyper-V gazdagépe.

**Virtuális gép**: a védeni kívánt virtuális gép. 

**Comments** (Megjegyzések): Ha egy virtuális gép egy meghatározott kötete esetében bármilyen műveletre van szükség, itt látható a vonatkozó megjegyzés. Ha például nem érhető el elegendő szabad terület egy köteten, akkor megjelenik az „Add additional storage to protect this VM” (Adjon hozzá további tárterületet a virtuális gép levédéséhez) megjegyzés.

**Volume (VHD path)** (Kötet (VHD útvonala)): Azon kötet neve, amelyen a virtuális gép VHD-i találhatók. 

**Szabad terület érhető el a köteten (GB)**: a virtuális gép kötetén elérhető szabad lemezterület. A köteteken lévő elérhető szabad terület kiszámításakor a rendszer figyelembe veszi az előző kötegek virtuális gépei – amelyeknek a VHD-i egyazon köteten találhatók – változásreplikációja által felhasznált lemezterületet is. 

Például tegyük fel, hogy a VM1, VM2 és VM3 virtuális gép egy köteten található (E:\VHDpath). A replikáció előtt a köteten 500 GB szabad terület van. A VM1 gép az 1. köteg része, a VM2 a 2. kötegé, a VM3 pedig a 3. kötegé. A VM1 által 500 GB szabad terület érhető el. A VM2 esetében a szabad terület 500 GB – ennyire van szükség a VM1 változásreplikációjához. Ha a VM1-nek 300 GB területre van szüksége a változásreplikációhoz, akkor a VM2 számára 500 GB - 300 GB = 200 GB szabad terület lesz elérhető. A VM2-nek hasonló módon 300 GB területre van szüksége a változásreplikációhoz. A VM3 számára 200 GB - 300 GB = -100 GB szabad terület lesz elérhető.

**Storage required on the volume for initial replication (GB)** (A kezdeti replikációhoz szükséges tárterület a köteten (GB)): A virtuális gép kezdeti replikációjához szükséges szabad tárterület a köteten.

**A köteten a különbözeti replikációhoz szükséges tárterület (GB)**: a virtuális gép kötetén szükséges szabad tárterület a különbözeti replikációhoz.

**Additional storage required based on deficit to avoid replication failure (GB)** A deficiten alapuló, további szükséges tárterület a köteten, amellyel elkerülhető a replikáció meghiúsulása (GB): A kötetre vonatkozó, a virtuális gép számára szükséges további tárterület-követelmény. Ez a maximális kezdeti replikációra és változásreplikációra vonatkozó tárterületi követelmények maximuma, levonva belőle a köteten elérhető szabad területet.

**Minimum bandwidth required for initial replication (Mbps)** (A kezdeti replikációhoz szükséges minimális sávszélesség (Mbps)): A virtuális gép kezdeti replikációjához szükséges minimális sávszélesség.

**Minimum bandwidth required for delta replication (Mbps)** (A változásreplikációhoz szükséges minimális sávszélesség (Mbps)): A virtuális gép változásreplikációjához szükséges minimális sávszélesség.

### <a name="network-utilization-details-for-each-batch"></a>Az egyes kötegek hálózatfelhasználásának részletei 
Minden kötegtáblázathoz tartozik egy összefoglalás az adott köteg hálózatfelhasználásáról.

**Bandwidth available for Batch** (A köteg számára elérhető sávszélesség): A köteg számára elérhető sávszélesség az előző köteg változásreplikációjához szükséges sávszélesség figyelembe vételével.

**Approximate bandwidth available for initial replication of batch** (Hozzávetőleges elérhető sávszélesség a köteg kezdeti replikációjához): A köteg virtuális gépeinek kezdeti replikációja számára elérhető sávszélesség. 

**Approximate bandwidth consumed for delta replication of batch** (A köteg változásreplikációja által felhasznált hozzávetőleges sávszélesség): A köteg virtuális gépeinek változásreplikációjához szükséges sávszélesség. 

A **Batch (óó: PP) becsült kezdeti replikálási ideje**: a kezdeti replikálási idő (óra): perc.



## <a name="next-steps"></a>Következő lépések
További információk a [költségbecslésről](hyper-v-deployment-planner-cost-estimation.md).