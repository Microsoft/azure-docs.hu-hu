---
title: Oktatóanyag – a Windows rendszerű virtuális gépek konfigurációjának kezelése az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan azonosíthatja a módosításokat, és hogyan kezelheti a csomagok frissítéseit egy Windows rendszerű virtuális gépen
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a478d9c620219a768983570897715f924565a80f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594306"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Oktatóanyag: változások figyelése és Windowsos virtuális gépek frissítése az Azure-ban

Az Azure [change Tracking](../../automation/change-tracking/overview.md) és [Update Management](../../automation/update-management/overview.md)segítségével könnyedén azonosíthatja a Windows rendszerű virtuális gépek változásait az Azure-ban, és kezelheti az operációs rendszer frissítéseit ezekhez a virtuális gépekhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Windows-frissítések kezelése.
> * A változások és a leltár figyelése.

## <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

A Azure Cloud Shell egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. Az Azure-fiókkal való használathoz a közös Azure-eszközök előre telepítve és konfigurálva vannak.

Ha meg szeretné nyitni a Cloud Shellban lévő összes kódot, egyszerűen válassza ki a **kipróbálás** lehetőséget a kód blokk jobb felső sarkában.

A Cloud Shell egy külön böngésző lapon is megnyithatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Válassza a **Másolás** elemet a kódok másolásához, illessze be őket a Cloud Shell lapra, és válassza az ENTER billentyűt a kód futtatásához.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ebben az oktatóanyagban az Azure-monitorozás és a frissítéskezelés konfigurálásához egy Azure-beli windowsos virtuális gép szükséges.

Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Ezután hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). Az alábbi példa egy nevű virtuális gépet hoz létre `myVM` a `East US` helyen. Ha még nem léteznek, az erőforráscsoport és a `myResourceGroupMonitor` támogató hálózati erőforrások jönnek létre:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.

## <a name="manage-windows-updates"></a>Windows-frissítések kezelése

A Update Management segítségével kezelheti az Azure Windows rendszerű virtuális gépek frissítéseit és javításait. Közvetlenül a virtuális gépről gyorsan elvégezheti a következőket:

- Az elérhető frissítések állapotának felmérése.
- A szükséges frissítések telepítésének ütemezése.
- Tekintse át a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések alkalmazása a virtuális gépre.

A díjszabással kapcsolatos információkért lásd: [az Automation díjszabása az Update Management](https://azure.microsoft.com/pricing/details/automation/)szolgáltatáshoz.

### <a name="enable-update-management"></a>Az Update Management engedélyezése

A virtuális gép Update Managementának engedélyezése:

1. Az ablak bal szélső oldalán válassza a **virtuális gépek** lehetőséget.
1. Válasszon egy virtuális gépet a listából.
1. A virtuális gép ablakának **műveletek** paneljén válassza a **frissítés kezelése** lehetőséget.
1. Megnyílik a **Update Management engedélyezése** ablak.

A rendszer ellenőrzi, hogy engedélyezve van-e a Update Management ezen a virtuális gépen. Az ellenőrzés magában foglalja a Log Analytics munkaterület, a csatolt Automation-fiókok, valamint az, hogy a megoldás szerepel-e a munkaterületen.

[Log Analytics](../../azure-monitor/logs/log-query-overview.md) munkaterületet használ a szolgáltatások és szolgáltatások, például a Update Management által generált adatok gyűjtéséhez. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

Ha további műveleteket szeretne végrehajtani a frissítéseket igénylő virtuális gépeken, Azure Automation használatával runbookok futtathat a virtuális gépeken. Ilyen műveletek közé tartoznak a frissítések letöltése vagy alkalmazása.

Az érvényesítési folyamat azt is ellenőrzi, hogy a virtuális gép a Microsoft monitoring Agent (MMA) és az Automation Hybrid Runbook Worker használatával van-e kiépítve. Az ügynököt a virtuális géppel folytatott kommunikációra és a frissítési állapottal kapcsolatos információk beszerzésére használja.

Az **Update Management engedélyezése** ablakban válassza ki a log Analytics munkaterületet és az Automation-fiókot, majd válassza az **Engedélyezés** lehetőséget. A megoldás engedélyezése akár 15 percet is igénybe vehet.

A rendszer az előkészítés során hiányzó alábbi előfeltételek bármelyikét automatikusan hozzáadja:

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) munkaterület
* [Automatizálás](../../automation/index.yml)
* [Hibrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md), amely engedélyezve van a virtuális gépen

