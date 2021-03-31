---
title: Adott API-összekötők kapcsolatainak letiltása
description: API-kapcsolatok létrehozásának és használatának korlátozása Azure Logic Appsban
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310036"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Összekötők által létrehozott kapcsolatok blokkolása Azure Logic Apps

Ha a szervezete nem engedélyezi a korlátozott vagy nem jóváhagyott erőforrásokhoz való csatlakozást Azure Logic Apps-összekötők használatával, letilthatja a kapcsolatok létrehozását és használatát a logikai alkalmazás munkafolyamataiban. A [Azure Policy](../governance/policy/overview.md)segítségével megadhatja és érvényesítheti azokat a [házirendeket](../governance/policy/overview.md#policy-definition) , amelyek megakadályozzák a kapcsolatok létrehozását vagy használatát a blokkolni kívánt összekötők számára. Biztonsági okokból például előfordulhat, hogy le kívánja tiltani a kapcsolatokat bizonyos közösségi platformokhoz vagy más szolgáltatásokhoz és rendszerekhez.

Ez a témakör bemutatja, hogyan állíthat be olyan házirendet, amely blokkolja az adott kapcsolatokat a Azure Portal használatával, de más módokon is létrehozhat szabályzat-definíciókat, például az Azure REST API, a Azure PowerShell, az Azure CLI és a Azure Resource Manager sablonokat. További információ: [oktatóanyag: házirendek létrehozása és kezelése a megfelelőség érvényesítéséhez](../governance/policy/tutorials/create-and-manage.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nincs előfizetése, [hozzon létre egy ingyenes Azure-fiókot a](https://azure.microsoft.com/free/) Kezdés előtt.

* A letiltani kívánt összekötő hivatkozási azonosítója. További információ: [az összekötő hivatkozási azonosítójának megkeresése](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Összekötő hivatkozási AZONOSÍTÓjának megkeresése

Ha már van egy logikai alkalmazás a blokkolni kívánt kapcsolatban, kövesse a [Azure Portal lépéseit](#connector-ID-portal). Egyéb esetben kövesse az alábbi lépéseket:

1. Látogasson el a [Logic apps csatlakozók listára](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Keresse meg a letiltani kívánt összekötő hivatkozási oldalát.

   Ha például le szeretné tiltani a Instagram-összekötőt, amely elavult, ugorjon a következő lapra:

   `https://docs.microsoft.com/connectors/instagram/`

1. A lap URL-címéből másolja és mentse az összekötő hivatkozási AZONOSÍTÓját a végén a Forward perjel ( `/` ) nélkül, például: `instagram` .

   Később, amikor létrehozza a házirend-definícióját, ezt az azonosítót használja a definíció feltétel-utasításában, például:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza a **logikai alkalmazás kódja nézetet** , hogy megtekintse a logikai alkalmazás JSON-definícióját.

   ![Az összekötő AZONOSÍTÓjának megkereséséhez nyissa meg a "Logic app Code View" kifejezést](./media/block-connections-connectors/code-view-connector-id.png)

1. Keresse meg az `parameters` objektumot tartalmazó objektumot `$connections` , amely tartalmazza a `{connection-name}` logikai alkalmazás egyes kapcsolatainak objektumait, és megadja az adott kapcsolatban található adatokat:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Az Instagram-összekötő esetében például keresse meg az `instagram` objektumot, amely egy Instagram-csatlakozást azonosít:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. A letiltani kívánt kapcsolatban keresse meg a `id` tulajdonságot és az értéket, amely a következő formátumot követi: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Itt látható például `id` egy Instagram-kapcsolatok tulajdonsága és értéke:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. A `id` tulajdonság értékében másolja és mentse az összekötő hivatkozási azonosítóját a végén, például: `instagram` .

   Később, amikor létrehozza a házirend-definícióját, ezt az azonosítót használja a definíció feltétel-utasításában, például:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Házirend létrehozása a kapcsolatok létrehozásának blokkolásához

Az alábbi lépéseket követve blokkolhatja, hogy a rendszer teljesen létrehozza a kapcsolatokat egy logikai alkalmazásban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A portál keresési mezőjébe írja be a kifejezést `policy` , majd válassza a **házirend** lehetőséget.

   ![A Azure Portalban keresse meg és válassza ki a "házirend" elemet.](./media/block-connections-connectors/find-select-azure-policy.png)

1. A **házirend** menü **szerzői műveletek** területén válassza a **definíciók**  >  **+ szabályzat definíciója** lehetőséget.

   ![Válassza a "definíciók" > "+ házirend-definíció" elemet.](./media/block-connections-connectors/add-new-policy-definition.png)

1. A **házirend-definíció** területen adja meg a házirend-definíció adatait a következő példa alapján bemutatott tulajdonságok alapján:

   ![Képernyőkép, amely a "házirend-definíció" tulajdonságokat jeleníti meg.](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Definíció helye** | Yes | <*Azure-előfizetés – név*> | A házirend-definícióhoz használandó Azure-előfizetés <p><p>1. az előfizetés megkereséséhez kattintson a három pont (**..**.) gombra. <br>2. az **előfizetés** listában keresse meg és válassza ki az előfizetését. <br>3. Ha elkészült, válassza a **kiválasztás** lehetőséget. |
   | **Név** | Yes | <*házirend-definíció – név*> | A házirend-definícióhoz használandó név |
   | **Leírás** | No | <*házirend-definíció – név*> | A házirend-definíció leírása |
   | **Kategória** | Yes | **Logikai alkalmazások** | A házirend-definíció meglévő kategóriájának vagy új kategóriájának neve |
   | **Szabályzatbetartatás** | Yes | **Engedélyezve** | Ezzel a beállítással adható meg, hogy engedélyezi vagy letiltja a házirend-definíciót a munka mentésekor. |
   ||||

1. A **házirend-szabály** területen a JSON-szerkesztő mező előre ki van töltve egy házirend-definíciós sablonnal. Cserélje le ezt a sablont a [házirend-definícióra](../governance/policy/concepts/definition-structure.md) az alábbi táblázatban leírt tulajdonságok alapján, és kövesse a következő szintaxist:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | `mode` | `All` | Az a mód, amely meghatározza a házirend által kiértékelt erőforrás-típusokat. <p><p>Ez a forgatókönyv `mode` a `All` -re vonatkozik, amely a szabályzatot Azure-erőforráscsoportok, előfizetések és minden erőforrástípus esetében alkalmazza. <p><p>További információ: [Policy definition Structure-Mode](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Az a feltétel, amely meghatározza, hogy mikor kell kikényszeríteni a szabályzat szabályát <p><p>Ebben az esetben a `{condition-to-evaluate}` meghatározza, hogy a `api.id` `Microsoft.Web/connections/api.id` egyezések értéke `*managedApis/{connector-name}` , amely a helyettesítő karakter (*) értéket adja meg. <p><p>További információ: [házirend-definíciós struktúra – házirend-szabály](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `field` | `Microsoft.Web/connections/api.id` | A `field` feltétellel összehasonlítandó érték <p><p>Ebben az esetben a a `field` [*aliast*](../governance/policy/concepts/definition-structure.md#aliases)használja az `Microsoft.Web/connections/api.id` összekötő tulajdonság értékének eléréséhez `api.id` . |
   | `like` | `*managedApis/{connector-name}` | Az érték összehasonlításához használandó logikai operátor és érték `field` <p><p>Ebben a forgatókönyvben az `like` operátor és a helyettesítő karakter (*) karaktert is ellenőrizze, hogy a szabály a régiótól függetlenül működik-e, és a karakterlánc a, az a érték, amely a `*managedApis/{connector-name}` `{connector-name}` blokkolni kívánt összekötő azonosítójának felel meg. <p><p>Tegyük fel például, hogy le kívánja tiltani a kapcsolatok létrehozását a közösségi média-platformokhoz vagy-adatbázisokhoz: <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>-SQL Server vagy Azure SQL: `sql` <p><p>Az összekötő-azonosítók megkereséséhez tekintse meg a témakör korábbi, [összekötő-hivatkozási azonosítójának megkeresése](#connector-reference-ID) című szakaszt. |
   | `then` | `{effect-to-apply}` | A feltétel teljesülése esetén alkalmazandó hatás `if` <p><p>Ebben az esetben az `{effect-to-apply}` egy olyan kérés vagy művelet blokkolása és meghibásodása, amely nem felel meg a szabályzatnak. <p><p>További információ: [házirend-definíciós struktúra – házirend-szabály](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | Az a `effect` kérelem blokkolása, amely a megadott kapcsolódás létrehozása <p><p>További információ: [Azure Policy Effects – megtagadás ismertetése](../governance/policy/concepts/effects.md#deny). |
   ||||

   Tegyük fel például, hogy le szeretné tiltani a kapcsolatok létrehozását az Instagram-összekötővel. A következő szabályzat-definíciót használhatja:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Az alábbi módon jelenik meg a **házirend-szabály** mező:

   ![A "házirend-szabály" mezőt tartalmazó képernyőkép, amely egy példa házirend-szabályra mutat.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Több összekötő esetén több feltételt is hozzáadhat, például:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Amikor elkészült, válassza a **Mentés** lehetőséget. A házirend-definíció mentése után Azure Policy a házirend-definícióhoz több tulajdonságot hoz létre és ad hozzá.

1. Ezután a szabályzat-definíció hozzárendeléséhez, ahol kényszeríteni kívánja a házirendet, [hozzon létre egy szabályzat-hozzárendelést](#create-policy-assignment).

Az Azure Policy-definíciókkal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Házirend szerkezetének meghatározása](../governance/policy/concepts/definition-structure.md)
* [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy beépített szabályzat-definíciók a Azure Logic Apps](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>A kapcsolatok használatának blokkolására szolgáló szabályzat létrehozása

Ha logikai alkalmazáson belül hoz létre egy kapcsolódást, akkor ez a kapcsolódás különálló Azure-erőforrásként van megalkotva. Ha csak a logikai alkalmazást törli, a rendszer nem törli automatikusan a kapcsolódást, és a törlés előtt továbbra is érvényben marad. Lehet, hogy van olyan forgatókönyv, amelyben a kapcsolat már létezik, vagy ahol létre kell hoznia a kapcsolatot a logikai alkalmazáson kívüli használatra. Továbbra is letilthatja a logikai alkalmazás meglévő kapcsolatainak használatát egy olyan szabályzat létrehozásával, amely megakadályozza a korlátozott vagy nem jóváhagyott kapcsolatban álló logikai alkalmazások mentését.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A portál keresési mezőjébe írja be a kifejezést `policy` , majd válassza a **házirend** lehetőséget.

   ![A Azure Portalban keresse meg és válassza ki a "házirend" elemet.](./media/block-connections-connectors/find-select-azure-policy.png)

1. A **házirend** menü **szerzői műveletek** területén válassza a **definíciók**  >  **+ szabályzat definíciója** lehetőséget.

   ![Válassza a "definíciók" > "+ házirend-definíció" elemet.](./media/block-connections-connectors/add-new-policy-definition.png)

1. A **házirend-definíció** területen adja meg a házirend-definíció információit a példa alatt leírt tulajdonságok alapján, és folytassa a Instagram példaként való használatával:

   ![Házirend-definíció tulajdonságai](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Definíció helye** | Yes | <*Azure-előfizetés – név*> | A házirend-definícióhoz használandó Azure-előfizetés <p><p>1. az előfizetés megkereséséhez kattintson a három pont (**..**.) gombra. <br>2. az **előfizetés** listában keresse meg és válassza ki az előfizetését. <br>3. Ha elkészült, válassza a **kiválasztás** lehetőséget. |
   | **Név** | Yes | <*házirend-definíció – név*> | A házirend-definícióhoz használandó név |
   | **Leírás** | No | <*házirend-definíció – név*> | A házirend-definíció leírása |
   | **Kategória** | Yes | **Logikai alkalmazások** | A házirend-definíció meglévő kategóriájának vagy új kategóriájának neve |
   | **Szabályzatbetartatás** | Yes | **Engedélyezve** | Ezzel a beállítással adható meg, hogy engedélyezi vagy letiltja a házirend-definíciót a munka mentésekor. |
   ||||

1. A **házirend-szabály** területen a JSON-szerkesztő mező előre ki van töltve egy házirend-definíciós sablonnal. Cserélje le ezt a sablont a [házirend-definícióra](../governance/policy/concepts/definition-structure.md) az alábbi táblázatban leírt tulajdonságok alapján, és kövesse a következő szintaxist:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | `mode` | `All` | Az a mód, amely meghatározza a házirend által kiértékelt erőforrás-típusokat. <p><p>Ez a forgatókönyv `mode` a `All` -re vonatkozik, amely a szabályzatot Azure-erőforráscsoportok, előfizetések és minden erőforrástípus esetében alkalmazza. <p><p>További információ: [Policy definition Structure-Mode](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | Az a feltétel, amely meghatározza, hogy mikor kell kikényszeríteni a szabályzat szabályát <p><p>Ebben az esetben az `{condition-to-evaluate}` határozza meg, hogy a karakterlánc kimenete tartalmazza-e `[string(field('Microsoft.Logic/workflows/parameters'))]` a karakterláncot `{connector-name}` . <p><p>További információ: [házirend-definíciós struktúra – házirend-szabály](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | A feltétellel összehasonlítandó érték <p><p>Ebben az esetben a a `value` karakterlánc kimenete, amely az objektumon `[string(field('Microsoft.Logic/workflows/parameters'))]` `$connectors` belüli objektumot karakterlánccá alakítja át `Microsoft.Logic/workflows/parameters` . |
   | `contains` | `{connector-name}` | A tulajdonsággal való összehasonlításhoz használandó logikai operátor és érték `value` <p><p>Ebben az esetben az operátor ellenőrzi, `contains` hogy a szabály attól függetlenül működik-e `{connector-name}` , hogy hol jelenik meg, ahol a karakterlánc, amely a `{connector-name}` korlátozni vagy letiltani kívánt összekötő azonosítója. <p><p>Tegyük fel például, hogy le kívánja tiltani a kapcsolatok használatát a közösségi média-platformokhoz vagy-adatbázisokhoz: <p><p>Twitter `twitter` <br>Instagram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>-SQL Server vagy Azure SQL: `sql` <p><p>Az összekötő-azonosítók megkereséséhez tekintse meg a témakör korábbi, [összekötő-hivatkozási azonosítójának megkeresése](#connector-reference-ID) című szakaszt. |
   | `then` | `{effect-to-apply}` | A feltétel teljesülése esetén alkalmazandó hatás `if` <p><p>Ebben az esetben az `{effect-to-apply}` egy kérelem vagy művelet letiltására és elutasítására szolgál, amely nem felel meg a szabályzatnak. <p><p>További információ: [házirend-definíciós struktúra – házirend-szabály](../governance/policy/concepts/definition-structure.md#policy-rule). |
   | `effect` | `deny` | A a `effect` `deny` megadott kapcsolódást használó logikai alkalmazás mentési kérelme vagy letiltása <p><p>További információ: [Azure Policy Effects – megtagadás ismertetése](../governance/policy/concepts/effects.md#deny). |
   ||||

   Tegyük fel például, hogy le szeretné tiltani a Instagram-kapcsolatokat használó logikai alkalmazások mentését. A következő szabályzat-definíciót használhatja:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Az alábbi módon jelenik meg a **házirend-szabály** mező:

   ![Szabályzat-definíció szabálya](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget. A házirend-definíció mentése után Azure Policy a házirend-definícióhoz több tulajdonságot hoz létre és ad hozzá.

1. Ezután a szabályzat-definíció hozzárendeléséhez, ahol kényszeríteni kívánja a házirendet, [hozzon létre egy szabályzat-hozzárendelést](#create-policy-assignment).

Az Azure Policy-definíciókkal kapcsolatos további információkért tekintse meg a következő témaköröket:

* [Házirend szerkezetének meghatározása](../governance/policy/concepts/definition-structure.md)
* [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)
* [Azure Policy beépített szabályzat-definíciók a Azure Logic Apps](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ezután hozzá kell rendelnie azt a házirend-definíciót, ahol kényszeríteni kívánja a házirendet, például egyetlen erőforráscsoporthoz, több erőforráscsoporthoz, Azure Active Directory (Azure AD) bérlőhöz vagy Azure-előfizetéshez. Ehhez a feladathoz kövesse az alábbi lépéseket egy szabályzat-hozzárendelés létrehozásához:

1. Ha kijelentkezett, jelentkezzen be ismét a [Azure Portalba](https://portal.azure.com). A portál keresési mezőjébe írja be a kifejezést `policy` , majd válassza a **házirend** lehetőséget.

   ![A Azure Portalban keresse meg és válassza ki a "házirend" elemet.](./media/block-connections-connectors/find-select-azure-policy.png)

1. A **házirend** menü **szerzői műveletek** területén válassza a **hozzárendelés**  >  **házirend hozzárendelése** elemet.

   ![Válassza a "hozzárendelések" > "hozzárendelés" lehetőséget.](./media/block-connections-connectors/add-new-policy-assignment.png)

1. Az **alapok** területen adja meg ezt az információt a szabályzat-hozzárendeléshez:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Hatókör** | Yes | Azok az erőforrások, amelyeken ki szeretné kényszeríteni a szabályzat-hozzárendelést. <p><p>1. a **hatókör** mező mellett kattintson az ellipszisek (**..**.) gombra. <br>2. az **előfizetés** listából válassza ki az Azure-előfizetést. <br>3. szükség esetén az **erőforráscsoport** listából válassza ki az erőforráscsoportot. <br>4. Ha elkészült, válassza a **kiválasztás** lehetőséget. |
   | **Kizárások** | No | A szabályzat-hozzárendelésből kizárandó Azure-erőforrások. <p><p>1. a **kizárások** mező mellett kattintson az ellipszisek (**..**.) gombra. <br>2. az **erőforrás** listából válassza ki az erőforrást, > a **Hozzáadás a kiválasztott hatókörhöz**. <br>3. Ha elkészült, válassza a **Mentés** lehetőséget. |
   | **Szabályzatdefiníció** | Yes | A hozzárendelni és érvényesíteni kívánt szabályzat-definíció neve. Ez a példa a "Instagram-kapcsolatok tiltása" Instagram szabályzattal folytatja a példát. <p><p>1. a házirend- **definíció** mező mellett válassza az ellipszisek (**...**) gombot. <br>2. **a szűrő vagy a** **keresőmező** használatával keresse meg és válassza ki a házirend-definíciót. <br>3. Ha elkészült, válassza a **kiválasztás** lehetőséget. |
   | **Hozzárendelés neve** | Yes | A szabályzat-hozzárendeléshez használandó név, ha eltér a házirend-definíciótól |
   | **Hozzárendelés azonosítója** | Yes | A szabályzat-hozzárendelés automatikusan generált azonosítója |
   | **Leírás** | No | A szabályzat-hozzárendelés leírása |
   | **Szabályzatbetartatás** | Yes | A szabályzat-hozzárendelést engedélyező vagy letiltó beállítás |
   | **Hozzárendelte** | No | Annak a személynek a neve, aki létrehozta és alkalmazta a szabályzat-hozzárendelést |
   ||||

   Például a szabályzat egy Azure-erőforráscsoporthoz való hozzárendeléséhez a Instagram példa használatával:

   ![Szabályzat-hozzárendelés tulajdonságai](./media/block-connections-connectors/policy-assignment-basics.png)

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

   A szabályzat létrehozása után akár 15 percet is várnia kell, amíg a házirend érvénybe lép. A módosítások hasonló késleltetett hatásokat is tartalmazhatnak.

1. A szabályzat érvénybe léptetése után [tesztelheti a szabályzatot](#test-policy).

További információ: gyors útmutató [: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>A szabályzat tesztelése

A szabályzat kipróbálásához hozzon létre egy kapcsolatokat a Logic app Designer mostantól korlátozott összekötője használatával. Ha folytatja a Instagram, amikor bejelentkezik a Instagram-be, ezt a hibaüzenetet kapja, miszerint a logikai alkalmazás nem tudta létrehozni a kapcsolódást:

![Az alkalmazott házirend miatti kapcsolódási hiba](./media/block-connections-connectors/connection-failure-message.png)

Az üzenet tartalmazza a következő információkat:

| Leírás | Tartalom |
|-------------|---------|
| A hiba oka | `"Resource 'instagram' was disallowed by policy."` |
| Hozzárendelés neve | `"Block Instagram connections"` |
| Hozzárendelés azonosítója | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| Házirend-definíció azonosítója | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Policy](../governance/policy/overview.md)