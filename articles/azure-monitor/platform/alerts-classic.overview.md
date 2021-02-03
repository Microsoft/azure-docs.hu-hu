---
title: A klasszikus riasztások áttekintése Azure Monitor
description: A klasszikus riasztások elavultak. A riasztások lehetővé teszik az Azure-erőforrások metrikáinak, eseményeinek vagy naplófájljainak figyelését, és értesítést kapnak, ha teljesül egy megadott feltétel.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 82923b461980fbc0740cad08a9e699765f17abc7
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525517"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Mik a Microsoft Azure klasszikus riasztásai?

> [!NOTE]
> Ez a cikk bemutatja, hogyan hozhat létre régebbi klasszikus metrikai riasztásokat. Azure Monitor mostantól támogatja [az újabb, közel valós idejű metrikai riasztásokat és új riasztási élményt](./alerts-overview.md). A klasszikus riasztások [kimaradnak a nyilvános](./monitoring-classic-retirement.md) Felhőbeli felhasználók számára, bár az új riasztásokat még nem támogató erőforrások esetében is korlátozott mértékben használják.
>

A riasztások lehetővé teszik az adatfeltételek konfigurálását, és értesítést kapnak, ha a feltételek megfelelnek a legújabb figyelési adatnak.

## <a name="old-and-new-alerting-capabilities"></a>A korábbi és az új riasztási képességek

A múltban Azure Monitor a Application Insights, a Log Analytics és a Service Health elkülönítte a riasztási képességeket. A túlóra, az Azure és a felhasználói felület és a különböző riasztási módszerek együttes használata is javult. Az összevonás még folyamatban van.

