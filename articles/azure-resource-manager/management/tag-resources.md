---
title: Erőforrások, erőforráscsoportok és előfizetések címkézése a logikai szervezet számára
description: Bemutatja, hogyan alkalmazhat címkéket az Azure-erőforrások számlázáshoz és felügyelethez való rendszerezéséhez.
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb93673b643fd13efe9ffea148c5fb1d072f9e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896223"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Címkék használata az Azure-erőforrások és a felügyeleti hierarchia rendszerezéséhez

Címkéket alkalmazhat az Azure-erőforrások, az erőforráscsoportok és az előfizetések számára, hogy logikailag szervezze őket a besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkézési stratégia megvalósításával kapcsolatos javaslatokért lásd: [erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> A címkék nevei a kis-és nagybetűk megkülönböztetését jelentik a műveletekhez. A címkével ellátott címkét a rendszer a beborítástól függetlenül frissíti vagy lekéri. Előfordulhat azonban, hogy az erőforrás-szolgáltató megtartja a címke nevéhez megadott burkolatot. Látni fogja, hogy a ház a Cost-jelentésekben szerepel.
> 
> A címke értékei megkülönböztetik a kis-és nagybetűket.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Szükséges hozzáférés

Két módon kérheti le a szükséges hozzáférést az erőforrások címkézéséhez.

- Írási jogosultsággal rendelkezhet a **Microsoft. Resources/Tags** erőforrástípus. Ez a hozzáférés lehetővé teszi, hogy bármilyen erőforrást címkével lássa el, még akkor is, ha nem rendelkezik hozzáféréssel az erőforráshoz. A [címke közreműködői](../../role-based-access-control/built-in-roles.md#tag-contributor) szerepköre engedélyezi ezt a hozzáférést. Jelenleg a tag közreműködői szerepkör nem alkalmazhat címkéket az erőforrásokra vagy az erőforráscsoportok a portálon keresztül. Címkéket is alkalmazhat az előfizetésekhez a portálon keresztül. Támogatja az összes címkézési műveletet a PowerShell és a REST API használatával.  

- Az erőforráshoz írási hozzáféréssel is rendelkezhet. A [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör biztosítja a szükséges hozzáférést a címkék bármely entitásra való alkalmazásához. Ha csak egy erőforrás-típusra kíván címkéket alkalmazni, használja az adott erőforrás közreműködői szerepkörét. Ha például címkéket szeretne alkalmazni a virtuális gépekre, használja a [virtuális gép közreműködőjét](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Címkék alkalmazása

Azure PowerShell két parancsot kínál a címkék alkalmazásához: [New-AzTag](/powershell/module/az.resources/new-aztag) és [Update-AzTag](/powershell/module/az.resources/update-aztag). Az az. Resources Module 1.12.0 vagy újabb verzióval kell rendelkeznie. A verzióját a segítségével is megtekintheti `Get-Module Az.Resources` . Telepítheti a modult, vagy [telepítheti a Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1-es vagy újabb verzióját.

A **New-AzTag** az erőforráson, az erőforráscsoporton vagy az előfizetésen lévő összes címkét lecseréli. A parancs hívásakor adja meg a címkével ellátni kívánt entitás erőforrás-AZONOSÍTÓját.

Az alábbi példa a címkék egy készletét alkalmazza a Storage-fiókra:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Ha a parancs befejeződik, figyelje meg, hogy az erőforrásnak két címkéje van.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Ha újra futtatja a parancsot, de ezúttal más címkékkel, figyelje meg, hogy a korábbi címkék el lesznek távolítva.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Ha címkéket szeretne felvenni egy olyan erőforráshoz, amely már rendelkezik címkékkel, használja az **Update-AzTag**. Állítsa be a **-Operation** paramétert az **egyesítéshez**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Figyelje meg, hogy a két új címke hozzá lett adva a két meglévő címkéhez.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Minden címke nevének csak egy értéke lehet. Ha egy címkéhez új értéket ad meg, akkor a rendszer akkor is lecseréli a régi értéket, ha az egyesítési műveletet használja. Az alábbi példa megváltoztatja az állapot címkéjét a Normálról zöldre.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Ha a **-Operation** paramétert **lecseréli**, a meglévő címkéket a címkék új készlete váltja fel.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Csak az új címkék maradnak meg az erőforráson.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Ugyanezek a parancsok az erőforráscsoportok vagy az előfizetések esetében is működnek. Adja meg a címkével ellátni kívánt erőforráscsoport vagy előfizetés azonosítóját.

Ha új címkéket szeretne hozzáadni egy erőforráscsoporthoz, használja a következőt:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Egy erőforráscsoport címkéinak frissítéséhez használja a következőt:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Új címkék előfizetéshez való hozzáadásához használja a következőt:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Az előfizetéshez tartozó címkék frissítéséhez használja a következőt:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Egy erőforráscsoporthoz több azonos nevű erőforrás is tartozhat. Ebben az esetben beállíthatja az egyes erőforrásokat a következő parancsokkal:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Címkék listázása

Ha egy erőforrás, erőforráscsoport vagy előfizetés címkéit szeretné lekérni, használja a [Get-AzTag](/powershell/module/az.resources/get-aztag) parancsot, és adja meg az entitás erőforrás-azonosítóját.

Az adott erőforráshoz tartozó címkék megtekintéséhez használja a következőt:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Egy erőforráscsoport címkéit a következő paranccsal tekintheti meg:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Az előfizetés címkéit a következő paranccsal tekintheti meg:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Listázás címke szerint

A megadott címke névvel és értékkel rendelkező erőforrások lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Ha olyan erőforrásokat szeretne lekérni, amelyek címkével megadott névvel rendelkeznek, használja a következőt:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

A megadott címke névvel és értékkel rendelkező erőforráscsoportok lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Címkék eltávolítása

Adott címkék eltávolításához használja az **Update-AzTag** és a set **-Operation** parancsot a **törléshez**. Adja meg a törölni kívánt címkéket.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

A megadott címkék el lesznek távolítva.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Az összes címke eltávolításához használja a [Remove-AzTag](/powershell/module/az.resources/remove-aztag) parancsot.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Címkék alkalmazása

Az Azure CLI két parancsot kínál a címkék alkalmazásához – [az tag Create](/cli/azure/tag#az_tag_create) és [az az tag Update](/cli/azure/tag#az_tag_update). Az Azure CLI-2.10.0 vagy újabb verziójára van szükség. A verzióját a segítségével is megtekintheti `az version` . A frissítéshez vagy a telepítéshez lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az **tag Create** lecseréli az összes címkét az erőforrás, az erőforráscsoport vagy az előfizetés elemre. A parancs hívásakor adja meg a címkével ellátni kívánt entitás erőforrás-AZONOSÍTÓját.

Az alábbi példa a címkék egy készletét alkalmazza a Storage-fiókra:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Ha a parancs befejeződik, figyelje meg, hogy az erőforrásnak két címkéje van.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Ha újra futtatja a parancsot, de ezúttal más címkékkel, figyelje meg, hogy a korábbi címkék el lesznek távolítva.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Ha címkéket szeretne felvenni egy olyan erőforráshoz, amely már rendelkezik címkékkel, használja a következőt: `az tag update` . Állítsa a paramétert a következőre: `--operation` `Merge` .

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

Figyelje meg, hogy a két új címke hozzá lett adva a két meglévő címkéhez.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Minden címke nevének csak egy értéke lehet. Ha egy címkéhez új értéket ad meg, akkor a rendszer akkor is lecseréli a régi értéket, ha az egyesítési műveletet használja. Az alábbi példa megváltoztatja az állapot címkéjét a Normálról zöldre.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Ha a paramétert a értékre állítja `--operation` `Replace` , a meglévő címkéket a címkék új készlete váltja fel.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Csak az új címkék maradnak meg az erőforráson.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Ugyanezek a parancsok az erőforráscsoportok vagy az előfizetések esetében is működnek. Adja meg a címkével ellátni kívánt erőforráscsoport vagy előfizetés azonosítóját.

Ha új címkéket szeretne hozzáadni egy erőforráscsoporthoz, használja a következőt:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Egy erőforráscsoport címkéinak frissítéséhez használja a következőt:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Új címkék előfizetéshez való hozzáadásához használja a következőt:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Az előfizetéshez tartozó címkék frissítéséhez használja a következőt:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Címkék listázása

Egy erőforrás, erőforráscsoport vagy előfizetés címkéjének lekéréséhez használja az az [tag List](/cli/azure/tag#az_tag_list) parancsot, és adja meg az entitás erőforrás-azonosítóját.

Az adott erőforráshoz tartozó címkék megtekintéséhez használja a következőt:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Egy erőforráscsoport címkéit a következő paranccsal tekintheti meg:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Az előfizetés címkéit a következő paranccsal tekintheti meg:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Listázás címke szerint

A megadott címke névvel és értékkel rendelkező erőforrások lekéréséhez használja a következőt:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Ha olyan erőforrásokat szeretne lekérni, amelyek címkével megadott névvel rendelkeznek, használja a következőt:

```azurecli-interactive
az resource list --tag Team --query [].name
```

A megadott címke névvel és értékkel rendelkező erőforráscsoportok lekéréséhez használja a következőt:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Címkék eltávolítása

Adott címkék eltávolításához használja a parancsot, `az tag update` és állítsa a következőre: `--operation` `Delete` . Adja meg a törölni kívánt címkéket.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

A megadott címkék el lesznek távolítva.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Az összes címke eltávolításához használja az az [tag delete](/cli/azure/tag#az_tag_delete) parancsot.

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Szóközök feldolgozása

Ha a címke neve vagy értéke szóközt tartalmaz, tegye idézőjelek közé.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="arm-templates"></a>ARM-sablonok

Az üzembe helyezés során egy Azure Resource Manager sablonnal (ARM-sablonnal) címkézheti az erőforrásokat, az erőforráscsoportokat és az előfizetéseket.

> [!NOTE]
> Az ARM-sablonon keresztül alkalmazott címkék felülírják a meglévő címkéket.

### <a name="apply-values"></a>Értékek alkalmazása

Az alábbi példa három címkével rendelkező Storage-fiókot telepít. A címkék közül kettő ( `Dept` és `Environment` ) konstans értékre van beállítva. Az egyik címke ( `LastDeployed` ) egy olyan paraméterre van beállítva, amely alapértelmezett értéke az aktuális dátum.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Objektum alkalmazása

Megadhat olyan objektumparamétert, amely több címkét tartalmaz, majd alkalmazhatja azt az objektumot a címkeelemre. Ez a megközelítés nagyobb rugalmasságot biztosít az előző példánál, mert az objektum különböző tulajdonságokkal rendelkezhet. Az objektum minden tulajdonsága az erőforrás külön címkéjévé válik. Az alábbi példa egy `tagValues` nevű paramétert tartalmaz, amely a címkeelemre van alkalmazva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>JSON-karakterlánc alkalmazása

Ha több értéket szeretne tárolni egyetlen címkében, alkalmazzon a megfelelő értékeket képviselő JSON-sztringet. A teljes JSON-karakterlánc egyetlen címkeként van tárolva, amely nem lehet hosszabb 256 karakternél. Az alábbi példában egy `CostCenter` nevű címke szerepel, amely egy JSON-sztring számos értékét tartalmazza:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Címkék alkalmazása az erőforrás-csoportból

Ha címkéket szeretne alkalmazni egy erőforrás-csoportból egy erőforrásra, használja a [resourceGroup ()](../templates/template-functions-resource.md#resourcegroup) függvényt. A címke értékének beolvasása során a szintaxis helyett használja a `tags[tag-name]` szintaxist `tags.tag-name` , mert néhány karakter nem megfelelően van értelmezve a dot jelölésben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Címkék alkalmazása erőforráscsoportok vagy előfizetések számára

Hozzáadhat címkéket egy erőforráscsoporthoz vagy előfizetéshez a **Microsoft. Resources/Tags** erőforrástípus üzembe helyezésével. A címkéket a rendszer a célként megadott erőforráscsoporthoz vagy előfizetésre alkalmazza a központi telepítéshez. Minden alkalommal, amikor központilag telepíti a sablont, a rendszer már alkalmazta a címkéket.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Ha a címkéket egy erőforráscsoporthoz szeretné alkalmazni, használja a PowerShell vagy az Azure CLI-t. Telepítse a címkével ellátni kívánt erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

A címkék előfizetésre való alkalmazásához használja a PowerShell vagy az Azure CLI-t. Telepítse a címkével ellátni kívánt előfizetést.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Az előfizetések telepítésével kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../templates/deploy-to-subscription.md).

A következő sablon hozzáadja a címkéket egy objektumból egy erőforráscsoporthoz vagy előfizetésbe.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Ha az Azure REST API használatával szeretne címkékkel dolgozni, használja a következőt:

* [Címkék – létrehozás vagy frissítés hatókör](/rest/api/resources/tags/createorupdateatscope) (Put művelet)
* [Címkék – frissítés a hatókörben](/rest/api/resources/tags/updateatscope) (javítási művelet)
* [Címkék – beolvasás hatóköre](/rest/api/resources/tags/getatscope) (lekérési művelet)
* [Címkék – törlés a hatókörben](/rest/api/resources/tags/deleteatscope) (törlési művelet)

## <a name="inherit-tags"></a>Címkék öröklése

Az erőforráscsoport vagy az előfizetés által alkalmazott címkéket nem öröklik az erőforrások. Ha címkéket szeretne alkalmazni egy előfizetésből vagy erőforráscsoporthoz az erőforrásokra, tekintse meg az [Azure-szabályzatok – címkék](tag-policies.md)című témakört.

## <a name="tags-and-billing"></a>Címkék és számlázás

Címkék segítségével a számlázási adatok is csoportosíthatók. Ha például több virtuális gépet futtat különböző vállalatok számára, akkor a használatot címkék segítségével tudja költséghely szerint csoportosítani. A címkék a költségek futtatókörnyezet szerinti besorolására, például az éles környezetben futó virtuális gépek használatának kiszámlázására is felhasználhatók.

A címkékre vonatkozó információkat a Azure Portalból elérhető, vesszővel tagolt (CSV) fájl letöltésével kérheti le. További információkért tekintse [meg az Azure számlázási és napi használati adatainak letöltését vagy megtekintését](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)ismertető témakört. A Azure Fiókközpont a használati fájl letöltésekor válassza a **2. verziót**. A számlázási címkéket támogató szolgáltatások esetében a címkék a **címkék** oszlopban jelennek meg.

REST API műveletekhez tekintse meg az [Azure számlázási REST API referenciáját](/rest/api/billing/).

## <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* Minden erőforrás, erőforráscsoport és előfizetés legfeljebb 50 címke név/érték párokat tartalmazhat. Ha a maximálisan megengedettnél több címkét kell alkalmaznia, használjon egy JSON-karakterláncot a címke értékhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Egy erőforráscsoport vagy előfizetés több olyan erőforrást is tartalmazhat, amelyek mindegyike 50 címke név/érték párokat tartalmaz.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* A címkék nem alkalmazhatók a klasszikus erőforrásokra, például a Cloud Servicesra.
* A címkék nevei nem tartalmazhatják a következő karaktereket:,,,,, `<` `>` `%` `&` `\` `?` , `/`

   > [!NOTE]
   > Azure DNS zónák és Traffic Manager szolgáltatások jelenleg nem teszik lehetővé a szóközök használatát a címkében.
   >
   > Az Azure bejárati ajtó nem támogatja a `#` címke nevében való használatát.
   >
   > Azure Automation és Azure CDN csak a 15 címkét támogatja az erőforrásokon.

## <a name="next-steps"></a>Következő lépések

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* A címkézési stratégia megvalósításával kapcsolatos javaslatokért lásd: [erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