A megoldás engedélyezését követően megnyílik az **Update Management** ablak. Konfigurálja a használni kívánt helyet, Log Analytics munkaterületet és Automation-fiókot, majd válassza az **Engedélyezés** lehetőséget. Ha ezek a beállítások szürkén jelennek meg, egy másik automatizálási megoldás van engedélyezve a virtuális gépen, és a megoldás munkaterület-és Automation-fiókját kell használnia.

![Update Management megoldás engedélyezése](./media/tutorial-monitoring/manageupdates-update-enable.png)

Az Update Management-megoldás akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos információk Azure Monitor naplókra áramlanak. 30 perctől akár 6 óráig is eltarthat, amíg az adatok elérhetővé válnak az elemzéshez.

### <a name="view-an-update-assessment"></a>Frissítésfelmérés megtekintése

Update Management engedélyezése után megjelenik az **Update Management** ablak. A frissítések értékelésének befejezése után a hiányzó **frissítések** lapon megjelenik a hiányzó frissítések listája.

 ![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy mely frissítési típusok szerepeljenek a központi telepítésben. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

A virtuális gép új központi telepítésének megadásához válassza a frissítés **központi telepítésének ütemezett** frissítése lehetőséget az **Update Management** ablak tetején. Az **új frissítés központi telepítése** ablakban a következő információkat kell megadnia:

| Beállítás | Leírás |
| --- | --- |
| **Név** |Adjon meg egy egyedi nevet a frissítés központi telepítésének azonosításához. |
|**Operációs rendszer**| Válassza a **Linux** vagy a **Windows** lehetőséget.|
| **Frissítendő csoportok** |Az Azure-ban üzemeltetett virtuális gépek esetében az előfizetés, az erőforráscsoportok, a helyszínek és a címkék kombinációján alapuló lekérdezést adhat meg. Ez a lekérdezés az Azure által üzemeltetett virtuális gépek dinamikus csoportjait hozza létre, amelyek beletartoznak a központi telepítésbe. </br></br>Az Azure-ban nem üzemeltetett virtuális gépek esetén válasszon ki egy meglévő mentett keresést. Ezzel a kereséssel kiválaszthatja a központi telepítésbe felvenni kívánt virtuális gépek egy csoportját. </br></br> További információ: [dinamikus csoportok](../../automation/update-management/configure-groups.md).|
| **Frissítendő gépek** |Válassza a **mentett keresés**, **importált csoport** vagy **gépek** lehetőséget.<br/><br/>Ha a **gépek** lehetőséget választja, a legördülő listából választhat egyes gépeket. Az egyes gépek készültsége a tábla **frissítési ügynök készültségi** oszlopában látható.</br></br> A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../../azure-monitor/logs/computer-groups.md) |
|**Frissítési besorolások**|Válassza ki az összes szükséges frissítési besorolást.|
|**Frissítések belefoglalása/kizárása**|Válassza ezt a lehetőséget a **Belefoglalás/kizárás** ablaktábla megnyitásához. A szerepeltetni kívánt frissítések és a kizárandó módosítások külön lapokon találhatók. További információ a bevonások kezeléséről: a [frissítések központi telepítésének ütemezett időpontja](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment). |
|**Ütemezési beállítások**|Válassza ki a kezdéshez szükséges időt, és válassza az **egyszer** vagy az **ismétlődő** lehetőséget.|
| **Parancsfájlok előtti + parancsfájlok utáni**|Válassza ki az üzembe helyezés előtt és után futtatandó szkripteket.|
| **Karbantartási időszak** | Adja meg a frissítések számára beállított percek számát. Az érvényes értékek 30 és 360 perc közé esnek. |
| **Vezérlő újraindítása**| Válassza ki az újraindítások kezelését. Az elérhető lehetőségek a következők:<ul><li>**Újraindítás szükség esetén**</li><li>**Mindig induljon újra**</li><li>**Soha ne induljon újra**</li><li>**Csak újraindítás**</li></ul>**Ha szükséges, indítsa újra** az alapértelmezett beállítást. Ha csak az **Újraindítás** lehetőséget választja, a frissítések nincsenek telepítve.|

