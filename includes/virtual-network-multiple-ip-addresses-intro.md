---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 0c3bc3f2995131c7777bfc48269a17fceda33192
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742267"
---
> [!div class="op_single_selector"]
> * [Azure Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

Egy Azure virtuális géphez (VM) egy vagy több hálózati adapter (NIC) van csatlakoztatva. Bármelyik hálózati adapter rendelkezhet egy vagy több hozzárendelt statikus vagy dinamikus nyilvános vagy magánhálózati IP-címmel. Több IP-cím hozzárendelése egy virtuális géphez a következő képességeket teszi elérhetővé:

* Több webhely vagy szolgáltatás üzemeltetése különböző IP-címekkel és SSL-tanúsítványokkal egyetlen kiszolgálón.
* Használat hálózati virtuális készülékként, például tűzfalként vagy terheléselosztóként.
* Bármely hálózati adapter bármely magánhálózati IP-címének hozzáadása egy Azure Load Balancer háttérkészlethez. Korábban csak az elsődleges hálózati adapter elsődleges IP-címét lehetett hozzáadni a háttérkészlethez. Ha többet szeretne megtudni több IP-konfiguráció terheléselosztásáról, olvassa el a [több IP-konfiguráció terheléselosztásával kapcsolatos](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikket.

A virtuális géphez csatolt minden hálózati adapter egy vagy több hozzárendelt IP-konfigurációval rendelkezik. Az egyes konfigurációkhoz egy statikus vagy dinamikus magánhálózati IP-cím van hozzárendelve. Az egyes konfigurációkhoz egy nyilvános IP-cím erőforrás is hozzárendelhető. Egy nyilvános IP-cím erőforráshoz egy dinamikus vagy statikus nyilvános IP-cím van hozzárendelve. Ha többet szeretne megtudni az Azure-ban használt IP-címekről, olvassa el az [IP-címek az Azure-ban](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) című cikket. 

Van korlát hány magánhálózati IP-címek rendelhetők egy hálózati adapterhez. Azure-előfizetésben használható hány nyilvános IP-címek korlátozva is van. A részletekért tekintse meg az [Azure korlátairól](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) szóló cikket.
