---
title: IP-korlátozási WAF-szabály konfigurálása a Azure Front Door
description: Megtudhatja, hogyan konfigurálhat egy Web Application Firewall egy meglévő végpont IP-címeinek Azure Front Door korlátozására.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860850"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>IP-korlátozási szabály konfigurálása Web Application Firewall a Azure Front Door

Ez a cikk bemutatja, hogyan konfigurálhatja az IP-korlátozási szabályokat egy Web Application Firewall-ban (WAF) az Azure Front Door-hoz az Azure Portal, az Azure CLI, a Azure PowerShell vagy egy Azure Resource Manager sablon használatával.

Az IP-címalapú hozzáférés-vezérlési szabály egy egyéni WAF-szabály, amely lehetővé teszi a webalkalmazások hozzáférésének szabályozását. Ehhez meg kell adnia az IP-címek vagy IP-címtartományok listáját classless Inter-Domain Routing (CIDR) formátumban. Az IP-cím megfeleltetve két egyező változótípus van: **RemoteAddr** és **SocketAddr.** A RemoteAddr az eredeti ügyfél IP-címe, amely általában az X-Forwarded-For kérésfejlécen keresztül van elküldve. A SocketAddr a WAF által látható forrás IP-cím. Ha a felhasználó proxy mögött van, a SocketAddr gyakran a proxykiszolgáló címe.

Alapértelmezés szerint a webalkalmazás elérhető az internetről. Ha korlátozni szeretné az ügyfelek hozzáférését az ismert IP-címek vagy IP-címtartományok listájából, létrehozhat egy IP-címegyeztetési szabályt, amely az IP-címek listáját egyeztetési értékként tartalmazza, és az operátort "Not" (Nem) (a negate értéke true), a művelet pedig Block (Blokkolás) **lesz.** Az IP-korlátozási szabály alkalmazása után az engedélyezett listán kívüli címekről származó kérések 403 Tiltott választ kapnak.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>WAF-szabályzat konfigurálása a Azure Portal

### <a name="prerequisites"></a>Előfeltételek

