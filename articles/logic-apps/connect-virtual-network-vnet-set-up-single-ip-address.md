---
title: Nyilvános kimenő IP-cím beállítása ISE-k számára
description: Megtudhatja, hogyan állíthat be egyetlen nyilvános kimenő IP-címet az integrációs szolgáltatási környezetekhez (ISE-k) a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874188"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Egyetlen IP-cím beállítása egy vagy több integrációs szolgáltatási környezethez a Azure Logic Apps

Ha a virtuális gépekkel Azure Logic Apps, integrációs szolgáltatási környezetet [  (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) állíthat be olyan logikai alkalmazások üzemeltetéséhez, amelyeknek hozzá kell férni az Azure-beli virtuális [hálózatok erőforrásaihoz.](../virtual-network/virtual-networks-overview.md) Ha több ISE-példánya van, amelyek más, IP-korlátozásokkal rendelkező végpontokhoz is hozzá kell férni, helyezzen üzembe egy [Azure Firewall-t](../firewall/overview.md) vagy egy hálózati virtuális berendezést [a](../virtual-network/virtual-networks-overview.md#filter-network-traffic) virtuális hálózatban, és irányítsa a kimenő forgalmat ezen a tűzfalon vagy hálózati virtuális berendezésen keresztül. Ezután a virtuális hálózat összes ISE-példánya egyetlen, nyilvános, statikus és kiszámítható IP-címet használhat a kívánt célrendszerekkel való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célrendszereken az egyes ISE-hez.

Ez a témakör bemutatja, hogyan irányíthatja át a kimenő forgalmat egy Azure Firewall-hálózaton keresztül, de hasonló fogalmakat alkalmazhat egy hálózati virtuális berendezésre, például egy külső tűzfalra a Azure Marketplace. Bár ez a témakör több ISE-példány beállítására összpontosít, akkor is használhatja ezt a módszert egyetlen ISE-hez, ha a forgatókönyvben korlátozni kell a hozzáférést igénylő IP-címek számát. Gondolja át, hogy a tűzfal vagy a virtuális hálózati berendezés további költségei megfelelőek-e a forgatókönyvnek. További információ a [Azure Firewall díjszabásról.](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-tűzfal, amely ugyanabban a virtuális hálózatban fut, mint az ISE. Ha nem rendelkezik tűzfallal, először adjon hozzá egy nevű alhálózatot [a](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) `AzureFirewallSubnet` virtuális hálózathoz. Ezután létrehozhat [és üzembe helyezhet egy tűzfalat a](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) virtuális hálózatban.

* Egy [Azure-útvonaltábla.](../virtual-network/manage-route-table.md) Ha még nem tudja, először hozzon létre [egy útvonaltáblát.](../virtual-network/manage-route-table.md#create-a-route-table) További információ az útválasztásról: [Virtuális hálózati forgalom útválasztása.](../virtual-network/virtual-networks-udr-overview.md)

## <a name="set-up-route-table"></a>Útvonaltábla beállítása

1. A [Azure Portal](https://portal.azure.com)válassza ki az útvonaltáblát, például:

   ![Útvonaltábla kiválasztása a kimenő forgalom útválasztási szabályával](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Új [útvonal hozzáadásához az](../virtual-network/manage-route-table.md#create-a-route)útvonaltábla menüjében válassza az Útvonalak **Hozzáadása**  >  **lehetőséget.**

   ![Útvonal hozzáadása a kimenő forgalom útválasztáshoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Az **Útvonal hozzáadása panelen** állítson be egy szabályt az új útvonalhoz, [](../virtual-network/manage-route-table.md#create-a-route) amely meghatározza, hogy a célrendszer felé irányuló összes kimenő forgalom a következő viselkedést követi:

   * A virtuális [**berendezést használja**](../virtual-network/virtual-networks-udr-overview.md#user-defined) a következő ugrás típusaként.

   * A következő ugrás címeként a tűzfalpéldány magánhálózati IP-címére kerül.

     Az IP-cím megkereséhez a tűzfal menüjében válassza az **Áttekintés** lehetőséget, keresse meg a címet a Magánhálózati **IP-cím alatt,** például:

     ![Tűzfal magánhálózati IP-címének megkerese](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Az alábbi példa bemutatja, hogyan néz ki egy ilyen szabály:

   ![Kimenő forgalomra vonatkozó szabály beállítása](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Útvonal neve** | <*unique-route-name*> | Az útvonal egyedi neve az útvonaltáblában |
   | **Címelőtag** | <*cél-cím*> | Annak a célrendszernek a címelőtagja, ahová a kimenő forgalmat el szeretné menni. Győződjön meg arról, hogy ehhez a címhez [classless Inter-Domain routing (CIDR) (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) -t használ. Ebben a példában ez a címelőtag egy SFTP-kiszolgálóhoz való, amelynek leírása a [Hálózati szabály beállítása című szakaszban található.](#set-up-network-rule) |
   | **A következő ugrás típusa** | **Virtuális berendezés** | A [kimenő](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) forgalom által használt ugrás típusa |
   | **A következő ugrás címe** | <*tűzfal magánhálózati IP-címe*> | A tűzfal magánhálózati IP-címe |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Hálózati szabály beállítása

1. A Azure Portal keresse meg és válassza ki a tűzfalat. A tűzfal menüjében a Beállítások **alatt válassza a** Szabályok **lehetőséget.** A szabályok panelen válassza a Hálózati **szabálygyűjtemény hozzáadása**  >  **Hálózati szabálygyűjtemény hozzáadása lehetőséget.**

   ![Hálózatiszabály-gyűjtemény hozzáadása tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. A gyűjteményben adjon hozzá egy szabályt, amely engedélyezi a célrendszerbe való forgalmat.

   Tegyük fel például, hogy van egy logikai alkalmazása, amely egy ISE-ban fut, és egy SFTP-kiszolgálóval kell kommunikálnia. Létrehoz egy nevű hálózatiszabály-gyűjteményt, amely egy nevű `LogicApp_ISE_SFTP_Outbound` hálózati szabályt `ISE_SFTP_Outbound` tartalmaz. Ez a szabály engedélyezi a forgalmat minden olyan alhálózat IP-címével, ahol az ISE a virtuális hálózaton fut a cél SFTP-kiszolgáló felé a tűzfal magánhálózati IP-címének használatával.

   ![Hálózati szabály beállítása tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Hálózatiszabály-gyűjtemény tulajdonságai**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*network-rule-collection-name*> | A hálózati szabálygyűjtemény neve |
   | **Prioritás** | <*prioritási szint*> | A szabálygyűjtemény futtatásához használt prioritási sorrend. További információ: [Mik azok a Azure Firewall fogalmak?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) |
   | **Művelet** | **Engedélyezés** | A szabályhoz szükséges művelettípus |
   |||

   **Hálózati szabály tulajdonságai**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*network-rule-name*> | A hálózati szabály neve |
   | **Protokoll** | <*kapcsolati protokollok*> | A használni szükséges kapcsolati protokollok. Ha például NSG-szabályokat használ, válassza a **TCP** és az **UDP** lehetőséget is, nem csak a **TCP-t.** |
   | **Forráscímek** | <*ISE-alhálózat-címek*> | Az alhálózati IP-címek, ahol az ISE fut, és ahonnan a logikai alkalmazás forgalma származik |
   | **Célcímek** | <*cél-IP-cím*> | Annak a célrendszernek az IP-címe, ahová a kimenő forgalmat el szeretné menni. Ebben a példában ez az IP-cím az SFTP-kiszolgálóhoz való. |
   | **Célportok** | <*célportok*> | A célrendszer által a bejövő kommunikációhoz használt portok |
   |||

   A hálózati szabályokkal kapcsolatos további információkért tekintse meg az alábbi cikkeket:

   * [Hálózatszabály konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Az Azure Firewall szabályfeldolgozási logikája](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall – gyakori kérdések](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>Következő lépések

* [Csatlakozás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
