---
title: Azure-beli virtuális hálózat alhálózatának hozzáadása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hol találhat információt a virtuális hálózatokról, és hogyan adhat hozzá, módosíthat vagy törölhet egy virtuális hálózati alhálózatot az Azure-ban.
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
ms.openlocfilehash: 1e655b20d2f6295f0d6cfe8008fee7b360525611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774286"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Virtuális hálózat alhálózatának hozzáadása, módosítása vagy törlése

Megtudhatja, hogyan adhat hozzá, módosíthat vagy törölhet virtuális hálózat alhálózatát. A virtuális hálózatban üzembe helyezett Azure-erőforrások a virtuális hálózat egy alhálózatán vannak üzembe helyezve. Ha még csak most ismerkedik a virtuális hálózatokkal, a virtuális hálózatok áttekintésében vagy egy rövid útmutatóban [talál további információt.](quick-create-portal.md) [](virtual-networks-overview.md) További információ a virtuális hálózatok kezeléséről: Virtuális hálózat [létrehozása, módosítása vagy törlése.](manage-virtual-network.md)

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik előfizetéssel, állítson be egy aktív előfizetéssel egy Azure-fiókot. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ezután a cikk bármely szakaszában el kell végrehajtania az alábbi feladatok valamelyikét, mielőtt elkezdi a lépéseket: 

