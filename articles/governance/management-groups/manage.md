---
title: A felügyeleti csoportok használata – Azure-szabályozás
description: Útmutató a felügyeleti csoport hierarchiájának megtekintéséhez, karbantartásához, frissítéséhez és törléséhez.
ms.date: 01/15/2021
ms.topic: conceptual
ms.openlocfilehash: 05e78d66c29e500842d14a6eeb563c4569ecf0bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596478"
---
# <a name="manage-your-resources-with-management-groups"></a>Erőforrások kezelése felügyeleti csoportokkal

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure-beli felügyeleti csoportok hatóköre az előfizetések fölötti szint. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási feltételeket alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától. További információ a felügyeleti csoportokról: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure Resource Manager a felhasználói jogkivonatok és a felügyeleti csoport gyorsítótára 30 percig tart, mielőtt azok frissítésre kényszerülnek. Ha bármilyen műveletet végez, például egy felügyeleti csoport vagy előfizetés áthelyezését, akár 30 percet is igénybe vehet. Ahhoz, hogy a frissítések hamarabb megjelenjenek, frissítenie kell a jogkivonatot a böngésző frissítésével, a bejelentkezéssel és a kijelentkezéssel, vagy új jogkivonat igénylésével.  

## <a name="change-the-name-of-a-management-group"></a>Felügyeleti csoport nevének módosítása

A felügyeleti csoport nevét a portál, a PowerShell vagy az Azure CLI használatával módosíthatja.

### <a name="change-the-name-in-the-portal"></a>A név módosítása a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **felügyeleti csoportok** lehetőséget.

1. Válassza ki az átnevezni kívánt felügyeleti csoportot.

1. Válassza a **részletek** lehetőséget.

1. A lap tetején kattintson a **csoport átnevezése** lehetőségre.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Képernyőfelvétel a műveleti sáv és a csoport átnevezése gombra a felügyeleti csoport lapon." border="false":::

1. Amikor megnyílik a menü, írja be az új nevet, amelyet meg szeretne jeleníteni.

   :::image type="content" source="./media/rename_context.png" alt-text="Képernyőfelvétel a csoport átnevezése ablakról és a felügyeleti csoportok átnevezési lehetőségeiről." border="false":::

1. Kattintson a **Mentés** gombra.

### <a name="change-the-name-in-powershell"></a>A név módosítása a PowerShellben

A megjelenítendő név frissítéséhez használja az **Update-AzManagementGroup**. Ha például a "contoso IT" nevű felügyeleti csoportokat a "contoso csoport" névre szeretné módosítani, a következő parancsot kell futtatnia:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>A név módosítása az Azure CLI-ben

Az Azure CLI esetében használja az Update parancsot.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Felügyeleti csoport törlése

Felügyeleti csoport törléséhez a következő követelményeknek kell teljesülniük:

