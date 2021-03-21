---
title: Hálózati adapterek hozzáadása vagy eltávolítása az Azure-beli virtuális gépekről
description: Ismerje meg, hogyan adhat hozzá hálózati adaptereket a virtuális gépek hálózati adapterei számára, illetve távolíthatja el azokat.
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
ms.openlocfilehash: 43d3da49688a7daeb3ea4e0c1c3dba505dcd3b59
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508333"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Hálózati adapterek hozzáadása vagy eltávolítása virtuális gépekről

Megtudhatja, hogyan adhat hozzá meglévő hálózati adaptert egy Azure-beli virtuális gép (VM) létrehozásakor. Azt is megtudhatja, hogyan adhat hozzá vagy távolíthat el hálózati adaptereket egy meglévő virtuális gépről a leállított (lefoglalt) állapotból. A hálózati adapterek lehetővé teszik, hogy egy Azure-beli virtuális gép kommunikáljon az internettel, az Azure-ral és a helyszíni erőforrásokkal. A virtuális gépek egy vagy több hálózati adapterrel rendelkeznek. 

Ha IP-címeket kell hozzáadnia, módosítania vagy eltávolítania egy hálózati adapterhez, tekintse meg a [hálózati adapter IP-címeinek kezelése](virtual-network-network-interface-addresses.md)című témakört. Hálózati adapterek létrehozásához, módosításához vagy törléséhez tekintse meg a [hálózati adapterek kezelése](virtual-network-network-interface.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik ilyennel, állítson be egy aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). A cikk hátralévő részének megkezdése előtt végezze el az alábbi feladatok egyikét:

- **Portál felhasználói**: Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók**: futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. A Azure Cloud Shell böngésző lapon keresse meg a **környezet kiválasztása** legördülő listát, majd válassza a **PowerShell** lehetőséget, ha még nincs kiválasztva.

    Ha helyileg futtatja a PowerShellt, használja a Azure PowerShell modul 1.0.0 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

- **Azure parancssori felület (CLI) felhasználói**: futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy futtassa a CLI-t a számítógépről. Ha helyileg futtatja az Azure CLI-t, használja az Azure CLI-2.0.26 vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Futtassa a `az login` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Meglévő hálózati adapterek hozzáadása egy új virtuális géphez

