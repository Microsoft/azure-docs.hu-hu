---
title: Ügyfél-áttekintési irányítópult létrehozása
description: Felhasználói visszajelzések, közösségi média-és egyéb funkciók kezelése az ügyfél-irányítópult létrehozásával Azure Logic Apps és Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 22e0c7304f7a53a86bc5c6739a2061352d738d29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784812"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Streaming Customer bepillantást tartalmazó irányítópult létrehozása Azure Logic Apps és Azure Functions

Az Azure olyan [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli eszközöket kínál, amelyekkel gyorsan hozhat létre és futtathat alkalmazásokat a felhőben anélkül, hogy az infrastruktúrával kellene gondolkodnia. Ebben az oktatóanyagban létrehozhat egy irányítópultot, amely az ügyfelek visszajelzéseit indítja el, elemzi a gépi tanulással kapcsolatos visszajelzéseket, és információkat tesz közzé a forrásról, például Power BI vagy Azure Data Lake.

Ebben a megoldásban a következő Azure-összetevőket használja a kiszolgáló nélküli alkalmazásokhoz: [Azure functions](https://azure.microsoft.com/services/functions/) és [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/).
A Azure Logic Apps kiszolgáló nélküli munkafolyamat-motort biztosít a felhőben, így összehangolhatja a kiszolgáló nélküli összetevőket, és csatlakozhat 200 + szolgáltatáshoz és API-hoz. Azure Functions kiszolgáló nélküli számítástechnikai szolgáltatásokat biztosít a felhőben. Ez a megoldás Azure Functions használ az ügyfelek tweetek megjelölésére előre definiált kulcsszavak alapján.

Ebben a forgatókönyvben egy logikai alkalmazást hoz létre, amely elindítja az ügyfelek visszajelzéseit. Néhány összekötő, amely segítséget nyújt a felhasználói visszajelzések megválaszolásához, például a Outlook.com, az Office 365, a Survey Monkey, a Twitter és egy [webes űrlap http-kérelme](/archive/blogs/logicapps/calling-a-logic-app-from-an-html-form). A létrehozott munkafolyamat a Twitteren lévő hashtag-t figyeli.

[A teljes megoldást felépítheti a Visual Studióban](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) , és [Azure Resource Manager sablonnal is üzembe helyezheti a megoldást](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). A megoldás létrehozásával kapcsolatos videós bemutatóban [tekintse meg ezt a Channel 9 videót](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Aktiválás az ügyféladatok alapján

1. Hozzon létre egy üres logikai alkalmazást a Azure Portal vagy a Visual Studióban. 

   Ha most ismerkedik a Logic apps [szolgáltatással](../logic-apps/quickstart-create-first-logic-app-workflow.md) , tekintse át a Azure Portal vagy a [Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)rövid útmutatóját.

2. A Logic app Designerben keresse meg és adja hozzá a Twitter-triggert, amely a következő művelettel rendelkezik: **új Tweet közzétételekor**

3. Állítsa be úgy a triggert, hogy a tweeteket egy kulcsszó vagy hashtag alapján figyelje.

   A lekérdezésen alapuló eseményindítók, például a Twitter-eseményindító esetében az ismétlődési tulajdonság határozza meg, hogy a logikai alkalmazás milyen gyakran keressen új elemeket.

   ![Twitter-trigger – példa][1]

Ez a logikai alkalmazás most már minden új tweeten bekövetkezik. Ezután elvégezheti és elemezheti a tweet-adatmennyiséget, így jobban megismerheti a kifejezett érzelmeket. 

## <a name="analyze-tweet-text"></a>Tweet szövegének elemzése

Egy bizonyos szöveg mögötti hangulat észleléséhez használhatja az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. A Logic app Designerben az trigger alatt válassza az **új lépés** lehetőséget.

2. Keresse meg az **text Analytics** -összekötőt.

3. Válassza az **érzelmek észlelése** műveletet.

4. Ha a rendszer kéri, adjon meg egy érvényes Cognitive Services kulcsot a Text Analytics szolgáltatáshoz.

5. A **kérelem törzse** területen válassza a **Tweet szövege** mezőt, amely a tweet szövegét adja meg az elemzéshez.

Miután megszerezte a tweetek adatait és a tweettel kapcsolatos információkat, mostantól számos más kapcsolódó összekötőt és a műveleteit is használhatja:

* **Power bi – sorok hozzáadása a folyamatos átviteli adatkészlethez**: megtekintheti a bejövő tweeteket egy Power bi irányítópulton.
* **Azure Data Lake – fájl hozzáfűzése**: ügyféladatok hozzáadása egy Azure Data Lake adatkészlethez, amely az elemzési feladatokban szerepel.
* **SQL – sorok hozzáadása**: adatok tárolása egy adatbázisban későbbi lekéréshez.
* **Tartalékidő – üzenet küldése**: egy Slack-csatorna értesítése arról, hogy milyen negatív visszajelzésre lehet szükség.

Létrehozhat egy Azure-függvényt is, amely segítségével egyéni feldolgozást végezhet az adatokon. 

## <a name="process-data-with-azure-functions"></a>Az adatfeldolgozás Azure Functions

A függvények létrehozása előtt hozzon létre egy Function alkalmazást az Azure-előfizetésében. Emellett ahhoz, hogy a logikai alkalmazás közvetlenül hívjon egy függvényt, a függvénynek rendelkeznie kell egy HTTP-trigger kötéssel, például használja a **HttpTrigger** sablont. Ismerje meg, [hogyan hozhatja létre az első Function-alkalmazást és-függvényt a Azure Portalban](../azure-functions/functions-get-started.md).

Ebben az esetben a tweet szövege legyen az Azure-függvény kérés törzse. A függvény kódjában adja meg azt a logikát, amely meghatározza, hogy a tweet szövege tartalmaz-e kulcsszót vagy kifejezést. A függvényt a forgatókönyvhöz szükséges egyszerűként vagy összetettebbként tartsa meg.
A függvény végén a logikai alkalmazásra adott válaszként egy adott adatra, például egy egyszerű logikai értékre (például `containsKeyword` vagy egy összetett objektumra) vonatkozó választ ad vissza.

> [!TIP]
> A logikai alkalmazásokban lévő függvények összetett válaszának eléréséhez használja a JSON- **elemzés** műveletet.

Ha elkészült, mentse a függvényt, majd adja hozzá a függvényt a felépített logikai alkalmazásban.

## <a name="add-azure-function-to-logic-app"></a>Azure-függvény hozzáadása a logikai alkalmazáshoz

1. A Logic app Designerben az **érzelmek észlelése** művelet alatt válassza az **új lépés** lehetőséget.

2. Keresse meg az **Azure functions** -összekötőt, majd válassza ki a létrehozott függvényt.

3. A **kérelem törzse** területen válassza a **Tweet szövege** elemet.

![Konfigurált Azure Function lépés][2]

## <a name="run-and-monitor-your-logic-app"></a>A logikai alkalmazás futtatása és figyelése

A logikai alkalmazás aktuális vagy korábbi futtatásának áttekintéséhez használhatja a Azure Portal, a Visual Studióban vagy az Azure REST API-kon és SDK-kon keresztül Azure Logic Apps által nyújtott részletes hibakeresési és figyelési képességeket.

A logikai alkalmazás egyszerű teszteléséhez a Logic app Designerben válassza az **trigger futtatása** lehetőséget. Az trigger a megadott ütemezés alapján kérdezi le a tweeteket, amíg meg nem találja a feltételeknek megfelelő tweetet. A Futtatás előrehaladtával a tervező élő nézetet jelenít meg a futtatáshoz.

A Visual Studióban vagy a Azure Portalban megtekintheti az előző futtatási előzményeket: 

* Nyissa meg a Visual Studio Cloud Explorer alkalmazást. Keresse meg a logikai alkalmazást, és nyissa meg az alkalmazás helyi menüjét. Válassza a **futtatási előzmények megnyitása** lehetőséget.

  > [!TIP]
  > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

* A Azure Portal keresse meg a logikai alkalmazást. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. 

## <a name="create-automated-deployment-templates"></a>Automatizált üzembehelyezési sablonok létrehozása

A Logic app-megoldás létrehozása után [Azure Resource Manager sablonként](../azure-resource-manager/templates/overview.md) rögzítheti és telepítheti az alkalmazást a világ bármely Azure-régiójába. Ezzel a képességgel módosíthatók az alkalmazás különböző verzióinak létrehozására és a megoldás Azure-folyamatokra való integrálására szolgáló paraméterek is. A központi telepítési sablonban Azure Functions is megadhat, így egyetlen sablonként kezelheti a teljes megoldást és az összes függőséget. Útmutató a [logikai alkalmazások telepítésének automatizálásához](logic-apps-azure-resource-manager-templates-overview.md).

Egy Azure-függvényt tartalmazó központi telepítési sablonhoz az [Azure Gyorsindítás sablon adattárában](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)tájékozódhat.

## <a name="next-steps"></a>Következő lépések

* [További példák és forgatókönyvek a Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png