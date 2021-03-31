---
title: Korrelált üzenetek küldése sorrendben, szekvenciális konvoj használatával
description: Kapcsolódó üzenetek küldése sorrendben a Azure Logic Appsban található szekvenciális konvoj minta használatával Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048656"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Kapcsolódó üzenetek küldése sorrendben a Azure Logic Appsban szekvenciális konvoj használatával Azure Service Bus

Ha a korrelációs üzeneteket egy adott sorrendben kell elküldenie, akkor a [Azure Logic apps](../logic-apps/logic-apps-overview.md) az [Azure Service Bus-összekötő](../connectors/connectors-create-api-servicebus.md)használatával követheti a [ *szekvenciális konvoj* mintáját](/azure/architecture/patterns/sequential-convoy) . A korrelált üzenetek olyan tulajdonsággal rendelkeznek, amely meghatározza az üzenetek közötti kapcsolatot, például a [munkamenet](../service-bus-messaging/message-sessions.md) azonosítóját Service Busban.

Tegyük fel például, hogy egy "1. munkamenet" nevű munkamenethez 10 üzenet tartozik, és egy "2. munkamenet" nevű munkamenethez 5 üzenet tartozik, amelyek mindegyike ugyanahhoz a [Service Bus-várólistához](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)lett küldve. Létrehozhat egy logikai alkalmazást, amely az üzeneteket a várólistából dolgozza fel, így az "1. munkamenet" összes üzenetét egyetlen trigger futtatja, és a "2. munkamenet" összes üzenetét a rendszer a következő trigger futtatásával kezeli.

