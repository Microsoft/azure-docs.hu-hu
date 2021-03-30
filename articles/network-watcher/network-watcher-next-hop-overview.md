---
title: Bevezetés az Azure Network Watcher következő ugrására | Microsoft Docs
description: Ez a cikk áttekintést nyújt a Network Watcher következő ugrási képességéről.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76844058"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>A következő ugrás használata a virtuális gépek útválasztási problémáinak diagnosztizálásához

A virtuális gépről (VM) érkező forgalmat a rendszer a hálózati adapterhez (NIC) társított érvényes útvonalak alapján küldi el a célhelyre. A következő ugrás egy adott virtuális gépről és hálózati adapterről származó csomag következő ugrási típusát és IP-címét kérdezi le. A következő ugrás ismerete segít eldönteni, hogy a forgalom a kívánt célra van-e irányítva, vagy hogy a forgalom hol érkezik. Az útvonalak nem megfelelő konfigurációja, ahol a forgalom egy helyszíni helyre vagy egy virtuális készülékre irányul, kapcsolódási problémákhoz vezethet. A következő ugrás a következő ugráshoz társított útválasztási táblázatot is visszaadja. Ha az útvonal felhasználó által definiált útvonalként van definiálva, akkor az adott útvonalat adja vissza. Ellenkező esetben a következő ugrás a **rendszer útvonalát** adja vissza.

![következő ugrás – áttekintés](./media/network-watcher-next-hop-overview/figure1.png)

A következő ugrások által visszaadott következő ugrások a következők:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Nincsenek

Az egyes következő ugrási típusokkal kapcsolatos további tudnivalókért lásd: az [Útválasztás áttekintése](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Következő lépések

A virtuálisgép-hálózat útválasztási problémáinak diagnosztizálásához használja a következő ugrást: a virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása a [Azure Portal](diagnose-vm-network-routing-problem.md), a [PowerShell](diagnose-vm-network-routing-problem-powershell.md)vagy az [Azure CLI](diagnose-vm-network-routing-problem-cli.md)használatával.
