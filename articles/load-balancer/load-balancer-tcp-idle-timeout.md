---
title: A terheléselosztó TCP-alaphelyzetbe állítása és üresjárati Időtúllépés beállítása
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure Load Balancer TCP üresjárati időtúllépését és alaphelyzetbe állítását.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747181"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>A TCP alaphelyzetbe állításának és üresjárati időkorlátjának konfigurálása Azure Load Balancer

A Azure Load Balancer a következő Üresjárati időkorlát-tartománnyal rendelkezik:

* 4 perc – 100 perc a kimenő szabályoknál
* 4 perc és 30 perc a Load Balancer szabályok és a bejövő NAT-szabályok esetében

Alapértelmezés szerint 4 percre van beállítva. Ha egy inaktivitási időtartam hosszabb az időtúllépési értéknél, nincs garancia arra, hogy a TCP-vagy HTTP-munkamenet az ügyfél és a szolgáltatás között marad. 

A következő szakaszok azt ismertetik, hogyan lehet módosítani a terheléselosztó erőforrásainak üresjárati időtúllépését és TCP-visszaállítási beállításait.

## <a name="set-tcp-reset-and-idle-timeout"></a>A TCP-visszaállítás és az üresjárat időkorlátjának beállítása
---
# <a name="portal"></a>[**Portál**](#tab/tcp-reset-idle-portal)

A terheléselosztó üresjárati időkorlátjának és TCP-visszaállításának beállításához szerkessze a terheléselosztási szabályt. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

3. Válassza ki a terheléselosztó erőforráscsoportot. Ebben a példában az erőforráscsoport neve **myResourceGroup**.

4. Válassza ki a terheléselosztót. Ebben a példában a terheléselosztó neve **myLoadBalancer**.

5. A **Beállítások** területen válassza a **terheléselosztási szabályok** lehetőséget.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="A terheléselosztó szabályainak szerkesztése." border="true":::

6. Válassza ki a terheléselosztási szabályt. Ebben a példában a terheléselosztási szabály neve **myLBrule**.

7. A terheléselosztási szabályban helyezze át a csúszkát **Üresjárati időkorlát (perc)** fölé az időtúllépési értékre.  

8. A **TCP alaphelyzetbe állítása** területen válassza az **engedélyezve** lehetőséget.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Állítsa be az Üresjárati időkorlát és a TCP-visszaállítás értékét." border="true":::

9. Kattintson a **Mentés** gombra.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Az Üresjárati időkorlát és a TCP-beállítások beállításához állítsa be a következő terheléselosztási szabály paramétereinek értékeit a [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

Cserélje le az alábbi példákat az erőforrások értékeire:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

Az Üresjárati időkorlát és a TCP alaphelyzetbe állításának beállításához használja a következő paramétereket az [az Network LB Rule Update](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **– tétlen – időtúllépés**
* **--Enable-TCP-reset**

A Kezdés előtt ellenőrizze a környezetet:

* Jelentkezzen be a Azure Portalba, és győződjön meg arról, hogy az előfizetése aktív a futtatásával `az login` .
* A futtatásával tekintse meg az Azure CLI-verziót egy terminálon vagy parancsablakban `az --version` . A legújabb verzióra vonatkozó megjegyzések a [legújabb kiadási megjegyzésekben](/cli/azure/release-notes-azure-cli?tabs=azure-cli)találhatók.
  * Ha nem rendelkezik a legújabb verzióval, frissítse a telepítést az [operációs rendszer vagy a platform telepítési útmutatóját](/cli/azure/install-azure-cli)követve.

Cserélje le az alábbi példákat az erőforrások értékeire:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Következő lépések

További információ a TCP Üresjárati időkorlát és alaphelyzetbe állításáról: [Load BALANCER TCP alaphelyzetbe állítása és üresjárati időtúllépés](load-balancer-tcp-reset.md)

A terheléselosztó terjesztési módjának konfigurálásával kapcsolatos további információkért lásd: [terheléselosztó terjesztési módjának konfigurálása](load-balancer-distribution-mode.md).
