---
title: B2B-üzenetek figyelése Azure Monitor használatával
description: Az AS2-, X12-és EDIFACT-üzenetek hibakeresése Azure Monitor naplók beállításával és diagnosztikai adatok összegyűjtésével Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100596861"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Azure Monitor-naplók beállítása és diagnosztikai adatok begyűjtése a B2B-üzenetekre vonatkozóan az Azure Logic Appsben

Miután beállította a VÁLLALATKÖZI kommunikációt a kereskedelmi partnerek között az integrációs fiókban, ezek a partnerek olyan protokollok használatával válthatnak be üzeneteket, mint az AS2, a X12 és a EDIFACT. Annak ellenőrzését, hogy a kommunikáció a várt módon működik-e, beállíthat [Azure monitor naplókat](../azure-monitor/logs/data-platform-logs.md) az integrációs fiókjához. [Azure monitor](../azure-monitor/overview.md) segít a felhő és a helyszíni környezetek monitorozásában, így könnyebben megtarthatja a rendelkezésre állást és a teljesítményt. Azure Monitor naplók használatával rögzítheti és tárolhatja a futtatókörnyezet adatait és eseményeit, például az események aktiválását, az események futtatását és a műveleti eseményeket egy [log Analytics munkaterületen](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Az üzenetek esetében a naplózás a következő információkat is gyűjti:

* Üzenetek száma és állapota
* Nyugták állapota
* Az üzenetek és a nyugták közötti korreláció
* Hibák részletes leírása

A Azure Monitor lehetővé teszi a [naplók lekérdezését](../azure-monitor/logs/log-query-overview.md) , így megkeresheti és áttekintheti az információkat. [A diagnosztikai adatait más Azure-szolgáltatásokkal is használhatja](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), mint például az Azure Storage és az Azure Event Hubs.

Az integrációs fiók naplózásának beállításához [telepítse a Logic apps B2B megoldást](#install-b2b-solution) a Azure Portal. Ez a megoldás összesített információt biztosít a B2B-üzenet eseményeihez. Ezután a naplózás engedélyezéséhez és a lekérdezések létrehozásához hozzon létre [Azure monitor naplókat](#set-up-resource-logs).

Ez a cikk bemutatja, hogyan engedélyezheti az integrációs fiók Azure Monitor naplózását.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ha nem rendelkezik Log Analytics munkaterülettel, megtudhatja, [hogyan hozhat létre log Analytics-munkaterületet](../azure-monitor/logs/quick-create-workspace.md).

* Azure Monitor naplózással beállított logikai alkalmazás, amely egy Log Analytics munkaterületre küldi ezt az információt. Ismerje meg [, hogyan állíthat be Azure monitor naplókat a logikai alkalmazáshoz](../logic-apps/monitor-logic-apps.md).

* Egy integrációs fiók, amely a logikai alkalmazáshoz van társítva. Ismerje meg [, hogyan kapcsolhat integrációs fiókját a logikai alkalmazáshoz](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B megoldás telepítése

Mielőtt Azure Monitor naplók nyomon követhetik a logikai alkalmazás B2B-üzeneteit, adja hozzá a **Logic apps B2B** megoldást a log Analytics munkaterülethez.

1. A [Azure Portal](https://portal.azure.com)keresési mezőjébe írja be a kifejezést `log analytics workspaces` , majd válassza **log Analytics munkaterületek** lehetőséget.

   !["Log Analytics munkaterületek" kiválasztása](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. A **log Analytics munkaterületek** területen válassza ki a munkaterületet.

   ![Log Analytics munkaterület kiválasztása](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Az Áttekintés ablaktáblán, az **első lépések a log Analytics** a  >  **figyelési megoldások konfigurálása** területen válassza a **megoldások megtekintése** lehetőséget.

   ![Az Áttekintés panelen válassza a "megoldások megtekintése" lehetőséget.](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Az Áttekintés panelen válassza a **Hozzáadás** lehetőséget.

   ![Az Áttekintés panelen új megoldás hozzáadása](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. A **piactér** megnyitása után a keresőmezőbe írja be a kifejezést `logic apps b2b` , majd válassza a **Logic apps B2B** lehetőséget.

   ![A piactéren válassza a "Logic Apps kezelés" lehetőséget.](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. A megoldás leírása panelen válassza a **Létrehozás** lehetőséget.

   ![Válassza a létrehozás lehetőséget a "Logic Apps B2B" megoldás hozzáadásához](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Tekintse át és erősítse meg a Log Analytics munkaterületet, ahol telepíteni szeretné a megoldást, majd válassza a **Létrehozás** újra lehetőséget.

   ![Válassza a "létrehozás" lehetőséget a "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Miután az Azure üzembe helyezte a megoldást az Log Analytics munkaterületet tartalmazó Azure-erőforráscsoport számára, a megoldás megjelenik a munkaterület összefoglalás paneljén. A B2B-üzenetek feldolgozásakor a rendszer frissíti az üzenetek darabszámát ezen a panelen.

   ![Munkaterület összegzése panel](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor naplók beállítása

Azure Monitor naplózást közvetlenül az integrációs fiókjából is engedélyezheti.

1. A [Azure Portalban](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókját.

   ![Integrációs fiók megkeresése és kiválasztása](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Az integrációs fiók menüjének **figyelés** területén válassza a **diagnosztikai beállítások** elemet. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.

   ![A "Figyelés" területen válassza a "diagnosztikai beállítások" elemet.](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. A beállítás létrehozásához kövesse az alábbi lépéseket:

   1. Adja meg a beállítás nevét.

   1. Válassza **a küldés log Analytics** lehetőséget.

   1. Az **előfizetés** mezőben válassza ki a log Analytics munkaterülethez társított Azure-előfizetést.

   1. **Log Analytics munkaterületen** válassza ki a használni kívánt munkaterületet.

   1. A **napló** területen válassza ki a **IntegrationAccountTrackingEvents** kategóriát, amely megadja a rögzíteni kívánt esemény kategóriáját.

   1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   Például: 

   ![Azure Monitor naplók beállítása a diagnosztikai adatok gyűjtéséhez](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Üzenet állapotának megtekintése

A logikai alkalmazás futtatása után megtekintheti a Log Analytics munkaterületen lévő üzenetekre vonatkozó állapotot és információkat.

1. A [Azure Portal](https://portal.azure.com) keresőmezőben keresse meg és nyissa meg log Analytics munkaterületét.

1. A munkaterület menüjében válassza a **munkaterület összefoglalása**  >  **Logic apps B2B** elemet.

   ![Munkaterület összegzése panel](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Ha a Logic Apps B2B csempe nem jeleníti meg azonnal az eredményeket a Futtatás után, próbálja meg a **frissítés** lehetőséget választani, vagy várjon egy rövid ideig, mielőtt újra próbálkozik.

   Alapértelmezés szerint a **Logic apps B2B** csempe egyetlen nap alapján jeleníti meg az értékeket. Az adathatókör más intervallumra való módosításához válassza ki a hatókör-vezérlőelemet az oldal tetején:

   ![Változási időköz](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Az üzenet állapota irányítópult megjelenése után megtekintheti az adott üzenet típusát, amely egyetlen nap alapján jeleníti meg az adatokat. Válassza ki az **AS2**, a **X12** vagy a **EDIFACT** csempét.

   ![Üzenetek állapotának megtekintése](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Megjelenik az üzenetek listája a kiválasztott csempéhez. Például az AS2-üzenetek listája a következőhöz hasonló lehet:

   ![Az AS2-üzenetek állapota és adatai](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Az egyes Üzenetbeállítások tulajdonságaival kapcsolatos további tudnivalókért tekintse meg a következő üzenet-tulajdonságok leírását:

   * [AS2-üzenet tulajdonságai](#as2-message-properties)
   * [X12 üzenet tulajdonságai](#x12-message-properties)
   * [EDIFACT üzenet tulajdonságai](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Az AS2-, X12-és EDIFACT-üzenetek tulajdonságainak leírása és formátuma

Minden üzenet típusa esetén itt láthatók a letöltött üzenetek tulajdonságainak leírása és formátuma.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2-üzenet tulajdonságainak leírása

Az alábbiakban láthatók az egyes AS2-üzenetek tulajdonságainak leírása.

| Tulajdonság | Leírás |
|----------|-------------|
| **Küldő** | A **fogadási beállításokban** megadott vendég partner vagy az AS2-szerződés **küldési beállításai** között megadott gazda partner |
| **Fogadó** | A **fogadási beállításokban** megadott gazda partner vagy az AS2-szerződés **küldési beállításai** között megadott vendég partner |
| **Logikai alkalmazás** | A logikai alkalmazás, amelyben az AS2-műveletek vannak beállítva |
| **Állapot** | Az AS2-üzenet állapota <br>Sikeres = kapott vagy elküldött egy érvényes AS2-üzenetet. Nincs beállítva MDN. <br>Sikeres = kapott vagy elküldött egy érvényes AS2-üzenetet. A MDN beállítása és fogadása, vagy a MDN küldése történik. <br>Sikertelen = érvénytelen AS2-üzenetet kapott. Nincs beállítva MDN. <br>Függőben = fogadott vagy küldött egy érvényes AS2-üzenetet. A MDN beállítása megtörténik, és a rendszer MDN vár. |
| **NYUGTA** | A MDN üzenet állapota <br>Elfogadva = pozitív MDN kapott vagy küldött. <br>Függőben = várakozás egy MDN fogadására vagy elküldésére. <br>Elutasítva = fogadott vagy negatív MDN küldött. <br>Nem kötelező = a MDN nincs beállítva a szerződésben. |
| **Irány** | Az AS2-üzenet iránya |
| **Követési azonosító** | A logikai alkalmazásban lévő összes eseményindítót és műveletet összekapcsoló azonosító |
| **Üzenetazonosító** | Az AS2-üzenet azonosítója az AS2-üzenetek fejlécében |
| **Időbélyeg** | Az az idő, amikor az AS2-művelet feldolgozta az üzenetet |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12-üzenet tulajdonságainak leírása

Az alábbiakban az egyes X12-üzenetekhez tartozó tulajdonságok leírását olvashatja.

| Tulajdonság | Leírás |
|----------|-------------|
| **Küldő** | A **fogadási beállításokban** megadott vendég partner vagy egy X12-szerződés **küldési beállításaiban** megadott gazda partner |
| **Fogadó** | A **fogadási beállításokban** megadott gazda partner vagy egy X12-szerződés **küldési beállításaiban** megadott vendég partner |
| **Logikai alkalmazás** | Az a logikai alkalmazás, amelyben a X12 műveletek be vannak állítva |
| **Állapot** | A X12 üzenet állapota <br>Sikeres = fogadott vagy elküldött egy érvényes X12 üzenetet. Nincs beállítva funkcionális ACK. <br>Sikeres = fogadott vagy elküldött egy érvényes X12 üzenetet. A funkcionális ACK beállítása és fogadása, vagy egy funkcionális ACK küldése. <br>Sikertelen = kapott vagy érvénytelen X12 üzenetet küldött. <br>Függőben = fogadott vagy küldött egy érvényes X12 üzenetet. A funkcionális ACK beállítása be van állítva, és a rendszer funkcionális ACK-t vár. |
| **NYUGTA** | Funkcionális ACK (997) állapot <br>Elfogadva = kapott vagy pozitív működési nyugtát küldött. <br>Elutasítva = kapott vagy negatív működési nyugtát küldött. <br>Függőben = várt működési ACK, de nem érkezett. <br>Függőben = létrehozta a funkcionális ACK-t, de nem tud elküldeni a partnernek. <br>Nem kötelező = a funkcionális ACK nincs beállítva. |
| **Irány** | A X12 üzenet iránya |
| **Követési azonosító** | A logikai alkalmazásban lévő összes eseményindítót és műveletet összekapcsoló azonosító |
| **Msg típusa** | Az EDI X12 üzenet típusa |
| **ICN** | A X12-üzenethez tartozó Interchange Control-szám |
| **TSCN** | A X12 üzenethez tartozó készlettranzakció-készlet vezérlőelem száma |
| **Időbélyeg** | Az az idő, amikor a X12 művelet feldolgozta az üzenetet |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT-üzenet tulajdonságainak leírása

Az alábbiakban az egyes EDIFACT-üzenetekhez tartozó tulajdonságok leírását olvashatja.

| Tulajdonság | Leírás |
|----------|-------------|
| **Küldő** | A **fogadási beállításokban** megadott vendég partner vagy egy EDIFACT-szerződés **küldési beállításaiban** megadott gazda partner |
| **Fogadó** | A **fogadási beállításokban** megadott gazda partner vagy egy EDIFACT-szerződés **küldési beállításaiban** megadott vendég partner |
| **Logikai alkalmazás** | Az a logikai alkalmazás, amelyben a EDIFACT műveletek be vannak állítva |
| **Állapot** | A EDIFACT üzenet állapota <br>Sikeres = fogadott vagy elküldött egy érvényes EDIFACT üzenetet. Nincs beállítva funkcionális ACK. <br>Sikeres = fogadott vagy elküldött egy érvényes EDIFACT üzenetet. A funkcionális ACK beállítása és fogadása, vagy egy funkcionális ACK küldése. <br>Sikertelen = kapott vagy érvénytelen EDIFACT üzenetet küldött <br>Függőben = fogadott vagy küldött egy érvényes EDIFACT üzenetet. A funkcionális ACK beállítása be van állítva, és a rendszer funkcionális ACK-t vár. |
| **NYUGTA** | Funkcionális ACK (CONTRL) állapot <br>Elfogadva = kapott vagy pozitív működési nyugtát küldött. <br>Elutasítva = kapott vagy negatív működési nyugtát küldött. <br>Függőben = várt működési ACK, de nem érkezett. <br>Függőben = létrehozta a funkcionális ACK-t, de nem tud elküldeni a partnernek. <br>Nem kötelező = a funkcionális ACK nincs beállítva. |
| **Irány** | A EDIFACT üzenet iránya |
| **Követési azonosító** | A logikai alkalmazásban lévő összes eseményindítót és műveletet összekapcsoló azonosító |
| **Msg típusa** | A EDIFACT üzenet típusa |
| **ICN** | A EDIFACT-üzenethez tartozó Interchange Control-szám |
| **TSCN** | A EDIFACT üzenethez tartozó készlettranzakció-készlet vezérlőelem száma |
| **Időbélyeg** | Az az idő, amikor a EDIFACT művelet feldolgozta az üzenetet |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Következő lépések

* [Monitorozási és nyomkövetési lekérdezések létrehozása](../logic-apps/create-monitoring-tracking-queries.md)
