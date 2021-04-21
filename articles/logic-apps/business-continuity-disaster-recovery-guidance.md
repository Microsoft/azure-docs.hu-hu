---
title: Folyamatos üzletmenet és vészhelyreállítás
description: Tervezze meg stratégiáját az adatok védelmére, a zavaró eseményekből való gyors helyreállításra, a kritikus fontosságú üzleti funkciókhoz szükséges erőforrások visszaállítására és az üzletmenet folytonosságának fenntartására Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: f974a99c59b19b5df7bf6ffcc66c2dc133743f0a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790538"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Üzletmenet-folytonosság és vészhelyreállítás Azure Logic Apps

A kiszámíthatatlan események üzleti és ügyfélre gyakorolt hatásának és hatásainak csökkentése érdekében győződjön meg arról, hogy rendelkezik vészhelyreállítási [  (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) megoldással, hogy megvédheti adatait, gyorsan visszaállíthatja a kritikus üzleti funkciókat támogató erőforrásokat, és működtetheti a műveleteket az üzletmenet-folytonosság [  (BC) fenntartása érdekében.](https://en.wikipedia.org/wiki/Business_continuity_planning) A fennakadások lehetnek például a szolgáltatáskimaradások, a mögöttes infrastruktúra vagy összetevők ,például a tárolási, hálózati vagy számítási erőforrások, a nem állítható alkalmazáshibák vagy akár az adatközpontok teljes elvesztése. Ha az üzletmenet-folytonossági és vészhelyreállítási (BCDR) megoldás készen áll, a vállalat vagy szervezet gyorsabban reagálhat a megszakításra, a tervezett vagy nem tervezett működésre, és csökkentheti az ügyfelek leállását.

Ez a cikk a BCDR-rel kapcsolatos útmutatást és stratégiákat tartalmaz, amelyek akkor alkalmazhatók, ha automatizált munkafolyamatokat épít fel a [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) A logikaialkalmazás-munkafolyamatok segítségével könnyebben integrálhatja és hangolthatja az adatokat az alkalmazások, a felhőszolgáltatások és a helyszíni rendszerek között, mivel csökkenti a kódot, amelyet meg kell írnia. Amikor a BCDR-t tervezi, ügyeljen arra, hogy ne csak a logikai alkalmazásokat vegye figyelembe, hanem a logikai alkalmazásokkal használt Azure-erőforrásokat is:

* [Logikai](../connectors/apis-list.md) alkalmazásokból létrehozott kapcsolatok más alkalmazásokhoz, szolgáltatásokhoz és rendszerekhez. További információkért lásd a jelen [témakör későbbi,](#resource-connections) Az erőforrásokhoz való csatlakozással kapcsolatos témakörét.

* [Helyszíni adatátjárók,](../logic-apps/logic-apps-gateway-connection.md) amelyek a logikai alkalmazásokban létrehozott és a helyszíni rendszerekben található adatok elérésére használt Azure-erőforrások. Minden átjáró-erőforrás egy külön adatátjáró [telepítését](../logic-apps/logic-apps-gateway-install.md) jelöli a helyi számítógépen. További információért tekintse meg [a jelen](#on-premises-data-gateways) témakör későbbi, Helyszíni adatátjárók témakörét.

* [Integrációs](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) fiókok, ahol meghatározhatja és tárolhatja a logikai alkalmazások által a vállalat közötti [(B2B)](../logic-apps/logic-apps-enterprise-integration-overview.md) vállalati integrációs forgatókönyvekhez használt összetevőket. Beállíthatja például a régiók közötti [vészhelyreállítást az integrációs fiókokhoz.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Integrációs szolgáltatási környezetek (ISE-k),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ahol olyan logikai alkalmazásokat hozhat létre, amelyek egy Azure-beli virtuális hálózaton Logic Apps futó, elkülönített Logic Apps futnak. Ezek a logikai alkalmazások ezután hozzáférhetnek a virtuális hálózat tűzfala mögött védett erőforrásokhoz.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Elsődleges és másodlagos helyek

Minden logikai alkalmazásnak meg kell adnia az üzembe helyezéshez használni kívánt helyet. Ez a hely vagy egy nyilvános régió a globális több-bérlős Azure-ban, például az "USA nyugati régiója", vagy egy integrációs szolgáltatási környezet (ISE), amelyet korábban létrehozott és üzembe helyezett egy Azure-beli virtuális hálózatban. A logikai alkalmazások ISE-környezetben való futtatása hasonló a logikai alkalmazások globális Azure-régióban való futtatásához, ami azt jelenti, hogy a vészhelyreállítási stratégia mindkét forgatókönyvre alkalmazható. Az ISE-knek azonban más szempontokat is figyelembe kell venniük, például konfigurálni kell a csak az ISE-k számára elérhető erőforrásokhoz való hozzáférést.

> [!NOTE]
> Ha a logikai alkalmazás olyan B2B-összetevőkkel is működik, mint a kereskedelmi partnerek, a szerződések, a sémák, a térképek és a tanúsítványok, amelyek egy integrációs fiókban vannak tárolva, az integrációs fióknak és a logikai alkalmazásoknak is ugyanazt a helyet kell megadniuk.

Ez a vészhelyreállítási stratégia az [](https://en.wikipedia.org/wiki/Failover) elsődleges logikai alkalmazás készenléti vagy biztonsági mentési logikai alkalmazásba történő feladatátvételének beállítására összpontosít egy másik helyen, ahol Azure Logic Apps is elérhető. Így ha az elsődleges adatbázis veszteségeket, fennakadásokat vagy meghibásodásokat okoz, a másodlagos képes a munka ellátására. Ehhez a stratégiához a másodlagos logikai alkalmazásnak és a függő erőforrásoknak már üzembe kell helyeznie és készen kell állnia a másik helyen.

Ha követi a megfelelő DevOps-eljárásokat, már Azure Resource Manager [sablonokkal](../azure-resource-manager/management/overview.md) definiálhatja és üzembe helyezheti a logikai alkalmazásokat és azok függő erőforrásait. Resource Manager sablonok lehetővé teszi, hogy egyetlen üzembe helyezési definíciót használjon, majd paraméterfájlokkal adja meg az egyes üzembe helyezési célokhoz használni szükséges konfigurációs értékeket. Ez a képesség azt jelenti, hogy ugyanazt a logikai alkalmazást üzembe helyezheti különböző környezetekben, például fejlesztési, tesztelési és éles környezetekben. Ugyanazt a logikai alkalmazást különböző Azure-régiókban vagy ISE-kben is üzembe helyezheti, amelyek támogatják a párosított régiókat támogató [vészhelyreállítási stratégiákat.](../best-practices-availability-paired-regions.md)

A feladatátvételi stratégia esetében a logikai alkalmazásoknak és helyeknek meg kell felelnie a következő követelményeknek:

* A másodlagos logikaialkalmazás-példány ugyanazokhoz az alkalmazásokhoz, szolgáltatásokhoz és rendszerekhez fér hozzá, mint az elsődleges logikaialkalmazás-példány.

* Mindkét logikaialkalmazás-példány azonos gazdagéptípussal rendelkezik. Tehát mindkét példány a globális több-bérlős Azure-régiókban, vagy mindkét példány ISE-n van telepítve, így a logikai alkalmazások közvetlenül hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz. A BCDR párosított régióira vonatkozó ajánlott eljárásokért és további információkért lásd: Üzletmenet-folytonosság és vészhelyreállítás [(BCDR): Az Azure párosított régiói.](../best-practices-availability-paired-regions.md)

  Például az elsődleges és a másodlagos helynek ISE-nek kell lennie, ha az elsődleges logikai alkalmazás ISE-környezetben fut, és [ISE-verzióval](../connectors/managed.md#ise-connectors)ellátott összekötőket, HTTP-műveleteket használ az Azure-beli virtuális hálózat erőforrásainak hívásához, vagy mindkettőt. Ebben a forgatókönyvben a másodlagos logikai alkalmazásnak is az elsődleges logikai alkalmazáshoz hasonlóan kell beállítania a másodlagos helyen.

  > [!NOTE]
  > Összetettebb forgatókönyvek esetén a több-bérlős Azure-t és az ISE-t is vegyesen használhatja helyként. Ügyeljen azonban arra, hogy figyelembe vegye és megértse a logikai alkalmazások [ISE-n és több-bérlős Azure-ban való futtatása közötti különbségeket.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Ha ISE-eket [használ,](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) győződjön meg arról, hogy azok horizontálisan fel vannak skálázva, vagy elegendő kapacitással rendelkeznek a terhelés kezeléshez.

#### <a name="example-multi-tenant-azure"></a>Például: Több-bérlős Azure

Ez a példa az elsődleges és másodlagos logikaialkalmazás-példányokat mutatja be, amelyek ebben a forgatókönyvben a globális több-bérlős Azure különböző régióiban vannak telepítve. Egyetlen [Resource Manager sablon definiálja](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) a logikaialkalmazás-példányokat és a logikai alkalmazásokhoz szükséges függő erőforrásokat. A különálló paraméterfájlok határozzák meg az egyes üzembe helyezési helyhez használni kívánt konfigurációs értékeket:

![Elsődleges és másodlagos logikaialkalmazás-példányok külön helyeken](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Példa: Integrációs szolgáltatási környezet

Ez a példa az előző elsődleges és másodlagos logikaialkalmazás-példányokat mutatja be, de külön ISE-kben van üzembe telepítve. Egyetlen Resource Manager sablon definiálja mind a logikaialkalmazás-példányokat, mind a logikai alkalmazásokhoz szükséges függő erőforrásokat, mind pedig az ISE-ket az üzembe helyezés helyeként. A különálló paraméterfájlok határozzák meg az egyes helyen való üzembe helyezéshez használni szükséges konfigurációs értékeket:

![Elsődleges és másodlagos logikai alkalmazások különböző helyeken](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Kapcsolatok az erőforrásokhoz

Azure Logic Apps beépített eseményindítókat és műveleteket, valamint több száz felügyelt összekötőt biztosít, amelyek segítségével a logikai alkalmazás más alkalmazásokkal, szolgáltatásokkal, rendszerekkel és más erőforrásokkal, például Azure [Storage-fiókokkal,](../connectors/apis-list.md) SQL Server-adatbázisokkal, munkahelyi vagy iskolai e-mail-fiókokkal stb. dolgozhat. Ha a logikai alkalmazásnak hozzá kell férni ezekhez az erőforrásokhoz, olyan kapcsolatokat kell létrehoznia, amelyek hitelesítik az erőforrásokhoz való hozzáférést. Minden kapcsolat egy külön Azure-erőforrás, amely egy adott helyen található, és más helyeken található erőforrások nem használhatók.

A vészhelyreállítási stratégia során vegye figyelembe azokat a helyeket, ahol a logikai alkalmazás példányaihoz viszonyítva függő erőforrások találhatók:

* Az elsődleges példány és a függő erőforrások különböző helyeken találhatók. Ebben az esetben a másodlagos példány csatlakozhat ugyanazokhoz a függő erőforrásokhoz vagy végponthoz. Azonban kifejezetten a másodlagos példányhoz kell kapcsolatokat létrehoznia. Így ha az elsődleges hely elérhetetlenné válik, a másodlagos kapcsolatára ez nem lesz hatással.

  Tegyük fel például, hogy az elsődleges logikai alkalmazás egy külső szolgáltatáshoz, például a Salesforce-hoz csatlakozik. A külső szolgáltatás rendelkezésre állása és helye általában független a logikai alkalmazás elérhetőségétől. Ebben az esetben a másodlagos példány kapcsolódhat ugyanhez a szolgáltatáshoz, de saját kapcsolattal kell rendelkezik.

* Az elsődleges példány és a függő erőforrások is ugyanazon a helyen vannak. Ebben az esetben a függő erőforrásoknak másik helyen kell biztonsági másolatokkal vagy replikált verzióval rendelkezik, hogy a másodlagos példány továbbra is hozzáférjen ezekhez az erőforrásokhoz.

  Tegyük fel például, hogy az elsődleges logikai alkalmazás egy azonos helyen vagy régióban található szolgáltatáshoz csatlakozik, például egy Azure SQL Database. Ha a teljes régió elérhetetlenné válik, a Azure SQL Database szolgáltatás valószínűleg nem lesz elérhető. Ebben az esetben azt szeretné, hogy a másodlagos példány egy replikált vagy biztonsági mentési adatbázist használjon, valamint egy külön kapcsolatot az adatbázishoz.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Helyszíni adatátjárók

Ha a logikai alkalmazás [több-bérlős](../logic-apps/logic-apps-gateway-install.md) Azure-ban fut, és hozzá kell férni a helyszíni erőforrásokhoz, például SQL Server-adatbázisokhoz, telepítenie kell a helyszíni adatátjárót egy helyi számítógépre. Ezután létrehozhat egy adatátjáró-erőforrást a Azure Portal hogy a logikai alkalmazás használni tudja az átjárót az erőforrással való kapcsolat létrehozásakor.

Az adatátjáró erőforrás egy helyhez vagy Azure-régióhoz van társítva, csakúgy, mint a logikai alkalmazás erőforrása. A vészhelyreállítási stratégiában győződjön meg arról, hogy az adatátjáró elérhető marad a logikai alkalmazás számára. Ha több [átjárót is telepített,](../logic-apps/logic-apps-gateway-install.md#high-availability) engedélyezheti a magas rendelkezésre állást az átjáró számára.

> [!NOTE]
> Ha a logikai alkalmazás integrációs szolgáltatási környezetben (ISE) fut, és csak ISE-verzióval ellátott összekötőket használ a helyszíni adatforrások számára, nincs szükség az adatátjáróra, mert az ISE-összekötők közvetlen hozzáférést biztosítanak a helyszíni erőforráshoz.
>
> Ha nem érhető el ISE-verzióval ellátott összekötő a kívánt helyszíni erőforráshoz, a logikai alkalmazás továbbra is létrehozhatja a kapcsolatot egy nem ISE-összekötővel, amely a globális több-bérlős Azure-ban fut, nem pedig az ISE-n. Ehhez a kapcsolathoz azonban a helyszíni adatátjáró szükséges.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Aktív-aktív vagy aktív-passzív szerepkörök

Beállíthatja az elsődleges és másodlagos helyeket, hogy az ezeken a helyeken található logikaialkalmazás-példányok a következő szerepköröket kapják meg:

| Elsődleges–másodlagos szerepkör | Description |
|------------------------|-------------|
| *Aktív-aktív* | Az elsődleges és másodlagos logikaialkalmazás-példányok mindkét helyen aktívan kezelik a kéréseket az alábbi minták valamelyikével: <p><p>- *Terheléselosztás:* A két példány figyelhet egy végpontot, és szükség szerint eltenheti az egyes példányok forgalmának terheléselosztását. <p>- *Versengő felhasználók:* Mindkét példány versengő fogyasztóként viselkedhet, így a példányok versenghetnek az üzenetsor üzeneteiért. Ha az egyik példány meghibásodik, a másik példány veszi át a számítási feladatot. |
| *Aktív-passzív* | Az elsődleges logikaialkalmazás-példány aktívan kezeli a teljes számítási feladatot, míg a másodlagos példány passzív (letiltva vagy inaktív). A másodlagos megvárja, hogy az elsődleges példány üzemkimaradás vagy hiba miatt nem működik, és a számítási feladatot veszi át aktív példányként. |
| Kombináció | Egyes logikai alkalmazások aktív-aktív, míg más logikai alkalmazások aktív-passzív szerepet játszanak. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Aktív-aktív példák

Ezek a példák azt az aktív-aktív beállítást mutatják be, amelyben mindkét logikaialkalmazás-példány aktívan kezeli a kéréseket vagy üzeneteket. Más rendszerek vagy szolgáltatások például az alábbi lehetőségek egyikével osztják el a kéréseket vagy üzeneteket a példányok között:

* Egy "fizikai" terheléselelosztási eszköz, például a forgalmat irányító hardver

* Egy "soft" terheléselosztási, [például Azure Load Balancer](../load-balancer/load-balancer-overview.md) [azure API Management.](../api-management/api-management-key-concepts.md) A API Management megadhatja azokat a szabályzatokat, amelyek meghatározzák a bejövő forgalom terheléselosztását. Vagy használhat olyan szolgáltatást, amely támogatja az állapotkövetést, például a [Azure Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

  Bár ez a példa elsősorban Azure Load Balancer, használhatja a forgatókönyv igényeinek leginkább megfelelő lehetőséget:

  !["Aktív-aktív" beállítás, amely terheléselosztást vagy állapot-szolgáltatási szolgáltatást használ](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Minden logikaialkalmazás-példány fogyasztóként működik, és mindkét példány verseng az üzenetsor üzeneteiért:

  !["Aktív-aktív" beállítás, amely "versengő fogyasztókat" használ](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktív-passzív példák

Ez a példa azt az aktív-passzív beállítást mutatja be, ahol az elsődleges logikaialkalmazás-példány egy adott helyen aktív, míg a másodlagos példány inaktív marad egy másik helyen. Ha az elsődleges rendszer kimaradást vagy meghibásodást tapasztal, az operátor futtathat egy szkriptet, amely aktiválja a másodlagos adatbázist a számítási feladat elvégzéséhez.

!["Aktív-passzív" beállítás, amely "versengő fogyasztókat" használ](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Aktív-aktív és aktív-passzív kombináció

Ez a példa egy kombinált beállítást mutat be, amelyben az elsődleges hely mind az aktív logikaialkalmazás-példányokkal, a másodlagos hely pedig az aktív-passzív logikaialkalmazás-példányokkal rendelkezik. Ha az elsődleges hely kimaradást vagy meghibásodást tapasztal, a másodlagos helyen lévő aktív logikai alkalmazás, amely már egy részleges számítási feladatot kezel, átveheti a teljes számítási feladatot.

* Az elsődleges helyen az aktív logikai alkalmazás egy Azure Service Bus-üzenetsort figyel az üzenetekhez, míg egy másik aktív logikai alkalmazás egy Office 365 Outlook lekérdezéses eseményindítóval keres e-maileket.

* A másodlagos helyen az aktív logikai alkalmazás az elsődleges helyen lévő logikai alkalmazással működik együtt azáltal, hogy figyel és verseng az ugyanabba a Service Bus üzenetsorból származó üzenetekért. Eközben a passzív inaktív logikai alkalmazás készenléti állapotban várakozik, és ellenőrzi  az e-maileket, amikor az elsődleges hely elérhetetlenné válik, de le van tiltva az e-mailek újraolvasásának elkerülése érdekében.

!["Aktív-passzív" és "aktív-passzív" kombináció, amely ismétlődési eseményindítókat használ](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Logikai alkalmazás állapota és előzményei

Amikor a logikai alkalmazás aktiválódik és elindul, az alkalmazás állapota ugyanazon a helyen lesz tárolva, ahol az alkalmazás elindult, és nem továbbítható másik helyre. Ha hiba vagy kimaradás történik, a folyamatban lévő munkafolyamat-példányok felhagynak. Ha be van állítva egy elsődleges és egy másodlagos hely, az új munkafolyamat-példányok a másodlagos helyen futnak.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>A felhagyott folyamatban lévő példányok számának csökkentése

A nem elérhető munkafolyamat-példányok számának minimalizálása érdekében különböző üzenetminták közül választhat, például:

* [Rögzített útválasztási minta](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Ez a vállalati üzenetminta, amely az üzleti folyamatot kisebb szakaszokra osztja fel. Minden fázishoz be kell állítania egy logikai alkalmazást, amely az erre a szakaszra vonatkozó számítási feladatokat kezeli. Az egymással való kommunikációhoz a logikai alkalmazások aszinkron üzenetkezelési protokollt, például üzenetsorokat Azure Service Bus üzenetsorokat vagy témaköröket. Ha egy folyamatot kisebb szakaszokra oszt, csökkentheti azon üzleti folyamatok számát, amelyek elakadnak egy hibás logikaialkalmazás-példányon. A mintával kapcsolatos általánosabb információkért lásd: [Vállalati integrációs minták – Útválasztási csúszkás.](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)

  Ez a példa egy útválasztási mintát mutat be, amelyben minden logikai alkalmazás egy fázist képvisel Service Bus egy üzenetsort a folyamat következő logikai alkalmazásával való kommunikációhoz.

  ![Üzleti folyamat felosztása logikai alkalmazások által képviselt szakaszokra, amelyek az üzenetsorok használatával kommunikálnak Azure Service Bus egymással](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Ha az elsődleges és a másodlagos logikaialkalmazás-példányok is ugyanazt [](/azure/architecture/patterns/competing-consumers) az útválasztási mintát követik a helyeken, akkor a versengő felhasználók mintáját úgy valósíthatja meg, hogy [aktív-aktív](#roles) szerepköröket konfigurál ezekhez a példányokhoz.

* [Folyamatkezelői (közvetítői) minta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Betekintés az időtúllépési minta nélküli zárolásba](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Az eseményindítók és futtatáselőzmények elérése

A logikai alkalmazás korábbi munkafolyamat-végrehajtási adataival kapcsolatos további információkért tekintse át az alkalmazás eseményindítóját és futtatás előzményeit. A logikai alkalmazások előzmény-végrehajtási előzményei ugyanazon a helyen vagy régióban vannak tárolva, ahol a logikai alkalmazás futott, ami azt jelenti, hogy az előzményeket nem lehet másik helyre átemelni. Ha az elsődleges példány egy másodlagos példánynak ad át feladat-vissza, akkor csak az egyes példányok eseményindítóihoz férhet hozzá, és a futtatás előzményei csak a példányok futási helyének megfelelő helyeken használhatja őket. Azonban helytől független információkat kaphat a logikai alkalmazás előzményeiről, ha a logikai alkalmazásokat úgy kell beosztani, hogy diagnosztikai eseményeket küldjenek egy Azure Log Analytics-munkaterületre. Ezután áttekintheti a több helyen futó logikai alkalmazások állapotát és előzményeit.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Útmutató az eseményindítók típusának

A logikai alkalmazásokban használt eseményindító-típus határozza meg a logikai alkalmazások különböző helyeken való beállításának lehetőségeit a vészhelyreállítási stratégiában. A logikai alkalmazásokban a következő eseményindító-típusok használhatók:

* [Ismétlődési eseményindító](#recurrence-trigger)
* [Lekérdezéses eseményindító](#polling-trigger)
* [Kérés eseményindítója](#request-trigger)
* [Webhook-eseményindító](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Ismétlődési eseményindító

Az **Ismétlődés** eseményindító független minden adott szolgáltatástól vagy végponttól, és kizárólag egy megadott ütemezés alapján aktiválódik, és nincs más feltétel, például:

* Rögzített gyakoriság és időköz, például 10 percenként
* Egy fejlettebb ütemezés, például minden hónap utolsó hétfője 17:00-kor

Amikor a logikai alkalmazás egy Ismétlődés eseményindítóval indul, be kell állítania az elsődleges és másodlagos logikaialkalmazás-példányokat az [aktív-passzív szerepkörökkel.](#roles) Ha csökkenteni  kell a helyreállítási időre vonatkozó célkitűzést (RTO), amely az üzleti folyamat megszakítás vagy katasztrófa utáni helyreállításának célidejére vonatkozik, a logikai alkalmazás példányait aktív-passzív szerepkörök és [passzív-aktív](#roles) szerepkörök kombinációjával állíthatja [be.](#roles) Ebben a beállításban felosztja az ütemezést a helyek között.

Tegyük fel például, hogy van egy logikai alkalmazása, amely 10 percenként fut. A logikai alkalmazásokat és helyeket beállíthatja úgy, hogy ha az elsődleges hely elérhetetlenné válik, a másodlagos hely átveheti a munkát:

![Ismétlődési eseményindítókat használó "aktív-passzív" és "passzív-aktív" kombináció](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Az elsődleges helyen állítsa be az [aktív-passzív szerepköröket](#roles) a következő logikai alkalmazásokhoz:

  * Az *aktív* engedélyezett logikai alkalmazásnál állítsa be az Ismétlődés eseményindítót úgy, hogy az óra tetején indul, és 20 percenként ismétlődjön, például 9:00-kor, 9:20-kor és így tovább.

  * A *passzív* letiltott logikai alkalmazásnál állítsa az Ismétlődés eseményindítót ugyanannak az ütemezésnek, de az óra végén 10 perccel kezdődjön, és ismételje meg például 20 percenként, például 9:10-kor, 9:30-kor stb.

* A másodlagos helyen állítsa be a [passzív-aktív beállítását](#roles) a következő logikai alkalmazásokhoz:

  * A *passzív* letiltott logikai alkalmazásnál állítsa az Ismétlődés eseményindítót az elsődleges helyen található aktív logikai alkalmazás ütemezésére, amely az óra tetején található, és 20 percenként ismétlődik, például 9:00-kor, 9:10-kor és így tovább.

  * Az  aktív engedélyezett logikai alkalmazásnál állítsa az Ismétlődés eseményindítót az elsődleges helyen található passzív logikai alkalmazás ütemezésére, amely az elmúlt 10 perccel kezdődik, és 20 percenként ismétlődik, például: 9:10, 9:20 stb.

Ha az elsődleges helyen zavaró esemény következik be, aktiválja a passzív logikai alkalmazást a másik helyen. Így ha a hiba megkeresése időt vesz igénybe, ez a beállítás korlátozza a kihagyott ismétlődéseket a késés során.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Lekérdezéses eseményindító

Annak rendszeres ellenőrzéséhez, hogy a feldolgozható új adatok elérhetők-e egy adott szolgáltatásból vagy végpontból, *a* logikai alkalmazás használhat olyan lekérdezéses eseményindítót, amely ismétlődően hívja meg a szolgáltatást vagy a végpontot egy rögzített ismétlődési ütemezés alapján. A szolgáltatás vagy a végpont által elérhető adatok a következő típusok valamelyikét tartalmazják:

* Statikus adatok, amelyek az olvasásra mindig elérhető adatokat írják le
* Ideiglenes adatok, amelyek az olvasás után már nem elérhető adatokat írják le

Annak érdekében, hogy elkerülje ugyanazon adatok ismételt olvasását, a logikai alkalmazásnak meg kell jegyezze, hogy mely adatok voltak korábban beolvasva az ügyféloldalon vagy a kiszolgálón, a szolgáltatáson vagy a rendszeroldalon való állapot fenntartásával.

* Az ügyféloldali állapotokkal használható logikai alkalmazások olyan eseményindítókat használnak, amelyek fenntartják az állapotot.

  Ha például egy eseményindító beolvas egy új üzenetet egy e-mail-postafiókból, az eseményindítónak meg kell jegyeznie a legutóbb olvasott üzenetet. Így az eseményindító csak akkor indítja el a logikai alkalmazást, amikor a következő olvasatlan üzenet érkezik.

* A kiszolgálóval, szolgáltatással vagy rendszeroldali állapotokkal használható logikai alkalmazások a kiszolgálón, szolgáltatáson vagy rendszeroldalon található tulajdonságértékeket vagy beállításokat használják.

  Például egy olyan lekérdezésalapú eseményindítóhoz, amely beolvas egy sort egy adatbázisból, a sornak olyan oszlopra van szüksége, amely a `isRead` következőre van beállítva: `FALSE` . Minden alkalommal, amikor az eseményindító beolvas egy sort, a logikai alkalmazás úgy frissíti ezt a sort, hogy az oszlopot `isRead` értékről `FALSE` sorra `TRUE` módosítja.

  Ez a kiszolgálóoldali megközelítés hasonlóan működik Service Bus üzenetsorokkal vagy üzenetsor-szemantikával kapcsolatos témakörökben, ahol az eseményindító olvashatja és zárolhatja az üzeneteket, miközben a logikai alkalmazás feldolgozza az üzenetet. Amikor a logikai alkalmazás befejezi a feldolgozást, az eseményindító törli az üzenetet az üzenetsorból vagy témakörből.

Vészhelyreállítási szempontból a logikai alkalmazás elsődleges és másodlagos példányainak beállításakor annak alapján kell figyelembe vennie ezeket a viselkedéseket, hogy a logikai alkalmazás az ügyféloldalon vagy a kiszolgálóoldalon követi-e az állapotot:

* Az ügyféloldali állapotokkal együttműködő logikai alkalmazásoknál győződjön meg arról, hogy a logikai alkalmazás nem olvassa be ugyanazt az üzenetet egynél több alkalommal. Egy adott időpontban csak egy helyen lehet aktív logikaialkalmazás-példány. Győződjön meg arról, hogy a másik helyen található logikaialkalmazás-példány inaktív vagy le van tiltva, amíg az elsődleges példány át nem veszi a adatokat a másik helyre.

  Az Office 365 Outlook-eseményindító például fenntartja az ügyféloldali állapotot, és nyomon követi a legutóbb elolvasott e-mail időbélyegét, hogy elkerülje a duplikált e-mailek olvasását.

* A kiszolgálóoldali állapotokkal működő logikai alkalmazások esetében beállíthatja, hogy a logikai alkalmazás példányai [aktív-aktív](#roles) szerepköröket játsszanak, amelyekben versengő felhasználókként működnek, vagy [aktív-passzív](#roles) szerepkörökként, ahol a másodlagos példány megvárja, amíg az elsődleges példány át nem veszi a feladatokat a másik helyre.

  Például egy üzenetsorból, például egy Azure Service Bus-üzenetsorból való olvasás kiszolgálóoldali állapotot használ, mivel az üzenetsor-szolgáltatás zárolja az üzeneteket, hogy más ügyfelek ne olvassák el ugyanazt az üzenetet.

  > [!NOTE]
  > Ha a logikai alkalmazásnak egy adott sorrendben kell olvasnia az üzeneteket, például egy Service Bus-üzenetsorból, használhatja a versengő fogyasztói mintát, de csak akkor, ha Service Bus munkamenetekkel kombinálja, más néven a [ *szekvenciális konvergens*](/azure/architecture/patterns/sequential-convoy)mintával. Ellenkező esetben aktív-passzív szerepkörökkel kell beállítania a logikaialkalmazás-példányokat.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Kérés eseményindítója

A **Kérés** eseményindító lehetővé teszi a logikai alkalmazás más alkalmazásokból, szolgáltatásokból és rendszerekből való hívását, és általában a következő képességeket biztosítja:

* Közvetlen REST API a logikai alkalmazáshoz, amit mások is hívhatnak

  Például a Kérelem eseményindítóval indítsa el a logikai alkalmazást, hogy más logikai alkalmazások is meg tudja hívni az eseményindítót a Munkafolyamat hívása **–** Logic Apps használatával.

* A [logikai alkalmazás webhook-](#webhook-trigger) vagy visszahívási mechanizmusa

* Felhasználói műveletek vagy rutinok manuális futtatásának módja a logikai alkalmazás hívása érdekében, például egy PowerShell-szkripttel, amely egy adott feladatot hajt végre

Vészhelyreállítási szempontból a Kérés eseményindító passzív fogadó, mert a logikai alkalmazás nem csinál semmilyen munkát, és vár, amíg egy másik szolgáltatás vagy rendszer explicit módon meg nem hívja az eseményindítót. Passzív végpontként az alábbi módokon állíthatja be az elsődleges és másodlagos példányokat:

* [Aktív-aktív:](#roles)Mindkét példány aktívan kezeli a kéréseket vagy hívásokat. A hívó vagy az útválasztó elosztja vagy elosztja a forgalmat ezek között a példányok között.

* [Aktív-passzív:](#roles)Csak az elsődleges példány aktív, és kezeli az összes munkát, míg a másodlagos példány megvárja, amíg az elsődleges példány megszakítást vagy meghibásodást tapasztal. A hívó vagy útválasztó határozza meg, hogy mikor hívja meg a másodlagos példányt.

Ajánlott architektúraként az Azure API Management használhatja proxyként a Kérés eseményindítókat használni képes logikai alkalmazásokhoz. API Management beépített régiók közötti rugalmasságot és a forgalom több végpont közötti útválasztásának [képességét biztosítja.](../api-management/api-management-howto-deploy-multi-region.md)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-eseményindító

A *webhook-eseményindító* lehetővé teszi, hogy a logikai alkalmazás feliratkozhat egy szolgáltatásra egy visszahívási *URL-cím* a szolgáltatásnak való átadásával. A logikai alkalmazás ezután figyelheti és megvárhatja, amíg egy adott esemény bekövetkezik a szolgáltatásvégponton. Amikor az esemény bekövetkezik, a szolgáltatás a visszahívási URL-cím használatával hívja meg a webhook eseményindítóját, amely ezután futtatja a logikai alkalmazást. Ha engedélyezve van, a webhook-eseményindító feliratkozik a szolgáltatásra. Letiltáskor az eseményindító leiratkozik a szolgáltatásról.

Vészhelyreállítási szempontból olyan elsődleges és másodlagos példányokat állítson be, amelyek webhook-eseményindítókat használnak az aktív-passzív szerepkörök lejátszására, mert csak egy példány fogadhat eseményeket vagy üzeneteket az előfizetett végpontról.

## <a name="assess-primary-instance-health"></a>Az elsődleges példány állapotának felmérése

Ahhoz, hogy a vészhelyreállítási stratégia működjön, a megoldásnak a következő feladatok elvégzésére van szüksége:

* [Az elsődleges példány rendelkezésre állásának ellenőrzése](#check-primary-availability)
* [Az elsődleges példány állapotának figyelése](#monitor-primary-health)
* [A másodlagos példány aktiválása](#activate-secondary)

Ez a szakasz egy olyan megoldást ismertet, amely a saját tervezésének alapjaként vagy a lehető legjobban használható. A megoldás magas szintű vizualizációs áttekintése:

![A watchdog logikai alkalmazás létrehozása, amely az elsődleges helyen figyeli az állapot-ellenőrző logikai alkalmazást](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Az elsődleges példány rendelkezésre állásának ellenőrzése

Annak megállapításához, hogy az elsődleges példány elérhető, fut és működik-e, létrehozhat egy "állapot-ellenőrzés" logikai alkalmazást, amely ugyanazon a helyen található, mint az elsődleges példány. Ezt az állapotellenőrzési alkalmazást egy másik helyről hívhatja meg. Ha az állapotellenőrzési alkalmazás sikeresen válaszol, az adott Azure Logic Apps szolgáltatás mögöttes infrastruktúrája elérhető és működik. Ha az állapotellenőrzési alkalmazás nem válaszol, feltételezheti, hogy a hely már nem megfelelő állapotú.

Ehhez a feladathoz hozzon létre egy alapszintű állapotellenőrzési logikai alkalmazást, amely a következő feladatokat végzi el:

1. Hívást fogad a Watchdog alkalmazástól a Request eseményindítóval.

1. A Válasz művelet használatával állapottal válaszolhat, amely jelzi, hogy az ellenőrzött logikai alkalmazás továbbra is működik-e.

   > [!IMPORTANT]
   > Az állapot-ellenőrző logikai alkalmazásnak Válasz műveletet kell használnia, hogy az alkalmazás szinkron, és nem aszinkron módon válaszoljon.

1. Ha szeretné még jobban meghatározni, hogy az elsődleges hely kifogástalan állapotú-e, figyelembe adhatja a cél logikai alkalmazást ezen a helyen kommunikáló egyéb szolgáltatások állapotát. Csak bontsa ki az állapotellenőrzési logikai alkalmazást a többi szolgáltatás állapotának felméréséhez is.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Watchdog logikai alkalmazás létrehozása

Az elsődleges példány állapotának figyelése és az állapotellenőrzési logikai alkalmazás hívása érdekében hozzon létre egy "watchdog" logikai alkalmazást egy *másik helyen.* Beállíthatja például úgy a Watchdog logikai alkalmazást, hogy ha az állapotellenőrzési logika hívása meghiúsul, a watchdog riasztást küldhet az üzemeltetési csapatnak, hogy kivizsgálni tudják a hibát, és hogy az elsődleges példány miért nem válaszol.

> [!IMPORTANT]
> Győződjön meg arról, hogy a watchdog logikai alkalmazás olyan helyen van, amely eltér az *elsődleges helyétől.* Ha az Logic Apps helyen található alkalmazásszolgáltatás problémát tapasztal, előfordulhat, hogy a watchdog logikai alkalmazás nem fut.

Ehhez a feladathoz a másodlagos helyen hozzon létre egy watchdog logikai alkalmazást, amely végrehajtja az alábbi feladatokat:

1. A futtatás rögzített vagy ütemezett ismétlődés alapján az Ismétlődés eseményindítóval.

   Az ismétlődést beállíthatja olyan értékre, amely a helyreállítási időre vonatkozó célkitűzés (RTO) tűréshatára alatt van.

1. Hívja meg az állapot-ellenőrző logikai alkalmazást az elsődleges helyen a HTTP-művelet használatával, például:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Másodlagos példány aktiválása

A másodlagos példány automatikus aktiválásához létrehozhat egy logikai alkalmazást, amely a felügyeleti [API-t](/connectors/arm/) hívja meg, például a Azure Resource Manager-összekötőt a megfelelő logikai alkalmazások másodlagos helyen való aktiválásához. A Watchdog alkalmazás kibontható úgy, hogy ezt az aktiválási logikai alkalmazást hívja meg bizonyos számú hiba bekövetkezte után.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Diagnosztikai adatok gyűjtése

Beállíthatja a logikai alkalmazás futásának naplózását, és elküldheti az eredményül kapott diagnosztikai adatokat olyan szolgáltatásoknak, mint az Azure Storage, a Azure Event Hubs és az Azure Log Analytics, további kezelés és feldolgozás céljából.

* Ha ezeket az adatokat az Azure Log Analytics szolgáltatással szeretné használni, az adatokat a logikai  alkalmazás diagnosztikai beállításainak beállításával és több Log Analytics-munkaterületre való küldésével elérhetővé is teszi mind az elsődleges, mind a másodlagos hely számára. További információ: A naplók Azure Monitor és diagnosztikai adatok gyűjtése a [Azure Logic Apps.](../logic-apps/monitor-logic-apps-log-analytics.md)

* Ha az adatokat az Azure Storage-ba vagy a Azure Event Hubs-be szeretné küldeni, az adatokat elérhetővé is teszi az elsődleges és a másodlagos hely számára is a georedundania beállításával. További információért lásd a következő cikkeket:<p>

  * [Azure Blob Storage vészhelyreállítás és fiók feladatátvétele](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo-vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Következő lépések

* [Az Azure rugalmasságának áttekintése](/azure/architecture/framework/resiliency/overview)
* [Az egyes Azure-szolgáltatások rugalmasságára vonatkozó ellenőrzőlista](/azure/architecture/checklist/resiliency-per-service)
* [Adatkezelés a rugalmasság érdekében az Azure-ban](/azure/architecture/framework/resiliency/data-management)
* [Azure-alkalmazások biztonsági mentése és vészhelyreállítása](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Helyreállítás egész régióra kiterjedő szolgáltatáskimaradás esetén](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft szolgáltatói szerződések (SBA-k) az Azure-szolgáltatásokhoz](https://azure.microsoft.com/support/legal/sla/)
