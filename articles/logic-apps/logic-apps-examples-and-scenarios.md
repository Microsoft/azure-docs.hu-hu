---
title: Példák & gyakori forgatókönyvekre
description: Példákat, gyakori forgatókönyveket, oktatóanyagokat és útmutatókat talál a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317379"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Gyakori forgatókönyvek, példák, oktatóanyagok és útmutatók Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) a különböző szolgáltatások összehangolása és integrálása révén [több száz használatra kész összekötőt](../connectors/apis-list.md)biztosít, amelyek SQL Server vagy SAP-ről az Azure-ra Cognitive Services. A Logic Apps szolgáltatás "kiszolgáló nélküli", így nem kell aggódnia a méretezéssel vagy a példányokkal kapcsolatban. Mindössze annyit kell tennie, hogy meghatározza a munkafolyamatot egy triggerrel, valamint a munkafolyamat által végrehajtott műveleteket. Az alapul szolgáló platform kezeli a skálázást, a rendelkezésre állást és a teljesítményt. Logic Apps különösen hasznos olyan használati esetekhez és forgatókönyvekhez, ahol több rendszer és szolgáltatás között kell összehangolni a műveleteket.

A Azure Logic Apps által támogatott képességek és minták megismerése érdekében ez a cikk általános kiindulási pontokat, példákat és forgatókönyveket ismertet.

## <a name="common-starting-points-for-logic-app-workflows"></a>A Logic app-munkafolyamatok gyakori kiindulási pontjai