1. A felügyeleti csoportban nincsenek alárendelt felügyeleti csoportok vagy előfizetések. Ha egy előfizetést vagy felügyeleti csoportot másik felügyeleti csoportba szeretne áthelyezni, tekintse meg [a felügyeleti csoportok és előfizetések áthelyezése a hierarchiába című részt](#moving-management-groups-and-subscriptions).

1. Írási engedéllyel kell rendelkeznie a felügyeleti csoportra ("tulajdonos", "közreműködő" vagy "felügyeleti csoport közreműködője"). Ha szeretné megtekinteni, hogy milyen engedélyekkel rendelkezik, válassza ki a felügyeleti csoportot, majd válassza a **iam** lehetőséget. További információ az Azure-szerepkörökről:  
   [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Törlés a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **felügyeleti csoportok** lehetőséget.

1. Válassza ki a törölni kívánt felügyeleti csoportot.

1. Válassza a **részletek** lehetőséget.

1. Válassza a **Törlés** elemet

   :::image type="content" source="./media/delete.png" alt-text="Képernyőkép a felügyeleti csoport oldaláról, a &quot;Törlés&quot; gombbal kiemelve." border="false":::

   > [!TIP]
   > Ha az ikon le van tiltva, az egérmutatót az ikon fölé húzva megjelenik az OK.

1. Ekkor megnyílik egy ablak, amely megerősíti, hogy törölni szeretné a felügyeleti csoportot.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Képernyőkép a &quot;csoport törlése&quot; megerősítési párbeszédpanelről a felügyeleti csoportok törléséhez." border="false":::

1. Válassza az **Igen** lehetőséget.

### <a name="delete-in-powershell"></a>Törlés a PowerShellben

A felügyeleti csoportok törléséhez használja a **Remove-AzManagementGroup** parancsot a powershellen belül.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Törlés az Azure CLI felületen

Az Azure CLI-vel használja az parancsot az az Account Management-Group delete paranccsal.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Felügyeleti csoportok megtekintése

Bármely olyan felügyeleti csoportot megtekintheti, amelyhez közvetlen vagy örökölt Azure-szerepkör tartozik.  

### <a name="view-in-the-portal"></a>Megtekintés a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **felügyeleti csoportok** lehetőséget.

1. A felügyeleti csoport hierarchia lapja betöltődik. Ezen a lapon megtekintheti az összes olyan felügyeleti csoportot és előfizetést, amelyhez hozzáfér. A csoport nevének kiválasztásával a hierarchia alacsonyabb szintre kerül. A navigáció ugyanúgy működik, mint a fájlkezelő.

1. A felügyeleti csoport részleteinek megtekintéséhez kattintson a **(részletek)** hivatkozásra a felügyeleti csoport címe mellett. Ha ez a hivatkozás nem érhető el, nincs engedélye a felügyeleti csoport megtekintésére.

   :::image type="content" source="./media/main.png" alt-text="Képernyőfelvétel a felügyeleti csoportok lapról alárendelt felügyeleti csoportok és előfizetések." border="false":::

### <a name="view-in-powershell"></a>Megtekintés a PowerShellben

Az összes csoport beolvasásához használja a Get-AzManagementGroup parancsot. Tekintse meg az az [. Resources](/powershell/module/az.resources/Get-AzManagementGroup) modulokat a felügyeleti csoport teljes listájához a PowerShell-parancsok beolvasása szakaszban.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Egyetlen felügyeleti csoport adatai esetében használja a-csoportnév paramétert.

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Ha egy adott felügyeleti csoportot és az alatta lévő hierarchia összes szintjét szeretné visszaadni, használja a **-Expand** és a **-recurse** paramétereket.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Megtekintés az Azure CLI-ben

Az összes csoport lekéréséhez használja a LIST parancsot.  

```azurecli-interactive
az account management-group list
```

Egyetlen felügyeleti csoport adatait a show parancs használatával tekintheti meg.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Ha egy adott felügyeleti csoportot és az alatta lévő hierarchia összes szintjét szeretné visszaadni, használja a **-Expand** és a **-recurse** paramétereket.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Felügyeleti csoportok és előfizetések áthelyezése   

A felügyeleti csoport létrehozásának egyik oka az előfizetések összevonása. Csak a felügyeleti csoportok és előfizetések hozhatók létre egy másik felügyeleti csoport gyermekeire. Egy felügyeleti csoportba áthelyezett előfizetés örökli a szülő felügyeleti csoport összes felhasználói hozzáférését és házirendjét.

Ha egy felügyeleti csoportot vagy előfizetést egy másik felügyeleti csoport gyermekének kíván áthelyezni, a három szabályt igaz értékre kell kiértékelni.

Ha az áthelyezés műveletet végzi, a következőkre lesz szüksége: 

- Felügyeleti csoport írási és szerepkör-hozzárendelési írási engedélyei az alárendelt előfizetéshez vagy a felügyeleti csoporthoz.
  - Beépített szerepkör – példa **tulajdonosa**
- Felügyeleti csoport írási hozzáférése a cél szülő felügyeleti csoportnál.
  - Beépített szerepkör – példa: **tulajdonos**, **közreműködő**, **felügyeleti csoport közreműködője**
- Felügyeleti csoport írási hozzáférése a meglévő szülő felügyeleti csoporton.
  - Beépített szerepkör – példa: **tulajdonos**, **közreműködő**, **felügyeleti csoport közreműködője**

**Kivétel**: Ha a cél vagy a meglévő szülő felügyeleti csoport a gyökérszintű felügyeleti csoport, az engedélyek követelményei nem érvényesek. Mivel a legfelső szintű felügyeleti csoport az összes új felügyeleti csoport és előfizetés alapértelmezett kihelyezett helye, nincs szükség arra, hogy az adott elem áthelyezéséhez szükséges engedélyekkel rendelkezik.

Ha az előfizetés tulajdonosi szerepköre az aktuális felügyeleti csoporttól örökölt, az áthelyezési célok korlátozottak. Az előfizetést csak egy másik felügyeleti csoportba helyezheti át, ahol a tulajdonosi szerepköre van. Az előfizetés nem helyezhető át olyan felügyeleti csoportba, ahol Ön csak közreműködő, mert elveszíti az előfizetés tulajdonjogát. Ha közvetlenül az előfizetés tulajdonosi szerepköréhez van rendelve, akkor áthelyezheti azt bármely olyan felügyeleti csoportba, ahol Ön közreműködő.

Ha szeretné megtekinteni, hogy milyen engedélyekkel rendelkezik a Azure Portalban, válassza ki a felügyeleti csoportot, majd válassza a **iam** lehetőséget. Az Azure-szerepkörökkel kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Előfizetések áthelyezése 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Meglévő előfizetés hozzáadása egy felügyeleti csoporthoz a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **felügyeleti csoportok** lehetőséget.

1. Válassza ki azt a felügyeleti csoportot, amelynek a szülőjét tervezi.

1. Az oldal tetején válassza az **előfizetés hozzáadása** elemet.

1. Válassza ki az előfizetést a listában a megfelelő AZONOSÍTÓval.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Képernyőkép az &quot;előfizetés hozzáadása&quot; lehetőségről a felügyeleti csoportba felvenni kívánt meglévő előfizetés kiválasztásához." border="false":::

1. Válassza a Save (Mentés) lehetőséget.

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Előfizetés eltávolítása egy felügyeleti csoportból a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **felügyeleti csoportok** lehetőséget.

1. Válassza ki azt a felügyeleti csoportot, amelyet a jelenlegi szülőként szeretne megtervezni.  

1. Válassza ki az előfizetéshez tartozó sor végén található ellipszist az áthelyezni kívánt listában.

   :::image type="content" source="./media/move_small.png" alt-text="Képernyőkép az előfizetés alternatív menüjéről az áthelyezés lehetőség kiválasztásához." border="false":::

1. Válassza az **Áthelyezés** lehetőséget.

1. A megnyíló menüben válassza ki a **szülő felügyeleti csoportot**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Képernyőkép az &quot;áthelyezés&quot; ablakról és az előfizetés másik felügyeleti csoportba való áthelyezésének lehetőségeiről." border="false":::

1. Kattintson a **Mentés** gombra.

### <a name="move-subscriptions-in-powershell"></a>Előfizetések áthelyezése a PowerShellben

Az előfizetés PowerShellben való áthelyezéséhez használja a New-AzManagementGroupSubscription parancsot.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

A és az előfizetés és a felügyeleti csoport közötti kapcsolat eltávolításához használja az Remove-AzManagementGroupSubscription parancsot.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Előfizetések áthelyezése az Azure CLI-ben

Ha egy előfizetést szeretne áthelyezni a CLI-ben, használja az Add parancsot.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Ha el szeretné távolítani az előfizetést a felügyeleti csoportból, használja az előfizetés eltávolítása parancsot.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-arm-template"></a>Előfizetések mozgatása ARM-sablonban

Az előfizetés Azure Resource Manager sablonban (ARM-sablon) való áthelyezéséhez használja a következő sablont.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="move-management-groups"></a>Felügyeleti csoportok áthelyezése 

### <a name="move-management-groups-in-the-portal"></a>Felügyeleti csoportok áthelyezése a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **felügyeleti csoportok** lehetőséget.

1. Válassza ki azt a felügyeleti csoportot, amelynek a szülőjét tervezi.

1. A lap tetején válassza a **felügyeleti csoport hozzáadása** lehetőséget.

1. A megnyíló menüben válassza ki, hogy új vagy meglévő felügyeleti csoportot szeretne használni.

   - Az új lehetőség kiválasztásával új felügyeleti csoport jön létre.
   - Ha kiválaszt egy meglévőt, az összes olyan felügyeleti csoport legördülő lista jelenik meg, amelyet át tud helyezni ebbe a felügyeleti csoportba.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Képernyőkép a felügyeleti csoport hozzáadása lehetőségről az új felügyeleti csoport létrehozásához." border="false":::

1. Kattintson a **Mentés** gombra.

### <a name="move-management-groups-in-powershell"></a>Felügyeleti csoportok áthelyezése a PowerShellben

A PowerShell Update-AzManagementGroup parancsával másik csoportba helyezheti át a felügyeleti csoportokat.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Felügyeleti csoportok áthelyezése az Azure CLI-ben

Az Update paranccsal helyezheti át a felügyeleti csoportokat az Azure CLI használatával.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

A felügyeleti csoportok támogatottak az [Azure-tevékenységnaplóban](../../azure-monitor/essentials/platform-logs-overview.md). A felügyeleti csoportba tartozó összes eseményt a többi Azure-erőforrással megegyező központi helyen kérdezheti le. Például megtekintheti egy adott felügyeleti csoporthoz tartozó összes szerepkör-hozzárendelés vagy szabályzat-hozzárendelés módosításait.

:::image type="content" source="./media/al-mg.png" alt-text="Képernyőkép a kiválasztott felügyeleti csoporttal kapcsolatos tevékenységi naplókról és műveletekről." border="false":::

Az Azure Portalon kívüli felügyeleti csoportok lekérdezésekor a felügyeleti csoportok célhatóköre a következőhöz hasonlóan néz ki: **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Más erőforrás-szolgáltatóktól származó felügyeleti csoportok hivatkozása

Ha más erőforrás-szolgáltató műveleteiből származó felügyeleti csoportokra hivatkozik, használja a következő elérési utat hatókörként. Ez az elérési út a PowerShell, az Azure CLI és a REST API-k használatakor használatos.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Ennek az elérési útnak a használatára példa az új szerepkör-hozzárendelés egy felügyeleti csoporthoz való hozzárendelése a PowerShellben:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Ugyanazt a hatókörbeli elérési utat használja a rendszer a házirend-definíciók egy felügyeleti csoportba való beolvasásakor.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>Következő lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](./create-management-group-portal.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](./manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)