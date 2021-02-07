---
title: Az AK-figyelési addon engedélyezése Azure Policy használatával
description: Ismerteti, hogyan engedélyezhető az AK-figyelési addon az Azure egyéni szabályzatának használatával.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 302fdbbbcadf211339952f4b1bd97dcbb4ab1a85
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808300"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Az AK-figyelési addon engedélyezése Azure Policy használatával
Ez a cikk azt ismerteti, hogyan engedélyezhető az AK monitoring addon az Azure egyéni szabályzatával. A figyelési addon egyéni szabályzata az előfizetés vagy az erőforráscsoport hatóköréhez rendelhető hozzá. Ha az Azure Log Analytics munkaterület és az AK-fürt különböző előfizetésekben található, akkor a házirend-hozzárendelés által használt felügyelt identitásnak rendelkeznie kell a szükséges szerepkör-engedélyekkel mind az előfizetésekhez, mind a legalább a Log Analytics munkaterület erőforrásához. Hasonlóképpen, ha a házirend hatóköre az erőforráscsoport, akkor a felügyelt identitásnak rendelkeznie kell a szükséges szerepkör-engedélyekkel a Log Analytics munkaterületen, ha a munkaterület nem szerepel a kiválasztott erőforráscsoport-hatókörben.

Az addon figyeléséhez a következő szerepkörökre van szükség a Azure Policy által használt felügyelt identitásban:

 - [Azure-kubernetes-Service-közreműködő-szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-contributor-role)
 - [log-Analytics – közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Szabályzat-definíció létrehozása és kiosztása Azure Portal használatával

### <a name="create-policy-definition"></a>Házirend-definíció létrehozása

1. Töltse le az Azure egyéni házirend-definícióját az AK-figyelési addon engedélyezéséhez.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. A házirend- https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions definíció létrehozása párbeszédpanelen a következő részletekkel megnyithatja és létrehozhatja a házirend-definíciót.
 
    - **Definíció helye**: válassza ki azt az Azure-előfizetést, ahol a házirend-definíciót tárolni szeretné.
    - **Név**: *(előzetes verzió) AK-monitoring-addon*
    - **Leírás**: *Egyéni Azure-házirend a megadott hatókörben lévő Azure Kubernetes-fürt (ek) figyeléséhez az addon engedélyezéséhez*
    - **Kategória**: válassza a *meglévő használata* lehetőséget, és válasszon *Kubernetes* a legördülő listából.
    - **Házirend-szabály**: távolítsa el a meglévő minta szabályokat, és másolja a *azurepolicy.js* tartalmát a fenti #1. lépésben letöltöttre.

### <a name="assign-policy-definition-to-specified-scope"></a>Házirend-definíció társítása megadott hatókörhöz

> [!NOTE]
>  A felügyelt identitás automatikusan létrejön, és a szabályzat-definícióban meghatározott szerepköröket rendel hozzájuk.

1. Válassza ki az imént létrehozott házirend-definíciót *(előzetes verzió) AK-figyelési addon* .
4. Kattintson a *hozzárendelés** * elemre, és adja meg azt a **hatókört** , ahol a szabályzatot hozzá kell rendelni. 
5. Kattintson a **tovább** gombra, és adja meg az Azure log Analytics munkaterület erőforrás-azonosítóját. Az erőforrás-AZONOSÍTÓnak ebben a formátumban kell lennie `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Akkor hozzon létre szervizelési feladatot abban az esetben, ha a kijelölt hatókörben meglévő AK-fürtökre kívánja alkalmazni a szabályzatot.
7. A szabályzat-hozzárendelés létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Szabályzat-definíció létrehozása és társítása az Azure CLI használatával

### <a name="create-policy-definition"></a>Házirend-definíció létrehozása

1. Töltse le az Azure-beli egyéni házirend-definíciós szabályokat és paramétereket tartalmazó fájlokat a következő parancsokkal:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Hozza létre a házirend-definíciót a következő paranccsal:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Házirend-definíció társítása megadott hatókörhöz

- Hozza létre a szabályzat-hozzárendelést a következő paranccsal:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Policyról](../../governance/policy/overview.md).
- Ismerje meg, hogyan [működik a Szervizelési biztonság](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- További információ a [tárolók Azure Monitoréről](../insights/container-insights-overview.md).
- Telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)-t.

