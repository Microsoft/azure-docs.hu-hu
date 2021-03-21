---
title: Logic app JSON munkafolyamat-definíciók létrehozása, szerkesztése vagy kiterjesztése
description: A logikai alkalmazás JSON munkafolyamat-definícióinak írása, szerkesztése és kiterjesztése Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014397"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Logikai alkalmazásokhoz készült JSON-munkafolyamatdefiníciók létrehozása, szerkesztése, vagy kiterjesztése az Azure Logic Appsben

Ha [Azure Logic apps](../logic-apps/logic-apps-overview.md)-ben automatizált munkafolyamatokkal rendelkező vállalati integrációs megoldásokat hoz létre, az alapul szolgáló logikai alkalmazás-definíciók egyszerű és deklaratív JavaScript Object Notation (JSON) és a [munkafolyamat-definíciós nyelv (WDL) sémáját](../logic-apps/logic-apps-workflow-definition-language.md) használják a leíráshoz és az érvényesítéshez. Ezek a formátumok a Logic app-definíciók könnyebben olvashatók és megérthetők, és a kód ismerete nélkül.
Ha automatizálni szeretné a logikai alkalmazások létrehozását és üzembe helyezését, [Azure Resource Manager-sablonokban](../azure-resource-manager/templates/overview.md)található [Azure-erőforrásokként](../azure-resource-manager/management/overview.md) is hozzáadhat logikai alkalmazás-definíciókat.
A Logic apps létrehozásához, kezeléséhez és üzembe helyezéséhez használhatja a [Azure PowerShell](/powershell/module/az.logicapp), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)vagy a [Azure Logic apps REST API-kat](/rest/api/logic/).