Miután befejezte az ütemterv konfigurálását, kattintson a **Létrehozás** gombra az állapot-irányítópultra való visszatéréshez. Az **ütemezett** táblázat a létrehozott központi telepítési ütemezést jeleníti meg.

A frissítési központi telepítéseket programozott módon is létrehozhatja. Ha meg szeretné tudni, hogyan hozhat létre frissítési központi telepítést a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört. Létezik egy minta-runbook is, amely használatával hetente frissítheti az üzembe helyezést. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Az ütemezett telepítés megkezdése után a telepítés állapota a **frissítés kezelése** ablak **frissítés központi telepítések** lapján tekinthető meg.

Ha az üzemelő példány jelenleg fut, az állapota "folyamatban" állapotra mutat. A sikeres befejezést követően az állapot "sikeres" állapotúra változik. Ha azonban az üzemelő példányban lévő bármelyik frissítés meghiúsul, az állapot "részlegesen meghiúsult".

A telepítéshez tartozó irányítópult megtekintéséhez válassza ki a befejezett frissítés központi telepítését.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manageupdates-view-results.png)

A **frissítés eredményei** csempe a virtuális gépen lévő frissítések és a telepítési eredmények összesített számának összegzését jeleníti meg. A jobb oldali táblázat az egyes frissítések részletes részletezését és a telepítés eredményét jeleníti meg. Minden eredmény az alábbi értékek egyikével rendelkezik:

* **Nem történt kísérlet**: a frissítés nincs telepítve. Nem áll rendelkezésre elegendő idő a karbantartási időszak meghatározott időtartama alapján.
* **Sikeres**: a frissítés sikeresen befejeződött.
* **Sikertelen**: a frissítés nem sikerült.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza **Az összes napló** elemet.

A **kimeneti** csempére kattintva megtekintheti annak a runbook a feladatait, amelynek feladata a frissítés központi telepítésének kezelése a cél virtuális gépen.

Válassza a **hibák** lehetőséget az üzembe helyezési hibák részletes adatainak megtekintéséhez.

## <a name="monitor-changes-and-inventory"></a>A módosítások és a leltár monitorozása

A számítógépeken a szoftver, a fájlok, a Linux-démonok, a Windows-szolgáltatások és a Windows-beállításkulcsok leltározása is összegyűjthető és megtekinthető. A gépek konfigurációjának nyomon követésével megtalálhatja a környezet működési problémáit, és jobban megismerheti a gépek állapotát.

### <a name="enable-change-and-inventory-management"></a>A változások és a leltár kezelésének engedélyezése

A virtuális gép módosításának és leltárának kezeléséhez tegye a következőket:

1. Az ablak bal szélső oldalán válassza a **virtuális gépek** lehetőséget.
1. Válasszon egy virtuális gépet a listából.
1. A virtuális gép ablakának **műveletek** területén válassza a **leltár** vagy a **változások követése** lehetőséget.
1. Megnyílik az **change Tracking és leltár engedélyezése** panel.