![Általános szekvenciális konvoj minta](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

Ez a cikk bemutatja, hogyan hozhat létre egy olyan logikai alkalmazást, amely megvalósítja ezt a mintát a **Service Bus-munkamenetek sablonnal végzett korrelációs kézbesítés** használatával. Ez a sablon olyan logikai alkalmazás-munkafolyamatot határoz meg, amely a Service Bus-összekötővel kezdődik, **Ha üzenet érkezik egy várólistába (betekintési)** indítóba, amely egy [Service Bus-várólistából](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)fogad üzeneteket. A logikai alkalmazás a következő magas szintű lépéseket hajtja végre:

* A munkamenet inicializálása egy üzenet alapján, amelyet az trigger az Service Bus-sorból olvas.

* A várólistán lévő összes üzenet olvasása és feldolgozása az aktuális munkafolyamat-futtatás során.

A sablon JSON-fájljának áttekintéséhez tekintse [meg a GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)című témakört.

További információ: [szekvenciális konvojos minta – Azure Architecture Cloud Design Patterns](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Service Bus névtér és egy [Service Bus üzenetsor](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), amely egy üzenetkezelő entitás, amelyet a logikai alkalmazásban fog használni. Ezeknek az elemeknek és a logikai alkalmazásnak ugyanazt az Azure-előfizetést kell használnia. Győződjön meg arról, hogy a várólista létrehozásakor a **munkamenetek engedélyezése** lehetőséget választotta. Ha nem rendelkezik ezekkel az elemekkel, megtudhatja, [hogyan hozhatja létre a Service Bus névteret és a várólistát](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Alapvető ismeretek a logikai alkalmazások létrehozásáról. Ha most ismerkedik a Azure Logic Appsval, próbálja ki a gyors üzembe helyezést, és [hozza létre első automatizált munkafolyamatát](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Service Bus névtér elérésének engedélyezése

Ha nem biztos abban, hogy a logikai alkalmazás rendelkezik-e jogosultsággal a Service Bus névtér eléréséhez, erősítse meg ezeket az engedélyeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki a Service Bus *névteret*.

1. A névtér menü **Beállítások** területén válassza a **megosztott elérési házirendek** elemet. A **jogcímek** területen győződjön meg arról, hogy rendelkezik az adott névtérhez **tartozó jogosultságokkal** .

   ![Service Bus névtér engedélyeinek kezelése](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Most szerezze be a Service Bus névtérhez tartozó kapcsolatok karakterláncát. Ezt a karakterláncot később is használhatja, amikor létrehozza a logikai alkalmazás névterével létesített kapcsolatokat.

   1. A **megosztott hozzáférési házirendek** ablaktábla **házirend** területén válassza a **RootManageSharedAccessKey** lehetőséget.
   
   1. Az elsődleges kapcsolódási karakterlánc mellett kattintson a Másolás gombra. Mentse a kapcsolatok karakterláncát későbbi használatra.

      ![Service Bus névtérbeli kapcsolatok karakterláncának másolása](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Annak ellenőrzéséhez, hogy a kapcsolódási karakterlánc társítva van-e a Service Bus névteréhez vagy egy üzenetküldési entitáshoz, például egy várólistához, keresse meg a paraméterhez tartozó kapcsolódási karakterláncot `EntityPath` . Ha megtalálta ezt a paramétert, a kapcsolódási karakterlánc egy adott entitásra vonatkozik, és nem a megfelelő karakterláncot használja a logikai alkalmazáshoz.

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a szakaszban egy logikai alkalmazást hoz létre a **korrelált sorrend szerinti kézbesítés a Service Bus-munkamenetek** sablonnal, amely magában foglalja a munkafolyamat-minta megvalósításának triggerét és műveleteit. Hozzon létre egy kapcsolódást a Service Bus névtérhez is, és adja meg a használni kívánt Service Bus-várólista nevét.

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást. Az Azure kezdőlapján válassza az **erőforrás létrehozása**  >  **integrációs**  >  **logikai alkalmazás** lehetőséget.

1. A sablon-gyűjtemény megjelenése után görgessen a videó és a Common triggers (gyakori eseményindítók) szakaszban. A **sablonok** szakaszban válassza ki a sablont, **korrelált sorrend szerinti kézbesítést a Service Bus-munkamenetek használatával**.

   ![Válassza a "korrelációs sorrendben történő kézbesítés a Service Bus-munkamenetek használatával" sablont](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Amikor megjelenik a megerősítő mező, válassza a **sablon használata** lehetőséget.

1. A Logic app Designer **Service Bus** alakzatában válassza a **Folytatás** lehetőséget, majd válassza ki az **+** alakzatban megjelenő pluszjelet ().

   ![Válassza a "Folytatás" lehetőséget a Azure Service Bushoz való kapcsolódáshoz](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Most hozzon létre egy Service Bus-kapcsolatokat az egyik lehetőség kiválasztásával:

   * A Service Bus névtérből korábban átmásolt kapcsolódási karakterlánc használatához kövesse az alábbi lépéseket:

     1. Válassza a **kapcsolatfelvételi adatok manuális megadása** lehetőséget.

     1. A **kapcsolatok neve** mezőben adja meg a kapcsolatok nevét. A **kapcsolatok karakterláncához** illessze be a névtér-kapcsolatok karakterláncát, és válassza a **Létrehozás** lehetőséget, például:

        ![Adja meg a kapcsolatok nevét és Service Bus a kapcsolatok karakterláncát](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Ha nem rendelkezik ezzel a kapcsolódási karakterlánccal, ismerkedjen meg [a Service Bus névtér kapcsolódási karakterláncának megkeresésével és másolásával](#permissions-connection-string).

   * Ha Service Bus névteret szeretne kiválasztani az aktuális Azure-előfizetésből, kövesse az alábbi lépéseket:

     1. A **kapcsolatok neve** mezőben adja meg a kapcsolatok nevét. **Service Bus névtér** esetében válassza ki a Service Bus névteret, például:

        ![Adja meg a kapcsolatok nevét, és válassza ki Service Bus névteret](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Amikor megjelenik a következő ablaktábla, válassza ki a Service Bus szabályzatot, és válassza a **Létrehozás** lehetőséget.

        ![Válassza ki Service Bus szabályzatot, majd a "létrehozás" lehetőséget.](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Ha elkészült, válassza a **Folytatás** lehetőséget.

   A Logic app Designer mostantól megjeleníti a **korrelált sorrend szerinti kézbesítést a Service Bus-munkamenetek** sablonnal, amely egy előre feltöltött munkafolyamatot tartalmaz egy triggerrel és műveletekkel, beleértve két olyan hatókört is, amelyek a mintázatot követő hibákat kezelik `Try-Catch` .

Most megtudhatja, hogyan használhatja a sablonban lévő triggert és műveleteket, vagy ugorjon a [logikai alkalmazás sablonjának értékeinek megadásához](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Sablon összegzése

A részletek összecsukása esetén a Service Bus-munkamenetek sablonjának legfelső szintű munkafolyamata a **korrelált sorrend szerinti kézbesítés** :

![Sablon legfelső szintű munkafolyamata](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Név | Leírás |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | A megadott ismétlődés alapján ez a Service Bus eseményindító minden üzenet esetében ellenőrzi a megadott Service Bus várólistát. Ha egy üzenet létezik a várólistában, az eseményindító elindít egy munkafolyamat-példányt, amely létrehoz és futtat egy munkafolyamatot. <p><p>A *betekintés – zárolás* kifejezés azt jelenti, hogy az trigger egy kérést küld egy üzenetnek a várólistából való beolvasásához. Ha egy üzenet létezik, az trigger lekéri és zárolja az üzenetet, hogy ne történjen más feldolgozás az üzeneten, amíg a zárolási időszak le nem jár. Részletekért [inicializálja a munkamenetet](#initialize-session). |
| **`Init isDone`** | Ez az [ **inicializálási változó** művelet](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) egy olyan logikai változót hoz létre, amely a `false` következő feltételek teljesülése esetén van beállítva és jelzi: <p><p>– A munkamenetben nem érhetők el több üzenet az olvasáshoz. <br>– A munkamenet-zárolást már nem kell megújítani, hogy az aktuális munkafolyamat-példány befejeződik. <p><p>Részletekért lásd: [a munkamenet inicializálása](#initialize-session). |
| **`Try`** | Ez a [ **hatókör** -művelet](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) az üzenetek feldolgozásához futtatott műveleteket tartalmazza. Ha probléma merül fel a `Try` hatókörben, a következő `Catch` **hatókör** -művelet kezeli ezt a problémát. További információt a ["Try" hatókörben](#try-scope)talál. |
| **`Catch`**| Ez a [ **hatókör** -művelet](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) tartalmazza azokat a műveleteket, amelyek akkor futnak, ha probléma merül fel az előző `Try` hatókörben. További információt a ["Catch" hatókörben](#catch-scope)talál. |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>"Kipróbálás" hatókör

A `Try` részletek összecsukása esetén itt látható a [hatókör művelet](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) legfelső szintű folyamata:

!["Try" hatóköri művelet munkafolyamata](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Név | Leírás |
|------|-------------|
| **`Send initial message to topic`** | Ezt a műveletet bármely olyan művelettel lecserélheti, amelyet az első üzenetnek a várólistán lévő munkamenetből való kezeléséhez szeretne kezelni. A munkamenet-azonosító megadja a munkamenetet. <p><p>Ehhez a sablonhoz egy Service Bus művelet küldi el az első üzenetet egy Service Bus témakörnek. Részletekért lásd: [a kezdeti üzenet kezelése](#handle-initial-message). |
| (párhuzamos ág) | Ez a [párhuzamos ág művelet](../logic-apps/logic-apps-control-flow-branches.md) két elérési utat hoz létre: <p><p>-Ág #1: az üzenet feldolgozásának folytatása. További információkért lásd [: ág #1: a kezdeti üzenet befejezése a várólistában](#complete-initial-message). <p><p>-Ág #2: az üzenet elhagyása, ha valami hiba lép fel, és a felvételt egy másik trigger futtatja. További információkért lásd [: ág #2: az első üzenet elhagyása a sorból](#abandon-initial-message). <p><p>Mindkét útvonal később csatlakozik a **várólista bezárási munkamenetéhez, és sikeres** műveletet végez a következő sorban leírtak szerint. |
| **`Close a session in a queue and succeed`** | Ez a Service Bus művelet összekapcsolja a korábban leírt ágakat, és a következő események bármelyikének megtörténte után bezárja a munkamenetet a várólistán: <p><p>– A munkafolyamat befejezi az elérhető üzenetek feldolgozását a várólistában. <br>– A munkafolyamat elhagyja a kezdeti üzenetet, mert hiba történt. <p><p>Részletekért lásd: [munkamenet lezárása egy várólistában és sikeres](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>Ág #1: a kezdeti üzenet befejezése a várólistában

| Név | Leírás |
|------|-------------|
| `Complete initial message in queue` | Ez a Service Bus művelet egy sikeresen lekért üzenetet jelöl meg befejezettként, és eltávolítja az üzenetet a várólistából az újrafeldolgozás megakadályozása érdekében. Részletekért lásd: [a kezdeti üzenet kezelése](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Ez [ **egészen addig, amíg** a hurok](../logic-apps/logic-apps-control-flow-loops.md#until-loop) továbbra is üzeneteket kap, vagy amíg egy óra el nem telik. Az ebben a hurokban található műveletekkel kapcsolatos további információkért lásd: a [várólistán található munkamenetek további üzenetei](#while-more-messages-for-session). |
| **`Set isDone = true`** | Ha nem létezik több üzenet, a [ **set változó** műveleti](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) beállítása a következőre: `isDone` `true` . |
| **`Renew session lock until cancelled`** | Ez [ **egészen addig, amíg** a hurok](../logic-apps/logic-apps-control-flow-loops.md#until-loop) megtartja a munkamenet zárolását a logikai alkalmazás, amíg az üzenetek léteznek, vagy amíg egy óra el nem telik. A hurok műveleteivel kapcsolatos további információkért lásd: [munkamenet zárolásának megújítása a megszakításig](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Ág #2: a várólista kezdeti üzenetének elhagyása

Ha az első üzenetet kezelő művelet meghiúsul, a Service Bus művelet, amely **elhagyja a várólistából a kezdeti üzenetet**, a felveszi és feldolgozza az üzenetet egy másik munkafolyamat-példány futtatásával. Részletekért lásd: [a kezdeti üzenet kezelése](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>"Catch" hatókör

Ha a `Try` hatókörben lévő műveletek meghiúsulnak, a logikai alkalmazásnak továbbra is be kell zárulnia a munkamenetben. A `Catch` [hatókör művelet](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) akkor fut le, amikor a `Try` hatókör művelet az állapot, a, vagy a állapotát eredményezi `Failed` `Skipped` `TimedOut` . A hatókör egy hibaüzenetet ad vissza, amely tartalmazza azt a munkamenet-azonosítót, amelyben a probléma történt, és leállítja a logikai alkalmazást.

A részletek összecsukása esetén itt látható a hatókör művelet legfelső szintű folyamata `Catch` :

!["Catch" hatóköri művelet munkafolyamata](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Név | Leírás |
|------|-------------|
| **`Close a session in a queue and fail`** | Ez a Service Bus művelet lezárja a munkamenetet a várólistán, így a munkamenet-zár nem marad nyitva. Részletekért lásd: [munkamenet lezárása egy várólistában és sikertelen](#close-session-fail)művelet. |
| **`Find failure msg from 'Try' block`** | Ez a [ **szűrő tömb** művelet](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) egy tömböt hoz létre a bemeneti adatokból és kimenetből a hatókörön belüli összes műveletből a `Try` megadott feltételek alapján. Ebben az esetben ez a művelet az állapotot eredményező műveletek kimeneteit adja vissza `Failed` . Részletekért lásd: [a "Try" blokkban található sikertelen msg keresése](#find-failure-message). |
| **`Select error details`** | Ez a [ **Select** művelet](../logic-apps/logic-apps-perform-data-operations.md#select-action) egy olyan tömböt hoz létre, amely JSON-objektumokat tartalmaz a megadott feltételek alapján. Ezek a JSON-objektumok az előző művelet által létrehozott tömbben lévő értékek alapján készültek `Find failure msg from 'Try' block` . Ebben az esetben ez a művelet egy tömböt ad vissza, amely az előző műveletből visszaadott hiba részleteiből létrehozott JSON-objektumot tartalmaz. Részletekért lásd: a [hiba részleteinek kiválasztása](#select-error-details). |
| **`Terminate`** | Ez a [ **megszakítási** művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) leállítja a munkafolyamat futtatását, megszakítja a folyamatban lévő műveleteket, kihagyja a hátralévő műveleteket, és visszaadja a megadott állapotot, a munkamenet-azonosítót és a művelet által okozott hibát `Select error details` . Részletekért lásd: [logikai alkalmazás leállítása](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>A sablon befejezése

Az alábbi lépéseket követve megadhatja az triggert és a műveleteket a **korrelált sorrendbe rendezésben a Service Bus-munkamenetek** sablonnal. A logikai alkalmazás mentése előtt meg kell adnia az összes szükséges értéket, amelyet egy csillag ( **\*** ) jelöl.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>A munkamenet inicializálása

* **Ha üzenet érkezik egy üzenetsor (bepillantás – zárolás)** triggerbe, adja meg ezeket az adatokat, hogy a sablon a **munkamenet-azonosító** tulajdonság használatával inicializálja a munkamenetet, például:

  ![Service Bus trigger részletei a következőhöz: "Ha üzenet érkezik egy várólistába (betekintési zárolás)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Kezdetben a lekérdezési időköz három percre van beállítva, így a logikai alkalmazás nem fut gyakrabban a vártnál, és nem várt számlázási díjakat eredményez. Ideális esetben az intervallumot és a gyakoriságot állítsa 30 másodpercre, hogy a logikai alkalmazás azonnal elindítson egy üzenetet.

  | Tulajdonság | Ehhez a forgatókönyvhöz szükséges | Érték | Leírás |
  |----------|----------------------------|-------|-------------|
  | **Üzenetsor neve** | Yes | <*üzenetsor – név*> | A korábban létrehozott Service Bus üzenetsor neve. Ez a példa a "Fabrikam-Service-Bus-üzenetsor" protokollt használja. |
  | **Várólista típusa** | Yes | **Fő** | Az elsődleges Service Bus üzenetsor |
  | **Munkamenet-azonosító** | Yes | **Következő elérhető** | Ez a beállítás az egyes triggerekhez tartozó munkameneteket az Service Bus üzenetsor üzenetében található munkamenet-azonosító alapján kapja meg. A munkamenet zárolása is megtörténik, így más logikai alkalmazások vagy más ügyfelek nem dolgozhatják fel az ehhez a munkamenethez kapcsolódó üzeneteket. A munkafolyamat későbbi műveletei az adott munkamenethez társított összes üzenetet feldolgozzák a jelen cikk későbbi részében leírtak szerint. <p><p>További információ a **munkamenet-azonosító** további lehetőségeiről: <p>- **Nincs**: az alapértelmezett beállítás, amely nem használ munkameneteket, és nem használható a szekvenciális konvoj mintázatának megvalósításához. <p>- **Adja meg az egyéni értéket**: akkor használja ezt a beállítást, ha ismeri a használni kívánt munkamenet-azonosítót, és mindig az adott munkamenet-azonosítóhoz tartozó triggert szeretné futtatni. <p>**Megjegyzés**: az Service Bus-összekötő egyszerre csak korlátozott számú egyedi munkamenetet tud menteni Azure Service Busról az összekötő gyorsítótárába. Ha a munkamenetek száma meghaladja ezt a korlátot, a rendszer eltávolítja a régi munkameneteket a gyorsítótárból. További információ: [Exchange-üzenetek a felhőben Azure Logic apps és Azure Service Bus](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Intervallum** | Yes | <*intervallumok száma*> | Az ismétlődések közötti időegységek száma az üzenet ellenőrzése előtt. |
  | **Gyakoriság** | Yes | **Másodperc**, **perc**, **óra**, **nap**, **hét** vagy **hónap** | Az üzenet keresésekor használandó időegység. <p>**Tipp**: **időzóna** vagy **Kezdési idő** hozzáadásához válassza ki ezeket a tulajdonságokat az **új paraméter hozzáadása** listáról. |
  |||||

  További információ az aktiválásról: [Service Bus – ha üzenet érkezik egy várólistába (betekintés – zárolás)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). Az trigger kimenete egy [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

A munkamenet inicializálását követően a munkafolyamat az **inicializálási változót** használja egy olyan logikai változó létrehozásához, amely kezdetben be `false` van állítva, és jelzi, hogy a következő feltételek teljesülnek-e: 

* Nem érhető el több üzenet a munkamenetben.

* A munkamenet-zárolást már nem kell megújítani, hogy az aktuális munkafolyamat-példány be tudja fejezni.

!["Változó inicializálása" művelet részletei "init isDone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Ezután a **Try** blokkban a munkafolyamat műveleteket hajt végre az első olvasott üzenetben.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>A kezdeti üzenet kezelése

Az első művelet egy helyőrző Service Bus művelet, a **kezdeti üzenet küldése a témakörnek**, amelyet bármilyen más művelettel lecserélhet, amelyet a várólistán lévő munkamenet első üzenetével kíván kezelni. A munkamenet-azonosító meghatározza azt a munkamenetet, ahonnan az üzenet származik.

A helyőrző Service Bus művelet elküldi az első üzenetet a **munkamenet-azonosító** tulajdonság által megadott Service Bus témakörnek. Így egy adott munkamenethez társított összes üzenet ugyanarra a témakörre mutat. A sablon további műveleteihez tartozó **munkamenet-azonosító** tulajdonságai ugyanazt a munkamenet-azonosító értéket használják.

![A "kezdeti üzenet küldése a témakörbe" Service Bus művelet részletei](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. A Service Bus műveletben fejezze be a **kezdeti üzenetet a várólistában**, adja meg a Service Bus üzenetsor nevét, és tartsa meg az összes többi alapértelmezett tulajdonság értékét a műveletben.

   ![Service Bus művelet részletei a "kezdeti üzenet a várólistában"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. A Service Bus műveletben **hagyja ki a várólista kezdeti üzenetét**, adja meg a Service Bus üzenetsor nevét, és tartsa meg az összes többi alapértelmezett tulajdonság értékét a műveletben.

   ![A "kezdeti üzenet elhagyása a sorból" című Service Bus művelet részletei](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Ezután meg kell adnia a szükséges információkat azokhoz a műveletekhez, amelyek a **teljes üzenetsor-** művelettel kapcsolatos kezdeti üzenetet követik. A műveletekkel együtt kell kezdenie, **amíg a munkamenetben több üzenet szerepel a várólista-** ciklusban.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Noha a várólistában több üzenet is található a munkamenethez

Ez [ **egészen addig, amíg** a ciklus nem](../logic-apps/logic-apps-control-flow-loops.md#until-loop) futtatja ezeket a műveleteket, amíg az üzenetek szerepelnek a várólistában, vagy amíg egy óráig nem halad A hurok időkorlátjának módosításához szerkessze a hurok **időtúllépési** tulajdonságának értékét.

* További üzenetek beolvasása a sorból az üzenetek létezése közben.

* A fennmaradó üzenetek számának a megadásához. Ha az üzenetek továbbra is fennállnak, folytassa az üzenetek feldolgozását. Ha nem létezik több üzenet, a munkafolyamat beállítja a `isDone` változót `true` , és kilép a hurokból.

![Az üzenetek a várólistán való feldolgozása közben](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. A Service Bus műveletben **további üzeneteket kaphat a munkamenetből**, adja meg a Service Bus üzenetsor nevét. Ellenkező esetben tartsa meg az összes többi alapértelmezett tulajdonság értékét a műveletben.

   > [!NOTE]
   > Alapértelmezés szerint az üzenetek maximális száma értékre van állítva `175` , de ezt a korlátot a Service Bus üzenet mérete és az üzenet mérete tulajdonság is befolyásolja. További információ: [üzenetsor üzenetének mérete](../service-bus-messaging/service-bus-quotas.md).

   ![Service Bus művelet – "további üzenetek beolvasása a munkamenetből"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Ezután a munkafolyamat a következő párhuzamos ágakra oszlik:

   * Ha hiba vagy hiba történik a további üzenetek keresésekor, állítsa a változót a következőre: `isDone` `true` .

   * A **folyamat üzenetei, ha bármilyen** feltétel ellenőrzi, hogy a fennmaradó üzenetek száma nulla-e. Ha hamis és több üzenet is létezik, folytassa a feldolgozást. Ha az értéke igaz, és nem létezik több üzenet, a munkafolyamat a változót a következőre állítja be: `isDone` `true` .

   ![Feltétel – az üzenetek feldolgozása, ha van ilyen](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   Ha az **IF false (hamis** ) szakaszban az **egyes** ciklusok mindegyike feldolgozza az egyes üzeneteket, először a kezdeti sorrendben (FIFO). A hurok **beállításaiban** a **Egyidejűség vezérlőelem** beállítása a (z) értékre van állítva `1` , így egyszerre csak egyetlen üzenet lesz feldolgozva.

   !["Az egyes" hurok-feldolgozáshoz minden egyes üzenet egyszerre](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. A Service Bus műveletek esetében **hajtsa végre az üzenetet egy várólistában** , és **hagyjon le egy várólistában lévő üzenetet**, adja meg a Service Bus üzenetsor nevét.

   ![Service Bus műveletek – "az üzenet befejezése egy várólistában" és "az üzenet megszakítása egy várólistában"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Miután a **várólista-munkamenethez több üzenet is érkezett** , a munkafolyamat a változót a következőre állítja be: `isDone` `true` .

Ezután meg kell adnia a műveletekhez szükséges adatokat a **megújítási munkamenet-zárolásban a megszakított** ciklusig.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Munkamenet zárolásának megújítása a megszakításig

Ez [ **egészen addig, amíg** a hurok](../logic-apps/logic-apps-control-flow-loops.md#until-loop) megtartja a munkamenet zárolását a logikai alkalmazás, miközben az üzenetek szerepelnek a várólistában, vagy amíg egy óra el nem telik a műveletek futtatásával. A hurok időkorlátjának módosításához szerkessze a hurok **időtúllépési** tulajdonságának értékét.

* A feldolgozás alatt álló várólista időtúllépési idejének kevesebb, mint 25 másodpercig, vagy pedig a zárolás időkorlátja. A minimális zárolási időtartam 30 másodperc, így az alapértelmezett érték elég. Azonban optimalizálhatja a hurok futásának számát a megfelelő beállítással.

* Győződjön meg arról, hogy a változó a következőre `isDone` van beállítva: `true` .

  * Ha a nem értékre `isDone` van állítva `true` , a munkafolyamat továbbra is üzeneteket dolgoz fel, így a munkafolyamat megújítja a munkamenet zárolását a várólistán, és ismét ellenőrzi a hurok feltételét.

    Meg kell adnia a Service Bus üzenetsor nevét a Service Bus műveletben, a [**munkamenet zárolásának megújítása egy várólistán**](#renew-lock-on-session).

  * Ha a `isDone` értéke `true` , akkor a munkafolyamat nem újítja meg a várólistán lévő munkamenet zárolását, és kilép a hurokból.

  ![Ciklusig – "a munkamenet zárolásának megújítása a megszakításig"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Várólistán lévő munkamenet zárolásának megújítása

Ez a Service Bus művelet megújítja a munkamenet zárolását a várólistán, miközben a munkafolyamat továbbra is üzeneteket dolgoz fel.

* A Service Bus műveletben a **munkamenet zárolásának megújítása egy várólistán**, adja meg a Service Bus üzenetsor nevét.

  ![Service Bus művelet – "zárolás megújítása a várólistán"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Ezután meg kell adnia a Service Bus művelethez szükséges adatokat, **lezárta a munkamenetet egy várólistában, és sikeres** lesz.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Munkamenet lezárása egy várólistában és sikeres

Ez a Service Bus művelet lezárja a munkamenetet a várólistán, miután a munkafolyamat befejezte a várólistán lévő összes elérhető üzenet feldolgozását, vagy a munkafolyamat elhagyja a kezdeti üzenetet.

* A Service Bus műveletben **zárjuk be a munkamenetet egy várólistában, és a sikerhez** adja meg a Service Bus üzenetsor nevét.

  ![Service Bus művelet – "munkamenet lezárása egy várólistában és sikeres"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

A következő szakaszok ismertetik a szakasz azon műveleteit `Catch` , amelyek a munkafolyamatban előforduló hibákat és kivételeket kezelik.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Munkamenet lezárása egy várólistában és sikertelen

Ez a Service Bus művelet mindig a hatókör első műveletének megfelelően fut, `Catch` és bezárja a munkamenetet a várólistán.

* A Service Bus műveletben **zárjuk be a munkamenetet egy várólistában, és a hiba** esetén adja meg a Service Bus üzenetsor nevét.

  ![Service Bus művelet – "munkamenet lezárása egy várólistában és sikertelen"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Ezután a munkafolyamat létrehoz egy tömböt, amely a hatókör összes műveletének bemeneteit és kimeneteit is megjeleníti, `Try` így a logikai alkalmazás hozzáférhet a hibával vagy a hibával kapcsolatos adatokhoz.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Hibaüzenetek keresése a "Try" blokkból

Ez a [ **szűrő tömb** művelet](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) egy tömböt hoz létre, amely a hatókörön belüli összes művelet bemeneteit és kimeneteit a `Try` [ `result()` függvény](../logic-apps/workflow-definition-language-functions-reference.md#result)használatával a megadott feltételek alapján állítja be. Ebben az esetben ez a művelet az állapottal rendelkező műveletek kimeneteit adja vissza a `Failed` [ `equals()` függvény](../logic-apps/workflow-definition-language-functions-reference.md#equals) és a [ `item()` függvény](../logic-apps/workflow-definition-language-functions-reference.md#item)használatával.

![Tömb szűrése művelet – "a hibaüzenetek keresése a következőből" Try "letiltás"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

A művelet JSON-definíciója a következő:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Ezután a munkafolyamat létrehoz egy tömböt egy JSON-objektummal, amely tartalmazza a műveletből visszaadott tömbben található hibaüzeneteket `Find failure msg from 'Try' block` .

<a name="select-error-details"></a>

### <a name="select-error-details"></a>A hiba részleteinek kiválasztása

Ez a [ **Select** művelet](../logic-apps/logic-apps-perform-data-operations.md#select-action) létrehoz egy TÖMBÖt, amely JSON-objektumokat tartalmaz az előző műveletből származó kimenetből származó bemeneti tömb alapján `Find failure msg from 'Try' block` . Pontosabban, ez a művelet egy olyan tömböt ad vissza, amely csak a tömbben lévő összes objektumhoz megadott tulajdonságokkal rendelkezik. Ebben az esetben a tömb a művelet nevét és a hiba eredményének tulajdonságait tartalmazza.

![Válassza a művelet – "a hiba részleteinek kiválasztása" lehetőséget.](./media/send-related-messages-sequential-convoy/select-error-details.png)

A művelet JSON-definíciója a következő:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Ezután a munkafolyamat leállítja a logikai alkalmazás futtatását, és visszaadja a futtatási állapotot, valamint további információt a hibáról vagy a hibaről.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Logikai alkalmazás futtatásának leállítása

Ez a leállítási [  művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) leállítja a logikai alkalmazás futtatását, és visszaadja a `Failed` logikai alkalmazás futtatásának állapotát a munkamenet-azonosítóval és a művelet hibájának eredményével együtt `Select error details` .

![A logikai alkalmazás futtatásának leállítására szolgáló művelet leállítása](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

A művelet JSON-definíciója a következő:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Logikai alkalmazás mentése és futtatása

A sablon befejezése után már mentheti a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

A logikai alkalmazás teszteléséhez küldje el az üzeneteket a Service Bus-várólistába. 

## <a name="next-steps"></a>Következő lépések

* További információ az [Service Bus-összekötő eseményindítóinak és műveleteiről](/connectors/servicebus/)
