---
title: Műveletek végrehajtása adatokon
description: Az adatkimenetek és a formátumok konvertálása, kezelése és módosítása Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84710371"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Adatműveletek végrehajtása a Azure Logic Appsban

Ez a cikk bemutatja, hogyan dolgozhat az adatokkal a logikai alkalmazásokban a következő feladatokhoz kapcsolódó műveletek hozzáadásával:

* Hozzon létre táblákat a tömbökből.
* Tömb létrehozása más tömbökből egy feltétel alapján.
* Felhasználóbarát tokeneket hozhat létre JavaScript Object Notation (JSON) objektum tulajdonságaiból, így egyszerűen használhatja ezeket a tulajdonságokat a munkafolyamatban.

Ha nem találja a kívánt műveletet, próbálja meg böngészni a Azure Logic Apps által biztosított számos különböző [adatkezelési funkciót](../logic-apps/workflow-definition-language-functions-reference.md) .

Ezek a táblázatok összefoglalják a felhasználható adatműveleteket, amelyeket a műveletek által használt forrás-adattípusok alapján szerveznek, de minden Leírás betűrendben jelenik meg.

**Tömb műveletei** 

Ezek a műveletek segítenek a tömbökben tárolt adatmennyiségek kezelésében.

| Művelet | Leírás |
|--------|-------------|
| [**CSV-tábla létrehozása**](#create-csv-table-action) | Hozzon létre egy vesszővel tagolt (CSV) táblázatot egy tömbből. |
| [**HTML-tábla létrehozása**](#create-html-table-action) | Hozzon létre egy HTML-táblázatot egy tömbből. |
| [**Tömb szűrése**](#filter-array-action) | Tömb részhalmazának létrehozása egy tömbből a megadott szűrő vagy feltétel alapján. |
| [**Csatlakozás**](#join-action) | Hozzon létre egy karakterláncot egy tömb összes eleméből, és válassza el az egyes elemeket a megadott karakterrel. |
| [**Válassza ezt:**](#select-action) | Hozzon létre egy tömböt a megadott tulajdonságok közül egy másik tömb összes eleméhez. |
||| 

**JSON-műveletek**

Ezek a műveletek segítenek JavaScript Object Notation (JSON) formátumban tárolt adatmennyiségek kezelésében.

| Művelet | Leírás |
|--------|-------------|
| [**Összeállítás**](#compose-action) | Hozzon létre egy üzenetet vagy karakterláncot több olyan bemenetből, amely különböző adattípusokkal rendelkezhet. Ezt a karakterláncot ezután egyetlen bemenetként használhatja, és nem kell ismételten megadnia ugyanazt a bemenetet. Létrehozhat például egy JSON-üzenetet különböző bemenetekről. |
| [**JSON-elemzés**](#parse-json-action) | Felhasználóbarát adattokeneket hozhat létre a JSON-tartalmak tulajdonságaihoz, így könnyebben használhatja a logikai alkalmazásaiban található tulajdonságokat. |
|||

Összetettebb JSON-átalakítások létrehozásához tekintse meg [a speciális JSON-átalakítások folyékony sablonokkal történő elvégzését](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyre szüksége van az adatkezelési műveletre

  Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: hozza létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Egy [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként 

  Az adatműveletek csak műveletként érhetők el, ezért a műveletek használata előtt indítsa el a logikai alkalmazást egy triggerrel, és adja meg a kívánt kimenetek létrehozásához szükséges egyéb műveleteket.

<a name="compose-action"></a>

## <a name="compose-action"></a>Összeállítási művelet

Ha egyetlen kimenetet szeretne létrehozni, például egy JSON-objektumot több bemenetből, használhatja az **összeállítás** műveletet. A bemenetek többféle típussal rendelkezhetnek, például az egész számok, a logikai elemek, a tömbök, a JSON-objektumok és a Azure Logic Apps által támogatott egyéb natív típusok, például a bináris és az XML. Ezután az **összeállítás** műveletet követő műveletek kimenetét használhatja. Az **összeállítási** művelettel a logikai alkalmazás munkafolyamatának létrehozásakor is megadhatja, hogy a rendszer ismételten beírja az azonos bemeneteket.

Például létrehozhat egy JSON-üzenetet több változóból, például karakterlánc-változókat, amelyek az emberek vezetéknevét és vezetéknevét tárolják, valamint egy egész szám változót, amely az emberek korát tárolja. Itt az **összeállítás** művelet fogadja a következő bemeneteket:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

és a következő kimenetet hozza létre:

`{"age":35,"fullName":"Owens,Sophie"}`

Egy példa kipróbálásához kövesse az alábbi lépéseket a Logic app Designer használatával. Ha inkább a Code View Editor használatával szeretne dolgozni, akkor a jelen cikkben szereplő, a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definícióba másolhatja az **összeállítási** és **inicializálási változók** definícióját: [adatműveleti kód példák – összeállítás](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portalt és egy logikai alkalmazást használ egy **ismétlődési** eseményindítóval és számos **inicializálási változó** művelettel. Ezek a műveletek két karakterlánc-változó és egy egész szám típusú változó létrehozásához vannak beállítva. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy meg kellene várnia a triggert.

   ![Minta logikai alkalmazás indítása a "levélírás" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Kövesse az alábbi lépések egyikét abban a logikai alkalmazásban, amelyben létre szeretné hozni a kimenetet: 

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![Válassza az "új lépés" lehetőséget a "levélírás" művelethez](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen, a keresőmezőbe írja be `compose` szűrőként a kifejezést. A műveletek listából válassza ki az **összeállítás** műveletet.

   ![A "levélírás" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. A **bemenetek** mezőben adja meg a kimenet létrehozásához használni kívánt bemeneteket.

   Ha ebben a példában a **bemenetek** mezőbe kattint, a dinamikus tartalom lista jelenik meg, így kiválaszthatja a korábban létrehozott változókat:

   ![Válassza ki a "levélírás" művelethez használandó bemeneteket](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   A következő példában a kész példa az **összeállítás** művelet: 

   ![Példa az "összeállítás" műveletre](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

További információ erről a műveletről az alapul szolgáló munkafolyamat-definícióban: [összeállítási művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy az **összeállítás** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza az **összeállítási** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket az **összeállítás** műveletből.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista, az **összeállítás** művelet alatt válassza a **kimenet** lehetőséget.

   Ez a példa az **E-mail küldése** műveletet használja, és tartalmazza az e-mail szövegtörzsében található **kimeneti** mezőket, valamint a tárgyat:

   !["Output" mezők a "levélírás" művelethez](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget.

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mail "levélírás" művelet eredményeivel](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>CSV-táblázat létrehozása művelet

Egy olyan vesszővel tagolt (CSV) tábla létrehozásához, amely egy tömb JavaScript Object Notation (JSON) objektumainak tulajdonságait és értékeit tartalmazza, használja a **CSV-táblázat létrehozása** műveletet. Ezután használhatja az eredményül kapott táblázatot a **CSV-táblázat létrehozása** műveletet követő műveletekben.

Ha inkább a Code View Editor használatával szeretne dolgozni, a példa **CSV-táblázat létrehozása** és a változó műveleti definíciók **inicializálása** ebből a cikkből a saját logikai alkalmazás mögöttes munkafolyamat-definíciója szerint: [adatműveleti kód példák – CSV-táblázat létrehozása](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ **ismétlődési** eseményindítóval és **inicializálási változó** művelettel. A művelet egy olyan változó létrehozásához van beállítva, amelynek a kezdeti értéke egy olyan tömb, amely tartalmaz néhány tulajdonságot és értéket JSON formátumban. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy meg kellene várnia a triggert.

   ![Minta logikai alkalmazás indítása a "CSV-táblázat létrehozása" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Kövesse az alábbi lépések egyikét abban a logikai alkalmazásban, amelyben létre kívánja hozni a CSV-táblázatot: 

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![Válassza az "új lépés" lehetőséget a "CSV-táblázat létrehozása" művelethez](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen, a keresőmezőbe írja be `create csv table` szűrőként a kifejezést. A műveletek listából válassza a CSV- **táblázat létrehozása** műveletet.

   ![Válassza a "CSV-táblázat létrehozása" műveletet.](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. A **from (forrás** ) mezőben adja meg a tábla létrehozásához használni kívánt tömböt vagy kifejezést.

   Ebben a példában a **from (forrás** ) elemre kattintva megjelenik a dinamikus tartalom lista, így kiválaszthatja a korábban létrehozott változót:

   ![Tömb kimenetének kiválasztása CSV-táblázat létrehozásához](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > A JSON-objektumok tulajdonságainak felhasználóbarát tokenek létrehozásához, hogy a tulajdonságokat kiválaszthatja bemenetként, használja a [JSON](#parse-json-action) elemzése műveletet a **CSV-táblázat létrehozása** művelet meghívása előtt.

   Az alábbi példában a **CSV-táblázat létrehozása** műveletet végezheti el: 

   ![Példa a "CSV-táblázat létrehozása" műveletre](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="customize-table-format"></a>Táblázat formátumának testreszabása

Alapértelmezés szerint az **oszlopok** tulajdonság úgy van beállítva, hogy a tömb elemei alapján automatikusan létrehozza a tábla oszlopait. Egyéni fejlécek és értékek megadásához kövesse az alábbi lépéseket:

1. Nyissa meg az **oszlopok** listát, és válassza az **Egyéni** lehetőséget.

1. A **header (fejléc** ) tulajdonságban válassza a használni kívánt egyéni fejléc szövegét.

1. Az **érték** tulajdonságnál válassza a használni kívánt egyéni értéket.

Ha értékeket szeretne visszaadni a tömbből, a [ `item()` függvényt](../logic-apps/workflow-definition-language-functions-reference.md#item) használhatja a **CSV-táblázat létrehozása** művelettel. Egy `For_each` hurokban használhatja a [ `items()` függvényt](../logic-apps/workflow-definition-language-functions-reference.md#items).

Tegyük fel például, hogy olyan tábla oszlopokat szeretne, amelyeknek csak a tulajdonsága van, és nem a tulajdonságok nevei tömbből. Ha csak ezeket az értékeket szeretné visszaadni, hajtsa végre a következő lépéseket a Tervező nézetben vagy a kód nézetben való munkához. A példa a következő eredményt adja vissza:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Munka tervező nézetben

A műveletben hagyja üresen a **fejléc** oszlopot. A **Value (érték** ) oszlop minden egyes sorában az összes kívánt tömb-tulajdonságot el kell hivatkozni. Az **érték** alatti sorok mindegyike visszaadja a megadott Array tulajdonság összes értékét, és a tábla egyik oszlopa lesz.

1. Az **érték** területen minden egyes kívánt sorban kattintson a szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája.

1. A dinamikus tartalom listában válassza a **kifejezés** lehetőséget.

1. A kifejezés-szerkesztőben írja be ezt a kifejezést, amely megadja a tömb tulajdonságérték kívánt értékét, majd kattintson **az OK gombra**.

   `item()?['<array-property-name>']`

   Például:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   !["Description" kifejezés a "CSV-táblázat létrehozása" kifejezésre](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Ismételje meg az előző lépéseket minden kívánt tömb-tulajdonság esetében. Ha elkészült, a művelet a következő példához hasonlóan néz ki:

   !["Item ()" függvény a "CSV-táblázat létrehozása"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Ha több leíró verzióra szeretné feloldani a kifejezéseket, váltson kód nézetre, és vissza a Tervező nézetre, majd nyissa meg újra az összecsukott műveletet:

   A **CSV-táblázat létrehozása** művelet most a következő példához hasonlóan jelenik meg:

   !["CSV-táblázat létrehozása" – megoldott kifejezések, nincsenek fejlécek](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Munka kód nézetben

A művelet JSON-definíciójában a `columns` tömbön belül állítsa a `header` tulajdonságot egy üres karakterláncra. Minden tulajdonság esetében az `value` összes kívánt Array tulajdonságot el kell, hogy hivatkozzon.

1. A tervező eszköztárán válassza a **kód nézet** lehetőséget.

1. A Kódszerkesztőben a művelet `columns` tömbben adja hozzá az üres `header` tulajdonságot és ezt a `value` kifejezést a kívánt tömbképlet minden oszlopához:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Például:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Váltson vissza Tervező nézetre, és nyissa meg újra az összecsukott műveletet.

   A **CSV-táblázat létrehozása** művelet most az alábbi példához hasonlóan jelenik meg, és a kifejezések több leíró verzióra vannak feloldva:

   !["CSV-táblázat létrehozása" – megoldott kifejezések és nincsenek fejlécek](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

További információ erről a műveletről a mögöttes munkafolyamat-definícióban: [tábla művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **CSV-táblázat létrehozása** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **CSV-táblázat létrehozása** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely az eredményeket a **CSV-táblázat létrehozása** műveletből küldi el.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista, a **CSV-táblázat létrehozása** művelet alatt válassza a **kimenet** lehetőséget. 

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletét használja, és tartalmazza az e-mail szövegtörzsének **output (kimenet** ) mezőjét:

   !["Output" mezők a "CSV-táblázat létrehozása" művelethez](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget.

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mail-cím "CSV-táblázat létrehozása" művelet eredményei](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML-táblázat létrehozása művelet

Ha olyan HTML-táblázatot szeretne létrehozni, amely egy tömb JavaScript Object Notation (JSON) objektumainak tulajdonságait és értékeit tartalmazza, használja a **HTML-táblázat létrehozása** műveletet. Ezután használhatja az eredményül kapott táblázatot a **HTML-táblázat létrehozása** műveletet követő műveletekben.

Ha inkább a Code View Editor használatával szeretne dolgozni, másolhatja a HTML- **táblázat létrehozása** és a változó műveleti definíciók **inicializálása** ebből a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definícióját: [adatműveleti kód példák – HTML-táblázat létrehozása](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ **ismétlődési** eseményindítóval és **inicializálási változó** művelettel. A művelet egy olyan változó létrehozásához van beállítva, amelynek a kezdeti értéke egy olyan tömb, amely tartalmaz néhány tulajdonságot és értéket JSON formátumban. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy meg kellene várnia a triggert.

   ![Minta logikai alkalmazás indítása a "HTML-tábla létrehozása"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. A logikai alkalmazásban, ahol létre szeretne hozni egy HTML-táblázatot, kövesse az alábbi lépések egyikét:

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![Válassza az "új lépés" lehetőséget a "HTML-tábla létrehozása" művelethez](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen, a keresőmezőbe írja be `create html table` szűrőként a kifejezést. A műveletek listából válassza a HTML- **táblázat létrehozása** műveletet.

   ![Válassza a "HTML-tábla létrehozása" műveletet.](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. A **from (forrás** ) mezőben adja meg a tábla létrehozásához használni kívánt tömböt vagy kifejezést.

   Ebben a példában a **from (forrás** ) elemre kattintva megjelenik a dinamikus tartalom lista, így kiválaszthatja a korábban létrehozott változót:

   ![Tömb kimenetének kiválasztása HTML-táblázat létrehozásához](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > A JSON-objektumok tulajdonságainak felhasználóbarát tokenek létrehozásához, hogy ezeket a tulajdonságokat kiválaszthatja bemenetként, a **HTML-táblázat létrehozása** művelet meghívása előtt használja a [JSON-elemzést](#parse-json-action) .

   A következő példában a **HTML-táblázat létrehozása** művelet elkészült:

   ![A "HTML-táblázat létrehozása" című példa elkészült](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="customize-table-format"></a>Táblázat formátumának testreszabása

Alapértelmezés szerint az **oszlopok** tulajdonság úgy van beállítva, hogy a tömb elemei alapján automatikusan létrehozza a tábla oszlopait. Egyéni fejlécek és értékek megadásához kövesse az alábbi lépéseket:

1. Nyissa meg az **oszlopok** listát, és válassza az **Egyéni** lehetőséget.

1. A **header (fejléc** ) tulajdonságban válassza a használni kívánt egyéni fejléc szövegét.

1. Az **érték** tulajdonságnál válassza a használni kívánt egyéni értéket.

Ha értékeket szeretne visszaadni a tömbből, használhatja a [ `item()` függvényt](../logic-apps/workflow-definition-language-functions-reference.md#item) a **HTML-táblázat létrehozása** művelettel. Egy `For_each` hurokban használhatja a [ `items()` függvényt](../logic-apps/workflow-definition-language-functions-reference.md#items).

Tegyük fel például, hogy olyan tábla oszlopokat szeretne, amelyeknek csak a tulajdonsága van, és nem a tulajdonságok nevei tömbből. Ha csak ezeket az értékeket szeretné visszaadni, hajtsa végre a következő lépéseket a Tervező nézetben vagy a kód nézetben való munkához. A példa a következő eredményt adja vissza:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Munka tervező nézetben

A műveletben hagyja üresen a **fejléc** oszlopot. A **Value (érték** ) oszlop minden egyes sorában az összes kívánt tömb-tulajdonságot el kell hivatkozni. Az **érték** alatti sorok mindegyike visszaadja a megadott tulajdonság összes értékét, és a tábla egyik oszlopa lesz.

1. Az **érték** területen minden egyes kívánt sorban kattintson a szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája.

1. A dinamikus tartalom listában válassza a **kifejezés** lehetőséget.

1. A kifejezés-szerkesztőben írja be ezt a kifejezést, amely megadja a tömb tulajdonságérték kívánt értékét, majd kattintson **az OK gombra**.

   `item()?['<array-property-name>']`

   Például:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![A (z) "HTML-tábla létrehozása" művelet dereference tulajdonsága](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Ismételje meg az előző lépéseket minden kívánt tömb-tulajdonság esetében. Ha elkészült, a művelet a következő példához hasonlóan néz ki:

   !["Item ()" függvény a "HTML-tábla létrehozása"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Ha több leíró verzióra szeretné feloldani a kifejezéseket, váltson kód nézetre, és vissza a Tervező nézetre, majd nyissa meg újra az összecsukott műveletet:

   A **HTML-táblázat létrehozása** művelet most a következő példához hasonlóan jelenik meg:

   !["HTML-táblázat létrehozása" – megoldott kifejezések, nincs fejléc](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Munka kód nézetben

A művelet JSON-definíciójában a `columns` tömbön belül állítsa a `header` tulajdonságot egy üres karakterláncra. Minden tulajdonság esetében az `value` összes kívánt Array tulajdonságot el kell, hogy hivatkozzon.

1. A tervező eszköztárán válassza a **kód nézet** lehetőséget.

1. A Kódszerkesztőben a művelet `columns` tömbben adja hozzá az üres `header` tulajdonságot és ezt a `value` kifejezést a kívánt tömbképlet minden oszlopához:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Például:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Váltson vissza Tervező nézetre, és nyissa meg újra az összecsukott műveletet.

   A **HTML-táblázat létrehozása** művelet most a példához hasonlóan jelenik meg, és a kifejezések több leíró verzióra vannak feloldva:

   !["HTML-tábla létrehozása" – megoldott kifejezések és nincsenek fejlécek](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

További információ erről a műveletről a mögöttes munkafolyamat-definícióban: [tábla művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **HTML-táblázat létrehozása** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely a **HTML-táblázat létrehozása** műveletből származó kimenetet tartalmaz.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely az eredményeket a **HTML-tábla létrehozása** műveletből küldi el.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista, a **HTML-táblázat létrehozása** művelet alatt válassza a **kimenet** lehetőséget. 

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletét használja, és tartalmazza az e-mail szövegtörzsének **output (kimenet** ) mezőjét:

   !["Output" mezők a "HTML-tábla létrehozása"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ha belefoglalja a HTML-táblázat kimenetét egy e-mail-műveletbe, ügyeljen arra, hogy a a **HTML** tulajdonság értéke **Igen** legyen az e-mail művelet speciális beállításai között. Így az e-mail-művelet helyesen formázza a HTML-táblázatot.

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget.

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mail a "HTML-táblázat létrehozása" eredményeivel](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Tömb szűrése művelet

Ha olyan kisebb tömböt szeretne létrehozni, amely meghatározott feltételeknek megfelelő elemeket tartalmaz egy meglévő tömbből, használja a **tömb szűrése** műveletet. Ezután használhatja a szűrt tömböt a **szűrő tömb** műveletét követő műveletekben.

> [!NOTE]
> A feltételben használt bármely szűrési szöveg megkülönbözteti a kis-és nagybetűket. Emellett ez a művelet nem változtathatja meg a tömbben lévő elemek formátumát vagy összetevőit. 
> 
> Ha a tömb kimenetét a tömb **szűrése** művelettel szeretné használni, akkor a műveleteknek el kell fogadniuk a tömböket bemenetként, vagy esetleg át kell alakítania a kimeneti tömböt egy másik kompatibilis formátumba.
> 
> Ha HTTP-végpontot hív meg, és JSON-választ kap, akkor a **JSON** elemzése művelettel dolgozza fel a JSON-választ. 
> Ellenkező esetben a **szűrő tömb** művelete csak a válasz törzsét olvashatja, nem pedig a JSON-adattartalom szerkezetét.

Ha inkább a Code View Editor használatával szeretne dolgozni, a jelen cikkben **szereplő, a** saját logikai alkalmazás alapjául szolgáló munkafolyamat-definícióba másolhatja a jelen cikk **változó** műveleti definícióját: [adatműveleti kód példák – szűrő tömb](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ **ismétlődési** eseményindítóval és **inicializálási változó** művelettel. A művelet egy olyan változó létrehozásához van beállítva, amelynek a kezdeti értéke egy olyan tömb, amely néhány minta egész számot tartalmaz. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy meg kellene várnia a triggert.

   > [!NOTE]
   > Bár ez a példa egy egyszerű egész tömböt használ, ez a művelet különösen a JSON-objektumok tömböknél hasznos, ahol az objektumok tulajdonságai és értékei alapján szűrheti.

   ![Minta logikai alkalmazás indítása a "tömb szűrése" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Kövesse az alábbi lépések egyikét abban a logikai alkalmazásban, amelyben létre kívánja hozni a szűrt tömböt: 

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![Válassza az "új lépés" lehetőséget a "tömb szűrése" művelethez](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be `filter array` szűrőként a kifejezést. A műveletek listából válassza a **tömb szűrése** műveletet.

   ![A "tömb szűrése" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. A **from (forrás** ) mezőben adja meg a szűrni kívánt tömböt vagy kifejezést.

   Ebben a példában a **from (forrás** ) elemre kattintva megjelenik a dinamikus tartalom lista, így kiválaszthatja a korábban létrehozott változót:

   ![Tömb kimenetének kiválasztása szűrt tömb létrehozásához](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. A feltétel beállításnál adja meg az összehasonlítani kívánt tömböt, válassza ki az összehasonlító operátort, és adja meg az összehasonlítási értéket.

   Ez a példa a `item()` függvényt használja a tömb egyes elemeinek eléréséhez, miközben a tömb **szűrése** művelet megkeresi azokat a tömb elemeket, amelyek értéke nagyobb, mint egy:

   ![Példa a "tömb szűrése" műveletre](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

További információ erről a műveletről a mögöttes munkafolyamat-definícióban: [lekérdezési művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **tömb szűrése** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **tömb szűrése** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely az eredményeket a **tömb szűrése** műveletből küldi el.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista, válassza a **kifejezés** lehetőséget. Ha a tömb kimenetét szeretné lekérni a tömb **szűrése** műveletből, írja be ezt a kifejezést, amely tartalmazza a **szűrő tömb** műveletének nevét:

   `@actionBody('Filter_array')`

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletét használja, és tartalmazza a **actionBody ("Filter_array")** kifejezés kimeneteit az e-mail törzsében:

   ![A "tömb szűrése" művelet kimenetei](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget.

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mail-cím "szűrési tömb" művelet eredményei](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Csatlakozási művelet

Ha egy tömb összes elemét tartalmazó sztringet szeretne létrehozni, és az elemeket egy adott elválasztó karakterrel választja el, használja az **illesztési** műveletet. Ezután használhatja a karakterláncot az **illesztési** művelet után következő műveletekben.

Ha inkább a Code View Editor használatával szeretne dolgozni, akkor a jelen cikkben szereplő, a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definícióba másolhatja a következő példában szereplő **illesztési** és **inicializálási** művelet-definíciókat: az [adatműveleti kódok példái – csatlakozás](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ **ismétlődési** eseményindítóval és **inicializálási változó** művelettel. Ez a művelet olyan változó létrehozásához van beállítva, amelynek a kezdeti értéke egy olyan tömb, amely néhány minta egész számmal rendelkezik. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy a triggert tüzet kellene várnia.

   ![Minta logikai alkalmazás indítása a "Join" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. A logikai alkalmazásban, ahol létre szeretné hozni a karakterláncot egy tömbből, kövesse az alábbi lépések egyikét:

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![SSelect "új lépés" a "Join" művelethez](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be `join` szűrőként a kifejezést. A műveletek listából válassza a következő műveletet: **Csatlakozás**

   ![A "Join" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. A **from (forrás** ) mezőben adja meg azt a tömböt, amely tartalmazza a karakterláncként csatlakoztatni kívánt elemeket.

   Ebben a példában a **from (forrás** ) mezőbe kattintva megjelenik a dinamikus tartalom lista, amely a korábban létrehozott változó kiválasztására használható:  

   ![Tömb kimenetének kiválasztása a karakterlánc létrehozásához](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. Az **illesztés** a következővel mezőben adja meg az egyes tömb elemek elválasztásához használni kívánt karaktert. 

   Ez a példa kettőspontot használ (:) az elválasztóként.

   ![Adja meg az elválasztó karaktert](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

További információ erről a műveletről az alapul szolgáló munkafolyamat-definícióban: [JOIN művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **JOIN** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza az **illesztési** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely képes elküldeni az eredményeket az **illesztési** műveletből.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista, az **összekapcsolás** művelet alatt válassza a **kimenet** lehetőséget. 

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletét használja, és tartalmazza az e-mail szövegtörzsének **output (kimenet** ) mezőjét:

   !["Output" mezők a "Join" művelethez](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget.

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mail-cím "Join" művelet eredményei](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>JSON-művelet elemzése

JavaScript Object Notation-(JSON-) tartalomban lévő tulajdonságok hivatkozásához vagy eléréséhez a **JSON** elemzése művelettel létrehozhat felhasználóbarát mezőket vagy jogkivonatokat ezekhez a tulajdonságokhoz. Így kiválaszthatja ezeket a tulajdonságokat a dinamikus tartalmak listájából, ha a logikai alkalmazás bemeneteit adja meg. Ehhez a művelethez egy JSON-sémát adhat meg, vagy létrehozhat egy JSON-sémát a mintául szolgáló JSON-tartalomból vagy adattartalomból.

Ha inkább a Code View Editor használatával szeretne dolgozni, a cikkből átmásolhatja a **JSON** -t, és **inicializálhatja a változó** műveleti definícióit ebből a cikkből a saját logikai alkalmazás mögöttes munkafolyamat-definíciójában: [adatműveleti kód példák-parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ **ismétlődési** eseményindítóval és **inicializálási változó** művelettel. A művelet egy olyan változó létrehozásához van beállítva, amelynek a kezdeti értéke egy olyan JSON-objektum, amelynek tulajdonságai és értékei vannak. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy meg kellene várnia a triggert.

   ![Minta logikai alkalmazás indítása a "JSON értelmezése" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. Kövesse az alábbi lépések egyikét a logikai alkalmazásban, ahol elemezni szeretné a JSON-tartalmat:

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![Válassza az "új lépés" lehetőséget a "JSON-elemzés" művelethez](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be `parse json` szűrőként a kifejezést. A műveletek listából válassza a JSON- **elemzés** műveletet.

   ![A "JSON-elemzés" művelet kiválasztása](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. A **Content (tartalom** ) mezőben adja meg az elemezni kívánt JSON-tartalmat.

   Ha ebben a példában a **tartalom** mezőbe kattint, a dinamikus tartalom lista jelenik meg, így kiválaszthatja a korábban létrehozott változót:

   ![JSON-objektum kiválasztása a JSON-elemzési művelethez](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Adja meg az elemezni kívánt JSON-tartalmat leíró JSON-sémát.

   Ebben a példában a JSON-séma a következő:

   ![JSON-séma megadása az elemezni kívánt JSON-objektumhoz](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Ha nem rendelkezik a sémával, ezt a sémát a JSON-tartalomból vagy a *hasznos* adatokból is létrehozhatja. 
   
   1. A **JSON-elemzés** műveletben válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

   1. A JSON-adattartalom **megadása vagy beillesztése** területen adja meg a JSON-tartalmat, majd válassza a **kész** lehetőséget.

      ![Adja meg a séma generálásához használt JSON-tartalmat](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

További információ erről a műveletről a mögöttes munkafolyamat-definícióban: [JSON-művelet](../logic-apps/logic-apps-workflow-actions-triggers.md)elemzése.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **JSON-elemzés** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza az **elemzés JSON** -művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket az **elemzés JSON** -műveletből.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista a **JSON** elemzése művelet alatt, mostantól kiválaszthatja a tulajdonságokat az elemzett JSON-tartalomból.

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletét használja, és tartalmazza az e-mail szövegtörzsének **FirstName**, **LastName** és **e-mail** -mezőit:

   ![Az "e-mail küldése" művelet JSON-tulajdonságai](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Itt látható a kész e-mail művelet:

   ![Példa az e-mail műveletre](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget. 

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mail-cím "JSON-elemzés" művelet eredményei](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Művelet kiválasztása

Egy meglévő tömb értékeiből származó JSON-objektumokat tartalmazó tömb létrehozásához használja a **Select** műveletet. Létrehozhat például egy JSON-objektumot egy egész tömb minden értékéhez az egyes JSON-objektumok tulajdonságainak megadásával, valamint a forrás tömbben lévő értékek hozzárendelésével ezekhez a tulajdonságokhoz. Bár a JSON-objektumok összetevői megváltoztathatók, a kimeneti tömb mindig ugyanannyi elemet tartalmaz, mint a forrás tömb.

> [!NOTE]
> A **Select** művelet által a tömb kimenetét használó műveletekhez a műveleteknek el kell fogadniuk a tömböket bemenetként, vagy esetleg át kell alakítania a kimeneti tömböt egy másik kompatibilis formátumba. 

Ha inkább a Code View Editor használatával szeretne dolgozni, másolhatja a jelen cikk  **változó** műveleti definícióit a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definícióba: [adatműveleti kódokra vonatkozó példák – kiválasztás](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ **ismétlődési** eseményindítóval és **inicializálási változó** művelettel. A művelet egy olyan változó létrehozásához van beállítva, amelynek a kezdeti értéke egy olyan tömb, amely néhány minta egész számot tartalmaz. Ha később teszteli a logikai alkalmazást, az alkalmazást manuálisan is futtathatja anélkül, hogy meg kellene várnia a triggert.

   ![Minta logikai alkalmazás indítása a "Select" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Kövesse az alábbi lépések egyikét abban a logikai alkalmazásban, amelyben létre kívánja hozni a tömböt: 

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![Válassza az "új lépés" lehetőséget a "Select" művelethez](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be `select` szűrőként a kifejezést. A műveletek listából válassza a **kijelölés** műveletet.

   ![Válassza a "kiválasztás" műveletet](./media/logic-apps-perform-data-operations/select-select-action.png)

1. A **Feladó** mezőben határozza meg a kívánt forrás-tömböt.

   Ebben a példában a **from (forrás** ) elemre kattintva megjelenik a dinamikus tartalom lista, így kiválaszthatja a korábban létrehozott változót:

   ![Forrás tömb kiválasztása a Select művelethez](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. A **Térkép** mező bal oldali oszlopában adja meg a forrás tömbben az egyes értékek hozzárendeléséhez használni kívánt tulajdonság nevét. A jobb oldali oszlopban adjon meg egy kifejezést, amely a tulajdonságot hozzárendelni kívánt értéket jelöli.

   Ez a példa a "Product_ID" értéket adja meg, mivel a tulajdonság neve az egész tömbben lévő értékek hozzárendelésére szolgál az `item()` egyes tömbökhöz hozzáférő kifejezésben szereplő függvény használatával. 

   ![A JSON-objektum tulajdonságainak és értékeinek meghatározása tömb létrehozásához](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Itt látható a befejezett művelet:

   ![Befejezett példa a "Select" műveletre](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

További információ erről a műveletről a mögöttes munkafolyamat-definícióban: [művelet kiválasztása](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **Select** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **Select** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely az eredményeket a **Select** műveletből küldheti el.

1. Ebben a műveletben kattintson bárhová, ahol az eredményeket meg szeretné jeleníteni. Amikor megnyílik a dinamikus tartalom lista, válassza a **kifejezés** lehetőséget. A **Select** műveletből származó tömb kimenetének lekéréséhez írja be ezt a kifejezést, amely tartalmazza a **Select** művelet nevét:

   `@actionBody('Select')`

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletét használja, és az `@actionBody('Select')` e-mail-szövegtörzsben szereplő kifejezés kimeneteit tartalmazza:

   ![Művelet kimenetei a "Select" műveletből](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás** lehetőséget.

   Az Ön által használt e-mail-összekötő alapján a következő eredmények érhetők el:

   ![E-mailek a "Select" művelet eredményeivel](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [Logic apps-összekötőről](../connectors/apis-list.md)
