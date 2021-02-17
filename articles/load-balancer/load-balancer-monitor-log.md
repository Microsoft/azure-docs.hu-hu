---
title: Nyilvános terheléselosztó műveleteinek, eseményeinek és számlálóinak figyelése
titleSuffix: Azure Load Balancer
description: Útmutató a Azure Load Balancer naplózásának engedélyezéséhez.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 7a456057bc088264cefb91be9f3e5069b29474a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596810"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Monitor naplók az Azure standard Load Balancer

Az Azure standard Load Balancer kezeléséhez és hibakereséséhez különböző típusú Azure Monitor-naplókat használhat. A naplók továbbíthatók egy Event hub-vagy Log Analytics-munkaterületre. Az Azure Blob Storageból kinyerheti az összes naplót, és megtekintheti őket olyan eszközökön, mint például az Excel és a Power BI. 

A naplók típusai a következők:

* **Tevékenységek naplói:** Megtekintheti az Azure-előfizetésekhez elküldött összes tevékenységet, valamint az állapotukat is. További információ: tevékenység- [naplók megtekintése az erőforrásokon végzett műveletek figyeléséhez](../azure-resource-manager/management/view-activity-logs.md). A Tevékenységnaplók alapértelmezés szerint engedélyezve vannak, és a Azure Portal tekinthetők meg. Ezek a naplók az Azure alapszintű Load Balancer és standard Load Balancer egyaránt elérhetők.
* **Standard Load Balancer metrikák:** Ez a napló a standard Load Balancer naplóként exportált metrikáinak lekérdezésére használható. Ezek a naplók csak standard Load Balancer számára érhetők el.

