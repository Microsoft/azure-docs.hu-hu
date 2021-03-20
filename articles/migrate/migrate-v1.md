---
title: A Azure Migrate korábbi verziójának használata
description: Leírja, hogyan használható a Azure Migrate korábbi verziója.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 00e44185c938c94903e9b85a4748906721dac27f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571696"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>A Azure Migrate korábbi verziójának használata

Ez a cikk a Azure Migrate korábbi verziójának használatáról nyújt információt. 


A Azure Migrate szolgáltatásnak két verziója van:

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Előző verzió**: ha a Azure Migrate korábbi verzióját használja (csak a helyszíni VMWare virtuális gépek felmérése támogatott), akkor most az aktuális verziót kell használnia. A korábbi verziójú projekteket klasszikus projekteknek nevezzük ebben a cikkben. A klasszikus Azure Migrate kivonása február 2024. Február 2024 után a Azure Migrate klasszikus verziója már nem támogatott, és a rendszer a klasszikus projektekben lévő leltári metaadatokat törli. Ha továbbra is klasszikus Azure Migrate-projekteket kell használnia, ezt a következő műveleteket hajthatja végre:
    - Már nem hozhat létre áttelepítési projekteket.
    - Javasoljuk, hogy ne végezzen új felfedezéseket.
    - Továbbra is hozzáférhet a meglévő projektekhez.
    - Továbbra is futtathatja az értékeléseket.
    

## <a name="upgrade-between-versions"></a>Frissítés verziók között

Az előző verzióban lévő projektek vagy összetevők nem frissíthetők az új verzióra. [Létre kell hoznia egy új Azure Migrate projektet](create-manage-projects.md), és [hozzá kell adnia az értékelési és áttelepítési eszközöket](./create-manage-projects.md) . Az oktatóanyagok segítségével megismerheti, hogyan használhatja az elérhető értékelési és áttelepítési eszközöket. Ha egy klasszikus projekthez csatlakoztatott Log Analytics munkaterülettel rendelkezik, a klasszikus projekt törlése után csatolhatja azt egy aktuális verzió projekthez.

## <a name="find-projects-from-previous-version"></a>Projektek keresése az előző verzióból

Az előző verzióból származó projekteket a következőképpen keresheti meg:

1. A Azure Portal > **minden szolgáltatás** területen keresse meg és válassza ki **Azure Migrate**. 
2. A Azure Migrate irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate projektek eléréséhez.
3. A klasszikus projektek megnyitásához kattintson a hivatkozásra.

## <a name="delete-projects-from-previous-version"></a>A korábbi verzióból származó projektek törlése

Az előző verzióból származó projekteket a következőképpen keresheti meg és törölheti:

1. A Azure Portal > **minden szolgáltatás** területen keresse meg és válassza ki **Azure Migrate**. 
2. A Azure Migrate irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate projektek eléréséhez.
3. A klasszikus projektek megnyitásához kattintson a hivatkozásra.
4. Válassza ki a törölni és törölni kívánt projektet. 


## <a name="create-an-assessment"></a>Értékelés létrehozása

Miután felfedezte a virtuális gépeket a portálon, csoportosíthatja őket, és létrehozhat értékeléseket.

- Azonnal létrehozhat helyszíni értékeléseket közvetlenül a virtuális gépek felderítése után a portálon.
- A teljesítmény-alapú felmérések esetében javasoljuk, hogy várjon legalább egy napot a teljesítmény-alapú Értékelés létrehozása előtt, hogy megbízható méretű javaslatokat kapjon.

Hozzon létre egy értékelést az alábbiak szerint:

1. A projekt **Áttekintés** lapján kattintson az **+Értékelés létrehozása** elemre.
2. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.
3. Hozza létre a csoportot, és adja meg a nevét.
4. Válassza ki a csoporthoz hozzáadni kívánt gépeket.
5. A csoport és az értékelés létrehozásához kattintson az **Értékelés létrehozása** elemre.
6. Az értékelést a létrehozását követően az **Áttekintés** > **Irányítópult** menüpontban tekintheti meg.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.

