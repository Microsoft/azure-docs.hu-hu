---
title: Lekérdezések naplózása Azure Monitor naplózási lekérdezésekben
description: A naplózási lekérdezések azon naplófájljainak részletei, amelyek a Azure Monitorban futtatott telemetria nyújtanak.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 28dfac7de8e73adf577b0a13e5fbd8740b1e3b06
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047417"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Lekérdezések naplózása Azure Monitor naplókban (előzetes verzió)
A napló lekérdezési naplói a Azure Monitorban futtatott telemetria kapcsolatos információkat biztosítanak. Ez olyan információkat tartalmaz, mint például a lekérdezés futtatása, a futtatásuk, a használt eszköz, a lekérdezés szövege és a lekérdezés végrehajtását leíró teljesítmény-statisztika.


## <a name="configure-query-auditing"></a>Lekérdezés naplózásának konfigurálása
A lekérdezés naplózása a Log Analytics munkaterületen [diagnosztikai beállítással](../essentials/diagnostic-settings.md) engedélyezhető. Ez lehetővé teszi, hogy naplózási adatait a jelenlegi munkaterületre vagy az előfizetése bármely más munkaterületére küldje az Azure Event Hubs az Azure-on kívülre vagy az Azure Storage-ba való archiválásra. 

### <a name="azure-portal"></a>Azure Portal
Egy Log Analytics munkaterület diagnosztikai beállításának elérése a Azure Portal a következő helyekről:

