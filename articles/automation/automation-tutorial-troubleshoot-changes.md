---
title: Azure-beli virtuális gépek változásainak hibakeresése Azure Automationban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli virtuális gépek módosításait.
services: automation
ms.subservice: change-inventory-management
keywords: változások, nyomon követés, változások nyomon követése, leltár, automatizálás
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a223ac4296dd160bbdd904e1d3443552d4f49a23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587932"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Azure-beli virtuális gépek változásainak hibáinak megoldása

Ez az oktatóanyag bemutatja, hogyan háríthatja el az Azure-beli virtuális gépek változásaival kapcsolatos hibákat. A Change Tracking és a leltár engedélyezésével nyomon követheti a számítógépeken a szoftverek, a fájlok, a Linux-démonok, a Windows-szolgáltatások és a Windows-beállításkulcsok módosításait.
A konfigurációk változásainak azonosításával megtalálhatja a környezetben felmerülő működési problémákat.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuális gép Change Trackingának és leltározásának engedélyezése
> * Leállított szolgáltatások változásnaplóinak keresése
> * Változáskövetés konfigurálása
> * Tevékenységnapló csatlakozásának engedélyezése
> * Események aktiválása
> * Változások megtekintése
> * Riasztások konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](./index.yml) , amely a megfigyelő és a műveleti runbookok, valamint a figyelő feladatot fogja tárolni.
* A szolgáltatás számára engedélyezni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md) .

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

Először engedélyeznie kell a Change Tracking és a leltárt ehhez az oktatóanyaghoz. Ha korábban engedélyezte a szolgáltatást, ez a lépés nem szükséges.

>[!NOTE]
>Ha a mezők szürkén jelennek meg, egy másik automatizálási funkció is engedélyezve van a virtuális gépen, és ugyanazt a munkaterületet és Automation-fiókot kell használnia.

