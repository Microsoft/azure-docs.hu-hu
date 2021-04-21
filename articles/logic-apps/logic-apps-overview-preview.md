---
title: Az Azure Logic Apps előzetes verziójának áttekintése
description: Azure Logic Apps Előzetes verzió egy felhőalapú megoldás olyan automatizált, egybérlős, állapotalapú és állapot nélküli munkafolyamatok építéséhez, amelyek alkalmazásokat, adatokat, szolgáltatásokat és rendszereket integrálnak minimális kódokkal a nagyvállalati szintű forgatókönyvekhez.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764818"
---
# <a name="overview-azure-logic-apps-preview"></a>Áttekintés: Azure Logic Apps előzetes verzió

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Logic Apps előzetes verzióval automatizálási és integrációs megoldásokat építhet ki az alkalmazások, adatok, felhőszolgáltatások és rendszerek számára, ha egybérlős logikai alkalmazásokat hoz létre és futtat az új **Logic App (előzetes verzió)** erőforrástípussal. Ezzel az egybérlős logikai alkalmazástípussal több, az újratervezett Azure Logic Apps-es előzetes verziójú futtatókörnyezet által működtetett állapotmentes munkafolyamatot is felépíthet, amely hordozhatóságot, jobb teljesítményt és rugalmasságot biztosít a különböző üzemeltetési környezetekben, például nem csak az Azure-ban, hanem Docker-tárolókban való üzembe helyezéshez és futtatáshoz is. [   ](#stateful-stateless)

Hogyan lehetséges ez? Az újratervezett Azure Functions [](../azure-functions/functions-bindings-register.md) az Azure Functions-bővítési modellt használja, és bővítményként van üzemeltetve a Azure Functions futtatáskor. Ez az architektúra azt jelenti, hogy az egybérlős logikai alkalmazástípust bárhol futtathatja, Azure Functions fut. Az újratervezett futásidőt szinte bármilyen hálózati topológián futtathatja, és bármilyen elérhető számítási méretet kiválaszthat a munkafolyamatokhoz szükséges számítási feladatok kezeléséhez. További információ: [Bevezetés az eseményindítók Azure Functions](../azure-functions/functions-overview.md) Azure Functions [és kötésekbe.](../azure-functions/functions-triggers-bindings.md)

A Logikai alkalmazás **(előzetes verzió)** erőforrást létrehozhatja a [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) első kiadásával, vagy egy projekt létrehozásával a Visual Studio Code-ban a [Azure Logic Apps (előzetes verzió) bővítmény használatával.](create-stateful-stateless-workflows-visual-studio-code.md) Emellett a Visual Studio Code-ban is  felépítheti és helyileg futtathatja a munkafolyamatokat a fejlesztési környezetben. Akár a portált, akár a Visual Studio Code-et használja, az egybérlős logikai alkalmazástípust ugyanolyan típusú üzemeltetési környezetekben helyezheti üzembe és futtathatja.

Ez az áttekintés a következő területeket fedi le:

* [Az előzetes Azure Logic Apps, a Azure Logic Apps-bérlős](#preview-differences)környezet és az integrációs szolgáltatási környezet közötti különbségek.

* [Az állapot-beli és állapot](#stateful-stateless)nélküli munkafolyamatok közötti különbségek, beleértve a beágyazott állapot-beli és állapot nélküli munkafolyamatok [közötti viselkedésbeli különbségeket](#nested-behavior)is.

* [A nyilvános előzetes verzió képességei.](#public-preview-contents)

* [A díjszabási modell működése.](#pricing-model)

* [Módosított, korlátozott, nem elérhető vagy nem támogatott képességek.](#limited-unavailable-unsupported)

* [Az előzetes verzió Azure Logic Apps korlátai.](#limits)

További információért tekintse át az alábbi témaköröket:

* [Azure Logic Apps futtatása – Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps nyilvános előzetes verzió ismert problémái (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Miben Azure Logic Apps az előzetes verzió?

A Azure Logic Apps előzetes verziójú [](../azure-functions/functions-overview.md) Azure Functions bővítményként van üzemeltetve a Azure Functions bővítményként. Ez az architektúra azt jelenti, hogy az egybérlős logikai alkalmazástípust bárhol futtathatja, Azure Functions fut. Az előzetes Azure Logic Apps futtathatja szinte bármilyen hálózati topológián, és bármilyen elérhető számítási méretet kiválaszthat a munkafolyamathoz szükséges számítási feladatok kezeléséhez. További információ a Azure Functions létrehozásáról: [WebJobs SDK: Creating custom input and output bindings (WebJobs SDK: Egyéni](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)bemeneti és kimeneti kötések létrehozása).

Ezzel az új megközelítéssel az Azure Logic Apps előzetes verziójú futtatás és a munkafolyamatok is részét képezi az alkalmazásnak, amely együtt csomagolt. Ez a képesség lehetővé teszi a munkafolyamatok üzembe helyezését és futtatását az összetevők egyszerűen az üzemeltetési környezetbe való másolása és az alkalmazás indítása révén. Ez a megközelítés szabványosabb élményt biztosít az üzembe helyezési folyamatoknak a munkafolyamat-projektek köré való építéséhez a szükséges tesztek és ellenőrzések futtatásához, mielőtt üzembe helyezi a módosításokat az éles környezetekben. További információ: Azure Logic Apps Running Anywhere - Runtime Deep Dive (Futtatás bárhol – [Futtatókörnyezet részletesen).](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

Az alábbi táblázat röviden összefoglalja az erőforrások munkafolyamatok közötti megosztásának különbségeit a futtatás környezete alapján. A korlátok közötti különbségekért lásd: Az előzetes [verzió Azure Logic Apps korlátai.](#limits)

| Környezet | Erőforrás-megosztás és -felhasználás |
|-------------|----------------------------------|
| Azure Logic Apps (több-bérlős) | A több *bérlőn lévő* ügyfelektől származó munkafolyamatok ugyanazt a feldolgozást (számítást), tárterületet, hálózatot stb. osztják meg. |
| Azure Logic Apps (előzetes verzió, egybérlős) | Az ugyanabban a *logikai alkalmazásban* és egyetlen bérlőben a munkafolyamatok ugyanazt a feldolgozást (számítást), tárterületet, hálózatot stb. osztják meg. |
| Integrációs szolgáltatási környezet (nem érhető el az előzetes verzióban) | Az ugyanabban a *környezetben a* munkafolyamatok ugyanazt a feldolgozást (számítást), tárterületet, hálózatot stb. osztják meg. |
|||

Addig is létrehozhatja a több-bérlős logikaialkalmazás-típust a Azure Portal és a Visual Studio Code-ban a több-bérlős logikai Azure Logic Apps használatával. Bár a fejlesztési élmény eltérő a több-bérlős és az egybérlős logikai alkalmazástípusok között, az Azure-előfizetés mindkét típust tartalmazhatja. Megtekintheti és elérheti az Összes üzembe helyezett logikai alkalmazást az Azure-előfizetésében, de az alkalmazások a saját kategóriáikban és szakaszaikban vannak rendezve.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Állapot- és állapot nélküli munkafolyamatok

Az egybérlős logikai alkalmazástípussal a következő munkafolyamat-típusokat hozhatja létre ugyanabban a logikai alkalmazásban:

* *Állapot- szerinti*

  Állapot- szerinti munkafolyamatokat hozhat létre, ha meg kell tartania, át kell vizsgálnia vagy hivatkozni kell a korábbi eseményekből származó adatokat. Ezek a munkafolyamatok az egyes műveletek bemenetét és kimenetét külső tárterületen mentik, ami lehetővé teszi a futtatás részleteinek és előzményeinek áttekintését az egyes futtatás befejezése után. Az állapot- szerinti munkafolyamatok nagy rugalmasságot biztosítanak kimaradás esetén. A szolgáltatások és rendszerek visszaállítása után rekonstruálhatja a megszakított futtatásokat a mentett állapotból, és újrafuttathatja a munkafolyamatokat a befejezésig. Az állapot- szerinti munkafolyamatok akár egy évig is futnak.

* *Állapot nélküli*

  Állapot nélküli munkafolyamatokat hozhat létre, ha nem kell mentenie, áttekintenie vagy hivatkozni a külső tárolóban történt korábbi események adatait későbbi felülvizsgálatra. Ezek a munkafolyamatok az egyes műveletek bemenetét és kimenetét, valamint azok állapotait csak a memóriában mentik *az* adatok külső tárolóba való átvitele helyett. Ennek eredményeképpen az állapot nélküli munkafolyamatok rövidebb futtatásokat futtatnak, amelyek általában nem hosszabbak 5 percnél, gyorsabb teljesítményt, gyorsabb válaszidőt, nagyobb átviteli sebességet és alacsonyabb futtatás-költségeket eredményeznek, mivel a futtatás részletei és előzményei nem tárolódnak külső tárolóban. Kimaradás esetén azonban a megszakított futtatásokat a rendszer nem visszaállítja automatikusan, így a hívónak manuálisan kell újra elterálnia a megszakított futtatásokat. Ezek a munkafolyamatok csak szinkron módon futtathatók.

  A könnyebb hibakeresés érdekében engedélyezheti a futtatás előzményeit egy állapot nélküli munkafolyamathoz, amely hatással van a teljesítményre, majd letilthatja a futtatás előzményeit, amikor végzett. További információ: [Állapot-](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban vagy Állapot-állapot nélküli munkafolyamatok létrehozása a [Azure Portal.](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)

  > [!NOTE]
  > Az állapot nélküli munkafolyamatok [](../connectors/managed.md)jelenleg csak az Azure-ban üzembe helyezett felügyelt összekötők esetén támogatják a műveleteket, az eseményindítókat nem.  A munkafolyamat elkezdését a beépített Request [(Kérés)](../connectors/built-in.md)vagy a Event Hubs vagy a Service Bus válassza. Ezek az eseményindítók natív módon futnak a Azure Logic Apps előzetes verziójú futtatásban. További információ a korlátozott, nem elérhető vagy nem támogatott eseményindítókról, műveletekről és [összekötőkről: Módosított, korlátozott,](#limited-unavailable-unsupported)nem elérhető vagy nem támogatott képességek.

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Az állapot-beli és állapot nélküli munkafolyamatok beágyazott viselkedésének különbségei

Az ugyanannak [a](../logic-apps/logic-apps-http-endpoint.md) Logic App-erőforrásnak **(előzetes verzió)** megfelelő munkafolyamatból hívható munkafolyamatot a Kérés eseményindító, [](../connectors/connectors-native-reqres.md)a HTTP [Webhook-eseményindító](../connectors/connectors-native-webhook.md)vagy a felügyelt összekötő eseményindítók használatával is meg lehet hívni, amelyek [ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) típusúak, és HTTPS-kéréseket fogadhatnak.

A beágyazott munkafolyamatok a következő viselkedési mintákat követik, miután egy szülő munkafolyamat gyermek-munkafolyamatot hív meg:

* Aszinkron lekérdezési minta

  A szülő nem vár a kezdeti hívásra adott válaszra, hanem folyamatosan ellenőrzi a gyermek futtatás előzményeit, amíg a gyermek be nem fejezi a futását. Alapértelmezés szerint az állapot-igénylési munkafolyamatok ezt a mintát követik, amely ideális olyan hosszan futó gyermek-munkafolyamatokhoz, amelyek túllépik a kérelmek [időkorlátait.](../logic-apps/logic-apps-limits-and-config.md)

* Szinkron minta ("tűz és elfelejtve")

  A gyermek válasz azonnali visszaküldésével nyugtázza a hívást, és a szülő anélkül folytatja a következő műveletet, hogy megvárja a gyermektől `202 ACCEPTED` kapott eredményeket. Ehelyett a szülő akkor kapja meg az eredményeket, amikor a gyermek befejeződik. A Response műveletet nem magában foglaló gyermek állapot- szerinti munkafolyamatok mindig a szinkron mintát követik. A gyermek állapot- szerinti munkafolyamatok futtatáselőzményei áttekinthetőek.

  Ennek engedélyezéséhez a munkafolyamat JSON-definíciójában állítsa a tulajdonságot a `operationOptions` következőre: `DisableAsyncPattern` . További információ: [Eseményindító- és művelettípusok – Műveleti beállítások.](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

* Aktiválás és várakozás

  Gyermek állapot nélküli munkafolyamatoknél a szülő olyan válaszra vár, amely visszaadja a gyermek eredményeit. Ez a minta hasonlóan működik, [](../connectors/connectors-native-http.md) mint a beépített HTTP-eseményindító vagy művelet használata egy gyermek-munkafolyamat hívásához. A Válasz műveletet nem magában foglaló gyermek állapot nélküli munkafolyamatok azonnal választ adnak vissza, de a szülő megvárja, amíg a gyermek befejeződik, mielőtt továbblép a `202 ACCEPTED` következő műveletre. Ezek a viselkedések csak a gyermek állapot nélküli munkafolyamatok esetén érvényesek.

Ez a táblázat határozza meg a gyermek-munkafolyamat viselkedését az alapján, hogy a szülő és a gyermek állapotalapú, állapot nélküli vagy vegyes munkafolyamat-típus:

| Szülő-munkafolyamat | Gyermek-munkafolyamat | Gyermek viselkedése |
|-----------------|----------------|----------------|
| Állapotalapú | Állapotalapú | Aszinkron vagy szinkron a `"operationOptions": "DisableAsyncPattern"` beállítással |
| Állapotalapú | Állapot nélküli | Aktiválás és várakozás |
| Állapot nélküli | Állapotalapú | Szinkron |
| Állapot nélküli | Állapot nélküli | Aktiválás és várakozás |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Képességek

Azure Logic Apps előzetes verzió számos jelenlegi és további képességet tartalmaz, például:

* Logikai alkalmazásokat és azok munkafolyamatait [több mint 400](/connectors/connector-reference/connector-reference-logicapps-connectors) összekötőből hozhatja létre a szolgáltatott szoftver (SaaS) és a szolgáltatásként nyújtott platform (PaaS) alkalmazásokhoz és szolgáltatásokhoz, valamint a helyszíni rendszerek összekötőihez.

  * Egyes felügyelt összekötők beépített verziókként érhetők el, amelyek hasonlóan futnak a beépített eseményindítókhoz és műveletekhez, mint például a Kérés eseményindító és a HTTP-művelet, amelyek natív módon futnak az Azure Logic Apps előzetes verziójú futásidejű verzióján. Ezek az új beépített összekötők például a következőket tartalmazzák: Azure Service Bus, Azure Event Hubs, SQL Server és MQ.

    > [!NOTE]
    > A beépített SQL Server esetében csak a  Lekérdezés végrehajtása művelet tud közvetlenül csatlakozni az Azure-beli virtuális hálózatokhoz anélkül, hogy a [helyszíni adatátjáróra volna szükség.](logic-apps-gateway-connection.md)

  * Hozzon létre saját beépített összekötőket minden szükséges szolgáltatáshoz az előzetes kiadás kihetőségi [keretrendszerének használatával.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) A beépített összekötőkhöz (például az Azure Service Bus és az SQL Server) [](../connectors/apis-list.md#custom-apis-and-connectors) hasonlóan, de az előzetes verzióban jelenleg nem támogatott egyéni összekötőktől eltérően ezek az összekötők nagyobb átviteli sebességet, alacsony késést, helyi kapcsolatot biztosítanak, és natív módon futnak az előzetes verziójú futtatás folyamatában.

    A szerzői funkció jelenleg csak az Visual Studio Code-ban érhető el, de alapértelmezés szerint nincs engedélyezve. Ezen összekötők létrehozásához váltsa át a projektet csomagalapú [bővítményről (Node.js) NuGet csomagalapúra (.NET).](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring) További információ: Azure Logic Apps Running Anywhere - Built-in connector extensibility (Bárhol fut – Beépített [összekötők extenzibilitása).](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)

  * Integrációs fiók nélkül is használhatja a B2B-műveleteket a Liquid-műveletekhez és az XML-műveletekhez. Ezeknek a műveleteknek a felhasználásával Liquid-térképekre, XML-leképezésekre vagy XML-sémákra van szükség, amelyek feltölthetők az Azure Portal megfelelő műveletei között, vagy felvehetők a Visual Studio Code-projekt **Artifacts** mappájába a megfelelő **Maps** és **Schemas** mappák használatával.

  * Olyan logikai alkalmazásokat hozhat létre és helyezhet üzembe, amelyek bárhol futnak, mivel a Azure Logic Apps szolgáltatás közös hozzáférésű jogosultság jogosultságú (SAS) kapcsolati sztringeket hoz létre, amelyek használatával ezek a logikai alkalmazások kéréseket küldenek a felhőkapcsolat futásidejű végpontjára. A Logic Apps szolgáltatás más alkalmazásbeállításokkal menti ezeket a kapcsolati sztringeket, így ezeket az értékeket könnyen tárolhatja Azure Key Vault azure-beli üzembe helyezéskor.

    > [!NOTE]
    > Alapértelmezés szerint a **logic app (előzetes verzió)** erőforrás rendszer által hozzárendelt felügyelt identitása automatikusan engedélyezve van a kapcsolatok futásidőben való hitelesítéséhez. [](../logic-apps/create-managed-service-identity.md) Ez az identitás eltér a kapcsolat létrehozásakor használt hitelesítő adatoktól vagy kapcsolati sztringtől. Ha letiltja ezt az identitást, a kapcsolatok futásidőben nem fognak működni. A beállítás megtekintéséhez a logikai alkalmazás menüjében, a **Beállítások alatt válassza** az Identitás **lehetőséget.**

* Olyan logikai alkalmazásokat hozhat létre állapot nélküli munkafolyamatokkal, amelyek csak a memóriában futnak, így gyorsabban befejeződnek, gyorsabban válaszolnak, magasabb átviteli sebességet és kevesebb költséggel futnak, mert a futtatás-történeteket és a műveletek közötti adatokat nem őrizik meg a külső tárolóban. A könnyebb hibakeresés érdekében engedélyezheti a futtatás előzményeit is. További információ: [Állapot-](#stateful-stateless)és állapot nélküli logikai alkalmazások.

* A logikai alkalmazásokat és azok munkafolyamatait helyileg futtatja, teszteli és hibakeresést Visual Studio Code fejlesztői környezetben.

  A logikai alkalmazás futtatása és tesztelése előtt megkönnyítheti a hibakeresést,  ha töréspontokat ad hozzá és használworkflow.jsegy munkafolyamat fájljában. A töréspontok azonban jelenleg csak műveletekhez támogatottak, eseményindítókhoz nem. További információkért lásd: Állapot- és állapot nélküli munkafolyamatok létrehozása a [Visual Studio Code-ban.](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)

* Közvetlenül közzétehet vagy üzembe helyezhet logikai alkalmazásokat és azok munkafolyamatait a Visual Studio Code-ból különböző üzemeltetési környezetekben, például Azure- és [Docker-tárolókban.](/dotnet/core/docker/introduction)

* Diagnosztikai naplózási és nyomkövetési képességek engedélyezése [](../azure-monitor/app/app-insights-overview.md) a logikai alkalmazáshoz a Application Insights használatával, ha az Azure-előfizetés és a logikai alkalmazás beállításai támogatják.

* A logikai alkalmazások prémium szintű csomaggal való létrehozásakor és üzembe helyezésekor az Azure Functions Azure Functions-hez hasonlóan hozzáférhet olyan hálózati képességekhez, mint a privát csatlakozás és integráció az [Azure-beli virtuális hálózatokkal.](../azure-functions/functions-premium-plan.md) További információért tekintse át az alábbi témaköröket:

  * [Az Azure Functions hálózatkezelési lehetőségei](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps Running Anywhere – Hálózati lehetőségek a Azure Logic Apps előzetes verzióval](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Az egybérlős logic app **(előzetes verzió)** erőforrás egyéni munkafolyamatai által használt felügyelt kapcsolatok hozzáférési kulcsait újragenerálja. Ehhez a feladathoz kövesse ugyanezeket a lépéseket a [ **több-bérlős**](logic-apps-securing-a-logic-app.md#regenerate-access-keys)Logic Apps, de az egyéni munkafolyamat szintjén ne a logikai alkalmazás erőforrásszinten.

* Az egybérlős tervezőben a több-bérlős tervezővel azonos lépéseket követve adhat hozzá párhuzamos ágakat.

További információ: [Módosított, korlátozott,](#limited-unavailable-unsupported) nem elérhető és nem támogatott képességek, valamint Logic Apps nyilvános előzetes verzió ismert problémáit bemutató oldal a [GitHubon.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Díjszabási modell

Amikor létrehozza az egybérlős logikai alkalmazás típusát a Azure Portal-ban, vagy üzembe helyez egy Visual Studio Code-ból, ki kell választania egy üzemeltetési tervet [(App Service](../azure-functions/functions-scale.md)vagy Prémium ) a logikai alkalmazás számára. Ez a csomag határozza meg a logikai alkalmazás futtatására vonatkozó díjszabási modellt. Ha az App Service tarifacsomagot választja, tarifacsomagot [is ki kell választania.](../app-service/overview-hosting-plans.md)

*Az állapot-alapú* munkafolyamatok külső tárolót [alkalmaznak,](../azure-functions/storage-considerations.md#storage-account-requirements)így az Azure [Storage](https://azure.microsoft.com/pricing/details/storage/) díjszabása az előzetes verziójú Azure Logic Apps által Azure Logic Apps tárolási tranzakciókra vonatkozik. Az üzenetsorok például ütemezésre, míg a táblák és blobok munkafolyamat-államok tárolására használhatók.

> [!NOTE]
> A nyilvános előzetes verzióban a logikai alkalmazások a  App Service futtatása nem jár további költségeket a kiválasztott csomagon felül.

Az egybérlős erőforrástípusra vonatkozó díjszabási modellekkel kapcsolatos további információkért tekintse át az alábbi témaköröket:

* [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)
* [Alkalmazás horizontális felskál méretezése a Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions díjszabás részletei](https://azure.microsoft.com/pricing/details/functions/)
* [App Service díjszabás részletei](https://azure.microsoft.com/pricing/details/app-service/)
* [Az Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Módosított, korlátozott, nem elérhető vagy nem támogatott képességek

Az Azure Logic Apps előzetes verzióban ezek a képességek megváltoztak, vagy jelenleg korlátozottak, nem érhetők el vagy nem támogatottak:

* **Operációs rendszer** támogatása: Az Visual Studio Code tervezője jelenleg nem működik Linux operációs rendszeren, de továbbra is üzembe helyezhet olyan logikai alkalmazásokat, amelyek az Logic Apps Preview-alapú futásidőt használják Linux-alapú virtuális gépeken. Logikai alkalmazásait jelenleg windowsos vagy macOS rendszeren a Visual Studio Code-ban építheti ki, majd linuxos virtuális gépen helyezheti üzembe.

* **Eseményindítók és** műveletek: A beépített eseményindítók és műveletek natív módon futnak az Azure Logic Apps előzetes verziójú futtatásban, míg a felügyelt összekötők az Azure-ban vannak üzembe gombra stb. Egyes beépített eseményindítók nem érhetők el, például a Csúszóablak és a Batch.

  A munkafolyamat elkezdését a beépített [Ismétlődés, Kérés, HTTP, HTTP-webhook,](../connectors/apis-list.md)Event Hubs vagy Service Bus eseményindítóval. A tervezőben a beépített eseményindítók és  műveletek a Beépített lapon jelennek meg, míg a felügyelt összekötők eseményindítói és műveletei az **Azure lapon jelennek** meg.

  > [!NOTE]
  > Ha helyileg futtatni Visual Studio Code-ban, a webhook-alapú triggerek és műveletek további beállításokat igényelnek. További információkért lásd: Állapot- és állapot nélküli munkafolyamatok létrehozása a [Visual Studio Code-ban.](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)

  * Állapot *nélküli munkafolyamatok esetén* az **Azure** lap nem jelenik meg az eseményindító kiválasztásakor, mert csak a felügyelt összekötő-műveleteket választhatja ki, az [eseményindítókat nem.](../connectors/managed.md) Bár az Azure-ban üzembe helyezett felügyelt összekötők engedélyezhetők állapot nélküli munkafolyamatokhoz, a tervező nem mutatja meg a hozzáadható felügyelt összekötő-eseményindítókat.

  * *Állapot- szerinti munkafolyamatok* esetén az alább nem elérhetőként felsorolt [](../connectors/managed.md) eseményindítóktól és műveletektől különböző esetekben a felügyelt összekötő eseményindítói és műveletei is használhatók.

  * Ezek az eseményindítók és műveletek vagy módosultak, vagy jelenleg korlátozottak, nem támogatottak vagy nem érhetők el:

    * [A helyszíni adatátjáró *eseményindítói*](../connectors/managed.md#on-premises-connectors) nem érhetők el, de az átjáróműveletek *elérhetők.*

    * A beépített művelet, Azure Functions [– Válasszon ki egy Azure-függvényt](logic-apps-azure-functions.md) mostantól **Azure-függvényműveletek – Azure-függvény hívása.** Ez a művelet jelenleg csak a HTTP-eseményindító sablonból létrehozott **függvények esetén** működik.

      A Azure Portal kiválaszthat egy HTTP-eseményindító függvényt, ahol hozzáféréssel rendelkezik, ha létrehoz egy kapcsolatot a felhasználói élményen keresztül. Ha megvizsgálja a függvény műveletének JSON-definícióját  kódnézetben vagyworkflow.jsfájlban, a művelet hivatkozással hivatkozik a `connectionName` függvényre. Ez a verzió kapcsolatként absztraktálja a függvény adatait,  amelyet a projekt fájljábanconnections.jstalál, amely a kapcsolat létrehozása után érhető el.

      > [!NOTE]
      > Az egybérlős verzióban a függvény művelet csak a lekérdezési sztringes hitelesítést támogatja. Azure Logic Apps előnézet lekérte az alapértelmezett kulcsot a függvénytől a kapcsolat létesítéskor, tárolja a kulcsot az alkalmazás beállításaiban, és a kulcsot használja a hitelesítéshez a függvény hívakor.
      >
      > Akár csak a több-bérlős verzió esetén, ha például a portál Azure Functions-élményében újítja meg a kulcsot, a függvény művelet az érvénytelen kulcs miatt már nem fog működni. A probléma megoldásához újra létre kell hoznia a kapcsolatot azzal a függvénysel, amely az új kulccsal hívja meg vagy frissíti az alkalmazás beállításait.

    * A beépített Beépített kód – [JavaScript-kód](logic-apps-add-run-inline-code.md) végrehajtása mostantól Beágyazott kódműveletek – Beágyazott **JavaScript futtatása.**

      * A beágyazott kódműveleti műveletekhez már nincs szükség integrációs fiókra.

      * macOS és Linux  rendszeren a beágyazott kódműveleteket mostantól a Azure Logic Apps (előzetes verzió) bővítmény a Visual Studio Code-ban használja.

      * Már nem kell újraindítani a logikai alkalmazást, ha módosításokat eszközlik egy beágyazott **kódműveletben.**

      * **A beágyazott kódműveleti műveleteknek** [frissített korlátaik vannak.](logic-apps-overview-preview.md#inline-code-limits)

    * Az [integrációs fiókok néhány beépített B2B-eseményindítója](../connectors/managed.md#integration-account-connectors) és -művelete nem érhető el, például az 1. fájl kódolási és dekódolási műveletei. 

    * A beépített művelet, Azure Logic Apps – Logikai alkalmazás [munkafolyamatának](logic-apps-http-endpoint.md) kiválasztása mostantól Munkafolyamat-műveletek – Munkafolyamat meghívása **ebben a munkafolyamat-alkalmazásban.**

* [Az egyéni összekötők](../connectors/apis-list.md#custom-apis-and-connectors) jelenleg nem támogatottak az előzetes verzióban.

* Üzemeltetési csomag rendelkezésre **állása:** Függetlenül attól, hogy az egybérlős Logikai alkalmazás **(előzetes verzió)** erőforrástípust az Azure Portal-ban hozza létre, vagy az Visual Studio Code-ból telepíti, csak a Prémium vagy App Service-alapú üzemeltetési csomag használható az Azure-ban. A használaton áteső üzemeltetési csomagok nem érhetők el, és nem támogatottak az erőforrástípus üzembe helyezéséhez. Az üzembe helyezést az Visual Studio Code-ból egy Docker-tárolóba, de integrációs szolgáltatási [környezetbe (ISE) nem.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

* **Töréspont-hibakeresés** az Visual Studio Code-ban: Bár a **workflow.js-fájlban** töréspontokat adhat hozzá és használhat munkafolyamatokhoz, a töréspontok jelenleg csak a műveletekhez támogatottak, az eseményindítókhoz nem. További információkért lásd: Állapot- és állapot nélküli munkafolyamatok létrehozása a [Visual Studio Code-ban.](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)

* **Nagyítás vezérlő:** A nagyítási vezérlő jelenleg nem érhető el a tervezőben.

* **Triggerelőzmények** és futtatáselőzmények: A Logikai alkalmazás **(előzetes verzió)** erőforrástípushoz az eseményindító-előzmények és a futtatáselőzmények a Azure Portal a munkafolyamat szintjén jelennek meg, nem a logikai alkalmazás szintjén. Az előzményadatok megkereséhez kövesse az alábbi lépéseket:

   * A futtatás előzményeinek megtekintéséhez nyissa meg a munkafolyamatot a logikai alkalmazásban. A munkafolyamat menüjében a Fejlesztő **alatt válassza** a **Figyelás lehetőséget.**

   * Az eseményindító előzményeinek áttekintéséhez nyissa meg a munkafolyamatot a logikai alkalmazásban. A munkafolyamat menü Fejlesztői **menüjében válassza** a **Trigger Histories** lehetőséget.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Forgalomengedélyek szigorú hálózati és tűzfal-forgatókönyvekben

Ha a környezetben szigorú hálózati követelmények vagy tűzfalak korlátozzák a forgalmat, hozzáférést kell engedélyeznie a logikai alkalmazás munkafolyamatai eseményindítói és műveletkapcsolatai számára.

A kapcsolatok teljes tartománynevének (FQDN) megkeresése a következő témakörök megfelelő szakaszaiban található:

* [Tűzfalengedélyek egybérlős logikai alkalmazásokhoz – Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Tűzfalengedélyek egybérlős logikai alkalmazásokhoz – Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Frissített korlátok

Bár az előzetes Azure Logic Apps korlátai ugyanazok maradnak, mint a [több-bérlős](logic-apps-limits-and-config.md)Azure Logic Apps korlátai, ezek a korlátok az előzetes verzióra Azure Logic Apps módosulnak.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP-időtúllépési korlátok

Egyetlen bejövő vagy kimenő hívás esetén az időkorlát 230 másodperc (3,9 perc) az alábbi eseményindítók és műveletek esetén:

* Kimenő kérés: HTTP-eseményindító, HTTP-művelet
* Bejövő kérelem: Kérés eseményindító, HTTP-webhook-eseményindító, HTTP-webhook művelet

Ezzel szemben az eseményindítók és műveletek időtúllépési korlátai más környezetekben, ahol a logikai alkalmazások és a munkafolyamataik futnak:

* Több-bérlős Azure Logic Apps: 120 másodperc (2 perc)
* Integrációs szolgáltatási környezet: 240 másodperc (4 perc)

További információ: [HTTP-korlátok.](logic-apps-limits-and-config.md#http-limits)

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Felügyelt összekötők

A felügyelt összekötők kapcsolatonként legfeljebb 50 kérést kérhetnek percenként. Az összekötő-szabályozási problémák kezelésével kapcsolatban lásd a szabályozási problémák [(429 – "Túl sok kérelem" hiba)](handle-throttling-problems-429-errors.md#connector-throttling)kezelésével kapcsolatos Azure Logic Apps.

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Beágyazott kódműveletek (JavaScript-kód végrehajtása)

Egyetlen logikai alkalmazás definíciója esetén a Beágyazott kódműveletek Művelet [**JavaScript-kód**](logic-apps-add-run-inline-code.md)végrehajtása művelet a következő frissített korlátokkal rendelkezik:

* A kód karakterek maximális száma 1024 karakterről 100 000 karakterre nő.

* A kód futtatásának maximális időtartama öt másodpercről 15 másodpercre nő.

További információ: [Logikaialkalmazás-definíciók korlátai.](logic-apps-limits-and-config.md#definition-limits)

## <a name="next-steps"></a>Következő lépések

* [Állapot- és állapot nélküli munkafolyamatok létrehozása a Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Állapot- és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps nyilvános előzetes verzió ismert problémáinak lapja a GitHubon](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Szívesen meghallgatjuk a Azure Logic Apps tapasztalatait!

* Hibák vagy problémák esetén hozza [létre a problémákat a GitHubon.](https://github.com/Azure/logicapps/issues)
* Ha kérdése, kérése, megjegyzése vagy egyéb visszajelzése van, használja [ezt a visszajelzési űrlapot.](https://aka.ms/lafeedback)