A klasszikus riasztások csak a klasszikus riasztások felhasználói képernyőjén tekinthetők meg az Azure Portalon. Ezt a képernyőt a **klasszikus riasztások megtekintése** gombra kattintva érheti el a riasztások képernyőjén. 

 ![Riasztási lehetőségek a Azure Portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Az új riasztások felhasználói élmény az alábbi előnyökkel jár a klasszikus riasztások terén:
- **Jobb értesítési rendszer** – minden újabb riasztás olyan műveleti csoportokat használ, amelyek az értesítések és műveletek névvel ellátott csoportjai, amelyek több riasztásban is felhasználhatók. A klasszikus metrikai riasztások és a régebbi Log Analytics riasztások nem használnak műveleti csoportokat.
- **Egységes authoring Experience** – az összes riasztás létrehozása a metrikák, naplók és tevékenységek naplózása Azure Monitor, Log Analytics és Application Insights között egy helyen történik.
- **Kilőtt log Analytics-riasztások megtekintése a Azure Portalban** – mostantól megtekintheti az előfizetésében log Analytics riasztásokat is. Korábban ezek egy külön portálon voltak.
- **Kilőtt riasztások és riasztási szabályok elkülönítése** – a riasztási szabályok (a riasztást kiváltó feltétel definíciója) és a kilőtt riasztások (a riasztási szabály égetésének egy példánya) differenciált, így az operatív és a konfigurációs nézetek külön vannak elválasztva.
- **Jobb munkafolyamat** – az új riasztások létrehozási élménye végigvezeti a felhasználót a riasztási szabály konfigurálásának folyamatán, ami egyszerűbbé teszi a riasztások beszerzéséhez szükséges megfelelő dolgok felderítését.
- **Intelligens riasztások összevonása** és **riasztási állapot beállítása**  – az újabb riasztások közé tartozik az automatikus csoportosítási funkció, amely hasonló riasztásokat jelenít meg a felhasználói felület túlterhelésének csökkentése érdekében 

Az újabb metrikai riasztások az alábbi előnyökkel járnak a klasszikus metrikai riasztások esetén:
- **Javított késés**: az újabb metrikai riasztások akár percenként is futhatnak. A régebbi metrikai riasztások mindig 5 perces gyakorisággal futnak. Az újabb riasztások egyre kisebb késleltetést mutatnak az értesítés vagy a művelet (3 – 5 perc) miatti probléma előfordulásakor. A régebbi riasztások a típustól függően 5 – 15 percet vesznek igénybe.  A naplózási riasztások általában 10 – 15 perces késéssel rendelkeznek a naplók betöltéséhez szükséges idő miatt, de az újabb feldolgozási módszerek csökkentik ezt az időt. 
- **Többdimenziós mérőszámok támogatása**: riasztást készíthet a dimenziós mérőszámokról, így a mérőszám érdekes szegmensét figyelheti.
- **Nagyobb mértékű szabályozás a metrikus feltételeknél**: megadhat gazdagabb riasztási szabályokat. Az újabb riasztások támogatják a metrikák maximális, minimális, átlagos és teljes értékének figyelését.
- **Több mérőszám együttes figyelése**: több mérőszámot is figyelheti (jelenleg legfeljebb két mérőszámot) egyetlen szabállyal. A riasztás akkor aktiválódik, ha mindkét metrika megszegi a vonatkozó küszöbértékeket a megadott időszakra vonatkozóan.
- **Jobb értesítési rendszer**: az összes újabb riasztás olyan [műveleti csoportokat](./action-groups.md)használ, amelyek az értesítések és műveletek névvel ellátott csoportjai, amelyek többször is felhasználhatók több riasztásban.  A klasszikus metrikai riasztások és a régebbi Log Analytics riasztások nem használnak műveleti csoportokat. 
- **Metrikák a naplókból** (nyilvános előzetes verzió): a log Analytics beolvasott adatok mostantól kioszthatók és átalakíthatók Azure monitor metrikára, majd a többi mérőszámhoz hasonlóan riasztást is kaphatnak. A klasszikus riasztásokra jellemző terminológiát a [riasztások (klasszikus)]() című témakörben tekintheti meg. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasszikus riasztások Azure Monitor-adatszolgáltatásban
A klasszikus riasztások két típusa érhető el – metrikus riasztások és műveletnapló riasztások.

* **Klasszikus metrikai riasztások** – ez a riasztás akkor aktiválódik, ha egy adott metrika értéke átlép egy hozzárendelt küszöbértéket. A riasztás a küszöbérték meghaladása és a riasztási feltétel teljesülése esetén értesítést hoz létre. Ezen a ponton a riasztás "aktiváltnak" minősül. Egy másik értesítést hoz létre, ha "megoldódott" – vagyis ha a küszöbértéket újra meghaladják, és a feltétel már nem teljesül.

* **Klasszikus műveletnapló – riasztások** – egy adatfolyam-naplózási riasztás, amely egy, a szűrési feltételeknek megfelelő műveletnapló-eseményre mutat. Ezek a riasztások csak egy állapottal rendelkeznek, "aktiválva". A riasztási motor egyszerűen alkalmazza a szűrési feltételeket minden új eseményre. Nem keres régebbi bejegyzéseket. Ezek a riasztások értesítik, ha új Service Health incidens következik be, vagy amikor egy felhasználó vagy alkalmazás végrehajt egy műveletet az előfizetésben, például "virtuális gép törlése".

Az Azure Monitoron keresztül elérhető erőforrás-naplózási adatforrások esetében a rendszer átirányítja az adatLog Analyticsba, és egy napló lekérdezése riasztást használ Log Analytics most az [új riasztási módszert](./alerts-overview.md) használja 

A következő ábra összefoglalja Azure Monitorban lévő adatforrásokat, és fogalmilag azt, hogyan lehet a riasztást kikapcsolni.

![A riasztások magyarázata](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Riasztások rendszertana (klasszikus)
Az Azure a következő kifejezéseket használja a klasszikus riasztások és a hozzájuk tartozó függvények leírásához:
* **Riasztás** – a feltételek (egy vagy több szabály vagy feltétel) definíciója, amely akkor aktiválódik, amikor teljesülnek.
* **Aktív** – az az állapot, amikor a klasszikus riasztás által meghatározott feltételek teljesülnek.
* **Megoldott** – az az állapot, amikor a klasszikus riasztás által definiált feltételek már nem teljesülnek, miután teljesültek a korábban.
* **Értesítés** – a klasszikus riasztások aktívvá válása után végrehajtott művelet aktív lesz.
* **Művelet** – egy értesítés fogadójának küldött megadott hívás (például egy cím küldése vagy egy WEBHOOK URL-címére történő közzététel). Az értesítések általában több műveletet indíthatnak el.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hogyan értesítést kap egy Azure Monitor klasszikus riasztásról?
A különböző szolgáltatásokból származó Azure-riasztások a saját beépített értesítési módszereiket használták. 

Azure Monitor létrehozta a *műveleti csoportok* nevű újrafelhasználható értesítési csoportot. A műveleti csoportok a fogadók készletét adják meg egy értesítéshez. Minden alkalommal, amikor egy riasztás aktiválva van, amely a műveleti csoportra hivatkozik, minden fogadó megkapja ezt az értesítést. A műveleti csoportok lehetővé teszik a fogadók (például a meghívásos mérnök-lista) egy csoporton belüli felhasználását számos riasztási objektumban. A műveleti csoportok az e-mail-címek, SMS-számok és számos más művelet mellett egy webhook URL-címére való közzétételsel támogatják az értesítést.  További információ: [Action groups](./action-groups.md). 

A régebbi klasszikus műveletnapló riasztásai a műveleti csoportokat használják.

Azonban a régebbi metrikai riasztások nem használnak műveleti csoportokat. Ehelyett a következő műveleteket állíthatja be: 
- E-mail-értesítések küldése a szolgáltatás-rendszergazdának, a saját rendszergazdáknak vagy a megadott e-mail címeknek.
- Hívjon fel egy webhookot, amely további automatizálási műveletek elindítását teszi lehetővé.

A webhookok lehetővé teszik az automatizálást és a szervizelést, például a következő használatával:
- Azure Automation-runbook
- Azure-függvény
- Azure logikai alkalmazás
- harmadik féltől származó szolgáltatás

## <a name="next-steps"></a>Következő lépések
Információk kérése a riasztási szabályokról és a konfigurálásuk a használatával:

* További információ a [metrikákkal](data-platform.md) kapcsolatban
* [Klasszikus metrikai riasztások konfigurálása Azure Portal használatával](alerts-classic-portal.md)
* [Klasszikus metrikai riasztások PowerShell](alerts-classic-portal.md) konfigurálása
* [Klasszikus metrikai riasztások parancssori felületének konfigurálása (CLI)](alerts-classic-portal.md)
* [Klasszikus metrikai riasztások konfigurálása Azure Monitor REST API](/rest/api/monitor/alertrules)
* További információ a [tevékenység naplóról](platform-logs-overview.md)
* [Műveletnapló riasztások konfigurálása Azure Portal használatával](activity-log-alerts.md)
* [Műveletnapló riasztások konfigurálása a Resource Manager használatával](alerts-activity-log.md)
* Tekintse át a [tevékenység naplójának riasztása webhook sémáját](activity-log-alerts-webhook.md)
* További információ a [műveleti csoportokról](action-groups.md)
* [Újabb riasztások](alerts-metric.md) konfigurálása

