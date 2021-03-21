---
title: Azure-beli virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hol találhat információt a virtuális hálózatokról, valamint arról, hogyan adhat hozzá, módosíthat vagy törölhet egy virtuális hálózati alhálózatot az Azure-ban.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: d591194f09e85c3e34ed4b904249df994a14bff5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558587"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Virtuális hálózat alhálózatának hozzáadása, módosítása vagy törlése

Megtudhatja, hogyan adhat hozzá, módosíthat vagy törölhet egy virtuális hálózati alhálózatot. A virtuális hálózatban üzembe helyezett Azure-erőforrások a virtuális hálózat egy alhálózatán vannak üzembe helyezve. Ha még nem ismeri a virtuális hálózatokat, további információt a [virtuális hálózatok áttekintésében](virtual-networks-overview.md) vagy a rövid útmutató elvégzésével [talál.](quick-create-portal.md) További információ a virtuális hálózatok kezeléséről: [virtuális hálózat létrehozása, módosítása vagy törlése](manage-virtual-network.md).

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik ilyennel, állítson be egy aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ezután hajtsa végre a fenti feladatok egyikét, mielőtt elkezdené a jelen cikk bármely szakaszának lépéseit: 

- **Portál felhasználói**: Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók**: futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell böngésző lapon keresse meg a **környezet kiválasztása** legördülő listát, majd válassza a **PowerShell** lehetőséget, ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell modul 1.0.0 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az `Connect-AzAccount` Azure-beli kapcsolatok létrehozásához is futtassa a parancsot.

- **Azure parancssori felület (CLI) felhasználói**: futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy futtassa a CLI-t a számítógépről. Ha helyileg futtatja az Azure CLI-t, használja az Azure CLI-2.0.31 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Az `az login` Azure-beli kapcsolatok létrehozásához is futtassa a parancsot.

