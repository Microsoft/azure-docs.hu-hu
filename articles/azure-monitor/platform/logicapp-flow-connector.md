---
title: Azure Monitor naplók használata Azure Logic Apps és energiagazdálkodási automatizálással
description: Megtudhatja, hogyan használhatja a Azure Logic Apps és az automatizálást az ismételhető folyamatok gyors automatizálásához az Azure Monitor-összekötő használatával.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: fdd3886dda794ff9a91e2c2be6a3d810086d0ed2
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526986"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Azure Monitor a naplók összekötőjét Logic Apps és a teljesítmény automatizálásához
A [Azure Logic apps](../../logic-apps/index.yml) és a [Power automatizálása](https://flow.microsoft.com) lehetővé teszi, hogy több száz műveletet használó automatizált munkafolyamatokat hozzon létre különböző szolgáltatásokhoz. A Azure Monitor naplók összekötővel olyan munkafolyamatokat hozhat létre, amelyek egy Log Analytics-munkaterületről vagy egy Application Insights alkalmazásból kérik le az adatok lekérését Azure Monitor. Ez a cikk az összekötőhöz tartozó műveleteket ismerteti, és útmutatást nyújt a munkafolyamatok ezen az adatain keresztül történő létrehozásához.

Létrehozhat például egy logikai alkalmazást, amellyel Azure Monitor naplózhatja az adatait az Office 365 e-mail értesítésében, létrehozhat egy hibát az Azure DevOps, vagy közzétehet egy Slack-üzenetet.  A munkafolyamatot egy egyszerű vagy egy csatlakoztatott szolgáltatás egyes műveleteiből is elindíthatja, például e-mail vagy Tweet érkezésekor. 

## <a name="connector-limits"></a>Összekötő korlátai
A Azure Monitor naplók összekötője a következő korlátozásokkal rendelkezik:
* Maximális adatméret: 16 MB
* Maximális lekérdezési válasz mérete 100 MB
* Rekordok maximális száma: 500 000
* A lekérdezés maximális időtúllépése 110 másodperc.
* A diagram vizualizációi elérhetővé válhatnak a naplók lapon, és hiányoznak az összekötőből, mivel az összekötő és a naplók lap jelenleg nem ugyanazt a diagramot használja.

Az adat méretétől és a használt lekérdezéstől függően előfordulhat, hogy az összekötő eléri a korlátait, és sikertelen lesz. Ilyen eseteket is használhat, amikor az eseményindító ismétlődését úgy állítja be, hogy gyakrabban fusson, és kevesebb adattal Kérdezzen le. Az adatokat összesítő lekérdezések használatával kevesebb rekordot és oszlopot adhat vissza.

## <a name="actions"></a>Műveletek
A következő táblázat ismerteti a Azure Monitor naplók összekötőhöz tartozó műveleteket. Mindkettő lehetővé teszi, hogy naplózási lekérdezést futtasson Log Analytics munkaterületen vagy Application Insights alkalmazáson. A különbség az, ahogy az adatvisszaadás történik.

> [!NOTE]
> A Azure Monitor naplók összekötő helyettesíti az [azure log Analytics-összekötőt](/connectors/azureloganalytics/) és az [Azure Application Insights-összekötőt](/connectors/applicationinsights/). Ez az összekötő ugyanazokat a funkciókat biztosítja, mint a többi, és az előnyben részesített módszer egy Log Analytics-munkaterületre vagy egy Application Insights alkalmazásra vonatkozó lekérdezés futtatásához.


| Művelet | Leírás |
|:---|:---|
| [Lekérdezési és listázási eredmények futtatása](/connectors/azuremonitorlogs/#run-query-and-list-results) | Az egyes sorok visszaadása saját objektumként. Akkor használja ezt a műveletet, ha az egyes sorokat a munkafolyamat többi részén külön szeretné használni. A műveletet általában az [egyes tevékenységek esetében](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)követi. |
| [Lekérdezés futtatása és eredmények megjelenítése](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Az eredményhalmaz összes sorát adja vissza egyetlen formázott objektumként. Akkor használja ezt a műveletet, ha az eredmény-készletet a munkafolyamat többi részén együtt szeretné használni, például az eredményeket egy levélben küldje el.  |

## <a name="walkthroughs"></a>Útmutatók
Az alábbi oktatóanyagok a Azure Logic Apps Azure Monitor-összekötők használatát szemléltetik. Ugyanezt a példát a Power automatizálva is végrehajthatja, az egyetlen különbség az, hogy hogyan hozza létre a kezdeti munkafolyamatot, és hogyan futtatja a befejezéskor. A munkafolyamat és a műveletek konfigurálása a kettő között azonos. A kezdéshez lásd: [folyamat létrehozása sablonból a Power gyorsbüféban](/power-automate/get-started-logic-template) .


### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Lépjen a Azure Portal **Logic apps** elemre, majd kattintson a **Hozzáadás** gombra. Válassza ki az új logikai alkalmazás tárolására szolgáló **előfizetést**, **erőforráscsoportot** és **régiót** , majd adjon meg egy egyedi nevet. **Log Analytics** beállítás bekapcsolásával információkat gyűjthet a futtatókörnyezet adatairól és eseményeiről az [Azure monitor naplók beállítása és a diagnosztikai adatok összegyűjtése a Azure Logic apps számára](../../logic-apps/monitor-logic-apps-log-analytics.md)című témakörben leírtak szerint. Ez a beállítás nem szükséges a Azure Monitor naplók összekötő használatához.

![Logikai alkalmazás létrehozása](media/logicapp-flow-connector/create-logic-app.png)


Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre. Ha a telepítés befejeződött, kattintson az **erőforrás** megnyitása lehetőségre a **Logic apps Designer** megnyitásához.

### <a name="create-a-trigger-for-the-logic-app"></a>Trigger létrehozása a logikai alkalmazáshoz
Az **Indítás általános eseményindítóval** területen válassza az **Ismétlődés** lehetőséget. Ez egy olyan logikai alkalmazást hoz létre, amely rendszeres időközönként automatikusan fut. A művelet **gyakorisága** mezőben válassza a **nap** és az **intervallum** mezőben az **1** értéket, ha naponta egyszer szeretné futtatni a munkafolyamatot.

![Ismétlődési művelet](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Forgatókönyv: a levelezés vizualizációs eredményei
A következő oktatóanyag azt mutatja be, hogyan hozhat létre olyan logikai alkalmazást, amely egy Azure Monitor log-lekérdezés eredményét e-mailben küldi el. 

### <a name="add-azure-monitor-logs-action"></a>Azure Monitor naplók hozzáadása művelet
Kattintson az **+ új lépés** gombra egy olyan művelet hozzáadásához, amely az ismétlődési művelet után fut. A **művelet kiválasztása** területen írja be az **Azure monitor** parancsot, majd válassza ki **Azure monitor naplókat**.

![Azure Monitor naplók művelete](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Kattintson **Az Azure log Analytics – lekérdezés futtatása elemre, és jelenítse meg az eredményeket**.

![Képernyőkép a Logic app Designer egyik lépéséhez hozzáadott új műveletről. Azure Monitor naplók ki vannak emelve a művelet kiválasztása lehetőség alatt.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor naplók hozzáadása művelet

Válassza ki az **előfizetést** és az **erőforráscsoportot** a log Analytics munkaterülethez. Válassza ki *log Analytics munkaterületet* az **erőforrás típusához** , majd válassza ki a munkaterület nevét az **Erőforrás neve** alatt.

Adja hozzá a következő naplózási lekérdezést a **lekérdezési** ablakhoz.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Válassza az **időtartomány** és a **diagram típusához** tartozó **HTML-táblázat** *beállítása a lekérdezésben* lehetőséget.
   
![Képernyőfelvétel: az új Azure Monitor naplók futtatása művelet, futtatási lekérdezés, eredmények megjelenítése.](media/logicapp-flow-connector/run-query-visualize-action.png)

A rendszer elküldi az e-mailt az aktuális kapcsolatban lévő fiókhoz. A **kapcsolatok módosítása** lehetőségre kattintva megadhat egy másik fiókot.

### <a name="add-email-action"></a>E-mail művelet hozzáadása

Kattintson az **+ új lépés**, majd **a + művelet hozzáadása** lehetőségre. A **művelet kiválasztása** területen írja be az **Outlook** kifejezést, majd válassza az **Office 365 Outlook** elemet.

![Outlook-összekötő kiválasztása](media/logicapp-flow-connector/select-outlook-connector.png)

Válassza **az E-mail küldése (v2)** lehetőséget.

![Office 365 Outlook kiválasztási ablak](media/logicapp-flow-connector/select-mail-action.png)

Kattintson bárhová a **törzs** mezőben egy **dinamikus tartalom** ablak megnyitásához, amely a logikai alkalmazás előző műveleteinek értékeivel jelenik meg. Válassza a **továbbiak** , majd a **törzs** lehetőséget, amely a lekérdezés eredménye a log Analytics műveletben.

![Törzs kijelölése](media/logicapp-flow-connector/select-body.png)

**A címzett ablakban és** **a tárgyban** lévő e-mail-cím tulajdonosának megadása. 

![Levelezési művelet](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>A logikai alkalmazás mentése és tesztelése
A logikai alkalmazás tesztelési futtatásához kattintson a **Mentés** , majd a **Futtatás** parancsra.

![Mentés és Futtatás](media/logicapp-flow-connector/save-run.png)


Ha a logikai alkalmazás befejeződik, tekintse meg a megadott címzett e-mail-címeit.  A következőhöz hasonló törzstel rendelkező e-mailt kell kapnia:

![Minta e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor lévő naplók lekérdezéséről](../log-query/log-query-overview.md).
- További információ a [Logic apps](../../logic-apps/index.yml)
- További információ az [energiagazdálkodás automatizálásáról](https://flow.microsoft.com).
