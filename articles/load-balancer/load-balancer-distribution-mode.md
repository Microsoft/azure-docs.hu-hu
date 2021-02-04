---
title: Azure Load Balancer terjesztési mód konfigurálása
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Load Balancer terjesztési módját a forrás IP-affinitás támogatásához.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 7f2525b89f03e8bc1a2c3166b46c40b4dbb6ff17
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99562019"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer elosztási módjának konfigurálása

A Azure Load Balancer két terjesztési módot támogat az alkalmazások forgalmának elosztásához:

* Kivonatoló alapú
* Forrás-IP-affinitás

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Load Balancer terjesztési módját.


## <a name="configure-distribution-mode"></a>Terjesztési mód konfigurálása

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

A terjesztési mód konfigurációját módosíthatja a portál terheléselosztási szabályának módosításával.

1. Jelentkezzen be a Azure Portalba, és keresse meg a módosítani kívánt terheléselosztó tartalmazó erőforráscsoportot az **erőforráscsoportok** lehetőségre kattintva.
2. A terheléselosztó áttekintése képernyőn válassza a **Beállítások** területen a **terheléselosztási szabályok** elemet.
3. A terheléselosztási szabályok képernyőn válassza ki azt a terheléselosztási szabályt, amelynél módosítani szeretné a terjesztési módot.
4. A szabály alatt a terjesztési mód módosul a **munkamenet-megőrzés** legördülő lista módosításával. 

A következő lehetőségek érhetők el: 

* **Nincs (kivonat-alapú)** – azt határozza meg, hogy egy adott ügyfél egymást követő kérelmeit bármely virtuális gép kezelheti.
* **Ügyfél IP-címe (forrás IP-affinitás 2-rekord)** – azt határozza meg, hogy ugyanazon ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a virtuális gép fogja kezelni.
* **Ügyfél IP-címe és protokollja (forrás IP-affinitás 3 – rekord)** – azt határozza meg, hogy az azonos ügyfél IP-címe és a protokoll kombinációja egymást követő kérelmeket ugyanazzal a virtuális géppel fogja kezelni.

5. Válassza ki a terjesztési módot, majd kattintson a **Mentés** gombra.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="A terheléselosztási szabály munkamenet-megőrzésének módosítása." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell használatával módosítsa a terheléselosztó terjesztési beállításait egy meglévő terheléselosztási szabályon. A következő parancs frissíti a terjesztési módot: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Állítsa be az elem értékét `LoadDistribution` a terheléselosztáshoz szükséges mennyiségre. 

**SourceIP** megadása két rekordos (forrás IP-cím és cél IP-cím) terheléselosztáshoz. 

Adja meg a **sourceIPProtocol** a három rekordos (forrás IP-cím, cél IP-cím és protokoll típusa) terheléselosztáshoz. 

Az öt rekordos terheléselosztás alapértelmezett működésének **alapértelmezett értékének megadása.**

---

## <a name="next-steps"></a>Következő lépések

* [Az Azure Load Balancer áttekintése](load-balancer-overview.md)
* [Ismerkedés az internetre irányuló terheléselosztó konfigurálásával](quickstart-load-balancer-standard-public-powershell.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)