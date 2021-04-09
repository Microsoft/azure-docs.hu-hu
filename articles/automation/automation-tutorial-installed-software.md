---
title: Ismerje meg, hogy milyen szoftverek vannak telepítve a virtuális gépeken a Azure Automation | Microsoft Docs
description: Ez a cikk a virtuális gépekre telepített szoftvereket ismerteti a környezetében.
services: automation
keywords: leltár, automatizálás, változások követése
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593774"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>A virtuális gépeken telepített szoftverek felderítése

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Automation Change Tracking és leltár szolgáltatást annak megállapítására, hogy milyen szoftverek vannak telepítve a környezetben. A számítógépeken összegyűjtheti és megtekintheti a szoftverek, a fájlok, a Linux-démonok, a Windows-szolgáltatások és a Windows-beállításkulcsok leltárát. A gépek konfigurációjának nyomon követésével megtalálhatja a környezetben felmerülő működési problémákat, és alaposabban megismerheti a gépek állapotát.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A Change Tracking és az Inventory engedélyezése
> * Azure-beli virtuális gép engedélyezése
> * Nem Azure-beli virtuális gép engedélyezése
> * Telepített szoftverek megtekintése
> * Telepített szoftverek keresése az Inventory-naplókban

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](./index.yml) , amely a megfigyelő és a műveleti runbookok, valamint a figyelő feladatot fogja tárolni.
* A szolgáltatás számára engedélyezni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md) .

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

Az oktatóanyag első lépéseként engedélyeznie kell a Change Tracking és az Inventory megoldást. Ha korábban engedélyezte a szolgáltatást, ez a lépés nem szükséges.

>[!NOTE]
>Ha a mezők szürkén jelennek meg, egy másik automatizálási funkció is engedélyezve van a virtuális gépen, és ugyanazt a munkaterületet és Automation-fiókot kell használnia.

1. Navigáljon az Automation-fiókjához, és válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció** felügyelete alatt.

2. Válassza ki a [log Analytics](../azure-monitor/logs/log-query-overview.md) munkaterületet. Ez a munkaterület az olyan szolgáltatások által generált adatokat gyűjti, mint például a Change Tracking és a leltár. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Válassza ki a használni kívánt Automation-fiókot.

4. Konfigurálja a központi telepítés helyét.

5. Kattintson az **Engedélyezés** gombra a szolgáltatás telepítéséhez a virtuális géphez. 

    ![Leltár-konfiguráció szalagcíme](./media/automation-tutorial-installed-software/enableinventory.png)

A telepítés során a virtuális gép a Windows Log Analytics ügynökével és egy [hibrid Runbook-feldolgozóval](automation-hybrid-runbook-worker.md)van kiépítve. A Change Tracking és a leltár engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot.

Ha a szolgáltatás engedélyezve van, a telepített szoftverekre vonatkozó információk és a virtuális gép változásai Azure Monitor naplókra áramlanak. Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Azure-beli virtuális gép hozzáadása a Change Trackinghoz és a leltárhoz

1. Az Automation-fiókjában navigáljon a **leltár** vagy a **change Tracking** elemre a **konfiguráció** felügyelete alatt.

2. Válassza az **+ Azure-beli virtuális gép hozzáadása** lehetőséget.

3. Válassza ki a virtuális gépet a virtuális gépek listájából. 

4. Kattintson az **Engedélyezés** gombra a Change Tracking és a leltár engedélyezéséhez a virtuális gépen. A Windows Log Analytics ügynöke telepítve van a virtuális gépen, és úgy konfigurálja a virtuális gépet, hogy az Log Analytics munkaterülettel beszéljen. A telepítési művelet eltarthat néhány percig. 

5. Ezen a ponton, ha szükséges, kiválaszthat egy új virtuális gépet a listából, hogy engedélyezze a szolgáltatást.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Nem Azure-beli gép hozzáadása a Change Trackinghoz és a leltárhoz

A nem Azure-beli gépek engedélyezése a következő szolgáltatáshoz:

1. Az operációs rendszertől függően telepítse az [log Analytics-ügynököt a Linux rendszerhez](../azure-monitor/agents/agent-windows.md) vagy log Analytics- [ügynökhöz](automation-linux-hrw-install.md). 

