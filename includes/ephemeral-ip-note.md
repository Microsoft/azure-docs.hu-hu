---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529589"
---
> [!NOTE]
> Az Azure egy olyan azure-beli Virtual Machines ip-címet biztosít, amelyhez nincs nyilvános IP-cím rendelve, vagy amelyek egy belső Alapszintű ip-cím háttérkészletében Azure Load Balancer. Aphemeral IP-mechanizmus nem konfigurálható kimenő IP-címet biztosít. 
>
>A nyilvános IP-cím le van tiltva, ha egy nyilvános IP-címet rendelnek a virtuális géphez, vagy ha a virtuális gép egy virtuális gép háttérkészletében van standard Load Balancer vagy anélkül. Ha egy [Azure Virtual Network NAT](../articles/virtual-network/nat-overview.md) átjáró-erőforrás hozzá van rendelve a virtuális gép alhálózata számára, a rendszer letiltja aphemeral IP-címet.
>
> Az Azure-beli kimenő kapcsolatokkal kapcsolatos további információkért lásd: [Using Source Network Address Translation (SNAT) for outbound connections (Forráshálózati címfordítás (SNAT) használata kimenő kapcsolatokhoz).](../articles/load-balancer/load-balancer-outbound-connections.md)