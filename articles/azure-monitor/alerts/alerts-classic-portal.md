---
title: Klasszikus metrikai riasztások létrehozása és kezelése Azure Monitor használatával
description: Ismerje meg, hogyan hozhatja létre, tekintheti meg és kezelheti a klasszikus metrikus riasztási szabályokat a Azure Portal, a CLI vagy a PowerShell használatával.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 854867bb6f40bdd8f0fac28e69d936a4027760b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714374"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Klasszikus metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával

> [!WARNING]
> Ez a cikk bemutatja, hogyan hozhat létre régebbi klasszikus metrikai riasztásokat. Azure Monitor mostantól támogatja [az újabb, közel valós idejű metrikai riasztásokat és új riasztási élményt](./alerts-overview.md). A klasszikus riasztások [kimaradnak](./monitoring-classic-retirement.md) a nyilvános Felhőbeli felhasználók számára, bár a használat továbbra is korlátozott a **2021**-ig. A Azure Government Cloud és az Azure China 21Vianet klasszikus riasztásai a **2024. február 29**-én törlődnek.
>

A klasszikus metrikus riasztások Azure Monitor lehetővé teszik, hogy értesítést kapjon, ha az egyik mérőszám egy küszöbértéket túlnyúlik. A klasszikus metrikai riasztások olyan régebbi funkciók, amelyek csak a nem dimenziós metrikák esetében engedélyezik a riasztást. Létezik egy metrikus riasztások nevű meglévő újabb funkció, amely továbbfejlesztett funkciókat biztosít a klasszikus metrikai riasztások terén. További információ az új metrikai riasztások funkcióról a [metrikus riasztások áttekintése című témakörben](./alerts-metric-overview.md)olvasható. Ebben a cikkben azt ismertetjük, hogyan hozhatók létre, tekinthetők meg és kezelhetők a klasszikus metrikus riasztási szabályok Azure Portal, az Azure CLI és a PowerShell használatával.

## <a name="with-azure-portal"></a>Azure Portal