Hozzon létre Azure Front Door-profilt a rövid útmutató: Front Door létrehozása magas rendelkezésre álló [globális webalkalmazáshozcímű útmutató utasításait követve.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>WAF-szabályzat létrehozása

1. A Azure Portal válassza az **Erőforrás** létrehozása lehetőséget, írja be a **Webalkalmazási** tűzfal lehetőséget a keresőmezőbe, majd válassza a Web Application Firewall **(WAF) lehetőséget.**
2. Válassza a **Létrehozás** lehetőséget.
3. A **WAF-szabályzat** létrehozása oldalon használja a következő értékeket az Alapvető **beállítások lap befejezéséhez:**
   
   |Beállítás  |Érték  |
   |---------|---------|
   |Szabályzat a     |Globális WAF (Front Door)|
   |Előfizetés     |Az előfizetés kiválasztása|
   |Erőforráscsoport     |Válassza ki azt az erőforráscsoportot, Front Door az erőforráscsoportot.|
   |Házirend neve     |Adja meg a szabályzat nevét|
   |Szabályzat állapota     |Engedélyezve|

   Válassza **a Tovább: Szabályzatbeállítások lehetőséget**

1. A Házirend **beállításai lapon** válassza a **Megelőzés lehetőséget.** A Block **response body (Blokkválasz törzse)** mezőbe írja be a *következőt: You's been blocked!* így láthatja, hogy az egyéni szabály érvényben van.
2. Válassza **a Tovább: Felügyelt szabályok lehetőséget.**
3. Válassza **a Tovább: Egyéni szabályok lehetőséget.**
4. Válassza **az Egyéni szabály hozzáadása lehetőséget.**
5. Az Egyéni **szabály hozzáadása lapon** használja a következő tesztértékeket egy egyéni szabály létrehozásához:

   |Beállítás  |Érték  |
   |---------|---------|
   |Egyéni szabály neve     |FdWafCustRule|
   |Állapot     |Engedélyezve|
   |Szabály típusa     |Match|
   |Prioritás    |100|
   |Egyezés típusa     |IP-cím|
   |Változó egyeztetése|RemoteAddr|
   |Művelet|Nem tartalmazza|
   |IP-cím vagy tartomány|10.10.10.0/24|
   |Akkor|Forgalom megtagadása|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Egyéni szabály":::

   Válassza a **Hozzáadás** lehetőséget.
6. Válassza **a Tovább: Társítás lehetőséget.**
7. Válassza **az Add frontend host (Előtere gazdagép hozzáadása) lehetőséget.**
8. Az **Előtere gazdagép beállításnál válassza** ki az előtere gazdagépet, majd válassza a **Hozzáadás lehetőséget.**
9. Válassza az **Áttekintés + létrehozás** lehetőséget.
10. A szabályzatérvényesítés után válassza a **Létrehozás lehetőséget.**

### <a name="test-your-waf-policy"></a>A WAF-szabályzat tesztelése

1. Miután a WAF-szabályzat üzembe helyezése befejeződött, keresse meg Front Door előtere gazdagépnevét.
2. Megjelenik az egyéni blokküzenet.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF-szabályteszt":::

   > [!NOTE]
   > A magánhálózati IP-címet szándékosan használták az egyéni szabályban annak garantálása érdekében, hogy a szabály aktiválódik. A tényleges üzembe helyezés  során az *adott* helyzetre vonatkozó IP-címekkel hozzon létre engedélyező és megtagadási szabályokat.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>WAF-szabályzat konfigurálása az Azure CLI-val

### <a name="prerequisites"></a>Előfeltételek
Az IP-korlátozási szabályzat konfigurálása előtt állítsa be a CLI-környezetet, és hozzon létre egy Azure Front Door profilt.

#### <a name="set-up-the-azure-cli-environment"></a>Az Azure CLI-környezet beállítása
1. Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli)vagy használja a Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza a **Try it (Kipróbálom)** gombot a következő CLI-parancsokban, majd a megnyíló Cloud Shell jelentkezzen be az Azure-fiókjába. A munkamenet kezdete után adja meg a következőt a Azure Front Door `az extension add --name front-door` hozzáadásához.
 2. Ha helyileg használja a CLI-t a Bashben, jelentkezzen be az Azure-ba a `az login` használatával.

