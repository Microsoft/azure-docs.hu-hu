---
title: DNS-zónák és rekordok védelme – Azure DNS
description: Ebben a képzési tervben a DNS-zónák és-rekordhalmazok védelme Microsoft Azure DNS-ben című cikkben olvasható.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 85aaf40237b6b6687c54d4b036f280805c98e7b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618967"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS-zónák és -rekordok védelme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A DNS-zónák és-rekordok kritikus erőforrások. Egy DNS-zóna vagy egyetlen DNS-rekord törlése a szolgáltatás kimaradását eredményezheti. Fontos, hogy a DNS-zónák és-rekordok védve legyenek a jogosulatlan vagy véletlen változásokkal szemben.

Ez a cikk azt ismerteti, hogyan teszi lehetővé a Azure DNS a saját DNS-zónák és-rekordok védetté tételét az ilyen változásokkal szemben.  Azure Resource Manager az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md) és az [erőforrás-zárolások](../azure-resource-manager/management/lock-resources.md)által biztosított két hatékony értékpapír-funkciót alkalmazzuk.

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) részletes hozzáférés-kezelést tesz lehetővé az Azure-felhasználók,-csoportok és-erőforrások számára. Az Azure RBAC segítségével megadhatja a felhasználók által igényelt hozzáférési szintet. További információ arról, hogyan segíti az Azure RBAC a hozzáférés kezelését: [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A DNS-zóna közreműködői szerepköre

A DNS-zóna közreműködő szerepköre egy beépített szerepkör a magánhálózati DNS-erőforrások kezeléséhez. A felhasználóra vagy csoportra alkalmazott szerepkör lehetővé teszi a DNS-erőforrások kezelését.

Az erőforráscsoport *myResourceGroup* öt zónát tartalmaz a contoso Corporation számára. A DNS-rendszergazda DNS-zóna közreműködői engedélyeinek megadása az adott erőforráscsoporthoz, lehetővé teszi a DNS-zónák teljes körű felügyeletét. Megakadályozza a szükségtelen engedélyek megadását. A DNS-rendszergazda nem tud virtuális gépeket létrehozni vagy leállítani.

Az Azure RBAC engedélyek hozzárendelésének legegyszerűbb módja [a Azure Portalon keresztül](../role-based-access-control/role-assignments-portal.md)történik.  

Nyissa meg az erőforráscsoport **hozzáférés-vezérlés (iam)** elemét, majd válassza a **Hozzáadás** lehetőséget, majd válassza ki a **DNS-zóna közreműködői** szerepkört. Válassza ki a szükséges felhasználókat vagy csoportokat az engedélyek megadásához.

![Az Azure RBAC erőforráscsoport-szintje a Azure Portal használatával](./media/dns-protect-zones-recordsets/rbac1.png)

[Az engedélyek a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)is megadhatók:

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Zóna szintű Azure-RBAC

Az Azure RBAC-szabályok egy előfizetésre, egy erőforráscsoport vagy egy adott erőforrásra is alkalmazhatók. Ez az erőforrás lehet egy egyedi DNS-zóna vagy egy különálló rekordazonosító.

A *myResourceGroup* erőforráscsoport például a *contoso.com* zónát és egy alzónát *customers.contoso.com* tartalmaz. A CNAME rekordok mindegyik ügyfél-fiókhoz jönnek létre. A CNAME rekordok kezeléséhez használt rendszergazdai fiók engedélyeket kap a rekordok létrehozásához a *customers.contoso.com* zónában. A fiók csak *customers.contoso.com* tud kezelni.

A zóna szintű Azure RBAC engedélyek a Azure Portalon keresztül adhatók meg.  Nyissa meg a zóna **hozzáférés-vezérlés (iam)** elemét, válassza a **Hozzáadás** lehetőséget, majd válassza ki a **DNS-zóna közreműködői** szerepkört, és válassza ki a szükséges felhasználókat vagy csoportokat az engedélyek megadásához.

![DNS-zóna szintű Azure-RBAC a Azure Portal használatával](./media/dns-protect-zones-recordsets/rbac2.png)

[Az engedélyek a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)is megadhatók:

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Record set szintű Azure RBAC

Az engedélyek a rekord beállítása szintjén lesznek alkalmazva.  A felhasználó vezérlést kap a szükséges bejegyzésekhez, és nem tud más módosításokat végrehajtani.

A Record-set szintű Azure RBAC-engedélyek a Azure Portalon keresztül konfigurálhatók a **Access Control (iam)** gomb segítségével a rekord készlet lapján:

![Az Azure-RBAC rögzítése a Azure Portal használatával](./media/dns-protect-zones-recordsets/rbac3.png)

A Record-set szintű Azure RBAC-engedélyek [a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)is megadhatók:

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Egyéni szerepkörök

A beépített DNS-zóna közreműködői szerepkör lehetővé teszi a DNS-erőforrások teljes körű felügyeletét. Létrehozhatók saját egyéni Azure-szerepkörök, amelyek finomabban szabályozható szabályozást tesznek lehetővé.

A CNAME-rekordok kezeléséhez használt fiók engedélyt kap a CNAME rekordok kezelésére. A fiók nem tudja módosítani a más típusú rekordokat. A fiók nem tudja végrehajtani a zóna szintű műveleteket, például a zóna törlését.

Az alábbi példa egy egyéni szerepkör-definíciót mutat be kizárólag a CNAME rekordok kezelésére:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
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

A műveletek tulajdonság a következő DNS-specifikus engedélyeket határozza meg:

* `Microsoft.Network/dnsZones/CNAME/*` teljes hozzáférést biztosít a CNAME-rekordokhoz
* `Microsoft.Network/dnsZones/read` engedélyt ad a DNS-zónák olvasására, de nem módosítja őket, így megtekintheti azt a zónát, amelyben a CNAME létrejött.

A fennmaradó műveleteket a rendszer a [DNS-zóna közreműködői beépített szerepkörből](../role-based-access-control/built-in-roles.md#dns-zone-contributor)másolja.

> [!NOTE]
> Egy egyéni Azure-szerepkör használatával megakadályozhatja a rekordhalmazok törlését, miközben továbbra is lehetővé teszi a frissítések frissítését. Megakadályozza a rekordhalmazok törlését, de nem akadályozza meg őket a módosításban.  Az engedélyezett módosítások közé tartoznak a rekordok hozzáadása és eltávolítása a rekordhalmazból, beleértve az összes rekord eltávolítását is, ha üres rekordhalmazt szeretne hagyni. Ennek ugyanaz a hatása, mint a rekord DNS-feloldási nézőpontból való törlése.

Az egyéni szerepkör-definíciók jelenleg nem definiálhatók a Azure Portalon keresztül. A szerepkör-definíción alapuló egyéni szerepkört Azure PowerShell használatával lehet létrehozni:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Az Azure CLI használatával is létrehozható:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

A szerepkör ezután ugyanúgy hozzárendelhető a beépített szerepkörökhöz, mint a jelen cikk korábbi részében leírtak szerint.

Az egyéni szerepkörök létrehozásával, kezelésével és hozzárendelésével kapcsolatos további információkért lásd: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Erőforrás-zárolások

Azure Resource Manager támogatja a más típusú biztonsági vezérlést, az erőforrások zárolásának lehetőségét. Az erőforrás-zárolások az erőforrásra vonatkoznak, és az összes felhasználóra és szerepkörre érvényesek. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/management/lock-resources.md).

Két típusú erőforrás-zárolás létezik: **CanNotDelete** és **readonly**. Ezek a zárolási típusok egy saját DNS zónára vagy egy különálló készletre is alkalmazhatók. Az alábbi szakaszok számos gyakori forgatókönyvet ismertetnek, valamint azt, hogyan támogatják őket az erőforrás-zárolások használatával.

### <a name="protecting-against-all-changes"></a>Védelem az összes módosítás ellen

A változtatások elkerülése érdekében alkalmazzon írásvédett zárolást a zónára. Ez a zárolás megakadályozza, hogy új rekordhalmazok jöjjenek létre, és a meglévő rekordhalmazok módosítva vagy törölve legyenek.

A zóna szintű erőforrás-zárolások a Azure Portal használatával hozhatók létre.  A DNS-zóna lapon válassza a **zárolások**, majd a **+ Hozzáadás** lehetőséget:

![A zóna szintű erőforrások zárolása a Azure Portal keresztül](./media/dns-protect-zones-recordsets/locks1.png)

A zóna szintű erőforrások zárolása [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock)használatával is létrehozható:

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](/cli/azure/lock#az-lock-create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Egyéni rekordok védelme

Ha meg szeretné akadályozni, hogy egy meglévő DNS-rekordot módosítson a módosítással, alkalmazza a írásvédett zárolást a rekordra.

> [!NOTE]
> A CanNotDelete zárolásának egy bejegyzéstípusra való alkalmazása nem hatékony vezérlő. Megakadályozza a rekordok törlését, de nem akadályozza meg a módosítást.  Az engedélyezett módosítások közé tartoznak a rekordok hozzáadása és eltávolítása a rekordhalmazból, beleértve az összes rekord eltávolítását is, ha üres rekordhalmazt szeretne hagyni. Ennek ugyanaz a hatása, mint a rekord DNS-feloldási nézőpontból való törlése.

A set szintű erőforrás-zárolások jelenleg csak Azure PowerShell használatával konfigurálhatók.  Nem támogatottak a Azure Portal vagy az Azure CLI-ben.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>A zónák törlésének védelme

Amikor töröl egy zónát Azure DNSban, a zónában lévő összes rekordhalmaz törölve lesz.  Ez a művelet nem vonható vissza. Ha véletlenül töröl egy kritikus zónát, azzal jelentős hatással lehet az üzleti tevékenységre.  Fontos, hogy védelmet nyújtson a zónák véletlen törlésével szemben.

A CanNotDelete zárolásának zónában való alkalmazása megakadályozza a zóna törlését. A zárolásokat a gyermek erőforrások öröklik. A zárolás megakadályozza a zónában lévő rekordhalmazok törlését. A fenti megjegyzésben leírtak szerint ez nem hatékony, mivel a rekordok továbbra is eltávolíthatók a meglévő rekordhalmazokból.

Alternatív megoldásként alkalmazzon egy CanNotDelete-zárolást a zónában lévő egyik rekorddal, például a SOA-rekorddal. A rendszer nem törli a zónát a rekordhalmazok törlése nélkül. Ez a zárolás védi a zónák törlését, miközben továbbra is engedélyezi a zónán belüli rekordhalmazok szabadon módosítását. Ha kísérlet történt a zóna törlésére, Azure Resource Manager észleli ezt az eltávolítást. Az Eltávolítás a SOA-rekord törlését is törli, Azure Resource Manager blokkolja a hívást, mert a SOA zárolva van.  Nincsenek rekordhalmazok törölve.

A következő PowerShell-parancs egy CanNotDelete-zárolást hoz létre a megadott zóna SOA-rekordjával:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Egy másik lehetőség, hogy megakadályozza a zónák véletlen törlését egyéni szerepkör használatával. Ez a szerepkör biztosítja, hogy a zónák kezeléséhez használt fiókok ne rendelkezzenek a zónák törléséhez szükséges engedélyekkel. 

Ha törölni kell egy zónát, két lépésből álló törlést is kikényszerítheti:

 - Első lépésként adja meg a zóna törlésének engedélyeit
 - Másodszor, adja meg a zóna törléséhez szükséges engedélyeket.

Az egyéni szerepkör a fiókok által elért összes zónára vonatkozóan működik. A zónák törlésére vonatkozó engedélyekkel rendelkező fiókok, például az előfizetés tulajdonosa, véletlenül törölhetnek egy zónát.

Mindkét módszert – erőforrás-zárolásokat és egyéni szerepköröket – egyszerre is használhatja, a DNS-zónák védelmének mélyreható megközelítésével.

## <a name="next-steps"></a>Következő lépések

* További információ az Azure RBAC használatáról: [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md).
* További információ az erőforrás-zárolások használatáról: [erőforrások zárolása Azure Resource Managersal](../azure-resource-manager/management/lock-resources.md).
