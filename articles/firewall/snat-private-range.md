---
title: SNAT magánhálózati IP-címtartományok Azure Firewall
description: A SNAT IP-címtartományok is konfigurálhatók.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508928"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>SNAT magánhálózati IP-címtartományok Azure Firewall

A Azure Firewall a nyilvános IP-címekre irányuló összes kimenő forgalom automatikus SNAT biztosítja. Alapértelmezés szerint a Azure Firewall nem SNAT meg a hálózati szabályokkal, ha a célként megadott IP-cím az [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartomány része. Az alkalmazási szabályok mindig [transzparens proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) használatával lesznek alkalmazva a cél IP-címétől függetlenül.

Ez a logika jól működik, ha közvetlenül az internetre irányítja át a forgalmat. Ha azonban engedélyezte a [kényszerített bújtatást](forced-tunneling.md), az internethez kötött forgalom a AzureFirewallSubnet egyik címfordítást, a forrást pedig a helyszíni tűzfalból elrejti.

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall SNATs a forgalmat a AzureFirewallSubnet egyik tűzfal magánhálózati IP-címére. Azonban úgy is konfigurálhatja a Azure Firewallt, hogy **ne** SNAT a nyilvános IP-címtartományt. Például egy egyedi IP-cím megadásához megadhatja az alábbihoz hasonlót: `192.168.1.10` . IP-címtartomány megadásához a következőhöz hasonló módon adhatja meg: `192.168.1.0/24` .

- Ha úgy szeretné konfigurálni a Azure Firewallt, hogy **Soha ne** SNAT meg a cél IP-címről, használja a **0.0.0.0/0 értéket** a magánhálózati IP-címtartomány alapján. Ezzel a konfigurációval a Azure Firewall soha nem irányíthatja át a forgalmat közvetlenül az internetre. 

- Ha úgy szeretné beállítani a tűzfalat, hogy **mindig** SNAT a célhelytől függetlenül, használja a **255.255.255.255/32** protokollt magánhálózati IP-címtartományként.

> [!IMPORTANT]
> A megadott privát címtartomány csak a hálózati szabályokra vonatkozik. Az alkalmazás szabályai jelenleg mindig SNAT.

> [!IMPORTANT]
> Ha saját magánhálózati IP-címtartományt szeretne megadni, és meg szeretné tartani az alapértelmezett IANA RFC 1918-címtartományt, győződjön meg arról, hogy az egyéni lista továbbra is tartalmazza az IANA RFC 1918 tartományát. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure PowerShell

A Azure PowerShell használatával megadhatja a tűzfal magánhálózati IP-címtartományt.

### <a name="new-firewall"></a>Új tűzfal

Új tűzfal esetén a Azure PowerShell parancsmag a következő:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> A Azure Firewall használatával történő üzembe helyezéséhez `New-AzFirewall` meglévő VNet és nyilvános IP-cím szükséges. Lásd: [Azure Firewall telepítése és konfigurálása Azure PowerShell használatával](deploy-ps.md) a teljes telepítési útmutatóhoz.

> [!NOTE]
> A IANAPrivateRanges a Azure Firewall aktuális alapértelmezett értékeire van kiterjesztve, míg a többi tartomány hozzá van adva. Ha meg szeretné őrizni a IANAPrivateRanges alapértelmezett értékeit a privát tartomány specifikációjában, akkor az `PrivateRange` alábbi példákban látható módon meg kell maradnia a specifikációban.

További információ: [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal konfigurálásához használja a következő Azure PowerShell parancsmagokat:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure CLI

Az Azure CLI használatával megadhatja a tűzfal magánhálózati IP-címtartományt.

### <a name="new-firewall"></a>Új tűzfal

Új tűzfal esetén az Azure CLI-parancs a következő:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Azure Firewall üzembe helyezése az Azure CLI parancs használatával `az network firewall create` további konfigurációs lépéseket igényel a nyilvános IP-címek és az IP-konfiguráció létrehozásához. Lásd: [Azure Firewall üzembe helyezése és konfigurálása az Azure CLI használatával](deploy-cli.md) a teljes telepítési útmutatóhoz.

> [!NOTE]
> A IANAPrivateRanges a Azure Firewall aktuális alapértelmezett értékeire van kiterjesztve, míg a többi tartomány hozzá van adva. Ha meg szeretné őrizni a IANAPrivateRanges alapértelmezett értékeit a privát tartomány specifikációjában, akkor az `PrivateRange` alábbi példákban látható módon meg kell maradnia a specifikációban.

### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal konfigurálásához az Azure CLI-parancs a következő:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT magánhálózati IP-címtartományok konfigurálása – ARM-sablon

Ha a SNAT-t az ARM Template deploymentban szeretné konfigurálni, a következő `additionalProperties` tulajdonsággal adhatja hozzá:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure Portal

A Azure Portal segítségével megadhatja a tűzfal magánhálózati IP-címtartományt.

1. Válassza ki az erőforráscsoportot, majd válassza ki a tűzfalat.
2. Az **Áttekintés** oldalon válassza a **magánhálózati IP-címtartományok** elemet, majd az alapértelmezett értéket az **IANA RFC 1918**.

   Megnyílik a **privát IP-előtag szerkesztése** oldal:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Magánhálózati IP-előtagok szerkesztése":::

1. Alapértelmezés szerint a **IANAPrivateRanges** konfigurálva van.
2. Szerkessze a környezet magánhálózati IP-címeinek tartományát, majd válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg [Azure Firewall kényszerített bújtatással](forced-tunneling.md).