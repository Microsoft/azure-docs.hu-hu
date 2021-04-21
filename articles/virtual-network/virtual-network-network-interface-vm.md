---
title: Hálózati adapterek hozzáadása Azure-beli virtuális gépekhez vagy eltávolításuk onnan
description: Ismerje meg, hogyan adhat hozzá hálózati adaptereket a virtuális gépekhez, és hogyan távolíthatja el azokat onnan.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 847f8dbd2d8f4064f12333348a4f03e5c5fcc611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774268"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Hálózati adapterek hozzáadása vagy eltávolítása virtuális gépekről

Megtudhatja, hogyan adhat hozzá meglévő hálózati adaptert Azure-beli virtuális gép (VM) létrehozásakor. Emellett megtudhatja, hogyan adhat hozzá vagy távolíthat el hálózati adaptereket egy leállított (felszabadított) állapotban lévő meglévő virtuális gépről. A hálózati adapter lehetővé teszi, hogy az Azure-beli virtuális gép kommunikáljon az internettel, az Azure-ral és a helyszíni erőforrásokkal. Egy virtuális gép egy vagy több hálózati adaptert használ. 

Ha ip-címeket kell hozzáadnia, módosítania vagy eltávolítania egy hálózati adapterhez, tekintse meg a Hálózati adapter IP-címeinek [kezelésecímek kezelését.](virtual-network-network-interface-addresses.md) A hálózati adapterek létrehozásáról, módosításról vagy törléséről lásd: [Hálózati adapterek kezelése.](virtual-network-network-interface.md)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik előfizetéssel, állítson be egy aktív előfizetéssel egy Azure-fiókot. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) A cikk további részében az alábbi feladatok elvégzése előtt kell elvégeznie az alábbi feladatokat:

