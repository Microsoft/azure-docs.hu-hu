---
title: Üzembehelyezési előzmények törlései
description: Leírja, Azure Resource Manager automatikusan törli az üzemelő példányokat az üzembe helyezési előzményekből. Az üzemelő példányok akkor törlődnek, ha az előzmények majdnem túllépik a 800-as korlátot.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: b55c022c35c43be6818bb3c551d5db85b1927ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781846"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatikus törlés az üzembe helyezési előzményekből

Minden alkalommal, amikor üzembe helyez egy sablont, az üzembe helyezéssel kapcsolatos információkat a rendszer az üzembe helyezési előzményekbe írja. Minden erőforráscsoport legfeljebb 800 üzemelő példányra korlátozódik az üzembe helyezési előzményekben.

Azure Resource Manager automatikusan törli az üzemelő példányokat az előzményekből, miközben a korláthoz közeledik. Az automatikus törlés a korábbi viselkedéshez való változást váltja ki. Korábban manuálisan kellett törölnie az üzemelő példányokat az üzembe helyezési előzményekből, hogy elkerülje a hibákat. A változás 2020. augusztus 6-án lett megvalósítva.

**Az erőforráscsoportok üzemelő példányai támogatják az automatikus törlést. Az előfizetés, felügyeleti csoport és [](deploy-to-management-group.md)bérlői [](deploy-to-tenant.md) üzemelő példányok előzményeiben lévő üzemelő példányok jelenleg nem törlődnek automatikusan. [](deploy-to-subscription.md)**

> [!NOTE]
> Az üzembe helyezés előzményekből való törlése nincs hatással az üzembe helyezett erőforrásokra.

## <a name="when-deployments-are-deleted"></a>Üzemelő példányok törlésekor

A 775 üzemelő példány túllépése esetén a rendszer törli az üzemelő példányokat az előzményekből. Azure Resource Manager törli az üzemelő példányokat, amíg az előzmények száma nem lesz 750. Először mindig a legrégebbi üzemelő példányok törlődnek.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Törlés az üzembe helyezési előzményekből":::

> [!NOTE]
> A kezdőszám (775) és a záró szám (750) változhat.
>
> Ha az erőforráscsoport már a 800-as korláton van, a következő üzembe helyezés egy hibával meghiúsul. Az automatikus törlési folyamat azonnal elindul. Rövid várakozás után újra megpróbálhatja az üzembe helyezést.

Az üzemelő példányok mellett törléseket is aktiválhat a [what-if](template-deploy-what-if.md) művelet futtatásakor vagy az üzembe helyezés ellenőrzésekor.

Ha az üzemelő példánynak ugyanazt a nevet adja, mint az előzményekben, visszaállítja a helyét az előzményekben. Az üzembe helyezés az előzmények legújabb helyére kerül. Az üzembe helyezés helyét akkor is alaphelyzetbe állítja, [amikor](rollback-on-error.md) egy hiba után visszaállítja az adott üzemelő példányra.

## <a name="remove-locks-that-block-deletions"></a>Törlést tiltó zárolások eltávolítása

Ha [canNotDelete zárolással](../management/lock-resources.md) van egy erőforráscsoporton, az adott erőforráscsoport üzemelő példányai nem törölhetők. El kell távolítania a zárolást, hogy kihasználja az automatikus törlés előnyeit az üzembe helyezési előzményekben.

Ha a PowerShell használatával szeretne törölni egy zárolást, futtassa a következő parancsokat:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Ha az Azure CLI használatával szeretne törölni egy zárolást, futtassa a következő parancsokat:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Szükséges engedélyek

A törléseket annak a felhasználónak az identitása alapján kéri le a rendszer, aki üzembe helyezette a sablont. Az üzemelő példányok törléséhez a felhasználónak hozzáféréssel kell lennie a **Microsoft.Resources/deployments/delete művelethez.** Ha a felhasználó nem rendelkezik a szükséges engedélyekkel, az üzemelő példányok nem törlődnek az előzményekből.

Ha az aktuális felhasználó nem rendelkezik a szükséges engedélyekkel, a rendszer újra megkísérli az automatikus törlést a következő üzembe helyezés során.

## <a name="opt-out-of-automatic-deletions"></a>Az automatikus törlés lemondása

Az automatikus törlést le is hagyhatja az előzményekből. **Ezt a beállítást csak akkor használja, ha saját maga szeretné kezelni az üzembe helyezési előzményeket.** Az előzményekben továbbra is érvényben van a 800 üzemelő példány korlátja. Ha túllépi a 800 üzemelő példány korlátját, hibaüzenetet kap, és az üzembe helyezés sikertelen lesz.

Az automatikus törlés letiltásához regisztrálja a `Microsoft.Resources/DisableDeploymentGrooming` funkciójelölőt. Amikor regisztrálja a funkciójelölőt, a teljes Azure-előfizetésre kikapcsolja az automatikus törlést. Csak egy adott erőforráscsoportot lehet kikapcsolni. Az automatikus törlés újratörlésének lehetővé teszi a funkciójelölő regisztrációjának törlését.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShellhez használja a [Register-AzProviderFeature parancsot.](/powershell/module/az.resources/Register-AzProviderFeature)

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Az előfizetés aktuális állapotának megtekintése a következővel:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Az automatikus törlés újra bekapcsolható, ha az Azure REST API vagy az Azure CLI használatával.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-hez használja [az az feature register lehetőséget.](/cli/azure/feature#az_feature_register)

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Az előfizetés aktuális állapotának megtekintése a következővel:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Az automatikus törlések újra bekapcsolhatóak az [az feature unregister használatával.](/cli/azure/feature#az_feature_unregister)

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

A REST API használja a [Szolgáltatások – Regisztráció szolgáltatásokat.](/rest/api/resources/features/register)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Az előfizetés aktuális állapotának megtekintése a következővel:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Az automatikus törlések újra bekapcsolhatóak a [Szolgáltatások – Regisztráció törlése funkcióval](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Következő lépések

* Az üzembe helyezési előzmények megtekintésével kapcsolatos információkért lásd: Telepítési előzmények megtekintése [a Azure Resource Manager.](deployment-history.md)
