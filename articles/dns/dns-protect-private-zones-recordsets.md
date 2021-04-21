---
title: Privát DNS-zónák és -rekordok védelme – Azure DNS
description: Ebben a képzési útvonalban megismerheti a privát DNS-zónák és -rekordhalmazok védelmét az Microsoft Azure DNS-ben.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: a68b40852750e124749ac838c50acae2212c4732
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785856"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Privát DNS-zónák és -rekordok védelme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

saját DNS zónák és rekordok kritikus fontosságú erőforrások. Egy DNS-zóna vagy egyetlen DNS-rekord törlése szolgáltatáskimaradást eredményezhet. Fontos, hogy a DNS-zónák és -rekordok védve vannak a jogosulatlan vagy véletlen módosításokkal szemben.

Ez a cikk azt ismerteti Azure DNS hogyan teszi lehetővé a privát DNS-zónák és -rekordok védelmét az ilyen módosítások ellen.  A szolgáltatás két hatékony funkcióját Azure Resource Manager: [Az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC)](../role-based-access-control/overview.md) és az [erőforrás-zárolásokat.](../azure-resource-manager/management/lock-resources.md)

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-felhasználók, -csoportok és -erőforrások hozzáférés-felügyeletének finomhangolt kezelését. Az Azure RBAC lehetővé teszi a felhasználók számára szükséges hozzáférési szint megadását. További információ arról, hogyan segít az Azure RBAC a hozzáférések kezelésében: Mi az [az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)?

### <a name="the-private-dns-zone-contributor-role"></a>A saját DNS Zóna-közreműködő szerepkör

A saját DNS Zóna-közreműködő szerepkör beépített szerepkör a privát DNS-erőforrások kezeléséhez. Ez a felhasználóra vagy csoportra alkalmazott szerepkör lehetővé teszi számukra a privát DNS-erőforrások kezelését.

A *myPrivateDNS* erőforráscsoport a Contoso Corporation öt zónáját tartalmazza. Ha a DNS-rendszergazda saját DNS zóna-közreműködői engedélyeket az adott erőforráscsoporthoz, teljes körű vezérlést biztosít az adott DNS-zónák felett. Elkerüli a szükségtelen engedélyek megadását. A DNS-rendszergazda nem tud virtuális gépeket létrehozni vagy leállítani.

Az Azure RBAC-engedélyek hozzárendelésének legegyszerűbb módja [az Azure Portal.](../role-based-access-control/role-assignments-portal.md)  

Nyissa meg az erőforráscsoport hozzáférés-vezérlését  **(IAM),** válassza a Hozzáadás lehetőséget, majd a **Saját DNS zóna-közreműködő szerepkört.** Válassza ki az engedélyek megadásához szükséges felhasználókat vagy csoportokat.

