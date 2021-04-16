---
title: A Azure Load Balancer mód konfigurálása
titleSuffix: Azure Load Balancer
description: Ebben a cikkben elkezdi konfigurálni az elosztási módot a Azure Load Balancer a forrás IP-affinitás támogatásához.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 0c6b845a8176054dc5ec6cfc239e609f568c925d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483618"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Az elosztási mód konfigurálása Azure Load Balancer

Azure Load Balancer két elosztási módot támogat a forgalom az alkalmazások közötti elosztásához:

* Kivonatalapú
* Forrás-IP-affinitás

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a terjesztési módot a Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Terjesztési mód konfigurálása

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

Az elosztási mód konfigurációját a terheléselosztási szabály módosításával módosíthatja a portálon.

1. Jelentkezzen be a Azure Portal, és az Erőforráscsoportok elemre kattintva keresse meg a módosítani kívánt **terheléselosztást tartalmazó erőforráscsoportot.**
2. A terheléselosztás áttekintő képernyőjén válassza a **Terheléselosztási szabályok lehetőséget a** Beállítások **alatt.**
3. A terheléselosztási szabályok képernyőn válassza ki azt a terheléselosztási szabályt, amelynél módosítani szeretné az elosztási módot.
4. A szabály alatt a terjesztési mód  a Munkamenet-megőrzés legördülő lista módosításával módosítható. 

A következő lehetőségek érhetők el: 

* **Nincs (kivonatalapú)** – Azt adja meg, hogy az ugyanazon ügyféltől származó egymást követő kéréseket bármely virtuális gép kezelheti.
* **Ügyfél IP-címe (két** rekordos forrás IP-affinitás) – Meghatározza, hogy az azonos ügyfél IP-címről származó egymást követő kéréseket ugyanaz a virtuális gép fogja kezelni.
* Ügyfél IP-címe és protokollja (három rekordos forrás **IP-affinitás)** – Megadja, hogy az azonos ügyfél IP-címről és protokollkombinációból származó egymást követő kéréseket ugyanaz a virtuális gép fogja kezelni.

5. Válassza ki a terjesztési módot, majd válassza a **Mentés lehetőséget.**

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="A terheléselosztási szabály munkamenet-megőrzésének módosítása." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell használatával módosíthatja a terheléselosztó elosztási beállításait egy meglévő terheléselosztási szabályon. A következő parancs frissíti a terjesztési módot: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Állítsa be az `LoadDistribution` elem értékét a szükséges terheléselosztási típushoz. 

* Adja **meg a SourceIP** értéket a két rekordos (forrás IP-cím és cél IP-cím) terheléselosztáshoz. 

* Adja **meg a SourceIPProtocol** értéket három rekordos (forrás IP-cím, cél IP-cím és protokolltípus) terheléselosztáshoz. 

* Adja **meg az** Alapértelmezett értéket az öt rekordos terheléselosztás alapértelmezett viselkedéseként.

# <a name="cli"></a>[**parancssori felület**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Az Azure CLI használatával módosíthatja egy meglévő terheléselosztási szabály terheléselosztási beállításait.  A következő parancs frissíti az elosztási módot:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Állítsa be a értékét a szükséges `--load-distribution` terheléselosztási típushoz.

* Adja **meg a SourceIP** értéket a két rekordos (forrás IP-cím és cél IP-cím) terheléselosztáshoz. 

* Adja **meg a SourceIPProtocol** értéket három rekordos (forrás IP-cím, cél IP-cím és protokolltípus) terheléselosztáshoz. 

* Adja **meg az** Alapértelmezett értéket az öt rekordos terheléselosztás alapértelmezett viselkedéseként.

A cikkben használt paranccsal kapcsolatos további információkért lásd: [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Következő lépések

* [Az Azure Load Balancer áttekintése](load-balancer-overview.md)
* [Az internetkapcsolattal kapcsolatos terheléseltöltők konfigurálásával kapcsolatos első lépések](quickstart-load-balancer-standard-public-powershell.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
