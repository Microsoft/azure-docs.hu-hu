---
title: Kötegelt feldolgozási üzenetek csoportként
description: Üzenetek küldése és fogadása csoportokban a munkafolyamatok között a kötegelt feldolgozás használatával Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87458255"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Üzenetek küldése, fogadása és kötegelt feldolgozása Azure Logic Apps

Az üzenetek küldéséhez és feldolgozásához a csoportokkal együtt, létrehozhat egy kötegelt megoldást. Ez a megoldás az üzeneteket egy *kötegbe* gyűjti, és megvárja, amíg a megadott feltételek teljesülnek a kötegelt üzenetek kiadása és feldolgozása előtt. A kötegelt feldolgozással csökkentheti, hogy a logikai alkalmazás milyen gyakran dolgozza fel az üzeneteket.

Ez a cikk bemutatja, hogyan hozhat létre kötegelt megoldást úgy, hogy két logikai alkalmazást hoz létre ugyanabban az Azure-előfizetésben, az Azure-régióban és ebben a sorrendben:

1. A ["batch receiver"](#batch-receiver) logikai alkalmazás, amely az üzeneteket egy kötegbe fogadja és gyűjti, amíg a megadott feltételek teljesülnek az üzenetek felszabadításához és feldolgozásához. Először hozza létre ezt a Batch-fogadót, hogy később kiválassza a Batch-célt a Batch-feladó létrehozásakor.

1. Egy vagy több ["batch feladó"](#batch-sender) logikai alkalmazás, amely az üzeneteket a korábban létrehozott batch-fogadónak küldi el.

   Megadhat egy egyedi kulcsot is, például egy vevőszámot, a *partíciókat* , illetve a cél köteget logikai részhalmazokra osztja a kulcs alapján. Így a fogadó alkalmazás összegyűjtheti az összes elemet ugyanazzal a kulccsal, és együtt is feldolgozza azokat.

A Batch-fogadónak és a Batch-küldőnek ugyanazt az Azure-előfizetést *és* Azure-régiót kell megosztania. Ha nem, akkor nem választhatja ki a Batch-fogadót a Batch-feladó létrehozásakor, mert azok nem láthatók egymás számára.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik előfizetéssel, [kezdheti egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Vagy regisztráljon az utólagos elszámolású [előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* E-mail-fiók minden [Azure Logic apps által támogatott e-mail-szolgáltatóval](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ha a Visual studiót a Azure Portal helyett szeretné használni, győződjön meg arról, hogy a [Visual studiót a Logic apps használatára állítja be](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Batch-fogadó létrehozása

Ahhoz, hogy üzeneteket küldhessen egy kötegbe, a kötegnek először az üzeneteket küldő célhelyként kell lennie. Először létre kell hoznia a "batch-fogadó" logikai alkalmazást, amely a **Batch** -triggerrel kezdődik. Így a "batch Sender" logikai alkalmazás létrehozásakor kiválaszthatja a Batch-fogadó logikai alkalmazást. A Batch-fogadó folytatja az üzenetek gyűjtését, amíg a megadott feltételek teljesülnek az üzenetek felszabadításához és feldolgozásához. Habár a Batch-fogadóknak semmit nem kell tudniuk a Batch-küldők számára, a Batch-küldőknek ismerniük kell az üzeneteket küldő célhelyet.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy logikai alkalmazást a következő névvel: `BatchReceiver`

1. A Logic app Designerben adja hozzá a **Batch** -triggert, amely elindítja a logikai alkalmazás munkafolyamatát. A keresőmezőbe írja be a kifejezést `batch` , majd válassza ki a következő triggert: **Batch-üzenetek**

   !["Batch-üzenetek" trigger hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Adja meg a következő tulajdonságokat a Batch-fogadó számára:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Batch mód** | - **Beágyazott**: a kiadási feltételek meghatározása a Batch-triggeren belül <br>- **Integrációs fiók**: több kiadási feltétel konfigurációjának definiálása egy [integrációs fiókon](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)keresztül. Integrációs fiókkal a konfigurációkat egy helyen kezelheti, nem pedig külön logikai alkalmazásokban. |
   | **Köteg neve** | A Batch neve, amely ebben a példában a "TestBatch", és csak a **beágyazott** batch üzemmódra vonatkozik |
   | **Kiadási feltételek** | Csak a **beágyazott** batch üzemmódra vonatkozik, és az egyes kötegek feldolgozása előtt kijelöli a feltételeknek megfelelő feltételeket: <p>- **Üzenetek száma**: a Batch által gyűjtött üzenetek száma alapján szabadítsa fel a köteget. <br>- **Méret alapú**: az adott köteg által gyűjtött összes üzenet bájtban kifejezett teljes mérete alapján szabadítsa fel a köteget. <br>- **Ütemezése**: a köteg felszabadítása egy ismétlődési ütemterv alapján, amely az intervallumot és a gyakoriságot határozza meg. A speciális beállítások területen kiválaszthatja az időzónát, és megadhatja a kezdő dátumot és időpontot is. <br>- Az **összes kijelölése**: használja az összes megadott feltételt. |
   | **Üzenetek száma** | A kötegben begyűjteni kívánt üzenetek száma, például 10 üzenet. A kötegek korlátja 8 000 üzenet. |
   | **Köteg mérete** | A kötegben összegyűjteni kívánt teljes méret (bájtban), például 10 MB. A kötegek maximális mérete 80 MB. |
   | **Ütemezés** | A kötegek kiadásainak időköze és gyakorisága, például 10 perc. A minimális ismétlődés 60 másodperc vagy 1 perc. A töredékes percek gyakorlatilag 1 percre vannak kerekítve. Az időzóna vagy a kezdő dátum és idő megadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a megfelelő tulajdonságokat. |
   |||

   > [!NOTE]
   >
   > Ha módosítja a kiadási feltételeket, miközben az trigger továbbra is kötegelt, de nem elküldött üzenetekkel rendelkezik, akkor az trigger a frissített kiadási feltételeket használja az el nem küldött üzenetek kezeléséhez.

   Ez a példa az összes feltételt mutatja, de a saját teszteléséhez próbáljon ki csak egy feltételt:

   ![Batch-trigger részleteinek megadása](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Most adjon hozzá egy vagy több olyan műveletet, amely feldolgozza az egyes kötegeket.

   Ebben a példában olyan műveletet adunk hozzá, amely e-mailt küld a Batch-eseményindító indításakor. Az trigger fut, és e-mailt küld, ha a köteg 10 üzenettel rendelkezik, 10 MB-nyi vagy 10 perces bérlet után.

   1. A Batch-trigger alatt válassza az **új lépés** lehetőséget.

   1. A keresőmezőbe írja be `send email` szűrőként a kifejezést. Az e-mail-szolgáltató alapján válasszon ki egy e-mail-összekötőt.

      Ha például munkahelyi vagy iskolai fiókja van, például @fabrikam.com a vagy a @fabrikam.onmicrosoft.com , válassza ki a **Microsoft 365 Outlook** -összekötőt. Ha személyes fiókkal (például @outlook.com vagy) rendelkezik, @hotmail.com válassza ki a **Outlook.com** -összekötőt. Ez a példa a Microsoft 365 Outlook Connectort használja.

   1. Válassza ki az "e-mail küldése" műveletet a szolgáltatónál, például:

      ![Az "e-mail küldése" művelet kiválasztása az e-mail-szolgáltatóhoz](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

1. Adja meg a hozzáadott művelet tulajdonságait.

   * A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét.

   * A **tulajdonos** mezőben a dinamikus tartalmak listájának megjelenésekor válassza a **partíció neve** mezőt.

     ![A dinamikus tartalom listából válassza a "partíció neve" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Később a Batch-feladóban megadhat egy egyedi partíciót, amely a cél köteget a logikai részhalmazokra osztja, ahol üzeneteket küldhet. Mindegyik készlethez tartozik egy egyedi szám, amelyet a Batch feladó logikai alkalmazás generál. Ez a funkció lehetővé teszi, hogy egyetlen köteget használjon több alkészlettel, és definiálja az egyes részhalmazokat az Ön által megadott névvel.

     > [!IMPORTANT]
     > A partíciók 5 000 vagy 80 MB-os korláttal rendelkeznek. Ha bármelyik feltétel teljesül, Logic Apps a köteget is kiszabadíthatja, még akkor is, ha a megadott kiadási feltétel nem teljesül.

   * A **törzs** mezőben, amikor megjelenik a dinamikus tartalom lista, válassza az **üzenet azonosítója** mezőt.

     A Logic app Designer automatikusan hozzáadja az e-mailek küldése művelethez **tartozó minden** hurokhoz, mert ez a művelet az előző művelet kimenetét kezeli gyűjteményként, nem pedig kötegként.

     ![A "Body" elemnél válassza az "üzenet azonosítója" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Mentse a logikai alkalmazást. Ezzel létrehozott egy batch-fogadót.

    ![A logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Ha a Visual studiót használja, mielőtt továbblépne a következő szakaszra, győződjön meg arról, hogy először a [Batch-fogadó logikai alkalmazást *helyezi üzembe* az Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)-ban. Ellenkező esetben a Batch-feladó létrehozásakor nem választhatja ki a Batch-fogadót.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Batch-feladó létrehozása

Most hozzon létre egy vagy több batch-küldő logikai alkalmazást, amely üzeneteket küld a Batch-fogadó logikai alkalmazásnak. Minden batch-feladóban meg kell adnia a Batch-fogadót és a köteg nevét, az üzenet tartalmát és az egyéb beállításokat. Megadhat egy egyedi partíciós kulcsot is, amellyel a köteget logikai részhalmazokra oszthatja, hogy üzeneteket gyűjtsön az adott kulccsal.

* Győződjön meg arról, hogy korábban [létrehozta és telepítette a Batch-fogadót](#batch-receiver) , így a Batch-feladó létrehozásakor kiválaszthatja a meglévő batch-fogadót célként megadott kötegként. Habár a Batch-fogadóknak semmit nem kell tudniuk a Batch-küldők számára, a kötegelt küldőknek tudniuk kell, hogy hová kell küldeniük az üzeneteket.

* Győződjön meg arról, hogy a Batch-fogadó és a Batch-küldő ugyanazzal az Azure-régióval *és Azure-* előfizetéssel rendelkezik. Ha nem, akkor nem választhatja ki a Batch-fogadót a Batch-feladó létrehozásakor, mert azok nem láthatók egymás számára.

1. Hozzon létre egy másik logikai alkalmazást a következő névvel: `BatchSender`

   1. A keresőmezőbe írja be `recurrence` szűrőként a kifejezést. Az eseményindítók listából válassza ki ezt az eseményindítót: **Ismétlődés**

      ![Az ismétlődési eseményindító hozzáadása](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Állítsa be az intervallumot és a gyakoriságot a küldő logikai alkalmazás percenkénti futtatásához.

      ![Ismétlődési eseményindító gyakoriságának és intervallumának megadása](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Új művelet hozzáadása az üzenetek kötegbe való küldéséhez.

   1. Az **ismétlődési** eseményindító alatt válassza az **új lépés** lehetőséget.

   1. A keresőmezőbe írja be szűrőként a kifejezést, `batch` majd válassza a következő műveletet: **válasszon Logic apps munkafolyamatot kötegelt triggerrel**

      ![Válassza a "Logic Apps munkafolyamat kiválasztása a Batch triggerrel" lehetőséget.](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      Megjelenik egy lista, amelyen csak azok a logikai alkalmazások jelennek meg, amelyek batch-eseményindítókkal rendelkeznek, és ugyanabban az Azure-régióban *és* Azure-előfizetésben találhatók, mint a Batch-küldő logikai alkalmazás.

   1. A Logic apps listából válassza ki a korábban létrehozott batch-fogadó logikai alkalmazást.

      ![A Batch-fogadó logikai alkalmazás kiválasztása](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Ha a Visual studiót használja, és nem látja a kiválasztható batch-fogadókat, ellenőrizze, hogy korábban létrehozta és telepítette-e a Batch-fogadót az Azure-ba. Ha még nem tette meg, Ismerje meg, [hogyan helyezheti üzembe a Batch-fogadó logikai alkalmazást az Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)-ban.

   1. A műveletek listából válassza a következő műveletet: **Batch_messages-<*a-Logic-app-Name* >**

      ![Válassza ki ezt a műveletet: "Batch_messages-<a-Logic-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. A Batch-feladó tulajdonságainak beállítása:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Köteg neve** | A fogadó logikai alkalmazás által definiált batch-név, amely `TestBatch` ebben a példában szerepel <p>**Fontos**: a Batch neve futásidőben lesz érvényesítve, és meg kell egyeznie a fogadó logikai alkalmazás által megadott névvel. A naplólap nevének módosítása hatására a Batch-feladó meghibásodik. |
   | **Üzenet tartalma** | Az elküldeni kívánt üzenet tartalma |
   |||

   > [!NOTE]
   > A rendszer automatikusan kitölti az **trigger neve** és a **munkafolyamat** tulajdonság értékét a kiválasztott logikai alkalmazásból.

   Ebben a példában adja hozzá ezt a kifejezést, amely beszúrja az aktuális dátumot és időpontot a kötegbe küldött üzenet tartalmába:

   1. Kattintson az **üzenet tartalma** mezőre.

   1. Amikor megjelenik a dinamikus tartalom lista, válassza a **kifejezés** lehetőséget.

   1. Adja meg a kifejezést `utcnow()` , majd kattintson **az OK gombra**.

      ![Az "üzenet tartalma" területen válassza a "kifejezés" értéket, írja be a "utcnow ()" kifejezést, majd kattintson az "OK" gombra.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Most állítson be egy partíciót a köteghez. A `BatchReceiver` műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a következő tulajdonságokat:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Partíció neve** | Opcionális egyedi partíciós kulcs, amely a cél köteg logikai részhalmazokra osztására és az üzenetek az adott kulcs alapján történő összegyűjtésére használható. |
   | **Üzenet azonosítója** | Egy opcionális, globálisan egyedi azonosítót (GUID) tartalmazó üzenet, amely üres |
   |||

   Ebben a példában a **partíció neve** mezőben adjon meg egy olyan kifejezést, amely véletlenszerűen generált számot hoz létre egy és öt között. Hagyja üresen az **üzenet-azonosító** mezőt.

   1. Kattintson a **partíció neve** mezőre, hogy megjelenjen a dinamikus tartalmak listája.

   1. A dinamikus tartalom listában válassza a **kifejezés** lehetőséget.

   1. Adja meg a kifejezést `rand(1,6)` , majd kattintson **az OK gombra**.

      ![Partíció beállítása a cél köteghez](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Ez a **Rand** függvény egy számot hoz létre egy és öt között. Így a köteget öt számú partícióra osztja, amelyek ezt a kifejezést dinamikusan határozzák meg.

1. Mentse a logikai alkalmazást. A küldő logikai alkalmazás most az alábbi példához hasonlóan néz ki:

   ![A küldő logikai alkalmazás mentése](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logikai alkalmazások tesztelése

A batching-megoldás teszteléséhez a logikai alkalmazásait néhány percen belül el kell hagyni. Hamarosan megkezdi az e-mailek lekérését az öt, mind ugyanazzal a partíciós kulccsal rendelkező csoportokba.

A Batch-küldő logikai alkalmazás percenként fut, és egy véletlenszerűen generált számot hoz létre egy és öt között, és ezt a generált számot használja a cél köteghez tartozó partíciós kulcsként, ahol az üzenetek küldése történik. Minden alkalommal, amikor a kötegnek öt eleme van ugyanazzal a partíciós kulccsal, a Batch-fogadó logikai alkalmazás elindít minden üzenetet, és e-mailt küld.

> [!IMPORTANT]
> Ha végzett a teszteléssel, győződjön meg arról, hogy letiltja a `BatchSender` logikai alkalmazást az üzenetek küldésének leállításához és a beérkezett fájlok túlterhelésének elkerüléséhez.

## <a name="next-steps"></a>Következő lépések

* [EDI-üzenetek kötegelése és küldése](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [A Logic app-definíciók létrehozása JSON használatával](../logic-apps/logic-apps-author-definitions.md)
* [Kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban Azure Logic Apps és függvények](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Kivételek és hibák naplózása a Logic apps esetében](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
