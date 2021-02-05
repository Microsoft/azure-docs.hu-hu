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
ms.openlocfilehash: 22d7af4f307a99d2d2e29bc1f494d327394e4f10
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594282"
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
* **Ügyfél IP-címe (forrás IP-affinitása kétrekordos)** – azt határozza meg, hogy ugyanazon ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a virtuális gép fogja kezelni.
* **Ügyfél IP-címe és protokollja (forrás IP-kapcsolat három rekordos)** – azt határozza meg, hogy az azonos ügyfél IP-címe és a protokoll kombinációja egymást követő kérelmeket ugyanazzal a virtuális géppel fogja kezelni.

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

Adja meg az elem értékét `LoadDistribution` a szükséges terheléselosztási típushoz. 

* **SourceIP** megadása két rekordos (forrás IP-cím és cél IP-cím) terheléselosztáshoz. 

* Adja meg a **SourceIPProtocol** a három rekordos (forrás IP-cím, cél IP-cím és protokoll típusa) terheléselosztáshoz. 

* Az öt rekordos terheléselosztás alapértelmezett működésének **alapértelmezett értékének megadása.**

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Az Azure CLI használatával módosíthatja a terheléselosztási terjesztési beállításokat egy meglévő terheléselosztási szabályon.  A következő parancs frissíti a terjesztési módot:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution SourceIP \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Állítsa be a értéket `--load-distribution` a szükséges terheléselosztási típushoz.

* **SourceIP** megadása két rekordos (forrás IP-cím és cél IP-cím) terheléselosztáshoz. 

* Adja meg a **SourceIPProtocol** a három rekordos (forrás IP-cím, cél IP-cím és protokoll típusa) terheléselosztáshoz. 

* Az öt rekordos terheléselosztás alapértelmezett működésének **alapértelmezett értékének megadása.**

További információ a cikkben használt parancsról: az [Network LB Rule Update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Következő lépések

* [Az Azure Load Balancer áttekintése](load-balancer-overview.md)
* [Ismerkedés az internetre irányuló terheléselosztó konfigurálásával](quickstart-load-balancer-standard-public-powershell.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)