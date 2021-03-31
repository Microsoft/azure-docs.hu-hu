---
title: DNS-zónák kezelése a Azure DNSban – PowerShell | Microsoft Docs
description: A DNS-zónák az Azure PowerShell használatával kezelhetők. Ez a cikk ismerteti, hogyan lehet frissíteni, törölni és létrehozni a DNS-zónákat Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6abcca9d9888dc8968d7233e7aee6cd76aa215f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965748"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>DNS-zónák kezelése a PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klasszikus Azure CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónákat Azure PowerShell használatával. A DNS-zónákat a platformfüggetlen [Azure CLI](dns-operations-dnszones-cli.md) vagy a Azure Portal használatával is kezelheti.

Ez az útmutató kifejezetten a nyilvános DNS-zónákat tárgyalja. További információ a Azure DNS privát zónák kezeléséről Azure PowerShell használatáról: a [Azure DNS Private Zones használatának első lépései Azure PowerShell használatával](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzureRmDnsZone` parancsmag használatával hozható létre.

A következő példa egy *contoso.com* nevű DNS-zónát hoz létre az *MyResourceGroup* nevű erőforráscsoporthoz:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Az alábbi példa bemutatja, hogyan hozhat létre egy DNS-zónát két [Azure Resource Manager címkével](dns-zones-records.md#tags), a *Project = demo* és a *env = test* használatával:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

A Azure DNS támogatja a magánhálózati DNS-zónákat is.  További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md). További információt a privát DNS-zónák létrehozásáról [az Azure DNS privát zónái PowerShell segítségével történő használatának első lépéseit](./private-dns-getstarted-powershell.md) ismertető cikkben olvashat.

## <a name="get-a-dns-zone"></a>DNS-zóna beszerzése

DNS-zóna lekéréséhez használja a `Get-AzureRmDnsZone` parancsmagot. A művelet egy, a Azure DNS meglévő zónájához tartozó DNS-zóna objektumot ad vissza. Az objektum a zónával kapcsolatos információkat (például a rekordhalmazok számát) tartalmazza, de nem tartalmazza a rekordhalmazokat (lásd: `Get-AzureRmDnsRecordSet` ).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>DNS-zónák listázása

Ha kihagyja a zóna nevét a `Get-AzureRmDnsZone` parancsból, felsorolhatja az egy erőforráscsoportba tartozó összes zónát. Ez a művelet zónaobjektumok tömbjét adja vissza.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Ha a zóna és az erőforráscsoport nevét is kihagyja a `Get-AzureRmDnsZone` parancsból, felsorolhatja az Azure-előfizetéshez tartozó összes zónát.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>DNS-zóna frissítése

A DNS-zóna erőforrásai a `Set-AzureRmDnsZone` parancsmaggal módosíthatók. Ez a parancsmag nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd a [DNS-rekordok kezeléséről szóló részt](dns-operations-recordsets.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Az írható zóna tulajdonságai jelenleg a [zóna-erőforráshoz tartozó "címkék" Azure Resource Manager](dns-zones-records.md#tags)vannak korlátozva.

A DNS-zónák frissítéséhez a következő két módszer egyikét használhatja:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Adja meg a zónát a zóna neve és az erőforráscsoport használatával

Ez a módszer a meglévő zónák címkéit a megadott értékekkel helyettesíti.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

Ez a módszer lekéri a meglévő zóna objektumot, módosítja a címkéket, majd véglegesíti a módosításokat. Így a meglévő címkék megtekinthetők.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

$Zone objektummal való használatakor a `Set-AzureRmDnsZone` rendszer a [ETAG ellenőrzi](dns-zones-records.md#etags) , hogy az egyidejű módosítások ne legyenek felülírva. `-Overwrite`Ezeket az ellenőrzéseket a választható kapcsoló használatával tilthatja le.

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

A DNS-zónák a parancsmag használatával törölhetők `Remove-AzureRmDnsZone` .

> [!NOTE]
> A DNS-zónák törlésével a zónában található összes DNS-rekord is törlődni fog. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zónát használó szolgáltatások futtatása meghiúsul a zóna törlésekor.
>
>A zónák véletlen törlése elleni védelemről további információt talál a [DNS-zónák és -rekordok védelme](dns-protect-zones-recordsets.md) című szakaszban.


DNS-zónák törléséhez használja az alábbi két módszer egyikét:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Zóna megadása a zóna és az erőforráscsoport nevének használatával

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

A törölni kívánt zóna egy, a `Get-AzureRmDnsZone` parancs által visszaadott `$zone` objektummal adható meg.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

A zónaobjektum paraméterként való továbbítása helyett át is adhatja azt.

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Csakúgy, mint a `Set-AzureRmDnsZone` -ben, a zóna egy objektummal való megadásával a `$zone` ETAG ellenőrzi, hogy az egyidejű módosítások ne legyenek törölve. Ezeket az `-Overwrite` ellenőrzéseket a kapcsoló használatával tilthatja le.

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` és `Remove-AzureRmDnsZone` parancsmagok mind támogatják a megerősítési kérések használatát.

A `New-AzureRmDnsZone` és a `Set-AzureRmDnsZone` egyaránt kér megerősítést, ha a `$ConfirmPreference` PowerShell-preferenciaváltozó értéke `Medium` vagy kisebb. A DNS-zónák lehetséges súlyos hatásai miatt a `Remove-AzureRmDnsZone` parancsmag felkéri a művelet megerősítésére, ha a `$ConfirmPreference` PowerShell-változó értéke más, mint `None`.

Mivel a `$ConfirmPreference` alapértelmezett értéke `High`, csak a `Remove-AzureRmDnsZone` parancsmag kér alapértelmezés szerint megerősítést.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést.

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](/powershell/module/microsoft.powershell.core/about/about_preference_variables) tartalmazó részben talál.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti a rekordhalmazokat [és rekordokat](dns-operations-recordsets.md) a DNS-zónában.
<br>
Ismerje meg, hogyan [delegálhatja a tartományt Azure DNSre](dns-domain-delegation.md).
<br>
Tekintse át a [Azure DNS PowerShell-útmutató dokumentációját](/powershell/module/azurerm.dns).