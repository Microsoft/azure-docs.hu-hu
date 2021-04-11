---
title: Várjon, és válaszoljon az eseményekre
description: Automatizálhatja, szüneteltetheti és folytathatja a munkafolyamatokat egy szolgáltatási végpont eseményei alapján a Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: cdbf853a96f319cb27c10136004a1398014e602f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109152"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatizált eseményvezérelt munkafolyamatok létrehozása és futtatása HTTP-webhookok használatával Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http webhook-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek előfizethetnek egy szolgáltatás-végpontra, megvárhatják az adott eseményeket, és ezek alapján futtathatják ezeket az eseményeket, nem pedig rendszeresen ellenőrizni vagy *lekérdezni* a végpontot.

Néhány példa webhook-alapú munkafolyamatra:

* Várjon, amíg egy elem egy [Azure Event hub](https://github.com/logicappsio/EventHubAPI) -ból érkezik a logikai alkalmazás futtatásának elindítása előtt.
* Várjon egy jóváhagyást, mielőtt folytatná a munkafolyamatot.

Ez a cikk bemutatja, hogyan használható a webhook-trigger és a webhook művelet, hogy a logikai alkalmazás fogadni tudjon és reagáljon a szolgáltatás-végpont eseményeire.

## <a name="how-do-webhooks-work"></a>Hogyan működnek a webhookok?

A webhook eseményindító esemény-alapú, amely nem függ az új elemek ellenőrzésének vagy lekérdezésének. Ha olyan logikai alkalmazást ment, amely egy webhook-triggerrel kezdődik, vagy ha a logikai alkalmazást Letiltottról Engedélyezettre módosítja, akkor a webhook-trigger *előfizet* a megadott szolgáltatási végpontra egy *visszahívási URL-cím* az adott végponttal való regisztrálásával. Az trigger ezután megvárja, amíg a szolgáltatási végpont meghívja az URL-címet, amely elindítja a logikai alkalmazás futtatását. A [kérelem eseményindítóhoz](connectors-native-reqres.md)hasonlóan a logikai alkalmazás azonnal aktiválódik, ha a megadott esemény történik. A webhook elindítja a szolgáltatás végpontjának *lemondását* , ha eltávolítja az triggert, és menti a logikai alkalmazást, vagy ha a logikai alkalmazást letiltottra módosítja.

Egy webhook művelet is eseményvezérelt, és *előfizet* a megadott szolgáltatási végpontra egy *visszahívási URL-cím* az adott végponttal való regisztrálásával. A webhook művelet szünetelteti a logikai alkalmazás munkafolyamatát, és megvárja, amíg a szolgáltatási végpont meghívja az URL-címet, mielőtt a logikai alkalmazás folytatja a futtatást. A webhook művelet *lemond* a szolgáltatási végpontról a következő esetekben:

* A webhook művelet sikeres befejeződése után
* Ha a logikai alkalmazás futtatása megszakad a válaszra való várakozás közben.
* A logikai alkalmazás időtúllépése előtt

Például az Office 365 Outlook Connector [**Küldés-jóváhagyási e-mail-**](connectors-create-api-office365-outlook.md) művelete egy példa a webhook műveletre, amely ezt a mintát követi. Ezt a mintát bármely szolgáltatásra kiterjesztheti a webhook művelet használatával.

További információt az alábbi témakörökben talál:

* [Webhookok és előfizetések](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Webhookot támogató egyéni API-k létrehozása](../logic-apps/logic-apps-create-api-app.md)

A logikai alkalmazáshoz (például [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), korábbi nevén SSL (SSL) vagy [Azure Active Directory nyílt hitelesítéshez (Azure ad OAuth)](../active-directory/develop/index.yml)való bejövő hívások titkosításával, biztonságával és engedélyezésével kapcsolatos információkért lásd: [biztonságos hozzáférés és adathozzáférés a kérelmeken alapuló eseményindítók felé irányuló bejövő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy már üzembe helyezett végpont vagy API URL-címe, amely támogatja a [webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) előfizetését és a leiratkozási mintát a Logic apps vagy [webhook műveletekben a logikai alkalmazásokban](../logic-apps/logic-apps-create-api-app.md#webhook-actions) a megfelelő módon.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* Az a logikai alkalmazás, amelyben meg szeretné várni a konkrét eseményeket a célként megadott végponton. Ha a HTTP webhook-triggerrel szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP webhook művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

## <a name="add-an-http-webhook-trigger"></a>HTTP webhook-trigger hozzáadása

Ez a beépített trigger meghívja az előfizetési végpontot a célkiszolgálón, és regisztrálja a visszahívási URL-címet a célként megadott szolgáltatásban. A logikai alkalmazás ezután megvárja, amíg a TARGET szolgáltatás elküld egy `HTTP POST` kérést a visszahívás URL-címére. Ha ez az esemény bekövetkezik, az eseményindító elindít és átadja a kérésben szereplő összes adatfeldolgozást a munkafolyamatnak.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervező keresési mezőjében adja meg `http webhook` a szűrőt. Az **Eseményindítók** listából válassza ki a **http webhook** eseményindítót.

   ![HTTP webhook-trigger kiválasztása](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Ez a példa átnevezi a triggert `HTTP Webhook trigger` úgy, hogy a lépésnek legyen egy leíró neve. A példa később egy HTTP webhook-műveletet is felvesz, és mindkét névnek egyedinek kell lennie.

1. Adja meg az előfizetési és leiratkozási hívásokhoz használni kívánt [http webhook-trigger paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) értékeit.

   Ebben a példában az trigger az előfizetés és a leiratkozási műveletek végrehajtásakor használandó metódusokat, URI-ket és üzenettörzs-törzseket tartalmazza.

   ![HTTP webhook-trigger paramétereinek megadása](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Előfizetés – metódus** | Yes | A célként megadott végpontra való feliratkozáskor használandó módszer |
   | **Előfizetés – URI** | Yes | A célként megadott végpontra való feliratkozáshoz használandó URL-cím |
   | **Előfizetés – törzs** | No | Az előfizetési kérelembe belefoglalni kívánt üzenettörzs. Ez a példa tartalmazza azt a visszahívási URL-címet, amely egyedileg azonosítja az előfizetőt, amely a logikai alkalmazás, a kifejezés használatával a `@listCallbackUrl()` logikai alkalmazás visszahívási URL-címének lekéréséhez. |
   | **Leiratkozás – metódus** | No | A cél végpontról való leiratkozáskor használandó módszer |
   | **Leiratkozás – URI** | No | A cél végpontról való leiratkozáshoz használandó URL-cím |
   | **Leiratkozás – törzs** | No | A leiratkozási kérelembe belefoglalni kívánt üzenettörzs <p><p>**Megjegyzés**: Ez a tulajdonság nem támogatja a `listCallbackUrl()` függvény használatát. Az trigger azonban automatikusan magában foglalja és elküldi a fejléceket, `x-ms-client-tracking-id` és `x-ms-workflow-operation-name` azt is, hogy a célként megadott szolgáltatás az előfizető egyedi azonosítására alkalmas legyen. |
   ||||

1. További trigger-tulajdonságok hozzáadásához nyissa meg az **új paraméterek hozzáadása** listát.

   ![További trigger-tulajdonságok hozzáadása](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Ha például hitelesítést kell használnia, hozzáadhatja az **előfizetés-hitelesítés** és a **leiratkozás – hitelesítés** tulajdonságokat. További információ a HTTP webhookhoz elérhető hitelesítési típusokról: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

   A logikai alkalmazás mentése meghívja az előfizetés végpontot a cél szolgáltatásban, és regisztrálja a visszahívási URL-címet. A logikai alkalmazás ezután megvárja, amíg a TARGET szolgáltatás elküld egy `HTTP POST` kérést a visszahívás URL-címére. Ha ez az esemény bekövetkezik, az eseményindító elindít és átadja a kérésben szereplő összes adatfeldolgozást a munkafolyamatnak. Ha a művelet sikeresen befejeződik, az trigger lemond a végpontról, és a logikai alkalmazás folytatja a hátralévő munkafolyamatot.

## <a name="add-an-http-webhook-action"></a>HTTP-webhook művelet hozzáadása

Ez a beépített művelet meghívja az előfizetési végpontot a célhelyen, és regisztrálja a visszahívási URL-címet a célként megadott szolgáltatásban. A logikai alkalmazás ezután szünetelteti és megvárja, amíg a TARGET szolgáltatás egy `HTTP POST` kérést küld a visszahívási URL-címre. Ha ez az esemény bekövetkezik, a művelet a kérelemben szereplő összes adattal együtt átadja a munkafolyamatot. Ha a művelet sikeresen befejeződik, a művelet lemond a végpontról, és a logikai alkalmazás továbbra is futtatja a hátralévő munkafolyamatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP webhook triggert használja első lépésként.

1. Válassza ki azt a lépést, amelyhez hozzá szeretné adni a HTTP webhook műveletet, majd kattintson az **új lépés** gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A tervező keresési mezőjében adja meg `http webhook` a szűrőt. A **műveletek** listából válassza ki a **http webhook** műveletet.

   ![HTTP-webhook művelet kiválasztása](./media/connectors-native-webhook/select-http-webhook-action.png)

   Ez a példa átnevezi a műveletet "HTTP webhook művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg az előfizetési és leiratkozási hívásokhoz használni kívánt http-webhook-paraméterek értékeit, amelyek az előfizetéshez és az előfizetés-visszaíráshoz használt [paraméterekhez](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)hasonlóak.

   Ebben a példában a művelet az előfizetés és a leiratkozási műveletek végrehajtásakor használandó metódusokat, URI-ket és üzenettörzs-törzseket tartalmazza.

   ![Adja meg a HTTP-webhook műveleti paramétereit](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Előfizetés – metódus** | Yes | A célként megadott végpontra való feliratkozáskor használandó módszer |
   | **Előfizetés – URI** | Yes | A célként megadott végpontra való feliratkozáshoz használandó URL-cím |
   | **Előfizetés – törzs** | No | Az előfizetési kérelembe belefoglalni kívánt üzenettörzs. Ez a példa tartalmazza azt a visszahívási URL-címet, amely egyedileg azonosítja az előfizetőt, amely a logikai alkalmazás, a kifejezés használatával a `@listCallbackUrl()` logikai alkalmazás visszahívási URL-címének lekéréséhez. |
   | **Leiratkozás – metódus** | No | A cél végpontról való leiratkozáskor használandó módszer |
   | **Leiratkozás – URI** | No | A cél végpontról való leiratkozáshoz használandó URL-cím |
   | **Leiratkozás – törzs** | No | A leiratkozási kérelembe belefoglalni kívánt üzenettörzs <p><p>**Megjegyzés**: Ez a tulajdonság nem támogatja a `listCallbackUrl()` függvény használatát. A művelet azonban automatikusan belefoglalja és elküldi a fejléceket, `x-ms-client-tracking-id` és `x-ms-workflow-operation-name` azt, hogy a célként megadott szolgáltatás mely segítségével egyedileg azonosíthatja az előfizetőt. |
   ||||

1. További művelet-tulajdonságok hozzáadásához nyissa meg az **új paraméterek hozzáadása** listát.

   ![További művelet tulajdonságainak hozzáadása](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Ha például hitelesítést kell használnia, hozzáadhatja az **előfizetés-hitelesítés** és a **leiratkozás – hitelesítés** tulajdonságokat. További információ a HTTP webhookhoz elérhető hitelesítési típusokról: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

   A művelet futtatásakor a logikai alkalmazás meghívja az előfizetési végpontot a cél szolgáltatásban, és regisztrálja a visszahívási URL-címet. Ezután a logikai alkalmazás szünetelteti a munkafolyamatot, és megvárja, amíg a TARGET szolgáltatás elküld egy `HTTP POST` kérést a visszahívás URL-címére. Ha ez az esemény bekövetkezik, a művelet a kérelemben szereplő összes adattal együtt átadja a munkafolyamatot. Ha a művelet sikeresen befejeződik, a művelet lemond a végpontról, és a logikai alkalmazás továbbra is futtatja a hátralévő munkafolyamatot.

## <a name="trigger-and-action-outputs"></a>Trigger-és műveleti kimenetek

Itt talál további információt egy HTTP webhook-triggerből vagy-műveletből származó kimenetről, amely a következő információkat adja vissza:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| fejlécek | object | A kérelemben szereplő fejlécek |
| body (Törzs) | object | A kérelem szövegtörzsét tartalmazó objektum |
| állapotkód | int | A kérelemben szereplő állapotkód |
||||

| Állapotkód | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérelem |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="connector-reference"></a>Összekötő-referencia

További információ az aktiválási és műveleti paraméterekről, amelyek hasonlóak egymáshoz: [http webhook paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

## <a name="next-steps"></a>Következő lépések

* [Biztonságos hozzáférés és adathozzáférés a beérkező hívások számára a kérelmeken alapuló eseményindítók számára](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)
