---
title: Az Azure-erőforrás használatának korlátozása a korlátok között | Microsoft Docs
description: Ismerje meg, hogyan ellenőrizhető az Azure-erőforrások használata az Azure-előfizetési korlátokon.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: 31eeb31fb78a4e9552e64121e0e85b5fd8d9b773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210632"
---
# <a name="check-resource-usage-against-limits"></a>Erőforrás-használat összevetése a korlátokkal

Ebből a cikkből megtudhatja, hogyan tekintheti meg az előfizetésében üzembe helyezett egyes hálózati erőforrástípusok számát, valamint az [előfizetési korlátokat](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) . Az erőforrás-használat korlátainak megtekintésére való képesség hasznos lehet az aktuális használat nyomon követéséhez és a jövőbeli használat megtervezéséhez. A használat nyomon követéséhez használhatja az [Azure Portalt](#azure-portal), a [PowerShellt](#powershell)vagy az [Azure CLI](#azure-cli) -t.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com).
2. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** lehetőséget.
3. Adja meg az *előfizetéseket* a **szűrő** mezőben. Amikor a keresési eredmények között megjelenik az **Előfizetések** elem, válassza ki.
4. Válassza ki annak az előfizetésnek a nevét, amelyre vonatkozóan meg szeretné tekinteni a használati adatokat.
5. A **Beállítások** területen válassza a **használat + kvóta** elemet.
6. Az alábbi lehetőségek közül választhat:
   - **Erőforrástípusok**: kiválaszthatja az összes erőforrástípust, vagy kiválaszthatja a megtekinteni kívánt erőforrások típusait.
   - **Szolgáltatók**: kiválaszthatja az összes erőforrás-szolgáltatót, vagy kiválaszthatja a **számítás**, a **hálózat** vagy a **tárterület** lehetőséget.
   - **Helyszínek**: kiválaszthatja az összes Azure-helyet, vagy kijelölhet bizonyos helyszíneket is.
   - Kiválaszthatja az összes erőforrás megjelenítését, vagy csak azokat az erőforrásokat, amelyeken legalább egy telepítve van.

     Az alábbi képen látható példa az összes olyan hálózati erőforrást mutatja, amely legalább egy, az USA keleti régiójában üzembe helyezett erőforrással rendelkezik:

       ![Használati adatok megtekintése](./media/check-usage-against-limits/view-usage.png)

     Az oszlopokat az oszlop fejlécének kiválasztásával rendezheti. A megjelenő korlátok az előfizetésre vonatkozó korlátok. Ha növelje az alapértelmezett korlátot, válassza a **kérés növekedése** lehetőséget, majd fejezze be és küldje el a támogatási kérést. Az összes erőforráshoz az Azure [korlátaiban](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)legfeljebb egy korlát szerepel. Ha a jelenlegi korlátja már a maximális számon van, a határérték nem növelhető.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy futtathatja a PowerShellt a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell modulra, a 1.0.0 vagy újabb verzióra. Futtassa a parancsot a `Get-Module -ListAvailable Az` számítógépen, és keresse meg a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor is futtatnia kell az Azure-ba való `Login-AzAccount` bejelentkezéshez.

A [Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage)használatával megtekintheti a korlátozásokat. A következő példa beolvassa azokat az erőforrásokat, amelyekben legalább egy erőforrás üzembe van helyezve az USA keleti régiójában:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

A kimenet a következő példa kimenetével megegyező formátumban jelenik meg:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez a cikkhez az Azure CLI 2.0.32 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha helyileg futtatja az Azure CLI-t, az Azure-ba való bejelentkezéshez is futnia kell `az login` .

Az az [Network List-](/cli/azure/network#az-network-list-usages)relimits használatával megtekintheti a korlátozásokat. Az alábbi példa az USA keleti régiójában található erőforrások használatát kéri le:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

A kimenet a következő példa kimenetével megegyező formátumban jelenik meg:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```