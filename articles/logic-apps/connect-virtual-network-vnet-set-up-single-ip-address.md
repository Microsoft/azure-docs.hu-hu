---
title: Nyilvános kimenő IP-cím beállítása a ISEs
description: Ismerje meg, hogyan állíthat be egyetlen nyilvános kimenő IP-címet az integrációs szolgáltatási környezetekhez (ISEs) Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211056"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Egyetlen IP-cím beállítása egy vagy több integrációs szolgáltatási környezethez Azure Logic Apps

Ha Azure Logic Apps-nal dolgozik, beállíthat egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) olyan logikai alkalmazások üzemeltetéséhez, amelyek egy Azure-beli [virtuális hálózat](../virtual-network/virtual-networks-overview.md)erőforrásaihoz férnek hozzá. Ha több ISE-példánnyal rendelkezik, amelyek IP-korlátozásokkal rendelkező más végpontokhoz férnek hozzá, helyezzen üzembe egy [Azure Firewall](../firewall/overview.md) vagy egy [hálózati virtuális berendezést](../virtual-network/virtual-networks-overview.md#filter-network-traffic) a virtuális hálózatban, és irányítsa át a kimenő forgalmat a tűzfalon vagy a hálózati virtuális berendezésen keresztül. Ezután a virtuális hálózat összes ISE-példánya egyetlen, nyilvános, statikus és kiszámítható IP-címet használ a kívánt célhelyekkel való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célszámítógépeken az egyes ISE-rendszerek esetében.

Ez a témakör bemutatja, hogyan irányíthatja a kimenő forgalmat egy Azure Firewallon keresztül, de hasonló fogalmakat alkalmazhat egy hálózati virtuális berendezésre, például egy külső gyártótól származó tűzfalra az Azure piactéren. Habár ez a témakör több ISE-példány beállítására koncentrál, ezt a módszert egyetlen ISE esetében is használhatja, ha a forgatókönyv megköveteli a hozzáférést igénylő IP-címek számának korlátozását. Gondolja át, hogy a tűzfal vagy a virtuális hálózati berendezés további költségei ésszerűek-e a forgatókönyvhöz. További információ a [Azure Firewall díjszabásáról](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-tűzfal, amely ugyanabban a virtuális hálózaton fut, mint az ISE. Ha nem rendelkezik tűzfallal, először [adjon hozzá egy nevű alhálózatot](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) `AzureFirewallSubnet` a virtuális hálózathoz. Ezután [létrehozhatja és üzembe helyezheti a tűzfalat](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) a virtuális hálózaton.

* Egy Azure- [útválasztási táblázat](../virtual-network/manage-route-table.md). Ha még nem rendelkezik ilyennel, először [hozzon létre egy útválasztási táblázatot](../virtual-network/manage-route-table.md#create-a-route-table). További információ az útválasztásról: [virtuális hálózati forgalom útválasztása](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Útválasztási táblázat beállítása

1. A [Azure Portal](https://portal.azure.com)válassza ki az útválasztási táblázatot, például:

   ![Válassza ki az útválasztási tábla szabályt a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. [Új útvonal hozzáadásához](../virtual-network/manage-route-table.md#create-a-route)az útválasztási táblázat menüben válassza az **útvonalak**  >  **Hozzáadás** lehetőséget.

   ![Útvonal hozzáadása a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Az **útvonal hozzáadása** panelen [állítsa be az új útvonalat](../virtual-network/manage-route-table.md#create-a-route) egy szabállyal, amely megadja, hogy a célként megadott rendszerre irányuló kimenő forgalom a következő viselkedést követi:

   * A következő ugrási típusként használja a [**virtuális készüléket**](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

   * Ugrás a tűzfal magánhálózati IP-címére a következő ugrási címként.

     Az IP-cím megkereséséhez a tűzfal menüjében válassza az **Áttekintés** lehetőséget, keresse meg a címet a **magánhálózati IP-cím** területen, például:

     ![Tűzfal magánhálózati IP-címének keresése](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Az alábbi példa bemutatja, hogyan nézhet ki egy ilyen szabály:

   ![Szabály beállítása a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Útvonal neve** | <*egyedi-útvonal-név*> | Az útvonal egyedi neve az útválasztási táblában |
   | **Címelőtag** | <*cél – címe*> | Annak a célhelynek a címe, ahol a kimenő forgalmat el szeretné járni. Ügyeljen arra, hogy ehhez a címnek az [osztály nélküli Inter-Domain útválasztási (CIDR) jelölést](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) használja. Ebben a példában ez a memóriacím egy SFTP-kiszolgálóhoz tartozik, amely a következő szakaszban található: [hálózati szabály beállítása](#set-up-network-rule). |
   | **A következő ugrás típusa** | **Virtuális berendezés** | A kimenő forgalom által használt [Ugrás típusa](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **A következő ugrás címe** | <*tűzfal – magánhálózati-IP-cím*> | A tűzfal magánhálózati IP-címe |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Hálózati szabály beállítása

1. A Azure Portal keresse meg és válassza ki a tűzfalat. A tűzfal menü **Beállítások** területén válassza a **szabályok** elemet. A szabályok ablaktáblán válassza a **hálózati szabályok gyűjtemény**  >  **hálózati szabálygyűjtemény hozzáadása** elemet.

   ![Hálózati szabálygyűjtemény hozzáadása a tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. A gyűjteményben adjon hozzá egy szabályt, amely engedélyezi a forgalmat a célként megadott rendszer felé.

   Tegyük fel például, hogy rendelkezik egy olyan logikai alkalmazással, amely egy ISE-ben fut, és az SFTP-kiszolgálóval kommunikálni kell. Létrehoz egy nevű hálózati szabálygyűjtemény-gyűjteményt `LogicApp_ISE_SFTP_Outbound` , amely egy nevű hálózati szabályt tartalmaz `ISE_SFTP_Outbound` . Ez a szabály minden olyan alhálózat IP-címéről engedélyezi a forgalmat, amelynél az ISE a virtuális hálózatban fut a cél SFTP-kiszolgálón a tűzfal magánhálózati IP-címének használatával.

   ![Hálózati szabály beállítása a tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Hálózati szabályok gyűjteményének tulajdonságai**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*hálózati-szabály-gyűjtemény-név*> | A hálózati szabály gyűjteményének neve |
   | **Prioritás** | <*prioritás – szint*> | A szabály gyűjteményének futtatásához használandó prioritási sorrend. További információ: [Mi a Azure Firewall fogalmak](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)? |
   | **Művelet** | **Engedélyezés** | A szabályhoz végrehajtandó Művelettípus |
   |||

   **Hálózati szabály tulajdonságai**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*hálózati szabály neve*> | A hálózati szabály neve |
   | **Protokoll** | <*kapcsolatok – protokollok*> | A használandó kapcsolódási protokollok. Ha például NSG-szabályokat használ, válassza a **TCP** és az **UDP** beállítást, nem csak a **TCP protokollt**. |
   | **Forrásoldali címek** | <*ISE-alhálózat – címek*> | Az alhálózati IP-címek, amelyeken az ISE fut, és a logikai alkalmazásból származó forgalom |
   | **Cél címei** | <*cél-IP-cím*> | Annak a célhelynek az IP-címe, amelyen a kimenő forgalmat el szeretné járni. Ebben a példában ez az IP-cím az SFTP-kiszolgáló. |
   | **Célportok** | <*cél – portok*> | A célként megadott rendszer által a bejövő kommunikációhoz használt portok |
   |||

   A hálózati szabályokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

   * [Hálózatszabály konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Az Azure Firewall szabályfeldolgozási logikája](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall – gyakori kérdések](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az Network Firewall Network-Rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
