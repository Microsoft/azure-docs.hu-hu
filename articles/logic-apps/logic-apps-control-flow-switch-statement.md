---
title: Switch-utasítások hozzáadása munkafolyamatokhoz
description: A munkafolyamat-műveleteket vezérlő kapcsolói utasítások létrehozása a Azure Logic Apps adott értékei alapján
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657460"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Munkafolyamat-műveleteket futtató kapcsolói utasítások létrehozása a Azure Logic Apps adott értékei alapján

Ha meghatározott műveleteket szeretne futtatni az objektumok, kifejezések vagy tokenek értékei alapján, adjon hozzá egy *switch* utasítást. Ez a struktúra kiértékeli az objektumot, a kifejezést vagy a tokent, kiválasztja az eredménynek megfelelő esetet, és csak adott esetben futtat adott műveleteket. Ha a Switch utasítás fut, csak egy esetnek kell megegyeznie az eredménnyel.

Tegyük fel például, hogy egy olyan logikai alkalmazást szeretne, amely különböző lépéseket hajt végre az e-mailben kiválasztott beállítás alapján. Ebben a példában a logikai alkalmazás egy webhely RSS-hírcsatornájában ellenőrzi az új tartalmat. Ha új elem jelenik meg az RSS-hírcsatornában, a logikai alkalmazás e-mailt küld egy jóváhagyónak. Attól függően, hogy a jóváhagyó kiválasztja-e a "jóváhagyás" vagy az "elutasítás" lehetőséget, a logikai alkalmazás különböző lépéseket követ.

> [!TIP]
> Az összes programozási nyelvhez hasonlóan a Switch utasítások csak az esélyegyenlőségi operátorokat támogatják. Ha más, például "nagyobb, mint" kapcsolatot igénylő operátorokra van szüksége, használjon [feltételes utasítást](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> A determinisztikus végrehajtásának biztosítása érdekében az eseteknek egyedi és statikus értéket kell tartalmazniuk dinamikus jogkivonatok vagy kifejezések helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A jelen cikkben szereplő példának megfelelően [hozza létre ezt a minta logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com-fiókkal vagy egy munkahelyi vagy iskolai fiókkal.

  1. Ha hozzáadja a műveletet az e-mailek küldéséhez, keresse meg és válassza ki ezt a műveletet: **jóváhagyó E-mail küldése**

     ![Válassza a "jóváhagyó e-mail küldése" lehetőséget.](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Adja meg a kötelező mezőket, például a jóváhagyási e-mailt küldő személy e-mail-címét. 
  A **felhasználói beállítások** alatt adja meg a "jóváhagyás, elutasítás" lehetőséget.

     ![Adja meg az e-mail adatait](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Switch-utasítás hozzáadása

1. Ebben a példában adjon hozzá egy switch utasítást a minta munkafolyamatának végén. Az utolsó lépés után válassza az **új lépés** lehetőséget.

   Ha a lépések között egy switch utasítást szeretne hozzáadni, vigye a mutatót arra a nyílra, ahová a Switch utasítást hozzá szeretné adni. Válassza ki a megjelenő **pluszjelet** ( **+** ), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "Switch" kifejezést. Válassza ki ezt a műveletet: **switch-Control**

   ![Kapcsoló hozzáadása](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Egy switch utasítás egy esettel és egy alapértelmezett esettel jelenik meg. 
   Alapértelmezés szerint a Switch utasításhoz legalább egy esetnek, valamint az alapértelmezett esetnek kell lennie. 

   ![Üres alapértelmezett Switch utasítás](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Kattintson a **bekapcsolva** lehetőségre, hogy megjelenjen a dinamikus tartalmak listája. Ebből a listából válassza ki azt a **SelectedOption** mezőt, amelynek kimenete meghatározza a végrehajtandó műveletet. 

   ![Válassza a "SelectedOption" lehetőséget](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Az esetek, amikor a jóváhagyó kiválasztja `Approve` vagy `Reject` , adjon hozzá egy másik esetet az **eset** és az **alapértelmezett érték** között. 

   ![Újabb eset hozzáadása](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adja hozzá ezeket a műveleteket a megfelelő esetekhez:

   | Esetben # | **SelectedOption** | Művelet |
   |--------|--------------------|--------|
   | 1. eset | **Jóváhagyás** | Adja hozzá az Outlook **E-mail küldése** műveletet, amely csak akkor küldi el az RSS-elem részleteit, ha a jóváhagyó a **jóváhagyást** választotta. |
   | 2. eset | **Elutasítás** | Adja hozzá az Outlook **E-mail küldése** műveletet az RSS-elem elutasításához szükséges más jóváhagyók értesítéséhez. |
   | Alapértelmezett | Nincsenek | Nincs szükség beavatkozásra. Ebben a példában az **alapértelmezett** eset üres, mert a **SelectedOption** csak két lehetőséggel rendelkezik. |
   |||

   ![Befejezett switch utasítás](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Mentse a logikai alkalmazást. 

   A példa manuális teszteléséhez válassza a **Futtatás** lehetőséget, amíg a logikai alkalmazás nem talál új RSS-elemet, és elküld egy jóváhagyó e-mailt. 
   Az eredmények megfigyeléséhez válassza a **jóváhagyás** lehetőséget.

## <a name="json-definition"></a>JSON-definíció

Most, hogy egy switch utasítás használatával létrehozott egy logikai alkalmazást, nézzük meg a Switch utasítás mögötti magas szintű kód definícióját.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Címke | Description |
|-------|-------------|
| `"Switch"`         | A Switch utasítás neve, amelyet átnevezni lehet az olvashatóság érdekében |
| `"type": "Switch"` | Megadja, hogy a művelet egy switch utasítás |
| `"expression"`     | Ebben a példában a jóváhagyó kiválasztott beállítását adja meg, amelyet a rendszer a definícióban később deklarált minden esetben kiértékel. |
| `"cases"` | Tetszőleges számú esetet határoz meg. `"Case_*"`Ebben az esetben az alapértelmezett név, amelyet átnevezheti az olvashatóság érdekében |
| `"case"` | Meghatározza az eset értékét, amelynek állandó és egyedi értéknek kell lennie, amelyet a Switch utasítás az összehasonlításhoz használ. Ha egyetlen eset sem felel meg a kapcsoló kifejezés eredményének, a `"default"` szakasz műveletei futnak. | 
| | | 

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A funkciókra vagy javaslatokra való beküldéshez vagy szavazáshoz látogasson el a [Azure Logic apps felhasználói visszajelzési webhelyre](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása feltételek alapján (feltételes utasítások)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtatási és ismétlési lépések (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveleti állapot alapján (hatókörök)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