Ha a Logic app-definíciókat a JSON-ban szeretné használni, nyissa meg a kód nézet szerkesztőjét, ha a Azure Portal vagy a Visual Studióban dolgozik, vagy másolja a definíciót a kívánt szerkesztőbe.
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [az első logikai alkalmazás létrehozását ismertető témakört](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Egyes Azure Logic Apps képességek, például a paraméterek definiálása és a Logic app-definíciók több triggere csak JSON-ban érhető el, nem pedig a Logic Apps Designerben.
> Így ezekhez a feladatokhoz a kód nézetben vagy egy másik szerkesztőben kell dolgoznia.

## <a name="edit-json---azure-portal"></a>JSON szerkesztése – Azure Portal

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget.
A keresőmezőbe keresse meg a "Logic apps" kifejezést, majd az eredmények közül válassza ki a logikai alkalmazást.

3. A logikai alkalmazás menüjében, a **fejlesztői eszközök** területen válassza a **logikai alkalmazás kódjának nézetét**.

   Megnyílik a kód nézet szerkesztője, és a logikai alkalmazás definícióját JSON formátumban jeleníti meg.

## <a name="edit-json---visual-studio"></a>JSON szerkesztése – Visual Studio

Mielőtt a Logic app-definícióját a Visual Studióban tudja dolgozni, győződjön meg arról, hogy [telepítette a szükséges eszközöket](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Logikai alkalmazás Visual Studióval való létrehozásához tekintse át a következő útmutatót [: feladatok és folyamatok automatizálása Azure Logic apps-Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

A Visual Studióban megnyithatja azokat a Logic apps-alkalmazásokat, amelyeket közvetlenül a Azure Portal vagy a Visual studióból Azure Resource Manager projects használatával hoztak létre és helyeztek üzembe.

1. Nyissa meg a Visual Studio-megoldást vagy az [Azure erőforráscsoport](../azure-resource-manager/management/overview.md) -projektet, amely tartalmazza a logikai alkalmazást.

2. Keresse meg és nyissa meg a logikai alkalmazás definícióját, amely alapértelmezés szerint a **LogicApp.js** nevű [Resource Manager-sablonban](../azure-resource-manager/templates/overview.md)jelenik meg.
A sablont a különböző környezetekben való üzembe helyezéshez használhatja és testreszabhatja.

3. Nyissa meg a logikai alkalmazás definíciójának és sablonjának helyi menüjét.
Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**.

   ![Logikai alkalmazás megnyitása egy Visual Studio-megoldásban](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

4. A tervező alján kattintson a **kód nézet** elemre.

   Megnyílik a kód nézet szerkesztője, és a logikai alkalmazás definícióját JSON formátumban jeleníti meg.

5. A tervező nézetbe való visszatéréshez a kód nézet szerkesztőjének alján válassza a **tervezés** lehetőséget.

## <a name="parameters"></a>Paraméterek

Az üzembe helyezési életciklus általában különböző környezetekkel rendelkezik fejlesztési, tesztelési, előkészítési és éles környezetben. Ha olyan értékekkel rendelkezik, amelyeket a logikai alkalmazásban rögzítjük nélkül kíván használni, vagy amelyek a telepítési igényektől függően változnak, létrehozhat egy [Azure Resource Manager sablont](../azure-resource-manager/management/overview.md) a munkafolyamat-definícióhoz, hogy automatizálható legyen a logikai alkalmazások telepítése is.

Az alábbi általános lépések végrehajtásával *parametrizálja*, vagy definiálhatja és használhatja a paramétereket az értékekhez. Ezután megadhatja az értékeket egy külön paraméter-fájlban, amely átadja ezeket az értékeket a sablonnak. Ily módon a logikai alkalmazás frissítése és újbóli üzembe helyezése nélkül is könnyebben módosíthatja ezeket az értékeket. A részletekért lásd [: Áttekintés: az üzembe helyezés automatizálása a Logic apps alkalmazásokhoz Azure Resource Manager-sablonokkal](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. A sablonban definiálja a sablon paramétereit és a munkafolyamat-definíciós paramétereket, hogy elfogadják az üzembe helyezéskor és a futtatókörnyezetben használandó értékeket.

   A sablon paraméterei a munkafolyamat-definíción kívüli paraméterek szakaszban vannak definiálva, míg a munkafolyamat-definíciós paraméterek a munkafolyamat-definícióban található paraméterek szakaszban vannak meghatározva.

1. Cserélje le a hardcoded értékeket azokra a kifejezésekre, amelyek hivatkoznak ezekre a paraméterekre. A sablon kifejezései olyan szintaxist használnak, amely eltér a munkafolyamat-definíciós kifejezéstől.

   Kerülje a kód bonyolultságát úgy, hogy nem használ sablon-kifejezéseket, amelyeket a rendszer az üzembe helyezéskor értékel ki a munkafolyamat-definíciós kifejezéseken belül, amelyek kiértékelése futásidőben történik. Csak a munkafolyamat-definíción kívüli sablon-kifejezéseket használja. Csak munkafolyamat-definíciós kifejezéseket használjon a munkafolyamat-definícióban.

   A munkafolyamat-definíciós paraméterek értékeinek megadásakor a rendszer a munkafolyamat-definíción kívüli paraméterek szakaszt használva hivatkozhat a sablon paramétereinek használatára, de továbbra is a logikai alkalmazás erőforrás-definícióján belül marad. Ily módon átadhatja a sablon paramétereinek értékeit a munkafolyamat-definíciós paraméterekbe.

1. Tárolja a paraméterek értékeit egy külön paraméter- [fájlban](../azure-resource-manager/templates/parameter-files.md) , és adja hozzá a fájlt az üzembe helyezéshez.

## <a name="process-strings-with-functions"></a>Karakterláncok feldolgozása függvényekkel

A Logic Apps számos funkcióval rendelkezik a karakterláncok használatáról.
Tegyük fel például, hogy egy vállalat nevét szeretné átadni egy rendelésről egy másik rendszerre.
Azonban nem biztos benne, hogy a karakterkódolás megfelelő kezelést használ.
Ezt a karakterláncot Base64 kódolással is elvégezheti, de az URL-cím megkerülésének elkerüléséhez Ehelyett több karaktert is lecserélhet. Emellett csak egy alsztringre van szükség a vállalat nevéhez, mert az első öt karakter nincs használatban.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Ezek a lépések azt írják le, hogy a példa hogyan dolgozza fel ezt a karakterláncot, a belsőről a külsőre:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Szerezze [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) be a vállalat nevét, így megkapja a karakterek teljes számát.

2. Egy rövidebb sztring levonásához `5` .

3. Most szerezzen be egy [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) .
Indítsa el az indexet `5` , és ugorjon a karakterlánc hátralévő részére.

4. Az alkarakterlánc konvertálása [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) karakterlánccá.

5. Most már minden karakterrel [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) `+` rendelkező karakter szerepel `-` .

6. Végül [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) az összes karakterrel `/` rendelkező karakter `_` .

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Listaelemek listázása tulajdonságértékek alapján, majd a Maps használata paraméterekként

Ha a tulajdonságok értéke alapján különböző eredményeket szeretne kapni, létrehozhat egy olyan térképet, amely megfelel az egyes tulajdonságoknak egy adott eredménynek, majd ezt a térképet használja paraméterként.

Ez a munkafolyamat például definiál bizonyos kategóriákat paraméterekként, valamint egy olyan térképet, amely megfelel az adott URL-címmel rendelkező kategóriáknak.
Először a munkafolyamat lekéri a cikkek listáját. Ezután a munkafolyamat a térképet használva megkeresi az egyes cikkekhez tartozó kategóriának megfelelő URL-címet.

*   A [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) függvény ellenőrzi, hogy a kategória megfelel-e egy ismert kategóriának.

*   A megfelelő kategória beszerzése után a példa szögletes zárójelek használatával lekéri az elemet a térképről: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Adatok lekérése a Date functions szolgáltatással

Ha olyan adatforrás adatait szeretné lekérni, amely nem támogatja natív módon az *eseményindítókat*, használja a Date functions szolgáltatást az időpontok és dátumok használatához.
Ez a kifejezés például megkeresi, hogy mennyi ideig tart a munkafolyamat lépései:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. A `order` műveletből bontsa ki a következőt: `startTime` .
2. Az aktuális idő beolvasása `utcNow()` .
3. Egy másodperc kivonása:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Más időegységeket is használhat, például `minutes` vagy `hours` .

3. Most összehasonlíthatja ezt a két értéket.

   Ha az első érték kisebb, mint a második érték, akkor a rendelés első elhelyezése óta a rendszer egynél több másodpercet adott át.

A dátumok formázásához karakterlánc-formázó használhat. A RFC1123 lekéréséhez például használja a következőt: [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md) .
További információ a [dátum formázásáról](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása feltételek alapján (feltételes utasítások)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (switch utasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatási és ismétlési lépések (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveleti állapot alapján (hatókörök)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* További információ a [Azure Logic apps munkafolyamat-definíciós nyelvi sémájáról](../logic-apps/logic-apps-workflow-definition-language.md)
* További információ a [Azure Logic apps munkafolyamat-műveleteiről és-eseményindítóról](../logic-apps/logic-apps-workflow-actions-triggers.md)