2. Keresse meg az Automation-fiókját, és lépjen a **leltár** vagy a **change Tracking** elemre a **konfiguráció** felügyelete alatt. 

3. Kattintson a **gépek kezelése** lehetőségre. Ekkor megjelenik a Log Analytics munkaterülethez jelentést küldő gépek listája, amelyeken nincs engedélyezve a Change Tracking és a leltár. Válassza ki a megfelelő beállítást a környezetéhez:

    * **Engedélyezés az összes rendelkezésre álló gépen** – ez a beállítás lehetővé teszi, hogy a szolgáltatás jelenleg a log Analytics munkaterületre jelentett összes gépen elérhető legyen.
    * **Engedélyezés az összes elérhető gépen és későbbi gépen** – ez a beállítás lehetővé teszi, hogy a szolgáltatás a log Analytics munkaterületre és a munkaterülethez hozzáadott összes jövőbeli gépre vonatkozóan az összes gépen jelentést küldje.
    * **Engedélyezés a kiválasztott gépeken** – ez a beállítás csak a kiválasztott gépeken engedélyezi a funkciót.

    ![Gépek kezelése](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Telepített szoftverek megtekintése

Ha a Change Tracking és a leltár funkció engedélyezve van, megtekintheti az eredményeket a leltár lapon.

1. Az Automation-fiókban válassza a **leltár** lehetőséget a **konfiguráció kezelése** területen.

2. Az Inventory lapon kattintson a **Szoftver** lapra.

3. Jegyezze fel a talált szoftvereket tartalmazó táblázatot. A szoftverek név és verzió szerint vannak csoportosítva. Az egyes szoftverrekordok részletes információi megtekinthetők a táblázatban. Ilyen részletek például a szoftver neve, verziója, közzétevője, a legutóbbi frissítés időpontja (a legutóbbi frissítési idő a csoport egyik gépének jelentése alapján), valamint azon gépek száma, amelyekre a szoftver telepítve van.

    ![Szoftverleltár](./media/automation-tutorial-installed-software/inventory-software.png)

4. Kattintson egy sorra a szoftverrekord tulajdonságainak, valamint azon gépek neveinek megtekintéséhez, amelyen a szoftver telepítve van.

5. Ha egy adott szoftverre vagy szoftvercsoportra kíváncsi, közvetlenül a szoftverlista fölött található szövegmezőben is kereshet.
A szűrő segítségével a szoftver neve, verziója vagy közzétevője alapján is kereshet. A **contoso** keresése például az összes olyan szoftvert adja vissza, amelynek a neve, közzétevője vagy verziója a **contoso** nevet tartalmazza.

## <a name="search-inventory-logs-for-installed-software"></a>Telepített szoftverek keresése az Inventory-naplókban

A Change Tracking és a leltár az Azure Monitor naplókba küldendő naplófájlokat hozza létre. Ha lekérdezések futtatásával szeretne keresni a naplókban, válassza a **log Analytics** elemet a leltár oldal tetején. A leltári adat a típus alatt tárolódik `ConfigurationData` .

A következő példa Log Analytics lekérdezés a közzétevő Microsoft Corporation leltározási eredményeit adja vissza.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

A naplófájlok Azure Monitor-naplókban való futtatásával és keresésével kapcsolatos további tudnivalókért tekintse meg [Azure monitor naplókat](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Egyetlen gép szoftveres leltározása

Ha egyetlen gépen szeretné megtekinteni a szoftver leltárát, hozzáférhet az Azure-beli virtuális gép erőforrás-oldaláról, vagy Azure Monitor naplók használatával szűrheti le a megfelelő gépre. A következő példa Log Analytics lekérdezés a **ContosoVM** nevű gép szoftverének listáját adja vissza.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan tekintheti meg a szoftverek leltározását:

> [!div class="checklist"]
> * A Change Tracking és az Inventory engedélyezése
> * Azure-beli virtuális gép engedélyezése
> * Nem Azure-beli virtuális gép engedélyezése
> * Telepített szoftverek megtekintése
> * Telepített szoftverek keresése az Inventory-naplókban

További információ a Change Tracking és a leltár funkció áttekintésében olvasható.

> [!div class="nextstepaction"]
> [A Change Tracking és a leltár áttekintése](change-tracking/overview.md)
