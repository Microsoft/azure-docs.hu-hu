---
title: A VMware vész-helyreállítási Deployment Planner jelentés elemzése Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan elemezheti a helyreállítási Deployment Planner által generált jelentést az Azure-ba történő VMware vész-helyreállításhoz Azure Site Recovery használatával.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: ramamill
ms.openlocfilehash: 7e2db720bb37a25b8511bd1c42c0c18e139aa216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93186603"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>A VMware vész-helyreállítási Deployment Planner jelentés elemzése az Azure-ba

Az elkészített Microsoft Excel-jelentés a következő táblázatokat tartalmazza:
## <a name="on-premises-summary"></a>Helyszíni összefoglalás
A Helyszíni összefoglalás munkalap áttekintést nyújt arról a VMware-környezetről, amelyről profilt készített.

![A VMware-környezet helyszíni összefoglalása](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Start Date** és **End Date** (Kezdő dátum és záró dátum): Azon profilkészítési adatok kezdő és záró dátuma, amelyekről jelentést kíván készíteni. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődik, a záró dátum pedig az a dátum, amikor a profilkészítés leáll. Ezek lehetnek a StartDate és EndDate értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Total number of profiling days** (Profilkészítés napjainak teljes száma): A jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma.

**Number of compatible virtual machines** (Kompatibilis virtuális gépek száma): Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, valamint a szükséges tárfiókok, Microsoft Azure-magok, illetve konfigurációs és további folyamatkiszolgálók számát.

**Total number of disks across all compatible virtual machines**(Lemezek teljes száma az összes kompatibilis virtuális gépen): Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.

**Average number of disks per compatible virtual machine** (Lemezek átlagos száma kompatibilis virtuális gépenként): Az összes kompatibilis virtuális gép alapján számított átlagos lemezszám.

**Average disk size (GB)** (Átlagos lemezméret (GB)): Az összes kompatibilis virtuális gép alapján számított átlagos lemezméret.

**Desired RPO (minutes)** (Kívánt RPO (perc)): A helyreállításipont-célkitűzés alapértelmezett értéke vagy a jelentéskészítéskor a „DesiredRPO” paraméterhez szükséges sávszélesség becslése érdekében megadott érték.

**Desired bandwidth (Mbps)** (Kívánt sávszélesség (Mbps)): A jelentéskészítéskor az elérhető RPO becslése érdekében a „Bandwidth” paraméterhez megadott érték.

**Observed typical data churn per day (GB)** (Megfigyelt átlagos napi adatváltozás (GB)): Az összes profilkészítési napon megfigyelt átlagos adatváltozás. Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.

## <a name="recommendations"></a>Javaslatok

A VMware – Azure jelentés javaslati táblázatában a következő részletek találhatók (a kiválasztott RPO szerint):

![VMware – Azure jelentéshez kapcsolódó javaslatok](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profilkészítés során létrehozott adatok
![A profilkészítés során létrehozott adatok nézete a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Profiled data period** (Profilkészítési adatok létrehozásának időtartama): A profilkészítés futtatásának időtartama. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz, kivéve, ha egy adott időszakról készít jelentést, és megadja a StartDate és EndDate beállításokat a jelentéskészítés során.

**Server Name** (Kiszolgáló neve): Azon VMware vCenter vagy ESXi-gazdagép neve vagy IP-címe, amelynek a virtuális gépeiről a jelentés készül.

**Desired RPO** (Kívánt RPO): Az üzembe helyezés kívánt helyreállítási időkorlátja. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a *DesiredRPOinMin* paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a Kívánt helyreállítási időkorlát eredményei között.

### <a name="profiling-overview"></a>Profilkészítés áttekintése

![A profilkészítés eredményei a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Total Profiled Virtual Machines** (Minden virtuális gép, amelyről profil készült): Azon virtuális gépek teljes száma, amelyekről profil készült. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Compatible Virtual Machines** (Kompatibilis virtuális gépek): Azon virtuális gépek száma, amelyek az Azure-ban védhetők a Site Recovery használatával. Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta a szükséges hálózati sávszélességet, a tárfiókok, az Azure-magok, valamint a konfigurációs és további folyamatkiszolgálók számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban.

**Incompatible Virtual Machines** (Nem kompatibilis virtuális gépek): Azon virtuális gépek száma, amelyekről profil készült, és amelyek nem védhetők Site Recoveryvel. Az inkompatibilitás okait a „Nem kompatibilis virtuális gépek” című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az „Incompatible VMs” (Nem kompatibilis virtuális gépek) szakasz végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Desired RPO**(Kívánt RPO): A kívánt helyreállítási időkorlát, percben megadva. Az eszköz három helyreállítási időkorlát értékről készít jelentést: 15 (alapértelmezett érték), 30 és 60 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található Desired RPO (Kívánt RPO) legördülő listából. Ha egyéni értékű *-DesiredRPO* paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a Kívánt helyreállítási időkorlát legördülő listában.

### <a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)

![Szükséges hálózati sávszélesség a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**To meet RPO 100 percent of the time:** (Az RPO betartása az idő 100 százalékában): Az ajánlott sávszélesség (Mbps), amelyet ki kell osztania, ha mindig garantáltan el kívánja érni a kívánt helyreállítási időkorlátot. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**To meet RPO 90 percent of the time** (Az RPO betartása az idő 90 százalékában): Ha a szélessávú kapcsolat díjszabása miatt vagy egyéb okból kifolyólag nincs lehetősége arra, hogy akkora sávszélességet állítson be, amellyel a kívánt helyreállítási időkorlát mindig betartható, akkor választhat egy alacsonyabb sávszélesség-beállítást, amellyel az idő 90 százalékában betarthatja a kívánt RPO-t. Az alacsonyabb sávszélesség-beállítás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Achieved Throughput:** (Elért átviteli sebesség): Az átviteli sebesség a GetThroughput parancs futtatásához használt kiszolgáló és a tárfióknak helyet adó Microsoft Azure-régió között. Ez az átvitelisebesség-érték azt a becsült szintet jelöli, amely akkor érhető el, ha a kompatibilis virtuális gépeket a Site Recoveryvel védi, feltéve, hogy a konfigurációs kiszolgáló vagy folyamatkiszolgáló tárolója és hálózati jellemzői megegyeznek azon kiszolgálóéval, amelyen az eszközt futtatta.

A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet kell beállítania. Ha a sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:

1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná a Site Recovery átviteli sebességét.

2. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében a Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e.

3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).

4. Módosítsa a Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Ha az eszközt olyan konfigurációs kiszolgálón vagy folyamatkiszolgálón futtatja, amely már rendelkezik védett virtuális gépekkel, futtassa többször azt eszközt. Az elért átviteli sebesség eltérő lesz az aktuális adatforgalomtól függően.

A Site Recovery minden vállalati üzemelő példánya esetében az [ExpressRoute](https://aka.ms/expressroute) használata javasolt.

### <a name="required-storage-accounts"></a>Szükséges tárfiókok
A következő diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) tárfiókok teljes számát mutatja. A „VM-storage placement” (Virtuálisgép-tároló elhelyezése) szakaszból megtudhatja, hogy melyik tárfiókot melyik virtuális géphez használhatja. Ha a Deployment Planner v 2.5-ös verziójával rendelkezik, ez a javaslat csak a replikáláshoz szükséges standard gyorsítótárbeli Storage-fiókok számát jeleníti meg, mivel az adatai közvetlenül Managed Disksba kerülnek.

![Szükséges tárfiókok a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Szükséges Azure-magok száma
Ez az eredmény az összes kompatibilis virtuális gép feladatátvétele vagy feladatátvételi tesztje előtt beállítandó magok teljes száma. Ha az előfizetés túl kevés maggal rendelkezik, a Site Recovery nem tudja létrehozni a virtuális gépeket a feladatátvételi teszt vagy a tényleges feladatátvétel alatt.

![Szükséges Azure-magok száma a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Szükséges helyszíni infrastruktúra
Ez az adat az összes virtuális gép védelméhez szükséges konfigurálandó konfigurációs kiszolgálók és további folyamatkiszolgálók teljes száma. A [konfigurációs kiszolgáló támogatott méretére vonatkozó javaslatoktól](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server) függően az eszköz további kiszolgálókat javasolhat. A javaslat a napi adatforgalmon vagy a védett virtuális gépek maximális számán alapul (virtuális gépenként átlagosan három lemezt feltételezve), ha eléri valamelyik korlátot a konfigurációs kiszolgálón vagy a további folyamatkiszolgálón. A napi összes adatváltozással és a védett lemezek teljes számával kapcsolatos adatok a „Helyszíni összefoglalás” szakaszban találhatók.

![Szükséges helyszíni infrastruktúra a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Lehetőségelemzés
Ez az elemzés kiemeli, hogy hányszor szegné meg a helyreállítási időkorlátot a profilkészítés időtartama alatt, ha alacsonyabb sávszélességet állít be, amely az idő 90 százalékában biztosítaná a kívánt RPO betartását. A helyreállítási időkorlát túllépése bármelyik nap egy vagy több alkalommal is előfordulhat. Az ábra a napi RPO-csúcsértéket mutatja.
Az elemzés alapján eldöntheti, hogy az RPO-túllépések teljes száma az összes nap alatt, valamint a napi RPO-csúcsérték elfogadható-e a megadott alacsonyabb sávszélesség esetén. Ha elfogadható, akkor lefoglalhatja az alacsonyabb sávszélességet a replikációhoz. Ha nem, akkor foglalja le a javasolt magasabb sávszélességet ahhoz, hogy az idő 100 százalékában betarthassa az RPO-t.

![Lehetőségelemzés a Deployment Planner eszközben](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>A virtuálisgép-köteg ajánlott mérete a kezdeti replikációhoz
A szakaszban arra teszünk javaslatot, hogy hány virtuális gép védhető meg egyszerre úgy, hogy a kezdeti replikáció 72 órán belül befejeződjön a javasolt sávszélesség és a kívánt RPO 100 százalékos betartása mellett a beállítás során. Ezen érték konfigurálható. Módosításához használja a *GoalToCompleteIR* paramétert a jelentéskészítés során.

A diagram egy sávszélesség-tartományt és azt a virtuálisgép-köteg méretet mutatja, amellyel a kezdeti replikáció 72 órán belüli befejezhető. Ez a szám az összes kompatibilis virtuális gép és a virtuális gépek átlagos észlelt méretén alapul.

A nyilvános előzetes verzióban a jelentés nem határozza meg, hogy melyik virtuális gépeket foglalja bele egy kötegbe. A „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban látható lemezméretek alapján kikeresheti az egyes virtuális gépek méretét, és kiválaszthatja az egyes kötegekhez tartozó virtuális gépeket, vagy választhat a számítási feladatok ismert jellemzői alapján is. A kezdeti replikáció befejezési ideje a virtuális gépek lemezeinek tényleges méretével, a felhasznált lemezterülettel és az elérhető hálózati átviteli sebességgel arányosan változik.

![A virtuálisgép-köteg ajánlott mérete](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Költségbecslés
Az ábra a választott célrégió és a jelentéskészítéshez megadott pénznem vészhelyreállításának (DR) becsült, teljes költségeinek összesített nézetét mutatja be az Azure-hoz.

![Költségbecslés összefoglalása](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Az összefoglalás segít megismerni a tárolás, számítás, hálózat és licenc költségeit, amelyet akkor kell fizetnie, ha az Azure-ban lévő összes kompatibilis virtuális gépet az Azure Site Recovery használatával védi. A költségek kiszámítása a kompatibilis virtuális gépek, nem pedig a profilkészítésben részt vevő virtuális gépek alapján történik.  

A költségeket havi vagy éves bontásban tekintheti meg. További információkat olvashat a [támogatott célrégiókról](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) és a [támogatott pénznemekről](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Összetevők szerinti költségek**: A vészhelyreállítás teljes költsége négy összetevőből áll: Számítás, Tárterület, Hálózat és az Azure Site Recovery licencköltségei. A költségeket a használat alapján számoljuk ki, amely költségek a replikáció során és a számítás, tárterület (prémium és standard), a helyszíni hely és az Azure között konfigurált ExpressRoute/VPN, valamint az Azure Site Recovery licence DR működési ideje során lépnek fel.

**Állapot szerinti költségek**: A vészhelyreállítás (DR) teljes költségeinek kategóriái két különböző állapoton alapulnak – Replikáció és DR működése.

**Replikáció költségei**: A replikáció során felmerülő költségek. Ez fedezi a tárolás, a hálózat és az Azure Site Recovery-licenc költségeit.

**DR működés költségei**: A feladatátvételi tesztek során felmerülő költségek. Az Azure Site Recovery virtuális gépeket indít el a feladatátvételi tesztek során. A DR működési költségei a futó virtuális gépek számítási és tárolási költségeit fedezik.

**Az Azure Storage tárterület éves/havi költségei** A tárterület teljes költségeit mutatja, amelyek a replikációhoz és a DR működésekor használt prémium és standard tárterület esetén merülnek fel.
A virtuális gépenkénti részletes költségelemzést a [Költségbecslés](site-recovery-vmware-deployment-planner-cost-estimation.md) táblázatban tekintheti meg.

### <a name="growth-factor-and-percentile-values-used"></a>A használt növekedési tényező és százalékértékek
A munkalap alján található szakasz a profilkészítésben részt vevő virtuális gépek összes teljesítményszámlálójához használt százalékértéket (az alapértelmezett érték 95 százalék), valamint az összes számításban használt növekedési tényezőt (az alapértelmezett érték 30 százalék) mutatja.

![A használt növekedési tényező és százalékértékek](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Javaslatok az elérhető sávszélesség bemenetként való megadásával

![Javaslatok az elérhető sávszélesség bemenetként való megadásával](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Előfordulhat olyan helyzet, hogy legfeljebb x Mbps sávszélességet tud beállítani a Site Recovery replikációjára. Az eszközzel megadhatja a rendelkezésre álló sávszélességet (a jelentéskészítés közben, a -Bandwidth paraméterrel), és megkapja az elérhető RPO-t percben megadva. Az elérhető RPO-értéknek ismeretében eldöntheti, hogy további sávszélességet kell beállítania, vagy elfogadja a vészhelyreállítási megoldást ezzel az RPO-val.

![Elérhető RPO 500 Mbps sávszélességhez](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Virtuálisgép-tároló elhelyezése

>[!Note]
>A Deployment Planner v 2.5-ös verzióban azt ajánljuk, hogy a tárolók elhelyezése olyan gépek esetében, amelyek közvetlenül a felügyelt lemezekre replikálódnak.

![Virtuálisgép-tároló elhelyezése](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Replikációs tár típusa**: vagy egy standard vagy prémium szintű felügyelt lemez, amely a **virtuális gépekről** az oszlopba helyezendő összes megfelelő virtuális gép replikálására szolgál.

**Log Storage-fiók típusa**: az összes replikációs naplót egy standard Storage-fiók tárolja.

A **Storage-fiók javasolt előtagja**: a javasolt három karakterből álló előtag, amelyet a cache Storage-fiók elnevezéséhez használhat. Saját előtagot is használhat, de az eszköz által javasolt nevek követik a [tárfiókok partíció-elnevezési szabályait](/en-in/azure/storage/blobs/storage-performance-checklist).

**Javasolt log-fiók neve**: a tárolási fiók neve, miután belefoglalta a javasolt előtagot. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Elhelyezés összegzése**: a virtuális gépeknek a tárolási típus szerint történő védelméhez szükséges lemezek összegzése. Magában foglalja a virtuális gépek teljes számát, a teljes kiosztott méretet az összes lemezen, valamint a lemezek teljes számát.

**Virtual Machines to Place** (Elhelyezendő virtuális gépek): Az összes olyan virtuális gép listája, amelyet az optimális teljesítmény és használat érdekében az adott tárfiókon ajánlott elhelyezni.

## <a name="compatible-vms"></a>Kompatibilis virtuális gépek
![A kompatibilis virtuális gépek Excel-táblázata](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM Name** (Virtuális gép neve): Jelentés létrehozásakor a VMListFile-ban használt virtuálisgépnév vagy IP-cím. Ez az oszlop a virtuális gépekhez csatolt lemezek (VMDK-k) listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**Virtuális gépek kompatibilitása**: az értékek **Igen** és **Igen \* *_. _* igen** \* , olyan példányok esetén, amelyekben a virtuális gép a [prémium SSD](../virtual-machines/disks-types.md)-k számára alkalmas. Itt a profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a P20-as vagy P30-as kategóriának, de a lemez mérete miatt a rendszer P10-es vagy P20-as kategóriába sorolja be. A tárfiók a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt. Például:
* 128 GB alatt P10.
* 128 GB és 256 GB között P15
* 256 GB és 512 GB között P20.
* 512 GB és 1024 GB között P30.
* 1025 GB és 2048 GB között P40.
* 2049 GB és 4095 GB között P50.

Ha például egy lemez számítási feladatait a P20 vagy a P30 kategóriába helyezi, de a méret leképezi egy alacsonyabb Premium Storage-lemez típusra, akkor az eszköz **Igen értéket** jelöl a virtuális gép számára \* . Az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen az ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.

**Storage Type** (Tároló típusa): Standard vagy Premium.

**Replikációhoz létrehozott Asrseeddisk (felügyelt lemez)**: a replikáció engedélyezésekor létrehozott lemez neve. Az Azure-ban tárolja az adattárakat és annak pillanatképeit.

**Peak R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS-csúcsérték (növekedési tényezővel)): A lemez írási/olvasási IOPS-csúcsértéke (az alapértelmezett érték a 95. percentilis), beleértve a későbbi növekedési faktort is (az alapértelmezett érték: 30%). Vegye figyelembe, hogy egy virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn in Mbps (with Growth Factor)** (Adatváltozás csúcsértéke (Mbps) (növekedési tényezővel)): A lemez adatváltozásának csúcsértéke (az alapértelmezett érték a 95. percentilis), beleértve a későbbi növekedési faktort is (alapértelmezés szerint: 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Azure VM Size** (Azure-beli virtuális gép mérete): Az Azure Cloud Services-beli hozzárendelt virtuális gép ideális mérete az adott helyszíni virtuális gép esetén. A hozzárendelés a helyszíni virtuális gép memóriáján, a lemezek/magok/hálózati adapterek számán és az olvasási/írási IOPS-értéken alapul. Mindig a legalacsonyabb, a helyszíni virtuális gép összes jellemzőjének megfelelő Azure-beli virtuálisgép-méretet ajánlott használni.

**Number of Disks** (Lemezek száma): A virtuális gép lemezeinek (VMDK-inak) teljes száma.

**Disk size (GB)** (Lemez mérete (GB)): A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok): A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)): A virtuális gép fizikai memóriája (RAM).

**NICs** (Hálózati adapterek): A virtuális gép hálózati adaptereinek száma.

**Boot Type** (Rendszerindítás típusa): A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.  Jelenleg az Azure Site Recovery támogatja a Windows Server EFI típusú virtuális gépeket (Windows Server 2012, 2012 R2 és 2016) abban az esetben, ha a rendszerindító lemez partícióinak száma kevesebb mint 4, a rendszerindító szektor mérete pedig 512 bájt. Az EFI típusú virtuális gépek védelme érdekében az Azure Site Recovery mobilitási szolgáltatás verziójának legalább 9.13-asnak kell lennie. Az EFI virtuális gépek esetében kizárólag a feladatátvétel támogatott. A feladat-visszavétel nem támogatott.  

**Operációs rendszer típusa**: a virtuális gép operációs rendszerének típusa. Ennek értéke Windows, Linux vagy egyéb lehet a virtuális gép létrehozása során a VMware vSphere használatával kiválasztott sablon alapján.  

## <a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek

![A nem kompatibilis virtuális gépek Excel-táblázata
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM Name** (Virtuális gép neve): Jelentés létrehozásakor a VMListFile-ban használt virtuálisgépnév vagy IP-cím. Ez az oszlop a virtuális gépekhez csatolt VMDK-k listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**VM Compatibility** (Virtuális gép kompatibilitása): Azt jelzi, hogy miért nem kompatibilis az adott virtuális gép a Site Recoveryvel való használattal. Az indokok a virtuális gép minden nem kompatibilis lemezénél vannak megadva, és a közzétett [tárhelykorlátok](/en-in/azure/storage/common/scalability-targets-standard-account) alapján a következők lehetnek:

* Az adatlemez mérete vagy helytelen operációsrendszer-lemez mérete nem megfelelő. [Tekintse át](vmware-physical-azure-support-matrix.md#azure-vm-requirements) a támogatási korlátokat. 
* A virtuális gép teljes mérete (replikáció + TFO) meghaladja a támogatott tárfiók méretkorlátozását (35 TB). Ez a fajta inkompatibilitás általában akkor fordul elő, ha a virtuális gép egy lemezének egyik teljesítményjellemzője meghaladja a támogatott standard szintű tárolóra vonatkozó Azure- vagy Site Recovery-korlátozásokat. Egy ilyen példány a prémium szintű tárolózónába kényszeríti a virtuális gépet. A prémium szintű tárfiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. Ügyeljen arra is, hogy amikor a feladatátvételi tesztet védett virtuális gépen hajtja végre, akkor az ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ezen a példányon a lemez méretének kétszeresét kell kiosztani, hogy a replikáció is folytatódhasson, és ezzel párhuzamosan a feladatátvételi teszt is sikeres legyen.

* A forrás IOPS-érték meghaladja a tároló lemezenkénti 7500-as IOPS-korlátját.

* A forrás IOPS-érték meghaladja a tároló virtuális gépenkénti 80 000-es IOPS-korlátját.

* Az átlagos adatváltozás meghaladja a támogatott Site Recovery adatváltozási korlátot 20 MB/s értékre a lemez átlagos I/O-méretéhez.

* Az összes virtuális gépre vonatkozó adatváltozás csúcsértéke meghaladja a Site Recovery virtuális gépenkénti 54 Mb/s-os támogatott adatváltozási csúcsérték korlátját.

* Az átlagos tényleges írási IOPS-érték meghaladja a Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját.

* A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet.

* A napi adatváltozás túllépi a folyamatkiszolgálók által támogatott napi 2 TB-os keretet.


**Peak R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS-csúcsérték (növekedési tényezővel)): A lemez IOPS-csúcsértéke (alapértelmezés szerint a 95. százalékos érték), beleértve a későbbi növekedési faktort is (az alapértelmezett érték: 30%). Vegye figyelembe, hogy a virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Peak Data Churn in Mbps (with Growth Factor)** (Adatváltozás csúcsértéke (Mbps) (növekedési tényezővel)): A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. százalékérték), beleértve a későbbi növekedési faktort is (alapértelmezés szerint 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Number of Disks** (Lemezek száma): A virtuális gép VMDK-inak teljes száma.

**Disk size (GB)** (Lemez mérete (GB)): A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok): A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)): A virtuális gép fizikai memóriájának (RAM) mennyisége.

**NICs** (Hálózati adapterek): A virtuális gép hálózati adaptereinek száma.

**Boot Type** (Rendszerindítás típusa): A virtuális gép rendszerindítási típusa. Ez BIOS vagy EFI lehet.  Jelenleg az Azure Site Recovery támogatja a Windows Server EFI típusú virtuális gépeket (Windows Server 2012, 2012 R2 és 2016) abban az esetben, ha a rendszerindító lemez partícióinak száma kevesebb mint 4, a rendszerindító szektor mérete pedig 512 bájt. Az EFI típusú virtuális gépek védelme érdekében az Azure Site Recovery mobilitási szolgáltatás verziójának legalább 9.13-asnak kell lennie. Az EFI virtuális gépek esetében kizárólag a feladatátvétel támogatott. A feladat-visszavétel nem támogatott.

**Operációs rendszer típusa**: a virtuális gép operációs rendszerének típusa. Ennek értéke Windows, Linux vagy egyéb lehet a virtuális gép létrehozása során a VMware vSphere használatával kiválasztott sablon alapján.

## <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. A legjobb eredmények érdekében még az üzembe helyezés megtervezése után is ajánlott az alkalmazás alapos tesztelése feladatátvételi tesztek használatával, így valós képet kaphat az alkalmazás teljesítményéről.

**Replikáció tárolási célja** | **Átlagos forrás lemez I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több | 20 MB/s | 1684 GB/lemez

**Forrásadat-változás** | **Felső korlát**
---|---
Adatváltozás csúcsértéke az összes lemezen virtuális gépenként | 54 MB/s
Folyamatkiszolgáló által támogatott napi adatváltozás maximuma | 2 TB

Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek. A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján. Az előbbi számok egy általános, körülbelül ötperces várólistát feltételeznek. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.


## <a name="cost-estimation"></a>Költségbecslés
További információk a [költségbecslésről](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Következő lépések
További információk a [költségbecslésről](site-recovery-vmware-deployment-planner-cost-estimation.md).