Ha szeretné egy meglévő értékelését a legújabb teljesítményadatokkal frissíteni, akkor ezt az értékelés **Újraszámítás** parancsával teheti meg.

## <a name="review-an-assessment"></a>Értékelés áttekintése 

Az értékelés három szakaszból áll:

- Az értékelés megfelelőségi elemzéssel kezdődik, amely alapján megállapítható, hogy a gépek kompatibilisek-e az Azure-ban.
- Méretezési becslések.
- Havi költségbecslés.

A gépek csak akkor haladnak át egy későbbi fázisban, ha az előzőre kerül. Ha például egy gép meghibásodik a megfelelőségi ellenőrzésen, az Azure-ban nem megfelelőként van megjelölve, és a méretezés és a költségszámítás nem történik meg.


### <a name="review-azure-readiness"></a>Az Azure-kompatibilitás áttekintése

Az értékelésben az Azure használatához szükséges állapotnézet az egyes virtuális gépek készenléti állapotát jeleníti meg.

**Készenlét** | **Állapot** | **Részletek**
--- | --- | ---
Készen áll az Azure-beli használatra | Nincsenek kompatibilitási problémák. A gép telepíthető át az Azure-ba, és az Azure-ban teljes körű Azure-támogatással indul el. | A kész virtuális gépekhez az Azure Migrate javasol egy virtuálisgép-méretet az Azure-ban.
Feltételesen készen áll az Azure-beli használatra | Előfordulhat, hogy a gép az Azure-ban indul el, de nem rendelkezik teljes körű Azure-támogatással. Például egy olyan gép, amelyen az Azure nem támogatja a Windows Server régebbi verzióját. | A Azure Migrate ismerteti a készültségi problémákat, és szervizelési lépéseket tesz lehetővé.
Nem áll készen az Azure-beli használatra |  A virtuális gép nem indul el az Azure-ban. Ha például egy virtuális gépen 4 TB-nál több lemez található, az nem üzemeltethető az Azure-ban. | A Azure Migrate ismerteti a készültségi problémákat, és szervizelési lépéseket tesz lehetővé.
A kompatibilitás ismeretlen | Azure Migrate nem tudja azonosítani az Azure-felkészültséget, általában azért, mert az adatforrások nem érhetők el.


#### <a name="azure-vm-properties"></a>Azure-beli virtuális gép tulajdonságai
A készültség számos virtuálisgép-tulajdonságot figyelembe vesz, hogy megtudja, a virtuális gép futtatható-e az Azure-ban.


**Tulajdonság** | **Részletek** | **Készenlét**
--- | --- | ---
**Rendszerindítás típusa** | A BIOS támogatott. Az UEFI nem támogatott. | Feltételesen üzemkész, ha a rendszerindítás UEFI típusú.
**Cores** | A Machines Core <= az Azure-beli virtuális gépek számára támogatott magok maximális száma (128).<br/><br/> Ha rendelkezésre áll a teljesítmény előzményei, Azure Migrate figyelembe veszi a felhasznált magokat.<br/>Ha az értékelési beállításokban meg van adva egy kényelmi tényező, a kihasználatlan magok számát a Comfort faktor megszorozza.<br/><br/> Ha nincsenek teljesítménybeli előzmények, Azure Migrate a lefoglalt magokat használja a komfort tényező alkalmazása nélkül. | Ha a korlát értéke kisebb vagy egyenlő, akkor készen áll.
**Memória** | A számítógép memóriájának mérete <= a maximális memória (3892 GB az Azure M sorozat Standard_M128m &nbsp; <sup>2</sup>) egy Azure-beli virtuális gépen. [További információ](../virtual-machines/sizes.md).<br/><br/> Ha rendelkezésre áll a teljesítmény előzményei, Azure Migrate a felhasznált memóriát veszi figyelembe.<br/><br/>Ha meg van adva egy kényelmi tényező, a kihasznált memóriát a komfort tényező szorozza meg.<br/><br/> Ha nincs előzmény, a rendszer a lefoglalt memóriát használja a komfort tényező alkalmazása nélkül.<br/><br/> | A határértékeken belül készen áll.
**Storage-lemez** | A lemez lefoglalt méretének 4 TB (4096 GB) vagy kevesebbnek kell lennie.<br/><br/> A géphez csatolt lemezek számának 65 vagy annál kisebbnek kell lennie, beleértve az operációsrendszer-lemezt is. | A határértékeken belül készen áll.
**Hálózat** | A számítógépnek 32 vagy annál kevesebb hálózati adapterrel kell rendelkeznie. | A határértékeken belül készen áll.