- **Portálfelhasználók:** Jelentkezzen [](https://portal.azure.com) be Azure Portal azure-fiókjával.

- **PowerShell-felhasználók:** Futtassa a parancsokat a Azure Cloud Shell, [vagy](https://shell.azure.com/powershell)futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell lapon keresse meg a  Környezet kiválasztása legördülő listát, majd válassza a **PowerShell** lehetőséget, ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

- **Azure parancssori felület (CLI)** felhasználói: Futtassa [](https://shell.azure.com/bash)a Azure Cloud Shell parancsokat, vagy futtassa a parancssori felületet a számítógépről. Ha helyileg futtatja az Azure CLI-t, használja az Azure CLI 2.0.26-os vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Futtassa a `az login` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Meglévő hálózati adapterek hozzáadása új virtuális géphez

Amikor a portálon keresztül hoz létre egy virtuális gépet, a portál létrehoz egy hálózati adaptert az alapértelmezett beállításokkal, és csatolja a hálózati adaptert a virtuális géphez. A portál használatával nem adhat hozzá meglévő hálózati adaptereket egy új virtuális géphez, és nem hozhat létre több hálózati adaptert is. Mindkettőt a parancssori felület vagy a PowerShell használatával is használhatja. Mindenképpen ismerkedjen meg a [korlátozásokkal.](#constraints) Ha több hálózati adapterrel rendelkező virtuális gépet hoz létre, a virtuális gép létrehozása után az operációs rendszert is konfigurálnia kell azok megfelelő használatára. Megtudhatja, hogyan konfigurálhat [Linuxot](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windowst](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapterhez.

### <a name="commands"></a>Parancsok

A virtuális gép létrehozása előtt [hozzon létre egy hálózati adaptert.](virtual-network-network-interface.md#create-a-network-interface)

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Hálózati adapter hozzáadása meglévő virtuális géphez

Hálózati adapter hozzáadása a virtuális géphez:

1. A virtuális gép [Azure Portal](https://portal.azure.com) meg egy meglévő virtuális gépet. Keresse meg és válassza a **Virtuális gépek lehetőséget.**

2. Válassza ki a virtuális gép nevét. A virtuális gépnek támogatnia kell a hozzáadni kívánt hálózati adapterek számát. Az egyes virtuálisgép-méretek által támogatott hálózati adapterek a [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy Windows rendszerű virtuális gépek azure-beli méretében találják meg a [méretet.](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

3. A virtuális gép parancssávjában válassza a **Leállítás,** majd az **OK** lehetőséget a megerősítő párbeszédpanelen. Ezután várja **meg, amíg a virtuális** gép Állapota **Leállítva (felszabadítva) állapotra változik.**

4. A virtuális gép menüsávjában válassza a **Hálózat csatolja**  >  **a hálózati adaptert lehetőséget.** Ezután a **Meglévő hálózati adapter csatolása részen** válassza ki a csatolni kívánt hálózati adaptert, majd kattintson az **OK gombra.**

    >[!NOTE]
    >A kiválasztott hálózati adapteren nem engedélyezhető a gyorsított hálózat, nem lehet hozzá IPv6-cím rendelve, és ugyanazon a virtuális hálózaton kell lennie, és a virtuális géphez jelenleg csatlakoztatott hálózati adapternek kell lennie.

    Ha még nincs hálózati adaptere, először létre kell hoznia egyet. Válassza a Hálózati adapter **létrehozása lehetőséget.** További információ a hálózati adapterek létrehozásáról: [Hálózati adapter létrehozása.](virtual-network-network-interface.md#create-a-network-interface) További információ a hálózati adapterek virtuális gépekhez való hozzáadásakor érvényben lévő további korlátozásokról: [Megkötések.](#constraints)

5. A virtuális gép újraindításához a virtuális gép **menüsávjában** válassza az Áttekintés  >  **Indítás** lehetőséget.

Most már konfigurálhatja a virtuális gép operációs rendszerét több hálózati adapter megfelelő használatára. Megtudhatja, hogyan konfigurálhat [Linuxot](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windowst](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapterhez.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (referencia); [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás); [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Virtuális gép hálózati adaptereinek megtekintése

A virtuális gépekhez aktuálisan csatlakoztatott hálózati adapterek megtekintésével megismerheti az egyes hálózati adapterek konfigurációját és az egyes hálózati adapterek ip-címeit. 

1. A virtuális [Azure Portal](https://portal.azure.com) megkeresi a virtuális gépet. Keresse meg és válassza a **Virtuális gépek lehetőséget.**

    >[!NOTE]
    >Jelentkezzen be egy olyan fiókkal, amely az előfizetés tulajdonosi, közreműködői vagy hálózati közreműködői szerepkörével rendelkezik. A szerepkörök fiókokhoz való hozzárendelésének további tudnivalókért lásd: Az [Azure szerepköralapú hozzáférés-vezérlésének beépített szerepkörei.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

2. Válassza ki annak a virtuális gépnek a nevét, amelyhez meg szeretné tekinteni a csatlakoztatott hálózati adaptereket.

3. A virtuális gép menüsávjában válassza a **Hálózat lehetőséget.**

A hálózati adapterek beállításaival és azok módosításával kapcsolatos további információkért lásd: [Hálózati adapterek kezelése.](virtual-network-network-interface.md) A hálózati adapterhez rendelt IP-címek hozzáadásáról, módosításról vagy eltávolításáról lásd: Hálózati adapter [IP-címeinek kezelése.](virtual-network-network-interface-addresses.md)

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Hálózati adapter eltávolítása egy virtuális gépről

1. A virtuális [Azure Portal](https://portal.azure.com) megkeresi a virtuális gépet. Keresse meg és válassza a **Virtuális gépek lehetőséget.**

2. Válassza ki annak a virtuális gépnek a nevét, amelynek meg szeretné tekinteni a csatlakoztatott hálózati adaptereket.

3. A virtuális gép eszköztárán válassza a **Leállítás lehetőséget.**

4. **Várjon, amíg a virtuális** gép Állapota **Leállítva (felszabadítva) állapotra változik.**

5. A virtuális gép menüsávjában válassza a **Hálózat**  >  **leválasztása hálózati adapter lehetőséget.**

6. A **Hálózati adapter leválasztása** párbeszédpanelen válassza ki a leválasztani kívánt hálózati adaptert. Ez után válassza az **OK** gombot.

    >[!NOTE]
    >Ha csak egy hálózati adapter szerepel a listában, nem tudja leválasztani, mert a virtuális gépeknek mindig legalább egy hálózati adaptert kell csatolniuk hozzá.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (hivatkozás); [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referencia); [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Korlátozások

- A virtuális gépnek legalább egy hálózati adaptert hozzá kell csatolnia.

- Egy virtuális géphez csak annyi hálózati adapter csatolható, amennyit a virtuális gép mérete támogat. Ha többet szeretne megtudni arról, hogy az egyes virtuálisgép-méretek hány hálózati adaptert támogatnak, tekintse meg a [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)rendszerű virtuális gépek azure-beli méretét. Minden méret legalább két hálózati adaptert támogat.

- A virtuális géphez adott hálózati adapterek jelenleg nem csatolhatóak másik virtuális géphez. További információ a hálózati adapterek létrehozásáról: [Hálózati adapter létrehozása.](virtual-network-network-interface.md#create-a-network-interface)

- Korábban csak olyan virtuális gépekhez lehetett hálózati adaptereket hozzáadni, amelyek több hálózati adaptert is támogattak, és legalább két hálózati adaptert hoztak létre. Nem tudott hálózati adaptert hozzáadni egy olyan virtuális géphez, amely egyetlen hálózati adapteren lett létrehozva, még akkor sem, ha a virtuális gép mérete egynél több hálózati adaptert támogatott. Ezzel szemben csak olyan virtuális gépről távolíthat el hálózati adaptert, amely legalább három hálózati adaptert használ, mert a legalább két hálózati adaptersel létrehozott virtuális gépeknek mindig legalább két hálózati adapterre volt szükség. Ezek a korlátozások már nem érvényesek. Most már bármilyen számú hálózati adaptert (a virtuális gép mérete által támogatott számban) is létrehozhat.

- Alapértelmezés szerint a virtuális géphez elsőként csatlakoztatott hálózati adapter az *elsődleges* hálózati adapter. A virtuális gép összes többi hálózati adaptere *másodlagos* hálózati adapter.

- Szabályozhatja, hogy melyik hálózati adapterre küld kimenő forgalmat. A virtuális gépek azonban alapértelmezés szerint az elsődleges hálózati adapter elsődleges IP-konfigurációjának IP-címére küldik az összes kimenő forgalmat.

- Korábban az egy rendelkezésre állási készletben található összes virtuális gépnek egyetlen vagy több hálózati adapterre volt szüksége. A bármely számú hálózati adaptert támogató virtuális gépek már létezhetnek ugyanabban a rendelkezésre állási készletben, a virtuális gép mérete által támogatott számig. A virtuális gépeket csak a létrehozásuk után lehet hozzáadni egy rendelkezésre állási készlethez. A rendelkezésre állási készletekkel kapcsolatos további információkért lásd: Virtuális gépek rendelkezésre állásának [kezelése az Azure-ban.](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

- Az ugyanazon a virtuális gépen lévő hálózati adaptereket a virtuális hálózat különböző alhálózataihoz csatlakoztathatja. A hálózati adaptereket azonban ugyanannak a virtuális hálózatnak kell csatlakoztatnia.

- Bármely elsődleges vagy másodlagos hálózati adapter IP-konfigurációjának IP-címét hozzáadhatja Azure Load Balancer háttérkészlethez. Korábban csak az elsődleges hálózati adapter elsődleges IP-címét lehetett hozzáadni a háttérkészlethez. Az IP-címekkel és -konfigurációval kapcsolatos további információkért lásd: [IP-címek hozzáadása, módosítása vagy eltávolítása.](virtual-network-network-interface-addresses.md)

- A virtuális gép törlése nem törli a hozzá csatolt hálózati adaptereket. Amikor töröl egy virtuális gépet, a hálózati adapterek le vannak különülve a virtuális gépről. Ezeket a hálózati adaptereket hozzáadhatja különböző virtuális gépekhez, vagy törölheti őket.

- A dokumentált optimális teljesítmény eléréséhez gyorsított hálózatra van szükség. Bizonyos esetekben explicit módon engedélyeznie kell a gyorsított hálózatépítést [Windows vagy](create-vm-accelerated-networking-powershell.md) [Linux rendszerű](create-vm-accelerated-networking-cli.md) virtuális gépeken.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="next-steps"></a>Következő lépések

Több hálózati adaptersel vagy IP-címmel rendelkező virtuális gép létrehozásához lásd:

|Feladat|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[PARANCSSORI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)FELÜLET, [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása több IPv4-címmel|[PARANCSSORI FELÜLET,](virtual-network-multiple-ip-addresses-cli.md) [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása privát IPv6-címmel (virtuális gép Azure Load Balancer)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell,](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|