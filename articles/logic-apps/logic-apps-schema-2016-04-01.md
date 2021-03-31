---
title: Schema Updates június-1-2016
description: A 2016-06-01-es verzió a Logic app-definíciók esetében Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000317"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Séma frissítései Azure Logic Apps – június 1-től 2016

A Azure Logic Apps [frissített sémája](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) és API-verziója olyan kulcsfontosságú funkciókat tartalmaz, amelyek megbízhatóbb és könnyebben használhatóvá teszik a logikai alkalmazásokat:

* A [hatókörök](#scopes) lehetővé teszik műveletek gyűjteményként történő csoportosítását vagy beágyazását.
* A [feltételek és a hurkok](#conditions-loops) mostantól az első osztályú műveletek.
* Pontosabb sorrend a műveletek futtatásához a `runAfter` tulajdonsággal, lecserélve `dependsOn`

A logikai alkalmazások 2015-as augusztus 1-től a 2016-es verzióra való frissítéséhez tekintse [meg a frissítés szakaszt](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Hatókörök

Ez a séma hatóköröket tartalmaz, amelyek lehetővé teszik a műveletek együttes csoportosítását, vagy egymáson belüli műveletek beágyazását. Egy feltétel például tartalmazhat egy másik feltételt is. További tudnivalók a [hatókör szintaxisáról](./logic-apps-control-flow-loops.md), vagy az alapvető hatókör áttekintése:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Feltételek és hurkok változásai

A korábbi sémák verzióiban a feltételek és a hurkok egyetlen művelethez társított paraméterek voltak. Ez a séma feloldja ezt a korlátozást, így a feltételek és a hurkok mostantól művelettípusként érhetők el. További információ a [hurkok és hatókörökről](./logic-apps-control-flow-loops.md), a [feltételekről](../logic-apps/logic-apps-control-flow-conditional-statement.md), illetve az alapvető példa, amely egy feltétel műveletét mutatja be:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>"runAfter" tulajdonság

A `runAfter` tulajdonság lecserélésekor `dependsOn` nagyobb pontosságot biztosít, ha az előző műveletek állapota alapján adja meg a műveletek futtatási sorrendjét. A `dependsOn` tulajdonság azt jelzi, hogy "a művelet futott és sikeres volt-e", attól függően, hogy az előző művelet sikeres volt-e, sikertelen vagy kihagyott-e, nem a művelet futtatásához szükséges idő. A `runAfter` tulajdonság rugalmasságot biztosít olyan objektumként, amely az összes művelet nevét adja meg, amely után az objektum fut. Ez a tulajdonság határozza meg az eseményindítóként elfogadható állapotok tömbjét is. Ha például azt szeretné, hogy egy művelet sikeres művelet után fusson, és a B művelet sikeres vagy sikertelen volt-e, állítsa be a következő `runAfter` tulajdonságot:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>A séma frissítése

A [legújabb sémára](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)való frissítéshez csak néhány lépést kell elvégeznie. A frissítési folyamat magában foglalja a Frissítési parancsfájl futtatását, az új logikai alkalmazásként való mentést, és ha szeretné, az előző logikai alkalmazás felülírását.

1. A Azure Portal nyissa meg a logikai alkalmazást.

2. Ugrás az **áttekintéshez**. A logikai alkalmazás eszköztárán válassza a **séma frissítése** lehetőséget.
   
   ![Válasszon frissítési sémát][1]
   
   A rendszer a frissített definíciót adja vissza, amely szükség esetén átmásolható és beilleszthető egy erőforrás-definícióba. 

   > [!IMPORTANT]
   > *Győződjön meg arról, hogy* a **Mentés másként** lehetőséget választja, így a kapcsolatok összes hivatkozása érvényes marad a frissített logikai alkalmazásban.

3. A frissítés panel eszköztárán válassza a **Mentés másként** lehetőséget.

4. Adja meg a logikai nevet és az állapotot. A frissített logikai alkalmazás üzembe helyezéséhez válassza a **Létrehozás** lehetőséget.

5. Ellenőrizze, hogy a frissített logikai alkalmazás a várt módon működik-e.
   
   > [!NOTE]
   > Ha kézi vagy kérelem típusú triggert használ, a visszahívási URL-cím módosul az új logikai alkalmazásban. Tesztelje az új URL-címet, és győződjön meg arról, hogy a végpontok közötti élmény működik. Az előző URL-címek megőrzése érdekében a meglévő logikai alkalmazás klónozása is megtörténik.

6. Nem *kötelező* Ha felül szeretné írni az előző logikai alkalmazást az új séma verziójával, akkor az eszköztáron válassza a **klónozás** lehetőséget a **séma frissítése** elem mellett. Ez a lépés csak akkor szükséges, ha meg szeretné tartani ugyanazt az erőforrás-azonosítót vagy a logikai alkalmazás trigger URL-címét.

## <a name="upgrade-tool-notes"></a>Frissítési eszközök megjegyzései

### <a name="mapping-conditions"></a>Leképezési feltételek

A frissített definícióban az eszköz a legjobb erőfeszítést teszi az igaz és a hamis ág-műveletek hatókörként való csoportosításához. Pontosabban, a tervező mintázata `@equals(actions('a').status, 'Skipped')` `else` műveletként jelenik meg. Ha azonban az eszköz nem felismerhető mintákat észlel, az eszköz külön feltételeket hozhat létre az igaz és a hamis ág esetében is. Ha szükséges, újra felveheti a műveleteket a frissítés után.

#### <a name="foreach-loop-with-condition"></a>"foreach" hurok feltétellel

Az új sémában a szűrés művelettel replikálhatja azt a mintát, amely **minden** hurokhoz egy feltétellel rendelkező hurkot használ. A módosítás azonban automatikusan megtörténik a frissítéskor. A feltétel egy szűrési művelet lesz, amely az **egyes** ciklusok előtt jelenik meg, és csak a feltételnek megfelelő elemek tömbjét adja vissza, és a tömböt az **egyes** műveletekhez adja át. Példa: [hurkok és hatókörök](./logic-apps-control-flow-loops.md).

### <a name="resource-tags"></a>Erőforráscímkék

A frissítés után a rendszer eltávolítja az erőforrás-címkéket, ezért alaphelyzetbe kell állítania a frissített munkafolyamatot.

## <a name="other-changes"></a>További változások

### <a name="renamed-manual-trigger-to-request-trigger"></a>A "Manual" trigger átnevezve a "Request" triggerre

Az `manual` trigger típusa elavult, és a típusra lett átnevezve `request` `http` . Ez a változás nagyobb konzisztenciát eredményez a trigger által felépített minta típusához képest.

### <a name="new-filter-action"></a>Új "szűrő" művelet

Ha egy nagyméretű tömböt kisebb készletekre szeretne szűrni, az új `filter` típus fogad egy tömböt és egy feltételt, kiértékeli az egyes elemek feltételeit, és egy tömböt ad vissza, amelyben a feltételnek megfelelő elemek szerepelnek.

### <a name="restrictions-for-foreach-and-until-actions"></a>A "foreach" és a "ig" műveletre vonatkozó korlátozások

A `foreach` és a `until` hurok egyetlen műveletre korlátozódik.

### <a name="new-trackedproperties-for-actions"></a>Új "trackedProperties" a műveletekhez

A műveletek most már rendelkezhetnek további tulajdonsággal `trackedProperties` , amely a testvér a `runAfter` és a `type` Tulajdonságok. Ez az objektum a munkafolyamat részeként kibocsátott Azure diagnosztikai telemetria felvenni kívánt műveletek bemeneteit és kimeneteit határozza meg. Például:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Következő lépések

* [Munkafolyamat-definíciók létrehozása logikai alkalmazásokhoz](../logic-apps/logic-apps-author-definitions.md)
* [A logikai alkalmazás üzemelő példányának automatizálása](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