1. Keresse meg a figyelni kívánt erőforrást a [portálon](https://portal.azure.com/), majd jelölje ki.

2. A **figyelés** szakaszban válassza a **riasztások (klasszikus)** lehetőséget. A szöveg és az ikon némileg eltérő lehet a különböző erőforrások esetében. Ha nem találja a **riasztásokat (klasszikus)** , akkor előfordulhat, hogy **riasztási** vagy **riasztási szabályokban** találja.

    ![Figyelés](media/alerts-classic-portal/AlertRulesButton.png)

3. Válassza a **metrikai riasztás hozzáadása (klasszikus)** parancsot, majd töltse ki a mezőket.

    ![Riasztás hozzáadása](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nevezze** el a riasztási szabályt. Ezután válasszon egy **leírást**, amely szintén megjelenik az értesítési e-mailekben.

5. Válassza ki a figyelni kívánt **metrikát** . Ezután válasszon egy **feltételt** és egy **küszöbértéket** a metrika számára. Adja meg azt az **időtartamot** is, ameddig a metrikai szabálynak meg kell felelnie a riasztási eseményindítók előtt. Ha például az "elmúlt 5 perc" időszakot használja, és a riasztás a 80%-nál nagyobb CPU-t keres, akkor a riasztás akkor aktiválódik, ha a processzor 5 percen át konzisztensen meghaladja a 80%-ot. Az első indítás után újraindul, ha a processzor 5 percig 80% alatt marad. A CPU-metrika mérése percenként történik.

6. Válassza ki az **e-mail-tulajdonosokat...** ha azt szeretné, hogy a rendszergazdák és a társ-rendszergazdák e-mail értesítéseket kapjanak, amikor a riasztás tüzek

7. Ha értesítést szeretne küldeni a riasztások további e-mail-címeire, vegye fel őket a **további rendszergazdai e-mailek (ok)** mezőbe. Pontosvesszővel elválasztva több e-mailt a következő formátumban: *e-mail \@ contoso. com; email2 \@ contoso.com*

8. Ha azt szeretné, hogy a riasztás bekövetkezik, a **webhook** mezőben érvényes URI-t kell létrehoznia.

9. Ha Azure Automation használ, kiválaszthat egy runbook, amelyet a riasztás tüzek esetén szeretne futtatni.

10. A riasztás létrehozásához kattintson **az OK gombra** .

Néhány percen belül a riasztás aktív, és a korábban leírt módon aktiválódik.

A riasztás létrehozása után kiválaszthatja a következő feladatok egyikét, és elvégezheti a következőket:

* Megtekintheti a mérőszám küszöbértékét és az előző naptól számított tényleges értékeket bemutató diagramot.
* Szerkessze vagy törölje.
* **Tiltsa le** vagy **engedélyezze** , ha átmenetileg le kívánja állítani vagy folytatni szeretné az értesítések fogadását az adott riasztáshoz.

## <a name="with-azure-cli"></a>Az Azure CLI-vel

Az előző szakasz ismerteti, hogyan hozhat létre, tekinthet meg és kezelhet metrikus riasztási szabályokat a Azure Portal használatával. Ez a szakasz azt írja le, hogyan végezheti el ugyanezt a platformfüggetlen [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával. Az Azure CLI használatának leggyorsabb módja a [Azure Cloud Shellon](../../cloud-shell/overview.md)keresztül.

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Az összes klasszikus metrikai riasztási szabály beolvasása egy erőforráscsoport esetében

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Egy adott klasszikus metrikai riasztási szabály részleteinek megtekintése

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Klasszikus metrikai riasztási szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Klasszikus metrikai riasztási szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>A PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a rész bemutatja, hogyan hozhat létre, tekinthet meg és kezelhet klasszikus metrikai riasztásokat a PowerShell-parancsok használatával. A cikkben szereplő példák bemutatják, hogyan használhatók Azure Monitor-parancsmagok a klasszikus metrikai riasztásokhoz.

1. Ha még nem tette meg, állítsa be a PowerShellt úgy, hogy a számítógépen fusson. További információ: [a PowerShell telepítése és konfigurálása](/powershell/azure/). Áttekintheti Azure Monitor PowerShell-parancsmagok teljes listáját Azure Monitor (betekintő [) parancsmagok](/powershell/module/az.applicationinsights)esetén is.

2. Először jelentkezzen be az Azure-előfizetésbe.

    ```powershell
    Connect-AzAccount
    ```

3. Ekkor megjelenik a bejelentkezési képernyő. Miután bejelentkezett a fiókjába, a TenantID és az alapértelmezett előfizetés-azonosító megjelenik. Az összes Azure-parancsmag az alapértelmezett előfizetés kontextusában működik. A következő parancs használatával tekintheti meg az Ön számára elérhető előfizetések listáját:

    ```powershell
    Get-AzSubscription
    ```

4. Ha módosítani szeretné a munkakörnyezetét egy másik előfizetésre, használja a következő parancsot:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Egy erőforráscsoport összes klasszikus metrikai riasztási szabályát lekérheti:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Megtekintheti a klasszikus metrika riasztási szabályának részleteit

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Lekérheti a célként megadott erőforráshoz beállított összes riasztási szabályt. Például a virtuális gépen beállított összes riasztási szabály.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. A klasszikus riasztási szabályok már nem hozhatók létre a PowerShell használatával. Riasztási szabály létrehozásához az új ["Add-AzMetricAlertRule"](/powershell/module/az.monitor/add-azmetricalertrule) parancsot kell használnia.

## <a name="next-steps"></a>Következő lépések

- [Hozzon létre egy klasszikus metrikai riasztást egy Resource Manager-sablonnal](./alerts-enable-template.md).
- [Klasszikus metrikai riasztást küld egy nem Azure-rendszerről webhook használatával](./alerts-webhooks.md).