#### <a name="guest-operating-system"></a>Vendég operációs rendszer

A virtuális gép tulajdonságai mellett Azure Migrate a helyszíni virtuális gép vendég operációs rendszerét is megvizsgálja annak megállapítására, hogy a virtuális gép futtatható-e az Azure-ban.

- Azure Migrate a vCenter Serverban megadott operációs rendszert veszi figyelembe.
- Mivel a Azure Migrate által végzett felderítés a készüléken alapul, nem tudja ellenőrizni, hogy a virtuális gépen futó operációs rendszer megegyezik-e a vCenter Serverban megadott értékkel.

A rendszer a következő logikát használja.

**Operációs rendszer** | **Részletek** | **Készenlét**
--- | --- | ---
Windows Server 2016 és minden SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2012 R2 és minden SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2012 és minden SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2008 R2 és minden SPs | Az Azure teljes körű támogatást biztosít.| Készen áll az Azure-beli használatra
Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2003, 2003 R2 | Támogatás nélkül, és az Azure-támogatáshoz [egyéni támogatási szerződés (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) szükséges. | Feltételesen készen áll az Azure használatára, érdemes lehet frissíteni az operációs rendszert az Azure-ba való Migrálás előtt.
Windows 2000, 98, 95, NT, 3,1, MS-DOS | Nem támogatott. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Az Azure-ra feltételesen készen áll, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Windows-ügyfél 7, 8 és 10 | Az Azure csak a [Visual Studio-előfizetéshez](../virtual-machines/windows/client-images.md) nyújt támogatást. | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztali verzió | Az Azure támogatást nyújt a több- [bérlős üzemeltetési jogosultságokhoz.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Nem támogatott. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Az Azure-ra feltételesen készen áll, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Linux | Az Azure támogatja ezeket a [Linux operációs rendszereket](../virtual-machines/linux/endorsed-distros.md). Más Linux operációs rendszerek is elindíthatók az Azure-ban, de javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert egy támogatott verzióra. | Készen áll az Azure-ra, ha a verziót jóváhagyták.<br/><br/>Feltételesen üzemkész, ha a verzió nincs támogatva.
Egyéb operációs rendszerek<br/><br/> Például: Oracle Solaris, Apple macOS stb., FreeBSD stb. | Az Azure nem támogatja ezeket az operációs rendszereket. A gép elindítható az Azure-ban, de az Azure nem biztosít operációsrendszer-támogatást. | Az Azure-ban feltételesen készen áll arra, hogy az Azure-ba való Migrálás előtt telepítsen egy támogatott operációs rendszert.  
Az operációs rendszer **vCenter Serverként van megadva** | A Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készültség. Győződjön meg arról, hogy a virtuális gépen futó operációs rendszer támogatott az Azure-ban.
32 bites operációs rendszerek | Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure használatára, érdemes lehet a gép operációs rendszerét 32 bites operációs rendszerről 64 bites operációs rendszerre frissíteni az Azure-ba való Migrálás előtt.


### <a name="review-sizing"></a>Méretezés áttekintése

 A Azure Migrate méretre vonatkozó javaslat az értékelés tulajdonságaiban megadott méretezési feltételtől függ.

- Ha a méretezés teljesítmény-alapú, a méretre vonatkozó javaslat a virtuális gépek (processzor és memória) és a lemezek (IOPS és átviteli sebesség) teljesítményének előzményeit veszi figyelembe.
- Ha a méretezési feltétel "mint helyszíni", az Azure-ban a méretre vonatkozó javaslat a helyszíni virtuális gép méretétől függ. A lemez méretezése az értékelés tulajdonságaiban megadott tárolási típuson alapul (az alapértelmezett érték a prémium szintű lemezek). Azure Migrate nem veszi figyelembe a virtuális gép és a lemezek teljesítményadatait.

### <a name="review-cost-estimates"></a>Költségbecslések áttekintése

A költségbecslés a virtuális gépek Azure-ban való futtatásának teljes számítási és tárolási költségeit, valamint az egyes gépek részleteit mutatják be.

- A költségbecslés kiszámítása a virtuálisgép-gépek méretére vonatkozó javaslat, valamint a lemezek és az értékelési tulajdonságok alapján történik.
- A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek.
- A költségbecslés a helyszíni virtuális gép Azure-beli infrastruktúra-szolgáltatásként (IaaS) történő futtatására szolgál. Azure Migrate nem tekinti a szolgáltatásként (Péter) vagy a szolgáltatott szoftverek (SaaS) költségeit.

### <a name="review-confidence-rating-performance-based-assessment"></a>Megbízhatósági minősítés áttekintése (teljesítmény-alapú értékelés)

Minden teljesítmény-alapú értékelés megbízhatósági minősítéssel van társítva.

- A megbízhatósági minősítés az egyik csillagtól az öt csillagig terjed (az egyik csillag a legalacsonyabb és az ötcsillagos a legmagasabb).
- A megbízhatósági minősítés egy értékeléshez van rendelve, az értékelés kiszámításához szükséges adatpontok rendelkezésre állása alapján.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés nem érhető el a helyszíni értékelésekhez.

A teljesítmény-alapú méretezéshez a Azure Migrate a következőkre van szüksége:
- A CPU kihasználtsági adatai.
- A virtuális gép memóriájának adatmennyisége.
- A virtuális géphez csatlakoztatott minden lemezhez szükség van a lemez IOPS és az átviteli sebességre.
- A virtuális géphez csatlakoztatott minden hálózati adapter esetében Azure Migrate a hálózati bemenet/kimenet szükséges.
- Ha a fentiek bármelyike nem érhető el, előfordulhat, hogy a méretre vonatkozó javaslatok (és így a megbízhatósági minősítések) nem megbízhatóak.


Az elérhető adatpontok százalékos arányának függvényében a lehetséges megbízhatósági minősítések a táblázatban vannak összegezve.

**Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag


#### <a name="assessment-issues-affecting-confidence-ratings"></a>A megbízhatósági minősítéseket érintő értékelési problémák

Előfordulhat, hogy az értékelés számos ok miatt nem érhető el az összes adatpontnál:

- Az értékelés időtartama alatt nem találta meg a környezetét. Ha például az értékelést egy napra állítja be, akkor a felderítés megkezdése után legalább egy napot várnia kell, vagy az összes összegyűjtött adatpontot.
- Néhány virtuális gép leállt az értékelés kiszámításának időtartama alatt. Ha bármely virtuális gép ki lett kapcsolva az időtartam egy részében, Azure Migrate nem tud teljesítményadatokat gyűjteni az adott időszakra vonatkozóan.
- Néhány virtuális gép az értékelés számítási időtartama alatt lett létrehozva. Ha például a múlt havi teljesítmény előzményei alapján hoz létre értékelést, de egy héttel ezelőtt több virtuális gépet hoz létre a környezetben, akkor az új virtuális gépek teljesítmény-előzményei nem lesznek a teljes időtartamra.

> [!NOTE]
> Ha az értékelés megbízhatósági minősítése öt csillag alatt van, várjon legalább egy napig, amíg a készülék profilja bekerül a környezetbe, majd számítsa ki újra az értékelést. Ha nem áll fenn a teljesítmény-alapú méretezés, előfordulhat, hogy nem megbízható. Ha nem szeretné újraszámítani az újraszámítást, javasoljuk, hogy a felmérési tulajdonságok módosításával váltson helyszíni méretre.



## <a name="create-groups-using-dependency-visualization"></a>Csoportok létrehozása függőségi vizualizáció használatával

A csoportok manuális létrehozása mellett a függőségi vizualizáció használatával csoportokat is létrehozhat.
- Ezt a módszert általában akkor érdemes használni, ha az értékelés futtatása előtt szeretné felmérni a nagyobb megbízhatósági szintű csoportokat.
- A függőségi vizualizáció segítséget nyújt az Azure-ba való Migrálás hatékony megtervezésében. Ezzel biztosíthatja, hogy semmi ne maradjon hátra, és hogy az Azure-ba való Migrálás során ne történjen meglepő kimaradás.
- Felderítheti az összes olyan egymástól függő rendszert, amely együtt kell migrálni, és meg kell határoznia, hogy egy futó rendszer továbbra is a felhasználókat szolgálja-e, vagy az áttelepítés helyett a leszerelésre jelölt.
- Azure Migrate a függőségi vizualizáció engedélyezéséhez a Azure Monitor Service Map megoldását használja.

> [!NOTE]
> A függőség vizualizációja Azure Governmentban nem érhető el.

A függőségi vizualizáció beállításához egy Log Analytics munkaterületet társít egy Azure Migrate projekthez, telepítenie kell az ügynököket azokon a gépeken, amelyeken függőségeket kíván megjeleníteni, majd csoportokat kell létrehoznia a függőségi adatok használatával. 



### <a name="associate-a-log-analytics-workspace"></a>Log Analytics munkaterület hozzárendelése

A függőségi vizualizáció használatához egy Log Analytics munkaterületet társít egy áttelepítési projekthez. A munkaterületet csak abban az előfizetésben lehet létrehozni vagy csatolni, amelyben az áttelepítési projekt létrejött.

1. Ha Log Analytics munkaterületet szeretne csatolni egy projekthez, az **áttekintés**> **Essentials** szolgáltatásban kattintson a **Konfigurálás szükséges** elemre.
2. Létrehozhat egy új munkaterületet, vagy csatolhat egy meglévőt:
  - Új munkaterület létrehozásához adjon meg egy nevet. A munkaterület ugyanabban az [Azure-földrajzbeli](https://azure.microsoft.com/global-infrastructure/geographies/) régióban jön létre, mint az áttelepítési projekt.
  - Ha meglévő munkaterületet csatol, az összes elérhető munkaterületet az áttelepítési projekttel megegyező előfizetésben is kiválaszthatja. Csak azok a munkaterületek jelennek meg, amelyek egy [támogatott Service Map régióban](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)lettek létrehozva. Munkaterület csatolásához ellenőrizze, hogy van-e "olvasó" hozzáférése a munkaterülethez.

> [!NOTE]
> Az áttelepítési projekthez társított munkaterület nem módosítható.

### <a name="download-and-install-vm-agents"></a>VM-ügynökök letöltése és telepítése

A munkaterület konfigurálása után le kell töltenie és telepítenie kell az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni. Továbbá, ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, le kell töltenie és telepítenie kell [log Analytics-átjárót](../azure-monitor/agents/gateway.md) .

1. Az **Áttekintés** lapon kattintson   >  a **gépek** kezelése lehetőségre, és válassza ki a szükséges gépet.
2. A **függőségek** oszlopban kattintson az **ügynökök telepítése** elemre.
3. A **függőségek** lapon töltse le és telepítse a Microsoft monitoring Agent (MMA) és a függőségi ügynököt minden olyan virtuális gépen, amelyet fel szeretne mérni.
4. Másolja ki a munkaterület-azonosítót és -kulcsot. Ezekre akkor van szükség, amikor telepíti az MMA-t a helyszíni gépre.

> [!NOTE]
> Az ügynökök telepítésének automatizálásához használhat olyan központi telepítési eszközt, mint például a Configuration Manager vagy egy olyan partneri eszköz, amely egy olyan [Intigua](https://www.intigua.com/intigua-for-azure-migration), amely ügynök-telepítési megoldást biztosít a Azure Migrate számára.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Az MMA-ügynök telepítése Windows rendszerű gépre

Az ügynök telepítése Windows rendszerű gépre:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. **A licencfeltételek oldalon kattintson** **az Elfogadom gombra a** licenc elfogadásához.
3. A **célmappában** tartsa meg vagy módosítsa az alapértelmezett telepítési mappát > a **Tovább gombra**.
4. Az **ügynök telepítési beállításai** területen válassza az **Azure log Analytics**  >  **tovább** lehetőséget.
5. Új Log Analytics munkaterület hozzáadásához kattintson a **Hozzáadás** gombra. Illessze be azt a munkaterület-azonosítót és-kulcsot, amelyet a portálról másolt. Kattintson a **Tovább** gombra.

Az ügynököt a parancssorból vagy egy automatizált módszerrel, például Configuration Manager is telepítheti. [További](../azure-monitor/agents/log-analytics-agent.md#installation-options) információ az MMA-ügynök telepítésével kapcsolatban ezen módszerek használatával.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Az MMA-ügynök telepítése Linux rendszerű gépre

Az ügynök telepítése Linux rendszerű gépre:

1. Vigye át a megfelelő köteget (x86 vagy x64) a Linux rendszerű számítógépre SCP/SFTP használatával.
2. Telepítse a csomagot a--install argumentum használatával.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[További](../azure-monitor/agents/agents-overview.md#supported-operating-systems) információ az MMA által támogatott Linux operációs rendszerek listájáról.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Az MMA-ügynök telepítése Operations Manager által figyelt gépre

A System Center Operations Manager 2012 R2 vagy újabb verziói által monitorozott gépek esetében nincs szükség az MMA-ügynök telepítésére. Service Map integrálódik a Operations Manager MMA-val a szükséges függőségi adatok összegyűjtéséhez. [További információ](../azure-monitor/vm/service-map-scom.md#prerequisites). A függőségi ügynököt telepíteni kell.

### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1. A függőségi ügynök Windows rendszerű gépen való telepítéséhez kattintson duplán a telepítési fájlra, és kövesse a varázslót.
2. A függőségi ügynök Linux rendszerű gépen történő telepítéséhez a következő parancs használatával root-ként kell telepíteni:

    ```sh InstallDependencyAgent-Linux64.bin```

- További információ a Windows és Linux operációs rendszerek [függőségi ügynökének támogatásáról](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) .
- [További](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) információ arról, hogyan használhatók a parancsfájlok a függőségi ügynök telepítéséhez.

>[!NOTE]
> A függőségi ügynök telepítéséhez szükséges rendszerkövetelmények és módszerek áttekintését ismertető Azure Monitor for VMs cikk a Service Map megoldásra is érvényes.

### <a name="create-a-group-with-dependency-mapping"></a>Függőségi leképezést tartalmazó csoport létrehozása

1. Az ügynökök telepítése után lépjen a portálra, és kattintson a   >  **gépek** kezelése lehetőségre.
2. Keresse meg azt a gépet, amelyre az ügynököket telepítette.
3. A gép **függőségek** oszlopának ekkor a következőnek kell megjelennie: **View függőségei**. Az oszlopra kattintva megtekintheti a gép függőségeit.
4. A gép függőségi térképe a következő adatokat jeleníti meg:
    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a gépről/számítógépről
        - Azok a függő gépek, amelyeken nincs telepítve az MMA és a függőségi ügynök, a portszámok szerint vannak csoportosítva.
        - Az MMA-t és a függőségi ügynököt futtató függő gépek külön mezőkként jelennek meg.
    - A gépen futó folyamatok kibontásával megtekintheti a folyamatokat
    - Megjelenik a számítógép tulajdonságai, beleértve a teljes tartománynevet, az operációs rendszert és a MAC-címeket. A részletek megtekintéséhez kattintson az egyes gépek mezőre.

4. A különböző időtartamokra vonatkozó függőségeket az időtartomány címkében lévő idő időtartamára kattintva tekintheti meg. Alapértelmezés szerint a tartomány egy óra. Módosíthatja az időtartományt, vagy megadhatja a kezdő és a záró dátumot, valamint az időtartamot.

   > [!NOTE]
   >    Legfeljebb egy óra időtartamú időtartomány támogatott. A [függőségi adat](./how-to-create-group-machine-dependencies.md) hosszabb időtartamon keresztüli lekérdezéséhez használjon Azure monitor naplókat.

5. Miután azonosította a csoportba felvenni kívánt függő gépeket, a CTRL billentyűt lenyomva tartva több gépet is kiválaszthat a térképen, majd kattintson a **számítógépek csoportosítása** lehetőségre.
6. Adja meg a csoport nevét. Győződjön meg arról, hogy a Azure Migrate a függő gépeket deríti fel.

    > [!NOTE]
    > Ha a Azure Migrate nem észleli a függő gépet, azt nem lehet felvenni a csoportba. Ha ilyen gépeket szeretne felvenni a csoportba, futtassa újra a felderítési folyamatot a vCenter Server megfelelő hatókörével, és győződjön meg arról, hogy a Azure Migrate a gépet felderíti.  

7. Ha értékelést szeretne készíteni ehhez a csoporthoz, jelölje be a jelölőnégyzetet, és hozzon létre egy új értékelést a csoport számára.
8. A csoport mentéséhez kattintson **az OK** gombra.

A csoport létrehozása után javasoljuk, hogy telepítse az ügynököket a csoport összes számítógépére, és pontosítsa a csoportot úgy, hogy megjeleníti a teljes csoport függőségét.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Függőségi adatok lekérdezése Azure Monitor naplókból

A Service Map által rögzített függőségi adatmennyiség a Azure Migrate projekthez társított Log Analytics munkaterületen kérdezhető le. [További](../azure-monitor/vm/service-map.md#log-analytics-records) információ a Service Map adattáblákról Azure monitor naplók lekérdezéséhez. 

A Kusto-lekérdezések futtatása:

1. Az ügynökök telepítése után lépjen a portálra, és kattintson az **Áttekintés** elemre.
2. Az **Áttekintés** területen nyissa meg a projekt **Essentials (alapok** ) szakaszát, és kattintson a **OMS munkaterület** melletti munkaterület neve elemre.
3. A log Analytics munkaterület lapon kattintson az **általános**  >  **naplók** elemre.
4. A függőségi adatok Azure Monitor naplók használatával történő gyűjtésének megírása a lekérdezésbe. Keresse meg a következő szakaszban szereplő lekérdezéseket.
5. Futtassa a lekérdezést a Futtatás gombra kattintva. 

[További](../azure-monitor/logs/log-analytics-tutorial.md) információ a Kusto-lekérdezések írásához. 

### <a name="sample-azure-monitor-logs-queries"></a>Példa Azure Monitor naplók lekérdezésére

A következő példák a függőségi adatok kinyerésére használható lekérdezések. A lekérdezéseket módosíthatja az előnyben részesített adatpontok kinyeréséhez. A függőségi adatrekordok mezőinek teljes listája [itt](../azure-monitor/vm/service-map.md#log-analytics-records)érhető el. További példákat [itt](../azure-monitor/vm/service-map.md#sample-log-searches)találhat.

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>A bejövő kapcsolatok összefoglalása a gépek egy készletén

A táblázatban lévő, a kapcsolati metrikák, VMConnection, nem az egyes fizikai hálózati kapcsolatok. A fizikai hálózati kapcsolatok több logikai kapcsolatba vannak csoportosítva. [További](../azure-monitor/vm/service-map.md#connections) információ arról, hogyan összesítik a fizikai hálózati kapcsolatok adatait egyetlen logikai rekordba a VMConnection-ben. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>A számítógépek egy készlete között a bejövő kapcsolatokon küldött és fogadott adatmennyiség összegzése

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>Következő lépések
[További](migrate-services-overview.md) információ a Azure Migrate legújabb verziójáról.