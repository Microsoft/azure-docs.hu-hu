---
title: Az Azure-beli virtuálisgép-méretezési csoportokkal végzett automatikus skálázás áttekintése
description: Ismerje meg az Azure virtuálisgép-méretezési csoport teljesítmény vagy rögzített ütemterv alapján történő automatikus méretezésének különböző módszereit
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591395"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Az Azure-beli virtuálisgép-méretezési csoportokkal végzett automatikus skálázás áttekintése
Az Azure virtuálisgép-méretezési csoport automatikusan növelheti vagy csökkentheti az alkalmazást futtató virtuálisgép-példányok számát. Ez az automatizált és rugalmas viselkedés csökkenti a felügyeleti terhelést az alkalmazás teljesítményének monitorozásához és optimalizálásához. Olyan szabályokat hozhat létre, amelyek meghatározzák a pozitív felhasználói élmény elfogadható teljesítményét. Ha ezek a meghatározott küszöbértékek teljesülnek, az automatikusan méretezhető szabályok végrehajtják a méretezési csoport kapacitásának módosítását. Az eseményeket úgy is ütemezhet, hogy rögzített időpontokban automatikusan növelje vagy csökkentse a méretezési csoport kapacitását. Ez a cikk áttekintést nyújt arról, hogy mely teljesítménymutatók érhetők el, és hogy milyen műveletek hajthatók végre.


## <a name="benefits-of-autoscale"></a>Az autoscale előnyei
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban.

> [!NOTE]
> A méretezési csoport automatikus példány-javításának használatakor a méretezési csoport példányainak maximális száma 200 lehet. További információ az [automatikus példányok javításáról](./virtual-machine-scale-sets-automatic-instance-repairs.md).

Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. A figyelni kívánt mérőszámokat, például a PROCESSZORt vagy a memóriát, az alkalmazás terhelésének meg kell felelnie egy adott küszöbértéknek, valamint a méretezési csoportba felvenni kívánt virtuálisgép-példányok számának szabályozása.

Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.


## <a name="use-host-based-metrics"></a>Gazdagép-alapú metrikák használata
Létrehozhat olyan autoskálázási szabályokat, amelyek a virtuálisgép-példányokból elérhető beépített gazdagép-metrikákat is tartalmazhatnak. A gazdagép metrikái lehetővé teszi a méretezési csoportokban lévő virtuálisgép-példányok teljesítményének áttekintését anélkül, hogy további ügynököket és adatgyűjteményeket kellene telepítenie vagy konfigurálnia. A mérőszámokat használó autoskálázási szabályok a CPU-használat, a memória-igény vagy a lemez-hozzáférés szempontjából felskálázást és a virtuálisgép-példányok számát is kibővítik.

A gazdagépalapú mérőszámokat használó automatikus méretezési szabályok a következő eszközökkel hozhatók létre:

- [Azure Portalra](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-sablon](tutorial-autoscale-template.md)

