---
title: Folyamatos figyelés a Azure Monitorsal | Microsoft Docs
description: Az Azure Monitor használatának konkrét lépéseit ismerteti a teljes munkafolyamatok folyamatos figyelésének engedélyezéséhez.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 2fe9d7e4da8950cee2dcb2fbcfb26355ace7285d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044017"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Folyamatos figyelés a Azure Monitor

A folyamatos monitorozás arra a folyamatra és technológiára utal, amely a DevOps és az IT-üzemeltetési életciklusok minden fázisában való monitorozáshoz szükséges. Segít az alkalmazás és az infrastruktúra állapotának, teljesítményének és megbízhatóságának folyamatos biztosításában, ahogy az a fejlesztéstől a Termelésig mozdul. A folyamatos monitorozás a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) fogalmait mutatja be, amelyek segítségével gyorsabban és megbízhatóbban fejlesztheti és szolgáltathatja a szoftvereket a felhasználók számára.

A [Azure monitor](overview.md) az Azure-ban egyesített figyelési megoldás, amely teljes körű megfigyelést biztosít a felhőben és a helyszínen található alkalmazások és infrastruktúrák között. Zökkenőmentesen működik együtt a [Visual Studióval és a Visual Studio Code](https://visualstudio.microsoft.com/) -val a fejlesztés és a tesztelés során, és integrálható az [Azure DevOps](/azure/devops/user-guide/index) a kiadási felügyelethez és a munkaelemek felügyeletéhez a telepítés és a műveletek A ITSM és a SIEM-eszközök között is integrálható, hogy segítsen nyomon követni a problémákat és az incidenseket a meglévő IT-folyamatokon belül.

Ez a cikk az Azure Monitor használatának konkrét lépéseit ismerteti a teljes munkafolyamatok folyamatos figyelésének engedélyezéséhez. Más dokumentációra mutató hivatkozásokat tartalmaz, amelyek részletesen ismertetik a különböző funkciók megvalósításának részleteit.


## <a name="enable-monitoring-for-all-your-applications"></a>Az összes alkalmazás figyelésének engedélyezése
Annak érdekében, hogy a teljes környezeten belül betekintést nyerjen, engedélyeznie kell a figyelést az összes webalkalmazáson és szolgáltatáson. Ez lehetővé teszi a végpontok közötti tranzakciók és kapcsolatok egyszerű megjelenítését az összes összetevőn.

- [Azure DevOps projects](../devops-project/overview.md) a meglévő kód-és git-tárház egyszerűsített élményét nyújtja, vagy választhat az egyik példa közül, hogy egy folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamatot hozzon létre az Azure-ba.
- A [DevOps kiadási folyamat folyamatos figyelése](./app/continuous-monitoring.md) lehetővé teszi, hogy a figyelési adatain alapuló üzembe helyezést vagy visszaállítást.
- [Állapotmonitor](./app/monitor-performance-live-website-now.md)  lehetővé teszi egy élő .NET-alkalmazás üzembe helyezését a Windowsban az Azure Application Insights használatával anélkül, hogy módosítania vagy újra kellene telepítenie a kódot.
- Ha rendelkezik hozzáféréssel az alkalmazás kódjához, akkor a Azure Monitor Application Insights SDK [.net](./app/asp-net.md), [.net Core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./app/nodejs-quick-start.md)vagy [más programozási nyelvekre](./app/platforms.md)való telepítésével engedélyezze a teljes figyelést [Application Insights](./app/app-insights-overview.md) . Ez lehetővé teszi az alkalmazáshoz és a vállalathoz tartozó egyéni események, metrikák vagy oldalletöltések megadását.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>A teljes infrastruktúra figyelésének engedélyezése
Az alkalmazások csak a mögöttes infrastruktúrájuk megbízhatóak. Ha a figyelést a teljes infrastruktúrán keresztül engedélyezte, a teljes körű monitorozást tesz lehetővé, és megkönnyíti a lehetséges kiváltó ok felderítését, ha valami meghiúsul. Azure Monitor segítségével nyomon követheti a teljes hibrid infrastruktúra állapotát és teljesítményét, beleértve a virtuális gépeket, a tárolókat, a tárolást és a hálózatot.

- A [platform metrikáit, a tevékenységek naplóit és a diagnosztikai naplókat](agents/data-sources.md) a konfiguráció nélkül, a legtöbb Azure-erőforrásból automatikusan beolvashatja.
- Lehetővé teszi a virtuális gépek mélyebb figyelését a [VM](vm/vminsights-overview.md)-elemzésekkel.
-  A [Azure monitor for containers esetében](containers/container-insights-overview.md)lehetővé teszi az AK-fürtök mélyebb figyelését.
- [Figyelési megoldások](./monitor-reference.md) hozzáadása a környezet különböző alkalmazásaihoz és szolgáltatásaihoz.


Az [infrastruktúra mint kód](/azure/devops/learn/what-is-infrastructure-as-code) az infrastruktúra kezelése egy leíró modellben, amely ugyanazt a verziószámozást használja, mint a DevOps Teams a forráskódot. Megbízhatóságot és méretezhetőséget biztosít a környezet számára, és lehetővé teszi az alkalmazások kezeléséhez használt hasonló folyamatok kihasználása.

-  A [Resource Manager-sablonok](./logs/resource-manager-workspace.md) használatával engedélyezheti a figyelést, és konfigurálhatja a riasztásokat egy nagy erőforrás-készleten keresztül.
- A [Azure Policy](../governance/policy/overview.md) használatával különböző szabályokat alkalmazhat az erőforrásokra. Ez biztosítja, hogy ezek az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatói szerződéseknek. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Erőforrások egyesítése Azure-erőforráscsoportok
Az Azure-ban egy tipikus alkalmazás több olyan erőforrást tartalmaz, mint például a virtuális gépek és a App Services Cloud Services, AK-alapú fürtökön vagy Service Fabricokon üzemeltetett szolgáltatások. Ezek az alkalmazások gyakran használják a függőségeket, például a Event Hubs, a Storage, az SQL és a Service Bus.

- Az Azure-erőforráscsoportok erőforrásainak egyesítésével teljes mértékben megtekintheti a különböző alkalmazásokat alkotó összes erőforrást. Az [erőforráscsoportok Azure monitor](./insights/resource-group-insights.md) egyszerű módszert biztosít a teljes teljes verembeli alkalmazás állapotának és teljesítményének nyomon követésére, és lehetővé teszi, hogy bármilyen vizsgálat vagy hibakeresés céljából lebontsa a megfelelő összetevőket.

## <a name="ensure-quality-through-continuous-deployment"></a>A minőség biztosítása folyamatos üzembe helyezéssel
A folyamatos integráció/folyamatos üzembe helyezés lehetővé teszi, hogy automatikusan integrálja és telepítse a kód módosításait az alkalmazásba az automatizált tesztelés eredményei alapján. Egyszerűsíti az üzembe helyezés folyamatát, és gondoskodik arról, hogy az éles környezetbe való áttérés előtt a változások minősége is megtörténjen.


- Az [Azure](/azure/devops/pipelines) -folyamatok segítségével folyamatos üzembe helyezést valósíthat meg, és automatizálhatja a teljes folyamatot a kód véglegesítve a CI/CD-tesztek alapján.
- A [minőségi kapuk](/azure/devops/pipelines/release/approvals/gates) használatával integrálhatja a figyelést az üzembe helyezés előtti vagy utáni üzembe helyezésbe. Ez biztosítja, hogy a kulcsfontosságú állapot-/teljesítmény-metrikák (KPI-k) megfelelnek az alkalmazásoknak, mivel az alkalmazások a fejlesztéstől a Termelésig mozognak, és az infrastruktúra környezetében vagy a skálázásban mutatkozó különbségek nem negatív hatással vannak a KPI-re.
- A különböző üzembe helyezési környezetek, például a dev, a test, a Canary és a Prod közötti [különálló figyelési példányok karbantartása](./app/separate-resources.md) . Ez biztosítja, hogy az összegyűjtött adatok relevánsak legyenek a kapcsolódó alkalmazásokban és infrastruktúrában. Ha különböző környezetekben szeretné összekapcsolni az adatforrásokat, használhat [több erőforrást tartalmazó diagramokat a Metrikaböngészőban](./essentials/metrics-charts.md) , vagy létrehozhat [több erőforrásból álló lekérdezéseket Azure monitorokban](logs/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Végrehajtható riasztások létrehozása műveletekkel
A figyelés kritikus aspektusa proaktív módon értesíti a rendszergazdákat az aktuális és az előre jelzett problémákról. 

- [Riasztásokat](./alerts/alerts-overview.md) hozhat létre Azure monitor naplók és metrikák alapján a kiszámítható meghibásodási állapotok azonosításához. Az összes riasztást úgy kell kipróbálni, hogy a tényleges kritikus feltételeknek megfelelőek legyenek, és hogy csökkentsék a téves pozitív állapotot. A [dinamikus](alerts/alerts-dynamic-thresholds.md) küszöbértékek használatával automatikusan kiszámíthatja a metrikus adatok alapterveit, és nem határozhatja meg a saját statikus küszöbértékeit. 
- A riasztásokra vonatkozó műveletek definiálása a leghatékonyabb módon a rendszergazdák értesítéséhez. [Az értesítésekhez elérhető műveletek az](alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) SMS, az e-mailek, a leküldéses értesítések vagy a hanghívások.
- További speciális műveletekkel [csatlakozhat a ITSM eszközhöz](alerts/itsmc-overview.md) vagy más riasztási felügyeleti rendszerekhez [webhookok](alerts/activity-log-alerts-webhook.md)használatával.
- A riasztásokban azonosított helyzetek javítása, valamint [Azure Automation runbookok](../automation/automation-webhooks.md) vagy [Logic apps](/connectors/custom-connectors/create-webhook-trigger) , amely webhookok használatával indítható riasztásból. 
- Az automatikus [skálázás](./autoscale/tutorial-autoscale-performance-schedule.md) használatával dinamikusan növelheti és csökkentheti a számítási erőforrásokat az összegyűjtött mérőszámok alapján.

## <a name="prepare-dashboards-and-workbooks"></a>Irányítópultok és munkafüzetek előkészítése
Annak biztosítása, hogy a fejlesztési és üzemeltetési műveletek azonos telemetria és eszközökhöz férjenek hozzá, lehetővé teszi, hogy a teljes környezetben megtekintsék a mintákat, és a lehető legkevesebb időt mutassanak a (MTTD) és a visszaállítási idő (MTTR) számára.

- Készítse elő az [Egyéni irányítópultokat](./app/tutorial-app-dashboards.md) a szervezet különböző szerepköreihez tartozó gyakori mérőszámok és naplók alapján. Az irányítópultok az összes Azure-erőforrásból is kombinálhatók.
- Készítse elő a [munkafüzetek](./visualize/workbooks-overview.md) előkészítését a fejlesztés és a műveletek közötti tudásmegosztás biztosításához. Ezek a mérőszám-diagramok és a naplók lekérdezésével, vagy akár a fejlesztők által készített hibaelhárítási útmutatókkal is készíthetők, amelyek segítségével az ügyfelek támogatását vagy műveleteit az alapvető problémák kezelésére lehet használni.

## <a name="continuously-optimize"></a>Folyamatos optimalizálás
 A monitorozás a népszerű Build-mérték – Learn filozófia egyik alapvető aspektusa, amely a KPI-k és a felhasználói viselkedési mérőszámok folyamatos nyomon követését javasolja, majd a tervezési iterációk segítségével optimalizálja őket. A Azure Monitor segítséget nyújt a vállalata számára releváns mérőszámok és naplók összegyűjtésében, valamint új adatpontok hozzáadásában a szükséges módon.

- A Application Insights eszközeit használva [nyomon követheti a végfelhasználói viselkedést és a részvételt](./app/tutorial-users.md).
- Az [Impact Analysis](./app/usage-impact.md) segítségével rangsorolhatja, hogy mely területek összpontosítanak a fontos KPI-k elérésére.


## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor](overview.md)különbségi összetevőiről.
- [Vegyen fel folyamatos figyelést](./app/continuous-monitoring.md) a kiadási folyamatba.