---
title: Az ArrInspector ellenőrző eszköz
description: A ArrInspector eszköz felhasználói kézikönyve
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97723825"
---
# <a name="the-arrinspector-inspection-tool"></a>Az ArrInspector ellenőrző eszköz

A ArrInspector egy webalapú eszköz, amely egy futó Azure távoli renderelési munkamenet vizsgálatára szolgál. Ennek célja, hogy hibakeresési célokra, a megjelenített jelenet struktúrájának vizsgálatára, a naplófájlok megjelenítésére és az élő teljesítmény figyelésére szolgáljon.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Csatlakozás a ArrInspector

Miután megszerezte az ARR-kiszolgáló hostname (végződés `mixedreality.azure.com` ) értékét, kapcsolódjon a [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)használatával. Ez a függvény létrehoz egy `StartArrInspector.html` eszközt azon az eszközön, amelyen az alkalmazás fut. A ArrInspector elindításához nyissa meg a fájlt egy böngészőben (Edge, Firefox vagy Chrome) egy számítógépen. A fájl csak 24 órán keresztül érvényes.

Ha a meghívásos alkalmazás `ConnectToArrInspectorAsync` már fut egy számítógépen:

* Ha az Unity-integrációt használja, előfordulhat, hogy automatikusan elindul.
* Ellenkező esetben a ( *\\ LocalAppData) \\ [your_app] \\ AC \\ temp felhasználói mappákban* található fájlt fogja megtalálni.

Ha az alkalmazás HoloLens fut:

1. A HoloLens elérése a [Windows-eszköz portál](/windows/mixed-reality/using-the-windows-device-portal)használatával.
1. Nyissa meg a *System > file Explorer programot*.
1. Navigáljon a *felhasználói mappák \\ LocalAppData \\ [your_app] \\ AC \\ temp*.
1. Mentse *StartArrInspector.html* -t a számítógépre.
1. Nyissa meg *StartArrInspector.html* -t a munkamenet ArrInspector betöltéséhez.

## <a name="the-performance-panel"></a>A teljesítmény panel

![A teljesítmény panel](./media/performance-panel.png)

Ez a panel a-kiszolgáló által kiváltott összes teljesítmény értékének gráfját mutatja. Az értékek jelenleg tartalmazzák a keret idejét, az FPS-t, a PROCESSZORt és a memóriahasználat, a memória statisztikáit, például a teljes RAM-használatot, az objektumok számát stb.

A paraméterek egyikének megjelenítéséhez kattintson az **új hozzáadása** gombra, és válassza ki a párbeszédpanelen megjelenő elérhető értékek egyikét. Ez a művelet egy új görgethető diagramot szúr be a panelre, valós időben nyomon követni az értékeket. A jobb oldalon láthatja a *minimális*, a *maximális* és a *jelenlegi* értéket.

A diagramot a tartalom egérrel való húzásával is megadhatja, a függőleges pásztázás azonban csak akkor lehetséges, ha a ArrInspector a szüneteltetett állapotban van.

A CTRL billentyűt lenyomva tartva a nagyítást is lehetővé teszi. A vízszintes nagyítás is szabályozható a csúszka alján.

A függőleges tartomány alapértelmezés szerint a jelenleg megjelenített értékek alapján van kiszámítva, a jobb oldalon pedig a minimális és a maximális értékek láthatók. Ha az értékeket manuálisan állítja be, vagy közvetlenül a szövegmezőbe írja be őket, vagy pásztázás/nagyítás használatával, a gráf ezeket az értékeket fogja használni. Az automatikus függőleges kialakítás visszaállításához kattintson a jobb felső sarokban található ikonra.

![függőleges tartomány](./media/vertical-range.png)

## <a name="the-log-panel"></a>A napló panel

![Napló panel](./media/log-panel.png)

A napló panel a kiszolgáló oldalán létrehozott naplóüzenetek listáját jeleníti meg. Kapcsolat esetén a rendszer akár 200 korábbi naplóbejegyzést is megjelenít, és az újakat fogja nyomtatni.

