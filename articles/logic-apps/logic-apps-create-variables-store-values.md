---
title: Változók létrehozása és kezelése az értékek tárolásához és átadásához
description: Megtudhatja, hogyan tárolhatja, kezelheti, használhatja és továbbíthatja az értékeket az automatizált feladatokban és a Azure Logic Apps használatával létrehozott munkafolyamatban található változók segítségével.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333902"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Értékek tárolása és felügyelete változók használatával az Azure Logic Appsben

Ez a cikk bemutatja, hogyan hozhatók létre és használhatók a logikai alkalmazásban található értékek tárolására használt változók. A változók segítségével például nyomon követheti, hogy hány alkalommal fut a hurok. Ha egy tömböt szeretne megismételni, vagy egy adott elem tömbjét szeretné megtekinteni, használhat egy változót az egyes tömbökhöz tartozó indexek hivatkozási számának megadásához.

Létrehozhat változókat olyan adattípusokhoz, mint például az Integer, az float, a Boolean, a string, a Array és az Object. Egy változó létrehozása után más feladatokat is elvégezhet, például:

* A változó értékének beolvasása vagy hivatkozása.
* Növelje vagy csökkentse a változót egy konstans értékkel, más néven *növekményt* és *csökkentéset*.
* Rendeljen egy másik értéket a változóhoz.
* Szúrja be vagy *fűzze hozzá* a változó értékét a karakterlánc vagy tömb utolsó elemeként.

A változók léteznek, és globálisak, csak a létrehozásuk során létrehozott Logic app-példányon belül. Emellett a Logic app-példányon belül minden hurok-iterációban megmaradnak. Ha egy változóra hivatkozik, használja a változó nevét tokenként, nem pedig a művelet nevét, amely a szokásos módon hivatkozik a művelet kimenetére.