![Erőforráscsoport-szintű Azure RBAC a Azure Portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Az engedélyek a következővel [is Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>Privát zónaszintű Azure RBAC

Az Azure RBAC-szabályok egy előfizetésre, egy erőforráscsoportra vagy egy adott erőforrásra is alkalmazhatók. Ez az erőforrás lehet egy egyéni DNS-zóna vagy egy különálló rekordhalmaz.

Például a *myPrivateDNS* erőforráscsoport tartalmazza  a zóna private.contoso.com egy *alzónát* customers.private.contoso.com. A CNAME rekordok minden ügyfélfiókhoz létrejönnek. A CNAME-rekordok kezeléséhez használt rendszergazdai fiókhoz engedélyek vannak hozzárendelve a rekord létrehozásához a *customers.private.contoso.com* zónában. A fiók csak a *customers.private.contoso.com* kezelheti.

A zónaszintű Azure RBAC-engedélyeket a következő Azure Portal.  Nyissa **meg a hozzáférés-vezérlést (IAM)** a zónához, válassza a Hozzáadás lehetőséget, majd saját DNS zóna **közreműködője szerepkört.**  Válassza ki az engedélyek megadásához szükséges felhasználókat vagy csoportokat.

![DNS-zónaszintű Azure RBAC a Azure Portal](./media/dns-protect-private-zones-recordsets/rbac2.png)

Az engedélyek a következővel [is Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Rekordhalmaz-szint Azure RBAC

Az engedélyek a rekordhalmaz szintjén vannak alkalmazva.  A felhasználó szabályozhatja a szükséges bejegyzéseket, és nem tud más módosításokat tenni.

Rekordhalmazszintű Azure RBAC-engedélyek a Azure Portal használatával konfigurálhatóak a **Access Control (IAM)** gombbal a rekordhalmaz oldalán:

![Képernyőkép a Access Control (I A M) gombról.](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Képernyőkép a Access Control a Szerepkör-hozzárendelés hozzáadása elemről.](./media/dns-protect-private-zones-recordsets/rbac4.png)

A rekordhalmaz-szintű Azure RBAC-engedélyek a következő használatával [is Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető:](../role-based-access-control/role-assignments-cli.md)

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Egyéni szerepkörök

A beépített zóna-saját DNS szerepkör teljes körű vezérlést tesz lehetővé egy DNS-erőforrás felett. Saját egyéni Azure-szerepköröket is létrehozhat, amelyek részletes vezérlést biztosítanak.

A CNAME-rekordok kezeléséhez használt fiók csak a CNAME rekordok kezelésére kap engedélyt. A fiók nem tudja módosítani más típusú rekordokat. A fiók nem tud zónaszintű műveleteket( például zóna törlése) végrehajtásához.

Az alábbi példa egy egyéni szerepkör-definíciót mutat be, amely csak CNAME-rekordok kezelésére való:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A Műveletek tulajdonság a következő DNS-specifikus engedélyeket határozza meg:

* `Microsoft.Network/privateDnsZones/CNAME/*` teljes körű vezérlést biztosít a CNAME-rekordok felett
* `Microsoft.Network/privateDNSZones/read` engedélyezi a DNS privát zónák olvasását, de nem módosítja őket, így láthatja a zónát, amelyben a CNAME létrehozása történik.

> [!NOTE]
> Az egyéni Azure-szerepkör használata a rekordhalmazok törlésének megakadályozására, miközben azok frissítését továbbra is lehetővé teszi, nem hatékony vezérlés. Megakadályozza a rekordhalmazok törléset, de nem akadályozza meg azok módosítását.  A megengedett módosítások közé tartozik a rekordok hozzáadása és eltávolítása a rekordhalmazból, beleértve az összes rekord eltávolítását, hogy üres rekordhalmazt hagyjanak. Ennek ugyanaz a hatása, mint a rekordhalmaz dns-feloldási nézetből való törlésének.

Az egyéni szerepkör-definíciók jelenleg nem határozhatóak meg a Azure Portal. Az ezen a szerepkör-definíción alapuló egyéni szerepkör a következő Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Az Azure CLI-n keresztül is létre lehet hozva:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

A szerepkör ezután a beépített szerepkörökhöz hasonló módon rendelhető hozzá, ahogyan azt a cikk korábbi cikke is ismerteti.

További információ az egyéni szerepkörök létrehozásáról, kezelésről és hozzárendelésről: [Egyéni Azure-szerepkörök.](../role-based-access-control/custom-roles.md)

## <a name="resource-locks"></a>Erőforrás-zárolások

Azure Resource Manager támogatja a biztonsági vezérlés egy másik típusát, az erőforrások zárolásának képességét. Az erőforrás-zárolások az erőforrásra vonatkoznak, és az összes felhasználóra és szerepkörre érvényesek. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/management/lock-resources.md).

Kétféle erőforrás-zárolás létezik: **CanNotDelete** és **ReadOnly.** Ezek a zárolási típusok alkalmazhatók egy adott saját DNS zónára vagy egy különálló rekordhalmazra.  Az alábbi szakaszok számos gyakori forgatókönyvet ismertetnek, és ismertetik, hogyan lehet őket erőforrás-zárolással támogatni.

### <a name="protecting-against-all-changes"></a>Védelem az összes módosítás ellen

A módosítások megelőzéséhez alkalmazson readOnly (Csak olvasható) zárolást a zónára. Ez a zárolás megakadályozza az új rekordhalmazok létrejöttét, valamint a meglévő rekordhalmazok módosítását vagy törléset.

A zónaszintű erőforrás-zárolások a fürtök Azure Portal.  A DNS-zóna lapon válassza a **Zárolások** lehetőséget, majd a **+Hozzáadás lehetőséget:**

![Zónaszintű erőforrás-zárolások a Azure Portal](./media/dns-protect-private-zones-recordsets/locks1.png)

A zónaszintű erőforrás-zárolások a következő [Azure PowerShell:](/powershell/module/az.resources/new-azresourcelock)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető:](/cli/azure/lock#az_lock_create)

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Egyéni rekordok védelme

Ha meg szeretné akadályozni egy meglévő DNS-rekordhalmaz módosítását, alkalmazson ReadOnly zárolást a rekordhalmazra.

> [!NOTE]
> A CanNotDelete zárolás rekordhalmazra való alkalmazása nem hatékony vezérlő. Ez megakadályozza a rekordhalmaz törléset, de nem akadályozza meg annak módosítását.  A megengedett módosítások közé tartozik a rekordok hozzáadása és eltávolítása a rekordhalmazból, beleértve az összes rekord eltávolítását, hogy üres rekordhalmazt hagyjanak. Ennek ugyanaz a hatása, mint a rekordhalmaz dns-feloldási nézetből való törlésének.

A rekordhalmaz-szintű erőforrás-zárolások jelenleg csak a Azure PowerShell.  Ezek nem támogatottak az azure Azure Portal Azure CLI-ban.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Védelem zónatörlés ellen

Ha törölnek egy zónát a Azure DNS, a zónában található összes rekordhalmaz törlődik.  Ez a művelet nem vonható vissza. Ha véletlenül töröl egy kritikus zónát, az jelentős hatással lehet az üzletre.  Fontos, hogy védelmet nyújtsunk a zóna véletlen törlése ellen.

Ha CanNotDelete zárolást alkalmaz egy zónára, az megakadályozza a zóna törléset. A zárolásokat a gyermekerőforrások öröklik. A zárolás megakadályozza a zónában található rekordhalmazok törléset. A fenti megjegyzésben leírtak szerint ez nem hatékony, mivel a rekordok továbbra is eltávolíthatók a meglévő rekordhalmazból.

Alternatív megoldásként canNotDelete zárolást alkalmazhat a zónában található rekordhalmazra, például az SOA-rekordhalmazra. A zóna nem törlődik a rekordhalmazok törlése nélkül. Ez a zárolás védelmet nyújt a zónatörlés ellen, miközben a zónában található rekordhalmazok szabadon módosíthatók. Ha megkísérlik törölni a zónát, a Azure Resource Manager észleli az eltávolítást. Az eltávolítás az SOA-rekordhalmazt is törli, Azure Resource Manager blokkolja a hívást, mert az SOA zárolva van.  Nem törlődnek rekordhalmazok.

A következő PowerShell-parancs canNotDelete zárolást hoz létre az adott zóna SOA-rekordja ellen:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Egy másik lehetőség a zónák véletlen törlésének megakadályozására egy egyéni szerepkör használata. Ez a szerepkör biztosítja, hogy a zónák kezeléséhez használt fiókok ne használjanak zóna törlésére vonatkozó engedélyeket. 

Ha törölnie kell egy zónát, kényszerítheti a kétlépéses törlést:

 - Először adja meg a zóna törlésére vonatkozó engedélyeket
 - Másodszor adjon engedélyeket a zóna törléséhez.

Az egyéni szerepkör az ezen fiókok által elért összes zónával működik. A zóna törlésére vonatkozó engedélyekkel rendelkező fiókok, például az előfizetés tulajdonosa, véletlenül törölhetik a zónákat.

A DNS-zónavédelem mélységi védelmi megközelítéseként mindkét megközelítés használható egyszerre – az erőforrás-zárolások és az egyéni szerepkörök is.

## <a name="next-steps"></a>Következő lépések

* Az Azure RBAC használatával kapcsolatos további információkért lásd: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)?
* Az erőforrás-zárolások használatának további információiért lásd: Erőforrások zárolása [a Azure Resource Manager.](../azure-resource-manager/management/lock-resources.md)