A lista alapján szűrheti a napló típusát a felül található `[Error/Warning/Info/Debug]` gombokkal.
![Naplók szűrése gombok](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Az időzítési adatrögzítés panel

![Időzítési adatrögzítés](./media/timing-data-capture.png)

Ez a panel az időzítési információk rögzítésére szolgál a kiszolgálóról, és letölti azt. A fájl a [Chrome nyomkövetési JSON-formátumot](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)használja. Az adatellenőrzéshez nyissa meg a Chrome-t az URL-címen, `Chrome://tracing` és húzza a letöltött fájlt a lapra. Az időzítési adatok folyamatos gyűjtése rögzített méretű gyűrűs pufferben történik. Ha kiírja, a rögzítés csak az azonnali múltbeli adatokat tartalmazza, azaz néhány másodpercet néhány percre.

## <a name="the-scene-inspection-panel"></a>A jelenet ellenőrzési panelje

![A jelenet ellenőrzési panelje](./media/scene-inspection-panel.png)

Ez a panel a renderelt jelenet szerkezetét mutatja. Az objektum-hierarchia balra van, a kiválasztott objektum tartalma a jobb oldalon van. A panel írásvédett, és valós időben frissül.

## <a name="the-vm-debug-information-panel"></a>A virtuális gép hibakeresési információi panelje

![Virtuális gép hibakeresési információi panelje](./media/state-debugger-panel.png)

Ez a panel néhány hibakeresési funkciót kínál.

### <a name="restart-service"></a>Szolgáltatás újraindítása

A **szolgáltatás újraindítása gomb újraindítja** azon a virtuális gépen futó futtatókörnyezetet, amelyhez a arrInspector csatlakozik. A csatlakoztatott ügyfelek le lesznek választva, és a arrInspector lapot újra kell tölteni az újraindított szolgáltatáshoz való kapcsolódáshoz.

### <a name="collect-debug-information"></a>Hibakeresési adatok gyűjtése

A **virtuális gép hibakeresési információinak összegyűjtése** gomb egy párbeszédpanelt nyit meg, amely lehetővé teszi az ARR-példány aktiválását, hogy hibakeresési információkat gyűjtsön a virtuális gépen:

![Virtuális gép hibakeresési adatai párbeszédpanel](./media/state-debugger-dialog.png)

A hibakeresési információk segítségével az Azure távoli renderelési csapata elemezheti a futó ARR-példányokban előforduló problémákat. A párbeszédpanelen további részletek is megadhatók, például a probléma ismételt létrehozásához szükséges lépések.

A **gyűjtés megkezdése** gombra kattintás után a párbeszédpanel bezáródik, és megkezdődik a gyűjtési folyamat. A virtuális gépen található információk gyűjtése néhány percet is igénybe vehet.

![A virtuális gép hibakeresési információinak gyűjtése folyamatban van](./media/state-debugger-panel-in-progress.png)

Miután a gyűjtemény elkészült, értesítést fog kapni a ArrInspector ablakban. Ez az értesítés egy azonosítót tartalmaz, amely azonosítja ezt az adott gyűjteményt. Mentse ezt az azonosítót, hogy átadja azt az Azure távoli renderelési csapatának.

![A virtuális gép hibakeresési információinak gyűjtése sikeres](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> A virtuális gép hibakeresési információi nem tölthetők le vagy egyéb módon nem érhetők el. Csak az Azure távoli renderelési csapata férhet hozzá az összegyűjtött adatokhoz. Kapcsolatba kell lépnie velünk, és el kell küldenie a gyűjtemény AZONOSÍTÓját, számunkra pedig meg kell vizsgálnia a megjelenő problémát.

## <a name="pause-mode"></a>Szüneteltetési mód

A jobb felső sarokban egy kapcsoló segítségével szüneteltetheti a panelek élő frissítését. Ez a mód hasznos lehet egy adott állapot alapos vizsgálatához.

![Szüneteltetési mód](./media/pause-mode.png)

Az élő frissítés újbóli engedélyezésekor az összes panel alaphelyzetbe áll.