> [!IMPORTANT]
> Alapértelmezés szerint a "for each" ciklusok párhuzamosan futnak. Ha változókat használ a hurkokban, futtassa a hurkot [egymás után](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) , hogy a változók kiszámítható eredményeket adjanak vissza.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás, amelyben létre kívánja hozni a változót

  Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: hozza létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Egy [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként

  A változók létrehozásával és használatával kapcsolatos műveletek hozzáadása előtt a logikai alkalmazásnak triggerrel kell kezdődnie.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Változó inicializálása

Létrehozhat egy változót, és deklarálhatja az adattípusát és a kezdeti értéket – mindezt a logikai alkalmazás egy műveletén belül. A változókat csak globális szinten deklarálhatja, nem a hatókörökön, a feltételeken és a hurkon belül.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a Azure Portal és egy logikai alkalmazást használ egy meglévő triggerrel.

1. A logikai alkalmazásban, amely alatt a változót hozzá kívánja adni, kövesse az alábbi lépések egyikét: 

   * Ha az utolsó lépés alatt szeretne felvenni egy műveletet, válassza az **új lépés** lehetőséget.

     ![A "Logic app Designer" oldalon kiválasztott "új lépés" műveletet bemutató képernyőkép.](./media/logic-apps-create-variables-store-values/add-action.png)

   * A lépések közötti művelet hozzáadásához vigye az egérmutatót az összekötő nyíl fölé, hogy megjelenjen a pluszjel ( **+** ). Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen, a keresőmezőbe írja be `variables` szűrőként a kifejezést. A műveletek listából válassza a **változó inicializálása** elemet.

   ![Művelet kiválasztása](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Adja meg az alábbi adatokat a változóról az alább leírtak szerint:

   | Tulajdonság | Kötelező | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | **Név** | Yes | <*változó – név*> | A növekményes változó neve |
   | **Típus** | Yes | <*változó típusa*> | A változó adattípusa |
   | **Érték** | No | <*kezdő érték*> | A változó kezdeti értéke <p><p>**Tipp**: bár nem kötelező, állítsa be ezt az értéket ajánlott eljárásként, hogy mindig tudja a változó indítási értékét. |
   |||||

   Például:

   ![Változó inicializálása](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Most folytassa a kívánt műveletek hozzáadását. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

Ha a tervezőből a kód nézet szerkesztőjére vált, a **változó inicializálása** művelet a logikai alkalmazás definíciójában jelenik meg, amely JavaScript Object Notation (JSON) formátumban van:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> Bár az **inicializálási változó** művelet egy `variables` tömbként strukturált szakaszt tartalmaz, a művelet egyszerre csak egy változót tud létrehozni. Minden új változóhoz egyéni **inicializálási változó** szükséges.

Néhány példa más változó típusokra:

*Karakterlánc-változó*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Logikai változó*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Tömb egész számokkal*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Tömb sztringekkel*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>A változó értékének beolvasása

Változó tartalmának lekéréséhez vagy hivatkozásához használhatja a Logic app Designer és a Code View Editor [változók () függvényét](../logic-apps/workflow-definition-language-functions-reference.md#variables) is. Egy változóra való hivatkozáskor használja a változó nevét tokenként, nem pedig a művelet nevét, amely a szokásos módon hivatkozik egy művelet kimenetére.

Ez a kifejezés például beolvassa a [cikkben korábban létrehozott](#append-value) Array változó elemeit a `variables()` függvény használatával. A `string()` függvény karakterlánc formátumban adja vissza a változó tartalmát: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Növekmény változó 

Egy változó állandó értékkel *való növeléséhez vagy* növeléséhez adja hozzá a **változó növekménye** műveletet a logikai alkalmazáshoz. Ez a művelet csak egész és lebegőpontos változók esetén működik.

1. A Logic app Designerben abban a lépésben, ahol egy meglévő változót szeretne bővíteni, válassza az **új lépés** lehetőséget. 

   Ez a logikai alkalmazás például már rendelkezik triggerrel és egy változót létrehozó művelettel. Ezért adjon hozzá egy új műveletet a következő lépésekben:

   ![Művelet hozzáadása](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   A meglévő lépések közötti művelet hozzáadásához vigye az egérmutatót a kapcsolódási nyíl fölé, hogy a pluszjel (+) megjelenjen. Válassza ki a plusz jelre, majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "változó növekménye" kifejezést. A műveletek listában válassza a **növekmény változó** lehetőséget.

   ![Válassza a "változó növekménye" műveletet.](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Adja meg ezt az információt a változó növeléséhez:

   | Tulajdonság | Kötelező | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | **Név** | Yes | <*változó – név*> | A növekményes változó neve |
   | **Érték** | No | <*növekmény – érték*> | A változó növeléséhez használt érték. Az alapértelmezett érték egy. <p><p>**Tipp**: bár nem kötelező, állítsa be ezt az értéket ajánlott eljárásként, hogy mindig tudja a változó növelésének adott értékét. |
   ||||

   Például:

   ![Növekmény értékének példája](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

Ha a tervezőből a kód nézet szerkesztőjére vált, itt látható, hogy a logikai alkalmazás definíciójában megjelenik a **növekmény változó** művelet, amely JSON formátumú:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Példa: Create loop Counter

A változók általában a hurok futási idejének megszámlálásához használatosak. Ez a példa bemutatja, hogyan hozhat létre és használhat változókat ehhez a feladathoz egy olyan hurok létrehozásával, amely megszámolja a mellékleteket egy e-mailben.

1. A Azure Portal hozzon létre egy üres logikai alkalmazást. Adjon hozzá egy triggert, amely új e-maileket és mellékleteket keres.

   Ez a példa az Office 365 Outlook triggert használja, **Amikor új e-mail érkezik**. Ezt a triggert csak akkor állíthatja be, ha az e-mail mellékletekkel rendelkezik. Azonban bármilyen összekötőt használhat, amely a mellékletekkel rendelkező új e-maileket keres, például az Outlook.com-összekötőt.

1. Az triggerben a mellékletek kereséséhez és a mellékletek a logikai alkalmazás munkafolyamataiba való átadásához válassza az **Igen** lehetőséget a következő tulajdonságok esetében:

   * **Melléklettel rendelkezik**
   * **Mellékletek is**

   ![Mellékletek keresése és belefoglalása](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Adja hozzá az [ **inicializálási változó** műveletet](#create-variable). Hozzon létre egy nevű egész szám típusú változót `Count` , amely nulla indítási értékkel rendelkezik.

   ![Művelet hozzáadása a "változó inicializálása"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Az egyes mellékleteken keresztüli váltáshoz vegyen fel egy *minden* hurokhoz.

   1. Az **inicializálási változó inicializálása** műveletnél válassza az **új lépés** lehetőséget.

   1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be a `for each` keresési szűrőt, és válassza **az egyesekhez** lehetőséget.

      !["For each" ciklus hozzáadása](./media/logic-apps-create-variables-store-values/add-loop.png)

1. A hurokban kattintson a **Kimenet kiválasztása az előző lépésekből** mezőbe. Amikor megjelenik a dinamikus tartalom lista, válassza a **mellékletek** lehetőséget.

   ![A „Mellékletek” elem kiválasztása](./media/logic-apps-create-variables-store-values/select-attachments.png)

   A **mellékletek** tulajdonság egy tömböt ad át, amely az trigger kimenetében található e-mail-mellékleteket adja vissza a hurokba.

1. A **minden** hurok esetében válassza a **művelet hozzáadása** lehetőséget.

   ![Válassza a "művelet hozzáadása" lehetőséget.](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. A keresőmezőbe írja be szűrőként a "változó növekménye" kifejezést. A műveletek listából válassza a **növekmény változó** lehetőséget.

   > [!NOTE]
   > Győződjön meg arról, hogy a **növekmény változó** művelet megjelenik a hurokban. Ha a művelet a hurokon kívül jelenik meg, húzza a műveletet a hurokba.

1. A **növekmény változó** műveletben a **név** listából válassza ki a **Count** változót.

   !["Count" változó kiválasztása](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. A hurok alatt adjon hozzá minden olyan műveletet, amely elküldi a mellékletek számát. A műveletben adja meg a **Count** változó értékét, például:

   ![Eredményeket küldő művelet hozzáadása](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás nincs engedélyezve, a logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Az eszköztáron válassza az **Engedélyezés** lehetőséget.

1. A Logic app Designer eszköztárán válassza a **Futtatás** lehetőséget. Ez a lépés manuálisan elindítja a logikai alkalmazást.

1. Küldjön egy vagy több mellékletet tartalmazó e-mailt az ebben a példában használt e-mail-fiókba.

   Ez a lépés a logikai alkalmazás eseményindítóját indítja el, amely létrehozza és futtatja a logikai alkalmazás munkafolyamatának példányát. Ennek eredményeképpen a logikai alkalmazás elküld egy üzenetet vagy e-mailt, amely az elküldött e-mailben szereplő mellékletek számát jeleníti meg.

Ha a tervezőből a Code View Editor-szerkesztőre vált, itt látható, hogy a **minden** hurok **megjelenik a logikai** alkalmazás definíciójában, amely JSON formátumú.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Változó csökkentése

Ha egy változót állandó értékkel szeretne *csökkenteni vagy csökkenteni* , kövesse a [változó növelésének](#increment-value) lépéseit, kivéve, ha megtalálta, majd válassza a **változó csökkentése** műveletet. Ez a művelet csak egész és lebegőpontos változók esetén működik.

Itt láthatók a **változó csökkentése** művelet tulajdonságai:

| Tulajdonság | Kötelező | Érték |  Leírás |
|----------|----------|-------|--------------|
| **Név** | Yes | <*változó – név*> | A csökkentő változó neve | 
| **Érték** | No | <*növekmény – érték*> | A változó csökkentésének értéke. Az alapértelmezett érték egy. <p><p>**Tipp**: bár nem kötelező, állítsa be ezt az értéket ajánlott eljárásként, hogy mindig tisztában legyen a változó csökkentése érdekében megadott értékkel. |
||||| 

Ha a tervezőből a kód nézet szerkesztőjére vált, a **változó csökkentése** művelet a logikai alkalmazás definíciójában jelenik meg, amely JSON formátumú.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Változó beállítása

Ha egy másik értéket szeretne hozzárendelni egy meglévő változóhoz, kövesse a [változók növelésének](#increment-value) lépéseit, kivéve, ha:

1. Keresse meg és válassza ki a **változó beállítása** műveletet.

1. Adja meg a hozzárendelni kívánt változó nevét és értékét. Az új értéknek és a változónak azonos adattípussal kell rendelkeznie. Az érték megadása kötelező, mert ez a művelet nem rendelkezik alapértelmezett értékkel.

Az alábbi tulajdonságok a **set változóra** vonatkozó művelet tulajdonságai:

| Tulajdonság | Kötelező | Érték |  Leírás |
|----------|----------|-------|--------------|
| **Név** | Yes | <*változó – név*> | A módosítandó változó neve |
| **Érték** | Yes | <*új érték*> | Az érték, amelyet hozzá kíván rendelni a változóhoz. Mindkettőnek azonos adattípussal kell rendelkeznie. |
||||| 

> [!NOTE]
> Ha nem növeli vagy csökkenti a változókat, akkor a hurkok módosítása nem várt eredményt *eredményezhet* , mert a hurkok párhuzamosan futnak, vagy egyidejűleg, alapértelmezés szerint. Ezekben az esetekben próbálja meg egymás után futtatni a hurok értékét. Ha például hivatkozni szeretne a változó értékére a hurokban, és a Loop-példány elején és végén ugyanazt az értéket szeretné használni, kövesse az alábbi lépéseket a hurok futtatásának megváltoztatásához: 
>
> 1. A hurok jobb felső sarkában kattintson a három pont (**..**.) gombra, majd válassza a **Beállítások** lehetőséget.
> 
> 2. A **Egyidejűség vezérlőelem** alatt módosítsa a **felülbírálás alapértelmezett** beállítását be **értékre.**
>
> 3. Húzza a **párhuzamossági fok** csúszkát **1-re**.

Ha a tervezőből a kód nézet szerkesztőjére vált, a **set változó** művelet a logikai alkalmazás definíciójában jelenik meg, amely JSON formátumú. Ez a példa megváltoztatja a `Count` változó aktuális értékét egy másik értékre.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Hozzáfűzés változóhoz

Karakterláncokat vagy tömböket tároló változók esetén a változók értékét beszúrhatja vagy *hozzáfűzheti* a karakterláncok és tömbök utolsó elemeként. Követheti a [változó növelésének](#increment-value) lépéseit, kivéve, ha az alábbi lépéseket követi: 

1. Keresse meg és válassza ki az alábbi műveletek egyikét attól függően, hogy a változó sztring vagy tömb: 

   * **Hozzáfűzés karakterlánc-változóhoz**
   * **Hozzáfűzés a tömb változóhoz** 

1. Adja meg a karakterlánc vagy tömb utolsó elemeként hozzáfűzni kívánt értéket. Kötelezően megadandó érték.

A Hozzáfűzés a következőhöz: **...** műveletekhez tartozó tulajdonságok:

| Tulajdonság | Kötelező | Érték |  Leírás |
|----------|----------|-------|--------------|
| **Név** | Yes | <*változó – név*> | A módosítandó változó neve |
| **Érték** | Yes | <*Hozzáfűzés – érték*> | A hozzáfűzni kívánt érték, amely bármilyen típusú lehet |
|||||

Ha a tervezőből a kód nézet szerkesztőjére vált, a **Hozzáfűzés a tömbhöz változó** művelet a logikai alkalmazás definíciójában jelenik meg, amely JSON formátumú. Ez a példa egy tömb változót hoz létre, és egy másik értéket helyez el a tömb utolsó elemeként. Az eredmény egy olyan frissített változó, amely tartalmazza ezt a tömböt: `[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [Logic apps-összekötőről](../connectors/apis-list.md)