Ha a portálon keresztül hoz létre virtuális gépet, a portál létrehoz egy alapértelmezett beállításokkal rendelkező hálózati adaptert, és csatlakoztatja a hálózati adaptert a virtuális géphez. A portál használatával meglévő hálózati adaptereket adhat hozzá egy új virtuális géphez, vagy létrehozhat egy több hálózati adapterrel rendelkező virtuális gépet. Mindkettőt a CLI vagy a PowerShell használatával is elvégezheti. Ügyeljen arra, hogy megismerkedjen a [korlátozásokkal](#constraints). Ha több hálózati adapterrel rendelkező virtuális gépet hoz létre, akkor a virtuális gép létrehozása után is megfelelően kell konfigurálnia az operációs rendszert. Megtudhatja, hogyan konfigurálhat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows rendszert](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapterhez.

### <a name="commands"></a>Parancsok

A virtuális gép létrehozása előtt [hozzon létre egy hálózati adaptert](virtual-network-network-interface.md#create-a-network-interface).

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Hálózati adapter hozzáadása meglévő virtuális géphez

Hálózati adapter hozzáadása a virtuális géphez:

1. Egy meglévő virtuális gép kereséséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. Válassza ki a virtuális gép nevét. A virtuális gépnek támogatnia kell a felvenni kívánt hálózati adapterek számát. Annak megállapításához, hogy az egyes virtuálisgép-méretek hány hálózati adaptert támogatnak, tekintse meg a Linux rendszerű [virtuális gépek](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Windows rendszerű virtuális gépek](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)méretét

3. A virtuális gép parancssáv mezőjében válassza a **Leállítás**, majd az **OK gombot** a megerősítő párbeszédpanelen. Ezután várjon, amíg a virtuális gép **állapota** leállítva értékre nem változik (fel van **foglalva)**.

4. A virtuális gép menüsorában **válassza a**  >  **hálózati adapter csatlakoztatása** lehetőséget. Ezután a **meglévő hálózati adapter csatolása** elemnél válassza ki a csatolni kívánt hálózati adaptert, majd kattintson **az OK gombra**.

    >[!NOTE]
    >A kiválasztott hálózati adapter nem rendelkezhet a gyorsított hálózatkezelés engedélyezésével, nem rendelkezhet hozzá IPv6-címekkel, és ugyanabban a virtuális hálózatban kell lennie, mint a virtuális GÉPHEZ aktuálisan csatlakoztatott hálózati adapternek.

    Ha nem rendelkezik meglévő hálózati adapterrel, először létre kell hoznia egyet. Ehhez válassza a **hálózati adapter létrehozása** lehetőséget. A hálózati adapterek létrehozásával kapcsolatos további tudnivalókért tekintse meg [a hálózati adapterek](virtual-network-network-interface.md#create-a-network-interface)létrehozásával foglalkozó témakört. Ha többet szeretne megtudni a hálózati adapterek virtuális gépekhez adásával kapcsolatos további korlátozásokról, lásd: [megkötések](#constraints).

5. A virtuális gép menüsávján válassza az **Áttekintés**  >  **indítása** lehetőséget a virtuális gép újraindításához.

Most már beállíthatja a virtuális gép operációs rendszerét, hogy megfelelően több hálózati adaptert használjon. Megtudhatja, hogyan konfigurálhat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows rendszert](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapterhez.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (Reference); [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás); [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Virtuális gép hálózati adaptereinek megtekintése

Megtekintheti a virtuális géphez csatlakozó hálózati adaptereket, és megismerheti az egyes hálózati adapterek konfigurációját, valamint az egyes hálózati adapterekhez hozzárendelt IP-címeket. 

1. Egy meglévő virtuális gép kereséséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

    >[!NOTE]
    >Jelentkezzen be egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózati közreműködő szerepkörhöz az előfizetéséhez. Ha többet szeretne megtudni a szerepkörök a fiókokhoz való hozzárendeléséről, tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)című témakört.

2. Válassza ki annak a virtuális gépnek a nevét, amelynek a csatlakoztatott hálózati adaptereit meg szeretné tekinteni.

3. A virtuális gép menüsorában válassza a **hálózatkezelés** lehetőséget.

A hálózati adapterek beállításairól és azok módosításáról a [hálózati adapterek kezelése](virtual-network-network-interface.md)című témakörben olvashat bővebben. A hálózati adapterhez hozzárendelt IP-címek hozzáadásával, módosításával vagy eltávolításával kapcsolatos további információkért lásd: a [hálózati adapter IP-címeinek kezelése](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az VM NIC List](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Hálózati adapter eltávolítása egy virtuális gépről

1. Egy meglévő virtuális gép kereséséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. Válassza ki annak a virtuális gépnek a nevét, amelynek a csatlakoztatott hálózati adaptereit meg szeretné tekinteni.

3. A virtuális gép eszköztárán válassza a **Leállítás gombot**.

4. Várjon, amíg a virtuális gép **állapota** leállítva értékre nem változik (fel van **foglalva)**.

5. A virtuális gép menüsorában **válassza a**  >  **hálózat leválasztása hálózati adapter** lehetőséget.

6. A **hálózati adapter leválasztása** párbeszédpanelen válassza ki a leválasztani kívánt hálózati adaptert. Ez után válassza az **OK** gombot.

    >[!NOTE]
    >Ha csak egy hálózati adapter van felsorolva, akkor nem lehet leválasztani, mert a virtuális gépnek mindig legalább egy hálózati adapterrel kell rendelkeznie.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (Reference); [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás); [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Korlátozások

- A virtuális gépekhez legalább egy hálózati adapternek hozzá kell tartoznia.

- A virtuális gépek csak annyi hálózati csatolóval rendelkezhetnek, amennyit a virtuálisgép-méret is támogat. Ha többet szeretne megtudni arról, hogy az egyes virtuálisgép-méretek hány hálózati adaptert támogatnak, tekintse meg a Linux rendszerű [virtuális gépek](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Windows rendszerű virtuális gépek](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)méreteit Minden méret legalább két hálózati adaptert támogat.

- A virtuális géphez hozzáadott hálózati adapterek jelenleg nem csatlakoztathatók egy másik virtuális géphez. A hálózati adapterek létrehozásával kapcsolatos további tudnivalókért tekintse meg [a hálózati adapterek](virtual-network-network-interface.md#create-a-network-interface)létrehozásával foglalkozó témakört.

- A múltban csak a több hálózati adaptert támogató virtuális gépekhez adhat hozzá hálózati adaptereket, és legalább két hálózati adapterrel lett létrehozva. Nem adhat hozzá olyan hálózati adaptert egy virtuális géphez, amely egyetlen hálózati adapterrel lett létrehozva, még akkor is, ha a virtuális gép mérete több hálózati adaptert is támogat. Ezzel szemben a hálózati adapterek csak legalább három hálózati adapterrel távolíthatók el, mert legalább két hálózati adapterrel létrehozott virtuális gépeknek mindig legalább két hálózati adapterrel kell rendelkezniük. Ezek a korlátozások már nem érvényesek. Most már létrehozhat egy tetszőleges számú hálózati adapterrel rendelkező virtuális gépet (a virtuális gép mérete által támogatott számmal).

- Alapértelmezés szerint a virtuális géphez csatolt első hálózati adapter az *elsődleges* hálózati adapter. A virtuális gép összes többi hálózati adaptere *másodlagos* hálózati adapter.

- Megadhatja, hogy melyik hálózati adapterre küldje a kimenő forgalmat. Azonban a virtuális gép alapértelmezés szerint az összes kimenő forgalmat az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt IP-címhez küldi.

- A múltban az azonos rendelkezésre állási csoportba tartozó összes virtuális gépnek egyetlen vagy több hálózati adapterrel kell rendelkeznie. A tetszőleges számú hálózati adapterrel rendelkező virtuális gépek már ugyanabban a rendelkezésre állási készletben létezhetnek, a virtuális gép mérete által támogatott számmal. A létrehozáskor csak egy virtuális gépet adhat hozzá rendelkezésre állási készlethez. További információ a rendelkezésre állási csoportokról: [virtuális gépek rendelkezésre állásának kezelése az Azure-ban](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

- Az azonos virtuális GÉPEN lévő hálózati adaptereket a virtuális hálózatban lévő különböző alhálózatokhoz is összekapcsolhatjuk. A hálózati adaptereknek azonban mind ugyanahhoz a virtuális hálózathoz kell csatlakozniuk.

- Bármely elsődleges vagy másodlagos hálózati adapter IP-konfigurációjához bármilyen IP-címet hozzáadhat egy Azure Load Balancer háttér-készlethez. A múltban csak az elsődleges hálózati adapter elsődleges IP-címe adható hozzá a háttér-készlethez. További információ az IP-címekről és a konfigurációkról: [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md).

- A virtuális gép törlése nem törli a hozzá csatolt hálózati adaptereket. Ha töröl egy virtuális gépet, a hálózati adapterek le lesznek választva a virtuális gépről. Ezeket a hálózati adaptereket különböző virtuális gépekhez is hozzáadhatja, vagy törölheti őket.

- Az optimális teljesítmény dokumentációjának megvalósításához gyorsított hálózatkezelésre van szükség. Bizonyos esetekben explicit módon engedélyeznie kell a gyorsított hálózatkezelést a Windows vagy [Linux](create-vm-accelerated-networking-cli.md) [rendszerű](create-vm-accelerated-networking-powershell.md) virtuális gépekhez.

## <a name="next-steps"></a>Következő lépések

Több hálózati adapterrel vagy IP-címmel rendelkező virtuális gép létrehozásához tekintse meg a következőt:

|Feladat|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása több IPv4-címmel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása magánhálózati IPv6-cím (Azure Load Balancer mögött)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|