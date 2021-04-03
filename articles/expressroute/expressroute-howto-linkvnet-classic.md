---
title: 'Azure ExpressRoute: VNet összekapcsolása egy áramkörrel: klasszikus'
description: Ez a dokumentum áttekintést nyújt a virtuális hálózatok (virtuális hálózatok) ExpressRoute-áramkörökhöz való összekapcsolásáról a klasszikus üzemi modell és a PowerShell használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e8a1d48934d73f1035c6f2c4c76e56d9a0e4052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206970"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel a PowerShell (klasszikus) használatával
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segítséget nyújt a virtuális hálózatok (virtuális hálózatok) és az Azure ExpressRoute-áramkörök összekapcsolásához a PowerShell használatával. Egyetlen VNet legfeljebb négy ExpressRoute-áramkörhöz lehet kapcsolni. A cikkben ismertetett lépéseket követve létrehozhat egy új hivatkozást minden olyan ExpressRoute-áramkörhöz, amelyhez csatlakozik. A ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetésekben vagy a kettő kombinációjában is. Ez a cikk a klasszikus üzembe helyezési modellel létrehozott virtuális hálózatokra vonatkozik.

Akár 10 virtuális hálózatot is összekapcsolhat egy ExpressRoute-áramkörhöz. Minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie. Nagyobb számú virtuális hálózatot csatolhat a ExpressRoute-áramkörhöz, vagy más geopolitikai régiókban található virtuális hálózatokat is összekapcsolhat, ha engedélyezi a ExpressRoute prémium bővítményét. A prémium szintű bővítménysel kapcsolatos további információkért olvassa el a [Gyakori kérdések](expressroute-faqs.md) című részt.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
   * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-classic.md) , és engedélyezze a kapcsolati szolgáltatónak az áramkört.
   * Győződjön meg arról, hogy az Ön áramköréhez konfigurálva van az Azure-beli magánhálózati kapcsolat. Az útválasztási utasításokért tekintse meg az [Útválasztás konfigurálása](expressroute-howto-routing-classic.md) című cikket.
   * Győződjön meg arról, hogy az Azure Private peering konfigurálva van, és a hálózat és a Microsoft közötti BGP-társak is elérhetők, így a végpontok közötti kapcsolat engedélyezhető.
   * Létre kell hoznia egy virtuális hálózatot és egy virtuális hálózati átjárót, és azt teljesen ki kell építeni. A [ExpressRoute virtuális hálózatának konfigurálásához](./expressroute-howto-add-gateway-portal-resource-manager.md)kövesse az utasításokat.

### <a name="download-the-latest-powershell-cmdlets"></a>A legújabb PowerShell-parancsmagok letöltése

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ugyanahhoz az előfizetéshez tartozó virtuális hálózat összekapcsolása egy áramkörrel
A virtuális hálózatot a következő parancsmag használatával kapcsolhatja össze egy ExpressRoute-áramkörhöz. Győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra a parancsmag futtatása előtt.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Egy áramkörhöz tartozó virtuális hálózati kapcsolat eltávolítása
A következő parancsmag használatával eltávolíthatja a virtuális hálózati kapcsolatot egy ExpressRoute-áramkörhöz. Győződjön meg arról, hogy az aktuális előfizetés van kiválasztva az adott virtuális hálózathoz. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Megoszthat egy ExpressRoute-áramkört több előfizetésen keresztül is. Az alábbi ábrán egy egyszerű sematikus ábrán látható, hogy a megosztás hogyan működik a ExpressRoute-áramkörök több előfizetésen keresztül.

A nagyméretű felhőben lévő kisebb felhőket a szervezeten belüli különböző részlegekhez tartozó előfizetések ábrázolására használják. A szervezeten belüli részlegek saját előfizetést használhatnak a szolgáltatásaik üzembe helyezéséhez, de a részlegek egyetlen ExpressRoute áramkört is megoszthatnak a helyszíni hálózathoz való kapcsolódáshoz. Egyetlen részleg (ebben a példában: IT) a ExpressRoute áramkör tulajdonosa lehet. A szervezeten belüli egyéb előfizetések a ExpressRoute áramkört is használhatják.

> [!NOTE]
> A dedikált áramkör kapcsolati és sávszélesség-díjait a rendszer a ExpressRoute Circuit tulajdonosára alkalmazza. Minden virtuális hálózat ugyanazt a sávszélességet használja.
> 
> 

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Felügyelet
Az *áramkör tulajdonosa* annak az előfizetésnek a rendszergazdája/rendszergazdája, amelyben az ExpressRoute-áramkör létrejött. Az áramkör tulajdonosa engedélyezheti a más előfizetések rendszergazdái vagy rendszergazdái *számára, hogy* a saját dedikált áramkört használják. Azok a felhasználók, akik jogosultak a szervezet ExpressRoute-áramkör használatára, az előfizetésben lévő virtuális hálózatot a hitelesítésük után a ExpressRoute-áramkörhöz kapcsolhatják.

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedélyek visszavonása az összes olyan hivatkozás törlését eredményezi, amelynek a hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramköri tulajdonosi műveletek

**Engedély létrehozása**

Az áramkör tulajdonosa más előfizetések rendszergazdái számára engedélyezi a megadott áramkör használatát. A következő példában az áramkör rendszergazdája (contoso IT) lehetővé teszi egy másik előfizetés (dev-test) rendszergazdájának, hogy két virtuális hálózatot kapcsoljon az áramkörhöz. A contoso informatikai rendszergazdája ezt a Dev-Test Microsoft-azonosító megadásával teszi lehetővé. A parancsmag nem küld e-mailt a megadott Microsoft-AZONOSÍTÓnak. Az áramkör tulajdonosának explicit módon értesítenie kell a másik előfizetés tulajdonosát az engedélyezés befejezéséről.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Eredményük a következő:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Engedélyek áttekintése**

Az áramkör tulajdonosa a következő parancsmag futtatásával áttekintheti az adott áramkörön kiadott összes engedélyt:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Eredményük a következő:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Engedélyek frissítése**

Az áramkör tulajdonosa a következő parancsmaggal módosíthatja az engedélyeket:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Eredményük a következő:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Engedélyek törlése**

Az áramkör tulajdonosa visszavonhatja vagy törölheti az engedélyeket a felhasználónak a következő parancsmag futtatásával:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Áramkör felhasználói műveletei

**Engedélyek áttekintése**

Az áramkör felhasználója a következő parancsmaggal ellenőrizheti az engedélyeket:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Eredményük a következő:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Kapcsolati engedélyek beváltása**

Az áramkör-felhasználó a következő parancsmagot futtathatja egy hivatkozás engedélyezésének beváltásához:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Eredményük a következő:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Futtassa ezt a parancsot a virtuális hálózat újonnan csatolt előfizetésében:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.