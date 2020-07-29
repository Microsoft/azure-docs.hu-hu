---
title: Műveletek és tevékenységek figyelése
titleSuffix: Azure Cognitive Search
description: Naplózás engedélyezése, lekérdezési tevékenység metrikáinak beolvasása, erőforrás-használat és egyéb rendszeradatok Azure Cognitive Search szolgáltatásból.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 421fddb819d4d396d3ab8890789e58ccb935cbc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806811"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Az Azure Cognitive Search működésének és tevékenységének figyelése

Ez a cikk áttekintést nyújt az Azure Cognitive Search figyelési alapelveiről és eszközeiről. Az átfogó figyeléshez használhatja a beépített funkciók és bővítmények, például a Azure Monitor kombinációját.

Összességében nyomon követheti a következőket:

* Szolgáltatás: állapot/rendelkezésre állás és a szolgáltatás konfigurációjának módosításai.
* Tárterület: mindkettő használatban van és elérhető, és minden tartalomtípushoz a szolgáltatási szinten engedélyezett kvótához viszonyítva számít.
* Lekérdezési tevékenység: kötetek, késések, valamint szabályozott vagy eldobott lekérdezések. A naplózott lekérdezési kérelmekhez [Azure monitor](#add-azure-monitor)szükséges.
* Indexelési tevékenység: a Azure Monitortel való [diagnosztikai naplózást](#add-azure-monitor) igényel.

A keresési szolgáltatás felhasználónkénti hitelesítést nem támogat, ezért a rendszer nem talál azonosító adatokat a naplókban.

## <a name="built-in-monitoring"></a>Beépített figyelés

A beépített figyelés a keresési szolgáltatás által naplózott tevékenységekre vonatkozik. A diagnosztika kivételével a figyelési szinthez nincs szükség konfigurációra.

Az Azure Cognitive Search a szolgáltatás állapotára és a lekérdezési metrikára vonatkozó jelentéskészítési 30 napos ütemterven tárolja a belső adatokat, amelyek a portálon vagy a [REST API](#monitoring-apis)-kon keresztül találhatók meg.

A következő képernyőfelvétel segít megtalálni a figyelési információkat a portálon. Az adatai a szolgáltatás használatának megkezdése után azonnal elérhetővé válnak. A portál oldalai néhány percenként frissülnek.

* **Figyelés** lap – a fő Áttekintés lapon a lekérdezési mennyiség, a késés, valamint a szolgáltatás nyomása alatt látható.
* A **tevékenység naplója**a bal oldali navigációs ablaktáblán csatlakozik a Azure Resource Managerhoz. A tevékenység napló jelentéseket készít a Resource Manager által végrehajtott műveletekről: a szolgáltatás rendelkezésre állása és állapota, a kapacitás (replikák és partíciók) változásai, valamint az API-kulcsokkal kapcsolatos tevékenységek.
* A **figyelési** beállítások – lejjebb – konfigurálható riasztásokat, metrikákat és diagnosztikai naplókat biztosítanak. Ezeket akkor hozza létre, amikor szüksége van rájuk. Az adatok gyűjtése és tárolása után lekérdezheti vagy megjelenítheti az elemzések adatait.

![Azure Monitor integráció egy keresési szolgáltatásban](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integráció egy keresési szolgáltatásban")

> [!NOTE]
> Mivel a portál oldalai néhány percenként frissülnek, a jelentett számok hozzávetőleges értékkel bírnak, így általános értelemben láthatja, hogy a rendszer milyen jól szolgálja ki a karbantartási kérelmeket. A tényleges mérőszámok, például a másodpercenkénti lekérdezések (QPS) lehetnek magasabbak vagy alacsonyabbak, mint az oldalon megjelenített szám. Ha a pontosság követelmény, érdemes lehet API-kat használni.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>A figyeléshez hasznos API-k

A következő API-k segítségével kérheti le a portál figyelés és használat lapjain található adatokat.

* [Szolgáltatás statisztikáinak beolvasása](/rest/api/searchservice/get-service-statistics)
* [Index statisztikáinak beolvasása](/rest/api/searchservice/get-index-statistics)
* [Dokumentumok számának beolvasása](/rest/api/searchservice/count-documents)
* [Indexelő állapotának beolvasása](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Tevékenységek naplói és szolgáltatás állapota

A portál [**tevékenység napló**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) lapja adatokat gyűjt a Azure Resource Managerről, és jelentéseket készít a szolgáltatás állapotának változásairól. A szolgáltatási állapottal kapcsolatos kritikus, hibával és figyelmeztetési feltételekkel nyomon követheti a tevékenység naplóját.

A közös bejegyzések az API-kulcsokra mutató hivatkozásokat tartalmaznak – általános tájékoztató értesítések, például *rendszergazdai kulcs* beolvasása és *lekérdezési kulcsok beolvasása*. Ezek a tevékenységek azokat a kérelmeket jelzik, amelyeket a felügyeleti kulcs (objektumok létrehozása vagy törlése) vagy a lekérdezési kulcs használatával hoztak létre, de a kérést nem jelenítik meg. A gabonával kapcsolatos információkért be kell állítania a diagnosztikai naplózást.

A **tevékenység naplóját** a bal oldali navigációs ablaktáblán, vagy a felső ablak parancssáv vagy a **problémák diagnosztizálása és megoldása** oldalon található értesítések közül lehet elérni.

### <a name="monitor-storage-in-the-usage-tab"></a>Tároló figyelése a használat lapon

A portálon a vizualizáció figyeléséhez a **használat** lapon az erőforrás rendelkezésre állása látható a szolgáltatási szinten kiszabott jelenlegi [korlátokhoz](search-limits-quotas-capacity.md) képest. Ha az éles számítási feladatokhoz [használt szintet](search-sku-tier.md)véglegesíti, vagy ha [módosítani szeretné az aktív replikák és partíciók számát](search-capacity-planning.md), akkor ezek a mérőszámok segítséget nyújthatnak ezekhez a döntésekhez azáltal, hogy megmutatják, hogy az erőforrások milyen gyorsan legyenek felhasználva, és hogy a jelenlegi konfiguráció milyen mértékben kezeli a meglévő terhelést.

Az alábbi ábra az ingyenes szolgáltatás, amely az egyes típusok 3 objektumára, illetve 50 MB tárterületre van korlátozva. Egy alapszintű vagy standard szolgáltatás magasabb korláttal rendelkezik, és ha megnöveli a partíciók számát, a maximális tárterület arányosan növekszik.

![A használati állapot a rétegek korlátaihoz képest](./media/search-monitor-usage/usage-tab.png
 "A használati állapot a rétegek korlátaihoz képest")

> [!NOTE]
> A tárterülettel kapcsolatos riasztások jelenleg nem érhetők el; a tárolási felhasználás nincs összesítve, vagy be van jelentkezve a Azure Monitor **AzureMetrics** táblába. A tárolási riasztások beszerzéséhez létre kell hoznia [egy egyéni megoldást](../azure-monitor/insights/solutions-creating.md) , amely az erőforrásokkal kapcsolatos értesítéseket bocsát ki, ahol a kód ellenőrzi a tárolási méretet, és kezeli a választ.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Kiegészítő figyelés Azure Monitor

Számos szolgáltatás, többek között az Azure Cognitive Search, a további riasztások, metrikák és naplózási diagnosztikai adatok [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) integrálásával. 

A keresési szolgáltatás [diagnosztikai naplózásának engedélyezése](search-monitor-logs.md) , ha az adatgyűjtést és-tárolást szeretné szabályozni. Az Azure Monitor által rögzített naplózott eseményeket a rendszer a **AzureDiagnostics** táblában tárolja, és a lekérdezésekhez és indexeléshez kapcsolódó operatív adatból áll.

Azure Monitor számos tárolási lehetőséget biztosít, és az Ön választása határozza meg, hogyan használhatja fel az adatait:

* Válassza az Azure Blob Storage lehetőséget, ha egy Power BI jelentésben szeretné [megjeleníteni a naplózási adattárakat](search-monitor-logs-powerbi.md) .
* Válassza a Log Analytics lehetőséget, ha Kusto-lekérdezéseken keresztül szeretné felderíteni az adatforrásokat.

Azure Monitor saját számlázási struktúrája van, és az ebben a szakaszban hivatkozott diagnosztikai naplókhoz kapcsolódó díj vonatkozik. További információ: [a Azure monitor használati és becsült költségei](../azure-monitor/platform/usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Felhasználói hozzáférés figyelése

Mivel a keresési indexek egy nagyobb ügyfélalkalmazás összetevői, a felhasználónkénti hozzáférés-vezérléshez és-figyeléshez nem áll rendelkezésre beépített módszertan az indexekhez. A rendszer a kérelmeket egy ügyfélalkalmazás, rendszergazdai vagy lekérdezési kérelmek esetében feltételezi. A rendszergazdai írási és olvasási műveletek közé tartozik a teljes szolgáltatáson belüli objektumok létrehozása, frissítése és törlése. A csak olvasási műveletek a dokumentumok gyűjteményére vonatkozó lekérdezések, amelyek hatóköre egyetlen indexre van korlátozva. 

Így a tevékenység naplóiban láthatók a rendszergazdai kulcsokat vagy a lekérdezési kulcsokat használó hívásokra mutató hivatkozások. A megfelelő kulcsot az ügyfél kódjából származó kérelmek tartalmazzák. A szolgáltatás nem alkalmas az identitás-tokenek vagy a megszemélyesítés kezelésére.

Ha a felhasználónkénti hitelesítéshez üzleti követelmények léteznek, a javaslat a Azure Active Directory integrációja. $Filter és felhasználói identitások használatával levágja a dokumentumok azon [keresési eredményeit](search-security-trimming-for-azure-search-with-aad.md) , amelyeket a felhasználó nem láthat. 

Ezt az információt nem lehet külön naplózni a lekérdezési karakterláncból, amely tartalmazza a $filter paramétert. A jelentéskészítési lekérdezési karakterláncokkal kapcsolatos részletekért lásd: [lekérdezések figyelése](search-monitor-queries.md) .

## <a name="next-steps"></a>További lépések

A Azure Monitorekkel való gördülékenység elengedhetetlen az Azure-szolgáltatások, például az Azure-Cognitive Searchek felügyeletéért. Ha nem ismeri a Azure Monitort, szánjon időt az erőforrásokkal kapcsolatos cikkek áttekintésére. Az oktatóanyagokon kívül a következő cikk is jó kiindulópont.

> [!div class="nextstepaction"]
> [Azure-erőforrások monitorozása Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
