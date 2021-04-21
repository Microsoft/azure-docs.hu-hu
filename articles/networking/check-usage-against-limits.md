---
title: Ellenőrizze az Azure-erőforrások használatát a | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizheti az Azure-erőforráshasználatot az Azure-előfizetés korlátaival.
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
ms.openlocfilehash: d629e65106145a4af364cd9dd489250c8910c25d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778570"
---
# <a name="check-resource-usage-against-limits"></a>Erőforrás-használat összevetése a korlátokkal

Ebből a cikkből megtudhatja, hogyan láthatja az előfizetésében üzembe helyezett egyes hálózati erőforrás-típusokat és az előfizetés [korlátait.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Az erőforrás-használat korlátokhoz való átalakítási képessége hasznos az aktuális használat nyomon követéséhez és a jövőbeli használat tervezésére. A használat nyomon [követéséhez](#azure-portal)használhatja az Azure Portalt, a [PowerShellt](#powershell)vagy az [Azure CLI-t.](#azure-cli)

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az Azure [Portalra.](https://portal.azure.com)
2. Az oldal bal felső sarkában válassza Azure Portal **Minden szolgáltatás lehetőséget.**
3. A *Szűrő mezőben* adja meg az **Előfizetések** lehetőséget. Amikor a keresési eredmények között megjelenik az **Előfizetések** elem, válassza ki.
4. Válassza ki annak az előfizetésnek a nevét, amely használati adatait meg szeretné tekinteni.
5. A **BEÁLLÍTÁSOK alatt** válassza a Használat + kvóta **lehetőséget.**
6. Az alábbi lehetőségek közül választhat:
   - **Erőforrástípusok:** Kiválaszthatja az összes erőforrástípust, vagy kiválaszthatja a megtekinteni kívánt erőforrástípusokat.
   - **Szolgáltatók:** Kiválaszthatja az összes erőforrás-szolgáltatót, vagy választhatja a **Számítás,** **a Hálózat** vagy a Tárolás **lehetőséget.**
   - **Helyek:** Kiválaszthatja az összes Azure-helyet, vagy kiválaszthat adott helyeket.
   - Kiválaszthatja, hogy az összes erőforrást, vagy csak azokat az erőforrásokat mutassa, ahol legalább egy üzembe van helyezni.

     Az alábbi képen látható példa az összes olyan hálózati erőforrást mutatja be, amely legalább egy, az USA keleti régiója régión belül üzembe helyezett erőforrással rendelkezik:

       ![Használati adatok megtekintése](./media/check-usage-against-limits/view-usage.png)

     Az oszlopokat az oszlopfejléc kiválasztásával rendezheti. A megjelenő korlátok az előfizetés korlátai. Ha növelnie kell egy alapértelmezett korlátot, válassza a **Kérés** növelése lehetőséget, majd töltse ki és küldje el a támogatási kérést. Minden erőforrásra vonatkozik egy korlát, amely az Azure korlátai között [szerepel.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Ha az aktuális korlát már a maximális számmal van megszabadva, nem növelhető.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancsokat futtathatja a Azure Cloud Shell [vagy](https://shell.azure.com/powershell)a PowerShell futtatásával a számítógépről. A Azure Cloud Shell egy ingyenes interaktív felület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShellt a számítógépről futtatja, szüksége lesz a Azure PowerShell 1.0.0-s vagy újabb verziójára. A telepített verzió megkereséhez futtassa a következőt `Get-Module -ListAvailable Az` a számítógépen: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a futtatásával is be kell `Login-AzAccount` jelentkeznie az Azure-ba.

A [Get-AzNetworkUsage-val](/powershell/module/az.network/get-aznetworkusage)megtekintheti a használatot a korlátokkal szemben. Az alábbi példa lekérte az erőforrások használatát, ahol legalább egy erőforrás az USA keleti régiója helyen van telepítve:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

A kimenet formátuma megegyezik az alábbi példakimenettel:

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

Ha Azure parancssori felületi (CLI)-parancsokat használ a cikkben található feladatok [](https://shell.azure.com/bash)végrehajtásához, futtassa a parancsokat a Azure Cloud Shell-ban, vagy futtassa a parancssori felületet a számítógépről. Ehhez a cikkhez az Azure CLI 2.0.32-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha az Azure CLI-t helyileg futtatja, akkor a futtatásával is be kell `az login` jelentkeznie az Azure-ba.

A használatot az [az network list-usages az limits (az network list-usages) nézetben megtekintheti a korlátokkal szemben.](/cli/azure/network#az_network_list_usages) Az alábbi példa az USA keleti régiója helyen található erőforrások használatát lekérte:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

A kimenet formátuma megegyezik az alábbi példakimenettel:

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