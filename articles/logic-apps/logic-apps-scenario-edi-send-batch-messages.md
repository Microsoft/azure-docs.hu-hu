---
title: Kötegelt feldolgozás – EDI-üzenetek csoportként
description: EDI-üzenetek küldése és fogadása kötegként, csoportként vagy gyűjteményekként a kötegelt feldolgozás használatával Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000402"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Exchange EDI-üzenetek kötegként vagy a kereskedelmi partnerek közötti csoportok között Azure Logic Apps

A vállalati és üzleti (B2B) forgatókönyvekben a partnerek gyakran üzeneteket cserélnek a csoportokban vagy *kötegekben*. Ha Logic Apps használatával hoz létre batch-megoldást, üzeneteket küldhet a kereskedelmi partnereknek, és feldolgozhatja ezeket az üzeneteket kötegekben. Ebből a cikkből megtudhatja, hogyan dolgozza fel az EDI-üzeneteket az X12 használatával példaként egy "batch-feladó" logikai alkalmazás és egy "batch-fogadó" logikai alkalmazás létrehozásával. 

A kötegelt X12-üzenetek ugyanúgy működnek, mint az egyéb üzenetek. olyan batch-triggert használ, amely üzeneteket gyűjt egy kötegbe és egy batch-műveletet, amely üzeneteket küld a kötegnek. Emellett a X12 batching tartalmaz egy X12 Kódolási lépést, mielőtt az üzenetek a kereskedelmi partnerhez vagy más célhoz mennek. További információ a Batch-triggerről és a műveletről: [kötegelt feldolgozás üzenetei](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

Ebben a cikkben két logikai alkalmazást hoz létre ugyanazon Azure-előfizetésben, az Azure-régióban, és ezt a konkrét sorrendet követve:

* Egy ["batch-fogadó"](#receiver) logikai alkalmazás, amely az üzeneteket egy kötegbe fogadja és gyűjti, amíg a megadott feltételek teljesülnek az üzenetek felszabadításához és feldolgozásához. Ebben az esetben a Batch-fogadó a kötegben lévő üzeneteket a megadott X12-szerződés vagy partner identitások használatával is kódolja.

  Győződjön meg arról, hogy először létrehozza a Batch-fogadót, hogy később kiválassza a Batch-célt a Batch-feladó létrehozásakor.

* Egy ["batch Sender"](#sender) logikai alkalmazás, amely az üzeneteket a korábban létrehozott batch-fogadónak küldi el. 

Győződjön meg arról, hogy a Batch-fogadó és a Batch-küldő ugyanazzal az Azure-előfizetéssel *és Azure-* régióval rendelkezik. Ha nem, akkor nem választhatja ki a Batch-fogadót a Batch-feladó létrehozásakor, mert azok nem láthatók egymás számára.

## <a name="prerequisites"></a>Előfeltételek

A példához a következő elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdheti egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Vagy regisztráljon az utólagos elszámolású [előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Egy meglévő, Azure-előfizetéshez társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely a logikai alkalmazásokhoz van társítva

* Legalább két meglévő [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) az integrációs fiókban. Minden partnernek a X12 (standard szintű szolgáltató Alpha Code) a partner tulajdonságaiban kell használnia az üzleti identitásként.

* Egy meglévő [X12-szerződés](../logic-apps/logic-apps-enterprise-integration-x12.md) az integrációs fiókban

* Ha a Visual studiót a Azure Portal helyett szeretné használni, győződjön meg róla, hogy a [Visual studiót a Logic apps használatára állítja be](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12 batch-fogadó létrehozása

Ahhoz, hogy üzeneteket küldhessen egy kötegbe, a kötegnek először az üzeneteket küldő célhelyként kell lennie. Először létre kell hoznia a "batch-fogadó" logikai alkalmazást, amely a **Batch** -triggerrel kezdődik. Így a "batch Sender" logikai alkalmazás létrehozásakor kiválaszthatja a Batch-fogadó logikai alkalmazást. A Batch-fogadó folytatja az üzenetek gyűjtését, amíg a megadott feltételek teljesülnek az üzenetek felszabadításához és feldolgozásához. Habár a Batch-fogadóknak semmit nem kell tudniuk a Batch-küldők számára, a Batch-küldőknek ismerniük kell az üzeneteket küldő célhelyet. 

Ehhez a Batch-fogadóhoz meg kell adnia a Batch-módot, a nevet, a kiadási feltételeket, a X12-szerződést és az egyéb beállításokat. 

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy logikai alkalmazást a következő névvel: "BatchX12Messages"

2. [Kapcsolja össze a logikai alkalmazást az integrációs fiókkal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. A Logic Apps Designerben adja hozzá a **Batch** -triggert, amely elindítja a logikai alkalmazás munkafolyamatát. A keresőmezőbe írja be szűrőként a "batch" kifejezést. Válassza ki ezt az triggert: **Batch-üzenetek**

   ![Batch-trigger hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. A Batch-fogadó tulajdonságainak beállítása: 

   | Tulajdonság | Érték | Jegyzetek | 
   |----------|-------|-------|
   | **Batch mód** | Beágyazott |  |  
   | **Köteg neve** | TestBatch | Csak **beágyazott** batch-módban érhető el | 
   | **Kiadási feltételek** | Üzenetek száma alapján, ütemterv alapján | Csak **beágyazott** batch-módban érhető el | 
   | **Üzenetek száma** | 10 | Csak az **üzenetek számán alapuló** kiadási feltételekkel érhető el | 
   | **Intervallum** | 10 | Csak a **Schedule-alapú** kiadási feltételekkel érhető el | 
   | **Gyakoriság** | perc | Csak a **Schedule-alapú** kiadási feltételekkel érhető el | 
   ||| 

   ![Batch-trigger részleteinek megadása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Ez a példa nem állít be partíciót a köteghez, így minden köteg ugyanazt a partíciós kulcsot használja. A partíciókkal kapcsolatos további tudnivalókért tekintse meg a [Batch-folyamatok üzenetei](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)című témakört.

5. Most adjon hozzá egy műveletet, amely kódolja az egyes kötegeket: 

   1. A Batch-trigger alatt válassza az **új lépés** lehetőséget.

   2. A keresőmezőbe írja be szűrőként a "X12 batch" kifejezést, majd válassza ezt a műveletet (bármely verzió): **Batch kódolás <*verzió*>-X12** 

      ![X12 batch-kódolási művelet kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Ha korábban nem kapcsolódott az integrációs fiókjához, hozza létre most a kapcsolatot. Adja meg a csatlakozás nevét, válassza ki a kívánt integrációs fiókot, majd válassza a **Létrehozás** lehetőséget.

      ![Kapcsolat létrehozása a Batch Encoder és az integrációs fiók között](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Állítsa be ezeket a tulajdonságokat a Batch Encoder művelethez:

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **X12-szerződés neve** | Nyissa meg a listát, és válassza ki a meglévő szerződést. <p>Ha a lista üres, ügyeljen arra, hogy a logikai alkalmazást a kívánt szerződéssel rendelkező [integrációs fiókhoz kapcsolja](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) . | 
      | **BatchName** | Kattintson a mezőre, majd a dinamikus tartalom lista megjelenése után válassza ki a **Batch Name** tokent. | 
      | **PartitionName** | Kattintson a mezőbe, majd a dinamikus tartalom lista megjelenése után válassza ki a **partíció neve** tokent. | 
      | **Elemek** | Zárjuk be az elem részleteit tartalmazó mezőt, majd kattintson a mező belsejébe. A dinamikus tartalom lista megjelenése után válassza ki a **kötegelt elemek** tokent. | 
      ||| 

      ![Batch-kódolási művelet részletei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Az **elemek** mezőben:

      ![Batch-kódolás műveleti elemei](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Mentse a logikai alkalmazást. 

7. Ha a Visual studiót használja, győződjön meg arról, hogy [üzembe helyezi a Batch-fogadó logikai alkalmazást az Azure-](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)ban. Ellenkező esetben a Batch-feladó létrehozásakor nem választhatja ki a Batch-fogadót.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a Batch-fogadó a várt módon működik-e, hozzáadhat egy HTTP-műveletet tesztelési célokra, és kötegelt üzenetet küldhet a [kérelem-raktárhely szolgáltatásnak](https://requestbin.com/). 

1. Az X12 kódolása művelet alatt válassza az **új lépés** lehetőséget. 

2. A keresőmezőbe írja be szűrőként a "http" kifejezést. Válassza ki ezt a műveletet: **http-http**
    
   ![HTTP-művelet kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Adja meg a HTTP-művelet tulajdonságait:

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Metódus** | A listából válassza a **post** lehetőséget. | 
   | **URI** | Adjon meg egy URI-t a kérelem-raktárhelyhez, majd adja meg az URI-t ebben a mezőben. | 
   | **Törzs** | Kattintson a mezőre, majd a dinamikus tartalom lista megnyitása után válassza ki a **törzs** tokent, amely a szakasz, a **Batch kódolása a szerződés neve szerint** elemnél jelenik meg. <p>Ha nem látja a **törzs** jogkivonatát, a **Batch általi kódolás** mellett válassza a **továbbiak** lehetőséget. | 
   ||| 

   ![HTTP-művelet részleteinek megadása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Mentse a logikai alkalmazást. 

   A Batch-fogadó logikai alkalmazás a következő példához hasonlóan néz ki: 

   ![A Batch-fogadó logikai alkalmazás mentése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12 batch-feladó létrehozása

Most hozzon létre egy vagy több olyan logikai alkalmazást, amely üzeneteket küld a Batch-fogadó logikai alkalmazásnak. Minden batch-feladóban meg kell adnia a Batch-fogadó logikai alkalmazást és a köteg nevét, az üzenet tartalmát és az egyéb beállításokat. Megadhat egy egyedi partíciós kulcsot is, amely a köteget részhalmazokra osztja, hogy üzeneteket gyűjtsön az adott kulccsal. 

* Győződjön meg arról, hogy már [létrehozta a Batch-fogadót](#receiver) , így a Batch-feladó létrehozásakor kiválaszthatja a meglévő batch-fogadót célként megadott kötegként. Habár a Batch-fogadóknak semmit nem kell tudniuk a Batch-küldők számára, a kötegelt küldőknek tudniuk kell, hogy hová kell küldeniük az üzeneteket. 

* Győződjön meg arról, hogy a Batch-fogadó és a Batch-küldő ugyanazzal az Azure-régióval *és Azure-* előfizetéssel rendelkezik. Ha nem, akkor nem választhatja ki a Batch-fogadót a Batch-feladó létrehozásakor, mert azok nem láthatók egymás számára.

1. Hozzon létre egy másik logikai alkalmazást a következő névvel: "SendX12MessagesToBatch" 

2. A keresőmezőbe írja be a "http-kérelem" kifejezést a szűrőként. Válassza ki ezt az triggert: **http-kérés fogadásakor** 
   
   ![A kérelem-trigger hozzáadása](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Művelet hozzáadása az üzenetek kötegbe küldéséhez.

   1. A HTTP-kérés művelet alatt válassza az **új lépés** lehetőséget.

   2. A keresőmezőbe írja be szűrőként a "batch" kifejezést. 
   Válassza ki a **műveletek** listát, majd válassza ki ezt a műveletet: **válasszon Logic apps munkafolyamatot batch triggerrel – üzenetek küldése kötegbe**

      ![Válassza a "Logic Apps munkafolyamat kiválasztása a Batch triggerrel" lehetőséget.](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Most válassza ki a korábban létrehozott "BatchX12Messages" logikai alkalmazást.

      ![A "batch receiver" logikai alkalmazás kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Válassza ki ezt a műveletet: **Batch_messages-<*a-Batch-fogadót* >**

      !["Batch_messages" művelet kiválasztása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. A Batch-feladó tulajdonságainak beállítása

   | Tulajdonság | Leírás | 
   |----------|-------------| 
   | **Köteg neve** | A fogadó logikai alkalmazás által definiált batch-név, amely ebben a példában a "TestBatch". <p>**Fontos**: a Batch neve futásidőben lesz érvényesítve, és meg kell egyeznie a fogadó logikai alkalmazás által megadott névvel. A naplólap nevének módosítása hatására a Batch-feladó meghibásodik. | 
   | **Üzenet tartalma** | Az elküldeni kívánt üzenet tartalma, amely ebben a példában a **szövegtörzs** tokenje | 
   ||| 
   
   ![Köteg tulajdonságainak beállítása](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Mentse a logikai alkalmazást. 

   A Batch-küldő logikai alkalmazás a következő példához hasonlóan néz ki:

   ![A Batch-feladó logikai alkalmazásának mentése](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>A logikai alkalmazások tesztelése

A batching-megoldás teszteléséhez tegye közzé az X12 üzeneteket a [Poster](https://www.getpostman.com/postman) vagy hasonló eszköz használatával a Batch-küldő logikai alkalmazásban. Hamarosan megkezdheti a X12-üzenetek lekérését a kérelem-tárolóban 10 percenként vagy 10 kötegben, mindezt ugyanazzal a partíciós kulccsal.

## <a name="next-steps"></a>Következő lépések

* [Üzenetek feldolgozása kötegként](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
