---
title: Kapcsolódás Bing Searchhoz
description: Automatizálja a feladatokat és munkafolyamatokat, amelyek megkeresik az eredményeket a Bing Search Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 306298e4338665ef52add7f46d6da8675c97c3e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716550"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Találatok keresése a Bing Searchban Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan találhatja meg a híreket, videókat és egyéb elemeket a Bing Search egy logikai alkalmazáson belül a Bing Search-összekötő használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a feladatokat és munkafolyamatokat a keresési eredmények feldolgozásához, és elérhetővé teszik ezeket az elemeket más műveletekhez. 

Például a keresési feltételek alapján megtalálhatja a híreket, és Twitter-hírcsatornában teheti közzé ezeket az elemeket tweetként.

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.
Az összekötő-specifikus technikai információk az Bing Search- [összekötő referenciájában](/connectors/bingsearch/)olvashatók.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Cognitive Services fiók](../cognitive-services/cognitive-services-apis-create-account.md)

* Egy [BING Search API-kulcs](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), amely hozzáférést biztosít a logikai alkalmazástól a Bing Search API-k

* Az a logikai alkalmazás, amelyhez el szeretné érni az Event hub-t. Ha Bing Search triggerrel szeretné elindítani a logikai alkalmazást, [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szüksége.

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Bing Search trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#logic-app-concepts)kell kezdődnie, amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

1. A Azure Portal vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert. Ez a példa a Azure Portal használja.

2. A keresőmezőbe írja be szűrőként a "Bing Search" kifejezést. Az eseményindítók listából válassza ki a kívánt eseményindítót.

   Ez a példa a következő triggert használja: **Bing Search-on New News cikk**

   ![Bing Search trigger keresése](./media/connectors-create-api-bing-search/add-trigger.png)

3. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a Bing Search-kapcsolatát](#create-connection).
Ha már létezik a kapcsolatai, adja meg a szükséges információkat az triggerhez.

   Ebben a példában megadhatja, hogy a rendszer hogyan adja vissza a megfelelő híreket a Bing Search.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | Keresési lekérdezés | Igen | <*Keresés – szavak*> | Adja meg a használni kívánt keresési kulcsszavakat. |
   | Piaci | Igen | <*területi beállítás*> | A keresés területi beállítása. Az alapértelmezett érték az "en-US", de választhat egy másik értéket is. |
   | Biztonságos Keresés | Igen | <*keresési szint*> | A felnőtt tartalom kizárásának szűrési szintje. Az alapértelmezett érték a "mérsékelt", de egy másik szint van kiválasztva. |
   | Darabszám | Nem | <*eredmények – darabszám*> | A megadott számú eredmény visszaadása. Az alapértelmezett érték 20, de egy másik értéket is megadhat. A visszaadott eredmények tényleges száma a megadott számnál kisebb lehet. |
   | Eltolás | Nem | <*Kihagyás – érték*> | A kihagyni kívánt eredmények száma az eredmények visszaadása előtt |
   |||||

   Például:

   ![Trigger beállítása](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Válassza ki az intervallumot és a gyakoriságot, hogy milyen gyakran kívánja a triggert az eredmények keresésére.

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

6. Most folytassa a logikai alkalmazáshoz egy vagy több művelet hozzáadását azokkal a feladatokkal, amelyeket el szeretne végezni az trigger eredményeivel.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Bing Search művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás egy Bing Search triggerrel kezdődik, amely a megadott feltételeknek megfelelő híreket ad vissza.

1. A Azure Portal vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben. Ez a példa a Azure Portal használja.

2. Az Indítás vagy művelet alatt válassza az **új lépés**  >  **művelet hozzáadása** lehetőséget.

   Ez a példa a következő triggert használja:

   **Bing Search – új hírekre vonatkozó cikk**

   ![Művelet hozzáadása](./media/connectors-create-api-bing-search/add-action.png)

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

3. A keresőmezőbe írja be szűrőként a "Bing Search" kifejezést.
A műveletek listából válassza ki a kívánt műveletet.

   Ez a példa a következő műveletet használja:

   **Bing Search-Hírek listázása lekérdezés alapján**

   ![Bing Search művelet keresése](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a Bing Search-kapcsolatát](#create-connection). Ha a kapcsolat már létezik, adja meg a szükséges információkat a művelethez.

   Ebben a példában adja meg az trigger eredményeinek egy részhalmazának visszaküldési feltételeit.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | Keresési lekérdezés | Igen | <*Keresés – kifejezés*> | Adjon meg egy kifejezést az trigger eredményeinek lekérdezéséhez. A dinamikus tartalom lista mezői közül választhat, vagy létrehozhat egy kifejezést a Kifejezésszerkesztő használatával. |
   | Piaci | Igen | <*területi beállítás*> | A keresés területi beállítása. Az alapértelmezett érték az "en-US", de választhat egy másik értéket is. |
   | Biztonságos Keresés | Igen | <*keresési szint*> | A felnőtt tartalom kizárásának szűrési szintje. Az alapértelmezett érték a "mérsékelt", de egy másik szint van kiválasztva. |
   | Darabszám | Nem | <*eredmények – darabszám*> | A megadott számú eredmény visszaadása. Az alapértelmezett érték 20, de egy másik értéket is megadhat. A visszaadott eredmények tényleges száma a megadott számnál kisebb lehet. |
   | Eltolás | Nem | <*Kihagyás – érték*> | A kihagyni kívánt eredmények száma az eredmények visszaadása előtt |
   |||||

   Tegyük fel például, hogy azokat az eredményeket szeretné használni, amelyek kategóriájának neve tartalmazza a "Tech" szót.

   1. Kattintson a **keresési lekérdezés** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. 
   A listából válassza a **kifejezés** lehetőséget, hogy a Kifejezésszerkesztő megjelenjen. 

      ![Bing Search trigger](./media/connectors-create-api-bing-search/bing-search-action.png)

      Most már elkezdheti a kifejezés létrehozását.

   2. A függvények listából válassza a **tartalmaz ()** függvényt, amely ezután megjelenik a kifejezés mezőben. Kattintson a **dinamikus tartalom** lehetőségre, hogy a mezőlista újra megjelenjen, de győződjön meg arról, hogy a kurzor a zárójelben marad.

      ![Függvény kiválasztása](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. A mezőlista listából válassza ki a **kategóriát**, amely egy paraméterre konvertál. 
   Adjon hozzá egy vesszőt az első paraméter után, és a vessző után adja hozzá ezt a szót: `'tech'` 

      ![Válasszon ki egy mezőt](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Amikor elkészült, válassza az **OK** lehetőséget.

      A kifejezés most megjelenik a **keresési lekérdezés** mezőben a következő formátumban:

      ![Befejezett kifejezés](./media/connectors-create-api-bing-search/resolved-expression.png)

      A kód nézetben ez a kifejezés a következő formátumban jelenik meg:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Kapcsolódás Bing Searchhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Ha a rendszer a kapcsolódási adatok megadását kéri, adja meg a következő adatokat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | Kapcsolat neve | Igen | <*kapcsolattípus*> | A kapcsolódáshoz létrehozandó név |
   | API-verzió | Igen | <*API-Version*> | Alapértelmezés szerint a Bing Search API verziója az aktuális verzióra van beállítva. Szükség szerint a korábbi verziót is kiválaszthatja. |
   | API-kulcs | Igen | <*API-kulcs*> | A korábban kapott Bing Search API-kulcs. Ha nem rendelkezik kulccsal, szerezze be az [API-kulcsot](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Például:

   ![Kapcsolat létrehozása](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Ha elkészült, válassza a **Létrehozás** lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő hencegő fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/bingsearch/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

