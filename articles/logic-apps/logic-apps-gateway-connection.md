---
title: A helyszíni adatforrások elérése az Azure Logic Apps |} A Microsoft Docs
description: Csatlakozhat a helyszíni adatok források a logikai alkalmazásokból létrehozásával egy helyszíni adatátjáró
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 2b9e1c153c3fa9b17145eb6c3c8f3ed02e3bf40f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304180"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>A helyszíni adatforrásokhoz kapcsolódik az Azure Logic Apps

Hozzáférni az adatforrásokhoz a helyszínen, a logic apps, az Azure Portalon egy helyszíni data gateway-erőforrás létrehozásához. A logic apps használhatja a [helyszíni összekötők](../logic-apps/logic-apps-gateway-install.md#supported-connections). Ez a cikk bemutatja, hogyan hozhat létre az Azure-átjáró erőforrás *után* , [töltse le és telepítse az átjárót a helyi számítógépen](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Csatlakozás az Azure virtuális hálózatokhoz, érdemes lehet létrehozni egy [ *integrációs szolgáltatás környezet* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) helyette. 

Az átjáró más szolgáltatásokkal való használatával kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [A Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow helyszíni adatátjáró](https://flow.microsoft.com/documentation/gateway-manage/)
* [A helyszíni adatátjáró a Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Az Azure Analysis Services a helyszíni adatátjáró](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Előfeltételek

* Megismerte már [letölti és telepíti az átjárót a helyi számítógép](../logic-apps/logic-apps-gateway-install.md).

* Az átjáró telepítése még nincs társítva egy átjáró-erőforrást az Azure-ban. Kapcsolat a kapcsolódásiátjáró-telepítés csak egy átjáró-erőforrás, amely történik, ha az átjáró-erőforrás létrehozásához, és válassza ki az átjáró telepítése. A linking elérhetetlenné teszi az átjáró telepítési források.

* Jelentkezzen be az Azure Portalon, és hozza létre az átjáró-erőforrást, ellenőrizze, hogy ugyanazon bejelentkezési fiókot használja, amely a korábban használt [a helyszíni adatátjáró telepítése](../logic-apps/logic-apps-gateway-install.md#requirements) együtt ugyanez [Azure-előfizetés ](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) az átjáró telepítéséhez használt. Ha nem rendelkezik Azure-előfizetésem, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

* Az Azure Portalon, az átjáró-erőforrás létrehozásához és kezeléséhez a [Windows-szolgáltatásfiók](../logic-apps/logic-apps-gateway-install.md#windows-service-account) legalább **közreműködői** engedélyeket. A helyszíni adatátjáró Windows-szolgáltatásként fut, és be van állítva a használandó `NT SERVICE\PBIEgwService` a Windows szolgáltatás bejelentkezési hitelesítő adatait. 

  > [!NOTE]
  > A Windows-szolgáltatásfiók eltér a helyszíni adatokhoz való kapcsolódáshoz használt fiók adatforrásokat, és az Azure munkahelyi vagy iskolai fiók a felhőalapú szolgáltatásokhoz való bejelentkezéshez használt.

## <a name="download-and-install-gateway"></a>Töltse le és telepítse az átjárót

A jelen cikkben ismertetett lépések folytatása előtt ellenőrizze az átjáró a helyi számítógépen már telepítve van.
Ha még nem tette, kövesse a lépéseket [töltse le és telepítse a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Azure-átjáró erőforrás létrehozása

Miután telepítette az átjárót a helyi számítógépen, majd létrehozhat egy Azure-erőforrás az átjáró. Ebben a lépésben is hozzárendeli az átjáró-erőforrást az Azure-előfizetés.

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>. Győződjön meg arról, hogy az azonos Azure munkahelyi vagy iskolai az átjáró telepítéséhez használt e-mail-cím.

2. Az Azure fő menüjéből válassza **erőforrás létrehozása** > 
**integrációs** > **a helyszíni adatátjáró**.

   ![Keresse meg a "helyszíni adatátjáró"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Az a **kapcsolódási átjáró létrehozása** lapon, az átjáró-erőforrást az információkat:

   | Tulajdonság | Leírás | 
   |----------|-------------|
   | **Name (Név)** | Az átjáró-erőforrás neve | 
   | **Előfizetés** | A logikai alkalmazás azonos előfizetéshez kell lennie az Azure-előfizetés neve. Az alapértelmezett előfizetést az Azure-fiókkal való bejelentkezéshez használt alapul. | 
   | **Erőforráscsoport** | A nevet a [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) a kapcsolódó erőforrások rendszerezéséhez | 
   | **Hely** | Azure korlátozza az ezen a helyen ugyanabban a régióban, amely esetében az átjáró felhőszolgáltatása során kiválasztott [kapcsolódásiátjáró-telepítés](../logic-apps/logic-apps-gateway-install.md). <p>**Megjegyzés**: Ellenőrizze, hogy az átjáró-erőforrás helye megegyezik az átjáró felhőalapú szolgáltatás helyét. Ellenkező esetben az átjáró telepítése nem jelenhet meg, hogy a következő lépésben válassza ki a telepített átjárók listáját. Különböző régiókban is használhatja, az átjáró-erőforrás és a logikai alkalmazás. | 
   | **Telepítés neve** | Ha az átjáró telepítése még nincs kiválasztva, válassza ki a korábban telepített átjáróhoz. | 
   | | | 

   Például:

   ![Adja meg az adatokat a helyszíni adatátjáró létrehozása](./media/logic-apps-gateway-connection/createblade.png)

4. Az átjáró-erőforrás hozzáadása az Azure-irányítópulton, válassza ki a **rögzítés az irányítópulton**. Ha elkészült, kattintson a **Létrehozás** gombra.

   Válassza ki, vagy bármikor, az Azure fő menüjéből, megtekintheti az átjáró **minden szolgáltatás**. 
   A Keresés mezőbe írja be a "helyszíni adatátjárók", és válassza ki **a helyszíni Adatátjárók**.

   !["A helyszíni Adatátjárók" keresése](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Csatlakozás helyszíni adatokhoz

Miután az átjáró-erőforrás létrehozásához és az Azure-előfizetés társítása ehhez az erőforráshoz, mostantól létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. Az Azure Portalon létrehozni, vagy nyissa meg a logikai alkalmazás a Logic App Designerben.

2. Adjon hozzá egy összekötőt, amely támogatja a helyszíni kapcsolatok, például **SQL Server**.

3. Most már a kapcsolat beállításához:

   1. Válassza ki **kapcsolódás helyszíni adatátjárón keresztül**. 

   2. A **átjárók**, válassza ki a korábban létrehozott átjáró-erőforrást. 

      Bár az átjáró kapcsolódási helyet léteznie kell a logikai alkalmazás ugyanabban a régióban, választhatja az átjáró egy másik régióban.

   3. Adjon meg egy egyedi kapcsolat neve és a szükséges adatokat. 

      A kapcsolat egyedi neve segítségével könnyen azonosíthatja azokat később, hogy a kapcsolat, különösen akkor, ha létrehozhat több kapcsolatot. Ha alkalmazható, tartalmazzák a felhasználónevet a tartománynév.
   
      Például:

      ![Logic app és a data gateway közötti kapcsolat létrehozása](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Ha elkészült, kattintson a **Létrehozás** gombra. 

A gateway-kapcsolat használata a logikai alkalmazás készen áll.

## <a name="edit-connection"></a>Kapcsolat szerkesztése

A logikai alkalmazás létrehozása egy átjárókapcsolat, után érdemes később frissíteni a beállításait, hogy az adott kapcsolat.

1. Keresse meg az átjárókapcsolat:

   * Csak a logikai alkalmazás minden API-kapcsolat alatt található a logikai alkalmazás menüjében **Fejlesztőeszközök**válassza **API-kapcsolatok**. 
   
     ![Nyissa meg a logikai alkalmazáshoz, jelölje be az "API-kapcsolatok"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Az Azure-előfizetéséhez társított összes API-kapcsolatok kereséséhez: 

     * Az Azure főmenüjében lépjen a **minden szolgáltatás** > **webes** > **API-kapcsolatok**. 
     * Vagy az Azure főmenüjében lépjen a **összes erőforrás**.

2. Válassza ki, és válassza az átjáró-kapcsolat **szerkesztése API-kapcsolat**.

   > [!TIP]
   > Ha a frissítések nem lép érvénybe, próbálja meg [leállítása és újraindítása az átjáró Windows-szolgáltatás](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró-erőforrás törlése

Hozzon létre egy másik átjáró-erőforrást, az átjáró társítandó egy másik erőforrás, vagy távolítsa el az átjáró-erőforrást, törölheti az átjáró erőforrás működésének megzavarása nélkül megtesztelheti az átjáró telepítése. 

1. Az Azure főmenüjében lépjen a **összes erőforrás**. 

2. Keresse meg és válassza ki az átjáró-erőforrást.

3. Ha még nem lenne kiválasztva, az átjáró erőforrás menüben válassza ki a **a helyszíni adatátjáró**. 

4. Az erőforráscsoport eszköztárán válassza **törlése**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek a logic apps](./logic-apps-examples-and-scenarios.md)
