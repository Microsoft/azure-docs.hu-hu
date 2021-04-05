---
title: Kapcsolatok – Azure Portal
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogyan használhatja az Azure Network Watcher kapcsolódási hibáit a Azure Portal használatával.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898113"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Az Azure Network Watcher-kapcsolatok hibáinak megoldása a Azure Portal használatával

> [!div class="op_single_selector"]
> - [Portál](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Megtudhatja, hogyan hozhatja meg a kapcsolódási hibák megoldását annak ellenőrzéséhez, hogy egy virtuális gépről egy adott végpontra irányuló közvetlen TCP-kapcsolódás létesíthető-e.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik a következő erőforrásokkal:

* Network Watcher egy példánya abban a régióban, ahol a kapcsolódást szeretné elhárítani.
* Virtuális gépek a szolgáltatással való kapcsolódási hibák megoldásához.

> [!IMPORTANT]
> A kapcsolati hibákhoz az szükséges, hogy a rendszer a virtuálisgép- `AzureNetworkWatcherExtension` bővítményt telepítse. A bővítmény Windows rendszerű virtuális gépen való telepítéséhez látogasson el az [azure Network Watcher Agent virtuálisgép-bővítmény a Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) rendszerhez és a Linux rendszerű virtuális gépekhez látogasson el az [Azure Network Watcher Agent virtuálisgép-bővítménye Linuxra](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A cél végponton nem szükséges a kiterjesztés.

## <a name="check-connectivity-to-a-virtual-machine"></a>Virtuális géphez való csatlakozás ellenőrzése

Ez a példa az 80-es porton keresztül ellenőrzi a cél virtuális géphez való kapcsolódást.

Navigáljon a Network Watcher, és kattintson a **kapcsolódási hibák** lehetőségre. Válassza ki azt a virtuális gépet, amelyről meg szeretné nézni a kapcsolatot. A **cél** szakaszban válassza a **virtuális gép kiválasztása** lehetőséget, majd válassza ki a megfelelő virtuális gépet és portot a teszteléshez.

Ha a **Check (ellenőrzés**) gombra kattint, a megadott porton lévő virtuális gépek közötti kapcsolat be van jelölve. A példában a cél virtuális gép nem érhető el, az ugrások listája látható.

![Virtuális gép csatlakozási eredményeinek ellenőrzése][1]

## <a name="check-remote-endpoint-connectivity"></a>A távoli privát végpont csatlakozásának ellenőrzése

Ha szeretné megnézni a kapcsolatot és a késést egy távoli végponton, akkor a **cél** szakaszban válassza a **manuális megadása** választógombot, adja meg az URL-címet és a portot, majd kattintson az **ellenőrzése** gombra.  Ez a távoli végpontok, például a webhelyek és a tárolási végpontok esetében használatos.

![Webhely csatlakozási eredményeinek ellenőrzése][2]

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan automatizálható a csomagok rögzítése a virtuálisgép-riasztásokkal a [riasztások által aktivált csomagok létrehozása](network-watcher-alert-triggered-packet-capture.md) funkció megtekintésével

Annak megállapítása, hogy az [IP-forgalom ellenőrzésének](diagnose-vm-network-traffic-filtering-problem.md) meglátogatása esetén engedélyezett-e a virtuális gép bejövő vagy kimenő forgalma

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png