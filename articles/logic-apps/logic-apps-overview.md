---
title: A Azure Logic Apps áttekintése
description: A Azure Logic Apps felhőalapú megoldás olyan automatizált munkafolyamatok létrehozásához és előkészítéséhez, amelyek az alkalmazásokat, az adatok, a szolgáltatások és a rendszerek integrálását teszik ki a nagyvállalati szintű forgatókönyvek minimális kódjával.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 03/11/2020
ms.openlocfilehash: 4c4e8c8f809452286fee493e20dbef2cc538c8bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96921541"
---
# <a name="what-is-azure-logic-apps"></a>Mi az Azure Logic Apps?

A [Azure Logic apps](https://azure.microsoft.com/services/logic-apps) egy felhőalapú szolgáltatás, amellyel a feladatok, az üzleti folyamatok és a [munkafolyamatok](#logic-app-concepts) ütemezhetők, automatizálható és hangolható össze, ha az alkalmazások, adatok, rendszerek és szolgáltatások integrálására van szükség a vállalatok és a szervezetek között. Logic Apps leegyszerűsíti az alkalmazások [integrálására](https://azure.microsoft.com/product-categories/integration/), az adatintegrációra, a rendszer-integrációra, a vállalati alkalmazások integrálására (EAI) és a VÁLLALATKÖZI (B2B) kommunikációra szolgáló méretezhető megoldások tervezését és kialakítását, akár a felhőben, akár a helyszínen, akár mindkettőn.

A logikai alkalmazásokkal többek között a következő munkafolyamatok automatizálhatók:

* Rendelések feldolgozása és irányítása a helyszíni rendszerek és a felhőszolgáltatások között.

* E-mail-értesítések küldése az Office 365-tel, ha valamilyen esemény következik be különböző rendszerekben, alkalmazásokban és szolgáltatásokban.

* Feltöltött fájlok áthelyezése egy SFTP- vagy FTP-kiszolgálóról az Azure Storage-ba.

* Egy adott témához tartozó tweetek monitorozása, vélemények elemzése, valamint riasztások és feladatok létrehozása az ellenőrzést igénylő elemekhez.

Ha Azure Logic Apps segítségével szeretne nagyvállalati integrációs megoldásokat kiépíteni, [több száz használatra kész összekötővel](../connectors/apis-list.md)is választhat, amelyek olyan szolgáltatásokat foglalnak magukban, mint például a Azure Service Bus, a Azure functions, az Azure Storage, a SQL Server, az Office 365, a Dynamics, a Salesforce, a BizTalk, az SAP, a Oracle db, a fájlmegosztás és egyebek. Az [összekötők](#logic-app-concepts)[triggereket](#logic-app-concepts) és [műveleteket](#logic-app-concepts) biztosítanak az olyan logikai alkalmazások létrehozásához, amelyek valós időben, biztonságosan érik el és dolgozzák fel az adatokat.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

## <a name="how-do-logic-apps-work"></a>Hogyan működnek a Logic apps? 

Minden logikai alkalmazás munkafolyamata egy eseményindítóval indul, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy egy adott feltételnek megfelelő új adatok válnak elérhetővé. A Logic Apps összekötői által biztosított számos eseményindító tartalmaz alapszintű ütemezési funkciókat, így beállíthatja, hogy a számítási feladatok milyen gyakran fussanak. Összetettebb ütemezések vagy speciális ismétlődések esetén bármelyik munkafolyamat első lépéseként használhat ismétlődési eseményindítót. További információ a [Schedule-alapú munkafolyamatokról](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

A Logic Apps-motor a trigger minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely futtatja a munkafolyamatban található műveleteket. Ezek a műveletek magukban foglalhatják az adatkonverziókat és a munkafolyamat-vezérlőket, például a feltételes utasításokat, a kapcsoló utasításait, a hurkokat és az elágazást is. Például a következő logikai alkalmazást egy Dynamics 365-eseményindító aktiválja, amely a beépített „Amikor egy rekord frissül” feltételt tartalmazza. Ha az eseményindító egy olyan eseményt észlel, amely megfelel ennek a feltételnek, akkor aktiválódik, majd elindítja a munkafolyamat műveleteit. Esetünkben e műveletek közé tartoznak az XML-átalakítások, az adatfrissítések, a döntések elágaztatása és ez e-mailes értesítések.

![Logic Apps Designer – példa egy logikai alkalmazásra](./media/logic-apps-overview/azure-logic-apps-designer.png)

Logikai alkalmazásait vizuálisan hozhatja létre a Logic Apps Designer segítségével, amely a böngészőn keresztül az Azure Portalon, illetve a Visual Studióban érhető el. További egyéni logikai alkalmazásokért létrehozhat vagy szerkeszthet JavaScript Object Notation (JSON) logikaialkalmazás-definíciókat a „kódnézet” szerkesztő alkalmazásával. Bizonyos feladatokat Azure PowerShell-parancsokkal és az Azure Resource Manager-sablonokkal is elvégezhet. A logikai alkalmazások az Azure felhőjében üzemelnek és futnak. Részletesebb bevezetésért tekintse meg a következő videót: [Az Azure vállalati integrációs szolgáltatások használata felhőalkalmazások futtatásához ipari méretekben](https://channel9.msdn.com/Events/Connect/2017/T119/)

## <a name="why-use-logic-apps"></a>Miért érdemes a Logic Apps szolgáltatást használni?

A legtöbb vállalat a digitalizáció felé halad, a Logic Apps pedig segít egyszerűbben és gyorsabban kapcsolatot teremteni a régebbi és a modern rendszerek között a Microsoft által felügyelt összekötőkként biztosított beépített API-k segítségével. Így Ön az alkalmazások üzleti logikáira és funkcióira koncentrálhat. Nem kell az alkalmazások létrehozásával, üzemeltetésével, méretezésével, felügyeletével, karbantartásával és monitorozásával foglalkoznia. Ezeket a Logic Apps elvégzi Ön helyett. Emellett a használatalapú [díjszabás](../logic-apps/logic-apps-pricing.md) keretében csak a valóban használt funkciókért kell fizetni.

A legtöbb esetben kódot írni sem kell. De ha mégis, akkor a kódrészleteket létrehozhatja az [Azure Functions](../azure-functions/functions-overview.md) segítségével, majd igény szerint futtathatja azokat a logikai alkalmazásokból. Ha a logikai alkalmazásoknak az Azure-szolgáltatásokból, egyéni alkalmazásokból vagy más gyártóktól származó megoldásokból származó eseményekkel kell interakcióba lépniük, akkor használhatja az [Azure Event Grid](../event-grid/overview.md) szolgáltatást az események monitorozásához, útválasztásához és közzétételéhez.

A Logic Apps, a Functions és az Event Grid szolgáltatásokat teljes mértékben a Microsoft Azure felügyeli, így Önnek nem kell foglalkoznia a megoldásai létrehozásával, üzemeltetésével, méretezésével, felügyeletével és monitorozásával. A [„kiszolgáló nélküli” alkalmazások és megoldások](../logic-apps/logic-apps-serverless-overview.md) létrehozásának képessége lehetővé teszi, hogy Ön csak az üzleti logikával foglalkozzon. A szolgáltatások az igényekhez igazodva automatikusan elvégzik a méretezést, felgyorsítják az integrációkat és lehetővé teszik robusztus felhőalkalmazások létrehozását úgy, hogy közben csak minimális kódírásra van szükség.

Az [ügyféltörténetekből](https://aka.ms/logic-apps-customer-stories) többet is megtudhat arról, hogy az egyes vállalatok hogyan tudtak nagyobb rugalmasságot elérni és jobban a fő üzleti tevékenységeikre koncentrálni, amikor a Logic Apps-alkalmazásokat egyéb Azure-szolgáltatásokkal és Microsoft-termékekkel kombinálták.

További információk a Logic Apps által kínált képességekről és előnyökről:

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>Munkafolyamatok vizuális létrehozása könnyen használható eszközökkel

A vizuális tervezőeszközökkel időt takaríthat meg és egyszerűsítheti az összetett folyamatokat. A logikai alkalmazások létrehozásának minden lépését elvégezheti a Logic Apps Designer segítségével, amely a böngészőből az Azure Portalon, illetve a Visual Studióban is elérhető. Indítsa el a munkafolyamatot egy eseményindítóval, majd adjon hozzá bármennyi műveletet az [összekötő-katalógusból](../connectors/apis-list.md).

### <a name="get-started-faster-with-logic-app-templates"></a>Gyorsabban láthat munkához a logikai alkalmazások sablonjaival

A gyakran használt megoldásokat gyorsabban létrehozhatja, ha a [sablonkatalógus](../logic-apps/logic-apps-create-logic-apps-from-templates.md) előre megadott munkafolyamatai közül választ. A sablonok az egyszerű SaaS-kapcsolatokat biztosító alkalmazásoktól a speciális B2B-megoldásokig terjednek, de van néhány olyan is, amely csak kedvtelésből készült. További információk: [Logikai alkalmazások létrehozása előre megadott sablonból](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

### <a name="connect-disparate-systems-across-different-environments"></a>Különböző környezetek különálló rendszereinek csatlakoztatása

Bizonyos mintákat és munkafolyamatokat könnyű leírni, de annál nehezebb a programkódban implementálni. A logikai alkalmazások segítenek zökkenőmentes kapcsolatot teremteni a helyszíni és a felhőkörnyezetekben található különálló rendszerek között. Például összekapcsolhat egy felhőbeli marketingmegoldását a helyszíni számlázórendszerrel, vagy központosíthatja az API-k és rendszerek közötti üzenetküldést egy Enterprise Service Bus segítségével. A logikai alkalmazásokkal gyors, megbízható és konzisztens módon hozhatók létre újrahasznosítható és újrakonfigurálható megoldások ezekhez a forgatókönyvekhez.

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Vállalati integrációk és B2B-forgatókönyvek első osztályú támogatása

A vállalatok és az intézmények az iparági szabványoknak megfelelő, de különböző üzenetküldési protokollok és formátumok (pl. EDIFACT, AS2 és X12) használatával kommunikálnak egymással elektronikus formában. Az [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md) segítségével olyan logikai alkalmazásokat hozhat létre, amelyek átalakítják a partnerek által használt formátumban lévő üzeneteket saját intézménye rendszerei által felismerhető és feldolgozható formátumokba. A logikai alkalmazások ezeket az átalakításokat zökkenőmentesen és biztonságosan intézik, titkosítás és digitális aláírások használatával.

Kezdjen kicsiben a jelenlegi rendszereivel és szolgáltatásaival, majd érjen el folyamatos növekedést a saját tempójában. Ha készen áll, a Logic Apps és az EIP segítségével megvalósíthat és vertikálisan felskálázhat olyan fejlettebb integrációs forgatókönyveket, amelyek többek között a következő funkciókat biztosítják:

* Használja a következő termékeket és szolgáltatásokat:

  * [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)
  * [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Azure Functions](../azure-functions/functions-overview.md)
  * [Azure API Management](../api-management/api-management-key-concepts.md)

* [XML-üzenetek](../logic-apps/logic-apps-enterprise-integration-xml.md) feldolgozása

* [Lapos fájlok](../logic-apps/logic-apps-enterprise-integration-flatfile.md) feldolgozása

* Üzenetváltás az [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), és [X12](../logic-apps/logic-apps-enterprise-integration-x12.md) protokollokkal

* A B2B összetevők és egyéb elemek tárolása és kezelése egy helyen az [integrációs fiókok](./logic-apps-enterprise-integration-create-integration-account.md) segítségével:

  * [Partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [Egyezmények](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [XML-átalakítási leképezések](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [XML-érvényesítési sémák](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Ha például a Microsoft BizTalk Servert használja, a logikai alkalmazások a [BizTalk Server-összekötő](../connectors/apis-list.md#on-premises-connectors) segítségével kommunikálhatnak a BizTalk Serverrel. Ezután BizTalk-szerű műveleteket végezhet és terjeszthet ki a logikai alkalmazásokban [integrációs fiókösszekötők](../connectors/apis-list.md#integration-account-connectors) hozzáadásával, amelyek az Enterprise Integration Packban érhetők el.

Ez fordítva is működik, a BizTalk Server csatlakozhat a logikai alkalmazásokhoz és kommunikálhat velük a [Logic Appshez való Microsoft BizTalk Server-adapter](https://www.microsoft.com/download/details.aspx?id=54287) segítségével. Ismerje meg, hogyan [állítható be és használható a BizTalk Server-adapter](/biztalk/core/logic-app-adapter) a BizTalk Serverben.

### <a name="write-once-reuse-often"></a>Írja meg egyszer, használja újra bármikor

Hozza létre a logikai alkalmazásait Azure Resource Manager sablonként, így [automatizálhatja a logikai alkalmazások telepítését](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) több környezet és régió között.

### <a name="access-resources-inside-azure-virtual-networks"></a>Erőforrások elérése Azure-beli virtuális hálózatokon belül

Az [ *integrációs szolgáltatási környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)létrehozásakor a Logic apps hozzáférhet a biztonságos erőforrásokhoz, például a virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz, amelyek egy Azure-beli [virtuális hálózaton](../virtual-network/virtual-networks-overview.md) belül vannak. Az ISE a Logic Apps szolgáltatás dedikált példánya, amely dedikált erőforrásokat használ, és külön fut a "globális" több-bérlős Logic Apps szolgáltatástól.

A Logic apps önálló dedikált példányban való futtatása segít csökkenteni a más Azure-bérlők által az alkalmazások teljesítményére gyakorolt hatást, más néven a ["zajos szomszédok" hatást](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Az ISE a következő előnyöket is biztosítja:

* A saját statikus IP-címei, amelyek a több-bérlős szolgáltatásban a Logic apps által megosztott statikus IP-címektől eltérnek. Beállíthat egy nyilvános, statikus és kiszámítható kimenő IP-címet is a célszámítógépeken való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célszámítógépeken az egyes ISE-rendszerek esetében.

* A futtatási időtartam, a tárterület megőrzése, az átviteli sebesség, a HTTP-kérés és a válasz időkorlátja, az üzenetek mérete és az egyéni összekötőre vonatkozó kérelmek száma nagyobb. További információ: [Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md).

Ha ISE-t hoz létre, az Azure *befecskendez* vagy üzembe helyezi az ISE-t az Azure-beli virtuális hálózatában. Ezt az ISE-t használhatja azon logikai alkalmazások és integrációs fiókok helyeként, amelyeknek hozzáférésre van szükségük. Az ISE létrehozásával kapcsolatos további információkért lásd: [Kapcsolódás Azure-beli virtuális hálózatokhoz a Azure Logic Appsból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

### <a name="built-in-extensibility"></a>Beépített bővíthetőség

Ha nem találja az egyéni kódot futtató összekötőt, kiterjesztheti a logikai alkalmazásokat úgy, hogy az igény szerinti kódrészleteket a [Azure Functionson](../azure-functions/functions-overview.md)keresztül hozza létre és hívja fel. Létrehozhatja a saját [API-jait](../logic-apps/logic-apps-create-api-app.md) és [egyéni összekötőit](../logic-apps/custom-connector-overview.md), amelyeket meghívhat a logikai alkalmazásokból.

### <a name="pay-only-for-what-you-use"></a>Csak a valóban használt funkciókért kell fizetni
  
A Logic Apps használatalapú [díjszabást és méréseket](../logic-apps/logic-apps-pricing.md) használ, hacsak nincsenek korábbi, az App Service-csomagok segítségével létrehozott logikai alkalmazásai is.

Többet tudhat meg a Logic Apps szolgáltatásról az alábbi bevezető videókból:

* [Integráció a Logic Apps szolgáltatással – A nulláról a teljességig](https://channel9.msdn.com/Events/Build/2017/C9R17)
* [Vállalati integráció a Microsoft Azure Logic Apps használatával](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188)
* [Speciális üzleti folyamatok létrehozása a Logic Apps segítségével](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179)

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Miben különbözik a Logic Apps a functions, a webjobs és a Power automatizál?

Ezen szolgáltatások mindegyike a különböző rendszerek csatlakoztatásában és „összeragasztásában” segít. Mindegyiknek megvannak a maga előnyei, épp ezért a képességeik egyesítése a legjobb módja egy méretezhető, teljes körű képességekkel rendelkező integrációs rendszer létrehozásának. További információ: választás a [Logic apps, a functions, a webjobs és a Power automatizálása között](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Fontos kifejezések:

* **Munkafolyamat**: Üzleti folyamatok lépésenként való vizualizálása, megtervezése, létrehozása, automatizálása és üzembe helyezése.

* **Felügyelt összekötők**: A logikai alkalmazásoknak hozzá kell férniük az adatokhoz, szolgáltatásokhoz és rendszerekhez. Használhatja a Microsoft által felügyelt, előre létrehozott összekötőket, amelyek az adatok eléréséhez, hozzáféréséhez és használatához készültek. Tekintse [meg Azure Logic apps összekötőit](../connectors/apis-list.md).

* **Eseményindítók**: Számos, a Microsoft által felügyelt összekötő tartalmaz eseményindítókat, amelyek akkor aktiválódnak, ha egy esemény vagy valamilyen új adat megfelel egy bizonyos feltételnek. Például ilyen esemény lehet egy e-mail érkezése vagy változtatások észlelése az Ön Azure Storage-tárfiókjában. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy új logikaialkalmazás-példányt, amely futtatja a munkafolyamatot.

* **Műveletek**: A műveletek mindazon lépések, amelyek az eseményindító aktiválása után végbemennek. Általában minden művelet egy másik művelethez vezet, amelyet egy felügyelt összekötő, egy egyéni API vagy egy egyéni összekötő határoz meg.

* **Enterprise Integration Pack** – A további speciális integrálási forgatókönyvek megvalósítása érdekében a Logic Apps BizTalk Server-képességeket is tartalmaz. Az Enterprise Integration Pack csomag olyan összekötőket tartalmaz, amelyek megkönnyítik a logikai alkalmazások számára az érvényesítés, az átalakítás és egyéb műveletek végrehajtását.

## <a name="get-started"></a>Bevezetés

A Logic Apps a Microsoft Azure-ban üzemeltetett számos szolgáltatás egyike. Éppen ezért a használatához rendelkeznie kell Azure-előfizetéssel. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

Ha már van Azure-előfizetése, próbálja ki ezt a rövid útmutatót [az első logikai alkalmazása létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md), amely egy RSS-hírfolyamon keresztül monitoroz egy adott weboldalt, és az új tartalmak megjelenésekor e-mailes értesítést küld.

## <a name="next-steps"></a>Következő lépések

* [Forgalom ellenőrzése ütemezésalapú logikai alkalmazás használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* További információk [az Azure kiszolgáló nélküli megoldásairól](../logic-apps/logic-apps-serverless-overview.md)
* További információk [az Enterprise Integration Pack segítségével végzett B2B-integrációról](../logic-apps/logic-apps-enterprise-integration-overview.md).
