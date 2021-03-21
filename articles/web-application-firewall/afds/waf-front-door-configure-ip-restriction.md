---
title: IP-korlátozási WAF szabály konfigurálása az Azure-beli bejárati ajtóhoz
description: Megtudhatja, hogyan konfigurálhat egy webalkalmazási tűzfalszabály-szabályt egy meglévő Azure-beli bejárati végpont IP-címeinek korlátozására.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 65e378c0380804c13e4b42d855aede7781b93592
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211668"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>IP-korlátozási szabály konfigurálása az Azure-hoz készült webalkalmazási tűzfallal

Ez a cikk bemutatja, hogyan konfigurálhat IP-korlátozási szabályokat egy webalkalmazási tűzfalon (WAF) az Azure bejárati ajtóhoz a Azure Portal, az Azure CLI, a Azure PowerShell vagy egy Azure Resource Manager sablon használatával.

Az IP-cím alapú hozzáférés-vezérlési szabály egy egyéni WAF-szabály, amely lehetővé teszi a webalkalmazásokhoz való hozzáférés szabályozását. Ezt úgy végezheti el, hogy az IP-címek és az IP-címtartományok listáját az osztály nélküli Inter-Domain útválasztási (CIDR) formátumban határozza meg. Az IP-címek egyeztetése, a **RemoteAddr** és a **SocketAddr** kétféle egyezési változóval rendelkezik. A RemoteAddr az az eredeti ügyfél IP-címe, amelyet általában X-továbbított-kérelem fejlécre küldenek. A SocketAddr a forrás IP-WAF látja. Ha a felhasználó proxy mögött található, a SocketAddr gyakran a proxykiszolgáló címe.

Alapértelmezés szerint a webalkalmazás elérhető az internetről. Ha szeretné korlátozni az ügyfelek hozzáférését az ismert IP-címek vagy IP-címtartományok listájáról, létrehozhat egy olyan IP-megfeleltetési szabályt, amely az IP-címek listáját tartalmazza egyező értékként, és beállítja az operátort a "not" (tagadás igaz) és a **blokkolt** művelet számára. Az IP-korlátozási szabály alkalmazása után az ezen az engedélyezett listán kívüli címekről származó kérelmek 403-es tiltott választ kapnak.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>WAF házirend konfigurálása a Azure Portal

### <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy Azure-beli bejárati profilt a gyors üzembe helyezési útmutatóban ismertetett utasításokat követve, a gyors [rendelkezésre állású globális webalkalmazások létrehozásához](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>WAF szabályzat létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása** elemet, írja be a  **webalkalmazási tűzfal** kifejezést a keresőmezőbe, majd válassza a **webalkalmazási tűzfal (WAF)** lehetőséget.
2. Válassza a **Létrehozás** lehetőséget.
3. A **WAF házirend létrehozása** lapon a következő értékekkel fejezheti be az **alapok** lapot:
   
   |Beállítás  |Érték  |
   |---------|---------|
   |Házirend a következőhöz:     |Globális WAF (bejárati ajtó)|
   |Előfizetés     |Az előfizetés kiválasztása|
   |Erőforráscsoport     |Válassza ki azt az erőforráscsoportot, amelyben a bejárati ajtó található.|
   |Házirend neve     |Adja meg a szabályzat nevét|
   |Házirend állapota     |Engedélyezve|

   Válassza a **tovább lehetőséget: házirend-beállítások**

1. A **házirend-beállítások** lapon válassza a **megelőzés** lehetőséget. A **blokk válasz törzséhez** írja be a következőt: *.* láthatja, hogy az egyéni szabály érvényben van.
2. Válassza a **Next (tovább): felügyelt szabályok** elemet.
3. Válassza a Next (tovább) lehetőséget **: egyéni szabályok**.
4. Válassza az **Egyéni szabály hozzáadása** lehetőséget.
5. Az **Egyéni szabály hozzáadása** lapon a következő tesztelési értékeket használva hozzon létre egy egyéni szabályt:

   |Beállítás  |Érték  |
   |---------|---------|
   |Egyéni szabály neve     |FdWafCustRule|
   |Állapot     |Engedélyezve|
   |Szabály típusa     |Match|
   |Prioritás    |100|
   |Egyezés típusa     |IP-cím|
   |Egyezés változó|RemoteAddr|
   |Művelet|Nem tartalmazza|
   |IP-cím vagy-tartomány|10.10.10.0/24|
   |Majd|Forgalom megtagadása|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Egyéni szabály":::

   Válassza a **Hozzáadás** lehetőséget.
