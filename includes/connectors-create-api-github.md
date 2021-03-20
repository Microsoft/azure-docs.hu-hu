---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999542"
---
1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást. 

2. A Logic Apps Designerben adja meg a "GitHub" kifejezést a szűrőként. 

3. Válassza ki a GitHub-összekötőt és a használni kívánt triggert.

   ![Válassza ki a GitHub-összekötőt és egy triggert](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Minden logikai alkalmazás-munkafolyamatnak triggerrel kell kezdődnie. Csak akkor választhatja ki a műveleteket, ha a logikai munkafolyamat már elindul triggerrel. 

4. Ha korábban nem hozott létre kapcsolatokat, válassza a **Bejelentkezés** lehetőséget, ha a rendszer kéri a GitHub hitelesítő adatainak megadását.  

   ![Bejelentkezés a GitHub hitelesítő adataival](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   A logikai alkalmazás ezeket a hitelesítő adatokat használja a GitHub-fiókhoz tartozó adatok csatlakoztatásának és elérésének engedélyezéséhez. 

5. Adja meg a GitHub felhasználónevét és jelszavát, majd erősítse meg az engedélyt.

   ![Hitelesítő adatok megadása és az engedélyezés megerősítése](./media/connectors-create-api-github/github-connector-authorize.png)   

   A rendszer most létrehozta a Azure Portal, és készen áll a használatra.

6. Folytassa a logikai alkalmazás munkafolyamatának definiálásával.

   ![További műveletek hozzáadása a logikai alkalmazás munkafolyamatához](./media/connectors-create-api-github/github-connector-logic-app.png)