#### <a name="create-an-azure-front-door-profile"></a>Új Azure Front Door létrehozása
Hozzon létre Azure Front Door-profilt a rövid útmutató: Front Door létrehozása magas rendelkezésre álló globális [webalkalmazáshozcímű útmutató utasításait követve.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>WAF-szabályzat létrehozása

Hozzon létre egy [WAF-szabályzatot az az network front-door waf-policy create paranccsal.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) A következő példában cserélje le az *IPAllowPolicyExampleCLI* szabályzatnevet egy egyedi házirendnévre.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Egyéni IP-hozzáférés-vezérlési szabály hozzáadása

Az [az network front-door waf-policy custom-rule create paranccsal](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) adjon hozzá egy egyéni IP-hozzáférés-vezérlési szabályt az újonnan létrehozott WAF-szabályzathoz.

A következő példákban:
-  Cserélje *le az IPAllowPolicyExampleCLI helyére* a korábban létrehozott egyedi szabályzatot.
-  Cserélje *le az ip-address-range-1,* *az ip-address-range-2 helyére* a saját tartományát.

Először hozzon létre egy IP-engedélyező szabályt az előző lépésben létrehozott szabályzathoz. 
> [!NOTE]
> **A --defer** kötelező, mert a szabálynak egy egyeztetési feltétellel kell megegyeznie, hogy a következő lépésben hozzá legyen adva.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Ezután adja hozzá a szabályhoz az egyeztetési feltételt:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>WAF-szabályzat azonosítójának megkerese 
Keresse meg a WAF-szabályzat azonosítóját az [az network front-door waf-policy show paranccsal.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) A következő példában cserélje le az *IPAllowPolicyExampleCLI* helyére a korábban létrehozott egyedi szabályzatot.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF-szabályzat csatolása egy Azure Front Door-gazdagéphez

Állítsa be Azure Front Door *WebApplicationFirewallPolicyLink* azonosítóját a szabályzatazonosítóra [az az network front-door update paranccsal.](/cli/azure/network/front-door#az_network_front_door_update) Cserélje *le az IPAllowPolicyExampleCLI helyére* a korábban létrehozott egyedi szabályzatot.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Ebben a példában a WAF-szabályzat a **FrontendEndpoints[0]** pontra lesz alkalmazva. A WAF-szabályzatot bármely előoldalhoz csatolhatja.
> [!Note]
> A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania, hogy egy WAF-szabályzatot egy Azure Front Door hozzá. A későbbi szabályzatfrissítések automatikusan alkalmazva vannak az előlapra.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>WAF-szabályzat konfigurálása Azure PowerShell

### <a name="prerequisites"></a>Előfeltételek
Az IP-korlátozási szabályzat konfigurálása előtt állítsa be a PowerShell-környezetet, és hozzon létre egy Azure Front Door profilt.

#### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Azure PowerShell olyan parancsmagokat biztosít, amelyek az [azure Azure Resource Manager modellt](../../azure-resource-manager/management/overview.md) használják az Azure-erőforrások kezeléséhez.

Az [Azure PowerShellt](/powershell/azure/) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Az oldalon található utasításokat követve jelentkezzen be a PowerShellbe az Azure-beli hitelesítő adataival, majd telepítse az Az modult.

1. Csatlakozzon az Azure-hoz a következő paranccsal, majd egy interaktív párbeszédpanelen jelentkezzen be.
    ```
    Connect-AzAccount
    ```
 2. Mielőtt telepít egy Azure Front Door modult, győződjön meg arról, hogy telepítve van a PowerShellGet modul aktuális verziója. Futtassa a következő parancsot, majd nyissa meg újra a PowerShellt.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Telepítse az Az.FrontDoor modult a következő paranccsal. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Új Azure Front Door létrehozása
Hozzon létre Azure Front Door-profilt a rövid útmutató: Front Door létrehozása magas rendelkezésre álló [globális webalkalmazáshozcímű útmutató utasításait követve.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>IP-egyeztetési feltétel meghatározása
A [New-AzFrontDoorWafMatchConditionObject paranccsal](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) határozzon meg egy IP-egyeztetési feltételt.
A következő példában cserélje *le az ip-address-range-1,* *az ip-address-range-2* helyére a saját tartományát.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Egyéni IP-cím-engedélyező szabály létrehozása

A [New-AzFrontDoorWafCustomRuleObject paranccsal](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) definiálhat egy műveletet, és beállíthatja a prioritást. A következő példában a listán nem található ügyfél-IP-ről származó kérések blokkolva lesznek.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF-szabályzat konfigurálása
Keresse meg az erőforráscsoport nevét, amely a Azure Front Door profilt tartalmazza a `Get-AzResourceGroup` használatával. Ezután konfigurálja a WAF-szabályzatot az IP-s s szabálysal a [New-AzFrontDoorWafPolicy használatával.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF-szabályzat csatolása egy Azure Front Door-gazdagéphez

WAF-szabályzatobjektum csatolása meglévő előoldali gazdagéphez, és a Azure Front Door frissítése. Először olvassa be a Azure Front Door objektumot a [Get-AzFrontDoor használatával.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) Ezután állítsa a **WebApplicationFirewallPolicyLink** *tulajdonságot* az előző lépésben létrehozott $IPAllowPolicyExamplePS erőforrás-azonosítójára a [Set-AzFrontDoor paranccsal.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Ebben a példában a WAF-szabályzat a **FrontendEndpoints[0]** pontra lesz alkalmazva. A WAF-szabályzatokat bármelyik előoldalhoz csatolhatja. A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania, hogy egy WAF-szabályzatot egy Azure Front Door hozzá. A későbbi szabályzatfrissítések automatikusan alkalmazva vannak az előlapra.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>WAF-szabályzat konfigurálása Resource Manager sablonnal
Az egyéni IP-korlátozási szabályokkal Azure Front Door WAF-szabályzatot Azure Front Door sablon megtekintéséhez kattintson a [GitHubra.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, [hogyan hozhat létre Azure Front Door profilt.](../../frontdoor/quickstart-create-front-door.md)