6. Válassza a **Tovább: társítás** lehetőséget.
7. Válassza a előtér- **gazda hozzáadása** lehetőséget.
8. A előtér- **gazdagéphez** válassza ki a előtér-gazdagépet, és válassza a **Hozzáadás** lehetőséget.
9. Válassza az **Áttekintés + létrehozás** lehetőséget.
10. A szabályzat érvényesítése után válassza a **Létrehozás** lehetőséget.

### <a name="test-your-waf-policy"></a>A WAF szabályzat tesztelése

1. A WAF házirend üzembe helyezésének befejezése után keresse meg a front Door előtér-gazdagép nevét.
2. Az egyéni blokk üzenetnek kell megjelennie.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF-szabály tesztelése":::

   > [!NOTE]
   > Egy magánhálózati IP-címet szándékosan használtak az egyéni szabályban annak biztosítására, hogy a szabály aktiválódik. Tényleges telepítés esetén hozzon létre *engedélyezési* és *megtagadási* szabályokat az adott helyzethez tartozó IP-címek használatával.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>WAF szabályzat konfigurálása az Azure CLI-vel

### <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az IP-korlátozási szabályzat konfigurálását, állítsa be a CLI-környezetet, és hozzon létre egy Azure-beli bejárati profilt.

#### <a name="set-up-the-azure-cli-environment"></a>Az Azure CLI-környezet beállítása
1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli)-t, vagy használja a Azure Cloud shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki az alábbi CLI-parancsok **kipróbálása** gombot, majd jelentkezzen be az Azure-fiókjába a megnyíló Cloud Shell-munkamenetben. A munkamenet elindítása után adja meg `az extension add --name front-door` Az Azure bejárati ajtó bővítményét.
 2. Ha a parancssori felületet a Bashben helyileg használja, jelentkezzen be az Azure-ba a használatával `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Azure-beli előtérben lévő profil létrehozása
Hozzon létre egy Azure-beli bejárati profilt a gyors üzembe helyezési útmutatóban ismertetett utasításokat követve, a gyors [rendelkezésre állású globális webalkalmazások létrehozásához](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>WAF szabályzat létrehozása

Hozzon létre egy WAF szabályzatot az az [Network elsőfékes-Door WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-create) parancs használatával. Az alábbi példában cserélje le a szabályzat neve *IPAllowPolicyExampleCLI* egyedi házirend-névre.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Egyéni IP-hozzáférés-vezérlési szabály hozzáadása

Az az [Network elülső WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule#ext-front-door-az-network-front-door-waf-policy-rule-create) paranccsal adhat hozzá egyéni IP-hozzáférés-vezérlési szabályt az IMÉNT létrehozott WAF-szabályzathoz.

Az alábbi példákban:
-  Cserélje le a *IPAllowPolicyExampleCLI* -t a korábban létrehozott egyedi szabályzatra.
-  Cserélje le az *IP-cím-Range-1*, *IP-cím-Range-2* tartományt a saját tartományára.

Először hozzon létre egy IP-engedélyezési szabályt az előző lépésben létrehozott házirendhez. 
> [!NOTE]
> **– a késleltetés** megadása kötelező, mert egy szabálynak meg kell egyeznie a következő lépésben felvenni kívánt egyeztetési feltétellel.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Ezután adja hozzá az egyeztetési feltételt a szabályhoz:

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
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>WAF szabályzat AZONOSÍTÓjának megkeresése 
A WAF szabályzat AZONOSÍTÓjának megkereséséhez használja az az [Network elsőfékes-Door WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-show) parancsot. Cserélje le a *IPAllowPolicyExampleCLI* a következő példában a korábban létrehozott egyedi szabályzatra.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF-szabályzat csatolása egy Azure-beli előtér-előtér-gazdagéphez

Állítsa be az Azure bejárati ajtó *WebApplicationFirewallPolicyLink* azonosítóját a házirend-azonosítóra az az [Network elsőfékes-Door Update](/cli/azure/ext/front-door/network/front-door#ext-front-door-az-network-front-door-update) paranccsal. Cserélje le a *IPAllowPolicyExampleCLI* -t a korábban létrehozott egyedi szabályzatra.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Ebben a példában a WAF szabályzat a **FrontendEndpoints [0]** értékre van alkalmazva. A WAF-szabályzatot bármelyik előtér-végponthoz csatolhatja.
> [!Note]
> A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania ahhoz, hogy egy WAF-szabályzatot egy Azure-beli előtér-előtérben lehessen kapcsolni. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>WAF szabályzat konfigurálása Azure PowerShell

### <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az IP-korlátozási szabályzat konfigurálását, állítsa be a PowerShell-környezetet, és hozzon létre egy Azure-beli bejárati profilt.

#### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Azure PowerShell olyan parancsmagokat biztosít, amelyek a [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez.

Az [Azure PowerShellt](/powershell/azure/) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be a PowerShellbe az Azure-beli hitelesítő adataival, majd telepítse az az modult.

1. Kapcsolódjon az Azure-hoz az alábbi paranccsal, majd jelentkezzen be egy interaktív párbeszédablak használatával.
    ```
    Connect-AzAccount
    ```
 2. Mielőtt telepítené az Azure-beli bejárati modult, győződjön meg arról, hogy a PowerShellGet modul aktuális verziója van telepítve. Futtassa a következő parancsot, majd nyissa meg újra a PowerShellt.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Telepítse az az. FrontDoor modult az alábbi parancs használatával. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure-beli előtérben lévő profil létrehozása
Hozzon létre egy Azure-beli bejárati profilt a gyors üzembe helyezési útmutatóban ismertetett utasításokat követve, a gyors [rendelkezésre állású globális webalkalmazások létrehozásához](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>IP-egyeztetési feltétel definiálása
A [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) parancs használatával határozza meg az IP-egyeztetés feltételeit.
Az alábbi példában cserélje le az *IP-cím-tartomány-1*, *IP-cím-tartomány-2* értékét a saját tartományára.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Egyéni IP-engedélyezési szabály létrehozása

A [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) parancs használatával Definiáljon egy műveletet, és állítsa be a prioritást. A következő példában a listán nem szereplő ügyfelek IP-címeitől érkező kérések le lesznek tiltva.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF házirend konfigurálása
Keresse meg az Azure-beli bejárati profilt tartalmazó erőforráscsoport nevét a használatával `Get-AzResourceGroup` . Ezután konfigurálja a WAF szabályzatot az IP-szabállyal a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)használatával.

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF-szabályzat csatolása egy Azure-beli előtér-előtér-gazdagéphez

WAF házirend-objektum csatolása meglévő előtér-gazdagéphez és az Azure-beli előtér-tulajdonságok frissítése. Először a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)használatával kérje le az Azure-beli előtérben lévő objektumot. Ezután állítsa a **WebApplicationFirewallPolicyLink** tulajdonságot az előző lépésben létrehozott *$IPALLOWPOLICYEXAMPLEPS* erőforrás-azonosítóra a [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) parancs használatával.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Ebben a példában a WAF szabályzat a **FrontendEndpoints [0]** értékre van alkalmazva. WAF-szabályzatot bármelyik előtér-végponthoz csatolhat. A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania ahhoz, hogy egy WAF-szabályzatot egy Azure-beli előtér-előtérben lehessen kapcsolni. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>WAF házirend konfigurálása Resource Manager-sablonnal
Ha meg szeretné tekinteni a sablont, amely létrehoz egy Azure-beli bejárati házirendet és egy WAF szabályzatot egyéni IP-korlátozási szabályokkal, lépjen a [githubra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre Azure-beli bejárati profilt](../../frontdoor/quickstart-create-front-door.md).
