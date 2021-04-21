---
title: Oktatóanyag – Linux rendszerű virtuális gépek konfigurációjának kezelése az Azure-ban
description: Ez az oktatóanyag bemutatja, hogyan azonosíthatja a módosításokat, és hogyan kezelheti a csomagfrissítéseket egy Linux rendszerű virtuális gépen
services: virtual-machines
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 16e8cfd9c6b20e3a8d17ba335079b1b6fe8b499f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785018"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>Oktatóanyag: Változások figyelése és Linux rendszerű virtuális gép frissítése az Azure-ban

Az Azure [Change Tracking](../../automation/change-tracking/overview.md) segítségével könnyedén azonosíthatja a változásokat, [Update Management](../../automation/update-management/overview.md) segítségével kezelheti az Azure-beli Linux rendszerű virtuális gépek operációsrendszer-frissítéseit.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Linux-frissítések kezelése
> * A módosítások és a leltár monitorozása

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-vm"></a>Virtuális gép létrehozása

A diagnosztika és a metrikák működés közbeni megtekintéséhez egy virtuális gépre van szükség. Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroupMonitor* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a *~/.ssh/* mappában:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>Szoftverfrissítések kezelése

A frissítéskezelés segítségével kezelheti az Azure-beli linuxos virtuális gépek frissítéseit és javításait.
A virtuális gépről gyorsan felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

### <a name="enable-update-management"></a>Az Update Management engedélyezése

Az Update management engedélyezése a virtuális géphez:

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában válassza a **Frissítéskezelés** elemet. Ekkor megnyílik **Az Update Management engedélyezése** képernyő.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen.
A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A [Log Analytics](../../azure-monitor/logs/log-query-overview.md)-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál.
A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.
A frissítést igénylő virtuális gépeken további műveletek elvégzése érdekében az Azure Automation runbookok futtatását is lehetővé teszi a virtuális gépeken (pl. letöltés és frissítések alkalmazása).

Az ellenőrzési folyamat azt is ellenőrzi, hogy a virtuális gép ki van-e építve a Log Analytics-ügynökkel és az Automation hibrid runbook-feldolgozóval. Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat.

Válassza ki a Log Analytics-munkaterületet és az Automation-fiókot, majd válassza az **Engedélyezés lehetőséget** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

Ha az előkészítés közben az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics-munkaterület](../../azure-monitor/logs/log-query-overview.md)
* [Automation-fiók](../../automation/index.yml)
* Engedélyezett [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) a virtuális gépen

Megnyílik az **Update Management** képernyő. Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd válassza az **Engedélyezés lehetőséget.** Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

![Az Update Management megoldás engedélyezése](./media/tutorial-monitoring/manage-updates-update-enable.png)

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezése után a virtuális gép hiányzó frissítésére vonatkozó információk a naplókba Azure Monitor vissza. Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

### <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő. Miután elkészült a frissítések kiértékelése, a **Hiányzó frissítések** lapon megjelenik a hiányzó frissítések listája.

 ![Frissítés állapotának megtekintése](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy olyan telepítést, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez kattintson a **Frissítéskezelés** képernyő felső részén található **Frissítések központi telepítésének ütemezése** elemre. Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

Új frissítéstelepítés létrehozásához válassza a **Frissítéstelepítés ütemezése lehetőséget.** Megnyílik **az Új frissítéstelepítés** oldal. Adja meg az alábbi táblázatban leírt tulajdonságok értékeit, majd kattintson a **Létrehozás gombra:**

| Tulajdonság | Leírás |
| --- | --- |
| Név |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Linux vagy Windows|
| Frissítenünk kell a csoportokat |Az Azure-beli gépekhez előfizetés, erőforráscsoportok, helyek és címkék kombinációján alapuló lekérdezést definiálhat az Azure-beli virtuális gépek dinamikus csoportjának felépítéséhez, amely az üzemelő példányba tartozik. </br></br>Nem Azure-beli gépek esetén válasszon ki egy meglévő mentett keresést az üzembe helyezésbe foglalni kívánt nem Azure-beli gépek csoportjának kiválasztásához. </br></br>További információ: Dinamikus [csoportok](../../automation/update-management/configure-groups.md)|
| Frissítenünk kell a gépeket |Válasszon ki egy Mentett keresést, importált csoportot, vagy válassza a Gép lehetőséget a legördülő menüből, és válassza ki az egyes gépeket. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható.</br> A számítógépcsoportok naplókban való létrehozásának különböző módszereivel kapcsolatos Azure Monitor lásd: Számítógépcsoportok a [Azure Monitor naplókban](../../azure-monitor/logs/computer-groups.md) |
|Frissítési besorolások|Válassza ki az összes szükséges frissítési besorolást|
|Frissítések be- és kizárása|Ez megnyitja **az Include/Exclude** oldalt. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. A belefoglalás kezelésére vonatkozó további információkért lásd: [Frissítéstelepítés ütemezése](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) |
|Ütemezési beállítások|Válassza ki a kezdési időt, majd válassza az Egyszeri vagy ismétlődő lehetőséget az ismétlődéshez|
| Pre-scripts + Post-scripts|Válassza ki az üzembe helyezés előtt és után futtatni kívánt szkripteket|
| Karbantartási időszak |A frissítésekhez beállított percek száma. Az érték nem lehet 30 percnél rövidebb és nem lehet 6 óránál több |
| Újraindítás-vezérlés| Meghatározza, hogyan kell kezelni az újraindításokat. Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

A frissítéstelepítések programozott módon is létre lehet hozva. Ha meg szeretne ismerkedni azzal, hogyan hozhat létre frissítéstelepítést a REST API szoftverfrissítési [konfigurációk – Létrehozás cikkből.](/rest/api/automation/softwareupdateconfigurations/create) Van egy minta runbook is, amely egy heti frissítéstelepítés létrehozására használható. További információ erről a runbookról: Create a weekly update deployment for one or [more VMs in a resource group (Heti](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)frissítéstelepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoportban).

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ezután visszalép az állapot-irányítópultra.
Ekkor az **Ütemezett** táblázatban már látható az Ön által létrehozott telepítésütemezés.

### <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, az **Update Management** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**. Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.
Ha kiválasztja a befejezett frissítéstelepítést, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/tutorial-monitoring/manage-updates-view-results.png)

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza, amely a következők egyike lehet:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres –** a frissítés sikeres volt
* **Sikertelen** – a frissítés sikertelen volt

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza **Az összes napló** elemet.

Válassza a **Kimenet** csempét azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="monitor-changes-and-inventory"></a>A módosítások és a leltár monitorozása

Összegyűjtheti és megtekintheti a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows beállításkulcsok leltárát. A gépek konfigurációjának nyomon követésével megtalálhatja a környezetben felmerülő működési problémákat, és alaposabban megismerheti a gépek állapotát.

### <a name="enable-change-and-inventory-management"></a>A változás- és leltárkezelés engedélyezése

A változás- és leltárkezelés engedélyezése a virtuális géphez:

1. A képernyő bal oldalán válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listából.
3. A virtuális gép képernyőjének **Műveletek** szakaszában válassza az **Inventory** vagy a **Change Tracking** elemet. Megnyílik **A Change Tracking and Inventory engedélyezése** képernyő.

Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd válassza az **Engedélyezés lehetőséget.** Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia. Bár a megoldások elkülönülve jelennek meg a menüben, ugyanarról a megoldásról van szó. Ha engedélyezi az egyiket, a virtuális gépen mindkét megoldás engedélyezve lesz.

![A változások és a leltár követésének engedélyezése](./media/tutorial-monitoring/manage-inventory-enable.png)

A megoldás engedélyezését követően eltarthat egy ideig, amíg a virtuális gépen elkészül a leltár és megjelennek az adatok.

### <a name="track-changes"></a>Változások követése

A virtuális gépen kattintson a **Change Tracking** elemre a **MŰVELETEK** területen. A megnyíló **Change Tracking** lapon válassza a **Beállítások szerkesztése** elemet. Jelölje be a követni kívánt beállításokat, majd válassza a **+ Hozzáadás** gombot. A Linux esetében elérhető lehetőség a **Linux-fájlok**.

A Change Tracking megoldásról további információt a [Virtuális gép módosításainak hibaelhárítása](../../automation/automation-tutorial-troubleshoot-changes.md) című cikkben talál.

### <a name="view-inventory"></a>Leltár megjelenítése

A virtuális gépen kattintson az **Inventory** elemre a **MŰVELETEK** területen. A **Szoftver** lapon egy táblázat formátumú lista jeleníti meg a felderített szoftvereket. Az egyes szoftverrekordok részletes információi megtekinthetők a táblázatban. Ilyen információ például a szoftver neve, verziója, közzétevője és a legutóbbi frissítés ideje.

![Leltár megjelenítése](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Tevékenységnaplók és változások monitorozása

A virtuális gép **Change Tracking** lapján válassza a **Tevékenységnapló-kapcsolat kezelése** lehetőséget. Ez a feladat megnyitja az **Azure-tevékenységnapló** lapot. A **Csatlakozás** gombra kattintva csatlakoztathatja a Change Tracking megoldást a virtuális gép Azure-tevékenységnaplójához.

A beállítás engedélyezése után lépjen a virtuális gép **Áttekintés** lapjára, majd kattintson a **Leállítás** gombra a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A felszabadított virtuális gépeket az **Indítás** gombra kattintva indíthatja újra.

A virtuális gép leállítása és elindítása egy-egy eseményt rögzít a tevékenységnaplóban. Lépjen vissza a **Change Tracking** oldalra. Az oldal alján válassza az **Események** lapot. Az események idővel megjelennek a diagramban és a táblázatban. Az egyes események kijelölésével megjelenítheti a hozzájuk tartozó részletes adatokat.

![Változások megtekintése a tevékenységnaplóban](./media/tutorial-monitoring/manage-activitylog-view-results.png)

A diagram az eltelt idő alatt bekövetkezett változásokat mutatja. A Tevékenységnapló-kapcsolat hozzáadása után a felül látható vonaldiagramon az Azure tevékenységnapló eseményei jelennek meg. A sávdiagramok minden egyes sora más-más követhető változástípust jelenít meg. Ezek a típusok lehetnek Linux-démonok, fájlok és szoftverek. A Változások lapon jelennek meg a vizualizációban látható változások részletei a változás bekövetkezte szerinti fordított időrendben (vagyis a legutóbbi van legelöl).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a virtuális gép Change Tracking és Update Management konfigurálta és áttekintett. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és virtuális gép létrehozása
> * Linux-frissítések kezelése
> * A módosítások és a leltár monitorozása

A következő oktatóanyag a virtuális gép monitorozását ismertető oktatóanyagot követi.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)