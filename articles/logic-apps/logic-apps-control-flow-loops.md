---
title: Hurkok hozzáadása az Ismétlési műveletekhez
description: Munkafolyamatok tevékenységeit ismétlő vagy tömböket feldolgozó hurkok létrehozása az Azure Logic Appsben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93358456"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Munkafolyamatok tevékenységeit ismétlő vagy tömböket feldolgozó hurkok létrehozása az Azure Logic Appsben

Egy tömb a logikai alkalmazásban való feldolgozásához létrehozhat egy ["foreach" ciklust](#foreach-loop). Ez a hurok megismétli a tömb egyes elemeinek egy vagy több műveletét. A "foreach" hurok által feldolgozható tömbök számának korlátozását lásd: [párhuzamosságok, ismétlések és letételi korlátok](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Ha meg szeretné ismételni a műveleteket, amíg egy feltétel nem teljesül, vagy egy állapot változik, létrehozhat egy ["ig" ciklust](#until-loop). A logikai alkalmazás először a hurokon belüli összes műveletet futtatja, majd ellenőrzi a feltételt vagy az állapotot. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok megismétlődik. A logikai alkalmazások futtatásához szükséges "ig" ciklusok alapértelmezett és maximális korlátainak megjelenítéséhez lásd: [párhuzamosságok, ismétlések és letételi korlátok](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Ha olyan triggerrel rendelkezik, amely egy tömböt kap, és minden tömb elemhez szeretne futtatni egy munkafolyamatot, akkor a tömböt a [ **SplitOn** trigger tulajdonsággal](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *végezheti el.*

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" hurok

A "foreach" ciklusban egy vagy több művelet ismétlődik minden egyes tömb elemen, és csak tömbökön működik. Az "foreach" hurkok használatakor a következő szempontokat érdemes figyelembe venni:

* A "foreach" hurok csak korlátozott számú tömböt tud feldolgozni. Ehhez a korláthoz lásd: [párhuzamosságok, ismétlések és letételi korlátok](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Alapértelmezés szerint a "foreach" ciklusban lévő iterációk egyszerre, vagy párhuzamosan futnak. Ez a viselkedés eltér a [Power automatizálás **minden** olyan hurokra vonatkozik](/power-automate/apply-to-each) , ahol az ismétlések egyszerre futnak, vagy egymás után. Azonban [szekvenciális "foreach"](#sequential-foreach-loop)ismétléseket is beállíthat. Ha például szüneteltetni szeretné a következő iterációt egy "foreach" hurokban a [késleltetési művelettel](../connectors/connectors-native-delay.md), a ciklust úgy kell beállítania, hogy szekvenciálisan fusson.

  Az alapértelmezett viselkedés alóli kivétel a beágyazott hurkok, amelyekben az ismétlések mindig egymás után futnak, nem párhuzamosan. Ha párhuzamosan szeretné futtatni a műveleteket egy beágyazott hurok elemein, hozzon létre és [hívjon egy alárendelt logikai alkalmazást](../logic-apps/logic-apps-http-endpoint.md).

* Ha az egyes hurok-iterációk esetében kiszámítható eredményt szeretne kapni a változók műveleteitől, futtassa ezeket a hurkokat egymás után. Ha például egy párhuzamosan futó hurok véget ér, a növekmény, a csökkentés és a Hozzáfűzés változó művelet előre jelezhető eredményeket ad vissza. Ugyanakkor a párhuzamosan futó hurokban az egyes ismétlések során ezek a műveletek kiszámíthatatlan eredményeket adhatnak vissza. 

* A "foreach" ciklusban végrehajtott műveletek a következőt használják: [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
a tömb egyes elemeinek hivatkozására és feldolgozására szolgáló kifejezés. Ha olyan adathalmazt ad meg, amely nem egy tömbben található, akkor a logikai alkalmazás munkafolyamata sikertelen lesz. 

Ez a példa a logikai alkalmazás egy webhely RSS-hírcsatornájának napi összefoglalóját küldi el. Az alkalmazás egy "foreach" hurkot használ, amely minden új elemről e-mailt küld.

1. [Hozza létre ezt a minta logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com-fiókkal vagy egy munkahelyi vagy iskolai fiókkal.

2. Az RSS-trigger és az e-mail küldése művelet között adjon hozzá egy "foreach" ciklust. 

   1. Ha a lépések között hurkot szeretne hozzáadni, vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a megjelenő **pluszjelet** ( **+** ), majd válassza a **művelet hozzáadása** lehetőséget.

      ![Válassza a "művelet hozzáadása" lehetőséget.](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. A keresőmező alatt válassza az **összes** lehetőséget. A keresőmezőbe írja be a "for each" kifejezést a szűrőként. A műveletek listából válassza ki ezt a műveletet: **az egyes vezérlőknél**

      !["Az egyes ciklusok" hozzáadása](media/logic-apps-control-flow-loops/select-for-each.png)

3. Most hozza létre a hurkot. A **dinamikus tartalom hozzáadása** **lehetőségre kattintva válassza ki a kimenet az előző lépésekből** lehetőséget, majd válassza ki az RSS-triggerből kimenetet a **hírcsatorna-hivatkozások** tömbben. 

   ![Kiválasztás a dinamikus tartalmak listájából](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Az előző lépésben *csak* a tömb kimenetei választhatók ki.

   A kiválasztott tömb most itt jelenik meg:

   ![Tömb kiválasztása](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Ha műveletet szeretne futtatni minden egyes tömb elemen, húzza az **E-mail küldése** műveletet a hurokba. 

   A logikai alkalmazás a következő példához hasonlóan néz ki:

   ![Lépések hozzáadása a "foreach" hurokhoz](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Mentse a logikai alkalmazást. A logikai alkalmazás manuális teszteléséhez a tervező eszköztárán válassza a **Futtatás** lehetőséget.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" hurok definíciója (JSON)

Ha a logikai alkalmazáshoz kód nézetben dolgozik, a `Foreach` hurok a logikai alkalmazás JSON-definíciójában is megadható, például:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" hurok: szekvenciális

Alapértelmezés szerint a "foreach" hurokban lévő ciklusok párhuzamosan futnak. Az egyes ciklusok egymás utáni futtatásához állítsa be a hurok **szekvenciális** beállítását. A "foreach" hurkoknak szekvenciálisan kell futniuk, ha beágyazott hurkok vagy változók vannak a hurokban, ahol előre jelezhető eredmények várhatók. 

1. A hurok jobb felső sarkában válassza az **ellipszisek** (**...**) > a **Beállítások** lehetőséget.

   ![A "foreach" ciklusban válassza a "..." > "beállítások" lehetőséget.](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. A **Egyidejűség vezérlőelem** alatt kapcsolja be a **Egyidejűség vezérlőelem** beállítást a **be** értékre. Helyezze át a **párhuzamossági** csúszka **1** értékre, majd válassza a **kész** lehetőséget.

   ![Egyidejűség-vezérlés bekapcsolása](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Ha a logikai alkalmazás JSON-definícióját használja, a `Sequential` (z) paraméter hozzáadásával is használhatja a lehetőséget `operationOptions` , például:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Ig" ciklus
  
A műveletek futtatásához és ismétléséhez, amíg egy feltétel nem teljesül, vagy nem változik az állapot, a műveleteket "ig" ciklusban kell elhelyezni. A logikai alkalmazás először a hurokban lévő összes műveletet futtatja, majd ellenőrzi a feltételt vagy az állapotot. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok megismétlődik. A logikai alkalmazások futtatásához szükséges "ig" ciklusok alapértelmezett és maximális korlátainak megjelenítéséhez lásd: [párhuzamosságok, ismétlések és letételi korlátok](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Íme néhány gyakori forgatókönyv, ahol "ig" ciklust használhat:

* Hívja meg a végpontot, amíg meg nem kapja a kívánt választ.

* Hozzon létre egy rekordot egy adatbázisban. Várjon, amíg a rekordban egy adott mező jóváhagyva lesz. Folytassa a feldolgozást. 

Ez a példa minden nap 8:00-kor kezdődik, így a logikai alkalmazás egy változót ér el, amíg a változó értéke 10. A logikai alkalmazás ezt követően egy e-mailt küld, amely megerősíti az aktuális értéket. 

> [!NOTE]
> Ezek a lépések az Office 365 Outlookot használják, de a Logic Apps által támogatott bármely e-mail-szolgáltatót használhat. 
> [Itt tekintse meg az összekötők listáját](/connectors/). Ha más e-mail-fiókot használ, az általános lépések ugyanazok maradnak, de a felhasználói felület némileg eltérő lehet. 

1. Üres logikai alkalmazás létrehozása. A Logic app Designerben a keresőmező alatt válassza az **összes** lehetőséget. Keressen rá az "Ismétlődés" kifejezésre. 
   Az eseményindítók listából válassza ki a következő eseményindítót: **Ismétlődés – ütemezése**

   !["Ismétlődés ütemezése" eseményindító hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Itt adhatja meg, hogy mikor induljon el az eseményindító a nap intervallumának, gyakoriságának és órájának beállításával. Az óra beállításához válassza a **Speciális beállítások megjelenítése lehetőséget**.

   ![Ismétlődési ütemterv beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Tulajdonság | Érték |
   | -------- | ----- |
   | **Intervallum** | 1 | 
   | **Gyakoriság** | Nap |
   | **Ezekben az órákban** | 8 |
   ||| 

1. Az trigger alatt válassza az **új lépés** lehetőséget. 
   Keressen rá a "változók" kifejezésre, és válassza a következő műveletet: **változó inicializálása – változók**

   !["Változó inicializálása – változók" művelet hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Állítsa be a változót a következő értékekkel:

   ![Változó tulajdonságainak beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Név** | Korlát | A változó neve | 
   | **Típus** | Egész szám | Változó adattípusa | 
   | **Érték** | 0 | A változó kezdő értéke | 
   |||| 

1. A **változó inicializálása** művelet alatt válassza az **új lépés** lehetőséget. 

1. A keresőmező alatt válassza az **összes** lehetőséget. Keressen rá a "ig" kifejezésre, és válassza ki a következő műveletet: **ig-Control**

   !["Ig" hurok hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. A hurok kilépési feltételének kiépítéséhez válassza a **limit** változót, az **pedig egyenlő** operátort. 
   Összehasonlítási értékként adja meg a **10-es** értéket.

   ![Build kilépési feltétele a hurok leállításához](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. A hurokon belül válassza a **művelet hozzáadása** lehetőséget. 

1. A keresőmező alatt válassza az **összes** lehetőséget. Keressen rá a "változók" kifejezésre, és válassza a következő műveletet: **változó növekménye – változók**

   ![Művelet hozzáadása a változó növeléséhez](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. A **név** mezőben válassza a **határérték** változót. Az **érték** mezőben adja meg az "1" értéket. 

     ![Növelje a "Limit" értéket 1-re](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Válassza az **új lépés** lehetőséget a hurokon kívül és alatt. 

1. A keresőmező alatt válassza az **összes** lehetőséget. 
     Keressen és adjon hozzá egy olyan műveletet, amely e-mailt küld, például: 

     ![E-mail küldésére szolgáló művelet hozzáadása](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

1. Adja meg az e-mail művelet tulajdonságait. Adja hozzá a **limit** változót a tárgyhoz. Így ellenőrizheti, hogy a változó aktuális értéke megfelel-e a megadott feltételnek, például:

      ![E-mail-tulajdonságok beállítása](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Tulajdonság | Érték | Leírás |
      | -------- | ----- | ----------- | 
      | **Ide:** | *\<email-address\@domain>* | A címzett e-mail-címe. Teszteléshez használja a saját e-mail-címét. | 
      | **Tárgy** | A "Limit" jelenlegi értéke **limit** | Határozza meg az e-mail tárgyát. Ebben a példában ellenőrizze, hogy tartalmazza-e a **határérték** változót. | 
      | **Törzs** | <*e-mail – tartalom*> | Itt adhatja meg a küldeni kívánt e-mail üzenet tartalmát. Ebben a példában írjon be egy tetszőleges szöveget. | 
      |||| 

1. Mentse a logikai alkalmazást. A logikai alkalmazás manuális teszteléséhez a tervező eszköztárán válassza a **Futtatás** lehetőséget.

      A logikájának futtatása után egy e-mailt kap a megadott tartalommal:

      ![Fogadott e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>Végtelen hurkok megakadályozása

A "ig" hurok a következő tulajdonságok alapján leállítja a végrehajtást, ezért ügyeljen arra, hogy az értékeiket ennek megfelelően állítsa be:

* **Darabszám**: ez az érték a hurkok a hurokból való kilépés előtt futtatott legnagyobb száma. A logikai alkalmazások futtatásához szükséges "ig" ciklusok alapértelmezett és maximális korlátainak megjelenítéséhez lásd: [párhuzamosságok, ismétlések és letételi korlátok](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* **Időtúllépés**: ez az érték a huroknak a kilépés előtt futtatott legnagyobb ideje, és [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)van megadva. Az **időtúllépési** értékre vonatkozó alapértelmezett és maximális korlátokat lásd: [párhuzamosságok, ismétlések és letételi korlátok](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

  Az időtúllépési érték kiértékelése minden hurok ciklusakor megtörténik. Ha a hurok bármelyik művelete hosszabb időt vesz igénybe, mint az időkorlát, az aktuális ciklus nem áll le. A következő ciklus azonban nem indul el, mert a korlátozási feltétel nem teljesül.

A határértékek módosításához a hurok műveletnél válassza a **határértékek módosítása** lehetőséget.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Amíg" definíció (JSON)

Ha a logikai alkalmazáshoz kód nézetben dolgozik, megadhat egy `Until` hurkot a logikai alkalmazás JSON-definíciójában, például:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Ebben a példában a "ig" ciklus egy olyan HTTP-végpontot hív meg, amely létrehoz egy erőforrást. A hurok leáll, amikor a HTTP-válasz törzse `Completed` állapottal tér vissza. A végtelen hurkok megelőzése érdekében a hurok akkor is leáll, ha a következő feltételek bármelyike megtörténik:

* A hurok 10 alkalommal futott le az attribútum által megadott módon `count` . Az alapértelmezett érték 60 alkalommal. 

* A hurok az attribútum által az ISO 8601 formátumban megadott két órán keresztül futott `timeout` . Az alapértelmezett érték egy óra.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A szolgáltatásokról és javaslatokról [Azure Logic apps felhasználói visszajelzési webhelyről](https://aka.ms/logicapps-wish)küldhet vagy szavazhat.

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása feltételek alapján (feltételes utasítások)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (switch utasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveleti állapot alapján (hatókörök)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