Minden logikai alkalmazás egy [*triggerrel*](../logic-apps/logic-apps-overview.md#logic-app-concepts)kezdődik, és csak egy triggert indít el, amely elindítja a logikai alkalmazás munkafolyamatát, és az adott trigger részeként továbbítja az összes adatmennyiséget. Egyes összekötők eseményindítókat biztosítanak, amelyek a következő típusokból származnak:

* *Lekérdezési eseményindítók*: rendszeresen ellenőrzi a szolgáltatási végpontot az új adatértékekhez. Ha új adatok állnak rendelkezésre, az trigger létrehoz és futtat egy új munkafolyamat-példányt, amely az adatokat bemenetként adja meg.

* *Leküldéses eseményindítók*: figyeli az adatszolgáltatási végponton tárolt adatkéréseket, és megvárja, amíg egy adott esemény bekövetkezik. Ha az esemény történik, az eseményindító azonnal elindul, és egy új munkafolyamat-példányt hoz létre és futtat, amely bármely elérhető adatot használ bemenetként.

Az alábbi példák a leggyakrabban használt eseményindítókat írják le:

* *Lekérdezési* eseményindítók:

  * Az [ **ismétlődési** eseményindítóval](../connectors/connectors-native-recurrence.md) megadható a kezdő dátum és idő, valamint a logikai alkalmazás égetésének ismétlődése. Kiválaszthatja például a hét napjait és a nap időpontját a logikai alkalmazás aktiválásához. További információt az alábbi témakörökben talál:<p>

    * [Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps használatával](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Oktatóanyag: automatizált, ütemezett ismétlődő munkafolyamatok létrehozása Azure Logic Apps használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **Ha a rendszer elküld egy e-mailt** , a logikai alkalmazás új e-maileket keres a Logic apps által támogatott bármely levelezési szolgáltatótól, például az [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), a [Gmail](/connectors/gmail/), a [Outlook.com](/connectors/outlook/)stb.

    > [!IMPORTANT]
    > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

    További információt az alábbi témakörökben talál:<p>

    * [Oktatóanyag: automatikus jóváhagyási alapú munkafolyamatok létrehozása Azure Logic Apps használatával](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Oktatóanyag: feladatok automatizálása az e-mailek feldolgozásához Azure Logic Apps, Azure Functions és Azure Storage használatával](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * A [ **http** -trigger](../connectors/connectors-native-http.md) http-vagy https-kapcsolaton keresztül hívhat egy szolgáltatási végpontot. További információ: [munkafolyamatok hívása, elindítása vagy beágyazása http-végpontok használatával](../logic-apps/logic-apps-http-endpoint.md).

* *Leküldéses* eseményindítók:

  * A [ **kérelem** triggere](../connectors/connectors-native-reqres.md) FOGADhatja a bejövő HTTPS-kéréseket.

  * A [ **http-webhook** -trigger](../connectors/connectors-native-webhook.md) a szolgáltatáshoz tartozó *visszahívási URL-cím* regisztrálásával előfizet egy szolgáltatási végpontra. Így a szolgáltatás csak akkor értesíti az eseményindítót, ha a megadott esemény történik, így az eseményindítónak nem kell lekérdezni a szolgáltatást.

A megadott esemény bekövetkezése után az eseményindító elindul, amely létrehoz egy új logikai alkalmazás-munkafolyamati példányt, és futtatja a műveleteket a munkafolyamatban. Az trigger összes adatait elérheti a munkafolyamaton belül. A Twitter **egy új Tweet** -trigger esetében például átadja a tweet tartalmát a logikai alkalmazás futtatásának. A Azure Logic Apps használatának megkezdéséhez próbálja ki a következő rövid útmutatót:

* [Rövid útmutató: az első automatizált munkafolyamat létrehozása Azure Logic Apps-Azure Portal használatával](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Gyors útmutató: automatizált feladatok, folyamatok és munkafolyamatok létrehozása a Azure Logic Apps-Visual Studio használatával](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gyors útmutató: automatizált logikai alkalmazások munkafolyamatainak létrehozása és kezelése a Visual Studio Code használatával](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Vezérlési folyamat és hibakezelés

A Logic apps többek között a speciális vezérlési folyamatok, például a feltételek, kapcsolók, hurkok és hatókörök széles körű funkcióit tartalmazza. A rugalmas megoldások biztosításához a munkafolyamatokban a hibák és kivételek kezelését is megvalósíthatja.

* Különböző műveletek végrehajtása [feltételes utasítások](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [switch utasítások](../logic-apps/logic-apps-control-flow-switch-statement.md) alapján
* [Tömbökben és gyűjteményekben lévő elemek ismétlése vagy feldolgozása hurkokkal](../logic-apps/logic-apps-control-flow-loops.md)
* [Műveletek csoportosítása hatókörökkel együtt](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Hiba-és kivételek hozzáadása egy munkafolyamathoz](../logic-apps/logic-apps-exception-handling.md)
* [Használati eset: hogyan használja az egészségügyi vállalat a Logic app-kivételeket a HL7 FHIR-munkafolyamatok kezeléséhez](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Egyéni API-k és összekötők létrehozása

A közzétett összekötőket nem tartalmazó rendszerekhez és szolgáltatásokhoz a logikai alkalmazásokat is kiterjesztheti.

* [Egyéni API-k létrehozása a Azure Logic Apps meghívásához](../logic-apps/logic-apps-create-api-app.md)
* [Új adatértékek vagy események keresése rendszeresen a lekérdezési trigger mintájának használatával](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Várjon, és figyelje az új adattípusokat vagy eseményeket a webhook trigger mintázatának használatával](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Hosszan futó feladatok végrehajtása a lekérdezési művelet mintájának használatával](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Hosszan futó feladatok végrehajtása a webhook műveleti mintázatának használatával](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Egyéni összekötők a Azure Logic Appsban](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Vállalatközi (B2B) megoldások létrehozása

A nagyvállalati integrációs megoldások és a szervezetek közötti zökkenőmentes kommunikáció érdekében a Enterprise Integration Pack (EIP) és a Azure Logic Apps használatával automatizált skálázható munkafolyamatokat hozhat létre ezekhez a forgatókönyvekhez. Bár a szervezetek különböző protokollokat és formátumokat használnak, elektronikus úton is cserélhetnek üzeneteket. A EIP különböző formátumokat alakít át egy olyan formátumba, amelyet a szervezet rendszerei feldolgozhatnak és támogatnak az iparági szabványoknak megfelelő protokollokat, beleértve az AS2, a X12, a EDIFACT és a RosettaNet. A megoldások létrehozásához létre kell hoznia egy integrációs fiókot, amely egy különálló Azure-erőforrás, amely biztonságos, méretezhető és kezelhető tárolót biztosít a logikai alkalmazás munkafolyamataihoz definiált és használható összetevőkhöz. Ilyenek például a kereskedelmi partnerek, a szerződések, a térképek, a sémák, a tanúsítványok és a Batch-konfigurációk.

* [Áttekintés: B2B vállalati integrációs megoldások Azure Logic Apps és Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Integrációs fiókok létrehozása és felügyelete B2B vállalati integrációkhoz az Azure Logic Appsben](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Azure-beli virtuális hálózati erőforrások elérése

Időnként a logikai alkalmazásoknak és az integrációs fiókoknak olyan biztonságos erőforrásokhoz, például virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz kell hozzáférnie, amelyek egy Azure-beli virtuális hálózaton vannak. A hozzáférés beállításához létrehozhat egy integrációs szolgáltatási környezetet (ISE), ahol létrehozhatja és futtathatja a logikai alkalmazásokat. Az ISE a Logic Apps szolgáltatás privát és elkülönített példánya, amely dedikált erőforrásokat, például tárterületet használ, és külön fut a nyilvános, a "globális", a több-bérlős Logic Apps szolgáltatástól. Ha elválasztja az elkülönített privát példányt és a nyilvános globális példányt, azzal csökkentheti annak hatását, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére, ami más néven "zajos szomszédok".

* [Áttekintés: hozzáférés az Azure-beli virtuális hálózati erőforrásokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Logic apps üzembe helyezése, kezelése és figyelése

A Visual Studióval, az Azure DevOps vagy bármely más verziókövetés és automatizált Build eszközzel teljes mértékben fejlesztheti és telepítheti a Logic apps-alkalmazásokat. Az erőforrás-sablonban a munkafolyamatok és a függő kapcsolatok üzembe helyezésének támogatásához a Logic apps Azure erőforrás-telepítési sablonokat használ. A Visual Studio-eszközök automatikusan létrehozzák ezeket a sablonokat, amelyek bejelentkezhetnek a verziókövetés verziókövetésba. A munkafolyamat-futtatási állapottal kapcsolatos értesítési és diagnosztikai naplók esetében a Azure Logic Apps figyelést és riasztásokat is biztosít.

### <a name="deploy"></a>Üzembe helyezés

* [Gyors útmutató: automatizált feladatok, folyamatok és munkafolyamatok létrehozása a Azure Logic Apps-Visual Studio használatával](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Áttekintés: logikai alkalmazások központi telepítésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager-sablonok létrehozása az Azure Logic Apps-alkalmazások üzembe helyezésének automatizálásához](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sablonok üzembe helyezése Azure Logic Apps-alkalmazásokhoz](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Minta: Kapcsolódás Azure Service Bus várólistákhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Kapcsolódás Azure Storage-fiókokhoz Azure Logic Apps és üzembe helyezés az Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Function app-művelet beállítása az Azure DevOps Azure-folyamatokkal való üzembe helyezéséhez és üzembe helyezéséhez Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: csatlakozás egy integrációs fiókhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Példa: az Azure-folyamatok összehangolása Azure Logic Apps használatával](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Kezelés

* [Logikai alkalmazások kezelése a Visual Studio használatával](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Integrációs fiókok létrehozása és kezelése a B2B vállalati integrációk számára](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Integrációs szolgáltatási környezet (ISE) kezelése Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitor

* [Futtatási állapot monitorozása, triggerelőzmények áttekintése és riasztások beállítása az Azure Logic Appshez](../logic-apps/monitor-logic-apps.md)
* [Azure Monitor naplók beállítása és diagnosztikai adatok összegyűjtése Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Azure Monitor-naplók beállítása és diagnosztikai adatok begyűjtése a B2B-üzenetekre vonatkozóan az Azure Logic Appsben](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [A Azure Monitor-naplók figyelésére és nyomon követésére szolgáló lekérdezések megtekintése és létrehozása Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>A tartalomtípusok, a konverziók és az átalakítások kezelése

Több tartalomtípus elérését, átalakítását és átalakítását a Azure Logic Apps [munkafolyamat-definíciós nyelv](./logic-apps-workflow-definition-language.md)számos funkciója segítségével végezheti el. A karakterlánc, a JSON és az XML között például a `@json()` és a `@xml()` munkafolyamat kifejezéseket lehet konvertálni. A Logic Apps motor megőrzi a tartalomtípusokat, hogy a szolgáltatások között veszteségmentes módon támogassa a tartalom átvitelét.

* A Azure Logic Apps, például a, és [a tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md) `application/` `application/octet-stream``multipart/formdata`
* [A függvények kifejezésekben való használatát ismertető útmutató a Azure Logic Apps és az energiagazdálkodás automatizálásához](../logic-apps/workflow-definition-language-functions-reference.md)
* [Munkafolyamat-definíció nyelvi sémája Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Egyéb integrációk és képességek

A Azure Logic Apps számos szolgáltatással integrálható, például a Azure Functions, az Azure API Management, a Azure App Service és az egyéni HTTP-végpontok, például a REST és a SOAP.

* [Azure Functions hívása Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Oktatóanyag: logikai alkalmazások meghívása vagy elindítása Azure Functions és Azure Service Bus használatával](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Oktatóanyag: streaming Customer-adatáttekintési irányítópult létrehozása Azure Logic Apps és Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Oktatóanyag: az Azure Logic Apps és az Azure Cognitive Services integrációs funkciójának létrehozása a Twitter utáni érzelmek elemzéséhez](../azure-functions/functions-twitter-email.md)
* [Oktatóanyag: AI-alapú közösségi irányítópult létrehozása Power BI és Azure Logic Apps használatával](https://aka.ms/logicappsdemo)
* [Oktatóanyag: Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps használatával](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Oktatóanyag: a IoT távoli figyelése és értesítései Azure Logic Apps az IoT hub és a postaláda csatlakoztatásával](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: a SOAP-szolgáltatások meghívása Azure Logic Apps használatával](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Végpontok közötti forgatókönyvek

* [Tanulmány: az Azure-szolgáltatásokkal való teljes körű ügyviteli integráció, például Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Ügyfelek történetei

Ismerje meg, hogy a Azure Logic Apps, valamint más Azure-szolgáltatások és Microsoft-termékek mellett a vállalatok az összetett folyamatok egyszerűsítésével, rendszerezésével, automatizálásával és előkészítésével javítsák a [vállalatuk](https://aka.ms/logic-apps-customer-stories) rugalmasságát és az alapvető üzleti tevékenységekre koncentrálva.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg [Logic apps összekötői](../connectors/apis-list.md)
* Ismerkedjen meg a [B2B vállalati integrációs forgatókönyvekkel Azure Logic apps](../logic-apps/logic-apps-enterprise-integration-overview.md)