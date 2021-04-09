---
title: Folyamatos üzletmenet és vészhelyreállítás
description: Tervezze meg a stratégiát az adatok védelme érdekében, gyorsan helyreállíthatja a zavaró eseményektől, visszaállíthatja a kritikus üzleti funkciókhoz szükséges erőforrásokat, és megőrizheti az üzletmenet folytonosságát Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89658198"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Üzletmenet-folytonosság és vész-helyreállítási Azure Logic Apps

Annak érdekében, hogy csökkentse a nem előre jelezhető események hatásait és hatásait a vállalatra és az ügyfelekre, győződjön meg arról, hogy van egy vész- [ *helyreállítási* (Dr)](https://en.wikipedia.org/wiki/Disaster_recovery) megoldás, amely lehetővé teszi az adatok védelméhez, a kritikus üzleti funkciókat támogató erőforrások gyors helyreállításához, valamint az [ *üzletmenet folytonosságának*](https://en.wikipedia.org/wiki/Business_continuity_planning)fenntartásához szükséges műveletek megtartását. Például a fennakadások lehetnek kimaradások, az alapul szolgáló infrastruktúra vagy összetevők, például a tárterület, a hálózat vagy a számítási erőforrások, a helyreállítható alkalmazások meghibásodása vagy akár egy teljes adatközpont elvesztése miatti veszteségek. Ha egy üzletmenet-folytonossági és vész-helyreállítási (BCDR) megoldás áll készen, a vállalata vagy szervezete gyorsabban reagálhat a megszakításokra, a tervezett és a nem tervezett állapotokra, és csökkentheti az ügyfelek leállását.

Ez a cikk BCDR útmutatást és stratégiákat tartalmaz, amelyek akkor alkalmazhatók, ha [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával hoz létre automatizált munkafolyamatokat. A Logic app-munkafolyamatok segítségével könnyebben integrálhatja és koordinálhatja az alkalmazásokat, a Cloud Servicest és a helyszíni rendszereket az alkalmazások között, így csökkentve az írási kód mennyiségét. A BCDR tervezésekor ügyeljen arra, hogy ne csak a logikai alkalmazásait, hanem a logikai alkalmazásokkal használt Azure-erőforrásokat is vegye figyelembe:

* A Logic appsből más alkalmazásokra, szolgáltatásokra és rendszerekre létrehozott [kapcsolatok](../connectors/apis-list.md) . További információkért lásd a témakör későbbi, az [erőforrásokhoz való kapcsolódását](#resource-connections) ismertető szakaszt.

* [A helyszíni adatátjárók](../logic-apps/logic-apps-gateway-connection.md) , amelyek olyan Azure-erőforrások, amelyeket a logikai alkalmazásokban hoz létre és használ a helyszíni rendszerekben lévő információk eléréséhez. Minden átjáró-erőforrás egy különálló [adatátjáró-telepítést](../logic-apps/logic-apps-gateway-install.md) jelöl a helyi számítógépen. További információkért lásd a jelen témakör későbbi, helyszíni [adatátjárók című szakaszát](#on-premises-data-gateways) .

* [Integrációs fiókok](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , ahol meghatározhatja és tárolhatja a Logic apps által a [vállalatközi (B2B) vállalati integrációs](../logic-apps/logic-apps-enterprise-integration-overview.md) forgatókönyvekhez használt összetevőket. [Beállíthatja például a régiók közötti vész-helyreállítást az integrációs fiókokhoz](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Integrációs szolgáltatási környezet (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , ahol olyan logikai alkalmazásokat hozhat létre, amelyek egy Azure-beli virtuális hálózaton belül elkülönített Logic apps Runtime-példányban futnak. Ezek a logikai alkalmazások ezután hozzáférhetnek a virtuális hálózat tűzfala mögött védett erőforrásokhoz.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Elsődleges és másodlagos helyen

Minden logikai alkalmazásnak meg kell adnia az üzembe helyezéshez használni kívánt helyet. Ez a hely vagy egy nyilvános régió a több-bérlős Azure-ban, mint például a "West US", vagy egy integrációs szolgáltatási környezet (ISE), amelyet korábban létrehozott és üzembe helyezett egy Azure-beli virtuális hálózatban. A Logic apps egy ISE-ben való futtatása hasonló a logikai alkalmazások globális Azure-régióban való futtatásához, ami azt jelenti, hogy a vész-helyreállítási stratégia mindkét forgatókönyvre alkalmazható. A ISEs azonban más szempontokat is figyelembe kell vennie, mint például a csak ISEs számára elérhető erőforrásokhoz való hozzáférés konfigurálása.

> [!NOTE]
> Ha a logikai alkalmazás olyan B2B-összetevőkkel is működik, mint például a kereskedelmi partnerek, szerződések, sémák, térképek és tanúsítványok, amelyek egy integrációs fiókban tárolódnak, akkor az integrációs fióknak és a Logic apps-nek is ugyanazt a helyet kell megadnia.

Ez a vész-helyreállítási stratégia arra összpontosít, hogy egy másik helyen állítsa be az elsődleges logikai alkalmazás [*feladatátvételét*](https://en.wikipedia.org/wiki/Failover) egy készenléti vagy biztonsági mentési logikai alkalmazásba, ahol Azure Logic apps is elérhető. Így ha az elsődlegesen veszteségek, fennakadások vagy meghibásodások merülnek fel, a másodlagos folyamat elvégezheti a munkát. Ehhez a stratégiához az szükséges, hogy a másodlagos logikai alkalmazás és a függő erőforrások már telepítve legyenek, és készen álljanak a másik helyre.

Ha követi a megfelelő DevOps gyakorlatokat, a logikai alkalmazások és a függő erőforrások definiálásához és üzembe helyezéséhez már [Azure Resource Manager sablonokat](../azure-resource-manager/management/overview.md) is használhat. A Resource Manager-sablonok lehetővé teszik egyetlen központi telepítési definíció használatát, majd a paraméterek használatával megadják az egyes telepítési célhelyekhez használandó konfigurációs értékeket. Ez a funkció azt jelenti, hogy ugyanazt a logikai alkalmazást különböző környezetekben, például fejlesztési, tesztelési és éles környezetben is üzembe helyezheti. Ugyanezt a logikai alkalmazást különböző Azure-régiókba vagy ISEs is üzembe helyezheti, amely támogatja a [párosított régiókat](../best-practices-availability-paired-regions.md)használó vész-helyreállítási stratégiákat.

A feladatátvételi stratégia esetében a logikai alkalmazásoknak és helyeknek meg kell felelniük az alábbi követelményeknek:

* A másodlagos logikai alkalmazás példánya ugyanazon alkalmazásokhoz, szolgáltatásokhoz és rendszerekhez fér hozzá, mint az elsődleges logikai alkalmazás példánya.

* Mindkét logikai alkalmazás példányának ugyanaz a gazdagép-típusa. Így mindkét példány üzembe helyezése a globális több-bérlős Azure-beli régiókban történik, vagy mindkét példány üzembe helyezése a ISEs-ben történik, amely lehetővé teszi, hogy a Logic apps közvetlenül hozzáférjen az erőforrásokhoz egy Azure-beli virtuális hálózaton. Az ajánlott eljárások és a BCDR párosított régióival kapcsolatos további információkért lásd [: Üzletmenet-folytonosság és vész-helyreállítási (BCDR): az Azure párosított régiói](../best-practices-availability-paired-regions.md).

  Például mind az elsődleges, mind a másodlagos helyen ISEs kell lennie, amikor az elsődleges logikai alkalmazás egy ISE-ben fut, és [ISE-verzióval ellátott összekötőket](../connectors/apis-list.md#ise-connectors), http-műveleteket használ az Azure virtuális hálózatban lévő erőforrások meghívásához, vagy mindkettőt. Ebben az esetben a másodlagos logikai alkalmazásnak is hasonló beállítással kell rendelkeznie a másodlagos helyen, mint az elsődleges logikai alkalmazás.

  > [!NOTE]
  > A fejlettebb forgatókönyvek esetében a több-bérlős Azure-t és az ISE-t is összekeverheti. Azonban ügyeljen arra, hogy a [logikai alkalmazások hogyan futnak az ISE és a több-bérlős Azure között](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Ha a ISEs-t használja, ügyeljen arra, [hogy a rendszer kibővítse vagy elegendő kapacitással rendelkezzen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) a terhelés kezeléséhez.

#### <a name="example-multi-tenant-azure"></a>Példa: több-bérlős Azure

Ez a példa azokat az elsődleges és másodlagos logikai alkalmazás-példányokat mutatja be, amelyek a globális, több-bérlős Azure-ban külön régiókban vannak üzembe helyezve ehhez a forgatókönyvhöz. Egyetlen [Resource Manager-sablon](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) határozza meg a logikai alkalmazás példányait és a logikai alkalmazások által igényelt függő erőforrásokat. Külön paraméter-fájlok: az egyes telepítési helyekhez használandó konfigurációs értékeket határozza meg:

![Az elsődleges és a másodlagos logikai alkalmazás példányai külön helyen](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Példa: integrációs szolgáltatási környezet

Ez a példa az előző elsődleges és másodlagos logikai alkalmazás példányait mutatja be, de külön ISEs van telepítve. Egyetlen Resource Manager-sablon határozza meg a logikai alkalmazás példányait, a logikai alkalmazások által igényelt függő erőforrásokat, valamint a ISEs a telepítési helyként. A különböző paraméterek fájljai a központi telepítéshez használandó konfigurációs értékeket határozzák meg az egyes helyeken:

![Elsődleges és másodlagos logikai alkalmazások különböző helyszíneken](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Erőforrások kapcsolatai

A Azure Logic Apps [beépített eseményindítókat és műveleteket biztosít, valamint több száz felügyelt összekötőt](../connectors/apis-list.md) , amelyeket a logikai alkalmazás használhat más alkalmazásokkal, szolgáltatásokkal, rendszerekkel és egyéb erőforrásokkal, például Azure Storage-fiókokkal, SQL Server adatbázisokkal, munkahelyi vagy iskolai e-mail-fiókokkal és így tovább. Ha a logikai alkalmazásnak hozzá kell férnie ezekhez az erőforrásokhoz, olyan kapcsolatokat hoz létre, amelyek hitelesítik a hozzáférést ezekhez az erőforrásokhoz. Az egyes kapcsolatok egy különálló Azure-erőforrás, amely egy adott helyen található, és más hely erőforrásai nem használhatók.

A vész-helyreállítási stratégia esetében vegye figyelembe azokat a helyeket, ahol a függő erőforrások a logikai alkalmazás példányaihoz képest léteznek:

* Az elsődleges példány és a függő erőforrások különböző helyszíneken találhatók. Ebben az esetben a másodlagos példány ugyanahhoz a függő erőforrásokhoz vagy végpontokhoz is csatlakozhat. A kapcsolatokat azonban kifejezetten a másodlagos példányhoz kell létrehoznia. Így ha az elsődleges hely elérhetetlenné válik, a másodlagos kapcsolatok nem érintettek.

  Tegyük fel például, hogy az elsődleges logikai alkalmazás egy külső szolgáltatáshoz (például Salesforce) csatlakozik. A külső szolgáltatás rendelkezésre állása és helye általában független a logikai alkalmazás rendelkezésre állásával. Ebben az esetben a másodlagos példány csatlakozni tud ugyanahhoz a szolgáltatáshoz, de saját kapcsolattal kell rendelkeznie.

* Az elsődleges és a függő erőforrások is ugyanabban a helyen vannak. Ebben az esetben a függő erőforrásoknak egy másik helyen lévő biztonsági másolatokkal vagy replikált verziókkal kell rendelkezniük, hogy a másodlagos példány továbbra is hozzáférhessen ezekhez az erőforrásokhoz.

  Tegyük fel például, hogy az elsődleges logikai alkalmazás egy olyan szolgáltatáshoz csatlakozik, amely ugyanabban a helyen vagy régióban található, például Azure SQL Database. Ha ez a teljes régió elérhetetlenné válik, az adott régióban lévő Azure SQL Database szolgáltatás valószínűleg nem érhető el. Ebben az esetben azt szeretné, hogy a másodlagos példánya replikált vagy biztonsági mentési adatbázist használjon, és ezzel külön kapcsolatban legyen az adatbázissal.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Helyszíni adatátjárók

Ha a logikai alkalmazás a több-bérlős Azure-ban fut, és hozzá kell férnie a helyszíni erőforrásokhoz, például SQL Server adatbázisokhoz, telepítenie kell a [helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) egy helyi számítógépre. Ezután létrehozhat egy adatátjáró-erőforrást a Azure Portalban, így a logikai alkalmazás használhatja az átjárót, amikor létrehozza az erőforráshoz való kapcsolódást.

Az adatátjáró-erőforrás egy helyhez vagy egy Azure-régióhoz van társítva, ugyanúgy, mint a Logic app-erőforráshoz. A vész-helyreállítási stratégiában győződjön meg arról, hogy az adatátjáró elérhető marad a logikai alkalmazás számára. Az [átjáró magas rendelkezésre állása](../logic-apps/logic-apps-gateway-install.md#high-availability) több átjáró telepítése esetén is engedélyezhető.

> [!NOTE]
> Ha a logikai alkalmazás egy integrációs szolgáltatási környezetben (ISE) fut, és csak az ISE-verzióval ellátott összekötőket használ a helyszíni adatforrásokhoz, nincs szüksége az adatátjáróra, mivel az ISE-összekötők közvetlen hozzáférést biztosítanak a helyszíni erőforráshoz.
>
> Ha nem érhető el ISE-verzióval rendelkező összekötő a kívánt helyszíni erőforráshoz, a logikai alkalmazás a nem ISE összekötővel is létrehozhatja a csatlakozást, amely a globális, több-bérlős Azure-ban fut, nem az ISE-t. Azonban ehhez a kapcsolathoz a helyszíni adatátjáróra van szükség.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Aktív-aktív és aktív-passzív szerepkörök

Beállíthatja az elsődleges és a másodlagos helyet, hogy a logikai alkalmazás példányai ezekben a helyekben le tudják játszani ezeket a szerepköröket:

| Elsődleges – másodlagos szerepkör | Leírás |
|------------------------|-------------|
| *Aktív – aktív* | Az elsődleges és a másodlagos logikai alkalmazás példányai mindkét helyen aktívan kezelik a kérelmeket a következő minták bármelyikének követésével: <p><p>- *Terheléselosztás*: mindkét példányban szükség esetén megfigyelheti a végpontot, és az egyes példányok forgalmának elosztását is. <p>- *Versengő fogyasztók*: mindkét példány versengő fogyasztóként működhet, így a példányok versengenek az üzenetsor üzeneteihez. Ha egy példány meghibásodik, a másik példány átveszi a munkaterhelést. |
| *Aktív – passzív* | Az elsődleges logikai alkalmazás példánya aktívan kezeli a teljes munkaterhelést, míg a másodlagos példány passzív (letiltva vagy inaktív). A másodlagos várakozás arra, hogy az elsődleges nem érhető el, vagy nem működik a megszakítás vagy a meghibásodás miatt, és az aktív példány átveszi a munkaterhelést. |
| Kombinációja | Egyes logikai alkalmazások aktív-aktív szerepkört játszanak, míg más logikai alkalmazások aktív-passzív szerepet játszanak. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Aktív – aktív példák

Ezek a példák azt az aktív-aktív beállítást mutatják be, ahol a logikai alkalmazás példányai aktívan kezelik a kérelmeket vagy az üzeneteket. Egy másik rendszer vagy szolgáltatás a példányok között osztja el a kérelmeket vagy üzeneteket, például az alábbi lehetőségek egyikét:

* Egy "fizikai" terheléselosztó, például egy olyan hardver, amely átirányítja a forgalmat

* Egy "Soft" terheléselosztó, például [Azure Load Balancer](../load-balancer/load-balancer-overview.md) vagy az [Azure API Management](../api-management/api-management-key-concepts.md). A API Management segítségével megadhatja azokat a házirendeket, amelyek meghatározzák a bejövő forgalom elosztásának módját. Vagy használhat olyan szolgáltatást is, amely támogatja az állapot-követést, például [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Bár ez a példa elsősorban a Azure Load Balancer mutatja be, a forgatókönyv igényeihez legjobban illő lehetőséget használhatja:

  !["Aktív-aktív" beállítás, amely egy terheléselosztó vagy állapot-nyilvántartó szolgáltatást használ](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Minden logikai alkalmazás-példány fogyasztóként működik, és mindkét példány verseng az üzenetsor üzeneteiben:

  !["Versengő fogyasztókat" használó "aktív-aktív" beállítás](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktív – passzív példák

Ez a példa azt az aktív-passzív beállítást mutatja be, amelyben az elsődleges logikai alkalmazás példánya aktív egy helyen, míg a másodlagos példány inaktív marad egy másik helyen. Ha az elsődleges megszakadást vagy meghibásodást tapasztal, akkor futtathat egy olyan parancsfájlt, amely aktiválja a másodlagos feladatot a számítási feladat elvégzéséhez.

!["Aktív-passzív" beállítás, amely "versengő fogyasztókat" használ](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Aktív-aktív és aktív-passzív kombináció

Ez a példa egy kombinált telepítést mutat be, ahol az elsődleges hely az aktív Logic app-példányokat is tartalmazza, míg a másodlagos hely aktív-passzív logikai alkalmazás-példányokkal rendelkezik. Ha az elsődleges hely megszakadást vagy hibát tapasztal, a másodlagos helyen lévő aktív logikai alkalmazás, amely már a részleges számítási feladatokat kezeli, átveheti a teljes munkaterhelést.

* Az elsődleges helyen az aktív logikai alkalmazás egy Azure Service Bus üzenetsor számára figyeli az üzeneteket, míg egy másik aktív logikai alkalmazás az Office 365 Outlook lekérdezési trigger használatával ellenőrzi az e-maileket.

* A másodlagos helyen az aktív logikai alkalmazás a logikai alkalmazással működik az elsődleges helyen azáltal, hogy figyeli és verseng az azonos Service Bus üzenetsor üzeneteit. Eközben a passzív inaktív logikai alkalmazás készenléti állapotra várakozik az e-mailek kereséséhez, ha az elsődleges hely elérhetetlenné válik, de *le van tiltva* az e-mailek újraolvasásának elkerülése érdekében.

![Ismétlődő eseményindítókat használó "aktív-passzív" és "aktív-passzív" kombináció](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Logic app-állapot és-előzmények

A logikai alkalmazás indításakor és futtatásakor az alkalmazás állapota ugyanazon a helyen tárolódik, ahol az alkalmazás elindult, és nem vihető át egy másik helyre. Ha hiba vagy megszakítás történik, az összes folyamatban lévő munkafolyamat-példány elhagyható. Ha az elsődleges és a másodlagos hely van beállítva, az új munkafolyamat-példányok a másodlagos helyen futnak.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Az elhagyott folyamatban lévő példányok csökkentése

Az elhagyott folyamatban lévő munkafolyamat-példányok számának minimalizálásához különböző, a megvalósításra alkalmas üzenet-mintákat választhat, például:

* [Rögzített útválasztási SLIP minta](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Ez a vállalati üzenet minta, amely az üzleti folyamatokat kisebb fázisokra osztja fel. Minden egyes szakaszhoz beállíthat egy logikai alkalmazást, amely kezeli az adott fázis munkaterhelését. Az egymással való kommunikációhoz a logikai alkalmazások aszinkron üzenetküldési protokollt használnak, például Azure Service Bus várólistákat vagy témaköröket. Ha kisebb szakaszokra osztja a folyamatot, csökkentheti az üzleti folyamatok számát, amelyek elakadnak egy sikertelen logikai alkalmazás-példányon. A mintával kapcsolatos további általános információkért lásd: [vállalati integrációs minták – útválasztási SLIP](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Ez a példa egy útválasztási SLIP-mintát mutat be, amelyben minden logikai alkalmazás egy szakaszt képvisel, és egy Service Bus üzenetsor használatával kommunikál a folyamat következő logikai alkalmazásával.

  ![Üzleti folyamat felosztása a Logic apps által képviselt szakaszokra, amelyek Azure Service Bus várólisták használatával kommunikálnak egymással](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Ha mind az elsődleges, mind a másodlagos logikai alkalmazás példánya ugyanazt a kísérőlevél-mintát követi a helyükön, akkor a [versengő fogyasztók mintát](/azure/architecture/patterns/competing-consumers) úgy is végrehajthatja, hogy [aktív-aktív szerepköröket](#roles) állít be ezekhez a példányokhoz.

* [A Process Manager (Broker) mintája](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Betekintési zárolás időkorlát-minta nélkül](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Hozzáférés az triggerhez és a futtatási előzményekhez

Ha további információkra van szüksége a logikai alkalmazás korábbi munkafolyamat-végrehajtásáról, tekintse át az alkalmazás triggerét és futtatási előzményeit. A logikai alkalmazás előzményeinek előzményei ugyanabban a helyen vagy régióban tárolódnak, ahol a logikai alkalmazás futott, ami azt jelenti, hogy nem tudja áttelepíteni ezeket az előzményeket egy másik helyre. Ha az elsődleges példány feladatátvételt hajt végre egy másodlagos példányra, csak az egyes példányok triggereit érheti el, és a megfelelő helyen futtathatja az előzményeket. A logikai alkalmazások előzményeivel kapcsolatban azonban a Logic apps szolgáltatással kapcsolatos információkat is megtudhatja, hogy a diagnosztikai eseményeket egy Azure Log Analytics-munkaterületre küldje. Ezután áttekintheti az állapotot és az előzményeket a több helyen futó logikai alkalmazások között.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Trigger típusú útmutató

A logikai alkalmazásokban használt trigger típusa határozza meg, hogy a vész-helyreállítási stratégia hogyan állíthatja be a logikai alkalmazásokat a különböző helyszíneken. Itt láthatók a Logic Appsben használható elérhető trigger-típusok:

* [Ismétlődési eseményindító](#recurrence-trigger)
* [Lekérdezési trigger](#polling-trigger)
* [Kérelem triggere](#request-trigger)
* [Webhook-trigger](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Ismétlődési eseményindító

Az **ismétlődési** eseményindító független bármely adott szolgáltatástól vagy végponttól, és kizárólag a megadott ütemezésen alapul, más feltételek nélkül, például:

* Rögzített gyakoriság és időköz, például 10 percenként
* Egy fejlettebb ütemterv, például az utolsó hétfő minden hónapban, 5:00 ÓRAKOR

Ha a logikai alkalmazás ismétlődési eseményindítóval kezdődik, be kell állítania az elsődleges és másodlagos logikai alkalmazás példányait az [aktív-passzív szerepkörökkel](#roles). Annak érdekében, hogy csökkentse a *helyreállítási idő célkitűzését* (RTO), amely az üzleti folyamatok megszakítás vagy katasztrófa utáni visszaállításának céljára vonatkozik, [aktív-passzív szerepkörök](#roles) és [passzív-aktív szerepkörök](#roles)kombinációjával állíthatja be a logikai alkalmazás példányait. Ebben a telepítőben az ütemtervet felosztja a különböző helyekre.

Tegyük fel például, hogy rendelkezik egy logikai alkalmazással, amelynek 10 percenként kell futnia. Beállíthatja a logikai alkalmazásokat és a helyeket úgy, hogy ha az elsődleges hely elérhetetlenné válik, a másodlagos hely átveheti a munkát:

![Ismétlődő eseményindítókat használó "aktív-passzív" és "passzív-aktív" kombináció](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Az elsődleges helyen állítsa be a logikai alkalmazások [aktív-passzív szerepköreit](#roles) :

  * Az *aktív* logikai alkalmazás esetében állítsa be az ismétlődési eseményindítót úgy, hogy az óra tetején kezdődjön, és 20 percenként ismételje meg a műveletet, például 9:00, 9:20 és így tovább.

  * A *passzívan* letiltott logikai alkalmazás esetében állítsa az ismétlődési eseményindítót ugyanarra az ütemtervre, de kezdjen hozzá 10 perccel az óra után, és ismételje meg a 20 percenként, például 9:10, 9:30 és így tovább.

* A másodlagos helyen állítsa be a [passzív-aktívt](#roles) a következő logikai alkalmazások esetében:

  * A *passzívan* letiltott logikai alkalmazás esetében állítsa az ismétlődési eseményindítót ugyanarra az ütemtervre, mint az elsődleges helyen lévő aktív logikai alkalmazás, amely az óra elején található, és ismételje meg 20 percenként, például 9:00, 9:10 és így tovább.

  * Az *aktív* logikai alkalmazás esetében állítsa be az ismétlődési eseményindítót az elsődleges helyen található passzív logikai alkalmazással megegyező időpontra, amely 10 perccel az óra végén kezdődik, majd 20 percenként ismétlődik, például 9:10, 9:20 és így tovább.

Most, ha egy zavaró esemény történik az elsődleges helyen, aktiválja a passzív logikai alkalmazást a másik helyen. Így ha a hiba megkeresése időt vesz igénybe, akkor ez a beállítás korlátozza az elmulasztott ismétlődések számát a késés során.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Lekérdezési trigger

Annak rendszeres vizsgálatához, hogy egy adott szolgáltatástól vagy végponttól származó új adatok feldolgozása elérhető-e, a logikai alkalmazás olyan *lekérdezési* eseményindítót használhat, amely ismételten meghívja a szolgáltatást vagy a végpontot egy rögzített ismétlődési ütemterv alapján. A szolgáltatás vagy végpont által biztosított adattípusok a következők lehetnek:

* Statikus információk, amelyek az olvasásra mindig rendelkezésre álló információkat ismertetik
* Illékony információk, amelyek az olvasás után már nem elérhetővé vált információkat ismertetik

Az azonos adat ismételt olvasásának elkerülése érdekében a logikai alkalmazásnak meg kell jegyeznie, hogy mely adatait használták korábban az ügyfél vagy a kiszolgáló, a szolgáltatás vagy a rendszer oldalának karbantartásával.

* Az ügyféloldali állapottal dolgozó logikai alkalmazások olyan eseményindítókat használnak, amelyek kezelhetik az állapotot.

  Például egy olyan trigger, amely egy új üzenetet olvas be egy e-mailből, megkövetelheti, hogy az trigger jegyezze fel a legutóbb olvasott üzenetet. Így az trigger csak akkor indítja el a logikai alkalmazást, ha a következő olvasatlan üzenet érkezik.

* A kiszolgálóval, szolgáltatással vagy rendszerszintű állapottal dolgozó logikai alkalmazások a kiszolgálón, a szolgáltatáson vagy a rendszeroldalon lévő tulajdonságértékeket vagy beállításokat használják.

  Például egy olyan lekérdezés-alapú trigger, amely az adatbázisból egy sort olvas, megköveteli, hogy a sorban `isRead` legyen egy oszlop, amely a következőre van beállítva: `FALSE` . Minden alkalommal, amikor az trigger beolvas egy sort, a logikai alkalmazás frissíti a sort úgy, hogy az oszlopot a verzióról a `isRead` `FALSE` értékre módosítja `TRUE` .

  Ez a kiszolgálóoldali megközelítés hasonlóan működik Service Bus várólistákhoz vagy olyan témakörökhöz, amelyekben üzenetsor-kezelő szemantika található, ahol egy trigger képes olvasni és zárolni egy üzenetet, miközben a logikai alkalmazás feldolgozza az üzenetet. Amikor a logikai alkalmazás befejezi a feldolgozást, az trigger törli az üzenetet a sorból vagy a témakörből.

A vész-helyreállítási perspektívából a logikai alkalmazás elsődleges és másodlagos példányainak beállításakor ügyeljen arra, hogy a logikai alkalmazás az ügyfél oldalán vagy a kiszolgálóoldali oldalon nyomon követi az adott viselkedést.

* Az ügyféloldali állapottal működő logikai alkalmazások esetében ügyeljen arra, hogy a logikai alkalmazás egyszerre ne olvassa ugyanazt az üzenetet. Egy adott időpontban csak egy hely lehet aktív logikai alkalmazás-példánnyal. Győződjön meg arról, hogy a logikai alkalmazás példánya inaktív vagy le van tiltva, amíg az elsődleges példány átadja a feladatátvételt a másik helyre.

  Az Office 365 Outlook trigger például az ügyféloldali állapotot tartja karban, és nyomon követi a legutóbb olvasott e-mailek időbélyegét, hogy elkerülje a duplikált üzenetek olvasását.

* A kiszolgálóoldali állapottal működő logikai alkalmazások esetében beállíthatja, hogy a logikai alkalmazás példányai olyan [aktív-aktív szerepköröket](#roles) játsszanak, ahol versengő fogyasztóként vagy [aktív-passzív szerepkörökként](#roles) működnek, ahol a másodlagos példány megvárja, amíg az elsődleges példány átadja a feladatokat a másik helyre.

  Például egy üzenetsor beolvasása (például egy Azure Service Bus üzenetsor) kiszolgálóoldali állapotot használ, mivel a várólista-kezelő szolgáltatás zárolja az üzeneteket, így megakadályozva, hogy más ügyfelek is olvassák ugyanezeket az üzeneteket.

  > [!NOTE]
  > Ha a logikai alkalmazásnak egy adott sorrendben kell elolvasnia az üzeneteket, például egy Service Bus sorból, használhatja a versengő fogyasztói mintát, de csak akkor, ha Service Bus-munkamenetekkel együtt, azaz [ *szekvenciális konvojként*](/azure/architecture/patterns/sequential-convoy)is ismert. Ellenkező esetben be kell állítania a logikai alkalmazás példányait az aktív-passzív szerepkörökkel.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Kérelem triggere

A **kérelem** triggere lehetővé teszi a logikai alkalmazás más alkalmazásokból, szolgáltatásokból és rendszerekből való meghívását, és általában a következő funkciók biztosítására szolgál:

* A más által hívható logikai alkalmazás közvetlen REST API

  Használja például a triggert a logikai alkalmazás elindításához, hogy más logikai alkalmazások is meghívják az triggert a **munkafolyamat-Logic apps hívási** művelettel.

* [Webhook](#webhook-trigger) vagy visszahívási mechanizmus a logikai alkalmazáshoz

* A felhasználói műveletek vagy rutinok manuális futtatásával meghívhatja a logikai alkalmazást, például egy adott feladatot végrehajtó PowerShell-parancsfájl használatával.

Vész-helyreállítási perspektívából a kérelem trigger passzív fogadó, mivel a logikai alkalmazás nem végez munkát, és addig vár, amíg egy másik szolgáltatás vagy rendszer kifejezetten nem hívja meg az triggert. Passzív végpontként beállíthatja az elsődleges és másodlagos példányokat a következő módokon:

* [Aktív-aktív](#roles): mindkét példány aktívan kezeli a kérelmeket és a hívásokat. A hívó vagy útválasztó kiegyenlíti vagy elosztja a forgalmat az adott példányok között.

* [Aktív – passzív](#roles): csak az elsődleges példány aktív, és kezeli az összes munkát, míg a másodlagos példány megvárja az elsődleges élmény megszakadását vagy meghibásodását. A hívó vagy útválasztó határozza meg, hogy mikor kell meghívnia a másodlagos példányt.

Ajánlott architektúraként használhatja az Azure API Management proxyként a kérelmek eseményindítóit használó logikai alkalmazásokhoz. A API Management [beépített régiók közötti rugalmasságot biztosít, és lehetővé teszi, hogy több végponton keresztül irányítsa a forgalmat](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-trigger

A *webhook* -trigger lehetővé teszi, hogy a logikai alkalmazás előfizessen a szolgáltatásra a *visszahívási URL-cím* átadásával. A logikai alkalmazás ezután figyelheti és megvárhatja, hogy egy adott esemény megtörténjen az adott szolgáltatási végponton. Ha az esemény történik, a szolgáltatás meghívja a webhook eseményindítóját a visszahívási URL-cím használatával, amely ezután futtatja a logikai alkalmazást. Ha engedélyezve van, a webhook-trigger előfizet a szolgáltatásra. Ha le van tiltva, az trigger lemond a szolgáltatástól.

Vész-helyreállítási perspektívából állítson be olyan elsődleges és másodlagos példányokat, amelyek webhook-eseményindítókat használnak aktív-passzív szerepkörök lejátszásához, mert csak egy példánynak kell eseményeket vagy üzeneteket kapnia az előfizetett végponttól.

## <a name="assess-primary-instance-health"></a>Az elsődleges példány állapotának felmérése

A vész-helyreállítási stratégia működéséhez a megoldásnak a következő feladatokat kell elvégeznie:

* [Az elsődleges példány rendelkezésre állásának keresése](#check-primary-availability)
* [Az elsődleges példány állapotának figyelése](#monitor-primary-health)
* [A másodlagos példány aktiválása](#activate-secondary)

Ez a szakasz egy olyan megoldást ismertet, amelyet a saját kialakításának megfelelően vagy a saját tervezés alapjaként használhat. Ez a megoldás a következő magas szintű vizuális áttekintést nyújtja:

![Állapot-ellenőrzési logikai alkalmazás figyelése az elsődleges helyen a házőrző logikai alkalmazás létrehozása](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Elsődleges példány rendelkezésre állásának keresése

Annak megállapításához, hogy az elsődleges példány elérhető-e, fut-e, és képes-e működni, létrehozhat egy "állapot-ellenőrzési" logikai alkalmazást, amely az elsődleges példánnyal azonos helyen található. Ezt az állapot-ellenőrzési alkalmazást egy másik helyről is meghívhatja. Ha az állapot-ellenőrzési alkalmazás sikeresen válaszol, az adott régióban található Azure Logic Apps szolgáltatás mögöttes infrastruktúrája elérhető és működik. Ha az állapot-ellenőrzési alkalmazás nem válaszol, feltételezheti, hogy a hely már nem kifogástalan állapotban van.

Ehhez a feladathoz hozzon létre egy alapszintű állapot-ellenőrzési logikai alkalmazást, amely a következő feladatokat hajtja végre:

1. Hívást kap a watchdog alkalmazástól a kérelem-trigger használatával.

1. Válaszoljon olyan állapottal, amely azt jelzi, hogy a kijelölt logikai alkalmazás továbbra is működik-e a válasz művelet használatával.

   > [!IMPORTANT]
   > Az állapot-ellenőrzési logikai alkalmazásnak egy válasz műveletet kell használnia, hogy az alkalmazás szinkronban, aszinkron módon válaszoljon.

1. Ha szeretné megállapítani, hogy az elsődleges hely kifogástalan állapotú-e, az ezen a helyen található, a cél logikai alkalmazással kommunikáló egyéb szolgáltatások állapotát is befolyásolhatja. Egyszerűen bontsa ki az állapot-ellenőrzési logikai alkalmazást, hogy a többi szolgáltatás állapotát is felmérje.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Watchdog logikai alkalmazás létrehozása

Az elsődleges példány állapotának figyeléséhez és az állapot-ellenőrzés logikai alkalmazás meghívásához hozzon létre egy "watchdog" logikai alkalmazást egy *másik helyen*. Beállíthatja például a watchdog Logic app alkalmazást, hogy ha az állapot-ellenőrzés logikájának meghívása sikertelen, a watchdog riasztást küldhet az operatív csapatnak, hogy megvizsgálják a hibát, és hogy az elsődleges példány miért nem válaszol.

> [!IMPORTANT]
> Győződjön meg arról, hogy a házőrző logikai alkalmazás olyan *helyen található, amely eltér az elsődleges helytől*. Ha az elsődleges helyen lévő Logic Apps szolgáltatás problémákat tapasztal, előfordulhat, hogy a házőrző logikai alkalmazás nem fut.

Ehhez a feladathoz a másodlagos helyen hozzon létre egy watchdog logikai alkalmazást, amely a következő feladatokat hajtja végre:

1. Futtatás rögzített vagy ütemezett ismétlődés alapján az ismétlődési eseményindító használatával.

   Megadhatja az ismétlődést olyan értékre, amely a helyreállítási időre vonatkozó célkitűzés (RTO) tűréshatára alatt marad.

1. Hívja meg az állapot-ellenőrzés logikai alkalmazást az elsődleges helyen a HTTP-művelet használatával, például:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Másodlagos példány aktiválása

A másodlagos példány automatikus aktiválásához létrehozhat egy logikai alkalmazást, amely meghívja a felügyeleti API-t, például az [Azure Resource Manager-összekötőt](/connectors/arm/) a megfelelő logikai alkalmazások aktiválásához a másodlagos helyen. A watchdog alkalmazás kibontásával meghívhatja ezt az aktiválási logikai alkalmazást egy adott számú hiba után.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Diagnosztikai adatok gyűjtése

Beállíthat naplózást a logikai alkalmazás futtatásához, és elküldheti az eredményül kapott diagnosztikai adatait olyan szolgáltatásokhoz, mint például az Azure Storage, az Azure Event Hubs és az Azure Log Analytics a további kezelés és feldolgozás érdekében.

* Ha ezeket az adatokat az Azure Log Analytics használatával szeretné használni, az adatokat az elsődleges és a másodlagos hely számára is elérhetővé teheti, ha beállítja a logikai alkalmazás **diagnosztikai beállításait** , és több log Analytics munkaterületre küldi az adatokat. További információ: [Azure monitor naplók beállítása és diagnosztikai adatok összegyűjtése Azure Logic Appshoz](../logic-apps/monitor-logic-apps-log-analytics.md).

* Ha az Azure Storage-ba vagy az Azure Event Hubsba kívánja elküldeni az adatküldés célját, a Geo-redundancia beállításával mind az elsődleges, mind a másodlagos helyen elérhetővé teheti az adatgyűjtést. További információért lásd a következő cikkeket:<p>

  * [Azure Blob Storage vész-helyreállítási és-fiók feladatátvétele](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo – vész-helyreállítás](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Következő lépések

* [A rugalmasság áttekintése az Azure-ban](/azure/architecture/framework/resiliency/overview)
* [Az egyes Azure-szolgáltatások rugalmasságára vonatkozó ellenőrzőlista](/azure/architecture/checklist/resiliency-per-service)
* [Adatkezelés a rugalmassághoz az Azure-ban](/azure/architecture/framework/resiliency/data-management)
* [Biztonsági mentési és vész-helyreállítási Azure-alkalmazásokhoz](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Helyreállítás egész régióra kiterjedő szolgáltatáskimaradás esetén](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft szolgáltatói szerződések (SLA-kat) az Azure-szolgáltatásokhoz](https://azure.microsoft.com/support/legal/sla/)