- A **Azure monitor** menüben válassza a **diagnosztikai beállítások** elemet, majd keresse meg és válassza ki a munkaterületet.

    [![Diagnosztikai beállítások Azure Monitor ](media/query-audit/diagnostic-setting-monitor.png)](media/query-audit/diagnostic-setting-monitor.png#lightbox) 

- A **log Analytics munkaterületek** menüben válassza ki a munkaterületet, majd válassza a **diagnosztikai beállítások** lehetőséget.

    [![Diagnosztikai beállítások Log Analytics munkaterület ](media/query-audit/diagnostic-setting-workspace.png)](media/query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager-sablon
Egy példa Resource Manager-sablonra a [log Analytics munkaterület diagnosztikai beállításainál](../essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Naplózási adatgyűjtés
A rendszer minden alkalommal létrehoz egy naplózási rekordot, amikor egy lekérdezés fut. Ha Log Analytics-munkaterületre küldi az adatküldést, azt egy *LAQueryLogs* nevű táblában tárolja. A következő táblázat a naplózási adatok egyes rekordjainak tulajdonságait ismerteti.

| Mező | Leírás |
|:---|:---|
| TimeGenerated         | A lekérdezés elküldésének UTC-ideje. |
| CorrelationId         | A lekérdezés azonosítására szolgáló egyedi azonosító. Hibaelhárítási forgatókönyvekben használható, ha segítségre van szükség a Microsofttal való kapcsolatfelvételhez. |
| AADObjectId           | A lekérdezést indító felhasználói fiók Azure Active Directory azonosítója.  |
| AADTenantId           | A lekérdezést indító felhasználói fiók bérlője.  |
| AADEmail              | A lekérdezést elindító felhasználói fiók bérlője e-mail-címe.  |
| AADClientId           | A lekérdezés elindításához használt alkalmazás azonosítója és feloldott neve. |
| RequestClientApp      | A lekérdezés elindításához használt alkalmazás feloldott neve. |
| QueryTimeRangeStart   | A lekérdezéshez kiválasztott időtartomány kezdete. Ez bizonyos helyzetekben nem tölthető fel, például akkor, ha a lekérdezés Log Analyticsból indul el, és az időtartomány a lekérdezésen belül van megadva az időválasztó helyett. |
| QueryTimeRangeEnd     | A lekérdezéshez kiválasztott időtartomány vége. Ez bizonyos helyzetekben nem tölthető fel, például akkor, ha a lekérdezés Log Analyticsból indul el, és az időtartomány a lekérdezésen belül van megadva az időválasztó helyett.  |
| QueryText             | A futtatott lekérdezés szövege. |
| RequestTarget         | A lekérdezés elküldéséhez használt API URL-cím.  |
| RequestContext        | Azoknak az erőforrásoknak a listája, amelyeken a lekérdezésre kérték. Legfeljebb három karakterlánc-tömböt tartalmaz: munkaterületeket, alkalmazásokat és erőforrásokat. Az előfizetés vagy az erőforráscsoport által megadott lekérdezések *erőforrásokként* jelennek meg. A RequestTarget által vélelmezett célként megadott célt is tartalmazza.<br>Ha feloldható, az egyes erőforrások erőforrás-azonosítója is szerepelni fog. Előfordulhat, hogy a rendszer nem tudja feloldani, ha az erőforráshoz való hozzáférés során hibaüzenetet ad vissza. Ebben az esetben a rendszer a lekérdezés megadott szövegét fogja használni.<br>Ha a lekérdezés nem egyértelmű nevet használ, például több előfizetésben létező munkaterület-nevet, a rendszer ezt a kétértelmű nevet fogja használni. |
| RequestContextFilters | A lekérdezés meghívásának részeként megadott szűrők halmaza. Legfeljebb három lehetséges karakterlánc-tömböt tartalmaz:<br>-ResourceTypes – a lekérdezés hatókörének korlátozására szolgáló erőforrás típusa<br>– Munkaterületek – a lekérdezésre korlátozni kívánt munkaterületek listája<br>-WorkspaceRegions – a lekérdezés korlátozására szolgáló munkaterület-régiók listája |
| ResponseCode          | A lekérdezés elküldésekor visszaadott HTTP-válasz kódja. |
| ResponseDurationMs    | A válasz eredményének időpontja.  |
| ResponseRowCount     | A lekérdezés által visszaadott sorok száma összesen |
| StatsCPUTimeMs       | A számítási, elemzési és beolvasási számítási idő összesen. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsDataProcessedKB | A lekérdezés feldolgozásához elért adatmennyiség. A cél tábla mérete, a használt időtartomány, az alkalmazott szűrők és a hivatkozott oszlopok száma befolyásolja. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsDataProcessedStart | A lekérdezés feldolgozásához elért legrégebbi adatmennyiség. Befolyásolja a lekérdezés explicit időtartománya és a szűrők alkalmazása. Ez nagyobb lehet, mint az adatparticionálás miatti explicit időtartomány. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsDataProcessedEnd  |A lekérdezés feldolgozásához elért legújabb adatmennyiség. Befolyásolja a lekérdezés explicit időtartománya és a szűrők alkalmazása. Ez nagyobb lehet, mint az adatparticionálás miatti explicit időtartomány. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsWorkspaceCount | A lekérdezés által elért munkaterületek száma. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |
| StatsRegionCount | A lekérdezés által elért régiók száma. Csak akkor van feltöltve, ha a lekérdezés a 200 állapotkódot adja vissza. |

## <a name="considerations"></a>Megfontolandó szempontok

- A lekérdezéseket csak akkor naplózza a rendszer, ha a végrehajtása felhasználói környezetben történik. Az Azure-on belül nem történik szolgáltatás-szolgáltatás naplózása. A kizáró lekérdezések két elsődleges csoportja a számlázási számítások és az automatizált riasztások végrehajtása. Riasztás esetén a rendszer csak az ütemezett riasztási lekérdezést fogja naplózni; a riasztás-létrehozási képernyőn a riasztás kezdeti végrehajtása felhasználói környezetben történik, és naplózási célokra elérhető lesz. 
- A teljesítménnyel kapcsolatos statisztikák nem érhetők el az Azure Adatkezelő proxyról érkező lekérdezések esetén. A lekérdezésekre vonatkozó egyéb adatok továbbra is fel lesznek töltve.
- A [sztringeket eltorzító](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) karakterláncok *h* -célzása nem lesz hatással a lekérdezési naplókra. A lekérdezések pontosan úgy lesznek rögzítve, hogy a karakterlánc ne legyen elküldve. Győződjön meg arról, hogy csak a megfelelőségi jogosultsággal rendelkező felhasználók láthatják ezt az információt az Log Analytics munkaterületeken elérhető különböző Kubernetes-RBAC vagy Azure RBAC módok használatával.
- Több munkaterület adatait tartalmazó lekérdezések esetén a lekérdezés csak azokon a munkaterületeken lesz rögzítve, amelyekhez a felhasználónak hozzáférése van.

## <a name="costs"></a>Költségek  
Az Azure diagnosztikai bővítmény használatáért nem számítunk fel díjat, de díjkötelesek lehetnek a betöltött adatokért járó díjak. Tekintse meg [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) azon a célhelyen, amelyben az adatok gyűjtése folyamatban van.

## <a name="next-steps"></a>Következő lépések

- További információ a [diagnosztikai beállításokról](../essentials/diagnostic-settings.md).
- További információ a [naplók lekérdezésének optimalizálásáról](query-optimization.md).
