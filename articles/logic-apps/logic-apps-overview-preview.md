---
title: Az Azure Logic Apps előzetes verziójának áttekintése
description: A Azure Logic Apps Preview egy felhőalapú megoldás az olyan automatizált, egybérlős, állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozásához, amelyek alkalmazások, adatok, szolgáltatások és rendszerek integrálását teszik elérhetővé minimális kóddal a nagyvállalati szintű forgatókönyvek esetében.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 7120b6ff17657232c0e614f49b75bb24263712b7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636335"
---
# <a name="overview-azure-logic-apps-preview"></a>Áttekintés: Azure Logic Apps előzetes verzió

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Logic Apps előzetes verzióban automatizálási és integrációs megoldásokat hozhat létre az alkalmazások, az erőforrások, a Cloud Services és a rendszerek között azáltal, hogy létrehoz és futtat egybérlős logikai alkalmazásokat az új **logikai alkalmazás (előzetes verzió)** típusú erőforrástípus használatával. Az egybérlős logikai alkalmazás típusának használatával több [ *állapot* -és *állapot* nélküli munkafolyamatot](#stateful-stateless) építhet ki, amelyek az újratervezett Azure Logic apps előzetes verzióban futnak, ami hordozhatóságot, jobb teljesítményt és rugalmasságot biztosít különböző üzemeltetési környezetek üzembe helyezéséhez és futtatásához, beleértve a nem csak az Azure-t, hanem a Docker-tárolókat is.

Hogyan lehetséges ez? Az újratervezett futtatókörnyezet a [Azure functions bővíthetőségi modellt](../azure-functions/functions-bindings-register.md) használja, és bővítményként fut a Azure functions futtatókörnyezetben. Ez az architektúra azt jelenti, hogy az egybérlős logikai alkalmazás típusát bárhol futtathatja, ahol a Azure Functions fut. Az újratervezett futtatókörnyezetet szinte bármilyen hálózati topológián üzemeltetheti, és a munkafolyamatok által igényelt szükséges munkaterhelés kezeléséhez kiválaszthatja a rendelkezésre álló számítási méreteket is. További információ: [bevezetés Azure functions](../azure-functions/functions-overview.md) és [Azure functions eseményindítók és kötések](../azure-functions/functions-triggers-bindings.md)használatába.

A **logikai alkalmazás (előzetes verzió)** erőforrását a [Azure Portal indításával](create-stateful-stateless-workflows-azure-portal.md) vagy a [Visual Studio Code-ban a Azure Logic apps (előzetes verzió) bővítménnyel létrehozott projekt létrehozásával](create-stateful-stateless-workflows-visual-studio-code.md)hozhatja létre. A Visual Studio Code-ban a munkafolyamatokat a fejlesztői környezetben is létrehozhatja *és helyileg futtathatja* . Függetlenül attól, hogy a portált vagy a Visual Studio Code-ot használja-e, ugyanúgy telepítheti és futtathatja az egybérlős logikai alkalmazások típusát, mint a különböző üzemeltetési környezetekben.

Ez az Áttekintés a következő területekre terjed ki:

* [Azure Logic apps előzetes verzió, a Azure Logic apps több-bérlős környezet és az integrációs szolgáltatási környezet közötti különbségek](#preview-differences).

* [Állapot-nyilvántartó és állapot nélküli munkafolyamatok közötti különbségek](#stateful-stateless), beleértve a [beágyazott állapotú és állapot nélküli munkafolyamatok](#nested-behavior)közötti viselkedési különbségeket.

* A [nyilvános előzetes verzió képességei](#public-preview-contents).

* [A díjszabási modell működése](#pricing-model).

* [Módosított, korlátozott, nem elérhető vagy nem támogatott képességek](#limited-unavailable-unsupported).

* [Korlátok Azure Logic apps előzetes](#limits)verzióban.

További információkért tekintse át a következő témaköröket:

* [A Azure Logic Apps bárhol fut a futás közben](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps nyilvános előzetes verzió ismert problémái (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Miben különbözik Azure Logic Apps előnézet?

A Azure Logic Apps előzetes verzió futtatókörnyezete [Azure functions](../azure-functions/functions-overview.md) bővíthetőséget használ, és bővítményként fut a Azure functions futtatókörnyezetben. Ez az architektúra azt jelenti, hogy az egybérlős logikai alkalmazás típusát bárhol futtathatja, ahol a Azure Functions fut. Szinte bármilyen hálózati topológián üzemeltetheti a Azure Logic Apps előnézeti futtatókörnyezetet, és kiválaszthatja az összes rendelkezésre álló számítási méretet, hogy kezelni tudja a munkafolyamat által igényelt szükséges munkaterhelést. A Azure Functions bővíthetőségéről a [Webjobs SDK: egyéni bemeneti és kimeneti kötések létrehozása](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)című témakörben olvashat bővebben.

Ezzel az új megközelítéssel a Azure Logic Apps előnézeti futtatókörnyezet és a munkafolyamatok is az alkalmazás részét képezik, amelyet együtt lehet csomagolni. Ez a funkció lehetővé teszi a munkafolyamatok üzembe helyezését és futtatását azáltal, hogy egyszerűen másolja az összetevőket az üzemeltetési környezetbe, és elindítja az alkalmazást. Ez a megközelítés egy szabványosított megoldást is biztosít a telepítési folyamatok létrehozásához a munkafolyamat-projektekben a szükséges tesztek és érvényesítések futtatásához, mielőtt telepítené a módosításokat az éles környezetekben. További információkért lásd: [Azure Logic apps fut bárhol a futtatókörnyezetben](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

Az alábbi táblázat röviden összefoglalja, hogy a munkafolyamatok milyen módon osztják meg az erőforrásokat a futtatott környezet alapján. A határértékekkel kapcsolatos különbségekért tekintse [meg a korlátozásokat Azure Logic apps előzetes](#limits)verzióban.

| Környezet | Erőforrás-megosztás és-felhasználás |
|-------------|----------------------------------|
| Azure Logic Apps (több-bérlős) | Az *ügyfelektől több bérlőn belüli* munkafolyamatok ugyanazt a feldolgozást (számítást), tárhelyet, hálózatot és így tovább használják. |
| Azure Logic Apps (előzetes verzió, egybérlős) | *Az azonos logikai alkalmazásban és egyetlen bérlőn belüli* munkafolyamatok ugyanazt a feldolgozást (számítást), tárhelyet, hálózatot stb. használják. |
| Integrációs szolgáltatási környezet (előzetes verzióban nem érhető el) | Az *ugyanabban a környezetben* lévő munkafolyamatok ugyanazt a feldolgozást (számítást), tárhelyet, hálózatot és egyebeket használják. |
|||

Eközben a több-bérlős logikai alkalmazás típusát is létrehozhatja a Azure Portal és a Visual Studio Code-ban a több-bérlős Azure Logic Apps bővítmény használatával. Bár a fejlesztési tapasztalatok különböznek a több-bérlős és az egybérlős logikai alkalmazások típusaitól, az Azure-előfizetések mindkét típust tartalmazhatják. Az Azure-előfizetésben megtekintheti és elérheti az összes telepített Logic apps-alkalmazást, de az alkalmazások a saját kategóriákban és szakaszban vannak rendszerezve.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Állapot-nyilvántartó és állapot nélküli munkafolyamatok

Az egybérlős logikai alkalmazás típusa esetén a következő munkafolyamat-típusok hozhatók létre ugyanazon logikai alkalmazáson belül:

* *Állapotalapú*

  Állapot-nyilvántartó munkafolyamatokat hozhat létre, ha az előző eseményekből származó adatok megtartására, felülvizsgálatára vagy hivatkozására van szüksége. Ezek a munkafolyamatok mentik az egyes műveletek bemeneteit és kimeneteit, valamint azok állapotát a külső tárolóban, ami az egyes futtatások befejezése után a Futtatás részleteinek és előzményeinek áttekintését teszi lehetővé. Az állapot-nyilvántartó munkafolyamatok nagy rugalmasságot biztosítanak, ha kimaradások történnek. A szolgáltatások és a rendszerek visszaállítása után megszakított futtatásokat állíthat össze a mentett állapotból, és újra futtathatja a munkafolyamatokat a befejezéshez. Az állapot-nyilvántartó munkafolyamatok akár egy évig is futhatnak.

* *Állapot nélküli*

  Állapot nélküli munkafolyamatokat hozhat létre, ha nem kell a külső tárolóban lévő korábbi eseményekből származó adatok mentését, felülvizsgálatát vagy hivatkozását későbbi ellenőrzés céljából. Ezek a munkafolyamatok *csak a memóriában* mentik el az egyes műveletek bemeneteit és kimeneteit, és nem továbbítják ezeket az adatokat külső tárolóba. Ennek eredményeképpen az állapot nélküli munkafolyamatok rövidebb ideig tartanak, amelyek általában nem 5 percnél hosszabbak, gyorsabbak a gyorsabb válaszidő, a nagyobb átviteli sebesség és a csökkenő működési költségek, mivel a futtatási adatok és előzmények nem a külső tárolóban maradnak. Ha azonban az kimaradások történnek, a megszakított futtatások nem állnak helyre automatikusan, így a hívónak manuálisan kell újraküldenie a megszakított futtatásokat. Ezek a munkafolyamatok csak szinkron módon futtathatók.

  A könnyebb hibakeresés érdekében engedélyezheti a futtatási előzményeket egy állapot nélküli munkafolyamathoz, amely hatással van a teljesítményre, majd letilthatja a futtatási előzményeket, ha elkészült. További információ: állapot-nyilvántartó [és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) , illetve állapot-nyilvántartó [és állapot nélküli munkafolyamatok létrehozása a Azure Portalban](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Az állapot nélküli munkafolyamatok jelenleg csak az Azure-ban üzembe helyezett [felügyelt összekötők](../connectors/apis-list.md#managed-api-connectors) *műveleteit* támogatják, és nem indítják el azokat. A munkafolyamat elindításához válassza a [beépített kérés, Event Hubs vagy Service Bus trigger](../connectors/apis-list.md#built-ins)lehetőséget. Ezek az eseményindítók natív módon futnak a Azure Logic Apps előzetes verziójának futtatókörnyezetében. A korlátozott, nem elérhető vagy nem támogatott eseményindítókkal, műveletekkel és összekötővel kapcsolatos további információkért lásd: [módosított, korlátozott, nem elérhető vagy nem támogatott képességek](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Az állapot-nyilvántartó és az állapot nélküli munkafolyamatok közötti beágyazott viselkedési különbségek

A munkafolyamatok [meghívhatók](../logic-apps/logic-apps-http-endpoint.md) más munkafolyamatokból is, amelyek ugyanabban a **logikai alkalmazásban (előzetes verzió)** találhatók, mint a [kérelem eseményindítója](../connectors/connectors-native-reqres.md), a [http webhook eseményindítója](../connectors/connectors-native-webhook.md)vagy a felügyelt összekötő eseményindítói, amelyek a [ApiConnectionWebhook típussal](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) rendelkeznek, és HTTPS-kéréseket fogadhatnak.

Az alábbiakban láthatók azok a viselkedési minták, amelyeket a beágyazott munkafolyamatok követhetnek, miután egy szülő munkafolyamat meghívja a gyermek munkafolyamatot:

* Aszinkron lekérdezési minta

  A szülő nem vár választ a kezdeti hívásra, de folyamatosan ellenőrzi a gyermek futási előzményeit, amíg a gyermek befejezi a futást. Alapértelmezés szerint az állapot-nyilvántartó munkafolyamatok ezt a mintát követik, ami ideális olyan hosszú ideig futó alárendelt munkafolyamatokhoz, amelyek túllépik a [kérelmek időtúllépési korlátait](../logic-apps/logic-apps-limits-and-config.md).

* Szinkron minta ("tűz és felejtsd el")

  A gyermek visszaigazolja a hívást, hogy azonnal visszaadja a `202 ACCEPTED` választ, és a szülő továbbra is a következő műveletre vár anélkül, hogy meg kellene várnia a gyermek eredményét. Ehelyett a szülő megkapja az eredményeket, amikor a gyermek befejezi a futását. Olyan alárendelt állapot-nyilvántartó munkafolyamatok, amelyek nem tartalmazzák a válasz műveletet, mindig a szinkron mintát kövessék. Gyermek állapot-nyilvántartó munkafolyamatok esetén a futtatási előzmények elérhetők az áttekintéshez.

  A viselkedés engedélyezéséhez a munkafolyamat JSON-definíciójában állítsa be a tulajdonságot a következőre: `operationOptions` `DisableAsyncPattern` . További információ: [trigger-és Művelettípus – műveleti beállítások](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Trigger és várakozás

  Gyermek állapot nélküli munkafolyamathoz a szülő vár egy olyan választ, amely visszaadja az eredményeket a gyermektől. Ez a minta hasonló módon működik, mint a beépített [http-trigger vagy művelet](../connectors/connectors-native-http.md) , amely egy alárendelt munkafolyamatot hív meg. Olyan alárendelt állapot nélküli munkafolyamatok, amelyek nem tartalmaznak választ azonnal választ adnak vissza `202 ACCEPTED` , de a szülő megvárja, amíg a gyermek befejeződik a következő művelet folytatása előtt. Ezek a viselkedés csak alárendelt állapot nélküli munkafolyamatokra érvényesek.

Ez a tábla a gyermek munkafolyamatok viselkedését határozza meg attól függően, hogy a szülő és a gyermek állapota állapotú, állapot nélküli vagy vegyes munkafolyamat-típusok-e:

| Szülő munkafolyamat | Gyermek munkafolyamat | Gyermek viselkedése |
|-----------------|----------------|----------------|
| Állapotalapú | Állapotalapú | Aszinkron vagy szinkron `"operationOptions": "DisableAsyncPattern"` beállítással |
| Állapotalapú | Állapot nélküli | Trigger és várakozás |
| Állapot nélküli | Állapotalapú | Szinkron |
| Állapot nélküli | Állapot nélküli | Trigger és várakozás |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Képességek

A Azure Logic Apps Preview számos aktuális és további funkciót tartalmaz, például:

* Hozzon létre logikai alkalmazásokat és azok munkafolyamatait [400 + összekötőből](/connectors/connector-reference/connector-reference-logicapps-connectors) a szolgáltatott szoftver (SaaS) és a szolgáltatásként nyújtott platform (Pásti) alkalmazásaihoz és szolgáltatásaihoz, valamint a helyszíni rendszerekhez.

  * Néhány felügyelt összekötő már elérhető beépített verzióként, amely hasonlóan fut a beépített triggerekhez és műveletekhez, például a kérelem eseményindítója és a HTTP művelet, amely natív módon fut a Azure Logic Apps előnézet futtatókörnyezetben. Ezek az új beépített összekötők például a következők: Azure Service Bus, Azure Event Hubs, SQL Server és MQ.

    > [!NOTE]
    > A beépített SQL Server összekötő esetében csak a **lekérdezés végrehajtása** művelet tud közvetlenül csatlakozni az Azure virtuális hálózatokhoz anélkül, hogy a helyszíni [adatátjáróra](logic-apps-gateway-connection.md)lenne szükség.

  * Hozzon létre saját beépített összekötőket minden szükséges szolgáltatáshoz az [előzetes verzió bővíthetőségi keretrendszerének](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)használatával. A beépített összekötők, például a Azure Service Bus és a SQL Server esetében, de az előzetes verzióban jelenleg nem támogatott [Egyéni összekötők](../connectors/apis-list.md#custom-apis-and-connectors) eltérően ezek az összekötők magasabb átviteli sebességet, kis késleltetést, helyi kapcsolatot biztosítanak, és natív módon futnak az előzetes verziójú futtatókörnyezettel azonos folyamatban.

    A szerzői művelet jelenleg csak a Visual Studio Code-ban érhető el, de alapértelmezés szerint nincs engedélyezve. Ezeknek az összekötőknek a létrehozásához [állítsa át a projektet a csomag-alapú (Node.js) NuGet Package-based (.net) értékre](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring). További információ: [Azure Logic apps futó, bárhol beépített összekötő bővíthetősége](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Integrációs fiók nélkül is használhatja a VÁLLALATKÖZI műveleteket a folyékony műveletekhez és az XML-műveletekhez. Ezeknek a műveleteknek a használatához olyan likvid térképeket, XML-térképeket vagy XML-sémákat kell használni, amelyeket fel lehet tölteni a Azure Portalban található megfelelő műveletekkel, vagy hozzáadhat a Visual Studio Code Project összetevőinek mappájához a megfelelő **térképek** és **sémák** **mappák használatával** .

  * Olyan logikai alkalmazásokat hozhat létre és helyezhet üzembe, amelyek bárhol futhatnak, mivel a Azure Logic Apps szolgáltatás közös hozzáférésű aláírási (SAS) kapcsolati karakterláncokat hoz létre, amelyeket ezek a logikai alkalmazások a felhőalapú kapcsolati futtatókörnyezet végpontjának küldött kérelmek küldésére használhatnak. A Logic Apps szolgáltatás menti ezeket a kapcsolódási karakterláncokat más Alkalmazásbeállítások segítségével, így egyszerűen tárolhatja ezeket az értékeket Azure Key Vault az Azure-ban való üzembe helyezéskor.

    > [!NOTE]
    > Alapértelmezés szerint a **logikai alkalmazás (előzetes verzió)** erőforrásának a [rendszer által hozzárendelt felügyelt identitása](../logic-apps/create-managed-service-identity.md) automatikusan engedélyezve van a kapcsolatok futásidőben történő hitelesítéséhez. Ez az identitás különbözik a kapcsolatok létrehozásakor használt hitelesítő adatoktól vagy a kapcsolódási karakterlánctól. Ha letiltja ezt az identitást, a kapcsolatok nem működnek futásidőben. A beállítás megtekintéséhez a logikai alkalmazás menüjében, a **Beállítások** területen válassza az **identitás** elemet.

* Logikai alkalmazásokat hozhat létre olyan állapot nélküli munkafolyamatokkal, amelyek csak a memóriában futnak, így gyorsabban, gyorsabban, nagyobb átviteli sebességgel és kevesebb ideig futnak, mivel a futtatási előzmények és a műveletek közötti adatok nem maradnak meg a külső tárolóban. Igény szerint engedélyezheti a futtatási előzményeket a hibakereséshez. További információ: állapot-nyilvántartó és [állapot nélküli Logic apps](#stateful-stateless).

* A Logic apps és a munkafolyamataik helyi futtatása, tesztelése és hibakeresése a Visual Studio Code fejlesztői környezetben.

  A logikai alkalmazás futtatása és tesztelése előtt a hibakeresést egyszerűbbé teheti a munkafolyamatok fájljának **workflow.jsján** belüli töréspontok hozzáadásával és használatával. A töréspontok azonban csak olyan műveletek esetén támogatottak, amelyek nem eseményindítók. További információ: állapot-nyilvántartó [és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* A Logic apps és a munkafolyamataik közvetlen közzététele vagy üzembe helyezése a Visual Studio Code-ból különböző üzemeltetési környezetekben, például az Azure-ban és a [Docker-tárolókban](/dotnet/core/docker/introduction).

* Engedélyezze a diagnosztikai naplózási és nyomkövetési funkciókat a logikai alkalmazáshoz, ha az Azure-előfizetése és a logikai alkalmazás beállításai támogatják a [Application Insights](../azure-monitor/app/app-insights-overview.md) .

* A hálózati képességek, például a csatlakozás és az Azure Virtual Networks szolgáltatással való integráció biztosítása, hasonlóan a Azure Functionshoz, amikor a logikai alkalmazásokat a [Azure functions Premium csomaggal](../azure-functions/functions-premium-plan.md)hozza létre és telepíti. További információkért tekintse át a következő témaköröket:

  * [Az Azure Functions hálózatkezelési lehetőségei](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps a bárhonnan hálózati lehetőségeket a Azure Logic Apps előzetes verziójával](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Az egybérlős **logikai alkalmazás (előzetes verzió)** erőforrásaiban az egyes munkafolyamatok által használt felügyelt kapcsolatok elérési kulcsainak újragenerálása. Ehhez a feladathoz [kövesse ugyanezen lépéseket a több-bérlős **Logic apps** erőforráshoz, de az egyes munkafolyamatok szintjén](logic-apps-securing-a-logic-app.md#regenerate-access-keys), ne a logikai alkalmazás erőforrás szintjén.

* A több-bérlős tervezővel megegyező lépéseket követve vegyen fel párhuzamos ágakat az egybérlős tervezőben.

További információ: [módosított, korlátozott, nem elérhető és nem támogatott képességek](#limited-unavailable-unsupported) és a [Logic apps nyilvános előzetes verzió ismert problémák lapja a githubon](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Díjszabási modell

Ha létrehoz egy egybérlős logikai alkalmazást a Azure Portal vagy a Visual Studio Code-ból való üzembe helyezést, akkor a logikai alkalmazás használatához ki kell választania egy [app Service vagy prémium](../azure-functions/functions-scale.md)szintű üzemeltetési csomagot. Ez a csomag határozza meg a logikai alkalmazás futtatására érvényes díjszabási modellt. Ha kijelöli a App Service csomagot, ki kell választania egy [árképzési szintet](../app-service/overview-hosting-plans.md)is.

Az *állapot-nyilvántartó* munkafolyamatok [külső tárterületet](../azure-functions/storage-considerations.md#storage-account-requirements)használnak, ezért az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/) a Azure Logic apps előnézeti futtatókörnyezet által végrehajtott tárolási tranzakciókra vonatkozik. A várólisták például az ütemezéshez használatosak, míg a rendszer a táblákat és a blobokat használja a Munkafolyamat-állapotok tárolásához.

> [!NOTE]
> A nyilvános előzetes verzióban a logikai alkalmazások App Serviceon való futtatása nem jár *további* költségekkel a kiválasztott csomag felett.

Az egybérlős erőforrástípus esetében alkalmazandó díjszabási modellekkel kapcsolatos további információkért tekintse át a következő témaköröket:

* [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)
* [Alkalmazás vertikális felskálázása Azure App Service](../app-service/manage-scale-up.md)
* [A Azure Functions díjszabása](https://azure.microsoft.com/pricing/details/functions/)
* [A App Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/)
* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Módosított, korlátozott, nem elérhető vagy nem támogatott képességek

Azure Logic Apps előzetes verzióban ezek a funkciók megváltoztak, vagy jelenleg korlátozottak, nem érhetők el vagy nem támogatottak:

* **Operációs rendszer támogatása**: a Visual Studio Code-ban a tervező jelenleg nem működik Linux operációs rendszeren, de továbbra is telepíthet olyan logikai alkalmazásokat, amelyek a Logic apps előnézet futtatókörnyezetet használják a Linux-alapú virtuális gépekhez. Egyelőre a Visual Studio Code-ban hozhat létre logikai alkalmazásokat Windows vagy macOS rendszeren, majd üzembe helyezheti azt egy Linux-alapú virtuális gépen.

* **Eseményindítók és műveletek**: a beépített eseményindítók és műveletek natív módon futnak a Azure Logic apps előnézet futtatókörnyezetben, míg a felügyelt összekötők üzembe helyezése az Azure-ban történik. Néhány beépített eseményindító nem érhető el, például a csúszó ablak és a Batch.

  A munkafolyamat elindításához használja a [beépített ismétlődés, kérelem, http, http webhook, Event Hubs vagy Service Bus eseményindítót](../connectors/apis-list.md). A tervezőben a beépített eseményindítók és műveletek a **beépített** lapon jelennek meg, míg a felügyelt összekötő-eseményindítók és műveletek az **Azure** lapon jelennek meg.

  > [!NOTE]
  > Ha helyileg szeretné futtatni a Visual Studio Code-ban, a webhook-alapú eseményindítók és műveletek további telepítést igényelnek. További információ: állapot-nyilvántartó [és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * *Állapot nélküli munkafolyamatok* esetén az **Azure** lap nem jelenik meg, ha kijelöl egy eseményindítót, mert csak a [felügyelt összekötők *műveleteit*](../connectors/apis-list.md#managed-api-connectors)lehet kijelölni, nem pedig eseményindítókat. Bár az állapot nélküli munkafolyamatokhoz engedélyezheti az Azure által telepített felügyelt összekötőket, a tervező nem jeleníti meg a felügyelt összekötő-eseményindítókat a hozzáadáshoz.

  * Az olyan *állapot-nyilvántartó munkafolyamatok* esetében, amelyek nem az alább felsorolt eseményindítókat és műveleteket használják, a [felügyelt összekötő-eseményindítók és műveletek](../connectors/apis-list.md#managed-api-connectors) is használhatók.

  * Ezek az eseményindítók és műveletek módosítva lettek, vagy jelenleg korlátozottak, nem támogatottak vagy nem érhetők el:

    * [A helyszíni adatátjáró- *Eseményindítók*](../connectors/apis-list.md#on-premises-connectors) nem érhetők el, *de az átjáró műveletei* elérhetők.

    * A beépített művelet, [Azure functions – válasszon ki egy Azure-](logic-apps-azure-functions.md) függvényt mostantól **Azure Function Operations – hívjon egy Azure-függvényt**. Ez a művelet jelenleg csak a **http-trigger** sablonból létrehozott függvények esetében működik.

      A Azure Portalban kiválaszthat egy HTTP-trigger függvényt, amelyhez hozzáférése van a felhasználói felületén keresztüli kapcsolat létrehozásával. Ha megvizsgálja a Function művelet JSON-definícióját a kód nézetben vagy a **workflow.js** fájlon, a művelet hivatkozás használatával hivatkozik a függvényre `connectionName` . Ez a verzió összevonta a függvény információit a kapcsolatok között, amelyet a projekt **connections.js** talál a fájlon, amely a kapcsolatok létrehozása után érhető el.

      > [!NOTE]
      > Az egybérlős verzióban a függvény művelet csak a lekérdezési karakterláncok hitelesítését támogatja. Azure Logic Apps az előnézet beolvassa az alapértelmezett kulcsot a függvényből a kapcsolódás során, tárolja a kulcsot az alkalmazás beállításaiban, és a kulcs használatával használja a hitelesítést a függvény hívásakor.
      >
      > A több-bérlős verzióhoz hasonlóan, ha megújítja ezt a kulcsot, például a portálon a Azure Functions felületén keresztül, a függvény művelete az érvénytelen kulcs miatt már nem működik. A probléma megoldásához újra létre kell hoznia a kapcsolódást ahhoz a függvényhez, amelyet az új kulccsal szeretne meghívni vagy frissíteni az alkalmazás beállításait.

    * A beépített művelet, a [beágyazott kód-végrehajtás JavaScript-kód](logic-apps-add-run-inline-code.md) mostantól beágyazott **kód-művelet – online JavaScript futtatása**.

      * A beágyazott kód műveleti műveleteihez már nincs szükség integrációs fiókra.

      * MacOS és Linux esetén a **beágyazott kód műveletei** mostantól támogatottak, ha a Visual Studio Code-ban a Azure Logic apps (előzetes verzió) bővítményt használja.

      * Nem kell újraindítania a logikai alkalmazást, ha változtatásokat hajt végre egy **beágyazott kód műveleti** műveletében.

      * A **beágyazott kód műveleti** műveleteinek [frissített korlátai](logic-apps-overview-preview.md#inline-code-limits)vannak.

    * Az [integrációs fiókokhoz tartozó beépített B2B-eseményindítók és műveletek](../connectors/apis-list.md#integration-account-connectors) nem érhetők el, például a **sima fájl** kódolása és a dekódolási műveletek.

    * A beépített művelet, [Azure Logic apps – a logikai alkalmazás munkafolyamatának kiválasztása](logic-apps-http-endpoint.md) most **munkafolyamat-műveletek – munkafolyamat meghívása ebben a munkafolyamat-alkalmazásban**.

* Az [Egyéni összekötők](../connectors/apis-list.md#custom-apis-and-connectors) jelenleg előzetes verzióban nem támogatottak.

* **Üzemeltetési csomag rendelkezésre állása**: az egybérlős **logikai alkalmazás (előzetes verzió)** típusú erőforrástípus létrehozása a Azure Portal vagy a Visual Studio Code-ból való üzembe helyezés esetén csak a prémium vagy a app Service üzemeltetési csomagot használhatja az Azure-ban. A felhasználási üzemeltetési csomagok nem érhetők el, és nem támogatottak az erőforrástípus üzembe helyezése során. A Visual Studio Code-ból telepítheti a Docker-tárolót, az [integrációs szolgáltatási környezetre (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)azonban nem.

* **Töréspontok hibakeresése a Visual Studio Code-ban**: bár a munkafolyamatok fájlján belül a **workflow.json** töréspontokat adhat hozzá és használhat, a töréspontok csak a jelenleg végrehajtott műveletekhez támogatottak, nem eseményindítók. További információ: állapot-nyilvántartó [és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Nagyítás vezérlő**: a nagyítás vezérlő jelenleg nem érhető el a tervezőben.

* **Trigger-előzmények és-futtatási előzmények**: a **logikai alkalmazás (előzetes verzió)** erőforrástípus, az előzmények és a futtatási előzmények a Azure Portal a munkafolyamat szintjén, nem a logikai alkalmazás szintjén jelennek meg. A korábbi adatértékek megkereséséhez kövesse az alábbi lépéseket:

   * A futtatási előzmények megtekintéséhez nyissa meg a munkafolyamatot a logikai alkalmazásban. A munkafolyamat menü **fejlesztő** területén válassza a **figyelés** elemet.

   * Az aktiválási előzmények áttekintéséhez nyissa meg a munkafolyamatot a logikai alkalmazásban. A munkafolyamat menü **fejlesztői** területén válassza az **aktiválási előzmények** elemet.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>A forgalom engedélyezése szigorú hálózati és tűzfal-forgatókönyvekben

Ha a környezete szigorú hálózati követelményekkel vagy a forgalmat korlátozó tűzfalakkal rendelkezik, a logikai alkalmazás munkafolyamataiban engedélyeznie kell a hozzáférést minden trigger-vagy művelet-kapcsolathoz.

A kapcsolatok teljes tartománynevének (FQDN) megkereséséhez tekintse át a következő témakörökben található megfelelő szakaszt:

* [Tűzfal engedélyei az egybérlős logikai alkalmazásokhoz – Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Tűzfal engedélyei az egybérlős logikai alkalmazásokhoz – Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Frissített korlátok

Bár a Azure Logic Apps előnézet számos korlátja ugyanaz marad, mint a [több-bérlős Azure Logic apps korlátai](logic-apps-limits-and-config.md), a Azure Logic apps előzetes verziójának korlátai megváltoztak.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP-időkorlát korlátai

Egyetlen bejövő hívás vagy kimenő hívás esetén az időtúllépési korlát 230 másodperc (3,9 perc) a következő eseményindítók és műveletek esetén:

* Kimenő kérelem: HTTP-trigger, HTTP-művelet
* Bejövő kérelem: trigger kérelmezése, HTTP webhook-trigger, HTTP-webhook művelet

Ezzel szemben az eseményindítók és más környezetekben a Logic apps és a munkafolyamatok futtatásával kapcsolatos időtúllépési korlátok:

* Több-bérlős Azure Logic Apps: 120 másodperc (2 perc)
* Integrációs szolgáltatási környezet: 240 másodperc (4 perc)

További információ: http- [korlátok](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Felügyelt összekötők

A felügyelt összekötők kapcsolatonként percenként 50 kérelemre korlátozódnak. Az összekötők szabályozásával kapcsolatos problémák megoldásához lásd: [a szabályozási problémák kezelése (429 – "túl sok kérés" hiba) a Azure Logic apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Beágyazott kód műveletei (JavaScript-kód végrehajtása)

Egyetlen logikai alkalmazás definíciója esetében a beágyazott kód műveleti művelet, a [**JavaScript-kód végrehajtása**](logic-apps-add-run-inline-code.md), a következő frissítési korlátokkal rendelkezik:

* A kód karaktereinek maximális száma 1 024 karakterből 100 000 karakterre nő.

* A kód futtatásának maximális időtartama öt másodperctől 15 másodpercre nő.

További információ: [Logic app definition Limits](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Következő lépések

* [Állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozása a Azure Portalban](create-stateful-stateless-workflows-azure-portal.md)
* [Állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps nyilvános előzetes kiadás ismert problémáinak lapja a GitHubon](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Azt is szeretnénk hallani, hogy Azure Logic Apps előzetes verzióval kapcsolatos tapasztalatairól van szó.

* Hibák vagy problémák esetén [hozza létre a problémákat a githubon](https://github.com/Azure/logicapps/issues).
* Kérdések, kérések, megjegyzések és egyéb visszajelzések esetén [használja ezt a visszajelzési űrlapot](https://aka.ms/lafeedback).
