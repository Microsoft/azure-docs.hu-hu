---
title: Integrációs és automatizálási platform lehetőségei az Azure-ban
description: 'Összehasonlíthatja az integrációs feladatokhoz optimalizált Microsoft Cloud Services-szolgáltatásokat: a Power automatizálás, a Logic Apps, a functions és a webjobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: e6bd97469a6b7b6c42427e130ac103954377c652
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643370"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>A megfelelő integrációs és automatizálási szolgáltatások kiválasztása az Azure-ban

Ez a cikk összehasonlítja az alábbi Microsoft-felhőszolgáltatásokat:

* [Microsoft Power automatizálás](https://flow.microsoft.com/) (Microsoft flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Mindegyik szolgáltatás megoldhatja az integrációs problémákat és automatizálhatja az üzleti folyamatokat. Mindegyik képes bemenet, műveletek, feltételek és kimenet meghatározására. Futtathatók ütemezve vagy eseményindítóval is. Az egyes szolgáltatások egyedi előnyökkel rendelkeznek, és ez a cikk ismerteti a különbségeket. 

Ha a Azure Functions és az egyéb Azure számítási lehetőségek közötti általánosabb összehasonlítást keres, tekintse meg az [Azure számítási szolgáltatás kiválasztásának feltételeit](/azure/architecture/guide/technology-choices/compute-comparison) és [egy Azure-beli számítási lehetőség kiválasztását a-szolgáltatásokhoz](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>A Microsoft Power automatizálása és Azure Logic Apps összehasonlítása

Az automatizálás és a Logic Apps a *tervező-első* integrációs szolgáltatások, amelyek munkafolyamatokat hozhatnak létre. Mindkét szolgáltatás integrálható különféle SaaS- és vállalati alkalmazásokkal. 

A Power automatizálás Logic Appsra épül. Ugyanazt a munkafolyamat-tervezőt és ugyanazokat az [összekötőket](../connectors/apis-list.md)használják. 

A Power automatizálás lehetővé teszi bármely irodai feldolgozó számára, hogy egyszerű integrációkat végezzen (például egy SharePoint-dokumentumtár jóváhagyási folyamata) anélkül, hogy a fejlesztőknek vagy az IT-nak kellene lennie. A Logic Apps a speciális integrációkat (például B2B folyamatokat) is lehetővé teheti, ahol nagyvállalati szintű Azure DevOps és biztonsági gyakorlatok szükségesek. Az üzleti munkafolyamatokra jellemző, hogy idővel egyre összetettebbé válnak. Ennek megfelelően először egy folyamat indul el, majd szükség szerint konvertálhatja egy logikai alkalmazásba.

Az alábbi táblázat segít megállapítani, hogy az energiagazdálkodás vagy a Logic Apps a legmegfelelőbb-e egy adott integrációhoz:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Felhasználók** |Irodai dolgozók, üzleti felhasználók, SharePoint-adminisztrátorok |Professzionális integrátorok és fejlesztők, informatikai szakemberek |
| **Forgatókönyvek** |Önkiszolgálás |Speciális integrációk |
| **Kialakítási eszköz** |Böngészőbeli és mobilalkalmazás, kizárólag felhasználói felület |Böngészőbeli, [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), [Kódnézet](../logic-apps/logic-apps-author-definitions.md) elérhető |
| **Alkalmazások életciklus-felügyelete (ALM)** |Tervezés és tesztelés nem éles környezetekben, előléptetés éles környezetben, ha készen áll |Azure DevOps: verziókövetés, tesztelés, támogatás, automatizálás és kezelhetőség [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) |
| **Rendszergazdai felület** |A Power automatizáló környezetek és az adatveszteség-megelőzési (DLP) szabályzatok kezelése, licencelés nyomon követése: [felügyeleti központ](https://admin.flow.microsoft.com) |Erőforráscsoportok, kapcsolatok, hozzáférés-kezelés és naplózás kezelése: [Azure Portal](https://portal.azure.com) |
| **Biztonság** |Microsoft 365 biztonsági naplók, DLP, inaktív [titkosítás](https://wikipedia.org/wiki/Data_at_rest#Encryption) a bizalmas adatokhoz |Az Azure biztonsági garanciája: [Azure Security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [naplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Az Azure Functions és az Azure Logic Apps összehasonlítása

A Functions és a Logic Apps egy Azure-szolgáltatás, melyek kiszolgáló nélküli feladatokhoz nyújtanak támogatást. Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, míg a Azure Logic Apps kiszolgáló nélküli munkafolyamatokat biztosít. Mindkettő összetett előkészítéseket is létrehozhat. A vezénylés a függvények vagy lépések – a Logic Appsben ezeket *műveleteknek* nevezzük – egy olyan készlete, amelynek a végrehajtásával összetett feladatokat tud elvégezni. Ha például egy köteget szeretne feldolgozni, egy függvény több példánya is végrehajtható párhuzamosan, várjon, amíg az összes példány befejeződik, majd hajtson végre egy olyan függvényt, amely kiszámítja az Összesítés eredményét.

Az Azure Functions esetében a vezénylések fejlesztéséhez kódot kell írnia a [Durable Functions bővítmény](durable/durable-functions-overview.md) segítségével. A Logic Appsben a vezényléseket grafikus felhasználói felülettel vagy konfigurációs fájlok szerkesztésével tudja létrehozni.

Ezeket a szolgáltatásokat használhatja vegyesen a vezénylések létrehozásához, vagyis hívhat meg függvényeket a logikai alkalmazásokból, illetve hívhat meg logikai alkalmazásokat a függvényekből. A vezénylések építésének módját a szolgáltatások képességei és a személyes preferenciái szerint választhatja ki. Az alábbi táblázat a következő főbb különbségeket sorolja fel:

|  | Tartós függvények | Logic Apps |
| --- | --- | --- |
| **Fejlesztés** | Kódközpontú (imperatív) | Tervezőközpontú (deklaratív) |
| **Kapcsolódás** | [Körülbelül egy tucat beépített kötési típus](functions-triggers-bindings.md#supported-bindings), kód írása az egyéni kötésekhez | [Az összekötők nagy gyűjteménye](../connectors/apis-list.md), [Enterprise Integration Pack B2B-forgatókönyvekhez](../logic-apps/logic-apps-enterprise-integration-overview.md), [Egyéni összekötők létrehozása](../logic-apps/custom-connector-overview.md) |
| **Műveletek** | Minden tevékenység egy Azure-függvény; kód írásával hozhat létre tevékenységfüggvényeket |[Használatra kész műveletek széles választéka](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Figyelés** | [Azure-Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure monitor naplók](../logic-apps/monitor-logic-apps.md)|
| **Felügyelet** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](/rest/api/logic/), [PowerShell](/powershell/module/az.logicapp), [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Végrehajtási környezet** | [Helyileg](functions-runtime-overview.md) vagy a felhőben is futtatható | Csak felhőben futtatható|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>A Functions és a WebJobs összehasonlítása

Ugyanúgy, mint az Azure Functions, az Azure App Service WebJobs a WebJobs SDK-val is olyan integrációs szolgáltatás, ahol a *kód* a kiindulási pont, és mindkettőt fejlesztők részére tervezték. Mindkettő az [Azure App Service](../app-service/overview.md) szolgáltatásra épül, és támogatják többek között a következő funkciókat: [verziókövetés integrálása](../app-service/deploy-continuous-deployment.md), [hitelesítés](../app-service/overview-authentication-authorization.md) és [Application Insights-integrációs monitorozás](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>A WebJobs és a WebJobs SDK

A App Service *webjobs* funkciójának használatával parancsfájlt vagy kódot futtathat egy app Service webalkalmazás környezetében. A *WebJobs SDK* egy, a WebJobshoz tervezett keretrendszer, amely leegyszerűsíti az Azure-szolgáltatások válaszadására írt kódokat. Előfordulhat például, hogy egy miniatűr rendszerkép létrehozásával válaszol egy képblob létrehozására az Azure Storage-ban. A WebJobs SDK .NET-konzolalkalmazásként fut, amelyet üzembe helyezhet egy WebJobon. 

A WebJobs és a WebJobs SDK együtt működnek a leghatékonyabban, de használhatja az egyiket a másik nélkül is. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben. A WebJobs SDK-konzolalkalmazások bárhol futtathatók, ahol konzolalkalmazások futtathatók, például a helyszíni kiszolgálókon.

### <a name="comparison-table"></a>Összehasonlító táblázat

Az Azure Functions a WebJobs SDK-ra épül, így sok, más Azure-szolgáltatásokkal közös eseményindítóval és kapcsolattal rendelkezik. Az alábbiakban néhány szempontot figyelembe kell venni, amikor a webjobs SDK-val Azure Functions és webjobs-feladatok közül választ:

|  | Functions | WebJobs WebJobs SDK-val |
| --- | --- | --- |
|**[Kiszolgáló nélküli alkalmazás](https://azure.microsoft.com/solutions/serverless/) -modell [automatikus skálázással](event-driven-scaling.md)**|✔||
|**[Fejlesztés és tesztelés böngészőben](./functions-get-started.md)** |✔||
|**[Használatalapú fizetés](consumption-plan.md)**|✔||
|**[Integráció a Logic Apps szolgáltatással](functions-twitter-email.md)**|✔||
| **Kiváltó események** |[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Fájlrendszer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Támogatott nyelvek**  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Csomagkezelők**|NPM és NuGet|NuGet<sup>2</sup>|

<sup>1</sup> a webjobs (a webjobs SDK nélkül) a következőt támogatja: C#, Java, JavaScript, bash,. cmd,. bat, POWERSHELL, php, írógéppel, Python és sok más. Ez nem egy átfogó lista. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben.

<sup>2</sup> a webjobs (a webjobs SDK nélkül) támogatja a NPM és a NuGet.

### <a name="summary"></a>Összefoglalás

Azure Functions nagyobb fejlesztői hatékonyságot biztosít, mint Azure App Service webjobs. Emellett több lehetőséget is kínál a programozási nyelvek, a fejlesztői környezetek, az Azure-szolgáltatások integrálására és a díjszabásra. A legtöbb forgatókönyvhöz ez a legjobb választás.

Az alábbiakban két olyan forgatókönyvet láthat, amelyekhez a WebJobs lehet a legjobb választás:

* Az eseményekre figyelő kódok nagyobb mértékű felügyeletére van szüksége – `JobHost` objektum. A Functions korlátozott számú módszert biztosít a `JobHost`-viselkedés a [host.json](functions-host-json.md) fájlban való testreszabására. Néha olyan műveletekre lehet szüksége, amelyek nem adhatók meg egy JSON-fájlbeli sztringben. Például csak a WebJobs SDK teszi lehetővé egyéni újrapróbálkozási szabályzat konfigurálását az Azure Storage-ban.
* Rendelkezik egy App Service alkalmazással, amelyhez kódrészleteket szeretne futtatni, és együtt szeretné kezelni őket ugyanabban az Azure DevOps-környezetben.

Minden egyéb olyan forgatókönyv esetén, ahol kódrészletek futtatásával kíván Azure- vagy külső szolgáltatásokat integrálni, válassza az Azure Functionst a WebJobs és WebJobs SDK helyett.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Automatizálás, Logic Apps, függvények és webjobs együtt

Ezen szolgáltatások közül csak egyet kell választania. Integrálva vannak egymással, valamint külső szolgáltatásokkal is.

Egy folyamat meghívhat egy logikai alkalmazást. Egy logikai alkalmazás meghívhat egy függvényt, egy függvény pedig meghívhat egy logikai alkalmazást. Példa: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).

A Power automatizálás, a Logic Apps és a függvények közötti integráció továbbra is az idő múlásával javul. Amit létrehoz az egyik szolgáltatásban, használhatja a többiben.

Az integrációs szolgáltatásokkal kapcsolatos további információk az alábbi hivatkozásokon keresztül olvashatók:

* [Christopher Anderson: Leveraging Azure Functions & Azure App Service for integration scenarios](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Az Azure Functions és az Azure App Service használata integrációs forgatókönyvekhez)
* [Charles Lamanna: Integrations Made Simple](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integráció egyszerűen)
* [Élő webes közvetítés Logic Apps](https://aka.ms/logicappslive)
* [A gyakori kérdések a Power automatizálva](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Következő lépések

Első lépésként hozza létre az első folyamatát, logikai alkalmazását vagy függvényalkalmazását. Válassza ki a következő hivatkozások bármelyikét:

* [Első lépések a Power Automate-ben](/power-automate/getting-started)
* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Az első Azure-függvény létrehozása](./functions-get-started.md)