A részletesebb teljesítménymutatókat használó automatikus skálázási szabályok létrehozásához [telepítheti és konfigurálhatja az Azure Diagnostics bővítményt](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) a virtuálisgép-példányokon, vagy [konfigurálhatja az alkalmazást az alkalmazás-felismerések használatával](#application-level-metrics-with-app-insights).

A gazdagép-alapú metrikákat, a vendég virtuális gépek metrikáit és az Azure diagnosztikai bővítményt használó automatikus méretezési szabályok, valamint az alkalmazás-felismerések a következő konfigurációs beállításokat használhatják.

### <a name="metric-sources"></a>Metrikus források
Az autoskálázási szabályok a következő források egyikének mérőszámait használhatják:

| Metrikaforrás        | Használati eset                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuális méretezési csoport    | Olyan gazdagép-alapú metrikák esetében, amelyek nem igényelnek további ügynököket a telepítéshez vagy a konfiguráláshoz.                                  |
| Tárfiók      | Az Azure diagnosztikai bővítmény a teljesítménymutatókat az Azure Storage-ba írja, amely az autoskálázási szabályok elindításához használatos. |
| Service Bus-üzenetsor    | Az alkalmazás vagy más összetevők üzeneteket küldhetnek egy Azure Service Bus-várólistán a szabályok elindításához.                   |
| Application Insights | Az alkalmazásba telepített Instrumentation-csomag, amely közvetlenül az alkalmazásból továbbítja a metrikákat.                         |


### <a name="autoscale-rule-criteria"></a>Az autoskálázási szabály feltételei
Az alábbi gazdagép-alapú mérőszámok használhatók az autoskálázási szabályok létrehozásakor. Ha az Azure diagnosztikai bővítményt vagy az alkalmazás-bepillantást használja, meghatározhatja, hogy mely metrikákat szeretné figyelni és használni az autoskálázási szabályokkal.

| Metrika neve               |
|---------------------------|
| Százalékos processzorhasználat            |
| Bejövő hálózat                |
| Kimenő hálózat               |
| Lemez olvasási bájtjai           |
| Lemez írási bájtjai          |
| Lemez olvasási művelete/mp  |
| Lemez írási műveletei/mp |
| Fennmaradó CPU-kreditek     |
| Felhasznált CPU-kreditek      |

Amikor egy adott metrika figyelésére hoz létre autoskálázási szabályokat, a szabályok az alábbi metrikák összesítési műveleteinek egyikét adják meg:

| Összesítés típusa |
|------------------|
| Átlag          |
| Minimum          |
| Maximum          |
| Összesen            |
| Utolsó             |
| Darabszám            |

Az autoskálázási szabályok akkor aktiválódnak, ha a metrikák összevetése a megadott küszöbértékkel történik a következő operátorok egyikével:

| Művelet                 |
|--------------------------|
| Nagyobb, mint             |
| Nagyobb vagy egyenlő |
| Kisebb, mint                |
| Kisebb vagy egyenlő    |
| Egyenlő                 |
| Nem egyenlő             |


### <a name="actions-when-rules-trigger"></a>Műveletek, amikor a szabályok trigger
Egy automatikus skálázási szabály indításakor a méretezési csoport a következő módokon képes automatikusan méretezni:

| Skálázási művelet     | Használati eset                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Szám növelése a következővel   | A létrehozandó virtuálisgép-példányok rögzített száma. Kisebb számú virtuális géppel rendelkező méretezési csoportokban hasznos.                                           |
| Százalék növelésének aránya | A virtuálisgép-példányok százalékos növekedése. A nagyobb méretezési csoportok esetében is jó, ha a rögzített növekedés nem fogja észrevenni a teljesítményt. |
| Növekedés száma   | Hozzon létre annyi virtuálisgép-példányt, amely a kívánt maximális mennyiség eléréséhez szükséges.                                                            |
| Szám csökkentése a következővel   | Az eltávolítandó virtuálisgép-példányok rögzített száma. Kisebb számú virtuális géppel rendelkező méretezési csoportokban hasznos.                                           |
| Százalék csökkentése a következővel: | A virtuálisgép-példányok százalékos csökkenése. A nagyobb méretű méretezési csoportok esetében is jó, ha a rögzített növekedés nem észrevehetően csökkenti az erőforrások felhasználását és költségeit. |
| Csökkentse a darabszámot   | A kívánt minimális összeg eléréséhez távolítsa el a sok virtuálisgép-példányt.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Vendég virtuális gép metrikái az Azure Diagnostics bővítménnyel
Az Azure Diagnostics bővítmény egy VM-példányon belül futó ügynök. Az ügynök figyeli és menti a teljesítménnyel kapcsolatos mérőszámokat az Azure Storage-ba. Ezek a teljesítménymutatók részletesebb információkat tartalmaznak a virtuális gép állapotáról, például a *AverageReadTime* lemezekhez vagy a CPU- *PercentIdleTime* . A virtuális gépek teljesítményének részletesebb megismerése, és nem csupán a CPU-használat vagy a memóriahasználat százalékos aránya alapján hozhat létre autoskálázási szabályokat.

Az Azure Diagnostics bővítmény használatához létre kell hoznia Azure Storage-fiókokat a virtuálisgép-példányokhoz, telepítenie kell az Azure Diagnostics-ügynököt, majd konfigurálnia kell a virtuális gépeket, hogy az adott teljesítményszámlálókat továbbítsák a Storage-fiókba.

További információt az Azure diagnosztikai bővítmény [Linux virtuális gépen](../virtual-machines/extensions/diagnostics-linux.md) vagy [Windows virtuális gépen](../virtual-machines/extensions/diagnostics-windows.md) való engedélyezésével kapcsolatos cikkekben talál.


## <a name="application-level-metrics-with-app-insights"></a>Alkalmazás szintű mérőszámok az App bepillantást
Az alkalmazások teljesítményének megismeréséhez használhatja a Application Insights. Telepít egy kisméretű Instrumentation-csomagot az alkalmazásban, amely figyeli az alkalmazást, és telemetria küld az Azure-nak. Figyelheti a mérőszámokat, például az alkalmazás válaszideje, az oldal betöltési teljesítménye és a munkamenetek száma. Ezek az alkalmazás-metrikák az autoskálázási szabályok részletes és beágyazott szinten történő létrehozásához használhatók, mivel a szabályok a felhasználói élményt befolyásoló, gyakorlatban használható információk alapján indíthatók el.

Az App Insights programmal kapcsolatos további információért lásd: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Ütemezett autoskálázás
Az ütemterveken alapuló autoskálázási szabályokat is létrehozhat. Ezek az ütemterv-alapú szabályok lehetővé teszik a virtuálisgép-példányok számának automatikus méretezését rögzített időpontokban. A teljesítmény-alapú szabályok alapján az alkalmazás teljesítménye hatással lehet az alkalmazásra, mielőtt az új virtuálisgép-példányok aktiválva lesznek. Ha várhatóan ilyen igény mutatkozik meg, a további virtuálisgép-példányok kiépítve és készen állnak a további ügyfelek használatára és az alkalmazásokra.

Az alábbi példák olyan forgatókönyveket mutatnak be, amelyek hasznosak lehetnek a Schedule-alapú autoscale-szabályok használatára:

- A virtuálisgép-példányok számának automatikus méretezése a munkaidő elején, amikor az ügyfél igénye növekszik. A munkanap végén a virtuális gép példányainak száma automatikusan méretezhető, hogy az alkalmazás használata alacsony szinten csökkentse az erőforrás-költségeket.
- Ha egy részleg a hónap vagy a pénzügyi ciklus bizonyos részein erősen használja az alkalmazást, a automatikusan méretezi a virtuálisgép-példányok számát, hogy megfeleljenek a további igényeknek.
- Ha marketing-, előléptetési vagy üdülési értékesítésre van szükség, automatikusan méretezheti a virtuálisgép-példányok számát a várt ügyfél-igény előtt. 


## <a name="next-steps"></a>Következő lépések
Az alábbi eszközök egyikével hozhat létre olyan autoskálázási szabályokat, amelyek gazdagép-alapú metrikákat használnak:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-sablon](tutorial-autoscale-template.md)

Ez az Áttekintés részletesen ismerteti, hogyan használhatók az autoskálázási szabályok horizontális skálázásra, illetve a méretezési csoport virtuálisgép-példányai *számának* növelésére és csökkentésére. A virtuálisgép-példány *méretének* növeléséhez vagy csökkentéséhez függőlegesen is méretezheti a méretezést. További információkért lásd: [vertikális autoskálázás virtuális gépi méretezési csoportokkal](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A virtuálisgép-példányok kezelésével kapcsolatos információkért lásd: [virtuálisgép-méretezési csoportok kezelése Azure PowerShellokkal](./virtual-machine-scale-sets-manage-powershell.md).

Ha meg szeretné tudni, hogyan hozhatók riasztások az automatikusan méretezhető szabályok indításakor, tekintse meg az [e-mailek és webhookok riasztási értesítéseinek Azure monitor-ben történő küldését](../azure-monitor/autoscale/autoscale-webhook-email.md)ismertető témakört. A [naplók használatával e-mail-és webhook](../azure-monitor/alerts/alerts-log-webhook.md)-értesítéseket is küldhet a Azure monitor.