A fiókkal, amelybe bejelentkezik, vagy csatlakozik az Azure-hoz, hozzá kell rendelni a [hálózati közreműködő szerepkör](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz, vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyek](#permissions)között felsorolt megfelelő műveletekhez van rendelve.

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. A virtuális hálózatok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

2. Válassza ki annak a virtuális hálózatnak a nevét, amelyhez hozzá kíván adni egy alhálózatot.

3. A **Beállítások** területen válassza az **alhálózatok**  >  **alhálózat** elemet.

4. Az **alhálózat hozzáadása** párbeszédpanelen adja meg a következő beállítások értékeit:

    | Beállítás | Leírás |
    | --- | --- |
    | **Név** | A névnek egyedinek kell lennie a virtuális hálózaton belül. A többi Azure-szolgáltatással való kompatibilitás érdekében javasoljuk, hogy használjon egy betűt a név első karaktere. Az Azure Application Gateway például nem fog olyan alhálózatba telepíteni, amelynek a neve egy számmal kezdődik. |
    | **Címtartomány** | <p>A tartománynak egyedinek kell lennie a virtuális hálózat címterület területén. A tartomány nem fedi át a virtuális hálózaton belüli többi alhálózati címtartományt. A Címterület osztály nélküli Inter-Domain útválasztási (CIDR) jelöléssel kell megadnia.</p><p>Ha például egy virtuális hálózatban a *10.0.0.0/16* címtartomány van megadva, akkor megadhatja a *10.0.0.0/22* alhálózati címtartományt. A legkisebb megadható tartomány a */29*, amely nyolc IP-címet biztosít az alhálózathoz. Az Azure fenntartja az egyes alhálózatok első és utolsó címeit a protokollok megfelelőségének megadásához. Három további cím van fenntartva az Azure-szolgáltatások használatára. Ennek eredményeképpen a */29* címtartományt tartalmazó alhálózat definiálása három használható IP-címet eredményez az alhálózatban.</p><p>Ha virtuális hálózatot szeretne csatlakozni egy VPN-átjáróhoz, létre kell hoznia egy átjáró-alhálózatot. További információ [az átjáró-alhálózatokkal kapcsolatos adott címtartomány-megfontolásokról](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Az alhálózat hozzáadása után a megadott feltételek szerint módosíthatja a címtartományt. Az alhálózati címtartomány módosításával kapcsolatos további információkért lásd: [alhálózat beállításainak módosítása](#change-subnet-settings).</p> |
    | **Hálózati biztonsági csoport** | Az alhálózat bejövő és kimenő hálózati forgalmának szűréséhez egy meglévő hálózati biztonsági csoportot is hozzárendelhet egy alhálózathoz. A hálózati biztonsági csoportnak ugyanabban az előfizetésben és helyen kell lennie, mint a virtuális hálózatnak. További információ a [hálózati biztonsági](./network-security-groups-overview.md) csoportokról és [a hálózati biztonsági csoportok létrehozásáról](tutorial-filter-network-traffic.md). |
    | **Útvonaltábla** | A hálózati forgalom útválasztásának más hálózatokra való vezérléséhez szükség lehet egy meglévő útválasztási táblázat egy alhálózathoz való hozzárendelésére. Az útválasztási táblázatnak ugyanabban az előfizetésben és helyen kell lennie, mint a virtuális hálózatnak. További információ az [Azure útválasztásáról](virtual-networks-udr-overview.md) és az útválasztási [táblázat létrehozásáról](tutorial-create-route-table-portal.md). |
    | **Szolgáltatásvégpontok** | <p>Egy alhálózat opcionálisan rendelkezhet egy vagy több, engedélyezett szolgáltatási végponttal. Egy szolgáltatás végpontjának engedélyezéséhez válassza ki azt a szolgáltatást vagy szolgáltatásokat, amely számára engedélyezni szeretné a szolgáltatás-végpontokat a **szolgáltatások** listájából. Az Azure egy végpont számára automatikusan konfigurálja a helyet. Alapértelmezés szerint az Azure konfigurálja a virtuális hálózat régiójához tartozó szolgáltatási végpontokat. A regionális feladatátvételi forgatókönyvek támogatása érdekében az Azure automatikusan konfigurálja a végpontokat az Azure-beli [párosított régiókba](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) az Azure Storage-hoz.</p><p>Egy szolgáltatási végpont eltávolításához szüntesse meg a szolgáltatás végpontjának eltávolításához használni kívánt szolgáltatást. Ha többet szeretne megtudni a szolgáltatási végpontokról és azokról a szolgáltatásokról, amelyek számára engedélyezhető a szolgáltatás, tekintse meg a [virtuális hálózati szolgáltatás végpontjai](virtual-network-service-endpoints-overview.md)című témakört. Miután engedélyezte a szolgáltatási végpontot a szolgáltatáshoz, engedélyeznie kell az alhálózat hálózati hozzáférését is a szolgáltatással létrehozott erőforrásokhoz. Ha például engedélyezi a Service-végpontot a **Microsoft. Storage** szolgáltatáshoz, engedélyeznie kell a hálózati hozzáférést minden olyan Azure Storage-fiókhoz, amelyhez hálózati hozzáférést szeretne biztosítani. Ha engedélyezni szeretné a hálózati hozzáférést azon alhálózatokhoz, amelyeken a szolgáltatási végpont engedélyezve van, tekintse meg a szolgáltatás végpontját engedélyező egyéni szolgáltatás dokumentációját.</p><p>Annak ellenőrzéséhez, hogy a szolgáltatási végpont engedélyezve van-e az alhálózaton, tekintse meg az alhálózat bármely hálózati adapterének [érvényes útvonalait](diagnose-network-routing-problem.md) . A végpontok konfigurálásakor megjelenik egy *alapértelmezett* útvonal a szolgáltatás előtagjaival, és a **VirtualNetworkServiceEndpoint** következő ugrási típusa jelenik meg. További információ az útválasztásról: [virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).</p> |
    | **Alhálózat delegálása** | Egy alhálózat opcionálisan rendelkezhet egy vagy több delegálással. Az alhálózati delegálás explicit jogosultságokat biztosít a szolgáltatás számára, hogy a szolgáltatás telepítése során egyedi azonosítóval hozzon létre szolgáltatás-specifikus erőforrásokat az alhálózatban. Egy szolgáltatás delegálásához válassza ki azt a szolgáltatást, amelyet delegálni szeretne a **szolgáltatások** listájából. |

5. Ha hozzá szeretné adni az alhálózatot a kiválasztott virtuális hálózathoz, kattintson az **OK gombra**.

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alhálózat beállításainak módosítása

1. A virtuális hálózatok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

2. Válassza ki annak a virtuális hálózatnak a nevét, amely a módosítani kívánt alhálózatot tartalmazza.

3. A **Beállítások** területen válassza az **alhálózatok** lehetőséget.

4. Az alhálózatok listájában válassza ki azt az alhálózatot, amelynek a beállításait módosítani szeretné.

5. Az alhálózat lapon módosítsa a következő beállítások bármelyikét:

    | Beállítás | Leírás |
    | --- | --- |
    | **Címtartomány** | Ha az alhálózaton belül nincs erőforrás telepítve, módosíthatja a címtartományt. Ha bármilyen erőforrás létezik az alhálózatban, akkor az erőforrásokat át kell helyeznie egy másik alhálózatba, vagy először törölnie kell őket az alhálózatból. Az erőforrások áthelyezéséhez vagy törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokban található erőforrások áthelyezésének és törlésének megismeréséhez olvassa el az egyes erőforrástípusok dokumentációját. Tekintse meg az [alhálózat hozzáadása](#add-a-subnet)szakasz 4. lépésében a **címtartomány** korlátozásait. |
    | **Felhasználók** | Az alhálózathoz való hozzáférést beépített szerepkörök vagy saját egyéni szerepkörök használatával szabályozhatja. Ha többet szeretne megtudni a szerepkörök és a felhasználók az alhálózat eléréséhez való hozzárendeléséről, olvassa el az [Azure-szerepkörök kiosztása](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört. |
    | **Hálózati biztonsági csoport** és **Útválasztási táblázat** | Lásd az [alhálózat hozzáadása](#add-a-subnet)című témakör 4. lépését. |
    | **Szolgáltatásvégpontok** | <p>Lásd: szolgáltatási végpontok az [alhálózat hozzáadása](#add-a-subnet)szakasz 4. lépésében. Ha egy meglévő alhálózathoz engedélyez egy szolgáltatási végpontot, győződjön meg arról, hogy az alhálózat egyik erőforrásán sem futnak kritikus feladatok. A szolgáltatási végpontok az alhálózaton lévő összes hálózati adapter útvonalait átkapcsolják. A szolgáltatási végpontok az alapértelmezett útvonalat használják a *0.0.0.0/0* -címnek és a következő ugrási típusnak az *Internet* használatával, egy új útvonal használatára a szolgáltatás előtagjaival és a *VirtualNetworkServiceEndpoint* következő ugrási típusával.</p><p>A kapcsoló során előfordulhat, hogy a nyitott TCP-kapcsolatok megszakadnak. A szolgáltatási végpont nincs engedélyezve, amíg az összes hálózati adapter számára a szolgáltatás felé irányuló adatforgalom az új útvonallal frissül. További információ az útválasztásról: [virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).</p> |
    | **Alhálózat delegálása** | Lásd: szolgáltatási végpontok az [alhálózat hozzáadása](#add-a-subnet)szakasz 4. lépésében. Az alhálózat delegálása nulla vagy több, engedélyezett delegálásra módosítható. Ha egy szolgáltatás erőforrása már telepítve van az alhálózatban, akkor az alhálózati delegálás nem vehető fel és nem távolítható el, amíg el nem távolítja a szolgáltatás összes erőforrását. Egy másik szolgáltatás delegálásához válassza ki azt a szolgáltatást, amelyet delegálni szeretne a **szolgáltatások** listájából. |

6. Kattintson a **Mentés** gombra.

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Alhálózat törlése

Csak akkor törölhet egy alhálózatot, ha nincsenek erőforrások az alhálózatban. Ha az erőforrások az alhálózaton vannak, törölnie kell ezeket az erőforrásokat az alhálózat törlése előtt. Az erőforrások törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokban található erőforrások törlésének megismeréséhez olvassa el az egyes erőforrástípusok dokumentációját.

1. A virtuális hálózatok megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális hálózatokat**.

2. Válassza ki annak a virtuális hálózatnak a nevét, amely a törölni kívánt alhálózatot tartalmazza.

3. A **Beállítások** területen válassza az **alhálózatok** lehetőséget.

4. Az alhálózatok listájában válassza ki a törölni kívánt alhálózatot.

5. Válassza a **Törlés** lehetőséget, majd a megerősítő párbeszédpanelen válassza az **Igen** lehetőséget.

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az Network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Engedélyek

Az alhálózatokon lévő feladatok elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) , vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely a következő táblázatban szereplő megfelelő műveletekhez van rendelve:

|Művelet                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft. Network/virtualNetworks/alhálózatok/olvasás                           |   Virtuális hálózat alhálózatának beolvasása              |
|Microsoft. Network/virtualNetworks/alhálózatok/írás                          |   Virtuális hálózati alhálózat létrehozása vagy frissítése  |
|Microsoft. Network/virtualNetworks/alhálózatok/törlés                         |   Virtuális hálózati alhálózat törlése            |
|Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet                    |   Csatlakozás virtuális hálózathoz                     |
|Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet  |   Szolgáltatási végpont engedélyezése egy alhálózathoz     |
|Microsoft. Network/virtualNetworks/Subnets/virtualMachines/READ           |   Virtuális gépek beolvasása egy alhálózaton       |

## <a name="next-steps"></a>Következő lépések

- Virtuális hálózat és alhálózatok létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) parancsfájl használatával vagy Azure [Resource Manager-sablonok](template-samples.md) használatával
- [Azure Policy-definíciók](./policy-reference.md) létrehozása és társítása virtuális hálózatokhoz