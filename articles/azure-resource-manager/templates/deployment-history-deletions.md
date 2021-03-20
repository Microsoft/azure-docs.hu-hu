---
title: Üzembehelyezési előzmények törlései
description: Ismerteti, hogyan Azure Resource Manager automatikusan törli a központi telepítéseket az üzembe helyezési előzményekből. A központi telepítések akkor törlődnek, ha az előzmények meghaladják a 800-as korlátot.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 13c65f3311e308708034bb5befb7e3c3ee158d38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91652482"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatikus törlés az üzembe helyezési előzményekből

Minden alkalommal, amikor telepít egy sablont, a központi telepítésre vonatkozó információkat az üzembe helyezési előzményekbe írja a rendszer. Az egyes erőforráscsoportok az üzembe helyezési előzményekben legfeljebb 800 üzemelő példányra korlátozódnak.

Azure Resource Manager automatikusan törli az előzményekből a korlát közelében lévő központi telepítéseket. Az automatikus törlés a korábbi viselkedés változása. Korábban a telepítési előzményekből manuálisan kellett törölni a központi telepítéseket, hogy elkerülje a hiba beolvasását. Ez a változás a 2020-es augusztus 6-án lett implementálva.

**Az erőforráscsoport-telepítések esetében az automatikus törlések támogatottak. Jelenleg az [előfizetés](deploy-to-subscription.md), a [felügyeleti csoport](deploy-to-management-group.md)és a [bérlői](deploy-to-tenant.md) központi telepítések előzményeiben üzemelő példányok nem törlődnek automatikusan.**

> [!NOTE]
> Egy központi telepítés az előzményekből való törlése nem befolyásolja az üzembe helyezett erőforrásokat.

## <a name="when-deployments-are-deleted"></a>Központi telepítések törlésekor

Az üzembe helyezések törlődnek az előzményekből, ha túllépi az 775-es üzemelő példányt. Azure Resource Manager törli az üzemelő példányokat, amíg az előzmények le nem 750. A legrégebbi üzemelő példányokat a rendszer mindig először törli.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Törlések az üzembe helyezési előzményekből":::

> [!NOTE]
> A kezdő szám (775) és a befejezési szám (750) változhat.
>
> Ha az erőforráscsoport már a 800-es korláttal rendelkezik, a következő telepítés hibát jelez. Az automatikus törlési folyamat azonnal elindul. A központi telepítést rövid várakozás után is kipróbálhatja.

Az üzemelő példányokon kívül a törléseket is aktiválhatja a [művelet](template-deploy-what-if.md) végrehajtásakor vagy a központi telepítés ellenőrzésekor.

Ha a központi telepítéshez ugyanazt a nevet adja, mint az előzményekben, akkor alaphelyzetbe állítja a helyet az előzményekben. Az üzembe helyezés az előzmények legutóbbi helyére kerül. Alaphelyzetbe állít egy központi telepítést is, ha egy hiba után visszaállítja az [adott központi telepítésre](rollback-on-error.md) .

## <a name="remove-locks-that-block-deletions"></a>Törléseket blokkoló zárolások eltávolítása

Ha egy [CanNotDelete zárolva](../management/lock-resources.md) van, az adott erőforráscsoport központi telepítése nem törölhető. A zárolást el kell távolítania, hogy kihasználhassa az automatikus törlés előnyeit az üzembe helyezési előzményekben.

Ha egy zárolás törléséhez a PowerShellt szeretné használni, futtassa a következő parancsokat:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Ha a zárolás törléséhez az Azure CLI-t szeretné használni, futtassa a következő parancsokat:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="opt-out-of-automatic-deletions"></a>Automatikus törlés engedélyezése

Az előzményekből letilthatja az automatikus törlést. **Ezt a lehetőséget csak akkor használja, ha saját maga szeretné kezelni az üzembe helyezési előzményeket.** Az előzményekben az 800-es üzemelő példányok korlátja továbbra is érvényben van. Ha túllépi az 800-es üzemelő példányokat, hibaüzenetet kap, és a telepítés sikertelen lesz.

Az automatikus törlés letiltásához regisztrálja a `Microsoft.Resources/DisableDeploymentGrooming` szolgáltatás jelölőjét. Ha regisztrálja a szolgáltatás jelzőjét, a teljes Azure-előfizetés automatikus törlését is elvégzi. Csak egy adott erőforráscsoport esetében lehet letiltani. Az automatikus törlés újraengedélyezéséhez szüntesse meg a szolgáltatás jelző regisztrációját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell esetében használja a [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Az előfizetés aktuális állapotának megtekintéséhez használja a következőt:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Az automatikus törlés újraengedélyezéséhez használja az Azure REST API vagy az Azure CLI-t.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetében használja az [az Feature Register lehetőséget](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Az előfizetés aktuális állapotának megtekintéséhez használja a következőt:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Az automatikus törlés újraengedélyezéséhez használja [az az funkció regisztrációjának törlése lehetőséget](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

REST API használja a [szolgáltatások – regisztráció lehetőséget](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Az előfizetés aktuális állapotának megtekintéséhez használja a következőt:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Az automatikus törlés újraengedélyezéséhez használja a [szolgáltatások – Regisztráció törlése lehetőséget](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Következő lépések

* Az üzembe helyezési előzmények megtekintésével kapcsolatos további tudnivalókért lásd: az [üzembe helyezési előzmények megtekintése Azure Resource Manager](deployment-history.md)használatával.
