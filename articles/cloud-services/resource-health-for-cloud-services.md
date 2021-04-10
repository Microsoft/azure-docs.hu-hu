---
title: Cloud Services Resource Health (klasszikus)
description: Ez a cikk a Microsoft Azure Cloud Services (klasszikus) Resource Health-ellenőrzési (RHC-) támogatásáról beszél.
ms.topic: article
ms.service: cloud-services
ms.subservice: resource-health
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 46a27e42d6b3440914ca0d32e1ae2138b128021e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935158"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Az Azure Cloud Services (klasszikus) Resource Health-ellenőrzési (RHC) támogatása

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk a [Microsoft Azure Cloud Services (klasszikus)](https://azure.microsoft.com/services/cloud-services) Resource Health-ellenőrzési (RHC-) támogatásáról beszél.

A Cloud Services [Azure Resource Health](../service-health/resource-health-overview.md) segítséget nyújt a cloud Service &-telepítést befolyásoló szolgáltatási problémák diagnosztizálásában és támogatásában. A felhőalapú szolgáltatások jelenlegi és múltbeli állapotáról szóló jelentést a telepítés, a szerepkör & a szerepkör-példány szintjén.

Azure-állapotjelentések az Azure-ügyfelek széles körét érintő problémákról. A Resource Health személyre szabott irányítópultot biztosít az erőforrások állapotáról. Resource Health azt mutatja, hogy az erőforrások az Azure-szolgáltatásokkal kapcsolatos problémák miatt nem voltak elérhetők. Ezek az információk megkönnyítik annak megtekintését, hogy az SLA-t megsértették-e.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="A képen a Azure Portal erőforrás állapota-ellenőrzési panelje látható.":::

## <a name="how-health-is-checked-and-reported"></a>Hogyan történik az állapot ellenőrzése és jelentése?
Az erőforrás-állapotot egy központi telepítés vagy szerepkör szintjén kell jelenteni. Az állapot-ellenõrzés a szerepkör-példány szintjén történik, összesítjük az állapotot, és bejelentjük a szerepkör szintjén. Például Ha az összes szerepkör-példány elérhető, akkor a szerepkör állapota elérhető. Hasonlóképpen összesítjük az összes szerepkör állapotát, és bejelentjük a telepítési szinten. Például Ha az összes szerepkör elérhető, az üzembe helyezés állapota elérhetővé válik. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Miért nem látok állapotot az átmeneti tárolóhelyek üzembe helyezéséhez?
Az erőforrás-állapot ellenőrzése csak az üzemi tárolóhelyek telepítéséhez használható. Az átmeneti tárolóhely üzembe helyezése még nem támogatott. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Resource Health az alkalmazás állapotának ellenőrzését is?
Nem, az állapot-ellenőrzés csak a szerepkör-példányok esetében fordul elő, és nem figyeli az alkalmazás állapotát. Például Az alkalmazás továbbra is elérhető marad, még akkor is, ha a 3 szerepkör-példány állapota nem kifogástalan. A RHC nem használ [terheléselosztó-próbákat](../load-balancer/load-balancer-custom-probe-overview.md) vagy a vendég ügynök mintavételét. Ezért az ügyfeleknek továbbra is a Load Balancer-teszteket kell használnia az alkalmazás állapotának figyelésére. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Mik a jegyzetek a Cloud Serviceshoz?
A jegyzetek az üzemelő példány vagy a szerepkörök állapota. Különböző jegyzetek szerepelnek az állapotadatok alapján, az állapot változásának indoklása stb. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Mit jelent a szerepkör-példány "nem érhető el"?
Ez azt jelenti, hogy a szerepkör-példány nem bocsát ki kifogástalan állapotú jeleket a platformra. Tekintse át a szerepkör-példány állapotát, és ellenőrizze, hogy miért nincs kibocsátva az egészséges jel.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Mit jelent az üzembe helyezés az "ismeretlen" kifejezéssel?
Ismeretlen érték azt jelenti, hogy a felhőalapú szolgáltatás központi telepítésének összesített állapota nem határozható meg. Ez általában azt jelzi, hogy a felhőalapú szolgáltatáshoz nem jött létre éles környezet, a központi telepítés újonnan lett létrehozva (és az Azure megkezdi az állapotadatok összegyűjtését), vagy a platform problémába ütközik az adott központi telepítésre vonatkozó egészségügyi események gyűjtésével.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Miért említi a szerepkör-példányok megjegyzései a virtuális gépeket a szerepkör-példányok helyett?
Mivel a szerepkör-példányok alapvetően virtuális gépek, és a virtuális gépek állapot-ellenőrzését a szerepkör-példányok esetében újra felhasználják, a virtuálisgép-kifejezés a szerepkör-példányok ábrázolására szolgál. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Cloud Services (telepítési szint) megjegyzések & a jelentésük
| Jegyzet | Leírás | 
| --- | --- | 
| Elérhető| Nincs olyan ismert probléma az Azure platformon, amely hatással van erre a Cloud Service-telepítésre |
| Ismeretlen | Jelenleg nem lehet megállapítani a felhőalapú szolgáltatás központi telepítésének állapotát | 
| Resource Health beállítása | Erőforrás állapotának beállítása ehhez az erőforráshoz. A Resource Health figyeli az Azure-erőforrásokat, és részletesen ismerteti azokat a folyamatban lévő és múltbeli eseményeket, amelyek hatással voltak rájuk|
| Csökkentett teljesítményű | A Cloud Service üzemelő példányának teljesítménye csökkent. Dolgozunk a Cloud Service üzemelő példányának automatikus helyreállításán és a probléma okának azonosításán. Jelenleg nincs szükség további műveletre |
| Nem kifogástalan | A felhőalapú szolgáltatás üzemelő példánya nem megfelelő állapotú, mert {0} a {1} szerepkör példányai nem érhetők el |
| Csökkentett teljesítményű | A felhőalapú szolgáltatás üzemelő példánya nem érhető el, mert {0} a {1} szerepkör példányai nem érhetők el | 
| Elérhető és valószínűleg érintett | A felhőalapú szolgáltatás üzemelő példánya fut, azonban egy folyamatban lévő Azure-szolgáltatás leállása miatt előfordulhat, hogy nem csatlakozik hozzá. A rendszer a leállás feloldása után visszaállítja a kapcsolatot. |
| Nem érhető el, és valószínűleg hatással van rá | A felhőalapú szolgáltatás központi telepítésének állapotát befolyásolhatja egy Azure-szolgáltatás kimaradása. A rendszer automatikusan helyreállítja a felhőalapú szolgáltatás központi telepítését a leállás feloldásakor |
| Ismeretlen és valószínűleg érintett | Jelenleg nem tudjuk meghatározni a felhőalapú szolgáltatás központi telepítésének állapotát. Ezt egy olyan folyamatban lévő Azure-szolgáltatás meghibásodása okozhatja, amely hatással lehet erre a virtuális gépre, amely a leállás feloldása után automatikusan helyreáll. |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Cloud Services (szerepkör-példány szintje) megjegyzések & a jelentésük
| Jegyzet | Leírás | 
| --- | --- | 
| Elérhető | Nincs olyan ismert probléma az Azure platformon, amely hatással van erre a virtuális gépre | 
| Ismeretlen | Jelenleg nem lehet megállapítani a virtuális gép állapotát |
| Leállítás és felszabadítás | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép leállítása és felszabadítása |
| Resource Health beállítása | Erőforrás állapotának beállítása ehhez az erőforráshoz. A Resource Health figyeli az Azure-erőforrásokat, és részletesen ismerteti azokat a folyamatban lévő és múltbeli eseményeket, amelyek hatással voltak rájuk |
| Nem érhető el | A virtuális gép nem érhető el. Dolgozunk a virtuális gép automatikus helyreállításán és a probléma okának megállapításán. Jelenleg nincs szükség további műveletre |
| Csökkentett teljesítményű | A virtuális gép csökkent teljesítményű. Dolgozunk a virtuális gép automatikus helyreállításán és a probléma okának megállapításán. Jelenleg nincs szükség további műveletre |
| Gazdagép-hardverhiba | Ezt a virtuális gépet a gazdagép-kiszolgáló végzetes {HardwareCategory} hibája befolyásolja. Az Azure újra üzembe helyezi a virtuális gépet egy kifogástalan gazdagép-kiszolgálóra |
| Ütemezett áttelepítés a csökkentett teljesítményű hardverek miatt | Az Azure megállapította, hogy a gazdagépen egy csökkentett teljesítményű {0} található, amely várhatóan nemsokára meghibásodik. Ha lehetséges, a lehető leghamarabb elvégezzük a virtuális gép élő migrálását, vagy UTC idő szerint {1} után máshogy helyezzük ismét üzembe. A szolgáltatásra vonatkozó kockázat minimálisra csökkentése érdekében, ha a hardver nem sikerül a rendszer által kezdeményezett áttelepítés előtt, javasoljuk, hogy a lehető leghamarabb telepítse újra a virtuális gépet. |
| Elérhető és valószínűleg érintett | A virtuális gép fut, azonban egy folyamatban lévő Azure-szolgáltatás leállása miatt előfordulhat, hogy nem csatlakozik hozzá. A rendszer a leállás feloldása után visszaállítja a kapcsolatot. |
| Nem érhető el, és valószínűleg hatással van rá | A virtuális gép állapotát befolyásolhatja egy Azure-szolgáltatás kimaradása. A rendszer automatikusan helyreállítja a virtuális gépet a leállás feloldásakor |
| Ismeretlen és valószínűleg érintett | Jelenleg nem lehet megállapítani a virtuális gép állapotát. Ezt egy olyan folyamatban lévő Azure-szolgáltatás meghibásodása okozhatja, amely hatással lehet erre a virtuális gépre, amely a leállás feloldása után automatikusan helyreáll. |
| Lefoglalt hardver-erőforrások | A hardvererőforrások hozzá lettek rendelve a virtuális géphez, amely hamarosan elérhető lesz |
| Leállítás és felszabadítás | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép leállítása és felszabadítása |
| Frissült a konfiguráció | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép konfigurációjának frissítése |
| A felhasználó újraindította | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép újraindítása. Az újraindítás befejeződése után ismét online állapotba kerül. |
| Újbóli üzembe helyezés másik gazdagépen | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép másik gazdagépen való újbóli üzembe helyezése. Az újratelepítés befejeződése után ismét online állapotba kerül. |
| Leállítva felhasználó által | Egy jogosult felhasználó vagy egy folyamat kérésének megfelelően folyamatban van a virtuális gép leállítása |
| Leállítva felhasználó vagy folyamat által | Egy jogosult felhasználó vagy egy, a virtuális gépen futó folyamat kérésének megfelelően folyamatban van a virtuális gép leállítása |
| Elindítva felhasználó | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép elindítása. Hamarosan online lesz |
| Karbantartás újbóli üzembe helyezése különböző gazdagépeken | Egy tervezett karbantartási tevékenység részeként folyamatban van a virtuális gép másik gazdakiszolgálón való újbóli üzembe helyezése. Az újratelepítés befejeződése után ismét online állapotba kerül. |
| Újraindítás kezdeményezve a gépen belül | A virtuális gép újraindította önmagát. Ezt a virtuális gépen futó operációs rendszer hibája okozhatta, vagy az újraindítást egy jogosult felhasználó, illetve folyamat kérhette. A virtuális gép az újraindítás befejeződése után ismét online állapotba kerül |
| Átméretezi felhasználó szerint | Egy jogosult felhasználó vagy folyamat kérésének megfelelően folyamatban van a virtuális gép méretezése. Az átméretezés befejezése után ismét online állapotba kerül. |
| A gép összeomlott | A virtuális gép újraindította önmagát. Ezt a virtuális gépen futó operációs rendszer hibája okozhatta, vagy az újraindítást egy jogosult felhasználó, illetve folyamat kérhette. A virtuális gép az újraindítás befejeződése után ismét online állapotba kerül |
| Karbantartási újraindítás a gazdagép frissítéséhez | Folyamatban van a karbantartási frissítések telepítése az ezt a virtuális gépet futtató gazdakiszolgálón. Nincs szükség további műveletre az Ön részéről. A karbantartás befejezése után ismét online állapotba kerül. |
| Karbantartás újbóli üzembe helyezése új hardveren | A virtuális gép nem érhető el, mert egy tervezett karbantartási esemény részeként folyamatban van a korszerűbb hardveren való újbóli üzembe helyezése. Nincs szükség további műveletre az Ön részéről. A tervezett karbantartás befejezése után ismét online állapotba kerül. |
| Alacsony prioritású gépi előzik | A virtuális gép működését a rendszer felfüggesztette. Az alacsony prioritású virtuális gép leállítása és visszafoglalása folyamatban van |
| Gazda kiszolgáló újraindítása | A gazdakiszolgáló váratlan újraindítása miatt a virtuális gép nem érhető el. Folyamatban van a gazdakiszolgáló újraindítása. A virtuális gép az újraindítás után újra elérhető lesz. Jelenleg nincs szükség további műveletre |
| Gazdagép meghibásodása miatti újbóli üzembe helyezés | A virtuális gép nem érhető el, és egy gazdakiszolgálón bekövetkezett váratlan hiba miatt folyamatban van az újbóli üzembe helyezése. Az Azure megkezdte az automatikus helyreállítást, és jelenleg folyamatban van a virtuális gép elindítása egy másik gazdagépen. Jelenleg nincs szükség további műveletre |
| Nem várt gazdagép-hiba | A virtuális gép egy gazdakiszolgálón bekövetkezett váratlan hiba miatt nem érhető el. Az Azure megkezdte az automatikus helyreállítást, és jelenleg folyamatban van a gazdakiszolgáló újraindítása. Nincs szükség további műveletre az Ön részéről. A virtuális gép az újraindítás befejeződése után ismét online állapotba kerül |
| Újbóli üzembe helyezés a nem tervezett gazdagépek karbantartása miatt | A virtuális gép nem érhető el, és egy gazdakiszolgálón bekövetkezett váratlan hiba miatt folyamatban van az újbóli üzembe helyezése. Az Azure megkezdte az automatikus helyreállítást, és jelenleg folyamatban van a virtuális gép elindítása egy másik gazdakiszolgálón. Jelenleg nincs szükség további műveletre |
| Kiépítési hiba | A virtuális gép váratlan üzembehelyezési problémák miatt nem érhető el. Váratlan hiba miatt nem sikerült kiépíteni a virtuális gépet |
| Élő áttelepítés | A virtuális gép működése egy memóriamegőrző élő migrálási művelet miatt fel van függesztve. A virtuális gép általában 10 másodpercen belül újra elérhető. Jelenleg nincs szükség további műveletre |
| Élő áttelepítés | A virtuális gép működése egy memóriamegőrző élő migrálási művelet miatt fel van függesztve. A virtuális gép általában 10 másodpercen belül újra elérhető. Jelenleg nincs szükség további műveletre | 
| Távoli lemez leválasztva | A virtuális gép nem érhető el, mert megszakadt a kapcsolata a távoli lemezzel. Dolgozunk a megszakadt lemezkapcsolat helyreállításán. Jelenleg nincs szükség további műveletre |
| Azure-szolgáltatással kapcsolatos probléma | A virtuális gépet az Azure-szolgáltatással kapcsolatos probléma befolyásolja |
| Hálózati probléma | Ezt a virtuális gépet a rendszer egy Top-of-rack hálózati eszköz befolyásolja |
| Nem érhető el | A virtuális gép nem érhető el. Jelenleg nem tudjuk meghatározni az állásidő okát |
| Gazda kiszolgáló újraindítása | A gazdakiszolgáló váratlan újraindítása miatt a virtuális gép nem érhető el. A gazdagép-kiszolgáló váratlan hibája megakadályozza a virtuális gép automatikus helyreállítását |
| Gazdagép meghibásodása miatti újbóli üzembe helyezés | A virtuális gép egy gazdakiszolgálón bekövetkezett váratlan hiba miatt nem érhető el. A gazdagép váratlan hibája megakadályozza a virtuális gép automatikus helyreállítását |
| Nem várt gazdagép-hiba | A virtuális gép egy gazdakiszolgálón bekövetkezett váratlan hiba miatt nem érhető el. A gazdagép váratlan hibája megakadályozza a virtuális gép automatikus helyreállítását |
| Újbóli üzembe helyezés a nem tervezett gazdagépek karbantartása miatt | A virtuális gép egy gazdakiszolgálón bekövetkezett váratlan hiba miatt nem érhető el. A gazdagép váratlan hibája megakadályozza a virtuális gép automatikus helyreállítását |
| Kiépítési hiba | A virtuális gép váratlan üzembehelyezési problémák miatt nem érhető el. Váratlan hiba miatt nem sikerült kiépíteni a virtuális gépet |
| Távoli lemez leválasztva | A virtuális gép nem érhető el, mert megszakadt a kapcsolata a távoli lemezzel. Egy váratlan probléma megakadályozza a virtuális gép automatikus helyreállítását |
| Újraindítás a vendég operációs rendszer frissítése miatt | Az Azure platform újraindítást kezdeményezett egy új vendég operációs rendszer frissítésének alkalmazásához. A virtuális gép az újraindítás befejeződése után ismét online állapotba kerül |