Konfigurálja a használni kívánt helyet, Log Analytics munkaterületet és Automation-fiókot, majd válassza az **Engedélyezés** lehetőséget. Ha a beállítások szürkén jelennek meg, az Automation-megoldás már engedélyezve van a virtuális gépen. Ebben az esetben a már engedélyezett munkaterület-és Automation-fiókot kell használni.

Annak ellenére, hogy a megoldások külön jelennek meg a menüben, ugyanaz a megoldás. Ha engedélyezi az egyiket, a virtuális gépen mindkét megoldás engedélyezve lesz.

![A változások és a leltár követésének engedélyezése](./media/tutorial-monitoring/manage-inventory-enable.png)

A megoldás engedélyezését követően eltarthat egy ideig, amíg a rendszer adatokat gyűjt a virtuális gépen az adatok megjelenése előtt.

### <a name="track-changes"></a>Változások követése

A virtuális gépen a **műveletek** területen válassza a **change Tracking** lehetőséget, majd válassza a **beállítások szerkesztése** lehetőséget. Megnyílik a **change Tracking** panel. Jelölje be a követni kívánt beállításokat, majd válassza a **+ Hozzáadás** gombot.

A Windows elérhető beállítási lehetőségei a következők:

* Windows-beállításjegyzék
* Windows-fájlok

A Change Tracking kapcsolatos részletes információkért lásd: [a virtuális gépek változásainak megoldása](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Leltár megjelenítése

A virtuális gépen válassza a **leltár** lehetőséget a **műveletek** területen. A **szoftver** lapon található egy táblázat, amely a talált szoftvereket jeleníti meg. Az egyes szoftvercsomagok magas szintű adatai megjelennek a táblázatban. Ezek az adatok tartalmazzák a szoftver nevét, verzióját, közzétevőjét és utolsó frissítésének időpontját.

![Leltár megjelenítése](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Tevékenységek naplóinak és változásainak figyelése

A virtuális gépen a **change Tracking (változások követése** ) ablakban válassza a **műveletnapló-kapcsolatok kezelése** lehetőséget az **Azure-tevékenység napló** paneljének megnyitásához. Válassza a **Kapcsolódás** lehetőséget a virtuális gép Azure-beli tevékenységi naplójához való kapcsolódáshoz Change Trackingához.

Change Tracking engedélyezése után lépjen a virtuális gép **Áttekintés** paneljére, és válassza a **Leállítás** lehetőséget a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A virtuális gép kiosztása után a virtuális gép újraindításához kattintson a **Start** gombra.

Egy virtuális gép leállítása és újraindítása naplóz egy eseményt a tevékenység naplójában. Lépjen vissza a **change Tracking (változások nyomon követése** ) panelre, és válassza a panel alján található **események** lapot. Egy idő után az események megjelennek a diagramon és a táblában. Az esemény részletes adatainak megtekintéséhez kiválaszthatja az egyes eseményeket.

![Változások megtekintése a tevékenységnaplóban](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Az előző diagramon az idő múlásával történt változások láthatók. Miután hozzáadta az Azure-beli műveletnapló-kapcsolatokat, a felül található gráf megjeleníti az Azure-tevékenységek naplózási eseményeit.

A sávdiagramok minden sora egy másik, nyomon követhető módosítás típusát jelöli. Ezek a típusok a Linux-démonok, a fájlok, a Windows-beállításkulcsok, a szoftverek és a Windows-szolgáltatások. A **módosítás lap a változás** részleteit jeleníti meg. A változások az egyes előfordulások sorrendjében jelennek meg, és a legutóbbi változás látható először.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálta és ellenőrizte Change Tracking és Update Management a virtuális géphez. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot és egy virtuális gépet.
> * Windows-frissítések kezelése.
> * A változások és a leltár figyelése.

Folytassa a következő oktatóanyaggal, amely a virtuális gép monitorozását ismerteti.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitor.md)