> [!IMPORTANT]
> Az állapot-mintavételi és Load Balancer riasztási eseménynaplók jelenleg nem működőképesek, és a [Azure Load Balancer ismert problémái](whats-new.md#known-issues)vannak felsorolva. 

> [!IMPORTANT]
> A naplók csak az Azure Resource Manager üzemi modellben üzembe helyezett erőforrásokhoz érhetők el. A klasszikus üzemi modell erőforrásaihoz nem használhat naplókat. További információ az üzembe helyezési modellekről: a [Resource Manager telepítésének ismertetése és a klasszikus üzembe helyezés](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Naplózás engedélyezése

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. Az esemény-és állapot-mintavételi naplózás engedélyezése a naplókon keresztül elérhető adatok gyűjtésének megkezdéséhez. Ehhez a következő lépések szükségesek:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nem rendelkezik terheléselosztó, a folytatás előtt [hozzon létre egy Load balancert](./quickstart-load-balancer-standard-public-portal.md) .
1. A portálon válassza az **erőforráscsoportok** lehetőséget.
2. Válassza ki **\<resource-group-name>** a terheléselosztó helyét.
3. Válassza ki a terheléselosztót.
4. Válassza a **műveletnapló**  >  **diagnosztikai beállítások** lehetőséget.
5. A **diagnosztika beállításai** ablaktábla **diagnosztika beállításai** területén válassza a **+ diagnosztikai beállítás hozzáadása** elemet.
6. A **diagnosztikai beállítások** létrehozása panelen adja meg a **MyLBDiagnostics** nevet a **név** mezőben.
7. A **diagnosztikai beállításoknak** három lehetősége van. Kiválaszthat egy, kettő vagy mind a három lehetőséget, és konfigurálhatja az egyes követelményeket:

   * **Archiválás egy Storage-fiókba**. Ehhez a folyamathoz már létre kell hoznia egy Storage-fiókot. A Storage-fiók létrehozásával kapcsolatban tekintse meg [a Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal)című témakört.
     1. Jelölje be az **archiválás a Storage-fiókba** jelölőnégyzetet.
     2. Válassza a **Konfigurálás** lehetőséget a **Storage-fiók kiválasztása** panel megnyitásához.
     3. Az **előfizetés** legördülő listában válassza ki azt az előfizetést, amelyben a Storage-fiókot létrehozták.
     4. A **Storage-fiók** legördülő listában válassza ki a Storage-fiók nevét.
     5. Válassza az **OK** lehetőséget.

   * **Adatfolyam küldése az Event hub-** nak. Ehhez a folyamathoz már létrehozott Event hub szükséges. Az Event hub létrehozásához tekintse meg a rövid útmutató [: Event hub létrehozása a Azure Portal használatával](../event-hubs/event-hubs-create.md)című témakört.
     1. Jelölje ki az **adatfolyamot az Event hub számára** jelölőnégyzetet.
     2. A **Konfigurálás** elemre kattintva nyissa meg az **Event hub kiválasztása** ablaktáblát.
     3. Az **előfizetés** legördülő listában válassza ki azt az előfizetést, amelyben az Event hub létrejött.
     4. Az **Event hub-névtér kiválasztása** legördülő listában válassza ki a névteret.
     5. Az **Event hub-házirend neve** legördülő listában válassza ki a nevet.
     6. Válassza az **OK** lehetőséget.

   * **Küldés log Analyticsba**. Ehhez a folyamathoz már létre kell hoznia és konfigurálnia kell egy log Analytics-munkaterületet. Log Analytics munkaterület létrehozásához tekintse [meg a log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/logs/quick-create-workspace.md)című témakört.
     1. Jelölje be a **Küldés a Log Analytics szolgáltatásba** jelölőnégyzetet.
     2. Az **előfizetés** legördülő listában válassza ki azt az előfizetést, ahol a log Analytics munkaterület.
     3. A **log Analytics munkaterület** legördülő listából válassza ki a munkaterületet.

8. A **diagnosztikai beállítások** ablaktábla **metrika** szakaszában jelölje be a **AllMetrics** jelölőnégyzetet.

9. Győződjön meg arról, hogy minden helyes, majd válassza a **Mentés** lehetőséget a **diagnosztikai beállítások** létrehozása ablaktábla tetején.

## <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A rendszer alapértelmezés szerint létrehozza a tevékenység naplóját. A [cikk utasításait](../azure-monitor/platform/activity-log.md)követve beállíthatja, hogy az előfizetési szinten exportálható legyen. További információ ezekről a naplókról: a [tevékenység naplóinak megtekintése az erőforrásokkal kapcsolatos műveletek figyelése](../azure-resource-manager/management/view-activity-logs.md) című cikkben.

A következő módszerek bármelyikével megtekintheti és elemezheti a tevékenység naplójának adatai:

* **Azure-eszközök:** A Azure PowerShell, az Azure CLI, az Azure REST API vagy a Azure Portal használatával kérdezheti le az adatokat a tevékenység naplójából. A [Resource Managerrel kapcsolatos naplózási műveletek](../azure-resource-manager/management/view-activity-logs.md) részletes útmutatót biztosítanak az egyes módszerekhez.
* **Power bi:** Ha még nem rendelkezik [Power bi](https://powerbi.microsoft.com/pricing) -fiókkal, ingyenes kipróbáláshoz is kipróbálhatja. Az Azure- [naplók Power bi-integrációjának](https://powerbi.microsoft.com/integrations/azure-audit-logs/)használatával az előre konfigurált irányítópultokkal elemezheti az adatait. Vagy testreszabhatja a nézeteket az igényeinek megfelelően.

## <a name="view-and-analyze-metrics-as-logs"></a>Metrikák megtekintése és elemzése naplókként
A Azure Monitor exportálási funkciójának használatával exportálhatja Load Balancer metrikáit. Ezek a metrikák minden egyperces mintavételi időközhöz naplóbejegyzést hoznak.

A metrikák és naplók közötti exportálás engedélyezett erőforrás-szinten. A naplók engedélyezése:

1. Nyissa meg a **diagnosztikai beállítások** ablaktáblát.
1. Szűrje az erőforráscsoportot, majd válassza ki azt a Load Balancer-példányt, amelyen engedélyezni szeretné a metrikák exportálását. 
1. Ha a Load Balancer diagnosztikai beállítások lapja megjelenik, válassza a **AllMetrics** lehetőséget a jogosult metrikák naplókként való exportálásához.

A metrikák exportálásának korlátozásait a jelen cikk [korlátozások](#limitations) című szakaszában találja.

Miután engedélyezte a **AllMetrics** -t a standard Load Balancer diagnosztikai beállításaiban, ha egy Event hub-vagy log Analytics-munkaterületet használ, ezek a naplók a **AzureMonitor** táblában lesznek feltöltve. 

Ha tárolóba exportál, kapcsolódjon a Storage-fiókjához, és kérje le az esemény-és állapot-mintavételi naplókhoz tartozó JSON-naplóbejegyzéseket. A JSON-fájlok letöltése után konvertálhatja őket CSV-re, és megtekintheti őket az Excelben, Power BI vagy bármely más adatvizualizációs eszközben. 

> [!TIP]
> Ha már ismeri a Visual studiót és a C#-beli állandók és változók értékeinek módosítására vonatkozó alapfogalmakat, használhatja a GitHubról elérhető [log Converter-eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter) .

## <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba
Ha a diagnosztikai adatokat egy esemény központba továbbítják, a központi naplózási elemzéshez használhatja a partner SIEM-eszközben Azure Monitor-integrációval. További információ: az [Azure monitoring-adatok továbbítása egy Event hubhoz](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

## <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek
Az Azure-beli erőforrásokhoz közvetlenül egy Log Analytics munkaterületre küldhet diagnosztikai adatokat. Ebben a munkaterületen összetett lekérdezéseket futtathat a hibaelhárítási és elemzési információkhoz. További információ: az [Azure-erőforrás-naplók gyűjtése Azure Monitor log Analytics munkaterületen](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Korlátozások
A Azure Load Balancer metrika-naplók exportálási funkciója a következő korlátozásokkal rendelkezik:
* A metrikák jelenleg belső neveken keresztül jelennek meg a naplókba való exportáláskor. A leképezést az alábbi táblázatban találja.
* A metrikák dimenzióját nem őrződnek meg. Például a **DipAvailability** (állapot-mintavételi állapot) mérőszámokkal nem lehet kiosztani vagy megtekinteni a HÁTTÉRBELI IP-címet.
* A használt SNAT-portok és a lefoglalt SNAT-portok metrikái jelenleg nem érhetők el naplókként való exportálásra.

## <a name="next-steps"></a>Következő lépések
* [Tekintse át a terheléselosztó elérhető mérőszámait](./load-balancer-standard-diagnostics.md)
* [Lekérdezések létrehozása és tesztelése a következő Azure Monitor utasítások alapján](../azure-monitor/log-query/log-query-overview.md)