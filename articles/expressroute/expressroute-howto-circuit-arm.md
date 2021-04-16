---
title: 'Rövid útmutató: Kapcsolat kapcsolati kapcsolat létrehozása és módosítása az ExpressRoute-Azure PowerShell'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre, hozhat létre, hozhat létre, ellenőrizhet, frissíthet, törölhet és megszüntethet egy ExpressRoute-kapcsolatkört.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-api
ms.openlocfilehash: 50307e40526f5cce4d1349180b29fac00fb70943
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534948"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Rövid útmutató: ExpressRoute-kapcsolatkör létrehozása és módosítása Azure PowerShell

Ez a rövid útmutató bemutatja, hogyan hozhat létre Egy ExpressRoute-kapcsolatkört PowerShell-parancsmagok és a Azure Resource Manager-modell használatával. Azt is ellenőrizheti, hogy a kapcsolati kör állapota frissítve, törölve vagy megszüntetve van-e.

## <a name="prerequisites"></a>Előfeltételek

* A [konfigurálás megkezdése](expressroute-prerequisites.md) előtt [tekintse](expressroute-workflows.md) át az előfeltételeket és a munkafolyamatokat.
* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure PowerShell vagy helyileg telepített Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kapcsolat kapcsolat kapcsolati kapcsolat létrehozása és kiépítése
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>A támogatott szolgáltatók, helyek és sávszélességek listájának lekérte
Mielőtt létrehoz egy ExpressRoute-kapcsolatkört, szüksége lesz a támogatott kapcsolatszolgáltatók, helyek és sávszélesség-beállítások listájára.

A **Get-AzExpressRouteServiceProvider PowerShell-parancsmag** ezeket az információkat adja vissza, amelyeket a későbbi lépésekben használni fog:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Ellenőrizze, hogy a kapcsolatszolgáltató szerepel-e a listán. Jegyezze fel az alábbi adatokat, amelyekre később szüksége lesz a kapcsolati kör létrehozásakor:

* Name
* PeeringLocations
* BandwidthsOffered

Most már készen áll egy ExpressRoute-kapcsolatkör létrehozására.

### <a name="create-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport létrehozása
Ha még nem rendelkezik erőforráscsoporttal, létre kell hoznia egyet az ExpressRoute-kapcsolatcsoport létrehozása előtt. Ezt a következő parancs futtatásával használhatja:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Az alábbi példa bemutatja, hogyan hozhat létre egy 200 Mbps sebességű ExpressRoute-kapcsolatkört az Equinix segítségével a Szilícium-völgyben. Ha más szolgáltatót és más beállításokat használ, a kérés igénylésekor cserélje le ezt az információt. Az alábbi példával kérhet új szolgáltatáskulcsot:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Győződjön meg arról, hogy a megfelelő termékváltozat-szintet és termékváltozat-családot adja meg:

