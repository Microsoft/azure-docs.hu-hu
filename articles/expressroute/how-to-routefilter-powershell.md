---
title: 'Oktatóanyag: a Microsoft társközi útvonal-szűrőinek konfigurálása – Azure PowerShell'
description: Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a Microsoft társközi útvonal-szűrőit a PowerShell használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 90d4def5a1c08e305b9315f299e83e2187b6be2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91969881"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-powershell"></a>Oktatóanyag: a Microsoft társközi útvonal-szűrőinek konfigurálása a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a ExpressRoute-áramkörök útválasztási szűrőinek konfigurálásához és kezeléséhez.

Microsoft 365 olyan szolgáltatások, mint az Exchange Online, a SharePoint Online és a Skype vállalati verzió, valamint az Azure-beli nyilvános szolgáltatások, például a Storage és az SQL Database, a Microsoft-partneri kapcsolaton keresztül érhetők el. Az Azure-beli nyilvános szolgáltatások régiónként választhatók, és nem definiálhatók nyilvános szolgáltatáson belül.

Ha a Microsoft-társat egy ExpressRoute-áramkörben konfigurálja, az ezen szolgáltatásokhoz kapcsolódó összes előtag a létrehozott BGP-munkameneteken keresztül lesz meghirdetve. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP közösségi értékek és a hozzájuk kapcsolódó szolgáltatások listáját itt tekintheti meg: [BGP-Közösségek](expressroute-routing.md#bgp).

Az összes Azure-és Microsoft 365-szolgáltatáshoz való kapcsolódás nagy számú előtagokat okoz a BGP-n keresztül. Az előtagok nagy száma jelentősen megnöveli a hálózaton belüli útválasztók által karbantartott útválasztási táblázatok méretét. Ha azt tervezi, hogy csak a Microsoft-partnerek által kínált szolgáltatások egy részhalmazát szeretné használni, az útválasztási táblázatok méretét kétféleképpen csökkentheti. A következőket teheti:

* Kiszűri a nemkívánatos előtagokat az útválasztási szűrők alkalmazásával a BGP-közösségeken. Az útvonal-szűrés szabványos hálózati gyakorlat, és általában számos hálózaton belül használatos.

* Adja meg az útvonal-szűrőket, és alkalmazza őket a ExpressRoute-áramkörre. Az útválasztási szűrő egy új erőforrás, amellyel kiválaszthatja a Microsoft-társon keresztül használni kívánt szolgáltatások listáját. A ExpressRoute-útválasztók csak az útválasztási szűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - BGP-közösségi értékek beolvasása.
> - Hozzon létre egy útválasztási szűrőt és egy szűrési szabályt.
> - Útvonal-szűrő hozzárendelése egy ExpressRoute-áramkörhöz.

### <a name="about-route-filters"></a><a name="about"></a>Az útválasztási szűrők ismertetése

A Microsoft Edge-útválasztók a ExpressRoute-áramkörön konfigurált, a saját peremhálózati útválasztókkal rendelkező BGP-munkameneteket a kapcsolat szolgáltatóján keresztül hozzák létre. Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezett lista az összes BGP közösségi értékről. Ha egy ExpressRoute-áramkörhöz egy útvonal-szűrő erőforrás van meghatározva és csatolva van, akkor a BGP közösségi értékekre leképező összes előtag a hálózatra lesz hirdetve.

Ha Microsoft 365-szolgáltatásokkal szeretne útválasztási szűrőket csatolni, rendelkeznie kell a ExpressRoute-en keresztüli Microsoft 365-szolgáltatások használatára vonatkozó engedéllyel. Ha nincs engedélye arra, hogy a ExpressRoute-n keresztül használja Microsoft 365-szolgáltatásokat, az útválasztási szűrők csatlakoztatásának művelete meghiúsul. Az engedélyezési folyamattal kapcsolatos további információkért lásd: [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő.
> 
## <a name="prerequisites"></a>Előfeltételek

- A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

- Rendelkeznie kell egy aktív ExpressRoute-áramkörrel, amelyhez Microsoft-társítás van kiépítve. A következő utasításokat követve hajthatja végre ezeket a feladatokat:
  - A folytatás előtt [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-arm.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.
  - [Hozzon létre Microsoft-társat](expressroute-circuit-peerings.md) , ha közvetlenül a BGP-munkamenetet kezeli. Vagy a kapcsolat szolgáltatója kiépíti a Microsoft-társat az áramkörhöz.
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a> Az előtagok és a BGP közösségi értékek listájának beolvasása

1. A következő parancsmaggal kérheti le a Microsoft-társon keresztül elérhető szolgáltatásokhoz kapcsolódó BGP közösségi értékek és előtagok listáját:

    ```azurepowershell-interactive
    Get-AzBgpServiceCommunity
    ```

1. Készítse el az útválasztási szűrőben használni kívánt BGP-közösségi értékek listáját.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Útvonal-szűrő és egy szűrési szabály létrehozása

Egy útvonal-szűrőnek csak egy szabálya lehet, és a szabálynak "Allow" típusúnak kell lennie. Ez a szabály tartalmazhatja a BGP-közösségi értékek listáját. A parancs `az network route-filter create` csak egy útvonal-szűrő erőforrást hoz létre. Az erőforrás létrehozása után létre kell hoznia egy szabályt, és csatolni kell az útválasztási szűrő objektumhoz.

1. Útvonal-szűrő erőforrás létrehozásához futtassa a következő parancsot:

    ```azurepowershell-interactive
    New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
    ```

1. Útvonal-szűrő szabály létrehozásához futtassa a következő parancsot:
 
    ```azurepowershell-interactive
    $rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
    ```

1. Futtassa a következő parancsot a szűrési szabály az útvonal-szűrőhöz való hozzáadásához:
 
    ```azurepowershell-interactive
    $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
    $routefilter.Rules.Add($rule)
    Set-AzRouteFilter -RouteFilter $routefilter
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Az útvonal-szűrő csatolása ExpressRoute-áramkörhöz

Futtassa a következő parancsot az útvonal-szűrő ExpressRoute-áramkörhöz való csatolásához, feltéve, hogy csak a Microsoft-partneri kapcsolattal rendelkezik:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Egy útvonal szűrő tulajdonságainak beolvasása

Az útválasztási szűrők tulajdonságainak beszerzéséhez kövesse az alábbi lépéseket:

1. Futtassa a következő parancsot az útvonal-szűrő erőforrás lekéréséhez:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. A következő parancs futtatásával szerezze be az útvonal-szűrő erőforrás útválasztási szabályait:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Egy útvonal szűrő tulajdonságainak frissítése

Ha az útvonal szűrője már csatolva van egy áramkörhöz, a BGP-közösségi lista frissítései automatikusan propagálják az előtagok hirdetményének változásait a létrejött BGP-munkameneten keresztül. A következő paranccsal frissítheti az útválasztási szűrő BGP közösségi listáját:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonal-szűrő leválasztása egy ExpressRoute-áramkörből

Ha egy útvonal-szűrőt leválasztanak a ExpressRoute áramkörről, a BGP-munkameneten belül nem történik meg az előtagok meghirdetése. A következő parancs használatával leválaszthat egy ExpressRoute áramkörből:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Csak akkor törölhet egy útvonal-szűrőt, ha nincs csatlakoztatva egyetlen áramkörhöz sem. A törlés megkísérlése előtt gondoskodjon arról, hogy az útválasztási szűrő ne legyen csatlakoztatva egyetlen áramkörhöz sem. Az útvonal-szűrőt a következő paranccsal törölheti:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Az útválasztó-konfigurációs mintákkal kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Útválasztó-konfigurációs minták az Útválasztás beállításához és kezeléséhez](expressroute-config-samples-routing.md)