- **Portálfelhasználók:** Jelentkezzen [](https://portal.azure.com) be Azure Portal azure-fiókjával.

- **PowerShell-felhasználók:** Futtassa a parancsokat a Azure Cloud Shell, [vagy](https://shell.azure.com/powershell)futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell lapon keresse meg a  Környezet kiválasztása legördülő listát, majd válassza a **PowerShellt,** ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az `Connect-AzAccount` Azure-ral való kapcsolat létrehozásához futtassa a (futtatás) futtatását is.

- **Azure parancssori felület (CLI)** felhasználói: Futtassa [](https://shell.azure.com/bash)a Azure Cloud Shell parancsokat, vagy futtassa a parancssori felületet a számítógépről. Ha az Azure CLI-t helyileg futtatja, használja az Azure CLI 2.0.31-es vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Az `az login` Azure-ral való kapcsolat létrehozásához futtassa a (futtatás) futtatását is.

A fióknak, amelybe bejelentkezik, vagy amelyről [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) csatlakozik az Azure-hoz, hozzá kell [rendelnie](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a Hálózati közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz, amely hozzá van rendelve a Permissions (Engedélyek) listában felsorolt megfelelő [műveletekhez.](#permissions)

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. A virtuális hálózatok [Azure Portal](https://portal.azure.com) a virtuális hálózatok megtekintéséhez. Keresse meg és válassza a **Virtuális hálózatok lehetőséget.**

2. Válassza ki annak a virtuális hálózatnak a nevét, amelybe az alhálózatot hozzá szeretné adni.

3. A **Beállítások menüben** válassza az **Alhálózatok**  >  **Alhálózat lehetőséget.**

4. Az **Alhálózat hozzáadása párbeszédpanelen** adja meg a következő beállítások értékeit:

    | Beállítás | Leírás |
    | --- | --- |
    | **Név** | A névnek egyedinek kell lennie a virtuális hálózaton belül. A más Azure-szolgáltatásokkal való maximális kompatibilitás érdekében javasoljuk, hogy a név első karaktereként egy betűt használja. Például a Azure Application Gateway nem helyezhető üzembe olyan alhálózaton, amely számmal kezdődik. |
    | **Címtartomány** | <p>A tartománynak egyedinek kell lennie a virtuális hálózat címtartományán belül. A tartomány nem lehet átfedésben a virtuális hálózaton belüli más alhálózati címtartományokkal. A címtérnek osztály nélküli útválasztási (CIDR) Inter-Domain kell lennie.</p><p>Például a *10.0.0.0/16* címtértel megadott virtuális hálózatokban a *10.0.0.0/22* alhálózati címtér definiálhat. A legkisebb megadható tartomány a */29*, amely nyolc IP-címet biztosít az alhálózat számára. Az Azure lefoglalja az alhálózatok első és utolsó címét a protokollnak való megfelelőség érdekében. Három további cím van fenntartva az Azure-szolgáltatások használatára. Ennek eredményeképpen egy */29* címtartományú alhálózat definiálása három használható IP-címet eredményez az alhálózatban.</p><p>Ha virtuális hálózatot szeretne csatlakoztatni egy VPN-átjáróhoz, létre kell hoznia egy átjáró-alhálózatot. További információ az [átjáró-alhálózatok címtartományokkal kapcsolatos szempontjairól.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) A címtartományt az alhálózat hozzáadása után, meghatározott feltételek mellett módosíthatja. Az alhálózat címtartományának módosításáról további információt az alhálózati [beállítások módosításáról olvashat.](#change-subnet-settings)</p> |
    | **Hálózati biztonsági csoport** | Az alhálózat bejövő és kimenő hálózati forgalmának szűréséhez hozzárendelhet egy meglévő hálózati biztonsági csoportot egy alhálózathoz. A hálózati biztonsági csoportnak ugyanabban az előfizetésben és helyen kell lennie, mint a virtuális hálózatnak. További információ a [hálózati biztonsági csoportokról](./network-security-groups-overview.md) és [a hálózati biztonsági csoportok létrehozásáról.](tutorial-filter-network-traffic.md) |
    | **Útvonaltábla** | A hálózati forgalom más hálózatokra való átirányításának szabályozásához igény szerint hozzárendelhet egy meglévő útválasztási táblázatot egy alhálózathoz. Az útvonaltáblának ugyanabban az előfizetésben és helyen kell lennie, mint a virtuális hálózatnak. További információ az [Azure-útválasztásról](virtual-networks-udr-overview.md) [és egy útvonaltábla létrehozásáról.](tutorial-create-route-table-portal.md) |
    | **Szolgáltatásvégpontok** | <p>Az alhálózatok opcionálisan egy vagy több szolgáltatásvégpontot is engedélyezhetnek. Ha engedélyezni szeretne egy szolgáltatásvégpontot egy szolgáltatáshoz, válassza ki azt a szolgáltatást vagy szolgáltatásokat, amelyek számára engedélyezni szeretné a **szolgáltatásvégpontokat** a Szolgáltatások listából. Az Azure automatikusan konfigurálja a végpont helyét. Alapértelmezés szerint az Azure konfigurálja a szolgáltatásvégpontokat a virtuális hálózat régiójához. A regionális feladatátvételi forgatókönyvek támogatása érdekében az Azure automatikusan konfigurálja a végpontokat az [Azure Storage-hoz párosított](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) Azure-régiókhoz.</p><p>Szolgáltatásvégpont eltávolításához törölje annak a szolgáltatásnak a kijelölését, amelyről el szeretné távolítani a szolgáltatásvégpontot. További információ a szolgáltatásvégpontokkal és azokkal a szolgáltatásokkal kapcsolatban, amelyekhez engedélyezhetők: [Virtuális hálózati szolgáltatásvégpont.](virtual-network-service-endpoints-overview.md) Miután engedélyezi a szolgáltatásvégpontot egy szolgáltatáshoz, a szolgáltatással létrehozott erőforrás alhálózatához is engedélyeznie kell a hálózati hozzáférést. Ha például engedélyezi a **Microsoft.Storage** szolgáltatásvégpontját, akkor az összes olyan Azure Storage-fiók hálózati hozzáférését is engedélyeznie kell, amelyek számára hálózati hozzáférést kíván engedélyezni. A szolgáltatásvégpont által engedélyezett alhálózatok hálózati hozzáférésének engedélyezéséhez tekintse meg annak az egyes szolgáltatásnak a dokumentációját, amely számára engedélyezte a szolgáltatásvégpontot.</p><p>Annak ellenőrzéshez, hogy a szolgáltatásvégpont engedélyezve [](diagnose-network-routing-problem.md) van-e egy alhálózaton, tekintse meg az alhálózat bármely hálózati adapterének érvényes útvonalait. A végpont konfigurálásakor egy alapértelmezett útvonal látható *a* szolgáltatás címelőtagjaival, és a következő ugrás típusa **VirtualNetworkServiceEndpoint** lesz. További információ az útválasztásról: [Virtuális hálózati forgalom útválasztása.](virtual-networks-udr-overview.md)</p> |
    | **Alhálózat delegálása** | Az alhálózatok igény szerint egy vagy több delegálást is engedélyezhetnek. Az alhálózat-delegálás explicit engedélyeket ad a szolgáltatásnak arra, hogy szolgáltatásspecifikus erőforrásokat hozzon létre az alhálózatban egy egyedi azonosító használatával a szolgáltatás üzembe helyezése során. Egy szolgáltatáshoz való delegáláshoz válassza ki azt a szolgáltatást, amely számára delegálni szeretne a **Szolgáltatások listából.** |

5. Ha hozzá szeretne adni egy alhálózatot a kiválasztott virtuális hálózathoz, kattintson az **OK gombra.**

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alhálózat beállításainak módosítása

1. A virtuális hálózatok [Azure Portal](https://portal.azure.com) a virtuális hálózatok megtekintéséhez. Keresse meg és válassza a **Virtuális hálózatok lehetőséget.**

2. Válassza ki a módosítani kívánt alhálózatot tartalmazó virtuális hálózat nevét.

3. A **Beállítások menüben** válassza az **Alhálózatok lehetőséget.**

4. Az alhálózatok listájában válassza ki azt az alhálózatot, amely beállításait módosítani szeretné.

5. Az alhálózat lapon módosítsa a következő beállítások bármelyikét:

    | Beállítás | Leírás |
    | --- | --- |
    | **Címtartomány** | Ha nem helyez üzembe erőforrásokat az alhálózaton belül, módosíthatja a címtartományt. Ha vannak erőforrások az alhálózaton, akkor vagy másik alhálózatra kell áthelyeznie az erőforrásokat, vagy először törölnie kell azokat az alhálózatból. Az erőforrások áthelyezésének vagy törlésének lépései az erőforrástól függően változnak. Az alhálózatok erőforrásainak áthelyezésével vagy törlésével kapcsolatban olvassa el az egyes erőforrástípusok dokumentációját. Az Alhálózat **hozzáadása** 4. lépésében tekintse meg [a címtartományra vonatkozó korlátozásokat.](#add-a-subnet) |
    | **Felhasználók** | Az alhálózathoz való hozzáférést beépített szerepkörökkel vagy saját egyéni szerepkörökkel szabályozhatja. További információ a szerepkörök és felhasználók alhálózathoz való hozzárendelésről: [Azure-szerepkörök hozzárendelése.](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
    | **Hálózati biztonsági csoport** és **Útválasztási táblázat** | Lásd az Alhálózat hozzáadása [4. lépését.](#add-a-subnet) |
    | **Szolgáltatásvégpontok** | <p>Lásd az Alhálózat hozzáadása [4. lépésében található szolgáltatásvégpontokat.](#add-a-subnet) Amikor egy meglévő alhálózathoz engedélyez egy szolgáltatásvégpontot, győződjön meg arról, hogy nem futnak kritikus feladatok az alhálózat bármely erőforrásán. A szolgáltatásvégpont az alhálózat minden hálózati adapterének útvonalait átváltja. A szolgáltatásvégpontok a *0.0.0.0/0* címelőtaggal és a következő ugrási típusú internettel az alapértelmezett útvonalról egy új útvonalra mutatnak a szolgáltatás címelőtagjaival, és a következő ugrás típusa *virtualNetworkServiceEndpoint* lesz.</p><p>A kapcsoló során a nyitott TCP-kapcsolatok megszakadhatnak. A szolgáltatásvégpont nincs engedélyezve, amíg az összes hálózati adapterhez a szolgáltatás felé áramlik a forgalom az új útvonalra való frissítésig. További információ az útválasztásról: [Virtuális hálózati forgalom útválasztása.](virtual-networks-udr-overview.md)</p> |
    | **Alhálózat delegálása** | Lásd az Alhálózat hozzáadása [4. lépésében található szolgáltatásvégpontokat.](#add-a-subnet) Az alhálózat-delegálás nullára módosítható, vagy több delegálás is engedélyezhető hozzá. Ha egy szolgáltatás erőforrása már üzembe van adva az alhálózaton, az alhálózat delegálását csak akkor lehet hozzáadni vagy eltávolítani, ha a szolgáltatás összes erőforrását el nem távolítják. Ha egy másik szolgáltatás számára szeretne delegálni, válassza ki a szolgáltatások listájából azt a szolgáltatást, amely számára **delegálni** szeretne. |

6. Kattintson a **Mentés** gombra.

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Alhálózat törlése

Alhálózatot csak akkor törölhet, ha nincsenek erőforrások az alhálózaton. Ha az erőforrások az alhálózatban vannak, az alhálózat törlése előtt törölnie kell ezeket az erőforrásokat. Az erőforrások törlésének lépései az erőforrástól függően változnak. Az alhálózatok erőforrásainak törléséről az egyes erőforrástípusok dokumentációjában olvashat.

1. A virtuális hálózatok [Azure Portal](https://portal.azure.com) a virtuális hálózatok megtekintéséhez. Keresse meg és válassza a **Virtuális hálózatok lehetőséget.**

2. Válassza ki a törölni kívánt alhálózatot tartalmazó virtuális hálózat nevét.

3. A **Beállítások menüben** válassza az **Alhálózatok lehetőséget.**

4. Az alhálózatok listájában válassza ki a törölni kívánt alhálózatot.

5. Válassza **a Törlés** lehetőséget, majd a megerősítési párbeszédpanelen válassza az Igen lehetőséget. 

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Engedélyek

Az alhálózatok feladatainak végrehajtásához [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) fiókját a Hálózati közreműködő szerepkörhöz vagy egy egyéni szerepkörhöz kell hozzárendelni, amely a következő táblázatban a megfelelő műveleteket rendeli hozzá: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

|Művelet                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Virtuális hálózat alhálózatának olvasása              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Virtuális hálózati alhálózat létrehozása vagy frissítése  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Virtuális hálózat alhálózatának törlése            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Csatlakozás virtuális hálózathoz                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Szolgáltatásvégpont engedélyezése alhálózathoz     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Alhálózat virtuális gépének lekért száma       |

## <a name="next-steps"></a>Következő lépések

- Virtuális hálózat és alhálózatok létrehozása [PowerShell-](powershell-samples.md) vagy [Azure CLI-mintaszkprogramokkal](cli-samples.md) vagy Azure [Resource Manager sablonokkal](template-samples.md)
- Virtuális [hálózatokhoz Azure Policy és hozzárendelése](./policy-reference.md)