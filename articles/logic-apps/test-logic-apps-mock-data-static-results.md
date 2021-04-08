---
title: Logikai alkalmazások tesztelése próbaadatokkal
description: Statikus eredmények beállítása a logikai alkalmazások tesztelésére az éles környezetek befolyásolása nélkül
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 711d753203aeaeba50cea692053a37fcab2e9c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027703"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Logic Apps-alkalmazások tesztelése statikus eredmények beállításával

A logikai alkalmazások tesztelésekor előfordulhat, hogy nem áll készen az alkalmazások, szolgáltatások és rendszerek tényleges hívására és elérésére különböző okokból. Általában ezekben a forgatókönyvekben előfordulhat, hogy eltérő feltételi elérési utakat kell futtatnia, hibákat kell megadnia, adott válaszüzeneteket kell megadnia, vagy akár néhány lépés kihagyása is. Ha statikus eredményeket állít be egy művelethez a logikai alkalmazásban, az adott műveletből kiválaszthatja a kimeneti adatokat. Egy művelet statikus eredményeinek engedélyezése nem futtatja a műveletet, hanem visszaadja az ál-adatmennyiséget.

Ha például az Outlook 365 küldési műveletének statikus eredményeit állítja be, akkor a Logic Apps motor csak a statikus eredményekként megadott ál-adatokkal tér vissza, és nem hívja meg az Outlookot, és nem küld e-mailt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, amelyben statikus eredményeket szeretne beállítani

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Statikus eredmények beállítása