1. Válassza a **virtuális gépek** lehetőséget, és válasszon ki egy virtuális gépet a listából.
2. A bal oldali menüben válassza a **leltár** elemet a **műveletek** területen. Megnyílik a leltár lap.

    ![Módosítás engedélyezése](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Válassza ki a [log Analytics](../azure-monitor/logs/log-query-overview.md) munkaterületet. Ez a munkaterület az olyan szolgáltatások által generált adatokat gyűjti, mint például a Change Tracking és a leltár. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Válassza ki a használni kívánt Automation-fiókot.

5. Konfigurálja a központi telepítés helyét.

5. Kattintson az **Engedélyezés** gombra a szolgáltatás telepítéséhez a virtuális géphez. 

A telepítés során a virtuális gép a Windows Log Analytics ügynökével és egy [hibrid Runbook-feldolgozóval](automation-hybrid-runbook-worker.md)van kiépítve. A Change Tracking és a leltár engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot.

Ha a szolgáltatás engedélyezve van, a telepített szoftverekre vonatkozó információk és a virtuális gép változásai Azure Monitor naplókra áramlanak.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>A Change Tracking és a leltár használata Azure Monitor naplókban

A Change Tracking és a leltár az Azure Monitor naplókba küldendő naplófájlokat hozza létre. Ha lekérdezések futtatásával szeretne keresni a naplókban, válassza a **log Analytics** elemet a Change Tracking (változások követése) oldal tetején. A Change Tracking-adat a típus alatt tárolódik `ConfigurationChange` .

A következő példa Log Analytics lekérdezés visszaadja az összes leállított Windows-szolgáltatást.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

A naplófájlok Azure Monitor-naplókban való futtatásával és keresésével kapcsolatos további tudnivalókért tekintse meg [Azure monitor naplókat](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-change-tracking"></a>Változáskövetés konfigurálása

A változások követése lehetőséggel kiválaszthatja a fájlok és beállításkulcsok gyűjtését és nyomon követését a virtuális gép változások követése lapjának felső részén található **szerkesztési beállítások** használatával. A munkaterület-konfiguráció lapon nyomon követheti a Windows-beállításkulcsok, Windows-fájlok és Linux-fájlok hozzáadását.

> [!NOTE]
> A Change Tracking és a Inventory is ugyanazokat a gyűjteményi beállításokat használja, és a beállítások a munkaterület szintjén vannak konfigurálva.

### <a name="add-a-windows-registry-key"></a>Windows-beállításkulcs hozzáadása

1. A **Windows-beállításjegyzék** lapon kattintson a **Hozzáadás** gombra. 

1. A Change Tracking Windows beállításjegyzék hozzáadása lapon adja meg a nyomon követni kívánt kulcs adatait, majd kattintson a **Mentés** gombra.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
    |Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
    |Group     | A fájlok logikai csoportosítására szolgáló csoportnév        |
    |Windows-beállításkulcs   | A fájl elérési útja, például: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>Windows-fájl hozzáadása

1. A **Windows-fájlok** lapon kattintson a **Hozzáadás** gombra. 

1. A Change Tracking Windows-fájl hozzáadása lapon adja meg a nyomon követni kívánt fájl vagy könyvtár adatait, majd kattintson a **Mentés** gombra.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
    |Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
    |Group     | A fájlok logikai csoportosítására szolgáló csoportnév        |
    |Elérési út megadása     | A fájl elérési útja, például: „c:\temp\\\*.txt”<br>Környezeti változók is használhatók, például: „%winDir%\System32\\\*.*”         |
    |Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
    |Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **Hamis**.|

### <a name="add-a-linux-file"></a>Linux-fájl hozzáadása

1. A **Linux-fájlok** lapon kattintson a **Hozzáadás** gombra. 

1. A Change Tracking Linux-fájl hozzáadása lapon adja meg a nyomon követni kívánt fájl vagy könyvtár adatait, majd kattintson a **Mentés** gombra.

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
    |Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
    |Group     | A fájlok logikai csoportosítására szolgáló csoportnév        |
    |Elérési út megadása     | A fájl elérési útja, például: „/etc/*.conf”       |
    |Elérési út típusa     | Írja be a követni kívánt elem típusát. A lehetséges értékek a Fájl és a Címtár        |
    |Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
    |Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
    |Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Mellőzés** – Mellőzi a szimbolikus hivatkozásokat, és nem szerepelteti a hivatkozott fájlokat/címtárakat<br>**Követés** – Követi a szimbolikus hivatkozásokat a rekurzió során, és szerepelteti a hivatkozott fájlokat/címtárakat<br>**Kezelés** – Követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom kezelésének módosítását      |
    |Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: Igaz vagy Hamis.|

   > [!NOTE]
   > A **Links** tulajdonság **kezelésének** értéke nem ajánlott. mert a fájltartalom lekérése nem támogatott.

## <a name="enable-activity-log-connection"></a>Tevékenységnapló csatlakozásának engedélyezése

1. A virtuális gép Change Tracking lapján válassza a **Tevékenységnapló-kapcsolat kezelése** lehetőséget. 

2. Az Azure-tevékenység naplója lapon kattintson a **Kapcsolódás** lehetőségre Change Tracking és leltár összekapcsolásához a virtuális gép Azure-beli tevékenységi naplójába.

3. Navigáljon a virtuális gép áttekintés lapjára, és válassza a **Leállítás** lehetőséget a virtuális gép leállításához. 

4. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. 

5. A virtuális gép kiosztása után a **Start** gombra kattintva indítsa újra. A virtuális gép leállítása és elindítása egy eseményt naplóz a tevékenység naplójában. 

## <a name="view-changes"></a>Változások megtekintése

1. Lépjen vissza a Change Tracking (változások követése) lapra, és válassza az oldal alján található **események** lapot. 

2. Egy idő után a Change Tracking Events megjelennek a diagramon és a táblában. A diagram az eltelt idő alatt bekövetkezett változásokat mutatja. A felső grafikonon az Azure Activity log-események láthatók. A sávdiagramok minden sora egy másik, nyomon követhető módosítás típusát jelöli. Ezek a típusok a Linux-démonok, a fájlok, a Windows-beállításkulcsok, a szoftverek és a Windows-szolgáltatások. A Change (módosítás) lapon a megjelenített módosítások részletei láthatók, a legutóbbi módosítás pedig először jelenik meg.

    ![Események megtekintése a portálon](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Figyelje meg, hogy a rendszer több módosítást hajtott végre, beleértve a szolgáltatások és a szoftverek változásait is. Az oldal tetején lévő szűrőkkel a **Változás típusa** vagy időtartomány szerint szűrheti az eredményeket.

    ![A virtuális gép változásainak listája](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Válasszon ki egy **WindowsServices** -változást. Ez a beállítás megnyitja a változás részletei lapot, amely a változás részleteit és a módosítás előtti és utáni értékeket mutatja. Jelen esetben a Szoftvervédelmi szolgáltatást leállt.

    ![Változások részleteinek megtekintése a portálon](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Hasznos, ha követni tudja a végrehajtott módosításokat az Azure Portalon, de még hasznosabbnak bizonyulhat, ha a módosítások (például egy szolgáltatás leállítása) esetén riasztást is küld a rendszer. Hozzunk létre egy riasztást egy leállított szolgáltatáshoz. 

1. A Azure Portal lépjen a **figyelés** elemre. 

2. Válassza a **riasztások** elemet a **megosztott szolgáltatások** területen, majd kattintson az **+ új riasztási szabály** elemre.

3. Kattintson a **kiválasztás** elemre egy erőforrás kiválasztásához. 

4. Az erőforrás kiválasztása lapon válassza ki a **log Analytics** elemet a **szűrés erőforrás típusa** legördülő menüből. 

5. Válassza ki az Log Analytics munkaterületet, majd kattintson a **kész** gombra.

    ![Erőforrás kiválasztása](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Kattintson a **feltétel hozzáadása** elemre.

7. A jel logikai beállítása lap táblázatban válassza az **egyéni naplók keresése** lehetőséget. 

8. Adja meg a következő lekérdezést a keresési lekérdezés szövegmezőben:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    A lekérdezés azon számítógépek nevét adja vissza, amelyeken a W3SVC szolgáltatás le lett állítva a megadott időszakban.

9. A **riasztási logika** területen a **küszöbérték** mezőbe írja be a **0** értéket. Ha elkészült, kattintson a **kész** gombra.

    ![Jellogika konfigurálása](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Válassza az **új létrehozása** a **műveleti csoportok** alatt lehetőséget. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a műveletek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/alerts/action-groups.md) szóló cikkben talál.

11. A **riasztás részletei** területen adja meg a riasztás nevét és leírását. 

12. A **Súlyosság** paraméter értéke legyen **Tájékoztató (Sev 2)**, **Figyelmeztető (Sev 1)** vagy **Kritikus (Sev 0)**.

13. A **Műveletcsoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

14. A **műveletek** mezőben adja meg a művelet nevét, például az **e-mail rendszergazdák** nevet. 

15. A **MŰVELETTÍPUS mezőben** válassza az **e-mail/SMS/leküldés/hang** lehetőséget. 

16. **Részletekért** válassza a **részletek szerkesztése** lehetőséget.

    ![Műveletcsoport hozzáadása](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. Az E-mail/SMS/leküldés/hang panelen adjon meg egy nevet, jelölje be az **e-mail** jelölőnégyzetet, majd adjon meg egy érvényes e-mail-címet. Ha elkészült, kattintson az **OK gombra** a panelen, majd kattintson az **OK** gombra a műveleti csoport hozzáadása lapon.

18. A riasztási e-mail tárgyának testreszabásához válassza a **műveletek testreszabása** lehetőséget. 

19. A **szabály létrehozása** lapon válassza az **e-mail tárgya**, majd a **riasztási szabály létrehozása** lehetőséget. A riasztás figyelmezteti, ha egy frissítés telepítése sikeresen befejeződött, és tájékoztat róla, hogy mely számítógépeket érintett az adott frissítéstelepítés. A következő ábra egy, a W3SVC szolgáltatás leállításakor kapott e-mailt mutat be.

    ![A képernyőfelvételen a W 3 S V C szolgáltatás leállításakor kapott e-mail-értesítés jelenik meg.](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális gép Change Trackingának és leltározásának engedélyezése
> * Leállított szolgáltatások változásnaplóinak keresése
> * Változáskövetés konfigurálása
> * Műveletnapló-kapcsolatok engedélyezése
> * Események aktiválása
> * Változások megtekintése
> * Riasztások konfigurálása

További információ a Change Tracking és a leltár funkció áttekintésében olvasható.

> [!div class="nextstepaction"]
> [A Change Tracking és a leltár áttekintése](change-tracking/overview.md)
