---
title: A B2B vállalati integráció XML-kódjának érvényesítése
description: Az XML érvényesítése a Azure Logic Apps sémái segítségével Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792164"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-ellenőrzés nagyvállalati integráció esetében az Azure Logic Appsben az Enterprise Integration Pack csomaggal

Gyakran B2B-forgatókönyvekben a szerződésben szereplő kereskedelmi partnereknek biztosítaniuk kell, hogy az általuk továbbított üzenetek érvényesek legyenek az adatfeldolgozás megkezdése előtt. A dokumentumok egy előre definiált sémán keresztül ellenőrizhetők az XML-érvényesítési művelettel, amely a Enterprise Integration Pack érhető el.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Olyan üres vagy meglévő logikai alkalmazás, amelyben használni szeretné az XML-érvényesítési műveletet. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.

* Az Azure-előfizetéshez társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ahhoz a logikai alkalmazáshoz van társítva, ahol az XML-érvényesítési műveletet tervezi használni, és az XML-tartalom érvényesítéséhez használni kívánt sémát tartalmazza. A logikai alkalmazásnak és az integrációs fióknak ugyanazon a helyen vagy az Azure-régióban kell lennie.

## <a name="add-xml-validation-action"></a>XML-érvényesítési művelet hozzáadása

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha üres logikai alkalmazással rendelkezik, a Logic app Designerben, a keresőmezőbe írja be szűrőként a kifejezést, `HTTP request` majd válassza ki a **http-kérelem fogadásának időpontját** . Ha nem, folytassa a következő lépéssel.

1. A munkafolyamat utolsó lépése alatt válassza az **új lépés** lehetőséget.

   A meglévő lépések közötti művelet hozzáadásához vigye az egérmutatót a lépéseket összekötő nyíl fölé úgy, hogy a plusz jel ( **+** ) megjelenjen. Válassza ki ezt a plusz jelet, majd válassza **a művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be `xml validation` szűrőként a kifejezést. A műveletek listából válassza az **XML-érvényesítés** lehetőséget.

   ![Az "XML-Érvényesítés" művelet megkeresése és kiválasztása](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Az érvényesíteni kívánt XML-tartalom megadásához kattintson a szövegmezőbe, hogy **megjelenjen a dinamikus** tartalmak listája.

   ![Dinamikus tartalom listájának megnyitása](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   A dinamikus tartalom lista a munkafolyamat előző lépéseinek kimeneteit képviselő tulajdonság-jogkivonatokat jeleníti meg. Ha a lista nem jeleníti meg a várt tulajdonságot, ellenőrizze az trigger vagy a művelet fejlécét, hogy kiválaszthatja-e a **továbbiak** lehetőséget.

1. A dinamikus tartalom listából válassza ki azt a tulajdonságot, amely az érvényesíteni kívánt tartalommal rendelkezik.

   Ez a példa kiválasztja a **törzs** kimenetét az triggerből.

   ![Érvényesíteni kívánt tartalom kiválasztása](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Az érvényesítéshez használni kívánt séma megadásához nyissa meg a **séma neve** listát, és válassza ki a csatolt integrációs fiókhoz hozzáadott érvényesítési sémát.

   ![Az érvényesítéshez használandó séma kiválasztása](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Mentse a logikai alkalmazást.

   Ezzel elkészült az érvényesítés beállításával. Egy valós alkalmazásban érdemes lehet egy üzletági (LOB) alkalmazásban (például SalesForce) tárolni az érvényesített adatmennyiséget. Az érvényesített kimenet Salesforce való elküldéséhez adjon hozzá egy műveletet.

1. Az érvényesítési művelet teszteléséhez elküldheti a logikai alkalmazás munkafolyamatának elindítására vonatkozó kérést.

## <a name="next-steps"></a>Következő lépések

* További információ a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)