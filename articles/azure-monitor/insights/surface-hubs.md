---
title: Felszíni hubok figyelése Azure Monitorokkal | Microsoft Docs
description: A Surface Hub megoldással nyomon követheti a felszíni hubok állapotát, és megtudhatja, hogyan használják őket.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 9d81af064b18ca8113599b460f2b9524c7288439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711076"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Felszíni hubok figyelése Azure Monitorekkel az állapotuk nyomon követéséhez

![Surface Hub szimbólum](./media/surface-hubs/surface-hub-symbol.png)

Ez a cikk azt ismerteti, hogyan használható a Azure Monitor Surface Hub megoldás a Microsoft Surface Hub-eszközök figyelésére. A megoldás segítségével nyomon követheti a felszíni hubok állapotát, valamint megtudhatja, hogyan használják őket.

Minden Surface Hub telepítve van a Microsoft monitoring Agent. Az ügynökön keresztül, amelyről a Surface Hub adatait elküldheti a Azure Monitor Log Analytics munkaterületére. A naplófájlokat a rendszer beolvassa a Surface hub-ból, és a rendszer elküldi őket a Azure Monitornak. Problémák, például offline kiszolgálók, a naptár szinkronizálása nem történik meg, vagy ha az eszköz fiókja nem tud bejelentkezni a Skype-ba, a Azure Monitor Surface Hub irányítópultján jelennek meg. Az irányítópulton található adatai segítségével azonosíthatja azokat az eszközöket, amelyek nem futnak, vagy amelyek más problémákba ütköznek, és esetleg az észlelt problémákra vonatkozó javításokat is alkalmazhatnak.

## <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat. A felszíni hubok Azure Monitor-ben való kezeléséhez a következőkre lesz szüksége:

* [Log Analytics előfizetési](https://azure.microsoft.com/pricing/details/log-analytics/) szint, amely a figyelni kívánt eszközök számát fogja támogatni. A Log Analytics díjszabása attól függően változik, hogy hány eszköz regisztrálva van, és mennyi az adatfeldolgozás. Ezt figyelembe kell vennie a Surface Hub bevezetésének megtervezése során.

Ezután adjon hozzá egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy újat. Az egyik módszer használatára vonatkozó részletes utasítások [a Azure Portal log Analytics munkaterületének létrehozása](../logs/quick-create-workspace.md)című témakörben találhatók. Miután konfigurálta a Log Analytics munkaterületet, kétféleképpen regisztrálhat Surface Hub-eszközöket:

* Automatikusan az Intune-on keresztül
* Manuálisan, a Surface Hub eszköz **beállításain** keresztül.

## <a name="set-up-monitoring"></a>Figyelés beállítása
A Surface Hub állapotát és tevékenységeit Azure Monitor használatával figyelheti. A Surface Hub az Intune használatával vagy helyileg is regisztrálhatja a Surface Hub **Beállítások** használatával.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Felszíni hubok összekötése az Intune-nal Azure Monitor
Szüksége lesz a munkaterület-AZONOSÍTÓra és a munkaterület azon kulcsára, amely a felületi hubokat fogja kezelni Log Analytics munkaterületen. Ezeket a Azure Portal munkaterület-beállításaiból kérheti le.

Az Intune egy Microsoft-termék, amely lehetővé teszi, hogy központilag felügyelje a Log Analytics munkaterület konfigurációs beállításait, amelyek egy vagy több eszközön vannak alkalmazva. Kövesse az alábbi lépéseket az eszközök Intune-beli konfigurálásához:

1. Jelentkezzen be az Intune-ba.
2. Navigáljon a **Beállítások**  >  **csatlakoztatott források** elemhez.
3. Hozzon létre vagy szerkesszen egy házirendet a Surface Hub sablon alapján.
4. Navigáljon a szabályzat Azure Operational Insights szakaszához, és adja hozzá a Log Analytics- *munkaterület azonosítóját* és a *munkaterület kulcsát* a szabályzathoz.
5. Mentse a szabályzatot.
6. Társítsa a szabályzatot a megfelelő eszközök csoportjához.

   ![Intune-házirend](./media/surface-hubs/intune.png)

Ezután az Intune szinkronizálja a Log Analytics beállításait a célcsoport eszközeivel, majd regisztrálja őket a Log Analytics munkaterületen.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Felszíni hubok összekapcsolásának Azure Monitor a beállítások alkalmazás használatával
Szüksége lesz a munkaterület-AZONOSÍTÓra és a munkaterület azon kulcsára, amely a felületi hubokat fogja kezelni Log Analytics munkaterületen. Ezeket a Azure Portal Log Analytics munkaterületének beállításaiból kérheti le.

Ha nem használja az Intune-t a környezet kezeléséhez, manuálisan is regisztrálhatja az eszközöket az egyes Surface Hub **beállításain** keresztül:

1. A Surface Hub válassza a **Beállítások lehetőséget**.
2. Ha a rendszer kéri, adja meg az eszköz rendszergazdai hitelesítő adatait.
3. Kattintson **erre az eszközre**, majd a **figyelés** területen kattintson a **log Analytics beállítások konfigurálása** elemre.
4. Válassza a **figyelés engedélyezése** lehetőséget.
5. A Log Analytics beállítások párbeszédpanelen írja be a Log Analytics **munkaterület azonosítóját** , és írja be a **munkaterület kulcsát**.  
   ![A képernyőfelvétel a Microsoft Operations Manager Suite beállításait mutatja be a figyelés engedélyezése jelölőnégyzetet, valamint a munkaterület-AZONOSÍTÓhoz és a munkaterület-kulcshoz tartozó szövegmezőket.](./media/surface-hubs/settings.png)
6. A konfiguráció befejezéséhez kattintson **az OK** gombra.

Megjelenik egy megerősítés, amely azt jelzi, hogy a konfigurációt sikerült-e alkalmazni az eszközön. Ha igen, megjelenik egy üzenet arról, hogy az ügynök sikeresen csatlakozott Azure Monitorhoz. Az eszköz ezután megkezdi az adatok küldését Azure Monitorba, ahol megtekintheti és elvégezheti azt.

## <a name="monitor-surface-hubs"></a>Felszíni hubok figyelése
A felszíni hubok Azure Monitor használatával történő figyelése nagyon hasonlít a többi regisztrált eszköz figyelésére.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Ha a Surface Hub csempére kattint, az eszköz állapota megjelenik.

   ![Surface Hub irányítópult](./media/surface-hubs/surface-hub-dashboard.png)

[Riasztásokat](../alerts/alerts-overview.md) meglévő vagy egyéni naplók alapján is létrehozhat. Az adatAzure Monitor a felületi hubokból gyűjtött adatok alapján megkeresheti az eszközökhöz definiált feltételekkel kapcsolatos problémákat és riasztásokat.

## <a name="next-steps"></a>Következő lépések
* A részletes Surface Hub-információk megtekintéséhez használja [a Azure monitor a naplózási lekérdezéseket](../logs/log-query-overview.md) .
* [Riasztásokat](../alerts/alerts-overview.md) hozhat létre, amelyekkel értesítést küldhet, ha problémák merülnek fel a Surface hubokban.
