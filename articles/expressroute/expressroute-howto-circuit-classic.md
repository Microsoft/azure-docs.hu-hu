---
title: 'Azure ExpressRoute: áramkör módosítása: PowerShell: klasszikus'
description: Ez a cikk végigvezeti a klasszikus ExpressRoute üzembe helyezési modell állapotának, frissítésének és törlésének és megszüntetésének lépésein.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be45d49d3f445810c7ac6a38e3e12abe178a4bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018161"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>ExpressRoute-kör módosítása a PowerShell (klasszikus) használatával

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk végigvezeti a klasszikus ExpressRoute üzembe helyezési modell állapotának, frissítésének és törlésének és megszüntetésének lépésein. Ez a cikk a klasszikus üzembehelyezési modellre vonatkozik.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Áramkör állapotának lekérése

Ezeket az adatokat bármikor lekérheti a `Get-AzureCircuit` parancsmag használatával. Ha a hívást paraméterek nélkül kívánja megtekinteni, az összes áramkör szerepel.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Egy adott ExpressRoute-áramkörről a szolgáltatás kulcsának paraméterként való átadásával kaphat információt a híváshoz.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Az összes paraméter részletes leírását a következő példa futtatásával érheti el:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Kapcsolatcsoport módosítása

Egy ExpressRoute-kör bizonyos tulajdonságait módosíthatja a kapcsolat befolyásolása nélkül.

A következő feladatokat a leállás nélkül végezheti el:

* Engedélyezheti vagy letilthatja a ExpressRoute-áramkörhöz tartozó ExpressRoute Premium-bővítményt.
* Növelje a ExpressRoute-áramkör sávszélességét, ha rendelkezésre áll a porton elérhető kapacitás. Az áramkör sávszélességének visszaminősítése nem támogatott.
* Módosítsa a mérési tervet a mért adatokról a korlátlan mennyiségű adatokra. A mérési terv korlátlan mennyiségű és mért adatok közötti módosítása nem támogatott.
* Engedélyezheti és letilthatja a *klasszikus műveletek engedélyezését*.

A korlátozásokkal és korlátozásokkal kapcsolatos további információkért tekintse meg a [ExpressRoute kapcsolatos gyakori kérdéseket](expressroute-faqs.md) .

### <a name="enable-the-expressroute-premium-add-on"></a>ExpressRoute prémium bővítmény engedélyezése

A következő PowerShell-parancsmag használatával engedélyezheti a ExpressRoute prémium bővítményt a meglévő áramkörhöz:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Az áramkör mostantól elérhetővé teszi a ExpressRoute prémium bővítmények funkcióit. Amint a parancs futtatása sikeres volt, megkezdődik a prémium szintű bővítményre vonatkozó számlázás.

### <a name="disable-the-expressroute-premium-add-on"></a>A ExpressRoute Premium bővítmény letiltása

> [!IMPORTANT]
> Ez a művelet sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak a standard szintű áramkör számára engedélyezettnél.
>
>

#### <a name="considerations"></a>Megfontolandó szempontok

* Győződjön meg arról, hogy az áramkörhöz kapcsolt virtuális hálózatok száma kevesebb, mint 10, mielőtt a prémiumról a standard szintre vált. Ha ezt nem teszi meg, a frissítési kérelem meghiúsul, és a prémium díjakat számítjuk fel.
* A többi geopolitikai régióban lévő összes virtuális hálózatot le kell kapcsolni. Ha nem, a frissítési kérelem meghiúsul, és a prémium díjakat számítjuk fel.
* Az útválasztási táblázatnak kisebbnek kell lennie, mint 4 000 útvonal a privát társak számára. Ha az útválasztási táblázat mérete nagyobb, mint 4 000 útvonal, a BGP-munkamenet elveszik, és nem lesz újra engedélyezve, amíg a meghirdetett előtagok száma nem haladja meg a 4 000 értéket.

#### <a name="to-disable-the-premium-add-on"></a>A prémium szintű bővítmény letiltása

A következő PowerShell-parancsmag használatával letilthatja a ExpressRoute Premium bővítményt a meglévő áramkörhöz:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>A ExpressRoute-áramkör sávszélességének frissítése

A szolgáltató által támogatott sávszélesség-beállításokról a [ExpressRoute gyakori kérdések](expressroute-faqs.md) című szakaszban tájékozódhat. Bármely olyan méretet választhat, amely nagyobb, mint a meglévő áramkör mérete, feltéve, hogy a fizikai port (amelyen az áramkör létrehozva van) lehetővé teszi.

> [!IMPORTANT]
> Előfordulhat, hogy újra létre kell hoznia a ExpressRoute áramkört, ha nincs elegendő kapacitás a meglévő porton. Az áramkör nem frissíthető, ha az adott helyen nem érhető el további kapacitás.
>
> Egy ExpressRoute áramkör sávszélessége nem csökkenthető megszakítás nélkül. A lefokozási sávszélességhez meg kell szüntetnie a ExpressRoute áramkört, majd újra kell telepítenie egy új ExpressRoute áramkört.
>
>

#### <a name="resize-a-circuit"></a>Áramkör átméretezése

Miután eldöntötte, hogy milyen méretűre van szüksége, a következő paranccsal méretezheti át az áramkört:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Ha az áramköre a Microsoft oldalán van méretezve, kapcsolatba kell lépnie a kapcsolat szolgáltatójával, hogy frissítse a konfigurációkat a saját oldalán, hogy az megfeleljen a változásnak. A számlázási forgalom a frissített sávszélesség beállításával kezdődik.

Ha az áramköri sávszélesség növelésekor a következő hibaüzenet jelenik meg, az azt jelenti, hogy nincs elegendő sávszélesség a meglévő áramkör létrehozásához használt fizikai porton. Törölnie kell ezt az áramkört, és létre kell hoznia egy új, a szükséges méretű áramkört.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Áramkör kiépítése és törlése

### <a name="considerations"></a>Megfontolandó szempontok

* Ahhoz, hogy a művelet sikeres legyen, az összes virtuális hálózatot le kell kapcsolni a ExpressRoute áramkörből. Ellenőrizze, hogy van-e olyan virtuális hálózat, amely kapcsolódik az áramkörhöz, ha a művelet meghiúsul.
* Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is fenntartjuk az erőforrásokat és számlázunk, amíg a szolgáltató befejezi az áramkör kiépítését, és értesítést küld nekünk.
* Ha a szolgáltató kiépítte az áramkört (a szolgáltató kiépítési állapota **nincs kiépítve**), akkor törölheti az áramkört. Ez leállítja a kapcsolatcsoport számlázását.

#### <a name="delete-a-circuit"></a>Áramkör törlése

A ExpressRoute-áramkört a következő parancs futtatásával törölheti:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