1. Ha még nem tette meg, a [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic apps Designerben.

1. Hajtsa végre az alábbi lépéseket azon a műveleten, amelynek statikus eredményeit szeretné beállítani: 

   1. A művelet jobb felső sarkában válassza a három pontot (*..*.), és válassza a **statikus eredmény** lehetőséget, például:

      ![Válassza a "statikus eredmény" > "statikus eredmény engedélyezése" lehetőséget.](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Válassza a **statikus eredmény engedélyezése** lehetőséget. A kötelező (*) tulajdonságoknál adja meg a művelet válaszára visszaadni kívánt Mock kimeneti értékeket.

      Például itt láthatók a HTTP-művelet kötelező tulajdonságai:

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Állapot** | A művelet visszatérési állapota |
      | **Állapotkód** | A visszaadni kívánt állapotkód |
      | **Fejlécek** | A visszaadni kívánt fejléc tartalma |
      |||

      ![Válassza a "statikus eredmény engedélyezése" lehetőséget.](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Ha JavaScript Object Notation (JSON) formátumban szeretné megadni az ál-értéket, válassza a **váltás JSON módra** lehetőséget ( ![ válassza a "váltás JSON-módra" lehetőséget ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ).

   1. A választható tulajdonságok lapon nyissa meg a **Választható mezők kiválasztása** listát, és válassza ki a kipróbálni kívánt tulajdonságokat.

      ![Választható tulajdonságok kiválasztása](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Ha készen áll a mentésre, válassza a **kész** lehetőséget.

   A művelet jobb felső sarkában a címsor most egy teszt főzőpohár ikont jelenít meg ( ![ statikus eredmények ikonja ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ), amely azt jelzi, hogy engedélyezte a statikus eredményeket.

   ![Az engedélyezett statikus eredményeket mutató ikon](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   A modelleket használó korábbi futtatások kereséséhez tekintse meg a jelen témakör későbbi, [statikus eredményeket használó futtatások keresése](#find-runs-mock-data) című szakaszát.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Előző kimenetek újrafelhasználása

Ha a logikai alkalmazás rendelkezik egy korábbi futtatással, és a kimenetek modell kimenetként is használható, akkor a kimeneteket másolhatja és beillesztheti a futtatásból.

1. Ha még nem tette meg, a [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic apps Designerben.

1. A logikai alkalmazás főmenüjében válassza az **Áttekintés** lehetőséget.

1. A futtatási **Előzmények** szakaszban válassza ki a kívánt logikai alkalmazást.

1. A logikai alkalmazás munkafolyamatában keresse meg és bontsa ki a kívánt kimenetekkel rendelkező műveletet.

1. Válassza a **nyers kimenetek megjelenítése** hivatkozást.

1. Másolja a teljes JavaScript Object Notation (JSON) objektumot vagy a használni kívánt alszakaszt, például a kimenetek szakaszt, vagy akár csak a fejlécek szakaszt.

1. A [statikus eredmények beállítása](#set-up-static-results)szakasz lépéseit követve megnyithatja a művelet **statikus eredmény** mezőjét.

1. A **statikus eredmény** mező megnyitása után válassza az egyik lépést:

   * A teljes JSON-objektum beillesztéséhez válassza a **váltás JSON-módra** lehetőséget ( ![ válassza a "váltás JSON-módra" lehetőséget ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) ):

     ![A teljes objektumhoz válassza a "váltás JSON-módra" lehetőséget.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Ahhoz, hogy csak egy JSON-szakaszt illesszen be a szakasz felirata mellett, válassza a **váltás JSON-módra** lehetőséget, például:

     ![Válassza a "váltás JSON-módba" lehetőséget a kimenetekhez.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. A JSON-szerkesztőben illessze be a korábban másolt JSON-t.

   ![JSON-mód](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Ha elkészült, válassza a **Kész** lehetőséget. Vagy ha vissza szeretne térni a tervezőhöz, válassza a **váltás szerkesztő üzemmódot** ( ![ válassza a "szerkesztési mód" lehetőséget ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) ).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Statikus eredményeket használó futtatások keresése

A logikai alkalmazás futtatási előzményei azonosítják azokat a futtatásokat, amelyekben a műveletek statikus eredményeket használnak. A futtatások megkereséséhez kövesse az alábbi lépéseket:

1. A logikai alkalmazás főmenüjében válassza az **Áttekintés** lehetőséget. 

1. A jobb oldali ablaktábla **futtatási előzmények** területén keresse meg a **statikus eredmények** oszlopot. 

   Minden olyan Futtatás, amely az eredménnyel rendelkező műveleteket tartalmazza, a **statikus eredmények** oszlop **engedélyezve** értékre van állítva, például:

   ![Futtatási előzmények – statikus eredmények oszlop](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. A statikus eredményeket használó műveletek megtekintéséhez válassza ki azt a futtatást, ahol a **statikus eredmények** oszlop **engedélyezve** értékre van állítva.

   A statikus eredményeket használó műveletek a teszt főzőpohár ( ![ statikus eredmények ikonja ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ) ikont jelenítenek meg, például:

   ![Futtatási előzmények – statikus eredményeket használó műveletek](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Statikus eredmények letiltása

A statikus eredmények kikapcsolása nem dobja el az utolsó beállítás értékeit. Így ha a következő alkalommal kapcsolja be a statikus eredményeket, továbbra is használhatja az előző értékeket.

1. Keresse meg azt a műveletet, ahol le szeretné tiltani a statikus kimeneteket. A művelet jobb felső sarkában válassza a teszt főzőpohár ikont ( ![ statikus eredmények ikonja ](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png) ).

   ![A képernyőképen egy H T T P művelet látható, ahol kiválaszthatja a teszt főzőpohár ikonját.](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Válassza a **statikus eredmény letiltása** lehetőséget  >  .

   ![A képernyőfelvételen a statikus eredmény letiltása lehetőség látható.](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referencia

További információ erről a beállításról a mögöttes munkafolyamat-definíciókban: [statikus eredmények – séma-hivatkozás a munkafolyamat-definíciós nyelvhez](../logic-apps/logic-apps-workflow-definition-language.md#static-results) és [runtimeConfiguration. staticResult – futásidejű konfigurációs beállítások](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Logic apps](../logic-apps/logic-apps-overview.md)