* A termékváltozat-szint határozza meg, hogy az ExpressRoute-kapcsolatkör [Helyi,](expressroute-faqs.md#expressroute-local)Standard vagy [Prémium.](expressroute-faqs.md#expressroute-premium) Megadhatja a *Local (Helyi)*, *Standard (Standard) vagy *Premium (Prémium) értéket.* A termékváltozatot nem módosíthatja *Standard/Prémium* termékváltozatról Local *(Helyi) termékváltozatra.*
* A számlázási típust az SKU-család határozza meg. A forgalmi díjas adatcsomaghoz a *MeteredData,* korlátlan adatcsomaghoz *pedig az UnlimitedData* értéket adhatja meg. A számlázási típust *a MeteredData* típusról *az UnlimitedData* típusra módosíthatja, de nem módosíthatja *az UnlimitedData* típust *MeteredData típusra.* A *helyi kapcsolati* kapcsolat mindig *UnlimitedData.*

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatkör a szolgáltatáskulcs kibocsátásának pillanatában lesz kiszámlázva. Győződjön meg arról, hogy ezt a műveletet akkor végzi el, amikor a kapcsolatszolgáltató készen áll a kapcsolat kapcsolati kapcsolat létesítéséhez.
>

A válasz tartalmazza a szolgáltatáskulcsot. Az összes paraméter részletes leírását az alábbi parancs futtatásával kaphatja meg:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Az összes ExpressRoute-kapcsolat kapcsolati kör felsorolása
Az összes létrehozott ExpressRoute-kapcsolat kapcsolati kör listáját a **Get-AzExpressRouteCircuit** parancs futtatásával lehet lekérte:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A válasz az alábbi példához hasonló:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

Ezt az információt bármikor lekérheti a `Get-AzExpressRouteCircuit` parancsmag használatával. A paraméterek nélkül hívás listázza az összes kapcsolatkört. A szolgáltatáskulcs megjelenik a *ServiceKey mezőben:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

A válasz az alábbi példához hasonló:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>A szolgáltatáskulcs küldése a kapcsolatszolgáltatónak a kiépítéshez
*A ServiceProviderProvisioningState* a szolgáltatói oldalon a kiépítés aktuális állapotáról nyújt információt. Az Állapot a Microsoft oldalán jelzi az állapotot. A kapcsolat kapcsolati kapcsolat létesítésével kapcsolatos további információkért lásd: [Munkafolyamatok.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Amikor létrehoz egy új ExpressRoute-kapcsolatkört, az a következő állapotban van:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Ha a kapcsolatszolgáltató jelenleg engedélyezi a kapcsolatszolgáltatót, a kapcsolati kapcsolat a következő állapotra változik:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Az ExpressRoute-kapcsolat kapcsolat kapcsolatának a következő állapotban kell lennie:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Rendszeres időközönként ellenőrizze az áramköri kulcs állapotát és állapotát
A szolgáltatáskulcs állapotának és állapotának ellenőrzése jelzi, ha a szolgáltató kiépíti a kapcsolatkört. Miután konfigurálta a kapcsolatkört, *a ServiceProviderProvisioningState* Kiépítve ként jelenik meg, ahogyan az alábbi példában látható: 

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A válasz az alábbi példához hasonló:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="create-your-routing-configuration"></a>Az útválasztási konfiguráció létrehozása
Részletes útmutatásért tekintse meg az [ExpressRoute-kapcsolatcsoport útválasztásának](expressroute-howto-routing-arm.md) konfigurálási cikkét a kapcsolatcsoport-társviszonyok létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak azokra a kapcsolati körökre vonatkoznak, amelyek 2. réteg kapcsolati szolgáltatásokat nyújtó szolgáltatókkal vannak létrehozva. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegi szolgáltatásokat (általában IP VPN-t, például MPLS-t) kínál, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
Ezután csatolja a virtuális hálózatot az ExpressRoute-kapcsolathálózathoz. A virtuális [hálózatok ExpressRoute-kapcsolatokkal](expressroute-howto-linkvnet-arm.md) való összekapcsolása cikkben az üzembehelyezés-Resource Manager dolgozhat.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute-kapcsolat kapcsolat kapcsolati állapotának lekért állapota
Ezt az információt bármikor lekérheti a **Get-AzExpressRouteCircuit** parancsmaggal. A paraméterek nélkül hívás az összes kapcsolatkört listázza.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

A válasz a következő példához hasonló:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Egy adott ExpressRoute-kapcsolatcsoport adatait úgy kaphatja meg, ha az erőforráscsoport nevét és a kapcsolatcsoport nevét paraméterként átkalmazhatja a híváshoz:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

A válasz az alábbi példához hasonló:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                        "Name": "Standard_MeteredData",
                                        "Tier": "Standard",
                                        "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

Az összes paraméter részletes leírását az alábbi parancs futtatásával kaphatja meg:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute-kapcsolat kapcsolat kapcsolatának módosítása
Az ExpressRoute-kapcsolat kapcsolatának bizonyos tulajdonságait a kapcsolat befolyásolása nélkül módosíthatja.

A következő feladatokat használhatja állásidő nélkül:

* ExpressRoute prémium bővítmény engedélyezése vagy letiltása az ExpressRoute-kapcsolaton. A termékváltozat *Standard/Prémiumról* *Helyire* való módosítása nem támogatott.
* Növelje az ExpressRoute-kapcsolatkör sávszélességét, ha van szabad kapacitás a porton. A kapcsolat körök sávszélességének csökkentése nem támogatott.
* Módosítsa a mérési tervet Forgalmi díjas adatokról Korlátlan adatra. A mérési terv Korlátlan adatról Forgalmi díjas adatokra váltása nem támogatott.
* Engedélyezheti és letilthatja a *Klasszikus műveletek engedélyezése lehetőséget.*

A korlátozásokkal és korlátozásokkal kapcsolatos további információkért tekintse meg az [ExpressRoute gyakori kérdéseket.](expressroute-faqs.md)

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény engedélyezése
A következő PowerShell-kódrészlet használatával engedélyezheti az ExpressRoute prémium bővítményt a meglévő kapcsolaton:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

A kapcsolaton mostantól engedélyezve vannak az ExpressRoute prémium bővítmény funkciói. Amint a parancs sikeresen lefutott, megkezdjük a prémium bővítmény funkció számlázását.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute prémium bővítmény letiltása
> [!IMPORTANT]
> Ha olyan erőforrásokat használ, amelyek nagyobbak, mint ami a standard kapcsolatkör számára engedélyezett, ez a művelet meghiúsulhat.
>

Tekintse meg az alábbi információkat:

* A prémiumról standardra való visszalépés előtt meg kell győződnie arról, hogy a kapcsolati kapcsolathoz csatolt virtuális hálózatok száma kevesebb mint 10. Ha nem, a frissítési kérelem meghiúsul, és prémium díjszabás szerint számlázunk Önnek.
* A többi geopolitikai régióban lévő összes virtuális hálózatot először leválasztottnak kell lennie. Ha nem távolítja el a hivatkozást, a frissítési kérelem sikertelen lesz, és továbbra is prémium díjakat számlázunk Önnek.
* A privát társviszony-létesítéshez az útvonaltáblának 4000-esnél kevesebb útvonalnak kell lennie. Ha az útvonaltábla mérete nagyobb 4000 útvonalnál, a BGP-munkamenet el fog csökkenni. A BGP-munkamenet csak akkor lesz újra engedélyezve, ha a meghirdetett előtagok száma 4000 alá nem esik.

Az ExpressRoute prémium bővítményét a következő PowerShell-parancsmaggal tilthatja le a meglévő kapcsolaton:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Az ExpressRoute-kapcsolat kapcsolat kapcsolati sávszélességének frissítése
A szolgáltató támogatott sávszélesség-beállításaiért tekintse meg az [ExpressRoute gyakori kérdéseket.](expressroute-faqs.md) Bármilyen, a meglévő kapcsolati kör méretnél nagyobb méretet választhat.

> [!IMPORTANT]
> Előfordulhat, hogy újra létre kell hoznia az ExpressRoute-kapcsolatkört, ha nincs elegendő kapacitás a meglévő porton. Nem frissítheti a kapcsolatkört, ha nem áll rendelkezésre további kapacitás ezen a helyen.
>
> Az ExpressRoute-kapcsolat kapcsolatok sávszélessége megszakítás nélkül nem csökkenthető. A sávszélesség-visszalépéshez meg kell megszüntetni az ExpressRoute-kapcsolatkört, majd újra ki kellépíteni egy új ExpressRoute-kapcsolatkört.
>

Miután eldönti, hogy milyen méretre van szüksége, a következő paranccsal méretezze át a kapcsolatkört:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

A kapcsolati kör a Microsoft oldalán lesz frissítve. Ezután kapcsolatba kell lépnie a kapcsolatszolgáltatóval, hogy frissítse a konfigurációkat a saját oldalukon, hogy azok megegyeznek ezzel a módosítással. Az értesítés után megkezdjük a frissített sávszélesség-beállítás számlázását.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>A termékváltozat áthelyezése forgalmi díjasról korlátlanra
Az ExpressRoute-kapcsolatörök termékváltozatát a következő PowerShell-kódrészlet használatával módosíthatja:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és a klasszikus Resource Manager szabályozása
Tekintse át az [ExpressRoute-kapcsolati körök áthelyezése a klasszikusból a Resource Manager modellbe útmutatót.](expressroute-howto-move-arm.md)

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolat kapcsolat kapcsolatának megszüntetése
Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell leválaszteni az ExpressRoute-kapcsolathálózatról. Ha ez a művelet sikertelen, ellenőrizze, hogy vannak-e virtuális hálózatok csatolva a kapcsolathálózathoz.
* Ha az ExpressRoute-kapcsolatkör szolgáltatójának kiépítési állapota Kiépítés vagy Kiépítve, akkor a szolgáltatóval együtt kell megszüntetnie a kapcsolatkört a szolgáltató oldalán.   Továbbra is fenntartjuk az erőforrásokat, és kiszámlázjuk, amíg a szolgáltató be nem fejeződik a kapcsolatkészlet megszüntetése után, és nem küld nekünk értesítést.
* Ha a szolgáltató megszüntette a kapcsolatkört, ami azt jelenti, hogy a szolgáltató kiépítési állapota **Nincs** kiépítve, akkor törölheti a kapcsolatkört. A kapcsolat kapcsolati kör számlázása ekkor leáll.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ExpressRoute-kapcsolatkört a következő parancs futtatásával törölheti:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Következő lépések

Miután létrehozott egy kapcsolatkört, és kiépíti a szolgáltatóval, folytassa a következő lépéssel a társviszony-létesítés konfigurálásán:

> [!div class="nextstepaction"]
> [Az ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása](expressroute-howto-routing-arm.md)
