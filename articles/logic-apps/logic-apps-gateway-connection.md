---
title: Helyszíni adatforrások elérése
description: Csatlakozás helyszíni adatforráshoz a Azure Logic Apps azure-beli adatátjáró-erőforrás létrehozásával
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 49da5d7f045ed06ba16696ebd16ad212b9d140d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763308"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Csatlakozás helyszíni adatforrásokhoz az Azure Logic Appsből

Miután telepítette a helyszíni adatátjárót egy helyi számítógépre, és mielőtt hozzáférhet a helyszíni adatforráshoz a logikai alkalmazásokból, létre kell hoznia egy átjáró-erőforrást az [ *Azure-ban*](../logic-apps/logic-apps-gateway-install.md) az átjáró telepítéséhez. Ezt az átjáró-erőforrást ezután kiválaszthatja a helyszíni összekötőkhöz használni kívánt eseményindítókban és műveletekben, amelyek a Azure Logic Apps. [](../connectors/managed.md#on-premises-connectors) Azure Logic Apps támogatja az olvasási és írási műveleteket az adatátjárón keresztül. Ezeknek a műveleteknek azonban [korlátaik vannak a hasznosság méretére.](/data-integration/gateway/service-gateway-onprem#considerations)

Ez a cikk bemutatja, hogyan hozhat létre Azure-átjáró-erőforrást egy korábban telepített átjáróhoz a [helyi számítógépen.](../logic-apps/logic-apps-gateway-install.md) További információ az átjáróról: [Az átjáró működése.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Ha az átjáró használata nélkül is közvetlenül hozzá akar férni az Azure-beli virtuális hálózatokban lévő helyszíni erőforrásokhoz, érdemes inkább integrációs szolgáltatási környezetet [*létrehoznia.*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 

Az átjáró más szolgáltatásokkal való használatával kapcsolatos információkért tekintse meg a következő cikkeket:

* [Microsoft Power Automate helyszíni adatátjáró](/power-automate/gateway-reference)
* [Microsoft Power BI helyszíni adatátjáró](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps helyszíni adatátjáró](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services adatátjáró létrehozása](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

A Azure Logic Apps helyszíni adatátjáró a következő [](../connectors/managed.md#on-premises-connectors) adatforrások helyszíni összekötőit támogatja:

* BizTalk Server 2016
* Fájlrendszer
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint-kiszolgáló
* SQL Server
* Teradata

REST vagy SOAP használatával olyan [egyéni összekötőket](../logic-apps/custom-connector-overview.md) is létrehozhat, amelyek HTTP- vagy HTTPS-kapcsolaton keresztül csatlakoznak az adatforráshoz. Bár maga az átjáró nem jár többletköltségekkel, a Logic Apps díjszabási modell a felhőben használt összekötőkre és egyéb műveletekre Azure Logic Apps. [](../logic-apps/logic-apps-pricing.md)

## <a name="prerequisites"></a>Előfeltételek

* Már [telepítette a helyszíni adatátjárót egy helyi számítógépre.](../logic-apps/logic-apps-gateway-install.md) Az átjáró telepítésének léteznie kell ahhoz, hogy létrehoz egy átjáró-erőforrást, amely erre a telepítésre kapcsolódik.

* Ugyanaz az [Azure-fiók és -előfizetése](../logic-apps/logic-apps-gateway-install.md#requirements) van, mint az átjáró telepítéséhez. Ennek az Azure-fióknak csak egy Azure Active Directory [(Azure AD) bérlőhöz vagy címtárhoz kell tartozni.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Az átjáró-erőforrás Azure-ban való létrehozásához ugyanazt az Azure-fiókot és -előfizetést kell használnia, mert csak az átjáró rendszergazdája hozhatja létre az átjáró-erőforrást az Azure-ban. A szolgáltatásnév jelenleg nem támogatott.

  * Amikor létrehoz egy átjáró-erőforrást az Azure-ban, ki kell választania egy átjárótelepítést, amely az átjáró-erőforráshoz és csak ahhoz az átjáró-erőforráshoz fog hivatkozni. Minden átjáró-erőforrás csak egy átjárótelepítéshez kapcsolhat. Nem választhat olyan átjárótelepítést, amely már társítva van egy másik átjáró-erőforráshoz.

  * A logikai alkalmazásnak és az átjáró-erőforrásnak nem kell léteznie ugyanabban az Azure-előfizetésben. Az eseményindítókban és műveletekben, ahol használhatja az átjáró-erőforrást, választhat egy másik Azure-előfizetést, amely rendelkezik átjáró-erőforrással, de csak akkor, ha az előfizetés ugyanabban az Azure AD-bérlőben vagy címtárban található, mint a logikai alkalmazás. Emellett rendszergazdai engedélyekkel kell rendelkeznie az átjárón, amelyet egy másik rendszergazda állíthat be Önnek. További információ: [Data Gateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) and [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > Jelenleg nem oszthat meg átjáró-erőforrást vagy -telepítést több előfizetés között. A termékkel kapcsolatos visszajelzéseket a Microsoft Azure [fórumon küldheti el.](https://feedback.azure.com/forums/34192--general-feedback)

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure Gateway-erőforrás létrehozása

Miután az átjárót egy helyi számítógépre telepíti, hozza létre az átjáró Azure-erőforrását.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az átjáró telepítéséhez használt Azure-fiókkal.

1. A Azure Portal keresőmezőbe írja be a "helyszíni adatátjáró" lehetőséget, majd válassza a **Helyszíni adatátjárók lehetőséget.**

   ![A "Helyszíni adatátjáró" megkeresése](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. A **Helyszíni adatátjárók alatt válassza** a Hozzáadás **lehetőséget.**

   ![Új Azure-erőforrás hozzáadása adatátjáróhoz](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. A **Kapcsolatátjáró létrehozása alatt** adja meg ezt az információt az átjáró-erőforráshoz. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Erőforrás neve** | Adjon egy olyan nevet az átjáró-erőforrásnak, amely csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket ( ), zárójeleket ( , ) vagy `-` `_` `(` `)` pontokat ( ) `.` tartalmaz. |
   | **Előfizetés** | Válassza ki az átjáró telepítéséhez használt Azure-fiók Azure-előfizetését. Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul. |
   | **Erőforráscsoport** | A [használni kívánt Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) |
   | **Hely** | Ugyanaz a régió vagy hely, amely az átjáró felhőszolgáltatásához lett kiválasztva [az átjáró telepítése során.](../logic-apps/logic-apps-gateway-install.md) Ellenkező esetben az átjáró telepítése nem jelenik meg a Telepítés **neve listában.** A logikai alkalmazás helye eltérhet az átjáró-erőforrás helyétől. |
   | **Telepítés neve** | Válasszon ki egy átjárótelepítést, amely csak akkor jelenik meg a listában, ha teljesülnek ezek a feltételek: <p><p>– Az átjárótelepítés ugyanazt a régiót használja, mint a létrehozni kívánt átjáró-erőforrás. <br>– Az átjárótelepítés nincs más Azure-átjáró-erőforráshoz kapcsolva. <br>– Az átjárótelepítés ugyan ahhoz az Azure-fiókhoz kapcsolódik, amely az átjáró-erőforrás létrehozásához is használ. <br>– Az Azure-fiókja egyetlen Azure Active Directory [-bérlőhöz vagy](../active-directory/fundamentals/active-directory-whatis.md#terminology) -címtárhoz tartozik, és ugyanaz a fiók, mint amit az átjáró telepítéséhez használt. <p><p>További információkért lásd a Gyakori [kérdések szakaszt.](#faq) |
   |||

   Az alábbi példa egy olyan átjárótelepítést mutat be, amely ugyanabban a régióban van, mint az átjáró-erőforrás, és ugyanhoz az Azure-fiókhoz van kapcsolva:

   ![Adja meg az adatátjáró-erőforrás létrehozásához szükséges adatokat](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Csatlakozás helyszíni adatokhoz

Miután létrehozott egy átjáró-erőforrást, és társítja azure-előfizetését ehhez az erőforráshoz, létrehozhat egy kapcsolatot a logikai alkalmazás és a helyszíni adatforrás között az átjáró használatával.

1. A Azure Portal hozza létre vagy nyissa meg a logikai alkalmazást a Logikaialkalmazás-tervezőben.

1. Adjon hozzá egy összekötőt, amely támogatja a helyszíni **kapcsolatokat,** például SQL Server.

1. Válassza **a Csatlakozás helyszíni adatátjárón keresztül lehetőséget.**

1. Az **Átjáró** alatt az **Előfizetés listából** válassza ki azt az Azure-előfizetést, amely a kívánt átjáró-erőforrással rendelkezik.

   A logikai alkalmazásnak és az átjáró-erőforrásnak nem kell léteznie ugyanabban az Azure-előfizetésben. Választhat más Azure-előfizetések közül, amelyek mindegyikének van átjáró-erőforrása, de csak akkor, ha ezek az előfizetések ugyanabban az Azure AD-bérlőben vagy -címtárban léteznek, mint a logikai alkalmazás, és rendelkezik rendszergazdai engedélyekkel az átjárón, amelyet egy másik rendszergazda állíthat be Önnek. További információ: [Data Gateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) and [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. A **Kapcsolatátjáró listából,** amely a kiválasztott előfizetésben elérhető átjáró-erőforrásokat jeleníti meg, válassza ki a kívánt átjáró-erőforrást. Minden átjáró-erőforrás egyetlen átjárótelepítéshez van csatolva.

   > [!NOTE]
   > Az átjárók listája más régiókban található átjáró-erőforrásokat is tartalmaz, mert a logikai alkalmazás helye eltérhet az átjáró-erőforrás helyétől. 

1. Adjon meg egy egyedi kapcsolatnevet és egyéb szükséges információkat, amelyek a létrehozni kívánt kapcsolattól függnek.

   Az egyedi kapcsolatnév segítségével később könnyen megtalálja a kapcsolatot, különösen akkor, ha több kapcsolatot hoz létre. Ha lehetséges, adja meg a felhasználónév minősített tartományát is.

   Alább bemutatunk egy példát:

   ![Kapcsolat létrehozása a logikai alkalmazás és az adatátjáró között](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

Az átjárókapcsolat ezzel készen áll a logikai alkalmazás használatára.

## <a name="edit-connection"></a>Kapcsolat szerkesztése

Az átjárókapcsolat beállításainak frissítéséhez szerkesztheti a kapcsolatot.

1. Ha csak a logikai alkalmazás összes API-kapcsolatát meg kell találnia, a logikai alkalmazás menüjében, a **Fejlesztői** eszközök alatt válassza az **API-kapcsolatok lehetőséget.**

   ![A logikai alkalmazás menüjében válassza az "API-kapcsolatok" elemet](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Válassza ki a kívánt átjárókapcsolatot, majd válassza az **API-kapcsolat szerkesztése lehetőséget.**

   > [!TIP]
   > Ha a frissítések nem lépnek [](../logic-apps/logic-apps-gateway-install.md#restart-gateway) érvénybe, próbálja meg leindítani és újraindítani az átjáró Windows-szolgáltatásfiókját az átjáró telepítéséhez.

Az Azure-előfizetéséhez társított összes API-kapcsolat megkeresése:

* A Azure Portal válassza a **Minden szolgáltatás**  >  **Webes**  >  **API-kapcsolatok lehetőséget.**
* Vagy a menüben válassza Azure Portal Minden **erőforrás lehetőséget.** Állítsa a **Típus szűrőt** **API-kapcsolatra.**

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Átjáró-erőforrás törlése

Ha másik átjáró-erőforrást szeretne létrehozni, az átjáró telepítését egy másik átjáró-erőforráshoz szeretné összekapcsolni, vagy el szeretné távolítani az átjáró-erőforrást, törölheti az átjáró-erőforrást anélkül, hogy ez hatással lenne az átjáró telepítésére.

1. A Azure Portal válassza a **Minden** erőforrás lehetőséget, vagy keresse meg és válassza a **Minden** erőforrás lehetőséget bármely oldalról. Keresse meg és válassza ki az átjáró-erőforrást.

1. Ha még nincs bejelölve, az átjáró erőforrásmenüjében válassza a **Helyszíni adatátjáró lehetőséget.** Az átjáró erőforrás-eszköztárán válassza a **Törlés lehetőséget.**

   Például:

   ![Átjáróerőforrás törlése az Azure-ban](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** Miért nem jelenik meg az átjáró telepítése, amikor létrehozom az átjáró-erőforrást az Azure-ban? <br/>
**A:** Ez a probléma a következő okok miatt fordulhat elő:

* Az Azure-fiókja nem ugyanaz a fiók, mint amit az átjáró telepítéséhez használt a helyi számítógépen. Ellenőrizze, hogy ugyanazokkal az identitással jelentkezett-e Azure Portal, mint az átjáró telepítéséhez. Csak az átjáró rendszergazdája hozhatja létre az átjáró-erőforrást az Azure-ban. A szolgáltatásnév jelenleg nem támogatott.

* Az Azure-fiókja nem csak egyetlen Azure AD-bérlőhöz vagy [-címtárhoz tartozik.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Ellenőrizze, hogy ugyanazt az Azure AD-bérlőt vagy -címtárat használja-e, mint az átjáró telepítésekor.

* Az átjáró-erőforrás és -telepítés nem ugyanabban a régióban létezik. A logikai alkalmazás helye azonban eltérhet az átjáró-erőforrás helyétől.

* Az átjáró telepítése már társítva van egy másik átjáró-erőforrással. Minden átjáró-erőforrás csak egy átjárótelepítéshez kapcsolhat, amely csak egy Azure-fiókhoz és -előfizetéshez kapcsolhat. Ezért nem választhat olyan átjárótelepítést, amely már társítva van egy másik átjáró-erőforráshoz. Ezek a telepítések nem jelennek meg a Telepítés **neve listában.**

  Az átjáróregisztrációk áttekintéséhez Azure Portal összes Azure-erőforrást, amely rendelkezik a helyszíni adatátjárók erőforrástípussal az összes **Azure-előfizetésben.**  Az átjáró telepítésének leválasztása a másik átjáró-erőforrásról: [Átjáró-erőforrás törlése.](#change-delete-gateway-resource)

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Következő lépések

* [A logikai alkalmazások védelme](./logic-apps-securing-a-logic-app.md)
* [Gyakori példák és forgatókönyvek logikai alkalmazásokhoz](./logic-apps-examples-and-scenarios.md)
