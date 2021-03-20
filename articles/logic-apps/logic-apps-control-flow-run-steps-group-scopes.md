---
title: Műveletek csoportosítása és futtatása hatókör szerint
description: A csoport állapota alapján futó hatókörön belüli műveletek létrehozása Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92314844"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Műveletek futtatása a csoport állapota alapján a Azure Logic Apps hatókörök használatával

Ha csak a műveletek egy másik csoportjának sikeres vagy sikertelen végrehajtása után szeretne műveleteket futtatni, csoportosítsa ezeket a műveleteket egy *hatókörön* belül. Ez a struktúra akkor hasznos, ha a műveleteket logikai csoportként szeretné szervezni, kiértékeli a csoport állapotát, és végrehajtja a hatókör állapotán alapuló műveleteket. A hatókör összes műveletének futása után a hatókör a saját állapotát is lekéri. Például hatóköröket alkalmazhat, ha a [kivételt és a hibakezelés](../logic-apps/logic-apps-exception-handling.md#scopes)megvalósítását szeretné végrehajtani. 

A hatókör állapotának vizsgálatához használhatja a logikai alkalmazások futtatási állapotának meghatározására használt feltételeket, például **sikeres**, **sikertelen**, **megszakított** és így tovább. Alapértelmezés szerint ha a hatókör összes művelete sikeres, a hatókör állapota **sikeresként** van megjelölve. Ha azonban a hatókörben lévő bármelyik művelet meghiúsul vagy meg lett szakítva, a hatókör állapota **nem** megfelelőként van megjelölve. A hatókörök korlátozásait lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

Íme például egy olyan magas szintű logikai alkalmazás, amely egy hatókör használatával futtat adott műveleteket és egy feltételt a hatókör állapotának vizsgálatához. Ha a hatókörben lévő műveletek végrehajtása sikertelen vagy váratlanul véget ért, akkor a hatókör **hibásként** van megjelölve vagy **megszakítva** , és a logikai alkalmazás "hatóköre sikertelen" üzenetet küld. Ha az összes hatókörrel rendelkező művelet sikeres, a logikai alkalmazás "hatókör sikeres" üzenetet küld.

![A diagram a Logic app-hatókör folyamatát mutatja be a "hatókör sikertelen" és a "hatókör sikeres" értékkel.](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő példához a következő elemek szükségesek:

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Logic Apps által támogatott e-mail-szolgáltatótól származó e-mail-fiók. Ez a példa az Outlook.com-t használja. Ha más szolgáltatót használ, az általános folyamat ugyanaz marad, de a felhasználói felülete eltérő lesz.

* Egy Bing Maps-kulcs. A kulcs beszerzéséhez tekintse meg <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">a Bing Maps-kulcs beszerzése</a>című témakört.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Minta logikai alkalmazás létrehozása

Először hozza létre ezt a minta logikai alkalmazást, hogy később is hozzá lehessen adni egy hatókört:

![Minta logikai alkalmazás létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Egy **ütemezett ismétlődési** eseményindító, amely a Bing Maps szolgáltatást egy megadott intervallumban ellenőrzi
* Egy **Bing Maps – útvonal lekérése** művelet, amely a két helyszín közötti utazási időt ellenőrzi
* Feltételes utasítás, amely ellenőrzi, hogy az utazási idő meghaladja-e a megadott utazási időt
* Egy művelet, amely e-mailt küld az aktuális utazási idő meghaladja a megadott időt

A logikai alkalmazást bármikor mentheti, így gyakran mentheti a munkáját.

1. Ha még nem tette meg, jelentkezzen be a <a href="https://portal.azure.com" target="_blank">Azure Portalba</a>. Üres logikai alkalmazás létrehozása.

1. Adja hozzá az **ütemterv-ismétlődési** eseményindítót a következő beállításokkal: **Interval** = "1" és **gyakoriság** = "minute"

   ![A "Schedule-Ismétlődés" eseményindító beállítása](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Ha szeretné vizuálisan leegyszerűsíteni a nézetet, és elrejti az egyes műveletek részleteit a tervezőben, az egyes műveletek alakzatait összecsukhatja az alábbi lépések végrehajtásával.

1. Adja hozzá a **Bing Maps – útvonal lekérése** műveletet. 

   1. Ha még nem rendelkezik Bing Maps-kapcsolódással, a rendszer megkéri, hogy hozzon létre egy-egy kapcsolódást.

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Kapcsolat neve** | BingMapsConnection | Adja meg a kapcsolat nevét. | 
      | **API-kulcs** | <*a-Bing-Maps-Key*> | Adja meg a korábban kapott Bing Térképek-kulcsot. | 
      ||||  

   1. Állítsa be az **Útvonal lekérése** műveletet úgy, ahogy az a következő táblázatban látható:

      ![A "Bing Maps – útvonal lekérése" művelet beállítása](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      További információk ezekről a paraméterekről: [Útvonal kiszámítása](/bingmaps/rest-services/routes/calculate-a-route).

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **1. útvonalpont** | <*Start*> | Adja meg az útvonal forrását. | 
      | **2. útvonalpont** | <*végén*> | Adja meg az útvonal célhelyét. | 
      | **Elkerülés** | Nincsenek | Adja meg azokat az elemeket, amelyeket el szeretne kerülni az útvonalon, például autópályákon, útdíjon és így tovább. A lehetséges értékekért lásd: [útvonal kiszámítása](/bingmaps/rest-services/routes/calculate-a-route). | 
      | **Optimalizálása** | timeWithTraffic | Válasszon egy paramétert az útvonal optimalizálásához, például a távolságot, az aktuális forgalmi információkkal rendelkező időt és így tovább. Ez a példa a következő értéket használja: "timeWithTraffic" | 
      | **Távolság mértékegysége** | <*saját preferencia*> | Adja meg az útvonal kiszámításához használandó távolsági egységet. Ez a példa a következő értéket használja: "Mile" | 
      | **Közlekedési mód** | Vezetés | Adja meg az útvonal utazási módját. Ez a példa a "vezetés" értéket használja. | 
      | **Tranzit dátuma és időpontja** | Nincsenek | Csak az átviteli módra vonatkozik. | 
      | **Tranzit Date-Type típusa** | Nincsenek | Csak az átviteli módra vonatkozik. | 
      ||||  

1. [Adjon hozzá egy feltételt](../logic-apps/logic-apps-control-flow-conditional-statement.md) , amely ellenőrzi, hogy az aktuális utazási idő meghaladja-e a megadott időt. 
   Ehhez a példához kövesse az alábbi lépéseket:

   1. Nevezze át a feltételt a következő leírással: **Ha a forgalmi idő meghaladja a megadott időt**

   1. A bal szélső oszlopban kattintson a **válasszon egy értéket** mezőre, hogy megjelenjen a dinamikus tartalmak listája. A listából válassza ki az **utazási időtartam forgalma** mezőt, amely másodpercben van. 

      ![Feltétel létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. A középső mezőben válassza ki ezt az operátort: **nagyobb, mint**

   1. A jobb szélső oszlopban adja meg ezt az összehasonlító értéket (másodpercben), amely 10 percnél egyenlő: **600**

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![Befejezett feltétel](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. A **Ha igaz** ág területen adjon hozzá egy "e-mail küldése" műveletet az e-mail-szolgáltatóhoz. 
   Állítsa be ezt a műveletet a rendszerkép lépéseinek követésével:

   !["E-mail küldése" művelet hozzáadása a "igaz" ág esetén](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. A cél **mezőben adja meg az e** -mail-címét tesztelési célokra.

   1. A **Tárgy** mezőbe írja be a következő szöveget:

      ```Time to leave: Traffic more than 10 minutes```

   1. A **törzs** mezőbe írja be ezt a szöveget egy záró szóközzel: 

      ```Travel time:```

      Amíg a kurzor megjelenik a **törzs** mezőben, a dinamikus tartalom lista nyitva marad, így kiválaszthatja az ezen a ponton elérhető paramétereket.

   1. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.

   1. Keresse meg és válassza ki a **div ()** függvényt. 
      Vigye a kurzort a függvény zárójelbe.

   1. Amíg a kurzor a függvény zárójelei között van, válassza a **dinamikus tartalom** lehetőséget, hogy megjelenjen a dinamikus tartalmak listája. 
   
   1. Az **Útvonal lekérése** szakaszban válassza a **forgalom időtartamának forgalma** mezőt.

      ![Válassza a "forgalom időtartama forgalom" lehetőséget.](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Miután a mező a JSON formátumra lett feloldva, adjon hozzá egy **vesszőt** ( ```,``` ), majd a számot, ```60``` hogy a **forgalom időtartama** másodpercről percre legyen konvertálva. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A kifejezés most a következő példához hasonlít:

      ![Befejezés kifejezés](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Ha elkészült, válassza az **OK** gombot.

   <!-- markdownlint-disable MD038 -->
   1. A kifejezés feloldása után adja hozzá a következő szöveget egy kezdő szóközzel: ``` minutes```
  
       A **törzs** mező most a következő példához hasonlít:

       ![Befejeződött a "törzs" mező](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy hatókört, hogy csoportosítsa az adott műveleteket, és értékelje az állapotukat.

## <a name="add-a-scope"></a>Hatókör hozzáadása

1. Ha még nem tette meg, nyissa meg a logikai alkalmazást a Logic app Designerben. 

1. Adjon hozzá egy hatókört a kívánt munkafolyamat-helyhez. Ha például egy hatókört szeretne hozzáadni a logikai alkalmazás munkafolyamatának meglévő lépései között, kövesse az alábbi lépéseket: 

   1. Vigye az egérmutatót arra a nyílra, amelyhez hozzá szeretné adni a hatókört. 
   Válassza a **pluszjelet** ( **+** ) > a **művelet hozzáadása** lehetőséget.

      ![Hatókör hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. A keresőmezőbe írja be szűrőként a "scope" kifejezést. 
   Válassza ki a **hatókör** műveletet.

## <a name="add-steps-to-scope"></a>Lépések hozzáadása a hatókörhöz

1. Most adja hozzá a lépéseket, vagy húzza a hatókörön belül futtatni kívánt meglévő lépéseket. Ebben a példában húzza ezeket a műveleteket a hatókörbe:
      
   * **Útvonal lekérése**
   * **Ha a forgalmi idő meghaladja a megadott időpontot**, az **igaz** és a **hamis** ágakat is tartalmazza

   A logikai alkalmazás most már a következő példához hasonlóan néz ki:

   ![Hatókör hozzáadva](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. A hatókör területen adjon hozzá egy feltételt, amely ellenőrzi a hatókör állapotát. Nevezze át a feltételt a következő leírással: **Ha a hatókör nem sikerült**

   ![Feltétel hozzáadása a hatókör állapotának vizsgálatához](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. A feltételben adja hozzá ezeket a kifejezéseket, és győződjön meg arról, hogy a hatókör állapota "sikertelen" vagy "megszakított" értékű-e. 

   1. További sor hozzáadásához válassza a **Hozzáadás** lehetőséget. 

   1. Az egyes sorokban kattintson a bal oldali mezőbe, hogy megjelenjen a dinamikus tartalmak listája. 
   A dinamikus tartalom listából válassza a **kifejezés** lehetőséget. A Szerkesztés mezőbe írja be ezt a kifejezést, majd kattintson **az OK gombra**: 
   
      `result('Scope')[0]['status']`

      ![Képernyőkép, amely a "kifejezés" mezőt mutatja a Kiemelt eredmény kifejezéssel.](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Mindkét sorban válassza a **egyenlő** , mint az operátor elemet. 
   
   1. Az összehasonlítási értékek esetében az első sorban adja meg a következőt: `Failed` . 
   A második sorban adja meg a értéket `Aborted` . 

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![A hatókör állapotát ellenőrző kifejezés hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Most állítsa be a feltétel `runAfter` tulajdonságát, hogy a feltétel ellenőrizze a hatókör állapotát, és futtatja a későbbi lépésekben definiált egyezési műveletet.

   1. A **Ha a hatókör sikertelen** állapotban van, válassza a **három** pontot (...), majd a **Futtatás a konfigurálás után** lehetőséget.

      ![A "runAfter" tulajdonság konfigurálása](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Válassza ki az összes ilyen hatókör-állapotot: **sikeres**, **sikertelen**, **kihagyva**, és túllépte az **időkorlátot**

      ![Hatókör állapotának kiválasztása](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Ha elkészült, válassza a **Kész** lehetőséget. 
   A feltétel most egy "információ" ikont jelenít meg.

1. Ha az **igaz** és a **hamis** ágak esetében az egyes hatókör-állapotok alapján kívánja végrehajtani a végrehajtandó műveleteket, például e-mailt vagy üzenetet küldhet.

   ![A hatókör állapota alapján végrehajtandó műveletek hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Mentse a logikai alkalmazást.

A kész logikai alkalmazás most már a következő példához hasonlít:

![Befejezett logikai alkalmazás hatókörrel](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>A munka tesztelése

A tervező eszköztárán válassza a **Futtatás** lehetőséget. Ha az összes hatókörrel rendelkező művelet sikeres, a "hatókör sikeres" üzenet jelenik meg. Ha bármelyik hatókörrel rendelkező művelet nem sikerül, a "hatókör sikertelen" üzenet jelenik meg. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definíció

Ha kód nézetben dolgozik, a logikai alkalmazás JSON-definíciójában megadhat egy hatókör-struktúrát. Például itt látható az előző logikai alkalmazás trigger és műveletek JSON-definíciója:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A szolgáltatásokról és javaslatokról a [Azure Logic apps felhasználói visszajelzéseket ismertető webhelyről](https://aka.ms/logicapps-wish)küldhet vagy szavazhat.

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása feltételek alapján (feltételes utasítások)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (switch utasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatási és ismétlési lépések (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)