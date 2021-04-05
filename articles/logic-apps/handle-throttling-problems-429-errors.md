---
title: Szabályozási problémák kezelése vagy "429 – túl sok kérés" hiba
description: A szabályozással kapcsolatos problémák vagy a HTTP 429 túl sok kérés hibájának megoldása a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94366051"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Szabályozási problémák kezelése (429 – "túl sok kérés" hiba) a Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md)esetén a logikai alkalmazás ["http 429 túl sok kérés" hibát](https://developer.mozilla.org/docs/Web/HTTP/Status/429) ad vissza a szabályozás során, ami akkor fordul elő, ha a kérések száma meghaladja azt a sebességet, amellyel a cél egy adott időtartamon belül kezelhető. A szabályozás olyan problémákat okozhat, mint például a késleltetett adatfeldolgozás, a teljesítmény sebességének csökkentése és a hibák, például a megadott újrapróbálkozási házirend túllépése.

![Szabályozás SQL Server-összekötőben](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Íme néhány gyakori fajta szabályozás, amelyet a logikai alkalmazás tapasztalhat:

* [Logikai alkalmazás](#logic-app-throttling)
* [Összekötő](#connector-throttling)
* [Cél szolgáltatás vagy rendszer](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Logic app-szabályozás

A Azure Logic Apps szolgáltatásnak saját [átviteli korlátja](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)van. Így ha a logikai alkalmazás túllépi ezeket a korlátokat, a logikai alkalmazás erőforrása leszabályozásra kerül, nem csak egy adott példányra vagy futtatásra.

Ha ezen a szinten szeretné megkeresni a szabályozási eseményeket, tekintse meg a logikai alkalmazás **metrikái** paneljét a Azure Portal.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menü **figyelés** területén válassza a **metrikák** lehetőséget.

1. A **diagram címe** területen válassza a **metrika hozzáadása** lehetőséget, hogy egy újabb mérőszámot adjon hozzá a meglévőhöz.

1. Az első metrika sávban a **metrika** listából válassza a **művelet által szabályozott események** elemet. A második metrika sávban, a **metrika** listából válassza ki az **aktiválási szabályozott események** elemet.

A szabályozás ezen a szinten való kezeléséhez a következő lehetőségek közül választhat:

* Korlátozza az egyszerre futtatható logikai alkalmazás-példányok számát.

  Alapértelmezés szerint, ha a logikai alkalmazás trigger-feltétele többször is teljesül egyszerre, a logikai alkalmazás több trigger-példánya egyidejűleg vagy *párhuzamosan* fut. Ez a viselkedés azt jelenti, hogy minden eseményindító-példány az előző munkafolyamat-példány futásának befejeződése előtt következik be.

  Habár a párhuzamosan futtatható trigger-példányok alapértelmezett száma [korlátlan](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), ezt a számot korlátozhatja [az trigger párhuzamossági beállításának bekapcsolásával](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency), és szükség esetén az alapértelmezett értéktől eltérő korlátot is megadhat.

* Magas átviteli sebességű üzemmód engedélyezése.

  A logikai alkalmazások [alapértelmezett korlátja a legfeljebb 5 perces időszakra futtatható műveletek száma](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Ha ezt a korlátot a műveletek maximális számához szeretné emelni, kapcsolja be a [nagy átviteli sebességet](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) a logikai alkalmazásban.

* Tiltsa le a tömb leválasztását ("felosztás bekapcsolva") az eseményindítók viselkedését.

  Ha egy trigger egy tömböt ad vissza a feldolgozni kívánt hátralévő munkafolyamat-műveletekhez, a rendszer a tömb elemeinek [  felosztását](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) és az egyes tömbökhöz tartozó munkafolyamat-példányok elindítását eredményezi, ami gyakorlatilag egyszerre több párhuzamos futtatást indít el a [  korláton](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). A szabályozás szabályozása érdekében kapcsolja ki a **felosztott** viselkedést, és a logikai alkalmazás a teljes tömböt egyetlen hívással dolgozza fel, és ne csak egyetlen elem legyen kezelve.

* Újrabontási műveletek kisebb logikai alkalmazásokba.

  Ahogy azt korábban említettük, egy logikai alkalmazás a [műveletek alapértelmezett számú műveletére korlátozódik, amely 5 percen belül futtatható](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Bár a [magas átviteli üzemmód](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)engedélyezésével növelheti ezt a korlátot, érdemes megfontolnia, hogy a logikai alkalmazás műveleteit kisebb logikai alkalmazásokba szeretné-e bontani, hogy az egyes logikai alkalmazásokban futó műveletek száma a határérték alatt maradjon. Így csökkentheti a terhelést egy logikai alkalmazás-erőforrás terhére, és elosztja a terhelést több logikai alkalmazás között. Ez a megoldás hatékonyabban működik a nagyméretű adatkészleteket kezelő műveleteknél, vagy olyan sok párhuzamosan futó műveletet, hurkos iterációt vagy műveleteket hajt végre az egyes hurok-ismétléseken belül, amelyek túllépik a művelet végrehajtási korlátját.

  Ez a logikai alkalmazás például elvégzi az összes munkát egy SQL Server-adatbázis tábláinak beolvasásához és az egyes táblák sorainak lekéréséhez. Az **minden** hurok párhuzamosan megismétli az egyes táblákat, így a **sorok beolvasása** művelet az egyes táblák sorait adja vissza. A táblákban tárolt adatmennyiség alapján ezek a műveletek túllépik a művelet végrehajtásának korlátját.

  ![Logikai alkalmazás "az újrabontás előtt"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Az újrabontás után a logikai alkalmazás most már szülő és gyermek logikai alkalmazás. A szülő beolvassa a táblákat a SQL Serverból, majd meghívja az egyes táblák alárendelt logikai alkalmazásait a sorok beolvasásához:

  ![Logikai alkalmazás létrehozása egy művelethez](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  A szülő logikai alkalmazás által hívott gyermek logikai alkalmazás az egyes táblák sorainak beolvasásához:

  ![Másik logikai alkalmazás létrehozása egy második művelethez](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Összekötő szabályozása

Mindegyik összekötő saját szabályozási korlátozásokkal rendelkezik, amelyek az összekötő technikai útmutató lapján találhatók meg. Például az Azure Service Bus- [összekötő](/connectors/servicebus/) olyan sávszélesség-szabályozási korláttal rendelkezik, amely percenként akár 6 000 hívást tesz lehetővé, míg a SQL Server-összekötő a [Művelet típusától függően eltérő korlátozásokkal](/connectors/sql/)rendelkezik.

Egyes eseményindítók és műveletek, például a HTTP, ["újrapróbálkozási szabályzattal"](../logic-apps/logic-apps-exception-handling.md#retry-policies) rendelkeznek, amelyeket testreszabhat az [újrapróbálkozási házirend korlátai](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) alapján a kivételek kezelésére. Ez a házirend határozza meg, hogy egy trigger vagy művelet milyen gyakran próbálkozik újra egy kéréssel, ha az eredeti kérelem meghiúsul vagy időtúllépés miatt megszakad, és 408, 429 vagy 5xx választ eredményez. Így ha a szabályozás elindul, és 429 hibát ad vissza, Logic Apps követi az újrapróbálkozási szabályzatot, ahol a támogatott.

Ha meg szeretné tudni, hogy egy trigger vagy művelet támogatja-e az újrapróbálkozási szabályzatokat, tekintse meg az trigger vagy a művelet beállításait. Ha meg szeretné tekinteni egy trigger vagy művelet újrapróbálkozási kísérleteit, nyissa meg a logikai alkalmazás futtatási előzményeit, válassza ki az áttekinteni kívánt futtatást, és bontsa ki az triggert vagy a műveletet a bemenetek, kimenetek és az újrapróbálkozások részleteinek megtekintéséhez, például:

![A művelet futtatási előzményeinek, újrapróbálkozások, bemenetek és kimenetek megtekintése](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Bár az újrapróbálkozási előzmények hibaüzeneteket biztosítanak, előfordulhat, hogy az összekötő szabályozása és a [cél szabályozása](#destination-throttling)közötti különbségtétel nem sikerül. Ebben az esetben előfordulhat, hogy át kell tekintenie a válasz részleteit, vagy végre kell hajtania néhány szabályozási intervallum számítást a forrás azonosításához.

A globális, több-bérlős Azure Logic Apps szolgáltatásbeli Logic apps esetén a szabályozás a *kapcsolatok* szintjén történik. Így például az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)futó Logic apps esetében a szabályozás továbbra is a nem ISE kapcsolatok esetében történik, mivel azok a globális, több-bérlős Logic apps szolgáltatásban futnak. Az ISE-összekötők által létrehozott ISE-kapcsolatok azonban nem szabályozva vannak, mert az ISE-ben futnak.

A szabályozás ezen a szinten való kezeléséhez a következő lehetőségek közül választhat:

* Több kapcsolat beállítása egyetlen művelethez, hogy a logikai alkalmazás particionálja az adatfeldolgozást.

  Ennél a beállításnál érdemes megfontolni, hogy el tudja-e osztani a munkaterhelést úgy, hogy a művelet kéréseit több kapcsolaton keresztül egy adott célhelyre osztja, ugyanazon hitelesítő adatok használatával.

  Tegyük fel például, hogy a logikai alkalmazás táblázatokat kap egy SQL Server adatbázisból, majd lekéri az egyes táblák sorait. A feldolgozandó sorok száma alapján több kapcsolatot is használhat, és **mindegyik** hurkohoz több kapcsolat is használható, hogy a sorok teljes számát a feldolgozásra kisebb készletekre ossza. Ez a **forgatókönyv két hurkot használ a sorok** teljes számának felosztásához. Az első **minden** hurokhoz egy kifejezést használ, amely az első felet kapja meg. Az **egyes** hurkok másik kifejezése egy másik kifejezést használ, amely a második felet kapja, például:<p>

    * 1. kifejezés: a `take()` függvény egy gyűjtemény elejét kapja. További információ: [ **`take()`** függvény](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 2. kifejezés: a `skip()` függvény eltávolítja a gyűjtemény elejét, és az összes többi elemet visszaadja. További információ: [ **`skip()`** függvény](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Íme egy vizualizációs példa, amely bemutatja, hogyan használhatja ezeket a kifejezéseket:

    ![Több kapcsolat létrehozása és használata egyetlen művelethez](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Egy másik kapcsolat beállítása minden művelethez.

  Ebben a beállításban gondolja át, hogy el tudja-e osztani a munkaterhelést úgy, hogy az egyes műveletek kéréseit a saját kapcsolaton keresztül terjeszti, még akkor is, ha a műveletek ugyanahhoz a szolgáltatáshoz vagy rendszerhez csatlakoznak, és ugyanazokat a

  Tegyük fel például, hogy a logikai alkalmazás beolvassa a táblákat egy SQL Server adatbázisból, és beolvassa az egyes sorokat az egyes táblákba. Különálló kapcsolatokat is használhat, hogy a táblák beolvasása egyetlen kapcsolaton keresztül történjen, míg az egyes sorok beolvasása egy másik kapcsolatot használ.

  ![Különböző kapcsolatok létrehozása és használata minden művelethez](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Változtassa meg a párhuzamosságot vagy a párhuzamosságot egy ["for each" ciklusban](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Alapértelmezés szerint a "for each" hurok-iterációk egyidejűleg futnak a [egyidejűségi korláttal](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Ha olyan összekötővel rendelkezik, amely egy "for each" ciklus belsejében van, csökkentheti a párhuzamosan futó hurok-ismétlések számát. További információt az alábbi témakörökben talál:
  
  * ["Az egyes ciklusokban – párhuzamosság módosítása vagy szekvenciális futtatása](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Munkafolyamat-definíció nyelvi sémája – minden hurokhoz](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Munkafolyamat-definíció nyelvi sémája – módosítás "az egyes hurok-párhuzamosságokhoz](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Munkafolyamat-definíció nyelvi sémája – Futtatás "az egyes ciklusok egymás után"](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>A cél szolgáltatás vagy a rendszer szabályozása

Amíg egy összekötő saját szabályozási korláttal rendelkezik, az összekötő által meghívott cél szolgáltatás vagy rendszer is rendelkezhet szabályozási korlátokkal. Előfordulhat például, hogy a Microsoft Exchange Server egyes API-jai szigorúbb szabályozási korlátokkal rendelkeznek, mint az Office 365 Outlook Connector.

Alapértelmezés szerint a logikai alkalmazás példányai és az ezekben a példányokban lévő hurkok vagy ágak *párhuzamosan* futnak. Ez azt jelenti, hogy több példány is meghívhatja ugyanazt a végpontot egyidejűleg. Az egyes példányok nem ismerik a másik létezését, ezért a sikertelen műveletek újrapróbálkozására tett kísérletek olyan [versenyfeltételeket](https://en.wikipedia.org/wiki/Race_condition) hozhatnak létre, amelyben több hívás próbálkozik egyszerre, de a sikeres végrehajtáshoz a hívásoknak meg kell érkezniük a célkiszolgálón vagy a rendszeren a szabályozás megkezdése előtt.

Tegyük fel például, hogy rendelkezik egy 100 elemet tartalmazó tömbvel. Az "for each" ciklushoz a tömbben kell megismételni a tömböt, és be kell kapcsolni a hurok egyidejűségének vezérlőjét, hogy a párhuzamos iterációk száma 20 vagy a [jelenlegi alapértelmezett korláton](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)legyen korlátozva. Ezen a hurokon belül egy művelet beszúr egy elemet a tömbből egy SQL Server-adatbázisba, amely másodpercenként csak 15 hívást tesz lehetővé. Ez a forgatókönyv sávszélesség-szabályozást eredményez, mert az újrapróbálkozások száma, és soha nem fog futni.

Ez a táblázat azt az idővonalat mutatja be, hogy mi történik a hurokban, ha a művelet újrapróbálkozási időköze 1 másodperc:

| Időpont | Futó műveletek száma | Sikertelen műveletek száma | Várakozási kísérletek száma |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 másodperc | 20 lapka | 5 sikertelen, SQL-korlát miatt | 5 újrapróbálkozás |
| T + 0,5 másodperc | 15 lapka, az előző 5 próbálkozás miatt várakozás | Mind a 15 sikertelen, mert az előző SQL-korlát még egy másik 0,5 másodpercig érvényes | 20 újrapróbálkozás <br>(előző 5 + 15 új) |
| T + 1 másodperc | 20 lapka | 5 sikertelen és az előző 20 újrapróbálkozások, az SQL-korlát miatt | 25 újrapróbálkozás (előző 20 + 5 új)
|||||

A szabályozás ezen a szinten való kezeléséhez a következő lehetőségek közül választhat:

* Hozzon létre logikai alkalmazásokat úgy, hogy mindegyik egyetlen műveletet kezelje.

  * Az ebben a szakaszban SQL Server forgatókönyvben szereplő példának megfelelően létrehozhat egy olyan logikai alkalmazást, amely tömbös elemeket helyez el egy várólistába, például egy [Azure Service Bus üzenetsor](../connectors/connectors-create-api-servicebus.md). Ezután létrehozhat egy másik logikai alkalmazást, amely csak az INSERT műveletet hajtja végre a várólista minden egyes eleménél. Így egy adott időpontban csak egy logikai alkalmazás-példány fut, amely vagy befejezi a beszúrási műveletet, és továbblép a várólista következő elemére, vagy a példány 429 hibát jelez, de nem próbálkozik a nem termék-újrapróbálkozásokkal.

  * Hozzon létre egy szülő logikai alkalmazást, amely minden művelethez egy gyermek vagy beágyazott logikai alkalmazást hív meg. Ha a szülő kimenetele alapján különböző alárendelt alkalmazásokat kell meghívnia, használhat egy feltételt vagy egy váltás műveletet, amely meghatározza, hogy melyik gyermek alkalmazást kell meghívni. Ez a minta segítséget nyújt a hívások vagy műveletek számának csökkentésében.

    Tegyük fel például, hogy két logikai alkalmazással rendelkezik, amelyek mindegyike egy lekérdezési triggerrel rendelkezik, amely egy adott tárgyhoz percenként ellenőrzi az e-mail-fiókját, például "sikeres" vagy "hiba". Ez a telepítés 120 hívást eredményez óránként. Ehelyett, ha olyan szülő logikai alkalmazást hoz létre, amely percenként kérdez le, de olyan gyermek logikai alkalmazást hív meg, amely a tulajdonos "sikeres" vagy "sikertelen" állapotára támaszkodik, a lekérdezési csekkek felét kivágja a fele vagy a 60 értékre.

* Kötegelt feldolgozás beállítása.

  Ha a cél szolgáltatás támogatja a Batch-műveleteket, akkor a szabályozást úgy is megtekintheti, ha a csoportokban vagy kötegekben lévő elemeket nem külön-külön dolgozza fel. A batch-feldolgozó megoldás megvalósításához létre kell hoznia egy "batch-fogadó" logikai alkalmazást és egy "batch feladó" logikai alkalmazást. A Batch-küldő üzeneteket vagy elemeket gyűjt, amíg a megadott feltételek teljesülnek, majd ezeket az üzeneteket vagy elemeket egyetlen csoportban küldi el. A Batch-fogadó elfogadja a csoportot, és feldolgozza ezeket az üzeneteket vagy elemeket. További információ: [Batch Process messages in groups](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* A lekérdezési verziók helyett használja az eseményindítók és műveletek webhook-verzióit.

  Miért? A lekérdezési trigger a cél szolgáltatást vagy a rendszer adott időközönként történő ellenőrzését is folytatja. Egy nagyon gyakori intervallum, például a másodpercenként, létrehozhat szabályozási problémákat. Egy webhook-eseményindító vagy-művelet (például a [http-webhook](../connectors/connectors-native-webhook.md)) csak egyetlen hívást hoz létre a cél szolgáltatás vagy a rendszer számára, amely az előfizetés időpontjában történik, és azt kéri, hogy a cél csak akkor jelezze az eseményindítót vagy műveletet, ha az esemény történik. Így az triggernek vagy a műveletnek nem kell folyamatosan ellenőriznie a célhelyet.
  
  Így ha a cél szolgáltatás vagy a rendszer támogatja a webhookokat, vagy egy olyan összekötőt biztosít, amely webhook-verziót tartalmaz, akkor ez a lehetőség jobb, mint a lekérdezési verzió használata. A webhook-eseményindítók és-műveletek azonosításához ellenőrizze, hogy rendelkeznek-e a `ApiConnectionWebhook` típussal, vagy hogy nem igénylik az ismétlődés megadását. További információ: [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) és [APIConnectionWebhook művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Következő lépések

* További információ a [Logic apps korlátairól és konfigurációjáról](../logic-apps/logic-apps-limits-and-config.md)
